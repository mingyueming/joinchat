---
description: 目前，Joinchat支持商家店铺系统集成，分别是Shopify店铺商家和独立购物网站两种商家类型。
---

# 店铺关联

## Shopify商家

由于Shopify平台支持第三方API对接，因此Joinchat针对Shopify平台开发的相对应的接口与之对接，只需前往Shopify的APP管理平台完成Joinchat APP的绑定，即可轻松启用我们为您量身定制的功能，具体绑定流程如下：

首先进入店铺关联，点击「shopify商家」

![](../.gitbook/assets/image%20%2827%29.png)

输入您在Shopify的店铺URL，点击「确认」，就会跳转至JoinChat app安装页面

![&#x8F93;&#x5165;&#x5E97;&#x94FA;URL](../.gitbook/assets/image%20%2843%29.png)

成功登陆店铺并安装好后，我们会提示您已经成功完成Joinchat app的安装。

![&#x9009;&#x62E9;&#x529F;&#x80FD;](../.gitbook/assets/image%20%2842%29.png)

在这里，joinchat目前有两种功能供Shopify商家使用，「物流更新」、「下单未付款」和「订单回执」，当您启用该功能后，用户在shopify上完成购物后，将自动通过JoinChat给Messenger用户发送她所购买商品的订单回执信息。当有物流状态进行变更时，将自动通过joinchat给messenger用户发送物流最新状态。在推送物流更新和订单回执这两种固定模板时，JoinChat还支持在消息模板后面选择一条已有的消息，一同发送给用户，方便运营人员精细化运用。这两项功能将极大提升用户在您网站中的售后体验。

若用户更新店铺时，则可以点击「解除绑定」，就能重新绑定新店铺。目前JoinChat一个机器人仅支持绑定一家店铺。

## 自有站商家

webhook管理页面说明

| 字段 | 释义 |
| :--- | :--- |
| Page ID | 显示当前所登陆Fb账号Page ID 秘钥 |
| 秘钥 | JoinChat为每一个商家生成的唯一标识 |
| IP地址 | 该地址为joinChat的IP地址，，为了保证商家数据传输的安全性，建议商家将IP地址加入您的访问白名单中。  |
| 配置URL | JoinChat访问商家的统一入口URL |
| Token | 字符串，用于验证URL访问 |
| 请求 | joinchat会为您自动生成请求示例 |
| 返回结果 | 判断验证是否通过 |



