#程序出现异常框架会怎么处理？
在 Dynamic Api 中 若出现异常都会被 webApi拦截器拦截到
```c#

  public class DynamicControllerExceptionFilterAttribute : ExceptionFilterAttribute,ISingleDependency
    {
        private readonly ErrorInfo _errorInfo;
        public DynamicControllerExceptionFilterAttribute(ErrorInfo errorInfo)
        {
            _errorInfo = errorInfo;
        }
       /// <summary>
       /// 错误拦截器
       /// </summary>
       /// <param name="context">当前 action 上下文</param>
        public override void OnException(HttpActionExecutedContext context)
        {

            _errorInfo.Exception = context.Exception;        
            context.Response = context.Request.CreateResponse(
                HttpStatusCode.OK,
                new AjaxResponse(_errorInfo) 
                );


        }
    }
```
返回json 中 Success=false  Error 中的 Exception 将会包含系统详细信息
##如何自定义 返回json格式？
只需在任意模块中 删除默认 Attribute 然后添加 自定义 Attribute
```c#
  httpConfiguration.Filters.Remove(...)
  httpConfiguration.Filters.Add(CustomAttribute);
```
因为错误过滤用的就是WebApi所以直接用WebApi的方法就可以了




在使用ApplicationBiz时，请求的异常处理处理，照常在Controller里去处理
