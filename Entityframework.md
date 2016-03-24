#Lanche.Entityframework
EntityFrameworkÖÐµÄDbContextÄ¬ÈÏÇé¿öÏÂÊÜµ½UnitOfWork¹ÜÀí
Ê×ÏÈ¶¨ÒåÒ»¸öDbContext£¬Õâ¸öÓÃ·¨ºÍÔ­±¾µÄefÒ»Ä£Ò»Ñù
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
È»ºó ¿ò¼Ü»áÔÚ³õÊ¼»¯ÖÐ×¢ÈëËùÓÐ¼Ì³Ð×ÔDbContextµÄefÊý¾Ý¿â·ÃÎÊ¶ÔÏó£¬²¢×¢ÈëÆäÖÐµÄÃ¿Ò»¸öDbSet,¿ª·¢ÈËÔ±Ö»ÐèÔÚBizÖÐÖ±½ÓÊ¹ÓÃ¼´¿É
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
ÉÏÃæµÄStudentRepositoryÊôÓÚ¹¹Ôìº¯Êý×¢Èë£¬µ±È»castle windsor Ò²ÊÇÖ§³ÖÊôÐÔ×¢ÈëµÄ
IEfRepository<>Ìá¹©µÄ·½·¨ÈçÏÂ
##²éÑ¯
```c#
 /// <summary>
        /// µÃµ½ IQueryable ,ÒÔÌá¹©linq ²éÑ¯ÄÜÁ¦
        /// </summary>
        /// <returns></returns>
        IQueryable<TEntity> GetAll();

        /// <summary>
        /// ·µ»ØËùÓÐÊµÌåList
        /// </summary>
        /// <returns>ÊµÌåList</returns>
        List<TEntity> GetAllList();

        /// <summary>
        /// ·µ»ØËùÓÐÊµÌåList
        /// </summary>
        /// <returns>ÊµÌåList</returns>
        Task<List<TEntity>> GetAllListAsync();



        /// <summary>
        /// ¸ù¾Ý lambda ·µ»Ø ÊµÌåList
        /// </summary>
        /// <param name="predicate">where Ìõ¼þ</param>
        /// <returns>ÊµÌå list</returns>
        List<TEntity> GetAllList(Expression<Func<TEntity, bool>> predicate);

        /// <summary>
        /// ¸ù¾Ý lambda ·µ»Ø ÊµÌåList
        /// </summary>
        /// <param name="predicate">where Ìõ¼þ</param>
        /// <returns>ÊµÌå list</returns>
        Task<List<TEntity>> GetAllListAsync(Expression<Func<TEntity, bool>> predicate);
         /// <summary>
        /// 返回所有实体List不再内存中缓存，不做状态跟�?
        /// </summary>
        /// <returns>实体List</returns>
        List<TEntity> GetAllListNoTracking();
        /// <summary>
        /// 返回所有实体List不再内存中缓存，不做状态跟�?异步方法
        /// </summary>
        /// <returns>实体List</returns>
        Task<List<TEntity>> GetAllListNoTrackingAsync();
        /// <summary>
        /// 根据 lambda 返回 实体List 不做状态跟�?
        /// </summary>
        /// <param name="predicate">where 条件</param>
        /// <returns>实体 list</returns>
        List<TEntity> GetAllListNoTracking(Expression<Func<TEntity, bool>> predicate);
        /// <summary>
        /// 根据 lambda 返回 实体List 不做状态跟�?异步
        /// </summary>
        /// <param name="predicate">where 条件</param>
        /// <returns>实体 list</returns>
        Task<List<TEntity>> GetAllListNoTrackingAsync(Expression<Func<TEntity, bool>> predicate);
        /// <summary>
        /// ·µ»Øµ¥¸ö£¬ÕÒµ½¶à¸ö Ö±½Ó±¨´í
        /// </summary>
        /// <param name="predicate">where Ìõ¼þ</param>
        /// <returns></returns>
        TEntity Single(Expression<Func<TEntity, bool>> predicate);
        /// <summary>
        /// ·µ»Øµ¥¸ö£¬ÕÒµ½¶à¸ö Ö±½Ó±¨´í
        /// </summary>
        /// <param name="predicate">where Ìõ¼þ</param>
        /// <returns></returns>
        Task<TEntity> SingleAsync(Expression<Func<TEntity, bool>> predicate);


        /// <summary>
        /// µÃµ½µÚÒ»¸ö
        /// </summary>
        /// <param name="predicate">where Ìõ¼þ</param>
        TEntity FirstOrDefault(Expression<Func<TEntity, bool>> predicate);
        /// <summary>
        /// µÃµ½µÚÒ»¸ö
        /// </summary>
        /// <param name="predicate">where Ìõ¼þ</param>
        Task<TEntity> FirstOrDefaultAsync(Expression<Func<TEntity, bool>> predicate);
         /// <summary>
        /// 返回单个，找到多�?直接报错 不做状态跟�?
        /// </summary>
        /// <param name="predicate">where 条件</param>
        /// <returns></returns>
        TEntity SingleNoTracking(Expression<Func<TEntity, bool>> predicate);
        /// <summary>
        /// 返回单个，找到多�?直接报错 不做状态跟�?async
        /// </summary>
        /// <param name="predicate">where 条件</param>
        /// <returns></returns>
        Task<TEntity> SingleNoTrackingAsync(Expression<Func<TEntity, bool>> predicate);
        /// <summary>
        /// 得到第一�?或�?null  不做状态跟�?
        /// </summary>
        /// <param name="predicate">where 条件</param>
        TEntity FirstOrDefaultNoTracking(Expression<Func<TEntity, bool>> predicate);
        /// <summary>
        /// 得到第一�?或�?null   不做状态跟�?async
        /// </summary>
        /// <param name="predicate">where 条件</param>
        Task<TEntity> FirstOrDefaultNoTrackingAsync(Expression<Func<TEntity, bool>> predicate);

        /// <summary>
        /// ·ÖÒ³
        /// </summary>
        /// <param name="query">where</param>
        /// <param name="pageIndex">µ±Ç°Ò³</param>
        /// <param name="pageSize">Ò³size</param>
        /// <param name="orderPropertyName">order Property</param>
        /// <param name="sort"> Õý»òÄæ </param>
        /// <returns>°üº¬ËùÓÐ·ÖÒ³ÐÅÏ¢µÄÊý¾Ý´«µÝ¶ÔÏó</returns>
        PagingEntity<TEntity> GetInPaging(Expression<Func<TEntity, bool>> query, int pageIndex, int pageSize, string orderPropertyName, bool sort = true);

        /// <summary>
        /// ·ÖÒ³
        /// </summary>
        /// <param name="query">where</param>
        /// <param name="pageIndex">µ±Ç°Ò³</param>
        /// <param name="pageSize">Ò³size</param>
        /// <param name="orderPropertyName">order Property</param>
        /// <param name="sort"> Õý»òÄæ </param>
        /// <returns>°üº¬ËùÓÐ·ÖÒ³ÐÅÏ¢µÄÊý¾Ý´«µÝ¶ÔÏó</returns>
        Task<PagingEntity<TEntity>> GetInPagingAsync(Expression<Func<TEntity, bool>> query, int pageIndex, int pageSize, string orderPropertyName, bool sort = true);
         /// <summary>
        /// 分页 不做状态跟�?
        /// </summary>
        /// <param name="query">where</param>
        /// <param name="pageIndex">当前�?/param>
        /// <param name="pageSize">页size</param>
        /// <param name="orderPropertyName">order Property</param>
        /// <param name="sort"> 正或�?</param>
        /// <returns>包含所有分页信息的数据传递对�?/returns>
        PagingEntity<TEntity> GetInPagingNoTracking(Expression<Func<TEntity, bool>> query, int pageIndex, int pageSize, string orderPropertyName, bool sort = true);
        /// <summary>
        /// 分页 不做状态跟�?async
        /// </summary>
        /// <param name="query">where</param>
        /// <param name="pageIndex">当前�?/param>
        /// <param name="pageSize">页size</param>
        /// <param name="orderPropertyName">order Property</param>
        /// <param name="sort"> 正或�?</param>
        /// <returns>包含所有分页信息的数据传递对�?/returns>
        Task<PagingEntity<TEntity>> GetInPagingNoTrackingAsync(Expression<Func<TEntity, bool>> query, int pageIndex, int pageSize, string orderPropertyName, bool sort = true);

        /// <summary>
        /// 得到Database对向 以获得ado.net 访问数据库的部分能力
        /// </summary>
        /// <returns></returns>
        Database GetDatebase();
          int Count();

        Task<int> CountAsync();

        int Count(Expression<Func<TEntity, bool>> predicate);

        Task<int> CountAsync(Expression<Func<TEntity, bool>> predicate);

        /// <summary>
        /// µ±¸öÊý³¬¹ý 2^31-1
        /// </summary>
        /// <returns>Count of entities</returns>
        long LongCount();

        /// <summary>
        /// µ±¸öÊý³¬¹ý 2^31-1
        /// </summary>
        /// <returns>Count of entities</returns>
        Task<long> LongCountAsync();

        /// <summary>
        /// µ±¸öÊý³¬¹ý 2^31-1
        /// </summary>
        /// <returns>Count of entities</returns>
        long LongCount(Expression<Func<TEntity, bool>> predicate);

        Task<long> LongCountAsync(Expression<Func<TEntity, bool>> predicate);
```
##ÐÂÔö
```c#
 ///Í¬²½ Ìí¼Óµ¥¸ö
        TEntity Insert(TEntity entity);

        ///Òì²½ Ìí¼Óµ¥¸ö
        Task<TEntity> InsertAsync(TEntity entity);

        /// <summary>
        /// ÅúÁ¿Ìí¼Ó
        /// </summary>
        /// <param name="entities"></param>
        /// <param name="options"></param>
        /// <param name="bulkSize"></param>
        void BulkInsert(IEnumerable<TEntity> entities, SqlBulkCopyOptions options, int? bulkSize=null);

        /// <summary>
        /// ÅúÁ¿Ìí¼Ó
        /// </summary>
        /// <param name="entities"></param>
        /// <param name="bulkSize"></param>

        void BulkInsert(IEnumerable<TEntity> entities, int? bulkSize=null);
```
##¸üÐÂ
/// <summary>
        /// ¸üÐÂ µ¥¸ö Í¬²½
        /// </summary>
        /// <param name="entity"></param>
        /// <returns></returns>
        TEntity Update(TEntity entity);

        /// <summary>
        /// ¸üÐÂ µ¥¸öÊµÌå Òì²½
        /// </summary>
        /// <param name="entity"></param>
        /// <returns></returns>
        Task<TEntity> UpdateAsync(TEntity entity);

         /// <summary>
        /// ÅúÁ¿¸üÐÂ
        /// </summary>
        /// <param name="filter"></param>
        /// <param name="update"></param>
        /// <returns></returns>
        int Update(Expression<Func<TEntity, bool>> filter,Expression<Func<TEntity,TEntity>> update);

        /// <summary>
        /// ÅúÁ¿¸üÐÂÒì²½
        /// </summary>
        /// <param name="filter"></param>
        /// <param name="update"></param>
        /// <returns></returns>
        Task<int> UpdateAsync(Expression<Func<TEntity, bool>> filter, Expression<Func<TEntity, TEntity>> update);
```
##É¾³ý
```c#
/// <summary>
        /// É¾³ýµ¥¸öÊµÌå
        /// </summary>
        /// <param name="entity"></param>
        void Delete(TEntity entity);
        /// <summary>
        /// É¾³ýµ¥¸öÊµÌå Òì²½
        /// </summary>
        /// <param name="entity"></param>
        /// <returns></returns>

        Task DeleteAsync(TEntity entity);
        /// <summary>
        /// ÅúÁ¿É¾³ý
        /// </summary>
        /// <param name="filter"></param>
        /// <returns></returns>
        int Delete(Expression<Func<TEntity, bool>> filter);

        /// <summary>
        /// ÅúÁ¿É¾³ý Òì²½
        /// </summary>
        /// <param name="filter"></param>
        /// <returns></returns>
        Task<int> DeleteAsync(Expression<Func<TEntity, bool>> filter);
```


