# 环境
```
测试环境：https://dev-open.youchainapi.com
正式环境：https://open.youchainapi.com
```

# 开放平台支付API 返回语义

```
{
  "ret":0, // 0 表示正常， >0 表示发生错误，数值为错误号
  "data":data, // 可能是任何类型
  "msg":"error msg" // 发生错误时，才会有该字段
}
```
# 支付流程
![支付流程图](arts/PAYMENT.png)

# 开放平台支付API 列表

## 统一下单接口（dapp服务端调用） 
接口说明：<br>
该接口用于dapp支付时下单操作，成功后将支付数据传递给jsapi以唤醒有令支付插件，弹出支付密码输入框并完成后续支付流程
```
POST
https://open.youchainapi.com/payment/order/create
Content-Type: application/json
```
参数说明：

| 参数           | 必传    | 类型/限制    |  说明  |
| --------      | -----:  | --------:   | :---- |
| appId         | 是      | String(32)  |  注册dapp时返回的appid  |
| mchId         | 是      | String(32)  |  注册开放平台账户时返回的mchId |
| openId        | 是      | String(80)  |  下单用户的openId    |
| deviceInfo    | 否      | String(32)  |  设备号 H5页面可以传值WEB    |
| nonceStr      | 是      | String(32)  |  随机字符串    |
| signType      | 是      | String(32)  |  签名类型，目前只支持MD5。传MD5    |
| sign          | 是      | String(32)  |  参数签名，防篡改    |
| outTradeNo    | 是      | String(32)  |  商户订单号    |
| body          | 否      | String(128) |  商品描述    |
| attach        | 否      | String(128) |  附加数据，在查询API和支付通知中原样返回，可作为自定义参数使用    |
| notifyUrl     | 是      | String(255) |  接收回调通知的URL，需给绝对路径，255 字以内，必须为外网可访问的url，不能携带参数 |
| redirectUrl   | 否      | String(255) |  交易成功后的跳转URL，需给绝对路径，255 字以内，必须为外网可访问的url，不能携带参数。 |
| timeStart     | 否      | String(14)  |  商家交易起始时间 格式为yyyyMMddHHmmss，如2009年12月25日9点10分10秒表示为20091225091010，该时间取自商户服务器 |
| timeExpire    | 否      | String(14)  |  商家订单失效时间 格式为yyyymmddhhmmss，如2009年12月25日9点10分10秒表示为20091225091010，该时间取自商户服务器 |
| payType       | 是      | String(10)  |  交易类型 JSAPI -JSAPI支付,NATIVE -Native支付,APP-APP支付    |
| mchCreateIp   | 是      | String(128) |  商户下单服务器的ip    |
| feeType       | 是      | String(32)  |  币种 默认YOU：YOU    |
| totalFee      | 是      | String(128) |  支付总金额    |
参数示例：
```
{
    "appId": "yc984a80fbebd32e7fd18f0b61e2cfb2d1",
    "mchId": "test_mch_id_001",
    "openId": "OGEwZTU4NTJlNjA1ZWM3N2IxOTIzZGQ2NjFlM2I3YTI",
    "deviceInfo": "WEB",
    "nonceStr": "25c88b08c01f4c28b494cc005054cf86",
    "signType": "MD5",
    "sign": "976EA6B92A48DDA7AE4C5B6F3CB0F113",
    "outTradeNo": "201906131745270002100201033",
    "body": "购买VIP元宝",
    "attach": "storeId=220000011&operator=lzol",
    "notifyUrl": "https://wxapp.80sis.com/notify.json",
    "redirectUrl": "https://wxapp.80sis.com/gametest.html?gameid=testgameid",
    "timeStart": "20190613163510",
    "timeExpire": "20190623163510",
    "payType": "JSAPI",
    "mchCreateIp": "188.99.36.201",
    "totalFee": "5.22",
    "feeType": "YOU"
}
```
返回值说明：

| 字段      |说明 |
| ---      | --- |
|appId     |注册dapp时返回的appid |
|nonceStr  |随机字符串 |
|payType   |交易类型 JSAPI -JSAPI支付,NATIVE -Native支付,APP-APP支付 |
|content   |支付数据 |
|timeStamp |时间戳（毫秒）|
|signType  |签名类型 MD5 |
|sign	   |签名|
返回值示例：
```$xslt
{
    "msg": "",
    "ret": 0,
    "data": {
        "appId": "yc984a80fbebd32e7fd18f0b61e2cfb2d1",
        "nonceStr": "aa0210b76a6045f2bdf4049a21ef34e8",
        "signType": "MD5",
        "sign": "FDDBF373054F86AB66180E08C5086912",
        "payType": "JSAPI",
        "content": "prepayId=201906191644498078338763128370237440;outTradeNo=201906131745270002100201064",
        "timestamp": "1560933889650"
    }
}
```

## 支付接口 （js调用）
接口说明：<br>
该接口用于用户输入支付密码后JSAPI调用完成最终支付流程
```
POST
https://open.youchainapi.com/payment/order/pay
Content-Type: application/json
```
参数说明：

| 参数           | 必传    | 类型/限制    |  说明  |
| --------      | -----:  | --------:   | :---- |
| appId         | 是      | String(32)  |  注册的appid（统一下单接口返回的appId）  |
| timestamp     | 是      | String(32)  |  时间戳（统一下单接口返回的timestamp） |
| nonceStr      | 是      | String(32)  |  随机字符串（统一下单接口返回的nonceStr） |
| signType      | 是      | String(32)  |  签名类型：固定值MD5，（统一下单接口返回的signType） |
| sign          | 是      | String(32)  |  参数签名，防篡改（统一下单接口返回的sign） |
| payType       | 是      | String(32)  |  JSAPI -JSAPI支付,NATIVE -Native支付,APP-APP支付（统一下单接口返回的payType） |
| content       | 是      | String(1000)|  支付数据（统一下单接口返回的content） |
| password      | 否      | String(80)  |  用户支付密码（password和payToken必须二选一）  |
| payToken      | 否      | String(80)  |  用户支付指纹码（password和payToken必须二选一）|
| uuid          | 否      | String(80)  |  指纹码uuid（若传payToken，则必须传uuid）   |
参数示例：
```
{
    "appId": "yc984a80fbebd32e7fd18f0b61e2cfb2d1",
    "nonceStr": "aa0210b76a6045f2bdf4049a21ef34e8",
    "signType": "MD5",
    "sign": "FDDBF373054F86AB66180E08C5086912",
    "payType": "JSAPI",
    "content": "prepayId=201906191644498078338763128370237440;outTradeNo=201906131745270002100201064",
    "timestamp": "1560933889650",
    "password": "password",
    "payToken": "",
    "uuid": ""
}
```
返回值说明：

| 字段       |说明 |
| ---        | --- |
|appId       |注册dapp时返回的appid |
|appName     |注册dapp时返回的appname |
|timeStamp   |时间戳（毫秒）|
|nonceStr    |随机字符串 |
|feeType     |实际支付币种 YOU |
|totalFee    |实际支付金额 |
|timePaid    |实际支付时间，格式为yyyyMMddHHmmss|
|orderStatus |订单状态 |
|prepayId    |有令开放平台订单号 |
|outTradeNo  |商家订单号 |
|redirectUrl |前端redirect地址，若为空直接关闭窗体不回调 |
|signType    |签名类型 MD5 |
|sign	     |签名|
返回值示例：
```$xslt
{
    "msg": "",
    "ret": 0,
    "data": {
        "appId": "yc984a80fbebd32e7fd18f0b61e2cfb2d1",
        "appName": "测试应用",
        "timestamp": "1560933904",
        "nonceStr": "9ab1b2a7e4074b3aa22ec769d59493e1",
        "signType": "MD5",
        "sign": "EB37C6543B3810CAC21E624F0BCE81BF",
        "prepayId": "201906191644498078338763128370237440",
        "outTradeNo": "201906131745270002100201064",
        "orderStatus": "PAID",
        "feeType": "YOU",
        "totalFee": "5.2200",
        "timePaid": "20190619164504",
        "redirectUrl": "https://wxapp.80sis.com/gametest.html?gameid=testgameid"
    }
}
```

## 订单查询接口 （dapp服务端调用）
接口说明：<br>
该接口用于dapp查询支付接口，一般用于未收到支付回调时处理
```
GET
https://open.youchainapi.com/payment/order/query
```
参数说明：

| 参数           | 必传    | 类型/限制    |  说明  |
| --------      | -----:  | --------:   | :---- |
| appId         | 是      | String(32)  |  注册dapp时返回的appid  |
| mchId         | 是      | String(32)  |  注册开放平台账户时返回的mchId |
| prepayId      | 否      | String(80)  |  有令开放平台交易号 prepayId与outTradeNo必须二传一 |
| outTradeNo    | 否      | String(32)  |  商户订单号 prepayId与outTradeNo必须二传一 |
| nonceStr      | 是      | String(32)  |  随机字符串    |
| signType      | 是      | String(32)  |  签名类型，目前只支持MD5。  |
| sign          | 是      | String(32)  |  参数签名，防篡改    |

返回值说明：

| 字段            |说明 |
| ---            | --- |
|appId           |注册dapp时返回的appid |
|mchId           |注册开放平台账户时返回的mchId |
|openId          |下单用户的openId |
|deviceInfo      |下单接口传的设备号 |
|nonceStr        |随机字符串 |
|signType        |签名类型 MD5 |
|sign	         |签名|
|prepayId        |有令开放平台交易号|
|outTradeNo      |商户订单号|
|payType         | 交易类型 JSAPI -JSAPI支付,NATIVE -Native支付,APP-APP支付 |
|orderStatus     |订单状态：NOTPAY未支付，PAID已支付，CLOSED已超时关闭，PAYERROR支付失败，REFUND已退款|
|body	         |商品描述|
|attach	         |附加数据，在查询API和支付通知中原样返回，可作为自定义参数使用|
|timeStart	     |商家订单开始时间，格式为yyyyMMddHHmmss|
|timeExpire	     |商家订单过期时间，格式为yyyyMMddHHmmss|
|totalFee	     |支付金额|
|feeType	     |支付币种：YOU|
|timePaid	     |实际支付时间，格式为yyyyMMddHHmmss|
|mchWalletAddress|商家收款钱包地址|
返回值示例：
```$xslt
{
    "msg": "",
    "ret": 0,
    "data": {
        "appId": "yc984a80fbebd32e7fd18f0b61e2cfb2d1",
        "mchId": "test_mch_id_001",
        "openId": "OGEwZTU4NTJlNjA1ZWM3N2IxOTIzZGQ2NjFlM2I3YTI",
        "deviceInfo": "WEB",
        "nonceStr": "3b45f2f47772478287ee604585d8e56f",
        "signType": "MD5",
        "sign": "6ECB30C4543764C1D1E48E6F24128849",
        "prepayId": "201906191644498078338763128370237440",
        "outTradeNo": "201906131745270002100201064",
        "payType": "JSAPI",
        "orderStatus": "PAID",
        "body": "购买VIP元宝",
        "attach": "storeId=220000011&operator=lzol",
        "timeStart": "20190613163510",
        "timeExpire": "20190623163510",
        "totalFee": 5.22,
        "feeType": "YOU",
        "timePaid": "20190619164504",
        "mchWalletAddress": "wallet_address_001"
    }
}
```
