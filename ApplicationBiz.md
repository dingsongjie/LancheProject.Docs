#ApplicationBiz业务层
一般的业务对象都要继承自 IApplicationBiz
```c#
  /// <summary>
    /// 起到反射后的筛选作用，表示这是业务逻辑载体
    /// </summary>
    public interface IApplicationBiz : ITransientDependency
    {
    }

    // 代表 有关 student业务的业务类
     public interface StudentApplicationBiz : IApplicationBiz
    {
        private IEfRepository<Student> StudentRepository{get;set;}
        public Virtual GetStudent(string id)
        {

        }
    }
```
这种业务对象一般直接在Controller中调用，在程序启动时这些业务类会直接注入到IocManager中,其中的方法都要virtual标识
##为什么方法要virtual？
在实现 IApplicationBiz 接口的业务类型中，一个请求第一次进入业务类的某个方法动态代理就会为此请求添加一个工作单元，工作单元会为此次请求中的业务层添加一个业务级别的事务，并提供一个管理器管理当前请求，管理器会管理当前请求所需的EF数据库上下文，请求中有新的DbContext的需求时，ioc会生成一个所需类型的DbContext添加到当前管理器当中，若请求中再一次需要使用此DbContext,之前的DbContext就会被复用，以此来减少内存开销，当这个请求中所有的业务结束时有关于此次请求所生成的工作单元会被释放，其中的DbContext管理器就会释放当前DbContext,要想知道当前工作单元细节请看UnitOfWork文档。
在业务层可以调用所需要的类来进行业务处理，之后大量的示例将在ApplicationBiz中进行