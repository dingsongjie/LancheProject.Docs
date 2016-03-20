#缓存
LancheProject 提供了两种缓存 内存缓存 和redis缓存,在使用Redis缓存存放实体类时，框架会把该类序列化成json然后保存到redis中
当使用者调用get方法取出时，框架回自动反序列化转成实体，默认滑动过期时间为1个小时，可以在配置中自行修改，也可以在方法中直接传参
##使用内存缓存
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
直接在Biz中使用
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
##使用redis缓存
在startup中注册
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
默认连接字符串为
```c#
  public  class RedisCacheConst
    {
      public static string RedisConnectionName = "Lanche.Redis.Cache";
    }
```
当然可以在初始化任意时间段修改
然后在Config中配置
```xml
 <add name="Lanche.Redis.Cache" connectionString="localhost ......" />
```
然后直接在Biz中使用即可
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
