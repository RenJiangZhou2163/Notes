# Zabbix系统接口文档





## 获取API版本

### 说明

```
string apiinfo.version(array)
```

该方法用于获取 Zabbix API 版本。

### 参数

此方法可用于未经身份验证的用户，必须在发送 JSON-RPC 请求中不加“auth”参数的情况下调用。

`(array)` 该方法接受一个空的数组。

### 返回值

`(string)` 返回 Zabbix API 的版本。

从 Zabbix 2.0.4 版本开始，API 的版本与 Zabbix 的版本相匹配。

### 范例

#### 获取 API 版本

获取 Zabbix API 版本.

请求:

```
{
    "jsonrpc": "2.0",
    "method": "apiinfo.version",
    "params": [],
    "id": 1
}
```



响应:

```
{
    "jsonrpc": "2.0",
    "result": "2.4.0",
    "id": 1
}
```



### 来源

CAPIInfo::version() in *frontends/php/include/classes/api/services/CAPIInfo.php*.





2.2. correlation.delete


Description

该方法允许删除关联

请求方法

POST

URL

http://192.168.3.132/zabbix/api_jsonrpc.php

Content-Type

application/json

Request

 

 

示例一：

删除多个关联

{
    "jsonrpc": "2.0",

    "method": "correlation.delete",
    
    "params": [
    
        "1",    //要删除的关联的ID
    
        "2"
    
    ],
    
    "auth": "343baad4f88b4106b9b5961e77437688",

"id": 1

}

 

 

 

返回值

返回一个对象，该对象包含“相关性”属性下删除的关联的ID

Response

对应示例一：

{
    "jsonrpc": "2.0",

    "result": {
        "correlaionids": [
    
            "1",
    
            "2"
    
        ]
    
    },

"id": 1

}


————————————————
版权声明：本文为CSDN博主「宇翔XGT」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/u012469528/article/details/80916163