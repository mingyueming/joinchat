---
description: '开发人员可以调用Joinchat发送消息api, 向messenger用户发送消息, 是客户自助解决问题的高效方案.'
---

# 消息推送

## 请求URL

```http
https://joinchat.ai/api/send_message
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

// 伪代码, 将签名添加到请求头中
$client->setHeaders(['x_joinchat_signature' => $sign]);

// 调用JoinChat发送API向messenger用户发送消息
$resp = $client->post('https://joinchat.ai/api/send_message', $params);
```

## 请求参数解释

### 参数示例

```php
{
    "page_id":12345,
    "recipient":{
        "uuid":"123456"
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
* recipient: 发送者对象, 见发送者对象
* message: 消息对象
  * template\_type: 消息类型 
    * text: 文本消息
    * button: 菜单模板
    * generic: 画册模板
    * list: 列表模板
    * receipt: 回执模板
  * 其他字段: 字符串或者对象, 依赖于template\_type, 详情见消息类型文档
* tag : 消息标签, 添加后消息不受24小时时间窗限制, 详情见消息标签模块
* delay: 消息延时, 单位s, 连续发送消息时, 先后顺序不好控制, 可加入适当延时, 最大10s

### 发送者对象

#### id

消息接收人的facebook\_id, 可在用户管理中查看

#### phone

消息接收人的phone, 可在用户管理中查看, 需用户绑定phone

#### email

消息接收人的email, 可在用户管理中查看, 需用户绑定email

#### uuid 

见uuid介绍

### **使用uuid发送消息\(最为灵活\)**

#### 介绍

> uuid是JoinChat为开发者提供了一种非常灵活的消息发送方式,  开发者可以将自己网站的用户和uuid做关联, 当开发者想向自己网站用户发送 messenger 消息, 既可通过uuid请求JoinChat发送API, 从而将消息送达到facebook用户, 使用场景如下:

* 用户注册后, 给用户messenger推送积分信息, 并且发送优惠券
* 用户购物车弃购后, 给用户messenger推送召回消息, 提高转化率
* 用户下单后, 给用户推送订单回执

#### 配置方法

如何将自己网站的用户和uuid做关联呢? 三步即可

* 登录JoinChat后台, 点击推广插件, 创建Send to Messenger插件或者优惠券插件;

![](../.gitbook/assets/image%20%2899%29.png)

* 创建推广插件后, 点击SDK管理, 选择刚创建的插件, 启用后, 然后复制链接插码到自己的网站

![](../.gitbook/assets/image%20%28154%29.png)

* 开发者打开自己网站, 即可看到send to messenger插件

![](../.gitbook/assets/image%20%2850%29.png)

* 当用户点击`Send to Messenger`插件, JoinChat会执行开发者设置的Javascript回调函数, 在回调函数中开发者可以将uuid和自己网站的用户id关联起来, 回调函数设置方式如下

```javascript
<script type="text/javascript">
    /**
     * 回调函数必须命名为joinchatSendToMessengerCallback
     * 用户可以在回调函数中执行任何js原生代码, 包括发送Ajax请求
     * Joinchat会将uuid传给回调函数
     * 开发者可以向后端发送ajax请求, 将uuid和用户id做关联
     * @param uuid JoinChat为每个用户生成的唯一标识, 存到Cookie中
     */
    let joinchatSendToMessengerCallback = function (uuid) {
        console.log('hello Joinchat, uuid is: ' + uuid);
        let ajax = new XMLHttpRequest();
        // 向开发者后端发送ajax请求, 将uuid和用户id做关联
        ajax.open('get','/save-uuid?uuid='+uuid);
        ajax.send();
        ajax.onreadystatechange = function () {
            if (ajax.readyState==4 &&ajax.status==200) {
                console.log(ajax.responseText);
            }
        }
    }
</script>
<script src="https://joinchat.ai/js/analytics/sdk/15150c9d53ccc1eb21ffef1af5b6b184.js"></script>
```

* 当开发者想向自己网站用户的`facebook messenger`推送消息时, 即可查找其uuid, 调用JoinChat发送API, 从而将消息推送到用户的messenger中

## 消息类型

joinchat支持用户发送文本消息, 菜单消息, 画册消息, 列表消息和回执消息, 

 其中文本消息为普通消息, 其他消息都被模板消息.

模板消息可以增加按钮, 首先先介绍支持的按钮类型, 然后介绍支持的各种消息类型

开发者想了解messenger消息类型, 可参阅facebook messenger 官方文档

{% embed url="https://developers.facebook.com/docs/messenger-platform/send-messages" %}

## 按钮类型

### 网址按钮

#### 格式

```javascript
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

