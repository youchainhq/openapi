本示例可用于h5类型的dapp接入有令开放平台的授权登录功能，仅供参考。    
主要步骤如下：    
第一步：检查url里面有没有code参数     
第二步：有code参数则认为用户已经授权，可以直接使用code去开放平台换access_token（调用https://dev-open.youchainapi.com/oauth2/access_token接口）    
第三步：若无code参数，则需要先进行授权，那么执行跳转到https://dev-open.youchainapi.com/connect/oauth2/authorize，以唤醒有令app授权页面，
用户点击授权后有令app会在回调地址url后面追加上code参数并打开该回调地址页面。重新执行第一步操作

注意：code仅有5分钟有效期且只能使用一次，获得code之后请立即使用code换取access_token和openid

假如dapp开户时配置的回调地址是 https://front.youchain.com/game/index.html,    
dapp后台使用code换取access_token的接口是 https://backend.youchain.com/game/code.do    
那么index.html内有js代码如下：
```js
    var appid = "yc792344e039b583096cd6f84e1a8d8cb0";
    init();
    function init() {
        var js = urljson();  // 解析url里面的参数
        if (typeof js != "undefined") {
            if (typeof js.code != "undefined" && js.code) { // 认为已授权
                beginGame(js.code);
            } else { // 需要先进行授权登录操作
                toAuthLogin(); 
            }
        }
    }
    function toAuthLogin() {
        var uri = encodeURIComponent("https://front.youchain.com/game/index.html").toLowerCase();
        var link = "https://dev-open.youchainapi.com/connect/oauth2/authorize?appid=" + appid + "&redirect_uri=" + uri 
                        + "&response_type=code&scope=snsapi_userinfo&state=STATE";
        window.location.href = link;
    }
    function beginGame(code) {
        $.ajax({
            type: "GET",
            url: "https://backend.youchain.com/game/code.do",
            data: "appid=" + appid + "&code=" + code,
            success: function(data){
                // 此处处理游戏逻辑
            },
            error: function (XMLHttpRequest, textStatus, errorThrown) {
                // 状态码
                console.log(XMLHttpRequest.status);
                // 状态
                console.log(XMLHttpRequest.readyState);
                // 错误信息
                console.log(textStatus);
            }
        });
    }
    function urljson() {
        var url = window.location.href;
        if(url.indexOf('?')!=-1){
            url=url.substring(url.indexOf('?')+1,url.split("").length);
            var str="{\"";
            var ch=url.split("");
            var size=ch.length;
            var gogo=true;
            for(var i=0;i<size;i++){
                if(gogo){
                    if(ch[i]=='='){
                        gogo=false;
                        str+="\":\"";
                    }else{
                        str+=ch[i];
                    }
                }else{
                    if(ch[i]=='&'){
                        gogo=true;
                        str+="\",\"";
                    }else{
                        str+=ch[i];
                    }
                }
            }
            str+="\"}";
            return JSON.parse(str);
        }else{
            return undefined;
        }
    }
```

服务端：https://backend.youchain.com/game/code.do代码如下：
```java
    @RestController
    public class IndexController {
        @GetMapping("/game/code.do")
        public String code(@RequestParam String appid, @RequestParam String code) {
            String url = "https://dev-open.youchainapi.com/oauth2/access_token";
            String appSecret = "xxxx"; // 开通app时的appSecret
            Map<String, Object> params = new HashMap<>();
            params.put("appid", appid);
            params.put("code", code);
            params.put("secret", appSecret);
            params.put("grant_type", "authorization_code");
            String result = HttpUtil.get(url, params);
            Map<String, Object> resultMap = JsonUtil.toMap(result);
            String accessToken = (String) resultMap.get("access_token");
            String openId = (String) resultMap.get("openid");
            // TODO 处理自己的业务
            return result;
        }
    }
    
```