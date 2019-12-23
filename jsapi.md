# Youchain-JavaScript-SDK

基于 YOUCHAIN 开发的前端 JavaScript SDK，依赖[有令客户端App]，只能在[有令客户端App]中访问 Dpp，才能进行初始化。

## 当前版本为 0.x

### App userAgent 说明

> userAgent : YOUChainBrowser/3.3.71(3.0.2)

## 引入

支持以下几种安装方式

* 直接使用静态文件地址：

  ```
  https://ucstatic.iyouchain.com/sdk/youchainDapp/dapp-1.0.3.min.js
  ```
  通过sctipt标签引入该文件，会在全局生成名为 `YOUChainDapp` 的对象

## 使用

- SDK 初始化：

```JavaScript
let dapp = new window.YOUChainDapp.Agent(window, (type, data)=>{
    if (type === YOUChainDapp.PAYMENT_TYPE.success) { //支付成功
        success_callback()
    } else if (type === YOUChainDapp.PAYMENT_TYPE.error) { //支付出错
        error_callback()
    } else if (type === YOUChainDapp.PAYMENT_TYPE.cancel) { //支付取消
        cancel_callback()
    } else if (type === YOUChainDapp.COMMON_TYPE.version) { //可选
        // 监听客户端版本，可以用于判断是否初始化成功
    } 
});

```
### 1. `Navigation` 是Youchain Dapp中的导航模块，用来进行导航相关操作
 #### Example
 - 返回：
 ```JavaScript
dapp.navigation.goBack(); // 返回操作
 ```

### 2. `Pay` 是Youchain Dapp中的支付模块，用来进行支付相关操作

#### Example

- 唤起支付：

```JavaScript
/*
  params: 下单接口（payment/order/create），返回参数
 */
dapp.payment.pay(params); // 唤起支付窗口
```

- 唤起分享：

```JavaScript
dapp.shareUtil.share(options , extra = {}); // 唤起分享窗口

let options = {
    "title":`我在有令赚了 10 YOU`,
    "text": `快下载有令App和我一起赚赚赚！`,
    "url": "https://h5.iyouchain.com/download.html", //在微信、微博，Facebook等平台中使用
    "disableLocale": true,
    "images": [data.url], // 分享图片，分享时，前端不展示
    "type": 0
};

//可选
let extra = {
    shareImageUrl: data.url, // 分享时，前端展示
    share: { // 分享渠道过滤
        include:["Download"], //包含渠道
        exclude:["CopyLink"] //不包含渠道
    }
};

```

###### 分享渠道说明：
```
默认：Facebook、Twitter、Line、QQ、Wechat、WechatMoments、SinaWeibo、CopyLink（复制链接）
可选：UChain（有令逛逛）、Download（本地保存图片）
```

- 发送自定义命令
```JavaScript
 dapp.postMessage(type, data);
 
 //1、 唤起另一个 webview 
  dapp.postMessage("go_url", {
    title: "快下载有令App ！",
    url: "https://h5.iyouchain.com/download.html",
    navigationHidden: false, //不隐藏标题栏
    shareMenu: true, //显示分享
    disableLocale: true});  
```

### 许可证

> Copyright (c) 2019 youchain.cc

### 基于 MIT 协议发布
