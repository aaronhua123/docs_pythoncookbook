### 注意事項
1. 所有需要登陆的模块都要继承`BaseApi`且实现`login,logout,is_session_expire`三个方法.     
    * `login`必须返回登陆成功的`session`。  
    * `logout`须先调用系统的登出请求再`session.closei()`  
    * `s_session_expire`返回`True`则表明`session`过期。

2. 所有`service`必须继承`BaseService`类且实现`api_var()`方法
    * `api_var`必须返回对应的`api`类。

3. 如果`Api`类继承了`BaseApi`，则被实例化的时候`username,password`必填且格式必须为`Api(username="zhangsan",password="pwd")`,不可写为`Api("zhangsan","pwd")`

4. 所有`service`类的方法用@service_exception_decorator装饰器修饰，不用额外try catch，且方法应直接返回api的数据，`service`调用`api`方法需要传参的时候参数直接写`self.eval_params`：
`api.method(**self.eval_params)`

5. 所有相关`Api`类的方法应返回`json`数据，如果需要维持`session`则需要使用`@auto_login_decorator`装饰器修饰类，方法处应使用`@exception_capture`和`@session_checker`装饰器修饰对应的业务方法。
且业务方法也无须额外`try catch`。
    * `@auto_login_decorator`装饰器有3个可选参数：`expire_time，unit。`分别代表超时时间与单位，默认为3分钟。另外如果有`token`等后续请求必填的字段请求，则可以再加上`key=method`，在`__init__`处获取。例子，某个系统需要一个名为`token`的字段作为后续请求的`header`必填值，则：

```
@auto_login_decorator(expire_time=10, unit=UNIT_MINUTES, token="get_token")
class TestApi(BaseApi):
    def __init__(self, username, password):
        super().__init__(username, password)
        self.open_token = self._.get("token", "")
    def login(self):
        # do login
        pass
    
    def logout(self):
        pass
    
    def is_session_expired(self, session, **kwargs):
        pass
    
    def get_token(self):
        # do request token thing
        return "token"
```
传入的`token="get_token"` `token`为`key`,`get_token`为对应的方法，`__init__`直接`selt._.get("token")`则可以获取到对应的全局`token`值。