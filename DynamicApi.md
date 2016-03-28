#DynamicApi
在微软的mvc，web api框架里 业务层对外开放接口都需要在外面套一层Controller,然后接口的使用者通过url调用该接口,所以基本上要为每个业务类型添加一个controller,
DynamicApi允许业务层直接开放接口，而不需要写controller,这样就减少了不必要的代码，DynamicApi开放出去的接口url可以自行定义。
##如何实现？
程序启动时反射生成所有Service信息以及每个ApplicationService中public方法的信息，当有请求匹配到自定义的DynamicApi路由，ControllerSeletor就会查找Service以及其中的方法如果匹配一致则通过表达式进入执行该ApplicationService中的方法,若参数绑定成功，则执行该方法，参数绑定机制也由WebApi提供。
DynamicApi路由机制由WebApi路由机制提供，不过其中的ControllerSeletor换成了自定义的ControllerSelector,继承自 WebApi DefaultHttpControllerSelector
```c#
/// <summary>
    /// 自定义 controller selector 通过路径 解析得到 controller ，该selector 为 动态controller 接收请求入口 ，前面的动作 asp.net 帮我完成
    /// </summary>
    public class DynamicHttpControllerSelector : DefaultHttpControllerSelector
    {
        private readonly HttpConfiguration _configuration;

        /// <summary>
        /// 构造函数
        /// </summary>
        /// <param name="configuration">http 配置</param>
        public DynamicHttpControllerSelector(HttpConfiguration configuration)
            : base(configuration)
        {
            _configuration = configuration;
        }

        /// <summary>
        /// 根据请求 在 DynamicApiControllerManager 里的 controllerinfo字典 找到并 生成 HttpControllerDescriptor
        /// </summary>
        /// <param name="request"></param>
        /// <returns></returns>
        public override HttpControllerDescriptor SelectController(HttpRequestMessage request)
        {

            if (request == null)
            {
                return base.SelectController(null);
            }
            //得到 routeData
            var routeData = request.GetRouteData();
            if (routeData == null)
            {
                return base.SelectController(request);
            }

            /// 得到 routedata 中的 {serviceNameWithAction}
            object serviceNameWithActionObj;

            if (!routeData.Values.TryGetValue("serviceNameWithAction", out serviceNameWithActionObj))
            {
                return base.SelectController(request);
            }
            string serviceNameWithAction = serviceNameWithActionObj.ToString();
            //去掉后缀
            if (serviceNameWithAction.EndsWith("/"))
            {
                serviceNameWithAction = serviceNameWithAction.Substring(0, serviceNameWithAction.Length - 1);
                routeData.Values["serviceNameWithAction"] = serviceNameWithAction;
            }

            //得到 controller
           
             //      1 ... pre/control/action  
            if (!DynamicApiServiceNameHelper.IsValidServiceNameWithAction(serviceNameWithAction))
            {
                return base.SelectController(request);
            }

            var serviceName = DynamicApiServiceNameHelper.GetServiceNameInServiceNameWithAction(serviceNameWithAction);
            var controllerInfo = DynamicApiControllerManager.FindOrNull(serviceName);
            if (controllerInfo == null)
            {
                return base.SelectController(request);
            }

        


            
            var controllerDescriptor = new DynamicHttpControllerDescriptor(_configuration, controllerInfo.ServiceName, controllerInfo.ApiControllerType, controllerInfo.Filters);
            controllerDescriptor.Properties["_DynamicApiControllerInfo"] = controllerInfo;
           
            return controllerDescriptor;
        }
    }
```
再在程序启动时替换默认ControllerSelector

```c#
  var httpConfiguration = GlobalConfiguration.Configuration;
  httpConfiguration.Services.Replace(typeof(IHttpControllerSelector), new DynamicHttpControllerSelector(httpConfiguration));
```


同理将替换掉 ActionSelector 并用自定义的ControllerDescriptor,ActionDescriptor做一些额外的工作，比如添加过滤器，还有就是对ApplicationService的返回值做一些加工


