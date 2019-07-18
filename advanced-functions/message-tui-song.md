---
description: '开发人员可以调用Joinchat发送消息api, 向messenger用户发送消息, 是客户自助解决问题的高效方案.'
---

# 消息推送

## 请求URL

```php
https://joinchat.ai/api/send/message
```

## 请求示例和接口安全性验证

JoinChat会为每个机器人生成独一无二的私钥。在调取Joinchat发送API时，开发人员需要用此密钥对整个请求参数提进行sha256、base64加密，并将签名加在请求Header头的`x_joinchat_signature`字段里。JoinChat会根据请求体和签名验证接口安全性,  注意不要泄露自己的私钥。开发者可以随时在后台`设置--API管理页面`更改自己的私钥

下面是调用JoinChat发送API向用户发送消息的简单示例 ：

```php
<?php
// API管理中秘钥
const API_SECRET  = 'xxxxx';

// 请求参数
$params = '{"page_id":12345,"recipient":{"id":"123456"},"message":{"template_type":"text","text":"Hello World"},"tag":"ACCOUNT_UPDATE","delay":1}';

// 将参数和秘钥加密生成签名
$sign = base64_encode(hash_hmac('sha256', $params, API_SECRET, true));

/*httpClient 是伪代码*/
$client = new httpClient();

// 将签名添加到请求头中
$client->setHeaders(['x_joinchat_signature' => $sign]);

// 调用JoinChat发送API向messenger用户发送消息
$resp = $client->post('https://joinchat.ai/api/send/messenger', $params);
```

## 请求参数

### 参数示例

```text
{
    "page_id":12345,
    "recipient":{
        "id":"123456"
    },
    "message":{
        "template_type":"text",
		"text":"Hello World"
    },
	"tag":"ACCOUNT_UPDATE",
	"delay": 1
}
```

### 参数含义

* page\_id: bot绑定的facebook主页id, 可在`设置-API`中查看
* recipient: 发送者对象
  * recipient.id: 消息接收人的id, 可在用户管理中查看
  * recipient.phone: 消息接收人的phone, 可在用户管理中查看, 需用户绑定phone
  * recipient.email: 消息接收人的email, 可在用户管理中查看, 需用户绑定email
  * recipient.uuid: **接收消息人的joinchat\_uuid, 需要机器人在`推广工具->sdk管理`中,启用插件, 并将代码插入到网站中, 用户点击插件\(Send to Messenger\), JoinChat即可将用户uuid和facebook\_id做关联, 开发者从cookie中获取uuid, 即可使用其发送消息**
* message: 消息对象
  * template\_type: 消息类型 
    * text: 文本消息
    * button: 菜单模板
    * generic: 画册模板
    * list: 列表模板
    * receipt: 回执模板
  * 其他字段: 字符串或者对象, 依赖于template\_type, 详情见消息类型文档
* tag : 消息标签, 添加后消息不受24小时时间窗限制
* delay: 消息延时, 单位s, 连续发送消息时, 先后顺序不好控制, 可加入适当延时, 最大10s

## 消息类型

joinchat支持用户发送文本消息, 菜单消息, 画册消息, 列表消息和回执消息, 文本消息时普通消息, 其他消息都是模板类消息, payload为对象, 可以添加按钮, 首先先介绍下支持添加的按钮类型.

## 按钮类型

### 网址按钮

#### 格式

```text
{
  "type": "web_url",
  "url": "<URL_TO_OPEN_IN_WEBVIEW>",
  "title": "<BUTTON_TEXT>",
}
```

#### 参数解释

* type: 值必须为web\_url
* url: 点击按钮要打开的网址, 需要在Joinchat管理端将该网址域名添加到白名单中
* title: 按钮标题。请勿超过 20 个字符。

### 回传按钮

#### 格式

```text
{
  "type":"postback",
  "title":"<BUTTON_TEXT>",
  "payload":"<DEVELOPER_DEFINED_PAYLOAD>"
}
```

