LancheProject默认添加了RabbitMq作为消息队列
在startUp中注册
```c#
 public class Startup
    {
        public void Configuration(IAppBuilder app)
        {

            app.UseLancheProject()
              
               .UseRabbitMq()
               ///创建一个消息队列的连接  test1 连接字符串在web.config中配置
               .UseMqConnection("test1")
                   ///创建一个消息队列的连接  test2 连接字符串在web.config中配置
               .UseMqConnection("test2");

        }
    }
```
连接的配置格式
```xml
 <add name="test1" connectionString="hostName=localhost;virtualHost=/;username=guest;password=guest;port=5672" />

```
直接在Service中使用
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
