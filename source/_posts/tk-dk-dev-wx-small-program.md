---
title: 淘客多客之小程序实现入门
date: 2018-08-06 18:49:35
categories: 技术
tags: 小程序
---
## 概述

现如今，互联网用户的流量成本越来越高，微信用户流量高，用户成本低，带动了社交电商的发展。作为一种新的开放能力，小程序可以被快速开发，可以在微信内被便捷地获取和传播，同时具有出色的使用体验，天然适合于淘客、多客等电商的推广。

本教程旨在全面讲解如何通过小程序实现淘客、多客，帮助有志于淘客、多客小程序开发的个人开发者快速入门。

### 一些需要了解的概念

**淘宝客应用推广**是指使用淘宝客的商品、店铺、活动及订单等信息输出接口和链接转换接口，可以开发出各类面向消费者的应用，为消费者提供导购服务。消费者通过淘宝客拥有的导购服务在淘宝或天猫购物后，可以按照卖家设定的佣金比例获得淘宝客佣金收益。

**淘宝 PID** 是对应每个账户的代码，用于识别不同的淘宝客。mm 后面带 3 串数字的样式，mm_账号id_推广渠道id_推广位id。

**淘口令**是一种字符串，被复制以后，打开手机淘宝可以跳转到对应的商品或者优惠券。

**多多客应用推广**是指使用多多客的商品、店铺、活动及订单等信息输出接口和链接转换接口，可以开发出各类面向消费者的应用，为消费者提供导购服务。消费者通过多多客应用的导购服务在拼多多购物后，可以按照卖家设定的佣金比例获得多多客佣金收益。

**多多客 PID** 是对应每个账户的代码，用于识别不同的多多客。形如：81_123213 的字符串。

### 用到的技术栈

后端：

