---
description: 「计划回复」是一项功能，允许给您的用户通过不同时间的延迟有间隔的发送一系列消息。您可以在「智能回复」 -「计划回复」 模块中找到当前计划或创建新计划。
---

# 计划回复

{% hint style="warning" %}
请注意，相同的计划用户只能收到一次。如果希望用户可以从头再次收到该计划中的消息，您需要先给用户添加[取消订阅计划](../../basic-knowledge/zu-cheng-jie-gou.md#ding-yue-xu-lie-he-qu-xiao-ding-yue-xu-lie)动作，然后再次添加[订阅计划](../../basic-knowledge/zu-cheng-jie-gou.md#ding-yue-xu-lie-he-qu-xiao-ding-yue-xu-lie)动作。 
{% endhint %}

![&#x65B0;&#x5EFA;&#x8BA1;&#x5212;](../../.gitbook/assets/image%20%284%29.png)

如何给用户添加计划？

通过在「智能回复」的按钮上添加「订阅计划回复」的动作，选择已经设定好的计划回复消息即可。

借助消息标签，您可以在需要持续发送通知或最新消息的一些有限情况下，不受 24 小时时间窗的限制向用户发送消息。这样一来，您的机器人就可以更灵活地与用户互动，您也可以在 Messenger 平台为用户打造更丰富的体验。

> 24小时时间窗规则：
>
> 用户与机器人互动后（例如：给您的机器人发消息，或通过您网站上的 Messenger 插件进行互动）后 24 小时内，您可以通过机器人发消息给该用户。 24 小时后，您仅可再向用户发送一条消息以便跟进对话，直至用户发起下次互动。24 小时期限过后，只能发送已获批准的模板和标签。不得将消息模板或标签用于非预期用途。

![&#x6D88;&#x606F;&#x6807;&#x7B7E;](../../.gitbook/assets/image%20%2826%29.png)

请注意，消息标签仅用于发送非推广内容。

使用消息标签发送推广内容（例如，每日特惠、优惠券、折扣或促销公告）是违反 Messenger 平台政策的行为。

但只限于特定用途的消息发送（详见下表），不同的标签下，可发送的卡片类型有所限制

Facebook 目前支持的标签列表如下：

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x4E2D;&#x6587;&#x540D;&#x79F0;</th>
      <th style="text-align:left">&#x6807;&#x7B7E;&#x8BF4;&#x660E;</th>
      <th style="text-align:left">&#x652F;&#x6301;&#x7684;&#x5361;&#x7247;&#x7C7B;&#x578B;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">&#x95EE;&#x9898;&#x8FDB;&#x5EA6;&#x8FFD;&#x8E2A;</td>
      <td style="text-align:left">&#x5728; Messenger &#x5BF9;&#x8BDD;&#x4E2D;&#x53D1;&#x8D77;&#x7684;&#x5BA2;&#x6237;&#x670D;&#x52A1;&#x95EE;&#x9898;&#x76F8;&#x5173;&#x7684;&#x72B6;&#x6001;&#x66F4;&#x65B0;</td>
      <td
      style="text-align:left">
        <p>&#x6587;&#x672C;&#x5361;&#x7247;</p>
        <p>&#x6A71;&#x7A97;&#x5361;&#x7247;</p>
        </td>
    </tr>
    <tr>
      <td style="text-align:left">&#x9884;&#x7EA6;&#x72B6;&#x6001;&#x66F4;&#x65B0;</td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x7528;&#x6237;&#x73B0;&#x6709;&#x9884;&#x7EA6;&#x7684;&#x66F4;&#x65B0;</td>
      <td
      style="text-align:left">&#x6A71;&#x7A97;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x5DF2;&#x62A5;&#x540D;&#x6D3B;&#x52A8;&#x63D0;&#x9192;</td>
      <td style="text-align:left">&#x5DF2;&#x62A5;&#x540D;&#x6D3B;&#x52A8;&#x7684;&#x53C2;&#x52A0;&#x63D0;&#x9192;</td>
      <td
      style="text-align:left">&#x6A71;&#x7A97;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x975E;&#x8425;&#x9500;&#x8BA2;&#x9605;&#x6D88;&#x606F;</td>
      <td style="text-align:left">&#x53D1;&#x9001;&#x89C4;&#x5B9A;&#x7C7B;&#x522B;&#x7684;&#x975E;&#x63A8;&#x5E7F;&#x6D88;&#x606F;&#xFF1A;&#x65B0;&#x95FB;&#x8D44;&#x8BAF;&#x3001;&#x5DE5;&#x4F5C;&#x6548;&#x7387;&#x548C;&#x4E2A;&#x4EBA;&#x8FFD;&#x8E2A;&#x3002;
        &#x60A8;&#x53EF;&#x5728;&#x4E3B;&#x9875;&#x7684;&#x201C;&#x8BBE;&#x7F6E;&#x201D;&gt;&#x201C;Messenger
        &#x5E73;&#x53F0;&#x201D;&#x7248;&#x5757;&#x7533;&#x8BF7;&#x6B64;&#x6807;&#x7B7E;&#x7684;&#x4F7F;&#x7528;&#x6743;&#x9650;&#x3002;</td>
      <td
      style="text-align:left">&#x6240;&#x6709;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x793E;&#x533A;&#x901A;&#x77E5;</td>
      <td style="text-align:left">&#x53D1;&#x9001;&#x901A;&#x77E5;&#x6765;&#x63D0;&#x9192;&#x7D27;&#x6025;&#x4E8B;&#x4EF6;&#x6216;&#x516C;&#x7528;&#x8BBE;&#x65BD;&#x95EE;&#x9898;&#xFF0C;&#x6216;&#x5728;&#x60A8;&#x7684;&#x793E;&#x7FA4;&#x5185;&#x8BE2;&#x95EE;&#x5E73;&#x5B89;&#x72B6;&#x51B5;</td>
      <td
      style="text-align:left">&#x6A71;&#x7A97;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x7269;&#x6D41;&#x4FE1;&#x606F;&#x66F4;&#x65B0;</td>
      <td style="text-align:left">&#x7528;&#x6237;&#x67D0;&#x4E2A;&#x5DF2;&#x8D2D;&#x4E70;&#x5546;&#x54C1;&#x7684;&#x914D;&#x9001;&#x72B6;&#x6001;&#x66F4;&#x65B0;</td>
      <td
      style="text-align:left">&#x6A71;&#x7A97;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x4ED8;&#x6B3E;&#x4FE1;&#x606F;&#x66F4;&#x65B0;</td>
      <td style="text-align:left">&#x7528;&#x6237;&#x73B0;&#x6709;&#x4EA4;&#x6613;&#x7684;&#x652F;&#x4ED8;&#x72B6;&#x6001;&#x66F4;&#x65B0;&#x901A;&#x77E5;</td>
      <td
      style="text-align:left">&#x6A71;&#x7A97;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x8D26;&#x53F7;&#x53D8;&#x52A8;&#x4FE1;&#x606F;</td>
      <td style="text-align:left">&#x7528;&#x6237;&#x5E10;&#x6237;&#x8BBE;&#x7F6E;&#x7684;&#x66F4;&#x6539;&#x901A;&#x77E5;</td>
      <td
      style="text-align:left">&#x6A71;&#x7A97;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x5339;&#x914D;&#x7ED3;&#x679C;&#x63D0;&#x9192;</td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x7528;&#x6237;&#xFF0C;&#x5DF2;&#x6839;&#x636E;&#x4E4B;&#x524D;&#x7684;&#x8BF7;&#x6C42;&#x627E;&#x5230;&#x4E86;&#x5339;&#x914D;&#x9879;</td>
      <td
      style="text-align:left">&#x7A97;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x4E2A;&#x4EBA;&#x8D22;&#x52A1;&#x63D0;&#x9192;</td>
      <td style="text-align:left">&#x63D0;&#x9192;&#x7528;&#x6237;&#x786E;&#x8BA4;&#x5176;&#x8D22;&#x52A1;&#x6D3B;&#x52A8;</td>
      <td
      style="text-align:left">&#x6A71;&#x7A97;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x6E38;&#x620F;&#x63D0;&#x9192;</td>
      <td style="text-align:left">&#x6E38;&#x620F;&#x5185;&#x7528;&#x6237;&#x8FDB;&#x5EA6;&#x3001;&#x5168;&#x5C40;&#x6D3B;&#x52A8;&#x6216;&#x76F4;&#x64AD;&#x4F53;&#x80B2;&#x8D5B;&#x4E8B;&#x7684;&#x66F4;&#x6539;&#x901A;&#x77E5;</td>
      <td
      style="text-align:left">&#x6A71;&#x7A97;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x7533;&#x8BF7;&#x72B6;&#x6001;&#x66F4;&#x65B0;</td>
      <td style="text-align:left">&#x7528;&#x6237;&#x7533;&#x8BF7;&#x72B6;&#x6001;&#x7684;&#x66F4;&#x65B0;&#x901A;&#x77E5;</td>
      <td
      style="text-align:left">&#x6A71;&#x7A97;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x529F;&#x80FD;&#x66F4;&#x65B0;</td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x7528;&#x6237;&#xFF0C;&#x673A;&#x5668;&#x4EBA;&#x63A8;&#x51FA;&#x4E86;&#x65B0;&#x7279;&#x6027;&#x6216;&#x65B0;&#x529F;&#x80FD;</td>
      <td
      style="text-align:left">&#x6A71;&#x7A97;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x4EA4;&#x901A;&#x72B6;&#x6001;&#x66F4;&#x65B0;</td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x7528;&#x6237;&#xFF0C;&#x73B0;&#x6709;&#x4EA4;&#x901A;&#x8FD0;&#x8F93;&#x670D;&#x52A1;&#x9884;&#x8BA2;&#x7684;&#x66F4;&#x65B0;</td>
      <td
      style="text-align:left">&#x6A71;&#x7A97;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x7968;&#x52A1;&#x72B6;&#x6001;&#x66F4;&#x65B0;</td>
      <td style="text-align:left">&#x5411;&#x7528;&#x6237;&#x53D1;&#x9001;&#x5DF2;&#x8D2D;&#x7968;&#x6D3B;&#x52A8;&#x7684;&#x66F4;&#x65B0;&#x6216;&#x63D0;&#x9192;</td>
      <td
      style="text-align:left">&#x6A71;&#x7A97;&#x5361;&#x7247;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x4F1A;&#x9762;&#x72B6;&#x6001;&#x66F4;&#x65B0;</td>
      <td style="text-align:left">&#x901A;&#x77E5;&#x7528;&#x6237;&#xFF0C;&#x73B0;&#x6709;&#x4F1A;&#x9762;&#x9884;&#x7EA6;&#x7684;&#x66F4;&#x6539;</td>
      <td
      style="text-align:left">&#x6A71;&#x7A97;&#x5361;&#x7247;</td>
    </tr>
  </tbody>
</table>