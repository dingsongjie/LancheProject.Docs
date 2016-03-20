#Lanche.Entityframework
EntityFramework�е�DbContextĬ��������ܵ�UnitOfWork����
���ȶ���һ��DbContext������÷���ԭ����efһģһ��
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
Ȼ�� ��ܻ��ڳ�ʼ����ע�����м̳���DbContext��ef���ݿ���ʶ��󣬲�ע�����е�ÿһ��DbSet,������Աֻ����Biz��ֱ��ʹ�ü���
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
�����StudentRepository���ڹ��캯��ע�룬��Ȼcastle windsor Ҳ��֧������ע���
IEfRepository<>�ṩ�ķ�������
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

        /// <summary>
        /// �ṩ sql ����ѯ����
        /// </summary>
        /// <typeparam name="TResult"></typeparam>
        /// <param name="sql"></param>
        /// <param name="parameters"></param>
        /// <returns></returns>
        DbRawSqlQuery<TResult> SqlQuery<TResult>(string sql, params object[] parameters);

        /// <summary>
        /// �ṩSQL��� sqlCommand
        /// </summary>
        /// <param name="sql"></param>
        /// <param name="parameters"></param>
        /// <returns></returns>
        int ExecuteSqlCommand(string sql, params object[] parameters);

        /// <summary>
        /// �ṩSQL��� sqlCommand �첽
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
 ///ͬ�� ��ӵ���
        TEntity Insert(TEntity entity);

        ///�첽 ��ӵ���
        Task<TEntity> InsertAsync(TEntity entity);

        /// <summary>
        /// �������
        /// </summary>
        /// <param name="entities"></param>
        /// <param name="options"></param>
        /// <param name="bulkSize"></param>
        void BulkInsert(IEnumerable<TEntity> entities, SqlBulkCopyOptions options, int? bulkSize=null);

        /// <summary>
        /// �������
        /// </summary>
        /// <param name="entities"></param>
        /// <param name="bulkSize"></param>

        void BulkInsert(IEnumerable<TEntity> entities, int? bulkSize=null);
```
##����
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

         /// <summary>
        /// ��������
        /// </summary>
        /// <param name="filter"></param>
        /// <param name="update"></param>
        /// <returns></returns>
        int Update(Expression<Func<TEntity, bool>> filter,Expression<Func<TEntity,TEntity>> update);

        /// <summary>
        /// ���������첽
        /// </summary>
        /// <param name="filter"></param>
        /// <param name="update"></param>
        /// <returns></returns>
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
        /// <summary>
        /// ����ɾ��
        /// </summary>
        /// <param name="filter"></param>
        /// <returns></returns>
        int Delete(Expression<Func<TEntity, bool>> filter);

        /// <summary>
        /// ����ɾ�� �첽
        /// </summary>
        /// <param name="filter"></param>
        /// <returns></returns>
        Task<int> DeleteAsync(Expression<Func<TEntity, bool>> filter);
```