* [Lumen](https://lumen.laravel.com/)，为速度而生的 Laravel 框架，为 Web 艺术家创造的框架，简洁、优雅。
* [MySQL](https://www.mysql.com/)，最流行的关系型数据库管理系统之一，在 WEB 应用方面，是最好的 RDBMS (Relational Database Management System，关系数据库管理系统) 应用软件。

前端：

* [小程序](https://mp.weixin.qq.com/cgi-bin/wx)，一种新的开放能力平台，来自于微信。

## 淘客接口实现

### 申请淘宝客 API 权限

从淘宝申请相关账号和权限，操作流程：

* 注册开发者身份，使用淘宝账号登录[淘宝开发平台](http://open.taobao.com/)，通过身份验证后，即可成为开发者；
* 创建验证网站，在[淘宝联盟](https://pub.alimama.com/) - 推广管理 - 网站管理里申请网站接入，通过网站域名验证后，即可获得 APP 证书和初级的淘宝客 API 调用权限。

### Lumen 集成淘宝 SDK

淘宝官方提供了多种编程语言的服务端 SDK，包括：PHP、Java、Python、.NET、Metadata、C、NodeJS。推荐直接使用，不用自己再封装。因为我们使用的是 Lumen 框架，为了方便，我们使用 Composer 包 [orzcc/taobao-top-client](https://packagist.org/packages/orzcc/taobao-top-client)。

```
$ composer require orzcc/taobao-top-client	// Lumen 集成淘宝 SDK
```

### 实现一些必须的接口

对淘客商品、类目等数据的处理有两种方案：

* **全量存储方案**，将数据通过接口存储到自己的数据库，然后再通过接口查询返回到客户端展示；
* **部分存储方案**，只存储部分关键性查询信息(比如：类目)，直接将淘宝返回信息进行处理即返回给客户端展示。

这两种方案，都实现过，也踩过坑，对于个人开发者，推荐第二种方案，优势有几点：

* 数据一致性有保证，因为数据是直接从淘宝实时查询返回的，不用考虑价格、优惠券数量的变更等；
* 商品排序良好，淘宝提供多种排序方式，减少自己排序算法的研发投入；
* 关键词搜索良好，直接使用淘宝的搜索，效率也是高的，保证了结果的准确性，不需要自己考虑分词、关键词匹配等;
* 对数据库的要求低，数据库只是存储一些查询的关键性配置信息，不会有什么压力，维护成本低。
* 劣势主要是不够灵活，可自定义的空间少，另外不便于做复杂的业务类型。但作为个人开发者，人力资源是有限的，维护起来也吃力。

确认好方案，接下来要做的就是对淘宝 SDK 相关接口进行封装，实现一些符合我们业务需求的接口。

还记得上面我们获得的 APP 证书吗？会有两个值 App Key 和 App Secret，添加到 Lumen 项目的 `.env` 配置文件

```
TB_APP_KEY=xxx
TB_APP_SECRET=xxx
TB_ADZONE_ID=xxx
```

`TB_ADZONE_ID` 是指 `adzone_id` 就是我们上面提到的推广位 ID。它的创建在淘宝联盟网站，找到一个商品，点“立即推广”就可以创建。

#### 建立商品类目

这个是我们后面筛选不同类目商品的关键，这里会用到接口 [taobao.itemcats.get](http://open.taobao.com/api.htm?docId=122&docType=2) (获取后台供卖家发布商品的标准商品类目)，因为作为初级淘宝客是没有这个接口的权限的，不过不要紧，我们可以用官方的 [API 测试工具](http://open.taobao.com/doc.htm?apiName=taobao.itemcats.get&docId=1&docType=15)自动配置的 AppKey 来调用获取。

接口返回结果，支持多种格式，我们选择 JSON 格式。结果里有这么几个关键字段：

```
cid	// 商品所属类目ID
parent_cid	// 父类目ID=0时，代表的是一级的类目
name 	// 类目名称
```

这里我们最需要其实是 cid，商品类目的筛选是通过这个字段。因为支持多个目录的筛选，我们可以整理规划出我们自己的类目，多个 cid 以英文 , 隔开。存储到数据库表 tk_opts 待用。

封装一个自己的商品类目接口

```
// 商品类目列表
public function optsList()
{
    $res = TKOpts::select()
        ->get()
        ->toArray();

    return response()->json(['code' => 200, 'data' => $res, 'msg' => '操作成功']);
}
```

#### 商品物料搜索

这里用到接口是 [taobao.tbk.dg.material.optional](http://open.taobao.com/api.htm?docId=35896&docType=2&scopeId=11655)（通用物料搜索API【导购】），通过分页的形式，获取海量淘宝商品物料。

```
use TopClient\TopClient;
use TopClient\request\TbkDgMaterialOptionalRequest;

// 省略部分代码
...

// 通用物料搜索 API（导购）
public function tkDgMaterialOptional($options)
{
	// 必填参数的判断
	...

	$c = new TopClient();
    $c->appkey = $this->appKey;
    $c->secretKey = $this->appSecret;
    $c->format = 'json';	// 定义返回数据格式 JSON
    $req = new TbkDgMaterialOptionalRequest();

    $req->setAdzoneId($options['adzone_id']);

    // 可选参数的判断添加
    ...

    $resp = $c->execute($req);

    // 对结果做一些符合业务需要的处理并返回
    ...
}
```

筛选会用到的几个关键参数：

```
q 	// 查询词
cat // 后台类目 ID，用 , 分割，最大 10 个，该 ID 可以通过 taobao.itemcats.get 接口获取到
adzone_id 	// mm_xxx_xxx_xxx 的第三位
has_coupon	// 是否有优惠券，设置为 true 表示该商品有优惠券，设置为 false 或不设置表示不判断这个属性
sort	// 排序_des（降序），排序_asc（升序），销量（total_sales），淘客佣金比率（tk_rate）， 累计推广量（tk_total_sales），总支出佣金（tk_total_commi），价格（price）
```

#### 获取好券清单

因为通用物料接口传参，查询词和类目 ID 必须有一个，所以不太适合作为一个综合类目展示。恰好淘宝提供另外一个接口 [taobao.tbk.dg.item.coupon.get](http://open.taobao.com/api.htm?docId=29821&docType=2&scopeId=11655)（好券清单API【导购】），可以支持支持同时不传查询词和类目 ID，通过分页的形式获取好券清单。

```
use TopClient\TopClient;
use TopClient\request\TbkDgItemCouponGetRequest;

// 省略部分代码
...

// 好券清单 API（导购）
public function tkDgItemCouponGet($options)
{
	// 必填参数的判断
	...

	$c = new TopClient();
    $c->appkey = $this->appKey;
    $c->secretKey = $this->appSecret;
    $c->format = 'json';	// 定义返回数据格式 JSON
    $req = new TbkDgItemCouponGetRequest();

    $req->setAdzoneId($options['adzone_id']);

    // 可选参数的判断添加
    ...

    $resp = $c->execute($req);

    // 对结果做一些符合业务需要的处理并返回
    ...
}
```

当后台类目和查询词均不指定的时候，最多出 10000 个结果，即 page_no*page_size 不能超过 10000；当指定类目或关键词的时候，则最多出 100 个结果。我们这里把它作为综合类目，所以不指定。

#### 获取商品详情

通过接口 [taobao.tbk.item.info.get](http://open.taobao.com/api.htm?docId=24518&docType=2&scopeId=11655)（淘宝客商品详情【简版】）获取商品详情，用于单个商品页面数据的展示。

```
use TopClient\TopClient;
use TopClient\request\TbkItemInfoGetRequest;

// 省略部分代码
...

// 淘宝客商品详情（简版）
public function tkItemInfoGet($options)
{
	// 必填参数的判断
	...

	$c = new TopClient();
    $c->appkey = $this->appKey;
    $c->secretKey = $this->appSecret;
    $c->format = 'json';	// 定义返回数据格式 JSON
    $req = new TbkItemInfoGetRequest();
    $req->setFields('num_iid,title,pict_url,small_images,reserve_price,zk_final_price,user_type,provcity,item_url');
    $req->setNumIids($options['num_iids']);

    $resp = $c->execute($req);

    // 对结果做一些符合业务需要的处理并返回
    ...
}
```

#### 生成淘口令

通过接口 [taobao.tbk.tpwd.create](http://open.taobao.com/api.htm?docId=31127&docType=2&scopeId=11655)（ 淘宝客淘口令) 生成商品或优惠券的淘口令。

```
use TopClient\TopClient;
use TopClient\request\TbkTpwdCreateRequest;

// 省略部分代码
...

// 淘宝客淘口令（免费）
public function tkTpwdCreate($options)
{
	// 必填参数的判断
	...

	$c = new TopClient();
    $c->appkey = $this->appKey;
    $c->secretKey = $this->appSecret;
    $c->format = 'json';	// 定义返回数据格式 JSON
    $req = new TbkTpwdCreateRequest();
    $req->setText($options['text']);	// 口令弹框内容
    $req->setUrl($options['url']);	// 口令跳转目标页

    // 可选参数的判断添加
    ...

    $resp = $c->execute($req);

    // 对结果做一些符合业务需要的处理并返回
    ...
}
```

#### 推荐搜索词

通过创建表 tk_recommend_search_words 可以存储一些推荐客户搜索的关键词，这个表的数据来源有两个，一是用户搜索，二是自己定义。

```
// 获取多客推荐搜索词
public function getSearchKeyWord()
{
    $res = TKRecommendSearchWords::select()->get()->toArray();

    return response()->json(['code' => 200, 'data' => $res, 'msg' => '操作成功']);
}
```

通过以上这些接口，就可以满足我们后续小程序代码的基本业务需求。

## 多客接口实现

### 申请多多客 API 权限

从拼多多申请相关账号和权限，操作流程：

* 注册[拼多多开放平台](http://open.pinduoduo.com/)账号，并申请成为第三方开发者；
* 在控制台——我的应用里创建多多客联盟应用，等待审核通过，获得 client_id；
* 在[多多进宝](http://ddjb.pinduoduo.com/) —— API 权限里绑定 client_id，即可获得多多客 API 权限。

### Lumen 集成多多客 API 准备

因为多多客官方没有提供相关 SDK，所以我们需要先封装一些基本的方法，方便后续具体 API 的调用。

#### 签名方法

实现一个签名方法，方便后续 API 调用验证签名。

```
// 生成 sign
public function sign($options)
{
    ksort($options);
    $str = '';

    foreach ($options as $key => $value) {

        $str .= $key . $value;
    }

    $str = $this->clientSecret . $str . $this->clientSecret;
    $str = md5($str);
    $sign = strtoupper($str);

    return $sign;
}
```

#### HTTP 请求

封装一个 HTTP 请求方法，方便请求拼多多 API 接口。

```
// http 请求方法
public function request($options)
{
    $ch = curl_init($this->api);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $options);

    $res = curl_exec($ch);
    $res = json_decode($res, true);

    return $res;
}
```

### 实现一些必须的接口

对多多客商品、类目等数据的处理我们同样采用类似处理淘宝客的方案，来实现一些符合我们业务需求的接口。

我们在拼多多开发平台创建的多多客联盟应用审核通过后，会获得两个主要参数：client_id 和 client_secret，类似于淘宝客的 App Key 和 App Secret，同样的，我们添加到 Lumen 项目的 `.env` 配置文件。

```
DK_CLIENT_ID=xxx
DK_CLIENT_SECRET=xxx
DK_API=http://gw-api.pinduoduo.com/api/router
DK_P_ID=xxx
```

DK_API 是指正式环境调用拼多多 API 地址，只支持 POST 请求。DK_P_ID 是指多多客 PID。

#### 建立商品类目

同样的这个是我们后面筛选不同类目商品的关键，这里我们通过接口 [pdd.goods.opt.get](http://open.pinduoduo.com/#/apidocument/port?id=50)（查询商品标签列表）获取一级的商品标签作为我们的类目。

```
// 查询商品标签列表
public function dkGoodsOptGet($options)
{
    $options['client_id'] = $this->clientId;
    $options['timestamp'] = time();
    $options['type'] = 'pdd.goods.opt.get';
    $sign = $this->sign($options);
    $options['sign'] = $sign;

    $res = $this->request($options);

	// 对结果做一些符合业务需要的处理并返回
	...
}
```

当 $options['parent_opt_id'] 为 0 时，即为一级的商品标签。返回的结果中 opt_id 商品标签 ID 是后面筛选商品需要的字段。

当然你也可以将标签做一些筛选整理存储本地数据库，再从本地数据库读取。

#### 商品查询

通过接口 [pdd.ddk.goods.search](http://open.pinduoduo.com/#/apidocument/port?id=28)（多多进宝商品查询）获取我们需要的商品列表。

```
// 多多进宝商品查询
public function dkGoodsSearch($options)
{
    $options['client_id'] = $this->clientId;
    $options['timestamp'] = time();
    $options['type'] = 'pdd.ddk.goods.search';
    $sign = $this->sign($options);
    $options['sign'] = $sign;

    $res = $this->request($options);

	// 对结果做一些符合业务需要的处理并返回
	...
}
```

不传 $options['opt_id'] 获取的结果可以作为综合类目，反之可以筛选需要的类目。其它比较关键的参数：

```
keyword 	// 商品关键词
sort_type	// 排序方式，有 20 多种之多，非常丰富
with_coupon	// 是否只返回优惠券的商品
range_list	// 范围列表，根据不同维度筛选
```

另外，上述结果如果按照默认排序筛选，会获得与拼多多官方应用完全一致的排序结果。

#### 商品查询详情

通过接口 [pdd.ddk.goods.detail](http://open.pinduoduo.com/#/apidocument/port?id=28)（多多进宝商品详情查询）获取商品的详情，用于单个商品页面数据的展示。

```
// 多多进宝商品详情查询
public function dkGoodsDetail($options)
{
	// 必填参数的判断
	...

    $options['client_id'] = $this->clientId;
    $options['timestamp'] = time();
    $options['type'] = 'pdd.ddk.goods.detail';
    $sign = $this->sign($options);
    $options['sign'] = $sign;

    $res = $this->request($options);

	// 对结果做一些符合业务需要的处理并返回
	...
}
```

参数 $options['goods_id_list'] 商品ID，仅支持单个查询。

#### 生成小程序跳转地址

拼多多支持其它小程序跳转到拼多多官方小程序进行微信的直接支付购买。这个也是淘客小程序不可能具有的便利优势。

由于微信的限制，只有同个公众号下的小程序才能进行跳转，所以需要在自己的公众号下绑定自己的和拼多多的小程序。

一个小程序可关联最多 500 个公众号，这个资源是有限的，不过我们拿到了一个名额，这个功能得以做下去。

通过接口 [pdd.ddk.goods.promotion.url.generate](http://open.pinduoduo.com/#/apidocument/port?id=28)（多多进宝推广链接生成）可以生成我们需要的跳转地址。

```
// 生成小程序跳转地址
public function dkGoodsPromotionUrlGenerate($options)
{
	// 必填参数的判断
	...

    $options['client_id'] = $this->clientId;
    $options['timestamp'] = time();
    $options['type'] = 'pdd.ddk.goods.promotion.url.generate';
    $sign = $this->sign($options);
    $options['sign'] = $sign;

    $res = $this->request($options);

	// 对结果做一些符合业务需要的处理并返回
	...
}
```

需要传参数 $options['goods_id_list'] 商品 ID，仅支持单个查询，参数 $options['generate_we_app'] 生成唤起小程序的关键参数，这个文档里是没有的，属于内部知道的参数。

#### 获取主题列表

拼多多官方运营人员会持续创建需要优质的主题活动，也支持自己创建，可以通过接口 [pdd.ddk.theme.list.get](http://open.pinduoduo.com/#/apidocument/port?id=28)（多多进宝主题列表查询）来获取。

```
// 多多进宝主题列表查询
public function dkThemeListGet($options)
{
    $options['client_id'] = $this->clientId;
    $options['timestamp'] = time();
    $options['type'] = 'pdd.ddk.theme.list.get';
    $sign = $this->sign($options);
    $options['sign'] = $sign;

    $res = $this->request($options);

	// 对结果做一些符合业务需要的处理并返回
	...
}
```

主题列表会包含：主题 ID、主题图片、主题名称、主题商品数量等字段，方便展示主题列表。

#### 获取主题列表商品

有了上述返回的主题 ID，我们就可以通过接口 [pdd.ddk.theme.goods.search](http://open.pinduoduo.com/#/apidocument/port?id=28)（多多进宝主题商品查询）获取主题含有的商品列表。

```
// 多多进宝主题商品查询
public function dkThemeGoodsSearch($options)
{
	// 必填参数的判断
	...

    $options['client_id'] = $this->clientId;
    $options['timestamp'] = time();
    $options['type'] = 'pdd.ddk.theme.goods.search';
    $sign = $this->sign($options);
    $options['sign'] = $sign;

    $res = $this->request($options);

	// 对结果做一些符合业务需要的处理并返回
	...
}
```

#### 推荐搜索词

通过创建表 dk_recommend_search_words 可以存储一些推荐客户搜索的关键词，这个表的数据来源有两个，一是用户搜索，二是自己定义。

```
// 获取多客推荐搜索词
public function getSearchKeyWord()
{
    $res = DKRecommendSearchWords::select()->get()->toArray();

    return response()->json(['code' => 200, 'data' => $res, 'msg' => '操作成功']);
}
```

通过以上这些接口，就可以满足我们后续小程序代码的基本业务需求。

## 小程序实现

我们现在已经实现的小程序有两款，分别针对的是两种不同的数据存储方案，基于全量存储方案的 `好券优选lite` 小程序，这是一个淘客的小程序，这个不是我们的重点。我们主要说基于部分存储方案的 `优选小工具` 小程序。

![](http://img.zinaer.com/blog/writing/you_min_logo1.jpg!/fw/200)
<center>识别该小程序码查看该小程序</center>

这个小程序的初衷是汇聚一批好用的工具，所以严格意义不是一个单纯的淘客、多客小程序，不过里面关于淘客、多客的内容都完全可以独立出来做成一个小程序。

### 申请小程序

微信对于个人开发者开放的类目有限，我们这里选择 `工具-报价/比价` 或者 `工具-信息查询` 来进行小程序的申请，也是推荐的。

原则上个人是不支持做电商类小程序的，尤其对于淘宝，微信是更不会通过申请的，所以为了提高申请通过率，以下一些技巧可以帮助你：

* 小程序名称要明确，不要强行蹭热点，蹭关键词，不要有淘宝、天猫、内部券、优惠券等词；
* 小程序介绍要与名称相关，不要风马牛不相及；
* 小程序 logo 不要有淘宝、天猫、内部优惠券等词。

不过我们毕竟是想要做淘客、多客的嘛，上述的一些信息，我们可以等小程序代码审核通过后，再进行修改，能不能通过就看运气了。

### 关键功能的实现

小程序申请下来了，我们就要进行代码方面的实现了。下面举例一些关键功能的实现，有些功能是淘客、多客通用的就不做区分说明了。

#### 配置信息

这里会针对小程序创建配置信息表 z_config，这个表会包含：

* 开关，控制线上/审核，顾名思义，线上就是我们最终想展示给用户的版本，审核是展示给审核人员的版本；
* 代码中不利于审核通过的信息存表；
* 一个可能会改变的信息，方便不用发版就可以更改。

不过为了便于拆分和管理，我这边会针对每个功能，比如：淘客、多客都创建单独的配置表。

#### 在欢迎页面判断要展示的版本

给小程序添加了一个欢迎页面，有两个目的：一是可以用来展示一些宣传信息；二是在渲染这个页面时会从服务端取到配置信息，决定展示的版本。

```
// 决定展示的版本
wx.request({
  url: app.globalData.zinaerApi + '/ztool/getConfig',
  data: options,
  method: 'POST',
  success: function (res) {
    if (res.statusCode == 200 && res.data.code == 200) {
      if (res.data.data.online == 1) {
        wx.switchTab({
          url: '../index/index',
        })
      }
      else {
        wx.navigateTo({
          url: '../zt/zt',
        })
      }
    }
  }
})
```

审核版本可以写一些简单的小工具，比如本小程序是一个 IP 地址信息查询的一个功能。

#### 主题列表

```
// 获取主题列表
wx.request({
  url: app.globalData.zinaerApi + '/dk/themeListGet',
  data: options,
  method: 'POST',
  success: function (res) {
    if (res.statusCode == 200 && res.data.code == 200) {
      self.setData({
        swiperData: res.data.data.theme_list
      })
    }
  }
})
```

可以使用分页参数取出几个，比如 5 个最新的主题作为首页的轮播滚动主题。

```
options['page'] = 1
options['page_size'] = 5
```

```
<swiper indicator-dots="{{indicatorDots}}" autoplay="{{autoplay}}" interval="{{interval}}" duration="{{duration}}">
  <block wx:key="{{id}}" wx:for="{{swiperData}}">
    <swiper-item>
      <image src="{{item.image_url}}" class="slide-image" data-hi="{{item}}" bindtap="openAlert"/>
    </swiper-item>
  </block>
</swiper>
```

#### 工具列表

创建了一个 z_data 工具数据表，用来存储不同的工具基础信息。这里主要分为两类，一种是程序内的，一种是跳转到其它小程序。

```
<view class="weui-panel__bd" wx:key="{{item.id}}" wx:for="{{zData}}">
  <navigator wx:if="{{item.type == 1}}" target="miniProgram" app-id="{{item.content}}" class="weui-media-box weui-media-box_appmsg" hover-class="weui-cell_active">
    <view class="weui-media-box__hd weui-media-box__hd_in-appmsg">
      <image class="weui-media-box__thumb" src="{{item.image_url}}" />
    </view>
    <view class="weui-media-box__bd weui-media-box__bd_in-appmsg">
      <view class="weui-media-box__title">{{item.title}}</view>
      <view class="weui-media-box__desc">{{item.des}}</view>
    </view>
  </navigator>
  <navigator wx:else url="{{item.content}}" class="weui-media-box weui-media-box_appmsg" hover-class="weui-cell_active">
    <view class="weui-media-box__hd weui-media-box__hd_in-appmsg">
      <image class="weui-media-box__thumb" src="{{item.image_url}}" />
    </view>
    <view class="weui-media-box__bd weui-media-box__bd_in-appmsg">
      <view class="weui-media-box__title">{{item.title}}</view>
      <view class="weui-media-box__desc">{{item.des}}</view>
    </view>
  </navigator>
</view>
```

#### 商品列表页面

通过写好的接口获取第一页的数据，同时会将下一页的请求数据封装好 options，通过小程序的页面上拉触底事件的处理函数 `onReachBottom` 实现分页的不停加载。

```
wx.request({
  url: app.globalData.zinaerApi + '/dk/goodsSearch',
  data: options,
  method: 'POST',
  success: function (res) {
    if (res.statusCode == 200 && res.data.code == 200) {
      if (res.data.data.goods_list.length < 100) {
        var isHideLoadMore = 1
        options.page = 0
      }
      else {
        var isHideLoadMore = 0
        options.page = options.page+1
      }

      if (self.data.goods == '') {
        var goods = res.data.data.goods_list
      }
      else {
        var goods = self.data.goods.concat(res.data.data.goods_list)
      }

      self.setData({
        goods: goods,
        options: options,
        isHideLoadMore: isHideLoadMore
      })
    }
  }
})
```

#### 具体商品页面

通过写好的接口取到商品详情，然后进行单个商品信息渲染。

```
wx.request({
  url: app.globalData.zinaerApi + '/dk/goodsDetail',
  data: options,
  method: 'POST',
  success: function (res) {
    if (res.statusCode == 200 && res.data.code == 200) {
      self.setData({
        good: res.data.data.goods_details[0]
      })
    }
  }
})
```

#### 商品分享和购买按钮的实现

以下是分享和购买按钮的页面实现，其中购买是跳转到拼多多官方小程序的。

```
<button class="share" data-hi="{{good}}" open-type="share">
  <view class="icon">
    <image src="/images/pdd_share.png"></image>
  </view>
  <view class="name">{{config.share}}</view>
</button>
<navigator class="normal" target="miniProgram" open-type="navigate" app-id="{{appInfo.app_id}}" path="{{appInfo.page_path}}">
  <view class="price">￥{{(good.min_normal_price - good.coupon_discount) / 100}}</view>
  <view class="name">{{config.normal_buy}}</view>
</navigator>
<navigator class="group" target="miniProgram" open-type="navigate" app-id="{{appInfo.app_id}}" path="{{appInfo.page_path}}">
  <view class="price">￥{{(good.min_group_price - good.coupon_discount) / 100}}</view>
  <view class="name">{{config.group_buy}}</view>  
</navigator>
```

通过小程序自带方法 `onShareAppMessage` 实现分享到微信好友或微信群的功能。

```
onShareAppMessage: function (e) {
  if (e.from === 'button') {
    return {
      'title': e.target.dataset.hi.goods_name,
      'imageUrl': e.target.dataset.hi.goods_image_url
    }
  }
}
```

通过接口生成跳转拼多多官方小程序的信息

```
wx.request({
  url: app.globalData.zinaerApi + '/dk/pagePath',
  data: options,
  method: 'POST',
  success: function (res) {
    if (res.statusCode == 200 && res.data.code == 200) {
      self.setData({
        appInfo: res.data.data
      })
    }
  }
})
```

淘客因为不支持跳转，我们购买按钮用显示模态弹窗的方式实现，生成淘口令并复制，从而可以在手淘中购买。

```
wx.request({
  url: app.globalData.zinaerApi + '/tk/tpwdCreate',
  data: options,
  method: 'POST',
  success: function (res) {
    if (res.statusCode == 200 && res.data.code == 200) {
      wx.showModal({
        title: good.coupon_price + self.data.config.coupon,
        content: res.data.data.model_content,
        showCancel: false,
        confirmText: '复制',
        success: function (result) {
          if (result.confirm) {
            wx.setClipboardData({
              data: res.data.data.model
            })
          }
        }
      })
    }
  }
})
```

以上是对做 优选小工具 小程序的学习和实践，做的一些总结，希望对想要学习这方面开发的人有所帮助。