#### 参数解释

* type: 值必须为postback
* payload: 点击按钮, 发送到Joinchat的数据, 目前支持格式为:
  * block\_id: 点击按钮, 触发的block
  * flow\_id: 点击按钮, 触发的flow

```text
{
    "block_id" : "123",
    "flow_id" : "12"
}
```

* title: 按钮标题。请勿超过 20 个字符。

## 文本消息

### **请求参数示例:**

```text
{
    "page_id":12345,
    "recipient":{
        "id":"123456"
    },
    "message":{
        "template_type":"text",
		"text":"Hello World",
    },
	"tag":"ACCOUNT_UPDATE",
	"delay": 1,
}
```

### **参数解释**

* type: 值必须是button
* template\_type: 值必须为button
* text: UTF-8 编码文本，最多 640 个字符。文本显示在按钮上方
* buttons: 显示为行动号召的一组[按钮](https://developers.facebook.com/docs/messenger-platform/send-api-reference/buttons)，包括 1-3 个按钮, 参见`按钮类型`模块

## 

## 

### 请求参数示例:

```text
{
    "page_id":12345,
    "recipient":{
        "id":"123456"
    },
    "message":{
		"template_type":"button",
		"text":"What do you want to do next?",
		"buttons":[
		  {
			"type":"web_url",
			"url":"https://www.messenger.com",
			"title":"Visit Messenger"
		  },
		  {
			...
		  },
		  {...}
		]
    },
	"tag":"ACCOUNT_UPDATE",
	"delay": 3,
}
```

### 参数解释

* template\_type: 值必须是button
* template\_type: 值必须为button
* text: UTF-8 编码文本，最多 640 个字符。文本显示在按钮上方
* buttons: 数组&lt;button&gt; 显示为行动号召的一组[按钮](https://developers.facebook.com/docs/messenger-platform/send-api-reference/buttons)，包括 1-3 个按钮, 参见`按钮类型`模块

![](../.gitbook/assets/image%20%2897%29.png)

## 画册消息

### 请求参数示例:

```text
{
    "page_id":12345,
    "recipient":{
        "id":"123456"
    },
    "message":{
		"template_type":"generic",
        "elements":[
           {
            "title":"Welcome!",
            "image_url":"https://petersfancybrownhats.com/company_image.png",
            "subtitle":"We have the right hat for everyone.",
            "default_action": {
              "type": "web_url",
              "url": "https://petersfancybrownhats.com/view?item=103",
              "webview_height_ratio": "tall",
            },
            "buttons":[
              {
                "type":"web_url",
                "url":"https://petersfancybrownhats.com",
                "title":"View Website"
              },{
                "type":"postback",
                "title":"Start Chatting",
                "payload":"DEVELOPER_DEFINED_PAYLOAD"
              }              
            ]      
          }
        ]
      }
    },
	"tag":"ACCOUNT_UPDATE",
	"delay": 3,
}
```

### 参数解释

* template\_type: 值必须为generic
* elements:  [`element`](https://developers.facebook.com/docs/messenger-platform/reference/template/generic/?translation#elements) 对象的数组，描述要发送的常规模板的实例。指定多个元素时，会发送可水平滚动的模板轮播。最多支持 10 个元素。
  * title: 字符串 显示在模板中的标题。不超过 80 个字符。
  * subtitle: 字符串  _**可选。**_ 显示在模板中的副标题。不超过 80 个字符。
  * image\_url: 字符串  _**可选。**_ 显示在模板中的图片网址。
  * default\_action: 对象  _**可选。**_ 用户轻触模板时执行的默认操作。接受与[网址按钮](https://developers.facebook.com/docs/messenger-platform/send-api-reference/url-button)相同的属性，`title` 除外。
  * buttons: 数组&lt;button&gt;  _**可选。**_ 要添加到模板中的[按钮](https://developers.facebook.com/docs/messenger-platform/send-api-reference/buttons)数组。每个元素最多支持 3 个按钮。

![img](../.gitbook/assets/image%20%2899%29.png)

## 列表消息

### 请求参数示例:

```text
{
    "page_id":12345,
    "recipient":{
        "id":"123456"
    },
    "message":{
		"template_type": "list",
        "top_element_style": "compact",
        "elements": [
          {
            "title": "Classic T-Shirt Collection",
            "subtitle": "See all our colors",
            "image_url": "https://peterssendreceiveapp.ngrok.io/img/collection.png",          
            "buttons": [
              {
                "title": "View",
                "type": "web_url",
                "url": "https://peterssendreceiveapp.ngrok.io/collection",
                "messenger_extensions": true,
                "webview_height_ratio": "tall",
                "fallback_url": "https://peterssendreceiveapp.ngrok.io/"            
              }
            ]
          },
          {
            "title": "Classic White T-Shirt",
            "subtitle": "See all our colors",
            "default_action": {
              "type": "web_url",
              "url": "https://peterssendreceiveapp.ngrok.io/view?item=100",
              "messenger_extensions": false,
              "webview_height_ratio": "tall"
            }
          },
          {
            "title": "Classic Blue T-Shirt",
            "image_url": "https://peterssendreceiveapp.ngrok.io/img/blue-t-shirt.png",
            "subtitle": "100% Cotton, 200% Comfortable",
            "default_action": {
              "type": "web_url",
              "url": "https://peterssendreceiveapp.ngrok.io/view?item=101",
              "messenger_extensions": true,
              "webview_height_ratio": "tall",
              "fallback_url": "https://peterssendreceiveapp.ngrok.io/"
            },
            "buttons": [
              {
                "title": "Shop Now",
                "type": "web_url",
                "url": "https://peterssendreceiveapp.ngrok.io/shop?item=101",
                "messenger_extensions": true,
                "webview_height_ratio": "tall",
                "fallback_url": "https://peterssendreceiveapp.ngrok.io/"            
              }
            ]        
          }
        ],
         "buttons": [
          {
            "title": "View More",
            "type": "postback",
            "payload": "payload"            
          }
        ]
    },
	"tag":"ACCOUNT_UPDATE",
	"delay": 3,
}
```

### 参数解释

* template\_type: 值必须为list
* top\_element\_style: 字符串 _**可选。**_ 设置第一个列表项的格式。

  `compact`:呈现无格式的列表项。

  `large`:将第一个列表项呈现为封面项。

* buttons: 一组按钮,  _**可选。**_ 显示在列表底部的[按钮](https://developers.facebook.com/docs/messenger-platform/send-api-reference/buttons)。最多支持 1 个按钮
* elements: 一组元素, 描述列表中各项目的一组元素。要求最少 2 个元素。最多支持 4 个元素
  * title: 字符串  显示为列表项标题的字符串。 不超过 80 个字符。如果标题跨越太多行，则可能会被截断, 元素也必须至少设置 `image_url` 或 `subtitle` 之一。
  * subtitle: 字符串 _**可选。**_ 显示为列表项副标题的字符串。不超过 80 个字符。如果副标题跨越太多行，则可能会被截断。

    元素必须至少设置 `image_url` 或 `subtitle` 之一。

  * image\_url: 字符串  _**可选。**_ 要显示在列表项中的图片网址

    元素必须至少设置 `image_url` 或 `subtitle` 之一

  * default\_action _**可选。**_[网址按钮](https://developers.facebook.com/docs/messenger-platform/send-api-reference/url-button#fields)，指定用户轻触列表项时要执行的默认操作
  * buttons: 一组按钮,  _**可选。**_ 要显示在列表项中的[按钮](https://developers.facebook.com/docs/messenger-platform/send-api-reference/buttons)。最多支持 1 个按钮。

![img](../.gitbook/assets/image%20%28134%29.png)



