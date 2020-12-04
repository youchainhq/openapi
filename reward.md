
# API 列表 (需 scope 为 snsapi_reward)
注意：使用奖励接口前提条件是需要dapp已绑定用户付款的有令用户id，如需手动绑定，请联系有令运营人员。

## /reward/send/single 发送单笔奖励

```
POST
https://dev-open.youchainapi.com/reward/send/single
```
参数示例：
```$xslt
POST方式：
Content-Type: application/json
{
    "appId": "yc792344e039b583096cd6f84e1a8d8cb0",
    "mchId": "mch87ca69344dd58a8138e8526c65e32a40",
    "openId": "NDM1ZjViYzVmZGI4NjMzMWY5Njk5NjIyMzIxNzZjNTE",
    "outTradeNo": "3019061317452700021002010661",
    "nonceStr": "25c88b08c01f4c28b494cc005054cf86",
    "signType": "MD5",
    "sign": "FEAFF2C0F9589BB678EC40C38B1B2958",
    "body": "奖励分红",
    "amount": "22.2212",
    "feeType": "you"
}
```
参数说明：

| 参数           | 必传    | 类型/限制    | 说明  |
| --------      | -----:  | --------:   | :---- |
| appId         | 是      | String(40)  | 注册dapp时返回的appid  |
| mchId         | 是      | String(40)  | 注册开放平台账户时返回的mchId |
| openId        | 是      | String(64)  | 用户的openId |
| outTradeNo    | 是      | String(32)  | 商户订单号 |
| body          | 是      | String(128) | 商品描述 |
| feeType       | 是      | String(32)  | 奖励币种 you：YOU copper_coin：铜币|
| amount        | 是      | String(32)  | 奖励数量、金额 |
| nonceStr      | 是      | String(32)  | 随机字符串 |
| signType      | 是      | String(32)  | 签名类型，目前只支持MD5。传MD5 |
| sign          | 是      | String(32)  | 参数签名，防篡改，请参考 [【数字签名规则】](sign.md) |

返回说明：

正确时返回的JSON数据包如下：

```
{
    "msg": "",
    "ret": 0,
    "data": {
        "appId": "yc792344e039b583096cd6f84e1a8d8cb0",
        "mchId": "mch87ca69344dd58a8138e8526c65e32a40",
        "openId": "NDM1ZjViYzVmZGI4NjMzMWY5Njk5NjIyMzIxNzZjNTE",
        "rewardId": "202012042001347767532327764657836032",
        "outTradeNo": "3019061317452700021002010163",
        "rewardStatus": "SUCCESS",
        "body": "奖励分红",
        "feeType": "you",
        "amount": "6.7",
        "nonceStr": "db0eccc8233c40338607edf08f1c5e40",
        "signType": "MD5",
        "sign": "B6F6EFF45FDFD975272EFA1EC0C42745"
    }
}
```

|字段|说明|
| --- | --- |
|rewardId|有令开放平台交易号|
|outTradeNo|商户订单号|
|rewardStatus|奖励状态 INIT初始化，未开始奖励 SUCCESS奖励成功 FAILED奖励失败|

错误说明：

| 字段      | 说明 |
| ---       | --- |
| 11001     | 参数解析错误 |
| 11012     | 参数错误 |
| 12001     | 不具备该接口scope权限 |
| 40001     | appid 错误 |
| 42001     | 商家状态不正常 |
| 42004     | 用户不存在 |
| 42005     | 用户状态不正常 |
| 42009     | 请输入交易号或第三方订单号 |
| 42007     | sign签名不正确 |
| 42011     | 找不到该订单 |
| 42026     | 商家未绑定付款账户 |



## 奖励订单查询接口 （dapp服务端调用）
接口说明：  
该接口用于dapp查询奖励接口
```
GET
https://dev-open.youchainapi.com/reward/order/query
```
参数说明：

| 参数           | 必传    | 类型/限制    | 说明  |
| --------      | -----:  | --------:   | :---- |
| appId         | 是      | String(40)  | 注册dapp时返回的appid |
| mchId         | 是      | String(40)  | 注册开放平台账户时返回的mchId |
| rewardId      | 否      | String(80)  | 有令开放平台交易号 rewardId与outTradeNo必须二传一 |
| outTradeNo    | 否      | String(32)  | 商户订单号 prepayId与outTradeNo必须二传一 |
| nonceStr      | 是      | String(32)  | 随机字符串 |
| signType      | 是      | String(32)  | 签名类型，目前只支持MD5 |
| sign          | 是      | String(32)  | 参数签名，防篡改，请参考 [【数字签名规则】](sign.md)  |

返回值说明：

| 字段            | 说明  |
| ---             | --- |
| appId           | 注册dapp时返回的appid |
| mchId           | 注册开放平台账户时返回的mchId |
| openId          | 下单用户的openId |
| nonceStr        | 随机字符串 |
| signType        | 签名类型 MD5 |
| sign	          | 签名|
| rewardId        | 有令开放平台交易号 |
| outTradeNo      | 商户订单号 |
| rewardStatus    | 订单状态：SUCCESS已成功，FAILED已失败，INIT奖励中 |
| body	          | 商品描述 |
| amount	      | 奖励金额 |
| feeType	      | 奖励币种 you：YOU copper_coin：铜币|
返回值示例：
```$xslt
{
    "msg": "",
    "ret": 0,
    "data": {
        "appId": "yc792344e039b583096cd6f84e1a8d8cb0",
        "mchId": "mch87ca69344dd58a8138e8526c65e32a40",
        "openId": "NDM1ZjViYzVmZGI4NjMzMWY5Njk5NjIyMzIxNzZjNTE",
        "rewardId": "201907311112069958353899686865801216",
        "outTradeNo": "3019061317452700021002010663",
        "rewardStatus": "SUCCESS",
        "body": "奖励分红",
        "feeType": "you",
        "amount": "6.7000",
        "nonceStr": "79832d96e5f64c6286567f4c880a50df",
        "signType": "MD5",
        "sign": "759CC69D027A1B3356B8821AEF28A198"
    }
}
```
错误说明：

| 字段      | 说明 |
| ---       | --- |
| 11001     | 参数解析错误 |
| 11012     | 参数错误 |
| 12001     | 不具备该接口scope权限 |
| 40001     | appid 错误 |
| 42009     | 请输入交易号或第三方订单号 |
| 42007     | sign签名不正确 |
| 42011     | 找不到该订单 |
