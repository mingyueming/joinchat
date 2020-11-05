---
description: 开发人员可以调用JoinChat的电商API，实现符合电商场景的垂直功能
---

# 电商API

##  介绍

JoinChat针对电商客户提供了三个功能卡片, 分别是

* 商品查找 
* 商品搜索
* 订单查找 

使用JoinChat电商机器人的商户，可以实现自己的商品和订单查询接口，并和JoinChat的电商模板三个功能卡片集成，随后即可让用户以便捷的方式查询商品和订单信息。 

## 接口安全性验证

JoinChat会为每一个机器人生成独一无二的私钥。JoinChat调取API时，先用此密钥对整个请求参数进行sha256、base64加密，并将签名加在请求Header的`x-joinchat-signature`字段里。商户需要验证此字段来证明此请求确实来自JoinChat，并注意不要泄露自己的私钥。 商户可以随时在后台`设置--API管理页面`更改自己的私钥

开发者可以使用如下代码验证签名：

```php
<?php
// API管理中秘钥
const API_SECRET  = 'xxxxx';

// 伪代码, 从请求头获取签名
$src_sign = $request->header('x-joinchat-signature');

// 伪代码, 获取请求参数, 字符串格式
$input = $request->getContentRaw();

// 对请求参数进行加密生成签名并对比header里的签名
$result_sign = base64_encode(hash_hmac('sha256', $input, API_SECRET, true));
$result = hash_equals($src_sign, $result_sign);

var_dump($result);
```

## 获取商品

当用户想查询商品或者，JoinChat将向商户的webhook发送请求参数进行查询

### 请求参数

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

* title: 根据商品标题进行模糊查询
* product\_type:  根据商品分类来查询
* vendor:  根据商品品牌来查询
* 以上三者之间是 或关系
* order\_type是排序规则，以逗号分隔，前面是要排序的字段，后面是升序或者降序。可以排序的值有title,created\_at,updated\_at,排序只有asc,desc两种

### 成功返回

```php
{
    "success":true,
    "total" : 1,   //搜索结果的数量
    "data":[
        {
            "title":"商品name",
            "image_url":"商品pic",
            "product_type":"商品类型",
            "vendor":"商品品牌",
            "desc":"商品描述", // 可选
            "detail_url":"商品详情连接", // 可选
            "buy_url":"商品购买链接",
            "price":"商品price",
        }
    ]
}
```

### 失败返回

```php
{
    "success": false,
    "errors": "Unknow errors".
}
```

## 获取商品品牌和类别

当商店想给顾客更多查询选择，但是考虑到顾客不知道输入什么，JoinChat将向商户的webhook发送请求参数进行查询，获取商家的商品分类和品牌，展示给客户，让客户有明确的选择。

### 请求参数

```php
{ 
	"method" : "property",
	"params" : { 
		"search_type" : "product_type|vendor", //product_type: 商品类型 vendor: 商品品牌
	} 
}
```

 字段值说明情况如下： 

* product\_type: 获取商品的所有分类 
* vendor: 获取商品的所有品牌

### 返回结果

#### 品牌接口

```php
{ 
	"success": true,
	"data" :[ 
		"Nike","LiNing","xxx"
	]
}
```

#### 分类接口

```php
{ 
	"success": true,
	"data" :[ 
		"clothes","T_shirt","xxx"
	]
}
```

### 失败返回

```php
{
    "success": false,
    "errors": "Unknow errors".
}
```

## 获取订单

* 当用户查询订单时, JoinChat提供给用户两种查询方式, 账户查询和订单号查询
* 查询时JoinChat将向商户的webhook提交请求参数进行查询，获取用户的订单, 然后通过messenger呈现给用户

### 请求参数

```php
{
    "method":"orders",
    "params":{
        "order_number":"12345678902",                //订单号
        "user_account":"bob.norman@hostmail.com",    //邮箱或者手机号
        //user_account与order_number只能发送一个
        "filter":"open" // 当根据用户账户进行查询时, 可以查询未完成和已完成的订单        
    },
    "pagination":{
        "page":1,
        "limit":3
    }
}
```

* 如果跟据用户账户获取，params中是user\_amount代表用户账号，filter则代表要取什么类型的订单
* * open 代表所有正在进行中的订单
  * closed 代表所有已完成的订单
* 如果根据订单号获取，params中是order\_number，代表订单号.
* user\_account和order\_number只能指定一个
* pagination是用于分页。在用户以账号获取订单列表时给出。page表示页数，limit表示一页的订单数

### 成功返回

* 返回数据字段含义和是否必传可以参考facebook文档, JoinChat不做处理, 直接传递给facebook
* facebook messenger 回执模板文档

  [https://developers.facebook.com/docs/messenger-platform/reference/template/receipt/](https://developers.facebook.com/docs/messenger-platform/reference/template/receipt/)

```php
	"success": true,
	"total" : 2,
	"data" : [
	{
		"template_type":"receipt", // 必须为receipt
		"recipient_name":"Stephane Crozatier",
		"order_number":"12345678902",
		"currency":"USD",
		"payment_method":"Visa 2345",        
		"order_url":"http://petersapparel.parseapp.com/order?order_id=123456",
		"timestamp":"1428444852",        
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
	}, 
	{
		"template_type":"receipt", //必须为receipt
		"recipient_name":"Stephane Crozatier",
		"order_number":"12345678902",
		"currency":"USD",
		"payment_method":"Visa 2345",        
		"order_url":"http://petersapparel.parseapp.com/order?order_id=123456",
		"timestamp":"1428444852",    
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
	}]
}
```

### 失败返回

```php
{
    "success": false,
    "errors": "Unknow errors".
}
```

