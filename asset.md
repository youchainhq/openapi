
# API 列表 (需 scope 为 snsapi_asset)

## /assets/user/query

```
GET
https://dev-open.youchainapi.com/assets/user/query?appId=APPID&openid=OPENID&feeType=FEETYPE&signType=MD5&sign=SIGN
```

参数说明：

| 参数        | 必须    |  说明  |
| --------   | -----:   | :----: |
| appId        | 是      |  app注册时的appId  |
| openId        | 是     |  用户唯一标识    |
| feeType      | 否    |  查询资产的类型  you、gold_coin、silver_coin、copper_coin、whale_coin等 |
| signType      | 是    |  加密方式。固定值：MD5    |
| sign      | 是    |  加密值    |

返回说明：

正确时返回的JSON数据包如下：

```
{
    "msg": "",
    "ret": 0,
    "data": [
        {
            "logo": "https://ucimg.iyouchain.com/static/icon/u_3x.png",
            "name": "YOU",
            "feeType": "you",
            "amount": 956.7
        },
        {
            "logo": "https://ucimg.iyouchain.com/upload/4337e1/56982d5326c83ce6f0f040459a-1608676267851776.png",
            "name": "有令创世纪念金币",
            "feeType": "gold_coin",
            "amount": 14
        },
        {
            "logo": "https://ucimg.iyouchain.com/upload/32504c/3902a885508be23e5b15df439d-1608676267851776.png",
            "name": "有令百万用户纪念银币",
            "feeType": "silver_coin",
            "amount": 12
        },
        {
            "logo": "https://ucimg.iyouchain.com/upload/0b7530/b75d7342b369761153f7ddce58-1608676267851776.png",
            "name": "铜币",
            "feeType": "copper_coin",
            "amount": 60
        },
        {
            "logo": "https://ucimg.iyouchain.com/upload/023d51/5dd494d6bf7269d53e379b78da-1635019168743424.png",
            "name": "鲸鱼创世通证幸运星",
            "feeType": "whale_coin",
            "amount": 41
        }
    ]
}
```

|字段|说明|
| --- | --- |
|logo|资产的图标|
|name|资产的名字|
|feeType|资产类型|
|amount	|资产数量|

