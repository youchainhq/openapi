# API 返回语义

```
{
  "ret":0, // 0 表示正常， >0 表示发生错误，数值为错误号
  "data":data, // 可能是任何类型
  "msg":"error msg" // 发生错误时，才会有该字段
}
```

# API 列表

## /users/show

```
GET
https://api.youchainapi.com/users/show?access_token=ACCESS_TOKEN&openid=OPENID&lang=zh_CN
```

参数说明：


| 参数        | 必须    |  说明  |
| --------   | -----:   | :----: |
| access_token        | 是      |   上一步拿到的 access_token  |
| openid        | 是     |  用户唯一标识    |
| lang        | 是     |  国家地区和语言版本    |

返回说明：

正确时返回的JSON数据包如下：

```
{
  "ret":0,
  "data":{
    "openid":" OPENID",
    "username": USERNAME,
    "gender":1,
    "avatar":"https://ucimg.ihuanqu.com/avatar/54f69c/6b83f463310db6b2c8181d09fc-1600614462193664.jpg",
    "cloudauth":"1"
  }
}
```

|字段|说明|
| --- | --- |
|openid|用户的唯一标识|
|username|用户名|
|gender|性别 0 未设置 1 男 2 女|
|avatar	|头像|
|cloudauth| 0 未认证,  1 认证中 2 通过认证  3 临时认证  4 认证失败|
