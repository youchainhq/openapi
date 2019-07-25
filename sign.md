
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

生成sign的示例代码(仅供参考)：java
```java
import cn.hutool.core.bean.BeanUtil;
import cn.hutool.core.collection.CollectionUtil;
import cn.hutool.crypto.SecureUtil;
import cn.hutool.crypto.digest.DigestUtil;
import cn.hutool.json.JSONUtil;
import com.google.common.collect.Maps;
import org.apache.commons.lang3.StringUtils;

public class SignUtil {

    private static final String SIGN_TYPE = "signType";
    private static final String SIGN = "sign";
    
    public static String getSign(Object params, String secret) {
        TreeMap<String, Object> treeMap = changeObjectToTreeMap(params); // 字典顺序排序
        return getSign(treeMap, secret);
    }
    
    private static TreeMap<String, Object> changeObjectToTreeMap(Object data) {
        if (data instanceof Map) {
            return new TreeMap<>((Map) data);
        }
        TreeMap<String, Object> treeMap = Maps.newTreeMap();
        BeanUtil.beanToMap(data, treeMap, Boolean.FALSE, Boolean.TRUE);
        return treeMap;
    }
    
    private static String getSign(TreeMap<String, Object> treeMap, String appSecret) {
        treeMap.remove(SIGN);  // sign不参与签名
        StringBuilder sb = new StringBuilder();
        for (Map.Entry entry : treeMap.entrySet()) {
            Object eValue = entry.getValue();
            if (eValue == null) continue; // 空值不参与签名
            sb.append("&").append(entry.getKey()).append("=");
            if (eValue instanceof String || eValue instanceof Character
                    || eValue instanceof Number || eValue instanceof Boolean) { 
                if (StringUtils.isNotEmpty(eValue.toString())) {  // 空值不参与签名    
                    sb.append(eValue);
                }
            } else if (eValue instanceof Iterable && CollectionUtil.isNotEmpty((Iterable) eValue)) { // 空集合不参与签名
                sb.append(JSONUtil.toJsonStr(eValue));  // 集合转化成json格式参与签名
            } else {
                sb.append(JSONUtil.toJsonStr(eValue)); // 其它类型对象转化成json参与签名
            }
        }
        String signA = sb.substring(1);
        String stringSignTemp = signA + "&key=" + appSecret; // 生成stringSignTemp字符串
        String signExpect = new String(SecureUtil.md5(new String(stringSignTemp.getBytes(Charset.forName("UTF-8"))))).toUpperCase(); //md5并转为大写
        return signExpect;
    }
}
```