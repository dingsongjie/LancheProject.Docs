##���ʹ��Mongodb
��LancheProject��Mongodb�е�Collection������Ϊһ�ű���Ef������Ҫ����һ��MongoDbContext
```c#
 public class FeturnMongoDbContext:MongoDbContext
    {
        public MongoDbSet<Car> Car { get; set; }

        public FeturnMongoDbContext()
            : base("Feture", "Test")
        {

        }
        
    }
    public class Car
    {
        public Guid Id{get;set;}
        public string Name{get;set;}
        
    }
```
ͬ����ܻ��ڳ�ʼ��ʱ ע��MongoDbContext�����е�MongoDbSet,ֱ����Biz��ʹ�ü���
```c#
 public class MongoDbTestBiz : ApplicationBizBase
    {
        private readonly IMongoDbRepository<Car> _carRepository;
        public IIocManager manager { get; set; }
        public MongoDbTestBiz(IMongoDbRepository<Car> carRepository)
        {
            this._carRepository = carRepository;
        }
     
        }
        public Car Get()
        {
           return _carRepository.Single(m => m.Name =="Alto");
        }
    }
```
IMongoDbRepository<>�ṩ�ķ���
##��ѯ
```c#
  /// <summary>
        /// �õ� IQueryable ,���ṩlinq ��ѯ����
        /// </summary>
        /// <returns></returns>
        IQueryable<TEntity> GetAll();

        /// <summary>
        /// ��������ʵ��List
        /// </summary>
        /// <returns>ʵ��List</returns>
        List<TEntity> GetAllList();

        /// <summary>
        /// ��������ʵ��List
        /// </summary>
        /// <returns>ʵ��List</returns>
        Task<List<TEntity>> GetAllListAsync();



        /// <summary>
        /// ���� lambda ���� ʵ��List
        /// </summary>
        /// <param name="predicate">where ����</param>
        /// <returns>ʵ�� list</returns>
        List<TEntity> GetAllList(Expression<Func<TEntity, bool>> predicate);

        /// <summary>
        /// ���� lambda ���� ʵ��List
        /// </summary>
        /// <param name="predicate">where ����</param>
        /// <returns>ʵ�� list</returns>
        Task<List<TEntity>> GetAllListAsync(Expression<Func<TEntity, bool>> predicate);

        /// <summary>
        /// 
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="queryMethod"></param>
        /// <returns></returns>
        T Query<T>(Func<IQueryable<TEntity>, T> queryMethod);


        /// <summary>
        /// ���ص������ҵ���� ֱ�ӱ���
        /// </summary>
        /// <param name="predicate">where ����</param>
        /// <returns></returns>
        TEntity Single(Expression<Func<TEntity, bool>> predicate);
        /// <summary>
        /// ���ص������ҵ���� ֱ�ӱ���
        /// </summary>
        /// <param name="predicate">where ����</param>
        /// <returns></returns>
        Task<TEntity> SingleAsync(Expression<Func<TEntity, bool>> predicate);


        /// <summary>
        /// �õ���һ��
        /// </summary>
        /// <param name="predicate">where ����</param>
        TEntity FirstOrDefault(Expression<Func<TEntity, bool>> predicate);
        /// <summary>
        /// �õ���һ��
        /// </summary>
        /// <param name="predicate">where ����</param>
        Task<TEntity> FirstOrDefaultAsync(Expression<Func<TEntity, bool>> predicate);

        /// <summary>
        /// ��ҳ
        /// </summary>
        /// <param name="query">where</param>
        /// <param name="pageIndex">��ǰҳ</param>
        /// <param name="pageSize">ҳsize</param>
        /// <param name="orderPropertyName">order Property</param>
        /// <param name="sort"> ������ </param>
        /// <returns>�������з�ҳ��Ϣ�����ݴ��ݶ���</returns>
        PagingEntity<TEntity> GetInPaging(Expression<Func<TEntity, bool>> query, int pageIndex, int pageSize, string orderPropertyName, bool sort = true);

        /// <summary>
        /// ��ҳ
        /// </summary>
        /// <param name="query">where</param>
        /// <param name="pageIndex">��ǰҳ</param>
        /// <param name="pageSize">ҳsize</param>
        /// <param name="orderPropertyName">order Property</param>
        /// <param name="sort"> ������ </param>
        /// <returns>�������з�ҳ��Ϣ�����ݴ��ݶ���</returns>
        Task<PagingEntity<TEntity>> GetInPagingAsync(Expression<Func<TEntity, bool>> query, int pageIndex, int pageSize, string orderPropertyName, bool sort = true);
          int Count();

        Task<int> CountAsync();

        int Count(Expression<Func<TEntity, bool>> predicate);

        Task<int> CountAsync(Expression<Func<TEntity, bool>> predicate);

        /// <summary>
        /// ���������� 2^31-1
        /// </summary>
        /// <returns>Count of entities</returns>
        long LongCount();

        /// <summary>
        /// ���������� 2^31-1
        /// </summary>
        /// <returns>Count of entities</returns>
        Task<long> LongCountAsync();

        /// <summary>
        /// ���������� 2^31-1
        /// </summary>
        /// <returns>Count of entities</returns>
        long LongCount(Expression<Func<TEntity, bool>> predicate);

        Task<long> LongCountAsync(Expression<Func<TEntity, bool>> predicate);
```
##����
```c#
TEntity Insert(TEntity entity);


        Task<TEntity> InsertAsync(TEntity entity);

        void InsertMany(IEnumerable<TEntity> entities);

        Task InsertManyAsync(IEnumerable<TEntity> entities);
```
##����
```c#
 /// <summary>
        /// ���� ���� ͬ��
        /// </summary>
        /// <param name="entity"></param>
        /// <returns></returns>
        TEntity Update(TEntity entity);

        /// <summary>
        /// ���� ����ʵ�� �첽
        /// </summary>
        /// <param name="entity"></param>
        /// <returns></returns>
        Task<TEntity> UpdateAsync(TEntity entity);

        int Update(Expression<Func<TEntity, bool>> filter, Expression<Func<TEntity, TEntity>> update);

        Task<int> UpdateAsync(Expression<Func<TEntity, bool>> filter, Expression<Func<TEntity, TEntity>> update);
```
##ɾ��
```c#
  /// <summary>
        /// ɾ������ʵ��
        /// </summary>
        /// <param name="entity"></param>
        void Delete(TEntity entity);
        /// <summary>
        /// ɾ������ʵ�� �첽
        /// </summary>
        /// <param name="entity"></param>
        /// <returns></returns>

        Task DeleteAsync(TEntity entity);

        int Delete(Expression<Func<TEntity, bool>> filter);

        Task<int> DeleteAsync(Expression<Func<TEntity, bool>> filter);
```
