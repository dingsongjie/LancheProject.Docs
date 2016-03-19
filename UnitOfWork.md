#统一工作单元
默认当一个请求第一次进入业务层的某个方法，系统会给业务层加一个统一的工作单元，此工作单元主要用来管理该请求中所调用的DbContext,以及管理业务层事务对象如果需要的话。
```c#
   public virtual PagingEntity<Students> GetInPaging(int pageIndex, int PageSize, bool sort, string orderProperty)
        {
           Simple();
            _logger.Debug("ss");
            return _studentRepository.GetInPaging(m => m.IsDeleted == false, pageIndex, PageSize, orderProperty, sort);

        }
   public virtual void Simple()
        {
           
          /// some bu

        }

```
如上所示这是一个业务类（包括普通ApplicationBiz和Dynamic ApplicationBiz）的一个public virtual方法,当执行这个方法前，框架会自动为此次请求的业务加一个工作单元,在方法1中调用了方法2 ，则他们两个方法将在同一个工作单元中
##如何关闭工作单元
```c#
          [UnitOfWork(IsDisabled =true)]
   public virtual PagingEntity<Students> GetInPaging(int pageIndex, int PageSize, bool sort, string orderProperty)
        {
           Simple();
            _logger.Debug("ss");
            return _studentRepository.GetInPaging(m => m.IsDeleted == false, pageIndex, PageSize, orderProperty, sort);

        }
```
只需在 方法上加 UnitOfWork(IsDisabled =true) 特性标签,这样这个方法将不会受到统一工作单元的管理。

##如何打开工作单元中的事务？
默认业务级事务是没有开启的，若要开启事务
```c#
          [UnitOfWork(isTransactional: true)]
   public virtual PagingEntity<Students> GetInPaging(int pageIndex, int PageSize, bool sort, string orderProperty)
        {
           Simple();
            _logger.Debug("ss");
            return _studentRepository.GetInPaging(m => m.IsDeleted == false, pageIndex, PageSize, orderProperty, sort);

        }
```
只需在方法上添加 UnitOfWork(isTransactional: true) 特性标签


默认情况下，业务中的所有工作都将在工作单元中完成，工作单元完成后会自动SaveChange其中的DbContext并释放他们，若果其中有事务单元则提交事务单元，若提交失败则回滚。
