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
��Biz�е�ʹ��
```c#
  public class TestApplicationBiz : ApplicationBizBase
    {
        private readonly ILogger _logger;
        ///���캯��ע��
        public TestApplicationBiz(ILogger logger)
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