```c#
 public override System.Threading.Tasks.Task<object> ExecuteAsync(HttpControllerContext controllerContext, System.Collections.Generic.IDictionary<string, object> arguments, System.Threading.CancellationToken cancellationToken)
        {

            dynamic Generic = controllerContext.Controller;
            var Service = Generic.Service;
            controllerContext.Controller = controllerContext.Controller = Service;
            return base
                .ExecuteAsync(controllerContext, arguments, cancellationToken)
                .ContinueWith(task =>
                {
                    try
                    {
                        if (task.Result == null)
                        {

                            return new AjaxResponse();
                        }

                        if (task.Result is AjaxResponse)
                        {
                            return task.Result;
                        }

                        return new AjaxResponse(task.Result);
                    }
                    catch (Exception ex)
                    {

                        throw ex;
                    }
                }, cancellationToken);
        }
```
ExecuteAsync方法最终返回ApplicationService中方法返回的值，ContinueWith方法将返回对象包装成一个 AjaxResponse对象 AjaxResponse会被webApi的序列化工具序列化（默认生成json）,最终生成json格式为
```json
{
    Success: boolean,
    Data:{object} ,//其中object对象即为ApplicationServices中方法所返回的对象 如果返回集合，则此时其中为数组
    Error: //返回的错误信息
    {
        UnAuthorizedRequest:boolean,   //是否没有权限
        Message:string,    //一般返回自定义错误信息 ，友好的错误提示
        Exception: string   //系统错误序列化后的字符串
    }
}
```
##调用者如何调用？
```c#
  private static void InitializeRoutes(HttpConfiguration httpConfiguration)
        {
            //Dynamic Web APIs

            httpConfiguration.Routes.MapHttpRoute(
                name: "DynamicWebApi",
                routeTemplate: "api/services/{*serviceNameWithAction}"
                );

          
        }
```
默认注册的路由由 api/services开头 ，如果要自定义，修改则要修改源代码，修改路由配置不会对框架产生其他影响，
路由自定义部分
```c#
  DynamicApiControllerBuilder.ForAll<ApplicationServiceBase>(Assembly.GetExecutingAssembly(), "SimpleDynamic").Build();
```
框架的使用者，在业务层有一个注册当前动态api的方法，这样所欲的DynamicApi就会在程序启动时做准备工作，此时DynamicApi的路由则为api/services/SimpleDynamic
```c#
 public class ApplicationServiceBase:IApplicationService,IHttpController
    {
       
    }
```
从这里可以看出Dynamic ApplicationService 和一般 ApplicationService的区别 ，Dynamic ApplicationService 具有 HttpController的能力
实现一个人简单的 Dynamic ApplicationService
```c#
public class StudentApplicationService:ApplicationServiceBase
    {
         public IEfRepository<Student> StudentRepository { get; set; }
         public virtual void AddToDb(IEnumerable<Student> students)
         {
             StudentRepository.BulkInsert(modules);
         }
    }
```
接着注册所有Dynamic ApplicationService
```c#
  DynamicApiControllerBuilder.ForAll<ApplicationServiceBase>(Assembly.GetExecutingAssembly(), "SimpleDynamic").Build();
```
这样便可以通过url /api/services/SimpleDynamic/Student/AddToDb 访问到 这个Service ,Service的路由方式和asp.net mvc 一样，通过约定来做，取Service类名{Student}ApplicationService
前半部分，并添加 方法名称，记住方法必须是public的
对于方法参数的要求，get请求 一般用多参数方法
```c#
public class StudentApplicationService:ApplicationServiceBase
    {
         public IEfRepository<Student> StudentRepository { get; set; }
         public virtual void GetSingle(string id,string name)
         {
             StudentRepository.Single(m=>m.Id==id&&Name==name);
         }
    }
```
对于post请求 一般用 实体类接收
```c#
public class StudentApplicationService:ApplicationServiceBase
    {
         public IEfRepository<Student> StudentRepository { get; set; }
         public virtual void Add(StudentVieWModel student)
         {
             StudentRepository.Add(student);
         }
    }
```
##如何自定义返回json格式?
自定义一个类继承自AjaxResponse，比如在Lanche.Extensions.JqueryDataTable.dll里实现了自定义Response
```c#
 public class PagingResultEntity<TEntity>:AjaxResponse where TEntity:class,new()
    {
        public PagingResultEntity(PagingEntity<TEntity> pagingEntity,int draw)
        {
            this.draw = draw;
            this.recordsTotal = pagingEntity.EntityTotalCount;
            this.recordsFiltered = pagingEntity.EntityTotalCount;
            this.Data = pagingEntity.Entities;

        }
        public int draw { get;private set; }
        public long recordsTotal { get; private set; }
        public int recordsFiltered { get; private set; }
    }
```




