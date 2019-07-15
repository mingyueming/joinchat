---
description: '开发人员可以调用Joinchat发送消息api, 向messenger用户发送消息, 是客户自助解决问题的高效方案.'
---

# 消息推送

## 接口安全性验证

JoinChat会为每个机器人生成独一无二的私钥。在调取Joinchat发送API时，开发人员需要用此密钥对整个请求参数提进行SHA256、base64加密，并将签名加在请求Header的X-Joinchat-Signature字段里。JoinChat会根据请求体和签名验证接口安全性,  注意不要泄露自己的私钥。开发者可以随时在后台`设置--API管理页面`更改自己的私钥

下面是调用JoinChat发送API向用户发送消息的简单示例 ：

```php
<?php
// API管理中秘钥
const API_SECRET  = 'xxxxx';
// 请求参数
$params = '{"page_id":12345,"recipient":{"id":"123456"},"params":{"text":"I am test message"}}';
// 将参数和秘钥加密生成签名
$sign = base64_encode(hash_hmac('sha256', $params, API_SECRET, true));
/*httpClient 是伪代码*/
$client = new httpClient();
// 将签名添加到请求头中
$client->setHeaders(['X_JOINCHAT_SIGNATURE' => $sign]);
// 调用JoinChat发送API向messenger用户发送消息
$resp = $client->post('https://joinchat.ai/api/send/messenger', $params);
```



