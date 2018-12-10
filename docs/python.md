本节将会讲解OpenAPI三个重要的类及它功能
一些辅助的接口将会在dowork中介绍
### 项目目录
    openapi
        \api(存放项目的业务逻辑)
        \common（通用的操作函数）
        \decorators（项目运行使用的装饰器）
        \doc（文档）
        \exception（错误处理）
        \js（js代码（用于复现js加密过程））
        \lib（无）
        \service（服务器反射调用原函数的中间站）
        \task（把任务进行反射调用的函数）
        \util（放着一些日志包）
        \utils（无）
        constant.py（通用配置）
        main.py（运行入口）

### base_api
    class BaseApi
        （登录类）
        
        def login
            登录采集系统的业务逻辑
            return session（已经登录完成的session）
            
        def logout
            退出登录的方法
            pass
            
        def is_session_expired
            检查session是否过期的方法
            return True or False
                
*示例:*

     class item(BaseApi):
        def login():
            self.session.get(url登录页面)
            self.session.post(url注册页面)
            return self.session
            
### service
    class BaseService(ABC)
        （反射类的中间站，构造基础任务，还有回推方法）
        
        @abstractmethod
        def api_var
            获得业务class的实例
            api = item(BaseApi)
            return api
            
        def success
            任务成功的回推数据方法
            构造成功的数据
            self.task_util.send_task_data（回推数据的方法）
            
        def failure
            任务失败的回推数据方法
            构造失败的数据
            self.task_util.send_task_data（回推数据的方法）
            
*示例*：

     class MssIct(BaseService):
            def __init__(self, taskId, username, password, params, systemName, **kwargs):
                super().__init__(taskId, username, password, params, systemName, **kwargs)
                
            @service_exception_capture
            def queryDemandEstimate(self):
                任务的methodname对应函数名queryDemandEstimate
                return self.api.demand_estimate(**self.eval_params)
      
编写这个反射中间站的时候，需要注意的是类名MssIct，和对应的文件名必须是一致的。因为task进行反射调用的时候是根据类名的小写。

### task
    class TaskService
        （任务处理类）
        def do_task
            （从后台获取到的任务）
            根据从任务获取到的methodname、systemname进行反射调用
            m = __import__('service.' + system_name.lower())
            m2 = getattr(m, system_name.lower())
            所以反射中间站的文件名是类名的小写，类名与systemname一一对应
            文件必须放在service文件夹内
            obj = getattr(c(**dict(task)), method_name)
            obj() 
            然后调用反射中间站的方法
这就是openapi反射的原理  
下一节有关如何配合OpenAPI去完成一个采集推送数据的过程的