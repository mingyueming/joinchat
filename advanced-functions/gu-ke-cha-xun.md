---
description: 可提供商户订单&包裹状态的查询功能，是客户自助解决问题的高效方案。
---

# 顾客查询

## 介绍

### 顾客查询：订单 & 包裹状态



使用JoinChat电商机器人的商户，可以实现自己的订单和物流查询接口，并和JoinChat的电商模板接口集成，随后即可让用户以便捷的方式查询自己的订单和物流信息。 正确集成API后用户将可以以如下方式查询订单和物流:

* 电商机器人提供的入口，如欢迎消息和菜单里的My Orders, JoinChat将引导用户输入自己的信息来进行集成
* 电商机器人内置的识别引擎可以识别用户查询订单的意图，并从用户的话语中分析出邮箱作为账号来进行查询

## 接口详述

### 接口加密签名介绍

JoinChat会为每一个独立站商家生成独一无二的私钥。在用户进行查询时，JoinChat将用此密钥对整个请求的Body进行SHA256、base64加密，并将签名加在请求Header的X-Joinchat-Signature字段里。商户需要验证此字段来证明此请求确实来自JoinChat，并注意不要泄露自己的私钥。 商户可以随时在后台更改自己的私钥。 下面是一个使用PHP去验证webhook的示例：

```php
<?php
define('MY_SHOP_SECRET', 'my_shared_secret');

function verify_webhook($data, $hmac_header)
{
  $calculated_hmac = base64_encode(hash_hmac('sha256', $data, SHOPIFY_APP_SECRET, true));
  return hash_equals($hmac_header, $calculated_hmac);
}


$hmac_header = $_SERVER['HTTP_X_JOINCHAT_SIGNATURE'];
$data = file_get_contents('php://input');
$verified = verify_webhook($data, $hmac_header);
error_log('Webhook verified: '.var_export($verified, true)); //chec
```

### 查询接口返回值介绍（这是商家返回的提示）

#### 成功

```php
{
    "success": true,
    "orders":[{}],  //数据为空返回空数组,根据不同请求返回不同的数据,如请求订单，返回orders。请求商品，返回products。
}
```

失败

```php
{
   "success": false,
   "errors":{
       'msg' => '',   //错误描述
   } 

```

### 接口有效性验证

JoinChat在电商管理webhook配置页提供了测试接口，验证的功能，以便商户了解JoinChat的请求结构，签名算法等。以验证webhook的有效性。

#### 请求

```php
{
	"method": "test",   //方法名
  	"params": {
        "token":"T45RE"
        }
}
```

JoinChat会在params字段中放置随机生成的token字段。商户需要读取该字段并返回。如果商户按照正确格式返回，则该webhook验证通过。

## 获取订单

### 接口

```php
{
    "method":"orders",
    "params":{
        "order_number":"12345678902",        //订单编号
        "filter":"unpaid"
        "user_account":"bob.norman@hostmail.com"  //也可以是手机号
        //user_account与order_number只能发送一个
        "order_type":"created_at,desc"
    },
    "pagination":{
        "page":1,
        "limit":3
    }
}
```



根据用户账户，或者订单号获取订单列表。

* 如果跟据用户账户获取，params中是user\_amount代表用户账号，filter则代表要取什么类型的订单
* * open 代表所有正在进行中的订单
  * unpaid 代表所有未支付的订单
  * closed 代表所有已完成的订单
* 如果根据订单号获取，params中是order\_number，代表订单号.
* user\_account和order\_number只能指定一个
* order\_type是排序规则，以逗号分隔，逗号前世排序的字段，逗号后是排序规则，可以排序的字段有created\_at,updated\_at,processed\_at
* pagination是用于分页。在用户以账号获取订单列表时给出。page表示页数，limit表示一页的订单数

### 成功返回

