#����ģ�黯
LancheProject�е�ÿһ��dll�ж���һ���̳���Module���࣬�����������������Ҫ�ķ���
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
�ֱ����ģ���ʼ�������׶���Ҫ������
ModuleManager�������ģ��֮���������ϵ���ε�����Щ����
ģ��֮���������ϵ���� DependsOn(typeof(CoreModule))  �������ָ�����������ģ��������CoreModule ������ģ��ĳ�ʼ��������CoreModule֮��������һ��MongoDb ģ����
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
��Ҫ��ģ���ʼ�������Ĺ����������������� ��������ע��
##Ϊʲôģ�黯��
Ϊ�˷�ֹ��������ʽ��ע����룬�����ģ�黯��ģ��ĳ�ʼ�����������з���һ��������ά��
