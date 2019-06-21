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

## 生成随机数算法
有令开放API接口协议中包含字段nonceStr，主要保证签名不可预测。我们推荐生成随机数算法如下：使用不带"-"的uuid。

## 数字签名
签名生成的通用步骤如下：  
第一步，设所有发送或者接收到的数据为集合M，除sign字段外，将集合M内非空参数值的参数按照参数名ASCII码从小到大排序（字典序），使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串stringA。  
特别注意以下重要规则：  
◆ 参数名ASCII码从小到大排序（字典序）；  
◆ 如果参数的值为空不参与签名；  
◆ 参数sign不参与签名；  
◆ 参数名区分大小写；  
◆ 验证调用返回或有令开放平台主动通知签名时，传送的sign参数不参与签名，将生成的签名与该sign值作校验。  
◆ 有令开放平台返回的应答或通知消息可能会由于升级增加参数，验证签名时必须支持增加的扩展字段  
第二步，在stringA最后拼接上key得到stringSignTemp字符串，并对stringSignTemp进行MD5运算，再将得到的字符串所有字符转换为大写，得到sign值signValue。  
◆ key设置路径：有令开放平台或找有令运营人员获取  
举例：假设传送的参数如下：
```$xslt
POST方式：Content-Type: application/json
{
    "appId": "yc984a80fbebd32e7fd18f0b61e2cfb2d1",
    "mchId": "test_mch_id_001",
    "deviceInfo": "WEB",
    "nonceStr": "25c88b08c01f4c28b494cc005054cf86",
    "signType": "MD5",
    "body": "购买VIP元宝"
}
```
或
```$xslt
GET方式：
appId=yc984a80fbebd32e7fd18f0b61e2cfb2d1&mchId=test_mch_id_001&deviceInfo=WEB&&nonceStr=25c88b08c01f4c28b494cc005054cf86&signType=MD5&body=购买VIP元宝 
```
第一步：对参数按照key=value的格式，并按照参数名ASCII字典序排序如下：
```$xslt
stringA="appId=yc984a80fbebd32e7fd18f0b61e2cfb2d1&body=购买VIP元宝&deviceInfo=WEB&mchId=test_mch_id_001&nonceStr=25c88b08c01f4c28b494cc005054cf86&signType=MD5"
```
第二步：拼接API密钥：  
```$xslt
stringSignTemp=stringA+"&key=192006250b4c09247ec02edce69f6a2d" //注：key为有令开放平台设置的appSecret或找有令运营人员获取
sign=MD5(stringSignTemp).toUpperCase()="16A6E08A0A3D88DEC5A9EA6B7ADD0467" //注：signType=MD5时的签名方式
```
最终得到最终发送的数据：
```$xslt
POST方式：
Content-Type: application/json
{
    "appId": "yc984a80fbebd32e7fd18f0b61e2cfb2d1",
    "mchId": "test_mch_id_001",
    "deviceInfo": "WEB",
    "nonceStr": "25c88b08c01f4c28b494cc005054cf86",
    "signType": "MD5",
    "body": "购买VIP元宝",
    "sign": "16A6E08A0A3D88DEC5A9EA6B7ADD0467"
}
```
或
```
GET方式：
appId=yc984a80fbebd32e7fd18f0b61e2cfb2d1&mchId=test_mch_id_001&deviceInfo=WEB&&nonceStr=25c88b08c01f4c28b494cc005054cf86&signType=MD5&body=购买VIP元宝&sign=16A6E08A0A3D88DEC5A9EA6B7ADD0467
```

## 补单回调机制
注意:  
对后台通知交互模式，如果有令开放平台回调接口收到商家的应答不是“success”或超时，会认为通知失败，然后通过一定的策略(如45分钟共8次)定期重新发起通知，尽可能提高通知的成功率，但不保证通知最终能成功。  
由于存在重新发􏰘后台通知的情况，因此同样的通知可能会多次发􏰘给商户系统。商户系统必须能够正确处理重复的通知。有令开放平台推荐的做法是，当收到通知进行处理时，首先检查对应业务数据的状态，判断该通知是否已经处理过，如果没有处理过再进行处理，如果处理过直接返回“success”。在对业务数据进行状态检查和处理之前，要采用数据􏰙进行并发控制，以避免函数重入造成的数据混乱。


# 开放平台支付API 列表

## 统一下单接口（dapp服务端调用） 
接口说明：  
该接口用于dapp支付时下单操作，成功后将支付数据传递给jsapi以唤醒有令支付插件，弹出支付密码输入框并完成后续支付流程
```
POST
https://open.youchainapi.com/payment/order/create
```
参数说明：

| 参数           | 必传    | 类型/限制    | 说明  |
| --------      | -----:  | --------:   | :---- |
| appId         | 是      | String(40)  | 注册dapp时返回的appid  |
| mchId         | 是      | String(40)  | 注册开放平台账户时返回的mchId |
| openId        | 是      | String(64)  | 下单用户的openId |
| deviceInfo    | 否      | String(32)  | 设备号 H5页面可以传值WEB |
| outTradeNo    | 是      | String(32)  | 商户订单号 |
| body          | 否      | String(128) | 商品描述 |
| attach        | 否      | String(128) | 附加数据，在查询API和支付通知中原样返回，可作为自定义参数使用 |
| notifyUrl     | 是      | String(255) | 接收回调通知的URL，需给绝对路径，255 字以内，必须为外网可访问的url，且为商家授信的域名下，不能携带参数 |
| redirectUrl   | 否      | String(255) | 交易成功后的跳转URL，需给绝对路径，255 字以内，必须为外网可访问的url，且为商家授信的域名下。 |
| timeStart     | 否      | String(14)  | 商家交易起始时间 格式为yyyyMMddHHmmss，如2009年12月25日9点10分10秒表示为20091225091010，该时间取自商户服务器 |
| timeExpire    | 否      | String(14)  | 商家订单失效时间 格式为yyyymmddhhmmss，如2009年12月25日9点10分10秒表示为20091225091010，该时间取自商户服务器 |
| payType       | 是      | String(32)  | 交易类型 JSAPI -JSAPI支付,NATIVE -Native支付,APP-APP支付 |
| mchCreateIp   | 是      | String(128) | 商户下单服务器的ip |
| feeType       | 是      | String(32)  | 币种 默认YOU：YOU |
| totalFee      | 是      | String(32)  | 支付总金额 |
| nonceStr      | 是      | String(32)  | 随机字符串 |
| signType      | 是      | String(32)  | 签名类型，目前只支持MD5。传MD5 |
| sign          | 是      | String(32)  | 参数签名，防篡改 |
参数示例：
```
Content-Type: application/json
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

| 字段      | 说明 |
| ---       | --- |
| appId     | 注册dapp时返回的appid |
| payType   | 交易类型 JSAPI -JSAPI支付,NATIVE -Native支付,APP-APP支付 |
| content   | 支付数据 |
| timeStamp | 时间戳（毫秒）|
| nonceStr  | 随机字符串 |
| signType  | 签名类型 MD5 |
| sign	    | 签名 |
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
接口说明：  
该接口用于用户输入支付密码后JSAPI调用完成最终支付流程
```
POST
https://open.youchainapi.com/payment/order/pay
```
参数说明：

| 参数           | 必传    | 类型/限制    | 说明  |
| --------      | -----:  | --------:   | :---- |
| appId         | 是      | String(40)  | 注册的appid（统一下单接口返回的appId） |
| timestamp     | 是      | String(32)  | 时间戳（统一下单接口返回的timestamp） |
| payType       | 是      | String(32)  | JSAPI -JSAPI支付,NATIVE -Native支付,APP-APP支付（统一下单接口返回的payType） |
| content       | 是      | String(1000)| 支付数据（统一下单接口返回的content） |
| nonceStr      | 是      | String(32)  | 随机字符串（统一下单接口返回的nonceStr） |
| signType      | 是      | String(32)  | 签名类型：固定值MD5，（统一下单接口返回的signType） |
| sign          | 是      | String(32)  | 参数签名，防篡改（统一下单接口返回的sign） |
| password      | 否      | String(80)  | 用户支付密码（password和payToken必须二选一） |
| payToken      | 否      | String(80)  | 用户支付指纹码（password和payToken必须二选一）|
| uuid          | 否      | String(80)  | 指纹码uuid（若传payToken，则必须传uuid） |
参数示例：
```
Content-Type: application/json
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

| 字段        | 说明 |
| ---         | --- |
| appId       | 注册dapp时返回的appid |
| appName     | 注册dapp时返回的appname |
| timeStamp   | 时间戳（毫秒）|
| nonceStr    | 随机字符串 |
| feeType     | 实际支付币种 YOU |
| totalFee    | 实际支付金额 |
| timePaid    | 实际支付时间，格式为yyyyMMddHHmmss|
| orderStatus | 订单状态 |
| prepayId    | 有令开放平台订单号 |
| outTradeNo  | 商家订单号 |
| redirectUrl | 前端redirect地址，若为空直接关闭窗体不回调 |
| signType    | 签名类型 MD5 |
| sign	      | 签名 |
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
接口说明：  
该接口用于dapp查询支付接口，一般用于未收到支付回调时处理
```
GET
https://open.youchainapi.com/payment/order/query
```
参数说明：

| 参数           | 必传    | 类型/限制    | 说明  |
| --------      | -----:  | --------:   | :---- |
| appId         | 是      | String(40)  | 注册dapp时返回的appid |
| mchId         | 是      | String(40)  | 注册开放平台账户时返回的mchId |
| prepayId      | 否      | String(80)  | 有令开放平台交易号 prepayId与outTradeNo必须二传一 |
| outTradeNo    | 否      | String(32)  | 商户订单号 prepayId与outTradeNo必须二传一 |
| nonceStr      | 是      | String(32)  | 随机字符串 |
| signType      | 是      | String(32)  | 签名类型，目前只支持MD5 |
| sign          | 是      | String(32)  | 参数签名，防篡改 |

返回值说明：

| 字段            | 说明  |
| ---            | --- |
| appId           | 注册dapp时返回的appid |
| mchId           | 注册开放平台账户时返回的mchId |
| openId          | 下单用户的openId |
| deviceInfo      | 下单接口传的设备号 |
| nonceStr        | 随机字符串 |
| signType        | 签名类型 MD5 |
| sign	          | 签名|
| prepayId        | 有令开放平台交易号 |
| outTradeNo      | 商户订单号 |
| payType         | 交易类型 JSAPI -JSAPI支付,NATIVE -Native支付,APP-APP支付 |
| orderStatus     | 订单状态：NOTPAY未支付，PAID已支付，CLOSED已超时关闭，PAYERROR支付失败，REFUND已退款 |
| body	          | 商品描述 |
| attach	      | 附加数据，在查询API和支付通知中原样返回，可作为自定义参数使用 |
| timeStart	      | 商家订单开始时间，格式为yyyyMMddHHmmss |
| timeExpire	  | 商家订单过期时间，格式为yyyyMMddHHmmss |
| totalFee	      | 支付金额 |
| feeType	      | 支付币种：YOU |
| timePaid	      | 实际支付时间，格式为yyyyMMddHHmmss |
| mchWalletAddress| 商家收款钱包地址 |
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

## 支付完成通知接口 （有令开放平台调用dapp服务端的notifyUrl）
接口说明：  
该接口用于有令开放平台通知dapp服务端该笔支付已完成
```
POST
统一下单接口商家传入的notifyUrl
```
参数说明：

| 参数           | 类型        | 说明 |
| --------      | --------:   | :---- |
| appId         | String(40)  | 注册dapp时返回的appid |
| mchId         | String(40)  | 注册开放平台账户时返回的mchId |
| openId        | String(80)  | 下单用户的openId |
| deviceInfo    | String(32)  | 设备号 H5页面可以传值WEB |
| prepayId      | String(80)  | 有令开放平台交易号 |
| outTradeNo    | String(32)  | 商户订单号 |
| payType       | String(32)  | 交易类型 JSAPI -JSAPI支付,NATIVE -Native支付,APP-APP支付 |
| orderStatus   | String(32)  | 订单支付状态 PAID—支付成功 REFUND—转入退款 PAYERROR-支付失败 CLOSED-订单已关闭 NOTPAY-未支付 |
| attach        | String(128) | 附加数据，在查询API和支付通知中原样返回，可作为自定义参数使用 |
| timeStart     | String(14)  | 商家交易起始时间 格式为yyyyMMddHHmmss，如2009年12月25日9点10分10秒表示为20091225091010 |
| timeExpire    | String(14)  | 商家订单失效时间 格式为yyyymmddhhmmss，如2009年12月25日9点10分10秒表示为20091225091010 |
| totalFee      | String(32)  | 支付总金额 |
| feeType       | String(32)  | 币种 默认YOU：YOU |
| timePaid      | String(14)  | 订单实际支付时间 格式为yyyymmddhhmmss，如2009年12月25日9点10分10秒表示为20091225091010 |
| mchWalletAddress|String(128)| 商家收款钱包地址 |
| nonceStr      | String(32)  | 随机字符串 |
| signType      | String(32)  | 签名类型，目前只支持MD5 |
| sign          | String(32)  | 参数签名，防篡改 |

参数示例：
```$xslt
Content-Type: application/json
{
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
    "attach": "storeId=220000011&operator=lzol",
    "timeStart": "20190613163510",
    "timeExpire": "20190623163510",
    "totalFee": 5.22,
    "feeType": "YOU",
    "mchWalletAddress", "wallet_address_001",
    "timePaid": "20190619164504"
}
```
有令开放平台通过调用notifyUrl通知商家，商家做业务处理后，需要以字符串的形式反馈处理结果，若处理成功内容为: success  
返回值说明：  
有令开放平台收到HTTP状态码是"200 OK"且响应体为success时表示处理成功，此后不再进行后续通知  
其它情况处理不成功（未收到任何响应或超时）（HTTP状态码不是"200 OK"）（HTTP状态码是"200 OK"但响应体不为success），有令开放平台通过补单机制再次通知