```php

{
  "success": true,
  "has_next_page" : true,
  "orders" : [
    {
      "recipient_name":"Bob Norman",          //收件人名称
      "order_number":"12345678902",           //订单号
      "currency":"USD",      //当前的货币币种
      "payment_method":"",   //支付方式       
      "order_url":"https://shop.com?order_id=12345678902",  //订单状态地址
      "timestamp":"1428444852",  //订单时间戳     
      "status": "unpaid",        //订单状态
      "address":{            //订单收货地址
        "street_1":"Chestnut Street 92",
        "street_2":"",
        "city":"Louisville", 
        "postal_code":"40202",   //邮政编码
        "state":"KY",    //省、州的字母缩写
        "country":"US"   //国家的字母缩写
      },
      "summary":{     
        "subtotal":75.00,     //订单总花费
        "shipping_cost":4.95, //物流总花费
        "total_tax":6.19,     //税收总花费
        "total_cost":56.14    //商品总价
      },
      "adjustments":[  
        {
          "name":"New Customer Discount",
          "amount":20
        },
        {
          "name":"$10 Off Coupon",
          "amount":10
        }
      ],
      "elements":[    //商品详情
        {
          "title":"Classic White T-Shirt",    //商品详情名称
          "subtitle":"100% Soft and Luxurious Cotton",  //商品额外说明
          "quantity":2,   //商品数量
          "price":50,     //商品单价
          "currency":"USD",  //商品价格货币
          "image_url":"http://petersapparel.parseapp.com/img/whiteshirt.png"  //商品详情url
        }
      ]
    },  
    {
      "recipient_name":"Stephane Crozatier",
      "order_number":"12345678902",
      "currency":"USD",
      "payment_method":"Visa 2345",        
      "order_url":"http://petersapparel.parseapp.com/order?order_id=123456",
      "timestamp":"1428444852",       
      "status": "unpaid",
      "address":{
        "street_1":"1 Hacker Way",
        "street_2":"",
        "city":"Menlo Park",
        "postal_code":"94025",
        "state":"CA",
        "country":"US"
      },
      "summary":{
        "subtotal":75.00,
        "shipping_cost":4.95,
        "total_tax":6.19,
        "total_cost":56.14
      },
      "adjustments":[],
      "elements":[
        {
          "title":"Classic White T-Shirt",
          "subtitle":"100% Soft and Luxurious Cotton",
          "quantity":2,
          "price":50,
          "currency":"USD",
          "image_url":"http://petersapparel.parseapp.com/img/whiteshirt.png"
        },
        {
          "title":"Classic Gray T-Shirt",
          "subtitle":"100% Soft and Luxurious Cotton",
          "quantity":1,
          "price":25,
          "currency":"USD",
          "image_url":"http://petersapparel.parseapp.com/img/grayshirt.png"
        }
      ]
    }  
  ]
}
```

### 失败返回

```php
{
    "success": false,
    "errors": {
        "message": "order do not find".
    }
}
```

## 获取物流

### 接口

```php
{
    "method":"packages",
    "params":{
        "order_number":"12345",
		"package_number":"3354543",   //order_number和package_numbe只会传一个
    }   
}
```



当用户想查询一个订单的所有包裹，或者是查看某个特定的包裹的送达情况时，JoinChat将向商户的webhook发出此接口进行查询。 params字段为用户以什么来查询的物流情况，可选值如下：

* order\_number: 查询该订单指定的所有包裹
* package\_number: 查询该包裹号对应的所有包裹
* 此两者不兼容，只能指定一个。

### 成功返回

```php
{
    "success":true,
    "has_next_page":true,
    "packages":[
        {
            "order_number":12345,   //订单号
            "package_number":"A1231411",  //包裹号
            "product_name":"Classic White Shirt", //商品名称
            "carrier":"UPS",  //物流公司名称
            "tracking_number":"UPS11012",  //物流号
            "tracking_url":"http://www.ups.com?id=aaaaaa",  //物流详情链接
            "package_status":"Delivery in progress", //包裹状态
            "exp_delivery":"2017-12-12",   //包裹上次状态的时间
            "image_url":"http://image.com/image1"  //当前订单的商品缩略图
        },
        {
            "order_number":12345,
            "package_number":"A12345zzz",
            "product_name":"Classic White Shirt 2",
            "carrier":"USPS",
            "tracking_number":"USPS12345",
            "tracking_url":"http://www.ups.com?id=aaaaaa",
            "package_status":"Delivery in progress",
            "exp_delivery":"2018-01-11",
            "image_url":"http://image.com/image1"
        }
    ]
}
```

### 失败返回

```php
{
"success": false,
    "errors": {
        "message": "Order not found".
    }
}
```

## 获取商品

### 接口

```php
{
    "method":"products",
    "params":{
        "title":"T_shirt",
        "product_type":"clothes",
        "vendor":"JACK & JONES",
        "order_type":"title,desc"
    },
    "pagination":{
        "page":1,
        "limit":3
    }
}
```



当用户想查询商品时，JoinChat将向商户的webhook发出此接口进行查询。 params字段为用户以什么来查询商品情况，可选值如下：

* title: 模糊查询该商品的名称
* product\_type: 根据商品分类来查询
* vendor: 根据商品品牌来查询
* 三者之间是并关系，至少有一项即可
* order\_type是排序规则，以逗号分隔，前面是要排序的字段，后面是升序或者降序。可以排序的值有title,created\_at,updated\_at,排序只有asc,desc两种

### 成功返回

```php
{
    "success":true,
    "has_next_page":true,
    "products":[
        {
            "product_number":"商品id",
            "title":"商品name",
            "image_url":"商品pic",
            "product_type":"商品类型",
            "tags":"商品标签",
            "description":"商品描述",
            "updated_at":"商品更新时间",
            "created_at":"商品创建时间",
            "detail_link":"商品详情连接",
            "buy_link":"商品购买链接",
            "stock":"商品库存",
            "price":"商品price",
            "compare_at_price":"商品原价",
            "currency_code":"类型，美元还是人名币",
            "discount":""
        }
    ]
}
```

### 失败返回

```php
{
"success": false,
    "errors": {
        "message": "Unknow errors".
    }
}
```

