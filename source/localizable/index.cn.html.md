# 基本介绍
## 简介
欢迎使用KuCoin合约(KuCoin Futures)开发者文档。 此文档概述了交易功能、市场行情和其他应用开发接口。


KuCoin Futures API分为两部分：`REST API` 和 `Websocket`实时数据流

 -  REST API 包含三个类别：`用户（私有）`、`交易（私有）`、`市场数据（公共）`
 -  Websocket包含两类：`公共频道`和`私人频道`

<aside class="notice">
    目前该文档相关接口暂未开放使用，上线时间另行通知，如有任何疑问请邮箱联系<code>newapi@kucoin.plus</code>
</aside>

## 更新预告

<!-- **为了进一步提升API安全性，KuCoin已经升级到了V2版本的API-KEY，验签逻辑也发生了一些变化，建议到[API管理页面](https://futures.kucoin.com/api)添加并更换到新的API-KEY。KuCoin将继续支持使用老的API-KEY到2021年05月01日。请查看“消息签名”，了解更多详情** -->


## 客户端开发库（SDK）

使用客户端开发库可快速集成到KuCoin Futures API。

* 官方软件开发工具包（SDK）
    - [PHP SDK](https://github.com/Kucoin/kucoin-futures-php-sdk)
    - [Java SDK](https://github.com/Kucoin/kucoin-futures-java-sdk)
    - [Node.js SDK](https://github.com/Kucoin/kucoin-futures-node-sdk)
    - [Python SDK](https://github.com/Kucoin/kucoin-futures-python-sdk)
    - [Go SDK](https://github.com/Kucoin/kucoin-futures-go-sdk)
  

## 沙盒测试环境
沙盒是测试环境，用于测试`API`连接和`Web`交易，并提供交易的所有功能。在沙盒中，您可以使用`虚假资金`来测试交易功能。
沙盒环境中的登录会话和API密钥与生产环境完全`分离`。您需要在Web登录沙盒网址`https://sandbox-futures.kucoin.com`去创建`API`密钥。
<aside class="notice">
    在沙盒环境中注册后，您将收到系统在您的帐户中自动充值的一定数量的`虚假资金（BTC）`。如果您想交易，请将资产从`储蓄账户`转移到`交易账户`。这些资金仅用于测试目的，不能提现。
</aside>

沙盒REST API地址: `https://api-sandbox-futures.kucoin.com` 
<aside class="warning">
    旧的沙盒REST API地址`https://sandbox-api.kumex.com`已经废弃
</aside>


## 请求频率限制

当请求频率超过限制频率时，系统将返回code为**429**的超频错误码。
<aside class="notice">如果接口请求频率超过限制，你的IP或账户会被限制使用10s。</aside>

### REST API

目前有以下3种限频规则：

* `code: 1015`是指从该IP的请求过多，触发了cloudflare限制, 触发后限制时间`30s`。建议不同(子)账号使用不同的IP去请求，避免相互影响。
* `{"code":"200002","msg":"Too many requests in a short period of time, please retry later"}`是指账号特定接口超过请求限制, 触发后限制时间为`10s`。您可以使用多个子账号来避免这个问题。
* `{"code":"429000","msg":"Too Many Requests"}`是指服务器短时间收到过多请求，由于服务器过载保护拒绝了此次请求。您可以直接重试请求。

### WEBSOCKET

**连接数量**
- 每个用户ID同时建立的连接数：≤ `50`个

**连接次数**
 - 连接请求次数限制：≤ `30`次/分钟

**上行消息条数** 
 - 向服务器发送指令条数限制：≤  `100`条/`10`秒

**订阅topic数量** 
 - 每个连接最大可订阅topic数量限制：≤`100`个topics

## 做市激励计划
KuCoin Futures为专业做市商提供做市激励计划。 参与该计划，可以获得以下激励：

 - 做市商返佣
 - 每月嘉奖前十名表现优良的做市团队，高额回馈最佳做市商
 - 直接市场接入（提供`private link`接入）

具有良好做市策略和大交易量的用户欢迎参与此长期做市激励计划。如果您的账户在过去30天内的交易量超过5000 BTC，请提供以下信息以发送电子邮件至`mm@kucoin.com`，邮件主题为`“Futures Market Maker Application”`：

 - 提供平台帐户（需要电子邮件，无需推荐关系）
 - 过去`30`天内在任何交易所交易的交易量证明或VIP级别的证明
 - 请简要说明做市的方法（不需要详细说明）以及估算做市订单量的百分比。

## VIP快速通道
具有良好做市策略和大交易量的用户欢迎参与长期做市商计划。 如果您的帐户资产大于`10`BTC，请提供以下信息以发送电子邮件至：`vip_futures@kucoin.com`进行做市商申请;

 - 提供平台帐户（需要电子邮件，无需推荐关系）;
 - 提供其他交易平台做市商交易量的截图（例如`30`天内的交易量，或VIP级别等）; 
 - 请简要说明做市的方法，不需要详细说明；

## 数据中心
KuCoin 数据中心位于AWS日本东京 (ap-northeast-1a) 地区。

## 常见问题

问题：如何计算订单成交平均价格
<br/>
答复：订单成交平均价格 = `dealValue`/`dealSize`.

问题：为什么没有找到XBT相关的合约
<br/>
答复：BTC交易对的symbol代码，会统一将`XBT`修改为`BTC`。比如：U本位BTC永续合约，symbol的代码会从 XBTUSDTM 换成 BTCUSDTM.

---
# REST API
## 请求说明

### API服务器地址

REST API对用户、交易及市场数据均提供了接口。

基本URL：`https://api-futures.kucoin.com`
请求URL由`基本URL`和指定`接口端点`组成。
<aside class="warning">`https://api.kumex.com`已过期不推荐使用</aside>
<aside class="notice">为了遵守当地法律要求，使用中国IP的用户不允许访问以上URL。</aside>



## 接口端点

每个接口都提供了对应的端点，可在`HTTP`请求模块下获取。


对于`GET`请求，只需将请求参数拼接在请求路径后面。

例如：对于“获取某个合约的仓位” 接口，其默认端点为`/api/v2/symbol-position`。请求“合约”参数（`BTCUSDTM`）时，该端点将变为：`/api/v2/symbol-position?symbol=BTCUSDTM`。因此，您最终请求的URL应为：`https://api-futures.kucoin.com/api/v2/symbol-position?symbol=BTCUSDTM`。

## 请求
所有的请求和响应的内容类型都是`application/json`。  


除非另行说明，所有的时间戳参数均以Unix时间戳毫秒计算。如：`1544657947759`

## 参数

对于`GET`和`DELETE`请求，需将参数拼接在请求URL中（如：`/api/v2/symbol-position?symbol=BTCUSDTM`）。

对于`POST`和`PUT`请求，需将参数以JSON格式拼接在请求主体中（如：`{"side":"buy"}`）。
<aside class="notice">不要在JSON字符串中添加空格</aside>


### 错误返回

系统会返回`HTTP错误代码`或`系统错误代码`。您可根据返回的参数消息排查错误原因。


#### HTTP错误码

```json
  {
    "code": "400100",
    "msg": "Invalid Parameter."
  }
```

代码 | 含义
---------- | -------
400 | Bad Request -- 请求格式不正确
401 | Unauthorized -- 无效API Key
403 | Forbidden -- 请求被禁止
404 | Not Found -- 找不到指定资源
405 | Method Not Allowed -- 您请求资源的方法不正确
415 | Content-Type -- 请求类型必须为application/json类型
429 | Too Many Requests -- 请求频率超出限制
500 | Internal Server Error -- 服务器出错，请稍后再试
503 | Service Unavailable -- 服务器维护中，请稍后再试



#### 系统错误码

代码 | 含义
---------- | -------
1015 | cloudflare frequency limit according to IP, block 30s--超频错误:基于ip的cloudflare限制,触发限制时间30s
40010 | Unavailable to place orders. Your identity information/IP/phone number shows you're at a country/region that is restricted from this service. -- 您当前属于限制国家，委托无法生效。
100001 | There are invalid parameters -- 无效参数
100002 | systemConfigError -- 系统配置有误
100003 | Contract parameter invalid -- 没有此合约
100004 | Order is in not cancelable state -- 订单不存在或者订单不可撤销
100005 | contractRiskLimitNotExist -- 合约风险限额不存在
200001 | The query scope for Level 2 cannot exceed xxx -- level2查询范围必须小于等于xxx　
200002 | Too many requests in a short period of time, please retry later--超频错误:基于接口业务层面的限制, 触发限制时间10s
200002 | The query scope for Level 3 cannot exceed xxx -- level3查询范围必须小于等于xxx
200003 | The symbol parameter is invalid. -- 参数symbol无效
300000 | request parameter illegal -- 请求参数非法
300001 | Active order quantity limit exceeded (limit: xxx, current: xxx) -- 超过活动委托数量限制，最大可提交 xxx 个活动委托，当前活动委托数: xxx
300002 | Order placement/cancellation suspended, please try again later. -- 系统暂停下单/撤单操作，将自动恢复，请稍后再试
300003 | Balance not enough, please first deposit at least 2 USDT before you start the battle -- 可用余额不足，委托成本为 xxx
300004 | Stop order quantity limit exceeded (limit: xxx, current: xxx) -- 超过条件委托数量限制，最大可提交 xxx 个条件委托，当前条件委托数: xxx
300005 | xxx risk limit exceeded -- 委托会导致超过最大风险限额 xxx
300006 | The close price shall be greater than the bankruptcy price. Current bankruptcy price: xxx. -- 平仓委托价格不能劣于仓位的破产价格，当前破产价格：xxx
300007 | priceWorseThanLiquidationPrice -- 加仓委托价格不能劣于仓位的强平价格，当前强平价格：xxx
300008 | Unavailable to place the order, there's no contra order in the market. -- 市场无订单，市价单提交失败
300009 | Current position size: 0, unable to close the position. -- 当前仓位为0，无法平仓
300010 | Failed to close the position -- 平仓失败
300011 | Order price cannot be higher than xxx -- 委托价格不能高于xxx
300012 | Order price cannot be lower than xxx -- 委托价格不能低于xxx
300013 | Unable to proceed the operation, there's no contra order in order book. -- 委托订单没有合法可撮合价格，订单提交失败
300014 | The position is being liquidated, unable to place/cancel the order. Please try again later. -- 强制平仓中，系统暂停下单/撤单，将自动恢复，请稍后重试
300015 | The order placing/cancellation is currently not available. The Contract/Funding is under the settlement process. When the process is completed, the function will be restored automatically. Please wait patiently and try again later. -- 合约交割/资金费用结算中，系统暂停下单/撤单，将自动恢复，请稍后重试
300016 | The leverage cannot be greater than xxx. -- 杠杆数不能大于xxx
300017 | Unavailable to proceed the operation, this position is for Futures Brawl -- 该仓位为乱斗仓位，不支持操作
300018 | clientOid parameter repeated -- clientOid参数重复
400001 | Any of KC-API-KEY, KC-API-SIGN, KC-API-TIMESTAMP, KC-API-PASSPHRASE is missing in your request header -- 请求头中缺少KC-API-KEY、KC-API-SIGN、KC-API-TIMESTAMP和KC-API-PASSPHRASE参数
400002 | KC-API-TIMESTAMP Invalid -- 请求时间与服务器时差超过5秒
400003 | KC-API-KEY not exists -- API-KEY不存在
400004 | KC-API-PASSPHRASE error -- API-PASSPHRAE不正确
400005 | Signature error -- 签名错误，请检查您的签名
400006 | The requested ip address is not in the api whitelist -- 请求IP不在API白名单中
400007 | Access Denied -- API权限不足，无法访问该URI目标地址。
400100 | Parameter Error -- 请求参数不合法
404000 | Url Not Found -- 找不到请求资源
411100 | User are frozen -- 用户已被冻结，请联系帮助中心
429000 | Too Many Requests -- 超频错误:基于接口的全站流量限制，可以直接重试请求
500000 | Internal Server Error -- 服务器出错，请稍后再试

系统返回的*HTTP状态码*不是`200`时，接口返回会显示错误码。调用成功时，系统将返回`code`和`data`字段；调用失败时，系统将返回`code`和`msg`字段。您可根据返回的参数消息排查错误。



### 成功返回

当系统返回*HTTP状态码*`200`和*系统代码*`200000`时，表示响应成功，返回结果如下：

```json
  {
    "code": "200000",
  }
```


### 分页器

KuCoin Futures 使用了**Pagination**和**HasMore**两种分页器，来返回数组的REST请求。


#### Pagination
```json
  {
      "currentPage": 1,
      "pageSize": 50,
      "totalNum": 6,
      "totalPage": 1,
      "data": ...
  }
```

**Pagination**允许使用当前页数获取结果，非常适用于获取实时数据。如/api/v1/deposit-list、/api/v1/orders及/api/v1/fills端点均默认返回第一页结果。如需获取更多数据，请根据当前返回的数据指定其他分页，然后再进行请求。

**示例** 
GET /api/v1/orders?currentPage=1&pageSize=50

**参数**

参数 | 默认值 | 含义
---------- | ------- | ------
currentPage | 1 | 当前页码
pageSize | 50 | 每页数据条数



#### HasMore
HasMore分页器采用滑动窗口机制，通过在一个数据流上滑动固定大小的窗口以获取分页数据，返回的数据中有一个字段HasMore，标志是否还有更多的数据。HasMore分页器每次滑动的时间相等，性能高效，非常适合于流式实时数据的查询。


**参数**

| 参数 | 数据类型    | 含义                                |
| --------- | ------- | --------------- |
| offset    | -       | 起始偏移量，一般使用上个请求最后一条返回结果的唯一属性，默认返回第一页
| forward   | boolean | 滑动方向，TRUE查询大于offset方向的数据      
| maxCount  | int     | 每次滑动的最大数据条数                    

**示例**
`GET /api/v1/interest/query?symbol=.XBTINT&offset=1558079160000&forward=true&maxCount=10`

## 类型说明 
### 时间戳 
API中的所有时间戳以Unix时间戳毫秒为单位返回（如：`1558079160000`）

## 接口认证
### 创建API KEY
通过接口进行请求前，您需在Web端创建API-KEY。创建成功后，您需妥善保管好以下三条信息：


* `Key`
* `Secret`
* `Passphrase`

`Key`和`Secret`由KuCoin Futures随机生成并提供，`Passphrase`是您在创建API时使用的密码。以上信息若遗失将无法恢复，需要重新申请API KEY。


### 权限

您可在[KuCoin Futures](https://futures.kucoin.com) Web端管理API权限。API权限分为以下几类：


* `通用权限` - 开启后，API可使用大部分GET请求来获取数据。
* `交易权限` - 开启后，可通过API进行下单/撤单和管理仓位。
* `转账权限` - 开启后，可通过API进行提现。需注意，开启此权限后，不需要两步验证，即可进行转账。


### 创建请求

所有REST参数的请求头需包含以下内容：

* `KC-API-KEY` API KEY是字符串类型。
* `KC-API-SIGN` 签名（请查看“消息签名”，了解更多详情）
* `KC-API-TIMESTAMP` 请求的时间戳
* `KC-API-PASSPHRASE` 创建API时填写的API密码
* `KC-API-KEY-VERSION` API-KEY版本号，可通过[API管理](https://futures.kucoin.com/api)页面查看版本号


### 消息签名

```php
    <?php
    class API {
        public function __construct($key, $secret, $passphrase) {
          $this->key = $key;
          $this->secret = $secret;
          $this->passphrase = $passphrase;
        }

        public function signature($request_path = '', $body = '', $timestamp = false, $method = 'GET') {
          $body = is_array($body) ? json_encode($body) : $body; // Body must be in json format 
          $timestamp = $timestamp ? $timestamp : time() * 1000;
          $what = $timestamp . $method . $request_path . $body;
          return base64_encode(hash_hmac("sha256", $what, $this->secret, true));
        }
    }
    ?> 
```

```python
    #Example for get user position in python
    api_key = "api_key"
    api_secret = "api_secret"
    api_passphrase = "api_passphrase"
    url = 'https://api-futures.kucoin.com/api/v2/symbol-position?symbol=BTCUSDTM'
    now = int(time.time() * 1000)
    str_to_sign = str(now) + 'GET' + '/api/v2/symbol-position?symbol=BTCUSDTM'
    signature = base64.b64encode(
        hmac.new(api_secret.encode('utf-8'), str_to_sign.encode('utf-8'), hashlib.sha256).digest())
    passphrase = base64.b64encode(hmac.new(api_secret.encode('utf-8'), api_passphrase.encode('utf-8'), hashlib.sha256).digest())    
    headers = {
        "KC-API-SIGN": signature,
        "KC-API-TIMESTAMP": str(now),
        "KC-API-KEY": api_key,
        "KC-API-PASSPHRASE": passphrase
        "KC-API-KEY-VERSION": "2"
    }
    response = requests.request('get', url, headers=headers)
    print(response.status_code)
    print(response.json())
    
    #Example for create deposit addresses in python
    url = 'https://api-futures.kucoin.com/api/v2/order'
    now = int(time.time() * 1000)
    data = {"symbol": "BTCUSDTM", "side": "BUY", "type": "LIMIT", "price": 1, "size": 1}
    data_json = json.dumps(data)
    str_to_sign = str(now) + 'POST' + '/api/v2/order' + data_json
    signature = base64.b64encode(
        hmac.new(api_secret.encode('utf-8'), str_to_sign.encode('utf-8'), hashlib.sha256).digest())
    passphrase = base64.b64encode(hmac.new(api_secret.encode('utf-8'), api_passphrase.encode('utf-8'), hashlib.sha256).digest())
    headers = {
        "KC-API-SIGN": signature,
        "KC-API-TIMESTAMP": str(now),
        "KC-API-KEY": api_key,
        "KC-API-PASSPHRASE": passphrase,
        "KC-API-KEY-VERSION": "2",
        "Content-Type": "application/json" # specifying content type or using json=data in request
    }
    response = requests.request('post', url, headers=headers, data=data_json)
    print(response.status_code)
    print(response.json())
```
#### 请求头中的 `KC-API-SIGN`
    1. 使用 API的`Secret` 对`timestamp + method+ endpoint + body`拼接的字符串进行`HMAC-sha256`加密（`+`表示字符串连接）。
    1. 再将加密内容使用 `base64` 编码。              

#### 请求头中的`KC-API-PASSPHRASE`
* 对于V1版的API-KEY，请使用明文传递
* 对于V2版的API-KEY，需要将`KC-API-KEY-VERSION`指定为`2`，并将passphrase使用API的Secret进行`HMAC-sha256`加密，再将加密内容通过`base64`编码后传递

注意：

* 加密的 `timestamp` 需要和请求头中的`KC-API-TIMESTAMP`保持一致
* 进行加密的`body`需要和`Request Body`中的内容保持一致
* 请求方法需要大写
* 对于 `GET`, `DELETE` 请求，endpoint 需要包含请求的参数（`/api/v1/deposit-address?currency=XBT`）。如果没有请求体（通常用于GET请求），请使用空字符串“”表示请求体


#### 请求头中的`KC-API-TIMESTAMP``:
请求头的`KC-API-TIMESTAMP`必须为Unix UTC时间，需精确到毫秒（如：`1547015186532`）。
服务器请求的时间戳与API服务器时差必须控制在`5`秒以内，否则请求会因过期而被服务器拒绝。如果服务器与API服务器之间存在时间偏差，请使用平台提供的服务器时间接口，获取API服务器的时间。

```python
#Example for Create Deposit Address

curl -H "Content-Type:application/json" -H "KC-API-KEY:5c2db93503aa674c74a31734" -H "KC-API-TIMESTAMP:1547015186532" -H "KC-API-PASSPHRASE:QWIxMjM0NTY3OCkoKiZeJSQjQA==" -H "KC-API-SIGN:7QP/oM0ykidMdrfNEUmng8eZjg/ZvPafjIqmxiVfYu4=" -H "KC-API-KEY-VERSION:2" 
-X POST -d '{"currency":"XBT"}' http://api-futures.kucoin.com/api/v1/deposit-address

KC-API-KEY = 5c2db93503aa674c74a31734
KC-API-SECRET = f03a5284-5c39-4aaa-9b20-dea10bdcf8e3
KC-API-PASSPHRASE = QWIxMjM0NTY3OCkoKiZeJSQjQA==
KC-API-KEY-VERSION = 2
TIMESTAMP = 1547015186532
METHOD = POST
ENDPOINT = /api/v1/deposit-address
STRING-TO-SIGN = 1547015186532POST/api/v1/deposit-address{"currency":"XBT"}
KC-API-SIGN = 7QP/oM0ykidMdrfNEUmng8eZjg/ZvPafjIqmxiVfYu4=
```

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

----------


## 枚举定义

* 买卖方向(`side`):
    - `BUY` 买/做多
    - `SELL` 卖/做空
</br></br>
* 订单类型(`orderType`, `type`):
    - `LIMIT` 限价单
    - `MARKET` 市价单
    - `STOP` 限价止损
    - `TAKE_PROFIT` 限价止盈
    - `STOP_MARKET` 市价止损
    - `TAKE_PROFIT_MARKET`  市价止盈
</br></br>
* 下单类型(`placeType`):
    - `DEFAULT`	默认
    - `OTOCO` 订单止盈止损
    - `STEP_REDUCE_POSITION`	阶梯减仓
    - `LIQUIDATION_TAKE_OVER`	强平接管
    - `ADL_TRIGGER`	ADL触发用户
    - `ADL_LUCKY`	ADL幸运用户
</br></br>
* 触发价格方式(`workingType`):
    - `MP`	标记价格
    - `TP`	最新成交价
</br></br>
* 有效方式(`timeInForce`):
    - `GTC` 用户主动取消才过期
    - `IOC` 立即成交可成交的部分，然后取消剩余部分，不进入买卖盘
    - `FOK` 如果下单不能全部成交，则取消
</br></br>
* 资金记录类型(`type`):
    - `CONSUME`-消费,
    - `FUNDING`-资金费用,
    - `FEE`-手续费,
    - `REALIZED_PNL`-已实现盈亏,
    - `TRANSFER_IN`-转入,
    - `TRANSFER_OUT`-转出,
    - `SON_MOTHER_ACCOUNT_TRANSFER`-合约子母账号划转,
    - `TRIAL_RECYCLE`-体验金回收,
    - `REWARD`-发奖,
    - `DEDUCTION_REFUND`-抵扣返还;
</br></br>
* 付款账户类型(`payAccountType`):
    - `MAIN`-储蓄账户
    - `TRADE`-交易账户
</br></br>
* 接收账户类型(`recAccountType`):
    - `MAIN`-储蓄账户
    - `TRADE`-币币账户
</br></br>
* 划转状态(`status`):
    - `APPLY`-待处理
    - `PROCESSING`-处理中
    - `PENDING_APPROVAL`-待审核
    - `APPROVED`-审核通过
    - `REJECTED`-审核拒绝
    - `PENDING_CANCEL`-待退款
    - `CANCEL`-取消
    - `SUCCESS`-成功
</br></br>
* 转出状态(`status`):
    - `PROCESSING`-处理中
    - `SUCCESS`-成功
    - `FAILURE`-失败
</br></br>
* 保证金模式(`marginType`):
    - `ISOLATED`-逐仓
    - `CROSSED`-全仓
</br></br>
* 仓位方向(`side`):
    - `BOTH`-单向持仓
    - `LONG`-做多方向
    - `SHORT`-做空方向
</br></br>
* 平仓类型(`type`):
    - `CLOSE_LONG`-手动平多
    - `CLOSE_SHORT`-手动平多
    - `LIQUID_LONG`-强制平多
    - `LIQUID_SHORT`-强制平空
    - `ADL_LONG`-adl平多
    - `ADL_SHORT`-adl平空
</br></br>
* 合约类型(`type`):
    - `FFWCSX`-永续
    - `FFICSX`-交割
</br></br>
* 合约状态(`status`):
  - `Open`-已上线
  - `PrepareSettled`-准备结算
  - `BeingSettled`-结算中
  - `Paused`-已暂停
  - `CancelOnly`只能撤单

---
# 用户
此部分需进行签名验证。

# 用户配置

## 修改用户自动追加保证金状态
```json
{
  "code": "200000",
  "data": true
} 
```
### HTTP请求
`POST /api/v2/user-config/change-auto-deposit`
### API权限
该接口需要`交易权限`
### 参数
参数 | 数据类型 | 是否必须 | 含义 |  
--------- | ------- | -----------| -----------|
symbol | String | 是 | 合约symbol
autoDeposit | Boolean | 是 | 是否开启自动追加保证金（设置为`true`时，达到强平价格时会尝试自动追加保证金）
## 获取用户全局杠杆
```json
{
  "code": "200000",
  "data": {
    "symbol": "ETHUSDTM",
    "leverage": "5",
    "maxRiskLimit": "10000000"
  }
}
```
### HTTP请求
`GET /api/v2/user-config/leverage`
### API权限
该接口需要`通用权限`
### 参数
参数 | 数据类型 | 是否必须 | 含义 |  
--------- | ------- | -----------| -----------|
symbol | String | 是 | 合约symbol
### 返回值
属性  | 含义 |  
--------- | -----------|
symbol | 合约symbol | 
leverage | 用户当前杠杆 | 
maxRiskLimit | 该杠杆下持仓最大限额 | 

## 获取用户全局杠杆(所有合约)
```json
{
  "code": "200000",
  "data": [
    {
    "symbol": "ETHUSDTM",
    "leverage": "5",
    "maxRiskLimit": "10000000"
    },
    {
    "symbol": "BTCUSDTM",
    "leverage": "5",
    "maxRiskLimit": "20000000"
    }
  ]
}
```
### HTTP请求
`GET /api/v2/user-config/leverages`
### API权限
该接口需要`通用权限`
### 参数
`无`
### 返回值
属性 | 含义 |
--------- | -----------|
symbol | 合约symbol |
leverage | 用户当前杠杆 |
maxRiskLimit | 该杠杆下持仓最大限额 |

## 修改用户全局杠杆
```json
{
  "code": "200000",
  "data": {
    "symbol": "ETHUSDTM",
    "leverage": "5",
    "maxRiskLimit": "10000000"
  }
}
```
### HTTP请求
`POST /api/v2/user-config/adjust-leverage`
### API权限
该接口需要`交易权限`
### 参数
参数 | 数据类型 | 是否必须 | 含义 |  
--------- | ------- | -----------| -----------|
symbol | String | 是 | 合约symbol
leverage | Integer | 是 | 杠杆（正数）|
### 返回值
属性  | 含义 |  
--------- | -----------|
symbol | 合约symbol | 
leverage | 用户当前杠杆 | 
maxRiskLimit | 该杠杆下持仓最大限额 | 



# 账户

## 查询子账户列表
```json
{
    "code": "200", //code为200代表成功，否则代表失败
    "msg": "",
    "retry": true,
    "success": true,
    "data": [
        {
            "userId": "620635a64a6f480001123ffa", //子账号id
            "subName": "William" //子账号名称
        }，
    {
            "userId": "620635a64a6f48004353461",
            "subName": "David"
        }
    ]
}
```
### HTTP请求
`GET /api/v2/sub-accounts`
### API权限
该接口需要`通用权限`
### 频率限制
此接口针对每个账号请求频率限制为`30次/3s`
### 参数
`无`
### 返回值
字段|含义
---|---
userId|子账号id
subName|子账号名称


## 获取账户概览
```json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": [
        {
            "currency": "USDT", //币种
            "walletBalance": "21098384.5602084900", //钱包余额
            "positionMargin": "310.7995729500", //仓位保证金
            "orderMargin": "7177.2558034300", //订单保证金
            "availableBalance": "21090896.5048321100", //可用余额
            "unrealisedPNL": "2.2572000000", //仓位未实现盈亏
            "accountEquity": "21098386.8174084900", //该币种账户总权益 = 钱包余额 + 仓位未实现盈亏
            "holdBalance": "0.0000000000", //划转冻结金额
            "availableTransferBalance": "21090896.5048321100" //可转出金额
        },
        {
            "currency": "ETH",
            "walletBalance": "100.0000000000",
            "positionMargin": "0.0000000000",
            "orderMargin": "0.0000000000",
            "availableBalance": "100.0000000000",
            "unrealisedPNL": "0",
            "accountEquity": "100.0000000000",
            "holdBalance": "0.0000000000",
            "availableTransferBalance": "100.0000000000"
        },
        {
            "currency": "XRP",
            "walletBalance": "10000.0000000000",
            "positionMargin": "0.0000000000",
            "orderMargin": "0.0000000000",
            "availableBalance": "10000.0000000000",
            "unrealisedPNL": "0",
            "accountEquity": "10000.0000000000",
            "holdBalance": "0.0000000000",
            "availableTransferBalance": "10000.0000000000"
        }
    ]
}
```
### HTTP请求
`GET /api/v2/account-overview`
### API权限
该接口需要`通用权限`
### 频率限制
此接口针对每个账号请求频率限制为`30次/3s`
### 参数  
参数|数据类型|是否必需|含义
---|---|---|---
currency|String|NO|币种 BTC、USDT、ETH、XRP、DOT,不传则查询所有币种
### 返回值  
字段|含义
---|---
currency|币种
walletBalance|钱包余额
positionMargin|仓位保证金
orderMargin|订单保证金
availableBalance|可用余额
unrealisedPNL|未实现盈亏
accountEquity|合约账户总权益。`合约账户总权益` = `钱包余额` + `未实现盈亏`
holdBalance|转出冻结
availableTransferBalance|可转金额


## 查询资金记录
```json
{
    "success": true,
    "code": "200", //200代表成功，其他代表失败
    "msg": "success",
    "retry": false,
    "data": [
        {
            "time": 1650348854000, //业务发生时间
            "currency": "USDT", //币种
            "type": "FEE", //业务类型
            "amount": "-0.00408000", //交易金额
            "walletBalance": "99999.70744444", //钱包余额
            "remark": "ETHUSDTM" //交易说明
        },
        {
            "time": 1650348854000,
            "currency": "USDT",
            "type": "REALIZED_PNL",
            "amount": "0.00408000",
            "walletBalance": "0.55080000",
            "remark": "ETHUSDTM"
        },
        {
            "time": 1650538206000,
            "currency": "USDT",
            "type": "FEE",
            "amount": "-0.54400000",
            "walletBalance": "100000.54400000",
            "remark": "ETHUSDTM"
        }
    ]
}
```
### HTTP请求
`GET /api/v2/transaction-history`
#### API权限
该接口需要`通用权限`
#### 频率限制
此接口针对每个账号请求频率限制为`30次/3s`
### 参数  
参数|数据类型|是否必需|含义
---|---|---|---
limit|Integer|NO|记录数，默认`100`，最大`1000`
currency|String|NO|币种
type|String|NO|流水类型
startAt|Long|NO|开始时间
endAt|Long|NO|结束时间  

<aside class="notice">
如果未传<code>startAt</code>和<code>endAt</code>,默认返回最近7天数据。
<code>startAt</code>和<code>endAt</code>的间隔时间不能超过<code>3</code>个月
</aside>

* 流水类型(`type`)说明：
    * `CONSUME`-消费,
    * `FUNDING`-资金费用,
    * `FEE`-手续费,
    * `REALIZED_PNL`-已实现盈亏,
    * `TRANSFER_IN`-转入,
    * `TRANSFER_OUT`-转出,
    * `SON_MOTHER_ACCOUNT_TRANSFER`-合约子母账号划转,
    * `TRIAL_RECYCLE`-体验金回收,
    * `REWARD`-发奖,  
    * `DEDUCTION_REFUND`-抵扣返还,
    * `INSURANCE_CROSS_POSITION_PAY`-保险基金穿仓支付;



### 返回值  
字段|含义
---|---
time|业务发生时间
currency|币种
type|类型
amount|交易金额
walletBalance|钱包余额
remark|说明



# 划转
## 子母账号之间划转
``` json
{
    "code": "200", //code为200代表划转成功，否则代表失败
    "msg": "",
    "retry": true,
    "success": true,
}
```
### HTTP请求
`POST api/v2/sub-transfer`
### API权限
该接口需要`交易权限`
### 频率限制
此接口针对每个账号请求频率限制为`30次/3s `
### 参数
参数|数据类型|是否必需|含义
---|---|---|---
amount | Number | YES | 划转金额
currency | String | YES | 币种
subUserId | String | YES | 子账户用户id
direction | String | YES | 资金划转方向：`OUT`-母账户划转给子账户,`IN`-子账户划转给母账户
accountType | String | YES | 主用户账户类型：`MAIN`-储蓄账户,`TRADE`-交易账户,`CONTRACT`-合约账户
subAccountType | String | YES | 子用户账户类型：`MAIN`-储蓄账户,`TRADE`-交易账户,`CONTRACT`-合约账户
### 返回值
`code`为`200`代表划转成功，否则代表失败

## 转出到KuCoin储蓄/币币账户
``` json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": {
        "applyId": "30626177500585984", //转出申请id，可以使用该id撤回申请
        "bizNo": "30626172928794624", //业务唯一编码
        "payAccountType": "CONTRACT", //付款账户类型
        "payTag": "DEFAULT", //付款账户子类型
        "remark": "", //用户备注
        "recAccountType": "MAIN", //收款账户类型
        "recTag": "DEFAULT", //收款账户子类型
        "recRemark": "", //收款账户流水备注
        "recSystem": "KUCOIN", //收款服务方
        "status": "APPLY", //状态
        "currency": "USDT", //币种
        "amount": "22.0000000000", //划转金额
        "fee": "0.0000000000", //划转手续费
        "sn": 30626177504780288, //唯一序列号
        "reason": "", //失败原因
        "createdAt": 1650773821000, //创建时间
        "updatedAt": 1650773822000 //更新时间
    }
}
```
### HTTP请求
`POST /api/v2/account/transfer-out`
### API权限
该接口需要`交易权限`
### 频率限制
此接口针对每个账号请求频率限制为`30次/3s `
### 参数  
参数|数据类型|是否必需|含义
---|---|---|---
amount|Number|YES|划转金额
currency|String|YES|币种，如USDT、BTC
recAccountType|String|YES|接收账户类型，只能是`MAIN`-储蓄账户、`TRADE`-币币账户
### 返回值  
字段|含义
---|---
applyId|转出申请id，可以使用该id撤回申请
bizNo|业务主键id
payAccountType|付款账户类型
payTag|付款账户子类型
remark|用户备注
recAccountType|收款账户类型
recTag|收款账户子类型
recRemark|收款账户流水备注
recSystem|收款服务方
status|状态:`APPLY`-待处理,`PROCESSING`-处理中,`PENDING_APPROVAL`-待审核,`APPROVED`-审核通过,`REJECTED`-审核拒绝,`PENDING_CANCEL`-待退款,`CANCEL`-取消,`SUCCESS`-成功
currency|币种
amount|划转金额
fee|划转手续费
sn|序列号
reason|失败原因
createdAt|创建时间
updatedAt|更新时间



## 查询转出申请记录
``` json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": [
        {
            "applyId": "30626177500585984", //申请id
            "currency": "USDT", //币种
            "recRemark": "", //收款账户流水备注
            "recSystem": "KUCOIN", //收款服务方
            "status": "PROCESSING", //状态
            "amount": "22.0000000000", //划转金额
            "reason": "", //失败原因
            "sn": 30626177504780288, //唯一序列号
            "createdAt": 1650773821000, //业务发生时间
            "remark": "" //用户备注
        },
        {
            "applyId": "8djl4jt07pc",
            "currency": "USDT",
            "recRemark": "",
            "recSystem": "KUCOIN",
            "status": "PROCESSING",
            "amount": "22.0000000000",
            "reason": "",
            "sn": 30624803597586433,
            "createdAt": 1650773493000,
            "remark": ""
        }
    ]
}
```
### HTTP请求
`GET /api/v2/transfer-list`
### API权限
该接口需要`通用权限`
### 频率限制
此接口针对每个账号请求频率限制为`30次/3s`
### 参数  
参数|数据类型|是否必需|含义
---|---|---|---
startAt|Long|NO|业务发生起始时间
endAt|Long|NO|业务发生截止时间
limit|Integer|NO|记录数，默认`100`，最大`1000`
currency|String|NO|币种
status|String|NO|状态，`PROCESSING`-处理中，`SUCCESS`-成功, `FAILURE`-失败

<aside class="notice">
如果未传<code>startAt</code>和<code>endAt</code>,默认返回最近7天数据。
<code>startAt</code>和<code>endAt</code>的间隔时间不能超过<code>3</code>个月
</aside>

### 返回值
字段|含义
---|---
applyId|转出申请Id
currency|币种
recRemark|收款账户流水备注
recSystem|收款服务方
status|状态
amount|转出金额
reason|失败原因
sn|序列号
createdAt|时间
remark|付款账户备注

## 资金转入合约账户
``` json
{
    "code": "200", //code为200代表转入成功，否则代表失败
    "msg": "",
    "retry": true,
    "success": true
}
```
### HTTP请求
`POST /api/v2/transfer-in`
### API权限
该接口需要`交易权限`
### 频率限制
此接口针对每个账号请求频率限制为`30次/3s`
### 参数  
参数|数据类型|是否必需|含义
---|---|---|---
amount|Number|YES|交易金额
currency|String|YES|币种
payAccountType|String|YES|付款账户类型：只能是`MAIN`-储蓄账户，`TRADE`-交易账户
### 返回值
当`code`为`200`代表成功，其他代表失败


---
# 交易
此部分需进行签名验证。

# 订单

## 下单
```json
//response
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": {
        "orderId": "25880469442662400"
    }
}
```
### HTTP请求
`POST /api/v2/order`
### API权限
该接口需要`交易权限`
### 参数
参数 | 数据类型 | 是否必须 | 含义 
--------- | ------- | -----------| -----------
 symbol       | STRING   | YES      | 交易对                                                       
 side         | ENUM     | YES      | 买卖方向 `SELL`, `BUY`                                           
 price        | DECIMAL  | NO       | 价格                                                         
 type         | ENUM     | YES      | 订单类型 `LIMIT`, `MARKET`, `STOP`, `TAKE_PROFIT`, `STOP_MARKET`, `TAKE_PROFIT_MARKET`
 size         | INT      | NO       | 下单数量,使用`closeOrder`不支持此参数                          
 clientOid    | STRING   | NO       | 唯一的订单ID，可用于识别订单。如：UUID,只能包含数字、字母、下划线（_）或 分隔线（-） 
 reduceOnly   | BOOLEAN  | NO       | [可选] 只减仓标记, 默认值是 `false` 。值为`true`时，需要设置平仓数量 
 closeOrder   | BOOLEAN  | NO       | [可选] 平仓单标记, 默认值是 `false` 。值为`true`时，代表仓位全平 
 hidden       | BOOLEAN  | NO       | [可选] 订单不会在买卖盘中展示。选择`hidden`，不允许选择`postOnly`。 
 visibleSize  | INT      | NO       | [可选] 隐藏单最大可展示的数量。                              
 stopPrice    | DECIMAL  | NO       | 触发价, 仅 `STOP`, `STOP_MARKET`, `TAKE_PROFIT`, `TAKE_PROFIT_MARKET` 需要此参数 
 postOnly     | BOOLEAN  | NO       | [可选] 只挂单的标识。选择`postOnly`，不允许选择`hidden`。当订单时效为`IOC`策略时，该参数无效。 
 workingType  | STRING   | NO       | [可选] 止损单触发价类型，包括`TP`和`MP`                          
 timeInForce  | STRING   | NO       | 有效方法                                                     
 clientTimestamp| TIMESTAMP | NO | [可选]客户端下单时间
 allowMaxTimeWindow| INT | NO | [可选]允许服务器接收到请求时间与`clientTimestamp`最大时间间隔

根据下单`type`的不同， 对应需要的参数如下：

| Type                               | 参数                          |
| ---------------------------------- | ---------------------------- |
| `LIMIT`                            | `size`, `price`              |
| `MARKET`                           | `size`                       |
| `STOP`,`TAKE_PROFIT`               | `size`, `price`, `stopPrice `|
| `STOP_MARKET`,`TAKE_PROFIT_MARKET` | `size`, `stopPrice`          |

* 条件单触发说明：
    * `STOP`, `STOP_MARKET` 止损单：
        * 买入: 最新合约价格/标记价格高于等于触发价`stopPrice`
        * 卖出: 最新合约价格/标记价格低于等于触发价`stopPrice`
    * `TAKE_PROFIT`, `TAKE_PROFIT_MARKET` 止盈单：
        * 买入: 最新合约价格/标记价格低于等于触发价`stopPrice`
        * 卖出: 最新合约价格/标记价格高于等于触发价`stopPrice`
<br/><br/>
* `clientTimestamp`和`allowMaxTimeWindow`参数说明：当这两个参数`同时`传递才会生效
      * 验证逻辑：
          * 1. 服务器接收到请求时间: `receiveTime`
          * 2. 服务器接收到请求时间(`receiveTime`)与客户端下单时间(`clientTimestamp`)间隔:`receiveTime` - `clientTimestamp` = `timeWindow`
          * 3. 当`clientTimestamp` <= `receiveTime`同时`timeWindow` <= `allowMaxTimeWindow`时，下单请求才会被接受,否则下单失败返回错误码`300019`
<br/><br/>

### 返回值
属性  | 含义 
--------- | -----------
orderId | 订单id 

<aside class="notice">
如果下单接口返回信息为<code>{"success":false,"code":"100000","msg":"error.timeOut","retry":false,"data":null}</code>代表下单超时，这种情形需要通过指定<code>clientOid</code>参数查询<code>GET /api/v2/order/detail</code>接口，来确定下单请求是否成功
</aside>

## 单个撤单
```json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": {
        "orderId": "51711797534732288"
    }
}
```

### HTTP请求
`DELETE /api/v2/order`
### API权限
该接口需要`交易权限`
### 参数
参数 | 数据类型 | 是否必须 | 含义 
--------- | ------- | -----------| -----------
symbol | STRING | YES | 合约symbol
orderId | STRING | NO | 订单id 
clientOid | STRING | NO | 用户自定义orderId 

<aside class="notice">
<code>orderId</code>和<code>clientOid</code>二选一，如果都传，默认使用<code>orderId</code>.
</aside>



## 批量撤单
```json
{
	"success": true,
	"code": "200",
	"msg": "success",
	"retry": false,
	"data": {
		"orderIds": [
			"24521602255290368",
			"24522071262363648",
			"24522186836410368"
		]
	}
}
```
### HTTP请求
`DELETE /api/v2/orders`
### API权限
该接口需要`交易权限`
### 参数
参数 | 数据类型 | 是否必须 | 含义 
--------- | ------- | -----------| -----------
symbol | STRING | YES | 合约symbol
### 返回值
属性  | 含义 
--------- | -----------
orderIds | 成功撤掉的订单id 


## 查询成交记录
```json
{
      "success": true,
      "code": "200",
      "msg": "success",
      "retry": false,
      "data": [
          {
              "id": "24074404942053376",
              "orderId": "23518930911887360",
              "symbol": "ETHUSDTM",
              "time": "1649211785419",
              "side": "BUY",
              "size": 1,
              "price": "3895.0",
              "fee": "0.00779",
              "feeRate": "0.00020",
              "placeType": "DEFAULT",
              "orderType": "LIMIT",
              "feeCurrency": "USDT",
              "pnl": "0.0",
              "pnlCurrency": "USDT",
              "value": "38.95",
              "maker": true,
              "forceTaker": "false"
          }
      ]
  }
```
### HTTP请求
`GET /api/v2/orders/historical-trades`
### API权限
该接口需要`通用权限`
### 参数
参数 | 数据类型 | 是否必须 | 含义 
--------- | ------- | -----------| -----------
| symbol | String | YES | 合约symbol	|
| startAt | Long  | NO | 开始时间（毫秒）	|
| endAt | Long | NO | 截止时间（毫秒）	|
| limit | Integer | NO | 默认 `50`; 最大 `1000`.	|
| fromId | Long | NO |从哪一条成交id开始返回. 缺省返回最近的成交记录	|
<aside class="notice">
如果未传<code>startAt</code>和<code>endAt</code>,默认返回最近7天数据。
<code>startAt</code>和<code>endAt</code>的间隔时间不能超过<code>3</code>个月
</aside>
### 返回值
| 字段   | 含义   |
| ------ | ------ |
| id | 交易记录ID |
| orderId | 订单ID |
| symbol | 合约交易对名字 |
| time | 撮合时间 |
| side | 方向：`BUY`:买/做多 `SELL`:卖/做空 |
| size | 数量 |
| price | 价格 |
| fee | 手续费 |
| feeRate | 手续费率 |
| feeCurrency | 手续费币种 |
| pnl | 盈亏 |
| pnlCurrency | 盈亏币种 |
| value | 价值 |
| orderType | 订单类型 |
| placeType | 下单类型：`DEFAULT`、`OTOCO`、`STEP_REDUCE_POSITION`、`LIQUIDATION_TAKE_OVER`、`ADL_TRIGGER`、`ADL_LUCKY` |
| maker | 是否maker |
| forceTaker | 是否强制作为taker处理|


## 查询单个订单详情
``` json
{
    "code": "200000",
    "data": {
        "id": "49629987480838144",
        "symbol": "BTCUSDTM",
        "type": "LIMIT",
        "side": "BUY",
        "price": "1.0",
        "size": "1",
        "dealSize": "0",
        "dealValue": "0.0",
        "workingType": "",
        "stopPrice": null,
        "timeInForce": "GTC",
        "postOnly": false,
        "hidden": false,
        "leverage": 5,
        "closeOrder": false,
        "visibleSize": null,
        "remark": null,
        "orderTime": "1655304711124",
        "reduceOnly": false,
        "status": "FINISHED",
        "placeType": "DEFAULT",
        "takeProfitPrice": null,
        "cancelSize": 1,
        "clientOid": "122345"
    }
}
```
### HTTP请求
`GET /api/v2/order/detail`
### API权限
该接口需要`通用权限`
### 参数
参数 | 数据类型 | 是否必须 | 含义 
--------- | ------- | -----------| -----------
orderId | STRING | NO | 订单id 
clientOid | STRING | NO | 用户自定义orderId 

<aside class="notice">
<code>orderId</code>和<code>clientOid</code>二选一，如果都传，默认使用<code>orderId</code>.
</aside>
<aside class="notice">
如果不是活跃委托订单则只能查询<code>1</code>个月以内的详情数据.
</aside>

### 返回值
| 字段   | 含义   |
| ------ | ------ |
| id | 订单ID |
| symbol | 合约名称 |
| type | 订单类型 |
| side | 方向：BUY:买/做多 SELL:卖/做空 |
| price | 订单价格 |
| size | 数量 |
| dealSize | 订单成交数量 |
| dealValue | 成交价值 |
| workingType | 触发价格方式：`MP`（标记价格）、`TP`（最新成交价） |
| stopPrice | 触发价格，订单止盈止损 止损价格 |
| timeInForce | `GTC`: 用户主动取消才过期, `IOC`:立即成交可成交的部分，然后取消剩余部分，不进入买卖盘； `FOK`：如果下单不能全部成交，则取消； |
| postOnly | 是否只做maker |
| hidden | 是否是隐藏单 |
| leverage | 杠杆 |
| closeOrder | 是否平仓单 |
| visibleSize | 隐藏单对应的访问数量 |
| remark | 订单说明 |
| orderTime | 下单时间 |
| reduceOnly | 是否只减仓 |
| status | 订单状态 |
| placeType | 下单类型：`DEFAULT`、`OTOCO`、`STEP_REDUCE_POSITION`、`LIQUIDATION_TAKE_OVER`、`ADL_TRIGGER、ADL_LUCKY` |
| takeProfitPrice| 订单止盈止损 止盈价格 |
| cancelSize | 取消数量 |
| clientOid	| 客户订单编号 |


## 查询活跃订单
``` json
{
    "code": "200000",
    "data": [
        {
            "id": "51812226666860544",
            "symbol": "BTCUSDTM",
            "type": "LIMIT",
            "side": "BUY",
            "price": "1.0000000000",
            "size": "10",
            "dealSize": "0",
            "dealValue": "0.0000000000",
            "workingType": null,
            "stopPrice": "0.0000000000",
            "timeInForce": "GTC",
            "postOnly": false,
            "hidden": false,
            "leverage": null,
            "closeOrder": false,
            "visibleSize": 0,
            "remark": null,
            "orderTime": "1655824997481",
            "reduceOnly": false,
            "status": "MATCHING",
            "placeType": "DEFAULT",
            "takeProfitPrice": "0.0000000000",
            "cancelSize": 0,
            "clientOid": "12e7fdc0f17611ec990facde48001122"
        }
    ]
}
``` 
### HTTP请求
`GET /api/v2/orders/active`
### API权限
该接口需要`通用权限`
### 参数
参数 | 数据类型 | 是否必须 | 含义 
--------- | ------- | -----------| -----------
| symbol | String | YES | 合约symbol	|

### 返回值
| 字段   | 含义   |
| ------ | ------ |
| id | 订单ID |
| symbol | 合约名称 |
| type | 订单类型 |
| side | 方向：`BUY`:买/做多 `SELL`:卖/做空 |
| price | 订单价格 |
| size | 数量 |
| dealSize | 订单成交数量 |
| dealValue | 成交价值 |
| workingType | 触发价格方式：`MP`（标记价格）、`TP`（最新成交价） |
| stopPrice | 触发价格，订单止盈止损 止损价格 |
| timeInForce | `GTC`: 用户主动取消才过期, `IOC`:立即成交可成交的部分，然后取消剩余部分，不进入买卖盘； `FOK`：如果下单不能全部成交，则取消； |
| postOnly | 是否只做maker |
| hidden | 是否是隐藏单 |
| leverage | 杠杆 |
| closeOrder | 是否平仓单 |
| visibleSize | 隐藏单对应的访问数量 |
| remark | 订单说明 |
| orderTime | 下单时间 |
| reduceOnly | 是否只减仓 |
| status | 订单状态 |
| placeType | 下单类型：`DEFAULT`、`OTOCO`、`STEP_REDUCE_POSITION`、`LIQUIDATION_TAKE_OVER`、`ADL_TRIGGER`、`ADL_LUCKY` |
| takeProfitPrice| 订单止盈止损 止盈价格 |
| cancelSize | 取消数量 |
| clientOid	| 客户订单编号 |



## 查询全部活跃订单
```json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "items": [
        {
            "id": "31744981194510336",
            "symbol": "LINKUSDTM",
            "size": 2020,
            "orderType": "LIMIT",
            "placeType": "DEFAULT",
            "side": "SELL",
            "price": "12.5930000000",
            "dealSize": 894,
            "reduceOnly": false,
            "workingType": null,
            "orderTime": "1651040593311",
            "stopPrice": "0.0000000000",
            "takeProfitPrice": "0.0000000000",
            "orderDisplayType": "LIMIT",
            "leverage": null,
            "timeInForce": "GTC",
            "hidden": false,
            "visibleSize": 0,
            "updatedAt": null,
            "status": "MATCHING",
            "cancelSize": 0,
            "dealValue": "1125.8142000000",
            "postOnly": false,
            "closeOrder": false,
            "fromReduce": false,
            "settlementCurrency": null,
            "createdAt": 1651040593311
        }
    ]
}
```
### HTTP请求
`GET /api/v2/orders/all-active`
### API权限
该接口需要`通用权限`
### 参数
`无`
### 返回值
| 字段   | 含义   |
| ------ | ------ |
| id | 订单ID |
| symbol | 合约名称 |
| type | 订单类型 |
| side | 方向：BUY:买/做多 SELL:卖/做空 |
| price | 订单价格 |
| size | 数量 |
| dealSize | 订单成交数量 |
| dealValue | 成交价值 |
| workingType | 触发价格方式：`MP`（标记价格）、`TP`（最新成交价） |
| stopPrice | 触发价格，订单止盈止损 止损价格 |
| timeInForce | `GTC`: 用户主动取消才过期, `IOC`:立即成交可成交的部分，然后取消剩余部分，不进入买卖盘； `FOK`：如果下单不能全部成交，则取消； |
| postOnly | 是否只做maker |
| hidden | 是否是隐藏单 |
| leverage | 杠杆 |
| closeOrder | 是否平仓单 |
| visibleSize | 隐藏单对应的访问数量 |
| remark | 订单说明 |
| orderTime | 下单时间 |
| reduceOnly | 是否只减仓 |
| status | 订单状态 |
| placeType | 下单类型：`DEFAULT`、`OTOCO`、`STEP_REDUCE_POSITION`、`LIQUIDATION_TAKE_OVER`、`ADL_TRIGGER`、`ADL_LUCKY` |
| takeProfitPrice| 订单止盈止损 止盈价格 |
| cancelSize | 取消数量 |
| clientOid	| 客户订单编号 |


## 查询历史订单
```json
{
    "code": "200000",
    "data": [
        {
            "id": "51813991558696960",
            "symbol": "BTCUSDTM",
            "type": "LIMIT",
            "side": "BUY",
            "price": "1.0000000000",
            "size": "10",
            "dealSize": "0",
            "dealValue": "0.0000000000",
            "workingType": null,
            "stopPrice": "0.0000000000",
            "timeInForce": "GTC",
            "postOnly": false,
            "hidden": false,
            "leverage": null,
            "closeOrder": false,
            "visibleSize": 0,
            "remark": null,
            "orderTime": "1655825418307",
            "reduceOnly": false,
            "status": "MATCHING",
            "placeType": "DEFAULT",
            "takeProfitPrice": "0.0000000000",
            "cancelSize": 0,
            "clientOid": "0dbb84ecf17711ec97fcacde48001122"
        }
    ]
}
```
### HTTP请求
`GET /api/v2/orders/history`
### API权限
该接口需要`通用权限`
### 参数
参数 | 数据类型 | 是否必须 | 含义 
--------- | ------- | -----------| -----------
| symbol | String | YES | 合约symbol	|
| startAt | Long  | NO | 开始时间（毫秒）	|
| endAt | Long  | NO | 截止时间（毫秒）	|
| fromId | Long | NO | 从哪一条成交id开始返回.|
| limit | Integer | NO | 默认 `50`; 最大 `1000`.|
<aside class="notice">
如果未传<code>startAt</code>和<code>endAt</code>,默认返回最近7天数据。
<code>startAt</code>和<code>endAt</code>的间隔时间不能超过<code>3</code>个月
</aside>
### 返回值
| 字段   | 含义   |
| ------ | ------ |
| id | 订单ID |
| symbol | 合约名称 |
| type | 订单类型 |
| side | 方向：`BUY`:买/做多 `SELL`:卖/做空 |
| price | 订单价格 |
| size | 数量 |
| dealSize | 订单成交数量 |
| dealValue | 成交价值 |
| workingType | 触发价格方式：`MP`（标记价格）、`TP`（最新成交价） |
| stopPrice | 触发价格，订单止盈止损 止损价格 |
| timeInForce | `GTC`: 用户主动取消才过期, `IOC`:立即成交可成交的部分，然后取消剩余部分，不进入买卖盘； `FOK`：如果下单不能全部成交，则取消； |
| postOnly | 是否只做maker |
| hidden | 是否是隐藏单 |
| leverage | 杠杆 |
| closeOrder | 是否平仓单 |
| visibleSize | 隐藏单对应的访问数量 |
| remark | 订单说明 |
| orderTime | 下单时间 |
| reduceOnly | 是否只减仓 |
| status | 订单状态 |
| placeType | 下单类型：`DEFAULT`、`OTOCO`、`STEP_REDUCE_POSITION`、`LIQUIDATION_TAKE_OVER`、`ADL_TRIGGER`、`ADL_LUCKY` |
| takeProfitPrice| 订单止盈止损 止盈价格 |
| cancelSize | 取消数量 |
| clientOid	| 客户订单编号 |

# 仓位

## 获取某个合约的仓位
```json
{
    "code": "200000",
    "data": [
        {
            "symbol": "BTCUSDM",
            "qty": 7,
            "leverage": 5,
            "marginType": "ISOLATED",
            "side": "BOTH",
            "autoDeposit": false,
            "entryPrice": "45494.04",
            "entryValue": "-0.0001538663",
            "margin": "0.0000306810",
            "totalMargin": "0.00002603090000000000",
            "liquidationPrice": "38771.12",
            "unrealisedPnl": "-0.00000465010000000000",
            "markPrice": "44159.35",
            "riskRate": "0.1537",
            "maintenanceMarginRate": "0.0250000000",
            "maintenanceMargin": "0.000004",
            "adlPercentile": "1"
        }
    ]
}
```
### HTTP请求
`GET /api/v2/symbol-position`
### API权限
该接口需要`通用权限`
### 参数
参数 | 数据类型 | 是否必须 | 含义 |  
--------- | ------- | -----------| -----------|
symbol | String | 是 | 合约symbol
### 返回值
属性  | 含义 |  
--------- | -----------|
symbol | 合约symbol | 
qty | 仓位数量（负数表示做空，正数表示做多） | 
leverage | 全局杠杆 | 
marginType | 保证金模式（`ISOLATED`-逐仓，`CROSSED`-全仓） | 
side | 仓位方向（`BOTH`，`LONG`，`SHORT`），当持仓模式为单向仓位时（目前只支持单向持仓），该合约只能有一个仓位，`side=BOTH`。当持仓模式为双向仓位时，该合约可以有两个反向的仓位,`side=LONG或SHORT` | 
autoDeposit | 是否自动追加保证金（为`true`时，强平触发自动追加保证金） | 
entryPrice | 平均开仓价格 | 
entryValue | 开仓价值 | 
margin | 仓位保证金 | 
totalMargin | 仓位总保证金（包含了未实现盈亏） | 
liquidationPrice | 强平价格 | 
unrealisedPnl | 未实现盈亏 | 
riskRate | 仓位风险率（`<1`，数值越大，风险越高） | 
maintenanceMarginRate | 仓位维持保证金率 | 
maintenanceMargin | 维持保证金 | 
adlPercentile | adl排名信息 | 

## 获取所有合约的仓位
```json
{
    "code": "200000",
    "data": [
        {
            "symbol": "BTCUSDM",
            "qty": 7,
            "leverage": 5,
            "marginType": "ISOLATED",
            "side": "BOTH",
            "autoDeposit": false,
            "entryPrice": "45494.04",
            "entryValue": "-0.0001538663",
            "margin": "0.0000306810",
            "totalMargin": "0.00002603090000000000",
            "liquidationPrice": "38771.12",
            "unrealisedPnl": "-0.00000465010000000000",
            "markPrice": "44159.35",
            "riskRate": "0.1537",
            "maintenanceMarginRate": "0.0250000000",
            "maintenanceMargin": "0.000004",
            "adlPercentile": "1"
        }
    ]
}
```
### HTTP请求
`GET /api/v2/all-position`
### API权限
该接口需要`通用权限`
### 参数
`无`
### 返回值
属性  | 含义 |  
--------- | -----------|
symbol | 合约symbol | 
qty | 仓位数量（负数表示做空，正数表示做多） | 
leverage | 全局杠杆 | 
marginType | 保证金模式（`ISOLATED`-逐仓，`CROSSED`-全仓） | 
side | 仓位方向（`BOTH`，`LONG`，`SHORT`），当持仓模式为单向仓位时（目前只支持单向持仓），该合约只能有一个仓位，`side=BOTH`。当持仓模式为双向仓位时，该合约可以有两个反向的仓位,`side=LONG或SHORT` | 
autoDeposit | 是否自动追加保证金（为`true`时，强平触发自动追加保证金） | 
entryPrice | 平均开仓价格 | 
entryValue | 开仓价值 | 
margin | 仓位保证金 | 
totalMargin | 仓位总保证金（包含了未实现盈亏） | 
liquidationPrice | 强平价格 | 
unrealisedPnl | 未实现盈亏 | 
riskRate | 仓位风险率（`<1`，数值越大，风险越高） | 
maintenanceMarginRate | 仓位维持保证金率 | 
maintenanceMargin | 维持保证金 | 
adlPercentile | adl排名信息 | 

## 增减保证金
```json
{
    "code": "200000",
    "data": true
}
```
### HTTP请求
`POST /api/v2/change-margin`
### API权限
该接口需要`交易权限`
### 参数
参数 | 数据类型 | 是否必须 | 含义 |  
--------- | ------- | -----------| -----------|
symbol | String | 是 | 合约symbol
positionSide | String | 是 | 目前只支持`BOTH`
amount | BigDecimal | 是 | 增减保证金数量，负数提取保证金，正数为增加仓位保证金
### 返回值
属性  | 含义 |  
--------- | -----------|
data | `true` 表示增减保证金成功 | 

## 仓位盈亏历史
```json
{
    "code": "200000",
    "data": [
        {
            "symbol": "LINKUSDTM",
            "settleCurrency": "USDT",
            "type": "CLOSE_LONG",
            "leverage": 5,
            "pnl": "-0.006",
            "roe": "-0.0098",
            "openTime": 1649299430000,
            "closeTime": 1649299436000,
            "openPrice": "15.37",
            "closePrice": "15.34"
        }
    ]
}
```
### HTTP请求
`GET /api/v2/close-pnl-his`
### API权限
该接口需要`通用权限`
### 参数
参数 | 数据类型 | 是否必须 | 含义 |  
--------- | ------- | -----------| -----------|
symbol | String | 否 | 合约symbol
type | String | 否 | 平仓类型
settleCurrency | String | 否 | 合约结算币种
startAt | String | 否 | 开始时间（时间跨度最多`3`个月）
endAt | String | 否 | 截止时间（时间跨度最多`3`个月）
limit | int | 否 | 记录数（默认`50`，最大`1000`）

<aside class="notice">
如果未传<code>startAt</code>和<code>endAt</code>,默认返回最近7天数据。
<code>startAt</code>和<code>endAt</code>的间隔时间不能超过<code>3</code>个月
</aside>

* `type`平仓类型说明：
    * `CLOSE_LONG`（手动平多）
    * `CLOSE_SHORT`（手动平空）
    * `LIQUID_LONG`（强制平多）
    * `LIQUID_SHORT`（强制平空）
    * `ADL_LONG`（adl平多）
    * `ADL_SHORT`（adl平空）


### 返回值
属性  | 含义 |  
--------- | -----------|
symbol | 合约symbol| 
settleCurrency | 合约结算币种 | 
type | 平仓类型 | 
leverage | 平仓时的全局杠杆 | 
pnl | 平仓盈亏 | 
roe | 收益率 | 
openTime | 开仓时间 | 
closeTime |平仓时间  | 
openPrice | 开仓价格 | 
closePrice | 平仓价格 | 

# 资金费用

## 查询资金费用结算历史
```json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": {
        "dataList": [
            {
                "id": "2783173922521088",
                "uid": "4456",
                "symbol": "SHIBUSDTM",
                "timePoint": "1644910900000",
                "fundingRate": "0.0010000000",
                "markPrice": "0.0000305200",
                "qty": "-1",
                "entryValue": "-3.0520000000",
                "funding": "0.0030520000",
                "settleCurrency": "USDT"
            }
        ]
    }
}
```
### HTTP请求
`GET /api/v2/funding-history`
### 参数
参数 | 数据类型 | 是否必须 | 含义 
--------- | ------- | -----------| -----------
| symbol | String | YES | 合约symbol	|
| startAt | Long  | NO | 开始时间（毫秒）	|
| endAt | Long  | NO | 截止时间（毫秒）	|
| fromId | Long | NO | 从哪一条成交id开始返回.缺省返回最近的资金费用结算历史|
| limit | Integer | NO | 默认 `50`; 最大 `1000`.|

<aside class="notice">
如果未传<code>startAt</code>和<code>endAt</code>,默认返回最近7天数据。
<code>startAt</code>和<code>endAt</code>的间隔时间不能超过<code>3</code>个月
</aside>

### 返回值
| 字段   | 含义   |
| ------ | ------ |
| id | id |
| uid | 用户uid |
| symbol | 合约symbol |
| timePoint | 时间点(毫秒) |
| fundingRate | 资金费率 |
| markPrice | 标记价格 |
| qty | 结算时的仓位数 |
| entryValue | 结算时的仓位价值 |
| funding | 结算的资金费用，正数表示收入；负数表示支出 |
| settleCurrency | 结算币种 |

---
# 市场数据
市场数据是公共的，不需要验证签名。

# 合约

## 获取所有开放合约信息
```json
{
    "code": "200000",
    "data": [
        {
            "symbol": "BTCUSDTM",
            "type": "FFWCSX",
            "firstOpenDate": 1585555200000,
            "expireDate": null,
            "settleDate": null,
            "baseCurrency": "BTC",
            "quoteCurrency": "USDT",
            "settleCurrency": "USDT",
            "maxOrderQty": 1000000,
            "maxPrice": 1000000,
            "lotSize": 1,
            "tickSize": 1,
            "indexPriceTickSize": 0.01,
            "multiplier": 0.001,
            "makerFeeRate": 0.0002,
            "takerFeeRate": 0.0006,
            "settlementFeeRate": 0.0006,
            "isInverse": false,
            "fundingBaseSymbol": ".BTCINT8H",
            "fundingQuoteSymbol": ".USDTINT8H",
            "fundingRateSymbol": ".BTCUSDTMFPI8H",
            "indexSymbol": ".KBTCUSDT",
            "settlementSymbol": "",
            "status": "Open"
        }
    ]
}
```
### HTTP请求
`GET /api/v2/contracts/active`
### 参数
`无`
### 返回值
属性  | 含义 |  
--------- | -----------|
symbol | 合约symbol | 
type | 合约类型：`FFWCSX`(永续)，`FFICSX`(交割） | 
firstOpenDate | 首次开放时间 | 
expireDate | 合约到期时间（永续合约用不过期） | 
settleDate | 交割合约交割时间 | 
baseCurrency | 基础货币 | 
quoteCurrency | 计价货币 | 
settleCurrency | 结算货币 | 
maxOrderQty | 最大委托数量 | 
maxPrice | 最大委托价格 | 
lotSize | 最小合约数量 | 
tickSize | 最小的价格变化 | 
indexPriceTickSize | 指数价格变化步长 | 
multiplier | 合约乘数 | 
makerFeeRate | maker手续费率 | 
takerFeeRate | taker手续费率 | 
settlementFeeRate | 交割合约交割时手续费率 | 
isInverse | 是否是币本位合约 | 
fundingBaseSymbol | 资金费用基础货币symbol | 
fundingQuoteSymbol | 资金费用计价货币symbol | 
fundingRateSymbol | 资金费率symbol | 
indexSymbol | 指数symbol | 
settlementSymbol | 结算symbol | 
status | 状态：`Open`(已上线)`、PrepareSettled`(准备结算)、`BeingSettled`（结算中）、`Paused`(已暂停)、`CancelOnly`(只能撤单) |  


## 获取某个合约
```json
{
    "code": "200000",
    "data": {
        "symbol": "BTCUSDTM",
        "type": "FFWCSX",
        "firstOpenDate": 1585555200000,
        "expireDate": null,
        "settleDate": null,
        "baseCurrency": "BTC",
        "quoteCurrency": "USDT",
        "settleCurrency": "USDT",
        "maxOrderQty": 1000000,
        "maxPrice": 1000000,
        "lotSize": 1,
        "tickSize": 1,
        "indexPriceTickSize": 0.01,
        "multiplier": 0.001,
        "makerFeeRate": 0.0002,
        "takerFeeRate": 0.0006,
        "settlementFeeRate": 0.0006,
        "isInverse": false,
        "fundingBaseSymbol": ".BTCINT8H",
        "fundingQuoteSymbol": ".USDTINT8H",
        "fundingRateSymbol": ".BTCUSDTMFPI8H",
        "indexSymbol": ".KBTCUSDT",
        "settlementSymbol": "",
        "status": "Open"
    }
}
```
### HTTP请求
`GET /api/v2/contracts/{symbol}`
### 参数
参数 | 数据类型 | 是否必须 | 含义 |  
--------- | ------- | -----------| -----------|
symbol | String | 是 | 合约symbol
### 返回值
属性  | 含义 |  
--------- | -----------|
symbol | 合约symbol | 
type | 合约类型：`FFWCSX`(永续)，`FFICSX`(交割） | 
firstOpenDate | 首次开放时间 | 
expireDate | 合约到期时间（永续合约用不过期） | 
settleDate | 交割合约交割时间 | 
baseCurrency | 基础货币 | 
quoteCurrency | 计价货币 | 
settleCurrency | 结算货币 | 
maxOrderQty | 最大委托数量 | 
maxPrice | 最大委托价格 | 
lotSize | 最小合约数量 | 
tickSize | 最小的价格变化 | 
indexPriceTickSize | 指数价格变化步长 | 
multiplier | 合约乘数 | 
makerFeeRate | maker手续费率 | 
takerFeeRate | taker手续费率 | 
settlementFeeRate | 交割合约交割时手续费率 | 
isInverse | 是否是币本位合约 | 
fundingBaseSymbol | 资金费用基础货币symbol | 
fundingQuoteSymbol | 资金费用计价货币symbol | 
fundingRateSymbol | 资金费率symbol | 
indexSymbol | 指数symbol | 
settlementSymbol | 结算symbol | 
status | 状态：`Open`(已上线)`、PrepareSettled`(准备结算)、`BeingSettled`（结算中）、`Paused`(已暂停)、`CancelOnly`(只能撤单) | 

## 获取合约的风险限额列表
```json
{
    "code": "200000",
    "data": [
        {
            "symbol": "ADAUSDTM",
            "level": 1,
            "maxRiskLimit": 500,
            "minRiskLimit": 0,
            "maxLeverage": 20,
            "initialMarginRate": 0.05,
            "maintenanceMarginRate": 0.025
        },
        {
            "symbol": "ADAUSDTM",
            "level": 2,
            "maxRiskLimit": 1000,
            "minRiskLimit": 500,
            "maxLeverage": 2,
            "initialMarginRate": 0.5,
            "maintenanceMarginRate": 0.25
        }
    ]
}
```
### HTTP请求
`GET /api/v2/contracts/risk-limit/{symbol}`
### 参数
参数 | 数据类型 | 是否必须 | 含义 |  
--------- | ------- | -----------| -----------|
symbol | String | 是 | 合约symbol
### 返回值
属性  | 含义 |  
--------- | -----------|
symbol | 合约symbol | 
level | 限额等级 | 
maxRiskLimit | 该等级最大风险限额 | 
minRiskLimit | 该等级最小风险限额 | 
maxLeverage | 该等级最大可用杠杆 | 
initialMarginRate | 该等级初始保证金率 | 
maintenanceMarginRate | 仓位价值处于该等级限额时，用到的维持保证金率 | 

## 获取合约K线数据
```json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": [
        [
            "1649642340000", //时间
            15.748, //开盘价
            15.748, //最高价
            15.736, //最低价
            15.736, //收盘价
            656 //成交量
        ]
    ]
}
```
### HTTP请求
`GET /api/v2/kline/query`
### 参数
参数 | 数据类型 | 是否必须 | 含义 
--------- | ------- | -----------| -----------
| granularity   | Integer   | YES | 代表分钟数，可选范围：`1`,`5`,`15`,`30`,`60`,`120`,`240`,`480`,`720`,`1440`,`10080` |
| symbol   | String   | YES | 合约symbol |
| from   |  Long  | NO | 起始时间（毫秒） |
| to   | Long | NO | 结束时间（毫秒） |

## 查询资金费率列表
```json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": {
        "dataList": [
            {
                "symbol": "SHIBUSDTM",
                "granularity": "28800000",
                "timePoint": "1649160000000",
                "value": "0.000100"
            }
        ],
        "hasMore": false
    }
}
```
### HTTP请求
`GET /api/v2/contract/{symbol}/funding-rates`
### 参数
参数 | 数据类型 | 是否必须 | 含义 
--------- | ------- | -----------| -----------
| symbol | String | YES | 合约symbol	|
| startAt | Long  | NO | 开始时间（毫秒）	|
| endAt | Long  | NO | 截止时间（毫秒）	|
| offset | Long | NO | 从哪一条成交id开始返回.缺省返回最近的资金费率列表|
| limit | Integer | NO | 默认 `50`; 最大 `1000`.|

<aside class="notice">
   查询资金费率时，<code>startAt/endAt</code> 限制了查询资金费率的开始/结束时间，如果返回数据中<code>hasMore</code>为<code>true</code>时，表示存在下一页数据，可以将<code>offset</code>设置为最后一条资金费率的<code>timePoint</code>，继续翻页查询数据。
</aside>


### 返回值
| 字段   | 含义   |
| ------ | ------ |
| symbol | 合约symbol |
| granularity | 时间粒度 |
| timePoint | 时间点(毫秒) |
| value | 资金费率 |
| hasMore | 是否有下一页 |

## 查询合约标记价格
```json
{
    "code": "200000",
    "data": {
        "symbol": "BTCUSDTM",
        "timePoint": "1651202804000",
        "value": "39504.65",
        "indexPrice": "39520.09"
    }
}
```
### HTTP请求
`GET /api/v2/mark-price/{symbol}/current`
### 参数
参数 | 数据类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
symbol | String | 是 | 合约symbol
### 返回值
属性 | 含义 |
--------- | -----------|
symbol | 合约symbol |
timePoint | 时间点, 以毫秒为单位 |
value | 标记价格值 |
indexPrice | indexPrice |

# 委托买卖盘

## 获取买卖盘
```json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": {
        "contractId": 206,
        "symbol": "LINKUSDTM",
        "ts": 1649746178803,
        "sequence": 102,
        "asks": [
            ["13.965",202],
            ["13.970",101]
        ],
        "bids": [
            ["13.942",164],
            ["13.937",164],
            ["13.928",178]
        ]
    }
}
```
返回买卖盘数据
### HTTP请求
`GET /api/v2/order-book`
### 参数
| 字段   | 类型   | 是否必需 | 说明                                |
| ------ | ------ | -------- | ----------------------------------- |
| symbol | 字符串 | Y        | 合约名称                            |
| limit  | 数字   | N        | 买卖盘档数，默认`500`，取值范围`1`~`1000` |
### 返回值
| 字段       | 含义     |
| ---------- | -------- |
| contractId | 合约ID   |
| symbol     | 合约名称 |
| ts         | 时间戳   |
| sequence   | 快照序号 |
| asks       | 卖盘     |
| bids       | 买盘     |

## 最优挂单
```json
{
  "success": true,
  "code": "200",
  "msg": "success",
  "retry": false,
  "data": {
    "symbol": "LINKUSDTM",
    "askPrice": "13.965",
    "askSize": 202,
    "bidPrice": "13.942",
    "bidSize": 164
  }
}
```
返回买卖盘最佳买一卖一价
### HTTP请求
`GET /api/v2/ticker/bookTicker`
### 参数
| 字段   | 类型   | 是否必需 | 说明     |
| ------ | ------ | -------- | -------- |
| symbol | 字符串 | Y        | 合约名称 |
### 返回值
| 字段     | 含义       |
| -------- | ---------- |
| symbol   | 合约名称   |
| askPrice | 最佳卖一价 |
| askSize  | 卖一数量   |
| bidPrice | 最佳买一价 |
| bidSize  | 买一数量   |


# 行情快照

## 获取最新成交价
```json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": {
        "symbol": "LINKUSDTM",
        "ts": 1649744712820,
        "price": "13.774",
    }
}
```
返回最新成交价
### HTTP请求
`GET /api/v2/ticker/price`
### 参数
| 字段   | 类型   | 是否必需 | 说明     |
| ------ | ------ | -------- | -------- |
| symbol | 字符串 | Y        | 合约名称 |
### 返回值
| 字段   | 含义       |
| ------ | ---------- |
| symbol | 合约名称   |
| ts     | 成交时间戳 |
| price  | 成交价格   |




## 获取最近成记录
```json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": [
      {
        "symbol": "ETHUSDTM",
        "matchSide": "buy",
        "price": "2929.90",
        "size": 10,
        "ts": 1649744581941
      }
    ]
  }
```
返回最近成交记录
### HTTP请求
`GET /api/v2/trades`
### 参数
| 字段   | 类型   | 是否必需 | 说明                                 |
| ------ | ------ | -------- | --------------------------------- |
| symbol | 字符串 | Y        | 合约名称                            |
| limit  | 数字   | N        | 返回记录条数，默认`20`，范围`1`~`1000`|
### 返回值
| 字段   | 含义                           |
| ------ | ----------------------------- |
| symbol | 合约名称                       |
| ts     | 成交时间戳                     |
| side   | taker方向，枚举值：`buy`,`sell` |
| price  | 成交价格                       |
| size   | 成交数量                       |


# 时间
## 获取服务器时间

```json
{
    "code": "200000",
    "msg": "success",
    "data": 1546837113087
}
```

获取API服务器时间。这是Unix时间戳。

### HTTP请求
`GET /api/v1/timestamp`

### 返回值
字段 | 含义
--------- | -------
data | 服务器时间, Unix时间戳。


# 服务状态

## 获取当前服务状态
```json
{
    "code": "200000",
    "data": {
        "status": "open", //open: 正常运行, close: 服务关闭, cancelonly:只能撤单
        "msg": "upgrade match engine" //备注
    }
}
```
获取当前服务状态
### HTTP请求
`GET /api/v1/status`

### 返回值
字段 | 含义
--------- | -------
status | 服务状态：`open`（正常运行）、`close`（服务关闭）、`cancelonly`（只能撤单）
msg | 备注



---
# Websocket
REST API的使用受到了访问频率的限制，因此推荐您使用Websocket获取实时数据。

**推荐您创建一条Websocket连接，多频道订阅获取实时数据。**

## 申请连接令牌

```json
{
    "code": "200000",
    "data": {
        "instanceServers": [
            {
                "pingInterval": 50000,
                "endpoint": "wss://push.kucoin.com/endpoint",
                "protocol": "websocket",
                "encrypt": true,
                "pingTimeout": 10000
            }
        ],
        "token": "vYNlCtbz4XNJ1QncwWilJnBtmmfe4geLQDUA62kKJsDChc6I4bRDQc73JfIrlFaVYIAE0Gv2--MROnLAgjVsWkcDq_MuG7qV7EktfCEIphiqnlfpQn4Ybg==.IoORVxR2LmKV7_maOR9xOg=="
    }
}
```

在创建Websocket连接前，您需申请一个令牌（Token）。



### 公共令牌 (不需要加签登录)

如果您只订阅公共频道的数据，请按照以下方式请求获取服务器列表和临时公共令牌。

#### HTTP请求
`POST /api/v1/bullet-public`

### 私有频道（需要验证签名）

```json
{
    "code": "200000",
    "data": {
        "instanceServers": [
            {
                "pingInterval": 50000,
                "endpoint": "wss://push.kucoin.com/endpoint",
                "protocol": "websocket",
                "encrypt": true,
                "pingTimeout": 10000
            }
        ],
        "token": "vYNlCtbz4XNJ1QncwWilJnBtmmfe4geLQDUA62kKJsDChc6I4bRDQc73JfIrlFaVYIAE0Gv2--MROnLAgjVsWkcDq_MuG7qV7EktfCEIphiqnlfpQn4Ybg==.IoORVxR2LmKV7_maOR9xOg=="
    }
}
```

如您需请求私有频道的数据（如：账户资金变化），请在签名验证后按照以下方式获取Websocket的服务实例和已验签的令牌。


#### HTTP 请求
`POST /api/v1/bullet-private`


### 返回值

|字段 | 含义|
-----|-----
|pingInterval| 发送消息的间隔时间（毫秒）|
|pingTimeout| 如果在pingTimeout时间后，未收到pong消息，那么连接可能已断开了 |
|endpoint| Websocket建立连接的服务器地址 |
|protocol| 支持的协议 |
|encrypt| 表示是否使用了SSL加密 |
|token | 令牌 |

## 建立连接

```javascript
var socket = new WebSocket("wss://push.kucoin.com/endpoint?token=xxx&[connectId=xxxxx]");
```

成功建立连接后，您将会收到系统向您发出的欢迎（welcome）消息。

```json
{
    "id": "hQvf8jkno",
    "type": "welcome"
}
```

**connectId**：连接ID，是客户端生成的唯一标识。您在创建连接时收到的欢迎（welcome）消息的ID以及错误消息的ID都属于连接ID（connectId）。


## Ping
```json
{
    "id": "1545910590801",
    "type": "ping"
}
```

为防止服务器断开TCP连接，客户端需要向服务器发送ping消息以保持连接的活跃性。

在服务器收到ping消息后，系统会向客户端返回一条pong消息。

如果服务器在60秒内没有收到来自客户端的ping消息，连接将被断开。


```json
{
    "id": "1545910590801",
    "type": "pong"
}
```

## 订阅数据

```json
{
    "id": 1545910660739, //表示ID的唯一值 
    "type": "subscribe",
    "topic": "/futuresMarket/ticker:BTCUSDTM", // 被订阅的频道。一些频道支持使用“,”分开订阅多个合约的信息推送。
    "privateChannel": false, // 是否使用了私有频道，默认设置为“false”。
    "response": true // 服务器是否需要返回该频道推送的信息。默认设置为“false”。
}
```

使用服务器订阅消息时，客户端应向服务器发送订阅消息。

订阅成功后，当`response`参数为`true`时，系统将向您发出`ack`消息。

```json
{
    "id": "1545910660739",
    "type": "ack"
}
```

当订阅频道产生新消息时，系统将向客户端推送消息。了解消息格式，请查看频道介绍。


### 参数
#### ID
ID用于标识请求和ack的唯一字符串。

#### Topic
您订阅的频道内容。

#### PrivateChannel

您可通过privateChannel参数订阅以一些用户私有的topic（如：`/contractMarket/level2`）。该参数默认设置为`false`。设置为`true`时，则您只能收到与您订阅的topic相关的内容推送。

#### Response
若设置为`true`, 用户成功订阅后，系统将返回ack消息。

## 退订
用于取消您之前订阅的topic

```json
  {
    "id": "1545910840805",                            // 表示ID的唯一值 
    "type": "unsubscribe",
    "topic": "/futuresMarket/ticker:BTCUSDTM",      //被取消订阅的频道。一些频道支持使用“,”分开取消多个交易对的信息订阅。
    "privateChannel": false, 
    "response": true,                                  //服务器是否需要返回该频道推送的信息。默认设置为“false”。


  }
```

```json
  {
    "id": "1545910840805",
    "type": "ack"
  }
```
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

### 参数

#### ID
用于标识请求的唯一字符串。

#### Topic
您订阅的topic内容。

#### PrivateChannel
您可通过privateChannel参数订阅以一些公共topic（如：`/contractMarket/tradeOrders`）。该参数默认设置为`false`。设置为`true`，您只能收到与您订阅相关的内容推送。

#### Response
若设置为`true`, 用户成功取消订阅后，系统将返回`ack`消息。

## 多路复用
 在一条物理连接上，您可开启多条多路复用通道，以订阅不同topic，获取多种数据推送。

例如：
请输入以下指令定开启多条bt1通道
 `{"id": "1Jpg30DEdU", "type": "openTunnel", "newTunnelId": "bt1", "response": true}`

在指定中添加参数**`tunnelId`**：
`{"id": "1JpoPamgFM", "type": "subscribe", "topic": "/futuresMarket/ticker:BTCUSDTM"，"tunnelId": "bt1", "response": true}`

请求成功后，您将收到 **`tunnelIId`** 对应的消息推送：
`{"id": "1JpoPamgFM", "type": "message", "topic": "/futuresMarket/ticker:BTCUSDTM", "subject": "trade.ticker", "tunnelId": "bt1", "data": {...}}`

关闭**`通道`**，请输入以下指令：
`{"id": "1JpsAHsxKS", "type": "closeTunnel", "tunnelId": "bt1", "response": true}`

##### 限制

1. 多路复用仅限API用户使用。
2. 最多可开启的多路复用通道条数：5条。

## 定序编号
买卖盘数据化、成交历史数据以及快照消息均会默认返回sequence字段。您可以从Level 2市场行情数据中的sequence来判断数据是否丢失，连接是否稳定。如果连接不稳定，请按照校准流程进行校准。

## 客户端消息判断逻辑

1. 判断消息类型。当前消息类型有三种消息类型：
    message（常用的推送消息）
    notice（通知）
    command（连续的命令）
2. 通过userId判断消息类型。有userId的消息为私有消息，没有userId的消息为一般消息。
3. 通过topic判断消息类型。可通过topic，来判断消息类型。
4. 通过subject判断消息类型。对于同一个topic下不同类型的消息，可通过subject判断消息类型。


# 公共频道

## 交易实时行情 ticker
```json
  {
    "id": 1545910660740,                          
    "type": "subscribe",
    "topic": "/futuresMarket/ticker:BTCUSDTM",
    "response": true                              
  }
```
```json
{
    "subject": "ticker",
    "topic": "/futuresMarket/ticker:BTCUSDTM",
    "data": {
        "symbol": "BTCUSDTM", // 行情
        "bestBidSize": 795, // 最佳买一价总数量
        "bestBidPrice": 3200.00, // 最佳买一价
        "bestAskPrice": 3600.00, // 最佳卖一价
        "bestAskSize": 284, // 最佳卖一价总数量
        "ts": 1650447469782 // 成交时间 - 毫秒
    }
}
```

Topic: `/futuresMarket/ticker:{symbol}`

* 推送频率: `实时推送`

订阅此topic，可获取指定交易对的最佳买一和卖一价（BBO）的数据推送。
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

## Level 2 市场行情

```json
//订阅示例
{
    "id": 1545910660740,
    "type": "subscribe",
    "topic": "/futuresMarket/level2:BTCUSDTM",
    "response": true
}
```
```json
//返回示例
{
    "subject": "level2",
    "topic": "/futuresMarket/level2:BTCUSDTM",
    "type": "message",
    "data": {
        "start": 20711,
        "end": 20712,
        "bids": [],
        "asks": [
            ["14.250",30]
        ],
        "ts": 1650447324950
    }
}
```

Topic：`/futuresMarket/level2:{symbol}`

* 推送频率: `100ms`一次

订阅此topic，获取Level 2买卖盘数据，订阅成功后，Websocket系统将向您推送增量数据的消息。


校准流程：

1. 订阅 `/futuresMarket/level2:BTCUSDTM`
2. 开始缓存收到的更新。同一个价位，后收到的更新覆盖前面的。
3. 访问Rest接口`/api/v2/order-book?symbol=BTCUSDTM&limit=1000`获得一个1000档的深度快照
4. 将目前缓存到的信息中`end`<步骤3中获取到的快照中的`start`的部分丢弃(丢弃更早的信息，已经过期)
5. 将深度快照中的内容更新到本地orderbook副本中，本地保存`lastUpdateId`=最后一个增量消息的`end`，并从websocket接收到的第一个`start <= lastUpdateId` 且 `end > lastUpdateId` 的event开始继续更新本地副本。
6. 每一个新的event的`start`应该小于或等于上一个event的`end`,否则可能出现了丢包，请从step3重新进行初始化。
7. 每一个event中的挂单量代表这个价格目前的挂单量绝对值，而不是相对变化。
8. 如果某个价格对应的挂单量为`0`，表示该价位的挂单已经撤单或者被吃，应该移除这个价位。
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

## 成交记录 
```json
//订阅示例
{
    "id": 1545910660741,
    "type": "subscribe",
    "topic": "/futuresMarket/execution:BTCUSDTM",
    "response": true
}
```
```json
//返回示例
{
    "topic": "/futuresMarket/execution:BTCUSDTM",
    "subject": "execution",
    "data": {
        "symbol": "BTCUSDTM", // 合约
        "matchSide": "sell", // 成交方向 buy/sell
        "size": 1, // 订单成交张数
        "price": 3200.00, // 成交价格
        "tradeId": 21518, // 成交编号
        "ts": 1650447324950 // 时间毫秒
    }
}
```

Topic: `/futuresMarket/execution:{symbol}`

* 推送频率: `实时推送`

每撮合一笔订单，系统就会推送此订单成交记录消息


<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>


## level2的5档全量数据推送频道 
```json
{
   "type": "message",
   "topic": "/futuresMarket/level2Depth5:BTCUSDM",
   "subject": "level2Depth5",
   "data": {
       "sequence":243,
       "asks":[
         ["9993", "3"],
         ["9992", "3"],
         ["9991", "47"],
         ["9990", "32"],
         ["9989", "8"]
       ],
       "bids":[
         ["9988", "56"],
         ["9987", "15"],
         ["9986", "100"],
         ["9985", "10"],
         ["9984", "10"]
  
       ],
       "ts": 1650447324950 //时间毫秒
    }
 }
```

Topic: `/futuresMarket/level2Depth5:{symbol}`

* 推送频率: `100ms`一次

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>



## level2的50档全量数据推送频道
```json
{
    "type": "message",
    "topic": "/futuresMarket/level2Depth50:BTCUSDM",
    "subject": "level2Depth5",
    "data": {
        "sequence": 243,
        "asks": [
            ["9993", "3"],
            ["9992", "3"],
            ["9991", "47"],
            ["9990", "32"],
            ["9989", "8"]
        ],
        "bids":[
            ["9988", "56"],
            ["9987", "15"],
            ["9986", "100"],
            ["9985", "10"],
            ["9984", "10"]
        ],
        "ts": 1650447324950 // 时间毫秒
    }
}
```

Topic: `/futuresMarket/level2Depth50:{symbol}`

* 推送频率: `100ms`一次
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>


## 标记价格、指数价格
```json
  //标记价格、指数价格
{
    "type": "message",
    "topic": "/futuresContract/markPrice",
    "subject": "mark.index.price",
    "sn": 123123,
    "data": {
        "symbol": "BTCUSDTM", //合约symbol
        "granularity": 1000, //粒度
        "indexPrice": 4000.23, //指数价格
        "markPrice": 4010.52, //标记价格
        "ts": 1551770400000 //时间毫秒
    }
}
```

Topic: `/futuresContract/markPrice`

* 推送频率: `1s`一次


<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>


## 资金费率
```json
 //资金费率
{
    "type": "message",
    "topic": "/futuresContract/fundingRate:BTCUSDTM",
    "subject": "funding.rate",
    "sn": 25997405694459904,
    "data": {
        "granularity": 60000, //粒度(预测资金费率：1分钟粒度60000; 资金费率: 8小时粒度28800000)
        "fundingRate": -0.002966, //资金费率
        "ts": 1551770400000
    }
}
```

Topic: `/futuresContract/fundingRate:{symbol}`

* 推送频率: `8h`一次
<br/><br/>
* `granularity`粒度说明：
    * `60000`（1分钟粒度）
    * `28800000`（8小时粒度）

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

# 私有消息

## 订单私有消息
```json
{
    "type": "message",
    "topic": "/futuresTrade/orders",
    "subject": "orderChange",
    "channelType": "private",
    "data": {
        "orderId": "5cdfc138b21023a909e5ad55", //订单号
        "tradeId": "123", // 撮合交易id
        "symbol": "BTCUSDTM", //合约symbol
        "eventType": "match", //消息类型，取值列表: "open", "match", "filled", "canceled" , "adl", "liquidation"
        "status": "MATCHING", //订单状态: "MATCHING", "FINISH"
        "matchSize": "", //成交数量 (当类型为"match"时包含此字段)
        "matchPrice": "", //成交价格 (当类型为"match"时包含此字段)
        "orderType": "LIMIT", //订单类型, "MARKET"表示市价单, "LIMIT"表示限价单
        "side": "BUY", // 订单方向，买或卖
        "price": "3600", //订单价格
        "size": "20000", //订单数量
        "remainSize": "20001", //订单剩余可用于交易的数量
        "filledSize": "20000", //订单已成交的数量
        "canceledSize": "0", // canceled消息中，订单减少的数量
        "clientOid": "5ce24c16b210233c36ee321d", //用户自定义ID
        "orderTime": , // 下单时间 ms
        "liquidity": "maker", // 流动性类型: "taker" 或 "maker"
        "ts": // 时间戳 ms,
        "sn": //序列号 sn
    }
}
```

Topic: `/futuresTrade/orders`

* 推送频率: `实时推送`
<br/><br/>
* `eventType`消息类型说明：
    * `open`（订单进入买卖盘时发出的消息）
    * `match`（订单成交时发出的消息）
    * `filled`（订单因成交后状态变为DONE时发出的消息）
    * `canceled`（订单因被取消后状态变为DONE时发出的消息）
    * `adl`（adl订单）
    * `liquidation`（强平订单）
<br/><br/>
* `status`订单状态说明：
    * `MATCHING`（撮合中，表示订单挂在盘口上）
    * `FINISH`（订单完成）
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

## 止损单生命周期监听事件
```json
{
    "topic": "/futuresTrade/stopOrder",
    "subject": "stopOrder",
    "data": {
        "orderId": "5cdfc138b21023a909e5ad55", //订单编号
        "symbol": "BTCUSDTM", //合约symbol
        "eventType": "open", // 消息类型: open (止损下单成功), triggered (止损单触发), canceled (止损单取消)
        "orderType": "STOP", // 订单类型: STOP, STOP_MARKET, TAKE_PROFIT, TAKE_PROFIT_MARKET
        "side": "BUY", // 订单买卖方向
        "size": "1000", //数量
        "orderPrice": "9000", //订单价格
        "stopPrice": "9100", //止损单触发价格
        "workingType": "TP", //止损单触发价格类型
        "triggerSuccess": true, //触发成功标记, 只有triggered类型消息需要
        "error": "error.createOrder.accountBalanceInsufficient", //错误码, 触发失败时使用
        "createdAt": 1558074652423, //创建时间
        "ts": 1233123123 //消息时间 ms
    }
}
```

Topic: `/futuresTrade/stopOrder`

* 推送频率: `实时推送`
<br/><br/>
* `eventType`说明：
    * `open`（止损下单成功）
    * `triggered`（止损单触发）
    * `canceled`（止损单取消）
<br/><br/>
* `orderType`说明：
    * `STOP`（限价止损单）
    * `STOP_MARKET`（市价止损单）
    * `TAKE_PROFIT`（限价止盈单）
    * `TAKE_PROFIT_MARKET`（市价止盈单）

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

## 可用余额变更事件

Topic: `/futuresAccount/accountChange`

* 推送频率: `实时推送`

```json
{
    "type": "message",
    "topic": "/futuresAccount/accountChange",
    "channelType": "private",
    "subject": "futures.availableBalance.change",
    "data": {
        "accountEquity": "999922.7850290000",//合约账户总权益
        "availableBalance": "814058.3002530800",//可用余额
        "availableTransferBalance": "814058.3002530800",//可转金额
        "event": "ORDER_MARGIN_CHANGE_EVENT",//消息事件
        "currency": "USDT",//转出冻结
        "holdBalance": "0.0000000000",//转出冻结
        "orderMargin": "185863.9384800000",//订单保证金
        "positionMargin": "0.5594959200",//仓位保证金
        "walletBalance": "999922.7982290000",//钱包余额
        "sn": 11004,//序列号
        "ts": 1650426007488//消息时间 ms
    }
}
```

* `event`说明：
    * `ORDER_MARGIN_CHANGE_EVENT`（订单保证金变更事件）
    * `POSITION_CHANGE_EVENT`（仓位变更事件）
    * `TRANSFER_EVENT`（划转事件）


<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>


## 仓位变化
### 仓位操作引起的仓位变化
```json
{
    "type": "message",
    "topic": "/futuresPosition/position",
    "channelType": "private",
    "subject": "position.change",
    "sn": 45300,
    "data": {
        "autoDeposit": false,//是否自动追加保证金
        "changeType": "POSITION_CHANGE",//仓位变化类型
        "entryPrice": 14.431,//平均开仓价格
        "entryValue": -17.3178,//开仓价值
        "leverage": 5,//全局杠杆
        "liquidationPrice": 17.161,//强平价格
        "liquidationValue": -20.59413818,
        "maintenanceMargin": 0.173178,//维持保证金
        "maintenanceMarginRate": 0.01,//仓位维持保证金率
        "margin": 3.44951618,//仓位保证金
        "marginType": "ISOLATED",//保证金模式
        "markPrice": 14.15,//标记价格
        "openTime": 1650424933902,//开仓时间
        "qty": -12,//仓位数量
        "riskRate": 0.0458,//仓位风险率（<1，数值越大，风险越高）
        "settleCurrency": "USDT",//合约结算币种
        "side": "BOTH",//仓位方向
        "snapshotId": 6558,
        "symbol": "LINKUSDTM",//合约symbol
        "totalMargin": 3.78731618,//仓位总保证金（包含了未实现盈亏）
        "unrealisedPnl": 0.3378,//未实现盈亏
        "sn": 12323,//序列号
        "ts": 1650424935025//消息时间 ms
    }
}
```

Topic: `futuresPosition/position`

* 推送频率: `实时推送`
<br/><br/>
* `changeType`说明：
    * `MARGIN_CHANGE`（增减保证金仓位变更）
    * `POSITION_CHANGE`（用户成交引起的仓位数量变更）
    * `LIQUIDATION`（强平仓位变更）
    * `ADL`（自动减仓仓位变更）
    * `LEVERAGE_CHANGE`（全局杠杆仓位变更)
    * `FUNDING_SETTLE`（资金费用结算仓位变更）
    * `AUTO_DEPOSIT`（自动追加保证金状态仓位变更）
    * `SETTLEMENT`（交割仓位变更）

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

# 登录 KuCoin
<a href="https://www.kucoin.com">登录 KuCoin</a>