```javascript
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

```javascript
{
    "block_id" : "123",
    "flow_id" : "12"
}
```

* title: 按钮标题。请勿超过 20 个字符。

## 文本消息

### **请求参数示例:**

```javascript
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

## 菜单消息

### 请求参数示例:

```javascript
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

![](../.gitbook/assets/image%20%28100%29.png)

## 画册消息

### 请求参数示例:

```javascript
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

![](../.gitbook/assets/image%20%28102%29.png)

## 列表消息

### 请求参数示例:

```javascript
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

![](../.gitbook/assets/image%20%28138%29.png)

## 消息标签

> 借助消息标签，您可以在需要持续发送通知或最新消息的一些有限情况下，不受 24 小时时间窗的限制向用户发送消息。这样一来，您的智能助手就可以更灵活地与用户互动，您也可以在 Messenger 平台为用户打造更丰富的体验。
>
> **允许的用途 — 无推广内容**
>
> 请注意，消息标签仅用于发送非推广内容。
>
> 使用消息标签发送推广内容（例如，每日特惠、优惠券、折扣或促销公告）是违反 Messenger 平台政策的行为。

{% embed url="https://developers.facebook.com/docs/messenger-platform/send-messages/message-tags" %}



<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x6807;&#x7B7E;</th>
      <th style="text-align:left">&#x5141;&#x8BB8;&#x7684;&#x7528;&#x9014;</th>
      <th style="text-align:left">&#x793A;&#x4F8B;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>BUSINESS_PRODUCTIVITY</code>
      </td>
      <td style="text-align:left">&#x53D1;&#x9001;&#x975E;&#x63A8;&#x5E7F;&#x6D88;&#x606F;&#x6765;&#x5E2E;&#x52A9;&#x7528;&#x6237;&#x7BA1;&#x7406;&#x4E1A;&#x52A1;&#x6216;&#x76F8;&#x5173;&#x6D3B;&#x52A8;&#x7684;&#x6548;&#x7387;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x5173;&#x4E8E;&#x516C;&#x53F8;&#x5DF2;&#x4ECE;&#x670D;&#x52A1;&#x63D0;&#x4F9B;&#x5546;&#x5904;&#x8BA2;&#x9605;&#x6216;&#x8D2D;&#x4E70;&#x7684;&#x4EA7;&#x54C1;&#x6216;&#x670D;&#x52A1;&#x7684;&#x901A;&#x77E5;</li>
          <li>&#x9488;&#x5BF9;&#x5373;&#x5C06;&#x652F;&#x4ED8;&#x7684;&#x7ED3;&#x7B97;&#x5355;&#x6216;&#x5373;&#x5C06;&#x5F00;&#x59CB;&#x7684;&#x670D;&#x52A1;&#x7EF4;&#x62A4;&#x53D1;&#x51FA;&#x7684;&#x63D0;&#x9192;&#x6216;&#x901A;&#x77E5;</li>
          <li>&#x6709;&#x5173;&#x516C;&#x53F8;&#x8868;&#x73B0;&#x3001;&#x6307;&#x6807;&#x6216;&#x63A8;&#x8350;&#x64CD;&#x4F5C;&#x7684;&#x62A5;&#x544A;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>COMMUNITY_ALERT</code>
      </td>
      <td style="text-align:left">&#x5411;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#x901A;&#x77E5;&#x7D27;&#x6025;&#x4E8B;&#x4EF6;&#x6216;&#x516C;&#x7528;&#x8BBE;&#x65BD;&#x95EE;&#x9898;&#xFF0C;&#x6216;&#x5728;&#x60A8;&#x7684;&#x793E;&#x7FA4;&#x5185;&#x8BE2;&#x95EE;&#x5E73;&#x5B89;&#x72B6;&#x51B5;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x8BE2;&#x95EE;&#x5E73;&#x5B89;&#x72B6;&#x51B5;</li>
          <li>&#x901A;&#x77E5;&#x7D27;&#x6025;&#x4E8B;&#x4EF6;&#x6216;&#x516C;&#x7528;&#x8BBE;&#x65BD;&#x95EE;&#x9898;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>CONFIRMED_EVENT_REMINDER</code>
      </td>
      <td style="text-align:left">&#x5411;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#x53D1;&#x9001;&#x53C2;&#x52A0;&#x6392;&#x671F;&#x6D3B;&#x52A8;&#x7684;&#x63D0;&#x9192;&#x6D88;&#x606F;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x7528;&#x6237;&#x6CE8;&#x518C;&#x53C2;&#x52A0;&#x7684;&#x8FD1;&#x671F;&#x8BFE;&#x7A0B;&#x6216;&#x6D3B;&#x52A8;</li>
          <li>&#x53C2;&#x52A0;&#x5DF2;&#x63A5;&#x53D7;&#x9080;&#x8BF7;&#x7684;&#x6D3B;&#x52A8;&#x6216;&#x9884;&#x7EA6;&#x670D;&#x52A1;&#x7684;&#x786E;&#x8BA4;&#x6D88;&#x606F;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>NON_PROMOTIONAL_SUBSCRIPTION</code>
      </td>
      <td style="text-align:left">&#x53D1;&#x9001; Messenger &#x5E73;&#x53F0;&#x8BA2;&#x9605;&#x6D88;&#x606F;&#x653F;&#x7B56;&#x4E2D;&#x89C4;&#x5B9A;&#x7684;&#x51E0;&#x4E2A;&#x7C7B;&#x522B;&#x7684;&#x975E;&#x63A8;&#x5E7F;&#x6D88;&#x606F;&#xFF1A;&#x65B0;&#x95FB;&#x8D44;&#x8BAF;&#x3001;&#x5DE5;&#x4F5C;&#x6548;&#x7387;&#x548C;&#x4E2A;&#x4EBA;&#x8FFD;&#x8E2A;&#x3002;&#x60A8;&#x53EF;&#x5728;&#x4E3B;&#x9875;&#x7684;&#x201C;&#x8BBE;&#x7F6E;&#x201D;&gt;&#x201C;Messenger
        &#x5E73;&#x53F0;&#x201D;&#x7248;&#x5757;&#x7533;&#x8BF7;&#x6B64;&#x6807;&#x7B7E;&#x7684;&#x4F7F;&#x7528;&#x6743;&#x9650;&#x3002;</td>
      <td
      style="text-align:left">&#x67E5;&#x770B;<a href="https://developers.facebook.com/docs/messenger-platform/policy/policy-overview#subscription_messaging">&#x5F00;&#x653E;&#x5E73;&#x53F0;&#x653F;&#x7B56;&#x6982;&#x89C8;&#x7684;&#x201C;&#x8BA2;&#x9605;&#x6D88;&#x606F;&#x529F;&#x80FD;&#x201D;&#x90E8;&#x5206;</a>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>PAIRING_UPDATE</code>
      </td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#xFF0C;&#x5DF2;&#x6839;&#x636E;&#x4E4B;&#x524D;&#x7684;&#x8BF7;&#x6C42;&#x627E;&#x5230;&#x5339;&#x914D;&#x9879;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x7EA6;&#x4F1A;&#x5E94;&#x7528;&#x4E2D;&#x627E;&#x5230;&#x4E86;&#x5339;&#x914D;&#x7684;&#x5BF9;&#x8C61;</li>
          <li>&#x505C;&#x8F66;&#x4F4D;&#x53EF;&#x7528;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>APPLICATION_UPDATE</code>
      </td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#x5176;&#x7533;&#x8BF7;&#x72B6;&#x6001;&#x7684;&#x66F4;&#x65B0;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x7533;&#x8BF7;&#x6B63;&#x5728;&#x63A5;&#x53D7;&#x5BA1;&#x6838;</li>
          <li>&#x7533;&#x8BF7;&#x5DF2;&#x83B7;&#x5F97;&#x6279;&#x51C6;</li>
          <li>&#x6C42;&#x804C;&#x7533;&#x8BF7;&#x72B6;&#x6001;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>ACCOUNT_UPDATE</code>
      </td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#x5176;&#x5E10;&#x6237;&#x8BBE;&#x7F6E;&#x7684;&#x66F4;&#x6539;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x4E2A;&#x4EBA;&#x4E3B;&#x9875;&#x5DF2;&#x66F4;&#x6539;</li>
          <li>&#x9996;&#x9009;&#x9879;&#x5DF2;&#x66F4;&#x65B0;</li>
          <li>&#x8BBE;&#x7F6E;&#x5DF2;&#x66F4;&#x6539;</li>
          <li>&#x4F1A;&#x5458;&#x8EAB;&#x4EFD;&#x5DF2;&#x8FC7;&#x671F;</li>
          <li>&#x5BC6;&#x7801;&#x5DF2;&#x66F4;&#x6539;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>PAYMENT_UPDATE</code>
      </td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#x73B0;&#x6709;&#x4EA4;&#x6613;&#x7684;&#x652F;&#x4ED8;&#x72B6;&#x6001;&#x66F4;&#x65B0;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x53D1;&#x9001;&#x6536;&#x636E;</li>
          <li>&#x53D1;&#x9001;&#x7F3A;&#x8D27;&#x901A;&#x77E5;</li>
          <li>&#x901A;&#x77E5;&#x62CD;&#x5356;&#x5DF2;&#x7ED3;&#x675F;</li>
          <li>&#x652F;&#x4ED8;&#x4EA4;&#x6613;&#x7684;&#x72B6;&#x6001;&#x5DF2;&#x66F4;&#x6539;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>PERSONAL_FINANCE_UPDATE</code>
      </td>
      <td style="text-align:left">&#x786E;&#x8BA4;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#x7684;&#x8D22;&#x52A1;&#x6D3B;&#x52A8;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x8D26;&#x5355;&#x652F;&#x4ED8;&#x63D0;&#x9192;</li>
          <li>&#x6392;&#x671F;&#x652F;&#x4ED8;&#x63D0;&#x9192;</li>
          <li>&#x652F;&#x4ED8;&#x6536;&#x636E;&#x901A;&#x77E5;</li>
          <li>&#x8D44;&#x91D1;&#x8F6C;&#x8D26;&#x786E;&#x8BA4;&#x6216;&#x66F4;&#x65B0;</li>
          <li>&#x5176;&#x4ED6;&#x91D1;&#x878D;&#x670D;&#x52A1;&#x7C7B;&#x4EA4;&#x6613;&#x6D3B;&#x52A8;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>SHIPPING_UPDATE</code>
      </td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#x67D0;&#x4EF6;&#x5DF2;&#x8D2D;&#x4E70;&#x5546;&#x54C1;&#x7684;&#x914D;&#x9001;&#x72B6;&#x6001;&#x66F4;&#x6539;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x5546;&#x54C1;&#x5DF2;&#x53D1;&#x8D27;</li>
          <li>&#x72B6;&#x6001;&#x66F4;&#x6539;&#x4E3A;&#x5728;&#x9014;</li>
          <li>&#x5546;&#x54C1;&#x5DF2;&#x9001;&#x8FBE;</li>
          <li>&#x914D;&#x9001;&#x5EF6;&#x8FDF;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>RESERVATION_UPDATE</code>
      </td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#x73B0;&#x6709;&#x9884;&#x8BA2;&#x7684;&#x66F4;&#x65B0;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x884C;&#x7A0B;&#x66F4;&#x6539;</li>
          <li>&#x5730;&#x70B9;&#x66F4;&#x6539;</li>
          <li>&#x53D6;&#x6D88;&#x5DF2;&#x786E;&#x8BA4;</li>
          <li>&#x9152;&#x5E97;&#x9884;&#x8BA2;&#x5DF2;&#x53D6;&#x6D88;</li>
          <li>&#x79DF;&#x8F66;&#x63A5;&#x4EBA;&#x65F6;&#x95F4;&#x66F4;&#x6539;</li>
          <li>&#x623F;&#x578B;&#x5347;&#x7EA7;&#x5DF2;&#x786E;&#x8BA4;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>ISSUE_RESOLUTION</code>
      </td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#x5728; Messenger
        &#x5BF9;&#x8BDD;&#x4E2D;&#x53D1;&#x8D77;&#x7684;&#x5BA2;&#x6237;&#x670D;&#x52A1;&#x95EE;&#x9898;&#x6709;&#x4F55;&#x66F4;&#x65B0;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x95EE;&#x9898;&#x5DF2;&#x89E3;&#x51B3;</li>
          <li>&#x95EE;&#x9898;&#x72B6;&#x6001;&#x5DF2;&#x66F4;&#x65B0;</li>
          <li>&#x8BE5;&#x95EE;&#x9898;&#x9700;&#x8981;&#x63D0;&#x4F9B;&#x5176;&#x4ED6;&#x4FE1;&#x606F;</li>
          <li>&#x8DDF;&#x8FDB;&#x5BA2;&#x6237;&#x67E5;&#x8BE2;&#x6216;&#x652F;&#x6301;&#x5DE5;&#x5355;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>APPOINTMENT_UPDATE</code>
      </td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#x73B0;&#x6709;&#x9884;&#x7EA6;&#x7684;&#x66F4;&#x6539;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x9884;&#x7EA6;&#x65F6;&#x95F4;&#x66F4;&#x6539;</li>
          <li>&#x9884;&#x7EA6;&#x5730;&#x70B9;&#x66F4;&#x6539;</li>
          <li>&#x9884;&#x7EA6;&#x5DF2;&#x53D6;&#x6D88;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>GAME_EVENT</code>
      </td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#xFF0C;&#x6E38;&#x620F;&#x5185;&#x7528;&#x6237;&#x8FDB;&#x5EA6;&#x3001;&#x5168;&#x5C40;&#x6D3B;&#x52A8;&#x6216;&#x76F4;&#x64AD;&#x4F53;&#x80B2;&#x8D5B;&#x4E8B;&#x7684;&#x66F4;&#x6539;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x73A9;&#x5BB6;&#x7684;&#x6E38;&#x620F;&#x5185;&#x4F5C;&#x7269;&#x5DF2;&#x53EF;&#x6536;&#x5272;</li>
          <li>&#x73A9;&#x5BB6;&#x7684;&#x6BCF;&#x65E5;&#x6BD4;&#x8D5B;&#x5373;&#x5C06;&#x5F00;&#x59CB;</li>
          <li>&#x7528;&#x6237;&#x6700;&#x559C;&#x6B22;&#x7684;&#x8DB3;&#x7403;&#x961F;&#x5373;&#x5C06;&#x5F00;&#x8E22;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>TRANSPORTATION_UPDATE</code>
      </td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#x73B0;&#x6709;&#x4EA4;&#x901A;&#x8FD0;&#x8F93;&#x670D;&#x52A1;&#x9884;&#x8BA2;&#x7684;&#x66F4;&#x65B0;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x822A;&#x73ED;&#x72B6;&#x6001;&#x66F4;&#x6539;</li>
          <li>&#x4FBF;&#x8F66;&#x5DF2;&#x53D6;&#x6D88;</li>
          <li>&#x884C;&#x7A0B;&#x5DF2;&#x5F00;&#x59CB;</li>
          <li>&#x6E21;&#x8F6E;&#x5DF2;&#x62B5;&#x8FBE;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>FEATURE_FUNCTIONALITY_UPDATE</code>
      </td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#xFF0C;&#x667A;&#x80FD;&#x52A9;&#x624B;&#x63A8;&#x51FA;&#x4E86;&#x65B0;&#x7279;&#x6027;&#x6216;&#x65B0;&#x529F;&#x80FD;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x667A;&#x80FD;&#x52A9;&#x624B;&#x4E2D;&#x6DFB;&#x52A0;&#x4E86;&#x4E0E;&#x5728;&#x7EBF;&#x5BA2;&#x670D;&#x804A;&#x5929;&#x7684;&#x529F;&#x80FD;</li>
          <li>&#x667A;&#x80FD;&#x52A9;&#x624B;&#x6DFB;&#x52A0;&#x4E86;&#x65B0;&#x6280;&#x80FD;</li>
        </ul>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>TICKET_UPDATE</code>
      </td>
      <td style="text-align:left">&#x5411;&#x6D88;&#x606F;&#x63A5;&#x6536;&#x4EBA;&#x53D1;&#x9001;&#x5DF2;&#x8D2D;&#x7968;&#x6D3B;&#x52A8;&#x7684;&#x66F4;&#x65B0;&#x6216;&#x63D0;&#x9192;&#x3002;</td>
      <td
      style="text-align:left">
        <ul>
          <li>&#x97F3;&#x4E50;&#x4F1A;&#x5F00;&#x59CB;&#x65F6;&#x95F4;&#x66F4;&#x6539;</li>
          <li>&#x6D3B;&#x52A8;&#x5730;&#x70B9;&#x66F4;&#x6539;</li>
          <li>&#x6F14;&#x51FA;&#x5DF2;&#x53D6;&#x6D88;</li>
          <li>&#x53EF;&#x4EE5;&#x7533;&#x8BF7;&#x9000;&#x6B3E;</li>
        </ul>
        </td>
    </tr>
  </tbody>
</table>