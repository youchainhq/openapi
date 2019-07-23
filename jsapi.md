# Youchain-JavaScript-SDK

基于 YOUCHAIN 开发的前端 JavaScript SDK

## 当前版本为 0.x

## 引入

支持以下几种安装方式

* 直接使用静态文件地址：

  ```
  https://ucstatic.iyouchain.com/sdk/youchainDapp/dapp.min.js
  ```
  通过sctipt标签引入该文件，会在全局生成名为 `YOUChainDapp` 的对象

## 使用
### 1. `YouchainDapp.Navigation` 是Youchain Dapp中的导航模块，用来进行导航相关操作
 #### Example
 - 返回：
```JavaScript
let dapp = new YOUChainDapp.Navigation() // 实例化导航对象

dapp.goBack() // 开始返回操作
 ```

### 2. `YouchainDapp.Pay` 是Youchain Dapp中的支付模块，用来进行支付相关操作

#### Example

- 支付结果监听：

```JavaScript
let dapp = new window.YOUChainDapp.Pay(window, (type, data)=>{
    if (type === YOUChainDapp.PAYMENT_TYPE.success) {
        success_callback()
    } else if (type === YOUChainDapp.PAYMENT_TYPE.error) {
        error_callback()
    } else if (type === YOUChainDapp.PAYMENT_TYPE.cancel) {
        cancel_callback()
    }
});
```

- 唤起支付：

```JavaScript
dapp.init(params) // 唤起支付窗口
```
### 许可证

> Copyright (c) 2019 youchain.cc

### 基于 MIT 协议发布
