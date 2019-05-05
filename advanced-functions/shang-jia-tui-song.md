# 商家推送

可提供商户推送订单回执&物流状态更新，是客户自助解决问题的高效方案。

电商机器人的API管理接口，为商家提供了一个有效互动的方式，提高网站的购物体验。 正确集成API管理后，商家将可以以如下方式向主页粉丝主动推送消息:

* 顾客成功下单后，通过 PSID 向其推送一条订单确认回执
* 订单发货/签收时，通过 PSID 向顾客发送物流状态更新提示

## 推送接口说明

X-Joinchat-Signature用于验证webhook。当商家向JoinChat推送数据时，在header头中添加X-Joinchat-Signature，X-Shop-Domain标识，用于JoinChat验证数据来源是否正确

## 订单回执

### 接口

```php
{
  "request": {
    "page_id": "407898491811542",
    "topic": "Order/Receipt"
  },
  "recipient": {
      "id": "<psid>" 
   },
  "params": {
      "recipient_name": "John Doe",  //收件人姓名
      "order_number": "U5555", 
      "currency": "USD",
      "payment_method": "Visa 2345",
      "order_url": "https://storage.googleapis.com/assets.bothub.ai/bothub/templates/classic_white_shirt.jpg",
      "timestamp": 1523619550,
      "address": {
        "street_1": "UNICEF 125 Maiden Lane",
        "street_2": "11th Floor",
        "city": "New York",
        "postal_code": "10038",
        "state": "NY",
        "country": "US"
      },
      "summary": {
        "subtotal": 138.4,
        "shipping_cost": 0,
        "total_tax": 0,
        "total_cost": 138.4
      },
      "adjustments": [],
      "elements": [
        {
          "title": "Classic White Shirts",
          "subtitle": "100% Soft and Luxurious Cotton",
          "quantity": 2,
          "price": 11.99,
          "currency": "USD",
          "image_url": "https://storage.googleapis.com/assets.bothub.ai/bothub/templates/classic_white_shirt.jpg"
        },
        {
          "title": "Floral Belted Romper",
          "subtitle": "Casual romper featuring striped pattern",
          "quantity": 2,
          "price": 13.89,
          "currency": "USD",
          "image_url": "https://storage.googleapis.com/assets.bothub.ai/bothub/templates/floral_belted_romper.jpg"
        },
        {
          "title": "One Shoulder Bikini Set",
          "subtitle": "Low Waisted",
          "quantity": 2,
          "price": 14.66,
          "currency": "USD",
          "image_url": "https://storage.googleapis.com/assets.bothub.ai/bothub/templates/one_shoulder_bikini_set.jpg"
        },
        {
          "title": "Smocked Mini Dress",
          "subtitle": "Elastic waistband",
          "quantity": 2,
          "price": 15.77,
          "currency": "USD",
          "image_url": "https://storage.googleapis.com/assets.bothub.ai/bothub/templates/smocked_mini_dress.jpg"
        },
        {
          "title": "Twist Tied Top",
          "subtitle": "Easily look good to pair it with anything",
          "quantity": 2,
          "price": 12.89,
          "currency": "USD",
          "image_url": "https://storage.googleapis.com/assets.bothub.ai/bothub/templates/twisted_tied_top.jpg"
        }
      ] 
  }
}
```

#### 说明

当商户需要向用户推送订单回执时，由商户填充params数据，JoinChat验证数据格式正确后向用户发送消息，其中recipent字段包含的值有psid，phone，email。例如传递邮箱，可以"recipient":{"email":''"youremail"}。

#### 成功返回

```php
{
    "success": true,
    "recipient_id": "1602407863220595"
}
```

#### 失败返回

```php
{
    "success": false
    "errors": {
        "code": 10010,
        "msg": "Recipient not found",
    }
}
```

### 物流更新

#### 接口

