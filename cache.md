#����
LancheProject �ṩ�����ֻ��� �ڴ滺�� ��redis����,��ʹ��Redis������ʵ����ʱ����ܻ�Ѹ������л���jsonȻ�󱣴浽redis��
��ʹ���ߵ���get����ȡ��ʱ����ܻ��Զ������л�ת��ʵ�壬Ĭ�ϻ�������ʱ��Ϊ1��Сʱ�������������������޸ģ�Ҳ�����ڷ�����ֱ�Ӵ���
##ʹ���ڴ滺��
```c#
 public class Startup
    {
        public void Configuration(IAppBuilder app)
        {

            app.UseLancheProject()
              
               .UseMemoryCache()
                 

        }
    }
```
ֱ����Biz��ʹ��
```c#
 public class CacheTestBiz : ApplicationBizBase
    {
        private readonly ICacheManager cacheManager;

        public CacheTestBiz(ICacheManager cacheManager)
        {
            this.cacheManager = cacheManager;
            
        }
        public void SetOne()
        {

            var cache = cacheManager.GetOrCreateCache("test1");
            cache.Set("Id", 123456, new TimeSpan(1, 0, 0));
        }
```
##ʹ��redis����
��startup��ע��
```c#
 public class Startup
    {
        public void Configuration(IAppBuilder app)
        {

            app.UseLancheProject()
             
               .UseRedisCache()
             

        }
    }
```
Ĭ�������ַ���Ϊ
```c#
  public  class RedisCacheConst
    {
      public static string RedisConnectionName = "Lanche.Redis.Cache";
    }
```
��Ȼ�����ڳ�ʼ������ʱ����޸�
Ȼ����Config������
```xml
 <add name="Lanche.Redis.Cache" connectionString="localhost ......" />
```
Ȼ��ֱ����Biz��ʹ�ü���
```c#
 public class CacheTestBiz : ApplicationBizBase
    {
        private readonly ICacheManager cacheManager;

        public CacheTestBiz(ICacheManager cacheManager)
        {
            this.cacheManager = cacheManager;
            
        }
        public void SetOne()
        {

            var cache = cacheManager.GetOrCreateCache("test1");
            cache.Set("Id", 123456, new TimeSpan(1, 0, 0));
        }
```
