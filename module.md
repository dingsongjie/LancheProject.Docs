#关于模块化
LancheProject中的每一个dll中都有一个继承自Module的类，在这个类中有三个主要的方法
```c#
   public virtual void PreInitialize()
        {
           
        }

     
        public virtual void Initialize()
        {

        }

        public virtual void PostInitialize()
        {

        }
```
分别代表，模块初始化三个阶段中要做的事
ModuleManager将会根据模块之间的依赖关系依次调用这些方法
模块之间的依赖关系将由 DependsOn(typeof(CoreModule))  这个特性指定，代表这个模块依赖于CoreModule 这样该模块的初始化将放在CoreModule之后，如下是一个MongoDb 模块类
```c#
 [DependsOn(typeof(CoreModule))]
    public class MongoDbModule:Module
    {
        public ITypeFinder TypeFinder { get; set; }
        public override void PreInitialize()
        {
          
            base.PreInitialize();
        }
        public override void Initialize()
        {
            RegisterGenericRepositories(IocManager);
            base.Initialize();
        }
    }
```
先要在模块初始化是做的工作都可以在这里做 比如依赖注入
##为什么模块化？
为了防止出现面条式的注册代码，如果不模块化，模块的初始化工作将集中放在一处，难以维护
