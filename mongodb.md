##如何使用Mongodb
在LancheProject中Mongodb中的Collection将被视为一张表，与Ef类似需要定义一个MongoDbContext
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
同样框架会在初始化时 注入MongoDbContext和其中的MongoDbSet,直接在Biz中使用即可
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
IMongoDbRepository<>提供的方法
##查询
```c#
  /// <summary>
        /// 得到 IQueryable ,以提供linq 查询能力
        /// </summary>
        /// <returns></returns>
        IQueryable<TEntity> GetAll();

        /// <summary>
        /// 返回所有实体List
        /// </summary>
        /// <returns>实体List</returns>
        List<TEntity> GetAllList();

        /// <summary>
        /// 返回所有实体List
        /// </summary>
        /// <returns>实体List</returns>
        Task<List<TEntity>> GetAllListAsync();



        /// <summary>
        /// 根据 lambda 返回 实体List
        /// </summary>
        /// <param name="predicate">where 条件</param>
        /// <returns>实体 list</returns>
        List<TEntity> GetAllList(Expression<Func<TEntity, bool>> predicate);

        /// <summary>
        /// 根据 lambda 返回 实体List
        /// </summary>
        /// <param name="predicate">where 条件</param>
        /// <returns>实体 list</returns>
        Task<List<TEntity>> GetAllListAsync(Expression<Func<TEntity, bool>> predicate);

        /// <summary>
        /// 
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="queryMethod"></param>
        /// <returns></returns>
        T Query<T>(Func<IQueryable<TEntity>, T> queryMethod);


        /// <summary>
        /// 返回单个，找到多个 直接报错
        /// </summary>
        /// <param name="predicate">where 条件</param>
        /// <returns></returns>
        TEntity Single(Expression<Func<TEntity, bool>> predicate);
        /// <summary>
        /// 返回单个，找到多个 直接报错
        /// </summary>
        /// <param name="predicate">where 条件</param>
        /// <returns></returns>
        Task<TEntity> SingleAsync(Expression<Func<TEntity, bool>> predicate);


        /// <summary>
        /// 得到第一个
        /// </summary>
        /// <param name="predicate">where 条件</param>
        TEntity FirstOrDefault(Expression<Func<TEntity, bool>> predicate);
        /// <summary>
        /// 得到第一个
        /// </summary>
        /// <param name="predicate">where 条件</param>
        Task<TEntity> FirstOrDefaultAsync(Expression<Func<TEntity, bool>> predicate);

        /// <summary>
        /// 分页
        /// </summary>
        /// <param name="query">where</param>
        /// <param name="pageIndex">当前页</param>
        /// <param name="pageSize">页size</param>
        /// <param name="orderPropertyName">order Property</param>
        /// <param name="sort"> 正或逆 </param>
        /// <returns>包含所有分页信息的数据传递对象</returns>
        PagingEntity<TEntity> GetInPaging(Expression<Func<TEntity, bool>> query, int pageIndex, int pageSize, string orderPropertyName, bool sort = true);

        /// <summary>
        /// 分页
        /// </summary>
        /// <param name="query">where</param>
        /// <param name="pageIndex">当前页</param>
        /// <param name="pageSize">页size</param>
        /// <param name="orderPropertyName">order Property</param>
        /// <param name="sort"> 正或逆 </param>
        /// <returns>包含所有分页信息的数据传递对象</returns>
        Task<PagingEntity<TEntity>> GetInPagingAsync(Expression<Func<TEntity, bool>> query, int pageIndex, int pageSize, string orderPropertyName, bool sort = true);
          int Count();

        Task<int> CountAsync();

        int Count(Expression<Func<TEntity, bool>> predicate);

        Task<int> CountAsync(Expression<Func<TEntity, bool>> predicate);

        /// <summary>
        /// 当个数超过 2^31-1
        /// </summary>
        /// <returns>Count of entities</returns>
        long LongCount();

        /// <summary>
        /// 当个数超过 2^31-1
        /// </summary>
        /// <returns>Count of entities</returns>
        Task<long> LongCountAsync();

        /// <summary>
        /// 当个数超过 2^31-1
        /// </summary>
        /// <returns>Count of entities</returns>
        long LongCount(Expression<Func<TEntity, bool>> predicate);

        Task<long> LongCountAsync(Expression<Func<TEntity, bool>> predicate);
```
##新增
```c#
TEntity Insert(TEntity entity);


        Task<TEntity> InsertAsync(TEntity entity);

        void InsertMany(IEnumerable<TEntity> entities);

        Task InsertManyAsync(IEnumerable<TEntity> entities);
```
##更新
```c#
 /// <summary>
        /// 更新 单个 同步
        /// </summary>
        /// <param name="entity"></param>
        /// <returns></returns>
        TEntity Update(TEntity entity);

        /// <summary>
        /// 更新 单个实体 异步
        /// </summary>
        /// <param name="entity"></param>
        /// <returns></returns>
        Task<TEntity> UpdateAsync(TEntity entity);

        int Update(Expression<Func<TEntity, bool>> filter, Expression<Func<TEntity, TEntity>> update);

        Task<int> UpdateAsync(Expression<Func<TEntity, bool>> filter, Expression<Func<TEntity, TEntity>> update);
```
##删除
```c#
  /// <summary>
        /// 删除单个实体
        /// </summary>
        /// <param name="entity"></param>
        void Delete(TEntity entity);
        /// <summary>
        /// 删除单个实体 异步
        /// </summary>
        /// <param name="entity"></param>
        /// <returns></returns>

        Task DeleteAsync(TEntity entity);

        int Delete(Expression<Func<TEntity, bool>> filter);

        Task<int> DeleteAsync(Expression<Func<TEntity, bool>> filter);
```
