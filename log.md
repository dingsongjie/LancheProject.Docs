#��־
Ĭ�������Log4net��
����ʹ�� ��Ҫ��startup��д��
```c#
 public class Startup
    {
        public void Configuration(IAppBuilder app)
        {

            app.UseLancheProject()
                /// log   log4net ��Ϊ��־
               .UseLog4Net("log4net.config")
              

        }
    }
```
���� app.UseLancheProject() ��ʹ��LancheProject�������
.UseLog4Net("log4net.config") ������ʹ��log4��Ϊ��־����ָ���������ļ�������
�����ļ����÷���log4net��ȫһ��
��Service�е�ʹ��
```c#
  public class TestApplicationService : ApplicationServiceBase
    {
        private readonly ILogger _logger;
        ///���캯��ע��
        public TestApplicationService(ILogger logger)
        {
           
            _logger = logger;
        }

        public virtual PagingEntity<Students> GetInPaging(int pageIndex, int PageSize, bool sort, string orderProperty)
        {
            /// debug ����� ��־
            _logger.Debug("ss");
          

        }
    }
```
