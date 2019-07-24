
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
