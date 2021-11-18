# Vue 微信JSAPI支付

## 技术栈

[![vue-qr](https://img.shields.io/badge/vue--qr-2.5.0-brightgreen.svg?style=plastic)](https://www.npmjs.com/package/vue-qr/)
[![weixin-js-sdk](https://img.shields.io/badge/weixin--js--sdk-1.6.0-green.svg?style=plastic)](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/JS-SDK.html/)
[![lib-flexible](https://img.shields.io/badge/lib--flexible-0.3.2-yellowgreen.svg?style=plastic)](http://nodejs.cn/)
[![postcss-pxtorem](https://img.shields.io/badge/postcss--pxtorem-5.1.1-yellow.svg?style=plastic)](http://nodejs.cn/)
[![vant](https://img.shields.io/badge/vant-2.12.29-orange.svg?style=plastic)](https://youzan.github.io/vant/#/zh-CN/address-edit/)
[![vConsole](https://img.shields.io/badge/vConsole-3.9.1-green.svg?style=plastic)](https://www.npmjs.com/package/vconsole/)

## 项目背景

PC+H5实现微信(JSAPI)支付

- 微信H5 vConsole 调试 开启界面 http://debugx5.qq.com/
- 方式二 项目引入 yarn add vconsole  3.9,1

## 实施流程

- PC端套餐购买界面中, 根据指定参数及URL组合, 通过`vue-qr`合成二维码
	- 参数: 套餐id / 购买份数 / token / [UUID](/fe/javascript?id=获取一个永不重复的id多种方法)(随机字符)
- H5工程接入`weixin-js-sdk`, 同时微信公众号后台需要做好配置 🔗 [传送门](https://pay.weixin.qq.com/wiki/doc/api/wxpay/ch/pages/OfficialPayMent.shtml)
- H5工程进行网页授权取code: 🔗 [传送门](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_webpage_authorization.html)
- 拿到code后更换SDK需要的签名等信息, 环境唤醒
- 采用微信支付接口进行拉起支付逻辑`chooseWXPay`

备注: 过程中PC端需要有一个获取订单状态的接口, 使用前端生成的UUID进行不断轮询, 监听订单状态, 用来及时更新支付状态!


## 实践过程中需解决的问题

- 大工程域下H5工程二级目录配置
	- 前端`vue.config.js`内`publicPath`配置带上与`nginx`匹配的目录名称, 如: `/pay/`
- `405 Not Allowed` 问题
	- 可nginx处理, 该项目为前端处理, `proxy`代理直接指向api的根地址, 接口中均带上api根地址, 不需要进行`pathRewrite`重写, 由于项目需要扩展, 全局请求不要设置`baseURL`

## 业务代码

- snsapi_userinfo 手动授权

- snsapi_base 静默授权

