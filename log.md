#日志
默认已添加Log4net，
如需使用 需要在startup中写上
```c#
 public class Startup
    {
        public void Configuration(IAppBuilder app)
        {

            app.UseLancheProject()
                /// log   log4net 作为日志
               .UseLog4Net("log4net.config")
              

        }
    }
```
其中 app.UseLancheProject() 是使用LancheProject所必需的
.UseLog4Net("log4net.config") 就是适使用log4作为日志，并指明了配置文件的名称
配置文件的用法和log4net完全一致
在Service中的使用
```c#
  public class TestApplicationService : ApplicationServiceBase
    {
        private readonly ILogger _logger;
        ///构造函数注入
        public TestApplicationService(ILogger logger)
        {
           
            _logger = logger;
        }

        public virtual PagingEntity<Students> GetInPaging(int pageIndex, int PageSize, bool sort, string orderProperty)
        {
            /// debug 级别的 日志
            _logger.Debug("ss");
          

        }
    }
```
