LancheProjectĬ�������RabbitMq��Ϊ��Ϣ����
��startUp��ע��
```c#
 public class Startup
    {
        public void Configuration(IAppBuilder app)
        {

            app.UseLancheProject()
              
               .UseRabbitMq()
               ///����һ����Ϣ���е�����  test1 �����ַ�����web.config������
               .UseMqConnection("test1")
                   ///����һ����Ϣ���е�����  test2 �����ַ�����web.config������
               .UseMqConnection("test2");

        }
    }
```
���ӵ����ø�ʽ
```xml
 <add name="test1" connectionString="hostName=localhost;virtualHost=/;username=guest;password=guest;port=5672" />

```
ֱ����Service��ʹ��
```c#
   public class RabbitMqTestService : ApplicationServiceBase
    {
        private readonly IMessageQueryManager _manager;
       
        public RabbitMqTestService(IMessageQueryManager manager)
        {
            this._manager = manager;
            
        }
        public void Send()
        {
            var channel = _manager.GetChannal("test1");
            channel.Send("test", "hellow");
            //_manager.Dispose();
          
        }
    }
```
