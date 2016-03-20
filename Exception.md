#��������쳣��ܻ���ô����
�� Dynamic Api �� �������쳣���ᱻ webApi���������ص�
```c#

  public class DynamicControllerExceptionFilterAttribute : ExceptionFilterAttribute,ISingleDependency
    {
        private readonly ErrorInfo _errorInfo;
        public DynamicControllerExceptionFilterAttribute(ErrorInfo errorInfo)
        {
            _errorInfo = errorInfo;
        }
       /// <summary>
       /// ����������
       /// </summary>
       /// <param name="context">��ǰ action ������</param>
        public override void OnException(HttpActionExecutedContext context)
        {

            _errorInfo.Exception = context.Exception;        
            context.Response = context.Request.CreateResponse(
                HttpStatusCode.OK,
                new AjaxResponse(_errorInfo) 
                );


        }
    }
```
����json �� Success=false  Error �е� Exception �������ϵͳ��ϸ��Ϣ
##����Զ��� ����json��ʽ��
ֻ��������ģ���� ɾ��Ĭ�� Attribute Ȼ����� �Զ��� Attribute
```c#
  httpConfiguration.Filters.Remove(...)
  httpConfiguration.Filters.Add(CustomAttribute);
```
��Ϊ��������õľ���WebApi����ֱ����WebApi�ķ����Ϳ�����




��ʹ��ApplicationBizʱ��������쳣�������ճ���Controller��ȥ����
