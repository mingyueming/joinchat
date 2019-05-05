---
description: >-
  Messenger机器人发送给用户的，我们统称为内容（Block），在这里我们将内容分为三类，分别是：消息块（Message）、动作块（Action）和消息流（Flows）「消息流是由‘消息块’和‘动作块’构成」。
---

# 组成结构

## 消息块

消息是Messenger平台支持的各种形式可以发送给用户如文本、图片、画册、视频以及JoinChat提供的各种类型卡片，如下图：

![&#x6D88;&#x606F;&#x5757;](../.gitbook/assets/image%20%2827%29.png)

Messenger支持在使用卡片过程中可以与用户进行交互，如文本卡片最多支持设置3个按钮。

![&#x6587;&#x672C;&#x5361;&#x7247;](../.gitbook/assets/image%20%2811%29.png)

消息块方便用户快速设定一次性回复给用户多条组合在一起的内容，比如 文本+图片，文本+视频 等，使得您在[自动回复](../general-function/zi-dong-hui-fu/)和[广播群发](../general-function/guang-bo-qun-fa.md)等功能中使用。

## 动作块

您可以通过给任何按钮添加一个动作，用于完成机器人自动触发的特定行为：

![&#x52A8;&#x4F5C;&#x5217;&#x8868;&#x548C;&#x52A8;&#x4F5C;&#x5757;](../.gitbook/assets/image%20%2815%29.png)

### 添加标签和取消标签

通过用户的行为认知，给用户赋予特定的标识，可以帮助您丰富用户画像，更好地划分用户群。

![&#x6DFB;&#x52A0;&#x6807;&#x7B7E;](../.gitbook/assets/image%20%2837%29.png)

### 订阅序列和取消订阅序列

当用户触发了添加该动作的按钮后，机器人会自动推送一些列消息。例如询问用户是否需要店铺动态时，当用户点击“同意”后，您可以在“同意”按钮下关联店铺的动态系列消息。

![&#x8BA2;&#x9605;&#x5E8F;&#x5217;](../.gitbook/assets/image%20%2824%29.png)

### 设置用户属性和清除用户属性

该动作可以给用户创建/清楚某些特定的属性，如性别、电话、邮箱等。通过该动作收集用户信息后存入用户详情中。为了准确的收集特定信息，我们支持以下四类属性：

![&#x8BBE;&#x7F6E;&#x7528;&#x6237;&#x5C5E;&#x6027;](../.gitbook/assets/image%20%2851%29.png)

| 属性类型 | 用例 |
| :--- | :--- |
| 文本类型 | 如地址、邮箱 |
| 数字类型 | 如年龄、电话号 |
| 日期类型 | 如生日 |
| 时间类型 | 如用餐时间 |

### 呼叫人工客服

该动作用于用户点击某些按钮后，由人工来继续服务以达到更好的效果。

![&#x547C;&#x53EB;&#x4EBA;&#x5DE5;&#x5BA2;&#x670D;](../.gitbook/assets/image%20%2864%29.png)

### 订阅机器人和取消订阅机器人

为了提供更好的用户体验，建议运营人员在新用户首次接触机器人时，对用户进行友好提示，仅对同意订阅机器人并触发“订阅机器人”动作的用户进行主动推送营销类或非营销类消息。

![&#x8BA2;&#x9605;&#x673A;&#x5668;&#x4EBA;](../.gitbook/assets/image%20%2850%29.png)

### 查询订单

该功能为电商高级工具，为用户提供便捷的途径进行订单查询。使用该功能前需确保您已经完成店铺绑定，具体使用说明可见[顾客查询订单&包裹](../advanced-functions/gu-ke-cha-xun.md#gu-ke-cha-xun-ding-dan-bao-guo-zhuang-tai)。

![&#x8BA2;&#x5355;&#x67E5;&#x8BE2;](../.gitbook/assets/image%20%2831%29.png)

## 消息流

消息流是由[消息块](zu-cheng-jie-gou.md#xiao-xi-kuai)和[动作块](zu-cheng-jie-gou.md#dong-zuo-kuai)按照运营人员引导用户进行交互体验的内容集合，作为某个完整对话情景的集合体，每个消息块和动作快都通过某个按钮相互关联。

消息流中，有一个特别的标示“起始点“，作为整个消息流中第一个消息块，在触发该消息流时，机器人会自动推送带有“起始点“标记的消息块。同时，您可以拖动“起始点“用于变更触发的第一条消息。

![&#x6D88;&#x606F;&#x6D41;](../.gitbook/assets/image%20%288%29.png)

我们在创建[固定菜单](../general-function/zi-dong-hui-fu/gu-ding-cai-dan.md)中的每一个按钮、[欢迎回复](../general-function/zi-dong-hui-fu/huan-ying-hui-fu.md)、[未知回复](../general-function/zi-dong-hui-fu/wei-zhi-hui-fu.md)、每一条[智能回复](../general-function/zi-dong-hui-fu/ai-hui-fu.md)、每一条[计划回复](../general-function/zi-dong-hui-fu/ji-hua-hui-fu.md)、每一个[事件回复](../general-function/zi-dong-hui-fu/shi-jian-hui-fu.md)都视为一个独立的消息流。为了便于内容的区分及管理，可以在[内容管理](../general-function/)中创建一个新的消息流，同时进行消息流的管理。



