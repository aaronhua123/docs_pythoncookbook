以下将会引导你使用OpenAPI完成一个项目
#### 定义一个业务类
    文件目录
    openapi
        \api
            \ict
                item.py
在api文件夹下创建项目目录ict，再在里面创建项目文件item.py。在文件里面创建业务类Item
    
    from api.base_api import BaseApi
    class Item(BaseApi):
        实现登录的逻辑方法
        def login(self):
            return self.session
        def logout(self):
            pass
        def is_session_expired(self):
            r = self.session.get(主页)
            return  '账号' not in r.text
            
这里Item类继承了BaseApi,所以Item必须实现三个方法
     
    @abstractmethod
    def login(self):
        raise RuntimeError("Login must be implementation!!")
    @abstractmethod
    def logout(self):
        raise RuntimeError("Logout must be implementation!!")
    @abstractmethod
    def is_session_expired(self, session, **kwargs):
        raise RuntimeError("Is_session_expired must be implementation!!")
#### 添加一个auto登录装饰器

    from decorators.cls_decorator import auto_login_decorator
    @auto_login_decorator(expire_time=10, unit=UNIT_MINUTES)
    class Item(BaseApi):
这个装饰器`@auto_login_decorator`在生成实例的时候就会调用`login`方法。所以业务类的调用方法改变了。由：
    
    item = Item(username="test",password="test")
    item.login（）
    变成
    item = Item(username="test",password="test")

#### 实现一个业务方法
    class Item(BaseApi):
        ...
        def load_test(self):
            编写业务逻辑do some thing
            r = self.session.get(目录页)
            for url in 详情页列表：
                r = self.session.get(url)
                构造数据字典
            return  {"load_test":....}
            返回字典型的数据（推送数据时会转成json数据）
        
在OpenAPI运行的时候，加载任务的方法

#### 添加装饰器
`@exception_capture`和`@session_checker`

    @exception_capture
    @session_checker
    def load_test(self):
        ...
        
`@session_checker`是检测session是否过期的装饰器，如果session过期将会重新登录。
`@exception_capture`是捕捉错误的装饰器，程序运行的过程中出现的异常会记录在log文件内。
运行日志需要自己手动编码了。
#### 构造反射中间站
    文件目录
    openapi
        \service
            mssitem.py
在service文件夹下创建项目文件mssitem.py。在文件里面创建中间类MssItem
    
    from service.base_service import BaseService
    from api.ict.item import Item
    class MssIct(BaseService):
        def __init__(self, taskId, username, password, params, systemName, **kwargs):
            super().__init__(taskId, username, password, params, systemName, **kwargs)
        
        @service_exception_capture
        def loadTest(self):
            return self.api.load_test(**self.eval_params)
            
        @service_exception_capture
        def api_var(self):
            return Item(username=self.username, password=self.password)
            
1. 导入Item业务类
2. 实现api_var的方法，返回Item业务类的实例
3. 实现loadTest的方法，该方法名对应Java后台传入的参数methodname，返回业务类的方法即可。

#### 注册task
最后到task文件添加账号信息即可

    def load_tasks():
    pre_tasks = [
        ...
        {"password": "123456", "systemName": "mssitem", "version": "1.0.0", "username": "test"},
    ]
    
下一节为一些注意事项