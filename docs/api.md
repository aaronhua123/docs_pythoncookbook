## 与Java后台定义的接口
#### 1.1 轮询任务接口  
    uri：/api/task/taskManage?methodname=queryTask
    方法名：queryTask  
    支持版本号：1.0.0  
请求参数说明：

名称 | 必填 | 类型 | 说明
-----|---|---|---
username|是|string|目标系统账号
password|是|string|密码
systemName|是|string|系统名称（附表3）
version|是|string|版本号

请求示例：


```
data:{
    "password":"123456",
    "systemName":"iscm",
    "version":"1.0.0",
    "username":"zhangsan"
    }
```



返回示例：

```
{
    "msg": "success",
    "code": 0,
    "data": {
        "password": "123456",
        "systemName": "iscm",
        "methodName": "queryzjsxDateByjrh",
        "username": "zhangsan",
        "params": "{\"jrh\":"",\"abc\":""}",
        "taskId": "1031354660771856384"
    },
    "timestamp": 1534729089954
}
```

注意：查询任务接口由于有账号密码等敏感信息，所以要对请求参数data的值进行AES 128位加密，加密因子双方约定，测试环境暂时是 "abcdefg"

#### 1.2 机器人通知任务状态接口
    uri：/api/task/taskManage?methodname=sendTaskStatus  
    方法名：sendTaskStatus  
    支持版本号：1.0.0  
    请求参数说明：
      
名称 | 必填 | 类型 | 说明
---|---|---|---
taskId|是|string|任务Id
taskStatus|是|int|任务运行状态 （具体见 附表2）
remark|否|string|备注（错误原因或其他，可不填）

请求示例：

```
data:{
    "password":"123456",
    "systemName":"iscm",
    "version":"1.0.0",
    "username":"zhangsan",
    "params":{
        "taskId":"1031355536785801217",
        "taskStatus":-1,
        "remark":"无"
        }
    }
```

返回示例：

```
{
    "msg": "success",
    "code": 0,
    "timestamp": 1534729366900
}
```


#### 1.3 机器人回推数据接口
    uri：/api/task/taskManage?methodname=sendTaskData
    方法名：sendTaskData
    支持版本号：1.0.0
请求参数说明：

名称 | 必填 | 类型 | 说明
---|---|---|---
taskId|是|string|任务Id
taskData|是|string|任务数据（json字符串）
remark|否|string|备注（错误原因或其他，可不填）
请求示例：

```
data:{
    "password":"123456",
    "systemName":"iscm",
    "version":"1.0.0",
    "username":"zhangsan",
    "params":{
        "taskId":"1031355536785801217",
        "taskData":{"zjsxDate":"2018-01-05"},
        "remark":"无"
        }
     }
```
返回示例：

```
{
    "msg": "success",
    "code": 0,
    "timestamp": 1534729366900
}
```