```php
{
  "request": {
    "page_id": "407898491811542",
    "topic": "package/update"
  },
  "recipient": {
      "id": "<psid>" 
   },
  "params": {
    "tracking_number": "zvfVUREPVPUQ1oMl",    // 物流号
    "image_url": "https://unsplash.it/120/120/?random",  //订单商品的缩略图
    "tracking_url": "http://shop.bothub.ai",  //物流详情url
    "recipient_name": "Peter",    //收件人姓名
    "tracking_status": "shipped"  //物流状态
  }
}
```

#### 说明

page\_id是 店铺关联的facebook主页id，topic是来明确推送消息的类别，recipient是用户标识，可以是id，email，phone。params中根据topic中类别来推送具体的信息。

#### 成功返回

```php
{
    "success": true,
    "recipient_id": "1602407863220595"
}
```

#### 失败返回

```php
{
    "success": false,
    "error": {
        "code": 10010,
        "msg": "Recipient not found",
   }
}
```

### 未支付订单

#### 接口：

```php
{
  "request": {
    "page_id": "407898491811542",
    "topic": "Order/Unpaid"
  },
  "recipient": {
      "id": "<psid>"   //也可以是邮箱，电话
   },
  "params": {
      "recipient_name": "John Doe",
      "order_number": "U5555",
      "currency": "USD",
      "payment_method": "",
      "order_url": "https://storage.googleapis.com/assets.bothub.ai/bothub/templates/classic_white_shirt.jpg",
      "timestamp": 1523619550,
      "address": {
        "street_1": "UNICEF 125 Maiden Lane",
        "street_2": "11th Floor",
        "city": "New York",
        "postal_code": "10038",
        "state": "NY",
        "country": "US"
      },
      "summary": {
        "subtotal": 138.4,
        "shipping_cost": 0,
        "total_tax": 0,
        "total_cost": 138.4
      },
      "adjustments": [],
      "elements": [
        {
          "title": "Classic White Shirts",
          "subtitle": "100% Soft and Luxurious Cotton",
          "quantity": 2,
          "price": 11.99,
          "currency": "USD",
          "image_url": "https://storage.googleapis.com/assets.bothub.ai/bothub/templates/classic_white_shirt.jpg"
        },
        {
          "title": "Floral Belted Romper",
          "subtitle": "Casual romper featuring striped pattern",
          "quantity": 2,
          "price": 13.89,
          "currency": "USD",
          "image_url": "https://storage.googleapis.com/assets.bothub.ai/bothub/templates/floral_belted_romper.jpg"
        },
        {
          "title": "One Shoulder Bikini Set",
          "subtitle": "Low Waisted",
          "quantity": 2,
          "price": 14.66,
          "currency": "USD",
          "image_url": "https://storage.googleapis.com/assets.bothub.ai/bothub/templates/one_shoulder_bikini_set.jpg"
        },
        {
          "title": "Smocked Mini Dress",
          "subtitle": "Elastic waistband",
          "quantity": 2,
          "price": 15.77,
          "currency": "USD",
          "image_url": "https://storage.googleapis.com/assets.bothub.ai/bothub/templates/smocked_mini_dress.jpg"
        },
        {
          "title": "Twist Tied Top",
          "subtitle": "Easily look good to pair it with anything",
          "quantity": 2,
          "price": 12.89,
          "currency": "USD",
          "image_url": "https://storage.googleapis.com/assets.bothub.ai/bothub/templates/twisted_tied_top.jpg"
        }
      ] 
  }
}
```

#### 说明

page\_id是 店铺关联的facebook主页id，topic是来明确推送消息的类别，recipient是用户标识，可以是id，email，phone。params中根据topic中类别来推送具体的信息。

#### 成功返回

```php
{
    "success": true,
    "recipient_id": "1602407863220595"
}
```

#### 失败返回

```php
{
    "success": false
    "errors": {
        "code": 10010,
        "msg": "Recipient not found",
    }
}
```

