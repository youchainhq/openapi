
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
| feeType       | 是      | String(32)  | 奖励币种 默认YOU：YOU |
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
        "rewardId": "201907232035302718351142369065832448",
        "outTradeNo": "3019061317452700021002010661"
    }
}
```

|字段|说明|
| --- | --- |
|rewardId|有令开放平台交易号|
|outTradeNo|商户订单号|
