#Lanche.Entityframework
EntityFramework中的DbContext默认情况下受到UnitOfWork管理
首先定义一个DbContext，这个用法和原本的ef一模一样
```c#
 public  class TestDbContext : DbContext
    {
        public TestDbContext()
            : base("name=TestDbContext")
        {
        }

        public virtual DbSet<Students> Students { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {

        }
    }
```
然后 框架会在初始化中注入所有继承自DbContext的ef数据库访问对象，并注入其中的每一个DbSet,开发人员只需在Biz中直接使用即可
```c#
 public class TestApplicationBiz : ApplicationBizBase
    {
        private readonly IEfRepository<Students> _studentRepository;      

        public TestApplicationBiz(IEfRepository<Students> studentRepository, IUnitOfWorkManager uowManger,ILogger logger)
        {
            _studentRepository = studentRepository;
           
        }
        
        public virtual PagingEntity<Students> GetInPaging(int pageIndex, int PageSize, bool sort, string orderProperty)
        {
           
          
            return _studentRepository.GetInPaging(m => m.IsDeleted == false, pageIndex, PageSize, orderProperty, sort);

        }
  }
```
上面的StudentRepository属于构造函数注入，当然castle windsor 也是支持属性注入的
IEfRepository<>提供的方法如下
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

        /// <summary>
        /// 提供 sql 语句查询能力
        /// </summary>
        /// <typeparam name="TResult"></typeparam>
        /// <param name="sql"></param>
        /// <param name="parameters"></param>
        /// <returns></returns>
        DbRawSqlQuery<TResult> SqlQuery<TResult>(string sql, params object[] parameters);

        /// <summary>
        /// 提供SQL语句 sqlCommand
        /// </summary>
        /// <param name="sql"></param>
        /// <param name="parameters"></param>
        /// <returns></returns>
        int ExecuteSqlCommand(string sql, params object[] parameters);

        /// <summary>
        /// 提供SQL语句 sqlCommand 异步
        /// </summary>
        /// <param name="sql"></param>
        /// <param name="parameters"></param>
        /// <returns></returns>
        Task<int> ExecuteSqlCommandAsync(string sql, params object[] parameters);
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
 ///同步 添加单个
        TEntity Insert(TEntity entity);

        ///异步 添加单个
        Task<TEntity> InsertAsync(TEntity entity);

        /// <summary>
        /// 批量添加
        /// </summary>
        /// <param name="entities"></param>
        /// <param name="options"></param>
        /// <param name="bulkSize"></param>
        void BulkInsert(IEnumerable<TEntity> entities, SqlBulkCopyOptions options, int? bulkSize=null);

        /// <summary>
        /// 批量添加
        /// </summary>
        /// <param name="entities"></param>
        /// <param name="bulkSize"></param>

        void BulkInsert(IEnumerable<TEntity> entities, int? bulkSize=null);
```
##更新
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

         /// <summary>
        /// 批量更新
        /// </summary>
        /// <param name="filter"></param>
        /// <param name="update"></param>
        /// <returns></returns>
        int Update(Expression<Func<TEntity, bool>> filter,Expression<Func<TEntity,TEntity>> update);

        /// <summary>
        /// 批量更新异步
        /// </summary>
        /// <param name="filter"></param>
        /// <param name="update"></param>
        /// <returns></returns>
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
        /// <summary>
        /// 批量删除
        /// </summary>
        /// <param name="filter"></param>
        /// <returns></returns>
        int Delete(Expression<Func<TEntity, bool>> filter);

        /// <summary>
        /// 批量删除 异步
        /// </summary>
        /// <param name="filter"></param>
        /// <returns></returns>
        Task<int> DeleteAsync(Expression<Func<TEntity, bool>> filter);
```


