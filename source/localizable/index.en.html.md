# General
## Introduction
Welcome to the KuCoin Futures Developer’s Manual. This document outlines trading functions, market quotes and other application development APIs.


The KuCoin Futures API is divided into two parts: `REST API` and `Websocket` real time data stream.

 -  The REST API includes three types: `User (private)`, `Trade (private)` and `Market Data (public)`
 -  Websocket includes two types: `Public Channel` and `Private Channel`

<aside class="notice">
    Currently, the related interface of the new document is not open for use. The launch time will be announced later. If you have any questions, please use email to contact <code>newapi@kucoin.plus</code>
</aside>

## Update Preview

<!-- **In order to further improve API security, KuCoin has been upgraded to the version 2 of the API-KEY, and the verification logic has also undergone some changes. We recommend that you add and replace the new API-KEY on the [API Management page](https://futures.kucoin.com/api). KuCoin will continue to support the use of the old API-KEY until May 1, 2021. Please see "Message Signature" for more details.** -->

## Client Development Library

You can quickly integrate into “KuCoin Futures API” with the client development library.

**Official Software Development Kit (SDK)**

- [PHP SDK](https://github.com/Kucoin/kucoin-futures-php-sdk)
<br/>
- [Java SDK](https://github.com/Kucoin/kucoin-futures-java-sdk)
<br/>
- [Node.js SDK](https://github.com/Kucoin/kucoin-futures-node-sdk)
<br/>
- [Python SDK](https://github.com/Kucoin/kucoin-futures-python-sdk)
<br/>
- [Go SDK](https://github.com/Kucoin/kucoin-futures-go-sdk)

  

## Sandbox Test Environment
The sandbox is a test environment, and can be used to test the API
 connection and web transactions, while also providing all the functions for transactions. Within the sandbox, you can use `virtual funds` to test the trading functions.
Login sessions and API keys in the sandbox environment are completely `separated` from the production environment. You can use the web interface in the sandbox environment to create API keys.

<aside class="notice">
    After registering in the sandbox environment, you will receive a certain amount of virtual funds (BTC) that the system automatically recharges into your account. If you wish to make a trade, please transfer your assets from your main account to your trading account. These funds are purely for testing purposes and cannot be withdrawn.
</aside>


Sandbox URL for API testing: `https://sandbox-futures.kucoin.com`
<br/>
REST API connection URL: `https://api-sandbox-futures.kucoin.com`
<aside class="warning">
    `https://sandbox-api.kumex.com` has been Deprecated
</aside>


## Request Frequency Limits

When your request frequency exceeds the limit, the system will return an over frequency error code of **429**.
<aside class="notice">If the API request frequency exceeds the limit, your IP or account will be restricted for 10s.</aside>

### REST API

There are currently three frequency limit rules as follows:

* `code: 1015` means that there are too many requests from this IP, which triggers the cloudflare limit, and the restricted time is `30s` after the trigger. We recommend that different (sub) accounts use different IPs to request to avoid affecting each other.
* `{"code":"200002","msg":"Too many requests in a short period of time, please retry later"}` means that the account-specific API has exceeded the request limit, and the restricted time after triggering is `10s`. You can avoid this problem by using multiple sub-accounts.
* `{"code":"429000","msg":"Too Many Requests"}` means that the server received too many requests in a short period of time, and the request was rejected due to server overload protection. You can directly try requesting again.

### WEBSOCKET

**Number of connections**

- each user ID can establish ≤ `50` simultaneous connections.

**Connection Frequency**

- Connection request limit ≤ `30` requests per minute.

**Number of upstream messages** 

- Limit on the number of instructions sent to the server: `100` per `10` seconds.

**Number of subscribed topics** 

 - Each connection can subscribe to a maximum of `100` topics.

## Market Making Incentive Program
KuCoin Futures provides a market making incentive program for professional market makers. Participate in the program to get the following incentives:

 - Market maker rebates
 - Top ten performing market making teams will be rewarded each month, with higher rewards for the best market makers
 - Direct market access (provided with `private link` access)

Users with good market making strategies and large trading volumes are welcome to participate in this long-term market making incentive plan. If your account’s trading volume is over 5000 BTC in the past 30 days, please provide the following information to email `mm@kucoin.com` with the subject line "Futures Market Maker Application":

 - Provide platform account (email required, no referral relationship required)
 - Proof of trading volume on any exchange in the past 30 days or proof of VIP level
 - Please briefly describe your method of market making (do not need to elaborate) and estimate the percentage of market making order volume.

## VIP Fast Track
Users with good market making strategies and large trading volumes are welcome to participate in this long-term market making program. If your account equity is greater than 10BTC, please provide the following information to send an email to: `vip_futures@kucoin.com` for market maker application:

 - Provide platform account (email required, no referral relationship required)
 - Provide screenshots of market making trading volume on other trading platforms (such as trading volume within `30` days or VIP level, etc.); 
 - Please briefly describe your method of money making, and you don’t need to elaborate.

## Data Center
KuCoin data center is located in AWS Japan's ap-northeast-1a region.

## FAQ

Q: Why no XBT contracts anymore
<br/>
A: The symbol of the BTC Contracts will be uniformly modified from `XBT` to `BTC`. For example: USDT Margined Perpetual Contracts, the symbol will be changed from XBTUSDTM to BTCUSDTM.

---
# REST API
## Request Description

### API Server URL

The REST API provides APIs for user, transaction and market data.

Base URL: `https://api-futures.kucoin.com`<br/>
The request URL consists of the base URL and the specified API endpoint.
<aside class="warning"><code>https://api.kumex.com</code> has expired and is deprecated</aside>
<aside class="notice">In order to comply with local legal requirements, users with Chinese IPs are not allowed to access the above URLs.</aside>




## Interface Endpoint

Each API provides a corresponding endpoint, available under the HTTP `Requests` module.
For `GET` requests, just concatenate the request parameters after the request path.

For example: For the "Get the position of a contract" API, the default endpoint is `/api/v2/symbol-position`. When the "contract" parameter (`BTCUSDTM`) is requested, this endpoint becomes: `/api/v2/symbol-position?symbol=BTCUSDTM`. So your final request URL should be: `https://api-futures.kucoin.com/api/v2/symbol-position?symbol=BTCUSDTM`.

## Requests
The content-type of all requests and responses is `application/json`.  

Unless otherwise stated, all timestamp parameters are in Unix timestamp milliseconds. Such as: `1544657947759`

## Parameters

For `GET` and `DELETE` requests, the parameters need to be concatenated in the request URL (eg: `/api/v2/symbol-position?symbol=BTCUSDTM`).

For `POST` and `PUT` requests, the parameters need to be spliced in the request body in JSON format (eg: `{"side":"buy"}`).
<aside class="notice">Don't add spaces to JSON strings.</aside>


### Errors

The system returns an `HTTP error code` or a `system error code`. You can troubleshoot the cause of the error based on the returned parameter message.


#### HTTP Error Codes

```json
  {
    "code": "400100",
    "msg": "Invalid Parameter."
  }
```

Codes | Definitions
---------- | -------
400 | Bad Request -- Incorrect request format
401 | Unauthorized -- Invalid API Key
403 | Forbidden --Forbidden request
404 | Not Found -- Cannot find the specified resource
405 | Method Not Allowed -- Incorrect resource request method
415 | Content-Type -- Request type must be application/json
429 | Too Many Requests -- Request limit exceeded
500 | Internal Server Error -- Server error. Please try again later
503 | Service Unavailable -- Server under maintenance. Please try again later



#### System Error Code

Codes | Definitions
---------- | -------
1015 | cloudflare frequency limit according to IP, block 30s-- Frequency exceeded error: Cloudfare’s ip-based restriction, which lasts 30s once triggered.
40010 | Unavailable to place orders. Your identity information/IP/phone number shows you're at a country/region that is restricted from this service. -- You’re in a restricted country and the order can’t take effect.
100001 | There are invalid parameters -- Invalid parameters
100002 | systemConfigError -- System configuration error
100003 | Contract parameter invalid -- No such contract
100004 | Order is in not cancelable state -- Order doesn’t exist or cannot be revoked.
100005 | contractRiskLimitNotExist -- Contract risk limit doesn’t exist.
200001 | The query scope for Level 2 cannot exceed xxx -- “level2” query range must be less than or equal to xxx　
200002 | Too many requests in a short period of time, please retry later-- Frequency exceeded error: Interface service level restriction based on API, which lasts 10s once triggered.
200002 | The query scope for Level 3 cannot exceed xxx -- “level3” query range must be less than or equal to xxx
200003 | The symbol parameter is invalid. -- Invalid “symbol” parameter.
300000 | request parameter illegal -- Illegal request parameters
300001 | Active order quantity limit exceeded (limit: xxx, current: xxx) -- Exceeded the limit for number of open orders. Can submit a maximum of xxx open orders, and your current number of active orders: xxx
300002 | Order placement/cancellation suspended, please try again later. -- The system has suspended order placement/cancellation and will resume automatically, please try again later.
300003 | Balance not enough, please first deposit at least 2 USDT before you start the battle -- Insufficient balance, order cost is set to xxx
300004 | Stop order quantity limit exceeded (limit: xxx, current: xxx) -- Exceeded the limit for number of stop orders. Can submit a maximum of xxx stop orders, and your current number of stop orders: xxx
300005 | xxx risk limit exceeded -- The order will cause you to exceed your risk limit xxx
300006 | The close price shall be greater than the bankruptcy price. Current bankruptcy price: xxx. -- Close order price cannot be lower than the position’s bankruptcy price. The current bankruptcy price: xxx
300007 | priceWorseThanLiquidationPrice -- Increase position order price cannot be lower than the position’s liquidation price: xxx
300008 | Unavailable to place the order, there's no contra order in the market. -- There are no orders in the market. Submission of market order failed.
300009 | Current position size: 0, unable to close the position. -- Current position is 0\. Unable to close position.
300010 | Failed to close the position -- Close position failed
300011 | Order price cannot be higher than xxx -- Order price cannot be higher than xxx
300012 | Order price cannot be lower than xxx -- Order price cannot be lower than xxx
300013 | Unable to proceed the operation, there's no contra order in order book. -- There is no legal matchable price for the entrusted order. Order submission failed.
300014 | The position is being liquidated, unable to place/cancel the order. Please try again later. -- Forced liquidation in progress. The system has suspended order placement/cancellation and will resume automatically. Please try again later
300015 | The order placing/cancellation is currently not available. The Contract/Funding is under the settlement process. When the process is completed, the function will be restored automatically. Please wait patiently and try again later. -- During contract delivery/funding fee settlement, the system suspends order placement/cancellation and will resume automatically. Please try again later
300016 | The leverage cannot be greater than xxx. -- Leverage cannot be greater than xxx
300017 | Unavailable to proceed the operation, this position is for Futures Brawl -- This position is a contested position and doesn’t support this operation.
300018 | clientOid parameter repeated -- Duplicate “clientOid” parameter
400001 | Any of KC-API-KEY, KC-API-SIGN, KC-API-TIMESTAMP, KC-API-PASSPHRASE is missing in your request header -- KC-API-KEY, KC-API-SIGN, KC-API-TIMESTAMP and KC-API-PASSPHRASE parameters are missing from the request header
400002 | KC-API-TIMESTAMP Invalid -- Request time and server time differ by more than 5s
400003 | KC-API-KEY not exists -- API Key doesn’t exist
400004 | KC-API-PASSPHRASE error -- Incorrect API-PASSPHRAE
400005 | Signature error -- Signature error. Please check your signature.
400006 | The requested ip address is not in the api whitelist -- Request IP doesn’t exist in the API whitelist.
400007 | Access Denied -- Insufficient API permissions. Unable to access the URL target address.
400100 | Parameter Error -- Illegal request parameters
404000 | Url Not Found -- Cannot find the requested resource
411100 | User are frozen -- The user has already been frozen. Please contact the help center.
429000 | Too Many Requests -- Frequency exceeded error: Side wide traffic limit based on API. You can directly try requesting again.
500000 | Internal Server Error -- Server error. Please try again later

When the *HTTP status code* returned by the system is not `200`, the API returns an error code. When the call is successful, the system will return the `code` and `data` fields. When the call fails, the system will return the “code” and “msg” fields. You can troubleshoot errors based on the returned parameter message.



### Returned Successfully

When the system returns **HTTP status code** `200` and **system code** `200000`, it indicates that the response is successful, and the returned result is as follows:

```json
  {
    "code": "200000",
  }
```


### Paginator

KuCoin Futures uses two paginators, **Pagination** and **HasMore** to make REST requests that return arrays.


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

**Pagination** allows fetching results with the current number of pages, ideal for fetching real-time data. For example, the /api/v1/deposit-list, /api/v1/orders and /api/v1/fills endpoints all return the first page of results by default. To get more data, specify another pagination based on the currently returned data before making the request.

**Example**
GET /api/v1/orders?currentPage=1&pageSize=50

**Parameters**

Parameters | Default Value | Definitions
---------- | ------- | ------
currentPage | 1 | Current Page Code
pageSize | 50 | Number of data lines per page



#### HasMore
The HasMore paginator uses a sliding window mechanism to obtain paged data by sliding a fixed-size window on a data stream. The returned data has a “HasMore” field, which indicates whether there is more data. The HasMore pager has the same sliding time and high performance, and is very suitable for querying streaming real-time data.


**Parameters**

| Parameters | Data Type    | Definitions                                |
| --------- | ------- | --------------- |
| offset    | -       | The starting offset, generally the only attribute of the last returned result of the previous request. The first page is returned by default
| forward   | boolean | Sliding side. “TRUE” queries data in the side that is greater than “offset”      
| maxCount  | int     | Maximum number of data lines for each slide                    

**Example**
`GET /api/v1/interest/query?symbol=.XBTINT&offset=1558079160000&forward=true&maxCount=10`

## Type Description 
### Timestamp 
All timestamps in the API are returned in Unix timestamp milliseconds (eg: `1546658861000`)

## Authentication
### Create API Key
Before making the request through the API, you need to create an API_KEY through the web API. After creating it, you need to properly keep the following three pieces of information:


* `Key`
* `Secret`
* `Passphrase`

`Key` and `Secret` are randomly generated and provided by KuCoin Futures, while `Passphrase` is the password you used when creating the API. You cannot recover the above information once lost, and will need to request for an API Key again.


### Permissions

You can manage API permissions on the [KuCoin Futures](https://futures.kucoin.com) web terminal. API permissions are divided into the following few types:


* `General` - When enabled, the API can use most GET requests to fetch data.
* `Trade` - Once enabled, you can place/cancel orders and manage positions through the API.
* `Transfer` - Once enabled, you can withdraw money through the API. It should be noted that after enabling this permission, you can transfer money without two-step verification.


### Create Request

Request headers for all REST parameters must contain the following:

* `KC-API-KEY` API KEY is of string type.
* `KC-API-SIGN` Signature (see `"Message Signatures"` for more details)
* `KC-API-TIMESTAMP` Request timestamp
* `KC-API-PASSPHRASE` API passphrase filled in when creating an API
* `KC-API-KEY-VERSION` API-KEY version number, you can view the version number on the [API Management](https://futures.kucoin.com/api) page


### Message Signature

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
    
    #Example for Order placement in python
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

#### `KC-API-SIGN` in the request header: 

   * 1. Use the API-Secret to perform **HMAC-sha256** encryption on the {timestamp + method+ endpoint + body} concatenated string.
   * 2. Then, encode the encrypted content using **base64**.
                       

#### `KC-API-PASSPHRASE` in the request header:

   * For V1 API-KEY, please use clear text transmission.
   * For V2 API-KEY, you need to specify `KC-API-KEY-VERSION` as `2`, and use API-Secret to encrypt passphrase with `HMAC-sha256`, and then pass the encrypted content through `base64` encoding

Note:

* The encrypted `timestamp` needs to be consistent with the `KC-API-TIMESTAMP` in the request header.
* The encrypted `body` needs to be consistent with the content in the Request Body
* Request method needs to be capitalized
* For `GET`, `DELETE` requests, the endpoint needs to contain the requested parameters (`/api/v1/deposit-address?currency=XBT`). If there is no request body (usually used for GET requests), use the empty string "" for the request body

#### `KC-API-TIMESTAMP` in the request header

The `KC-API-TIMESTAMP` in the request header must be Unix UTC time, accurate to milliseconds (eg `1547015186532`).

The time difference between the time stamp of the server request and the API server must be controlled within `5s`, otherwise the request will be rejected by the server due to expiration. If there is a time difference between the server and the API server, please use the server time API provided by the platform to obtain the time of the API server.

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


## Enumerator Definition

* Buy-sell side (`side`):
    - `BUY` Buy/long
    - `SELL` Sell/short
</br></br>
* Order type (`orderType`, `type`):
    - `LIMIT` Limit order
    - `MARKET` Market order
    - `STOP` Stop loss limit
    - `TAKE_PROFIT` take profit limit
    - `STOP_MARKET` Stop loss market
    - `TAKE_PROFIT_MARKET` take profit market
</br></br>
* Order placement type (`placeType`):
    - `DEFAULT`	Default
    - `OTOCO` order take profit stop loss
    - `STEP_REDUCE_POSITION`	Ladder reduce position
    - `LIQUIDATION_TAKE_OVER`	Liquidation takeover
    - `ADL_TRIGGER`	ADL trigger user
    - `ADL_LUCKY`	ADL lucky user
</br></br>
* Stop price method (`workingType`):
    - `MP`	Mark Price:
    - `TP`	Latest transaction price
</br></br>
* Effective method (`timeInForce`):
    - `GTC` Expires only if the user actively cancels
    - `IOC` Immediately trade the part that can be traded, and then cancel the remaining part without entering the order book.
    - `FOK` If the order cannot be fully filled, it will be canceled.
</br></br>
* Funding record type (`type`):
    - `CONSUME` \- cost
    - `FUNDING` \- funding fee
    - `FEE` \- handling fee
    - `REALIZED_PNL` \- realized PNL
    - `TRANSFER_IN` \- transfer in
    - `TRANSFER_OUT` \- transfer out
    - `SON_MOTHER_ACCOUNT_TRANSFER` \- contract sub-master account transfer
    - `TRIAL_RECYCLE` \- trial funds recycling
    - `REWARD` \- rewards
    - `DEDUCTION_REFUND` \- deduction refunds
</br></br>
* Payment Account Type (`payAccountType`):
    - `MAIN` \- main account
    - `TRADE` \- trading account
</br></br>
* Receiving Account Type (`recAccountType`):
    - `MAIN` \- main account
    - `TRADE` \- trading account
</br></br>
* Transfer Status (`status`):
    - `APPLY` \- pending
    - `PROCESSING` \- processing
    - `PENDING_APPROVAL` \- awaiting review
    - `APPROVED` \- review passed
    - `REJECTED` \- review rejected
    - `PENDING_CANCEL` \- awaiting refund
    - `CANCEL` \- canceled
    - `SUCCESS` \- success
</br></br>
* Transfer Out Status (`status`):
    - `PROCESSING` \- processing
    - `SUCCESS` \- success
    - `FAILURE` \- failure
</br></br>
* Margin Type (`marginType`):
    - `ISOLATED` \- isolated
    - `CROSSED` \- crossed
</br></br>
* Position side (`side`):
    - `BOTH` \- single-direction position
    - `LONG` \- long side
    - `SHORT` \- short side
</br></br>
* Close Position Type (`type`):
    - `CLOSE_LONG` \- manual close position
    - `CLOSE_SHORT` \- manual close position
    - `LIQUID_LONG` \- forced close position
    - `LIQUID_SHORT` \- forced close position
    - `ADL_LONG` \- ADL close position
    - `ADL_SHORT` \- ADL close position
</br></br>
* Contract Type (`type`):
    - `FFWCSX` \- perpetual
    - `FFICSX` \- delivery
</br></br>
* Contract Status (`status`):
  - `Open` \- already live
  - `PrepareSettled` \- preparing for settlement
  - `BeingSettled` \- being settled
  - `Paused` \- paused
  - `CancelOnly` \- can only be canceled

---
# User
This portion requires signature verification.

# User Configuration

## Modify the user's auto deposit margin status.
```json
{
  "code": "200000",
  "data": true
} 
```
### HTTP Request
`POST /api/v2/user-config/change-auto-deposit`
### API Permissions
This API requires `Trade` permissions
### Parameters
Parameters | Data Type | Compulsory? | Definitions |  
--------- | ------- | -----------| -----------|
symbol | String | Yes | Contract Symbol
autoDeposit | Boolean | Yes | Enable automatic deposit margin? (when set to `true`, automatic deposit margin will be attempted when the liquidation price is reached)
## Get the user’s global leverage
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
### HTTP Request
`GET /api/v2/user-config/leverage`
### API Permissions
This API requires `General` permissions
### Parameters
Parameters | Data Type | Compulsory? | Definitions |  
--------- | ------- | -----------| -----------|
symbol | String | Yes | Contract Symbol
### Return Value
Attribute  | Definitions |  
--------- | -----------|
symbol | Contract Symbol | 
leverage | User’s current leverage | 
maxRiskLimit | The maximum position limit under this leverage | 

## Get user global leverage (all contracts)
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
### HTTP Request
`GET /api/v2/user-config/leverages`
### API Permissions
This API requires `General` permissions
### Parameters
`N/A`
### Return Value
Attribute | Definitions |
--------- | -----------|
symbol | Contract Symbol |
leverage | User’s current leverage |
maxRiskLimit | The maximum position limit under this leverage |

## Modify the user’s global leverage
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
### HTTP Request
`POST /api/v2/user-config/adjust-leverage`
### API Permissions
This API requires `Trade` permissions
### Parameters
Parameters | Data Type | Compulsory? | Definitions |  
--------- | ------- | -----------| -----------|
symbol | String | Yes | Contract Symbol
leverage | Integer | Yes | Leverage (positive value)|
### Return Value
Attribute  | Definitions |  
--------- | -----------|
symbol | Contract Symbol | 
leverage | User’s current leverage | 
maxRiskLimit | The maximum position limit under this leverage | 



# Account

## Get the list of all sub-accounts
```json
{
    "code": "200", //200 represents a successful query, while other codes represent failure.
    "msg": "",
    "retry": true,
    "success": true,
    "data": [
        {
            "userId": "620635a64a6f480001123ffa", //The user ID of a sub-user.
            "subName": "William" //The username of a sub-user.
        }，
    {
            "userId": "620635a64a6f48004353461",
            "subName": "David"
        }
    ]
}
```
### HTTP Request
`GET /api/v2/sub-accounts`
### API Permissions
This API requires `General` permissions
### Frequency Limits
The request frequency of this API is limited to `30 times/3s` for each account
### Parameters
`N/A`
### Return Value
Field|Definitions
---|---
userId|The user ID of a sub-user.
subName|The username of a sub-user.

## Get account overview
```json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": [
        {
            "currency": "USDT", // Currency type
            "walletBalance": "21098384.5602084900", // Wallet balance
            "positionMargin": "310.7995729500", // Position margin
            "orderMargin": "7177.2558034300", // Order margin
            "availableBalance": "21090896.5048321100", // Available balance
            "unrealisedPNL": "2.2572000000", // Position unrealized PNL
            "accountEquity": "21098386.8174084900", // Total equity of the currency account = wallet balance + position unrealized PNL
            "holdBalance": "0.0000000000", // Transfer frozen amount
            "availableTransferBalance": "21090896.5048321100" // Transferable amount
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
### HTTP Request
`GET /api/v2/account-overview`
### API Permissions
This API requires `General` permissions
### Frequency Limits
The request frequency of this API is limited to `30 times/3s` for each account
### Parameters  
Parameters|Data Type|Compulsory?|Definitions
---|---|---|---
currency|String|NO|Currency type: BTC, USDT, ETH, XRP or DOT. If not passed, query all currencies
### Return Value  
Field|Definitions
---|---
currency|Currency Type
walletBalance|Wallet balance
positionMargin|Position margin
orderMargin|Order margin
availableBalance|Available balance
unrealisedPNL|Unrealized PNL
accountEquity|Futures account total equity. `Futures account total equity` = `Wallet balance` + `unrealized PNL`
holdBalance|Transfer out frozen
availableTransferBalance|Transferable amount


## Query Fund Record
```json
{
    "success": true,
    "code": "200", // 200 represents a successful query, while other codes represent failure.
    "msg": "success",
    "retry": false,
    "data": [
        {
            "time": 1650348854000, // Time of business
            "currency": "USDT", // Currency type
            "type": "FEE", // Business type
            "amount": "-0.00408000", // Transaction amount
            "walletBalance": "99999.70744444", // Wallet balance
            "remark": "ETHUSDTM" // Transaction description
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
### HTTP Request
`GET /api/v2/transaction-history`
#### API Permissions
This API requires `General` permissions
#### Frequency Limits
The request frequency of this API is limited to `30 times/3s` for each account
### Parameters  
Parameters|Data Type|Compulsory?|Definitions
---|---|---|---
limit|Integer|NO|Number of records, default `100`, max `1000`
currency|String|NO|Currency Type
type|String|NO|Cashflow type
startAt|Long|NO|Start time
endAt|Long|NO|End time  

<aside class="notice">
If <code>startAt</code> and <code>endAt</code> is not transmitted, returns the past <code>7</code>  days’ data by default.
The interval between <code>startAt</code> and <code>startAt</code> cannot exceed <code>3</code> months
</aside>

* Cashflow type (`type`) description:
    * `CONSUME` \- cost
    * `FUNDING` \- funding fee
    * `FEE` \- handling fee
    * `REALIZED_PNL` \- realized PNL
    * `TRANSFER_IN` \- transfer in
    * `TRANSFER_OUT` \- transfer out
    * `SON_MOTHER_ACCOUNT_TRANSFER` \- contract sub-master account transfer
    * `TRIAL_RECYCLE` \- trial funds recycling
    * `REWARD` \- rewards  
    * `DEDUCTION_REFUND` \- deduction refunds
    * `INSURANCE_CROSS_POSITION_PAY` \- Insurance fund cross position payments



### Return Value  
Field|Definitions
---|---
time|Time of business
currency|Currency Type
type|Type
amount|Transaction amount
walletBalance|Wallet balance
remark|Description



# Transfer
## Transfer between Master user and Sub-user
``` json
{
    "code": "200", //200 represents a successful transfer, while other codes represent failure.
    "msg": "",
    "retry": true,
    "success": true,
}
```
### HTTP Request
`POST /api/v2/sub-transfer`
### API Permissions
This API requires `Trade` permissions
### Frequency Limits
The request frequency of this API is limited to `30 times/3s` for each account
### Parameters
Parameters|Data Type|Compulsory?|Definitions
---|---|---|---
amount | Number | YES | Transfer amount
currency | String | YES | currency
subUserId | String | YES | the user ID of a sub-account.
direction | String | YES | `OUT` — the master user to sub user<br/>`IN` — the sub user to the master user.
accountType | String | YES | The account type of the master user: `MAIN`, `TRADE` or `CONTRACT`
subAccountType | String | YES | The account type of the sub user: `MAIN`, `TRADE` or `CONTRACT`
### Return Value
`code` `200` represents a successful transfer, while other codes represent failure.


## Transfer out to KuCoin main/trading account
``` json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": {
        "applyId": "30626177500585984", // Transfer out request ID. Can use this ID to revoke the request
        "bizNo": "30626172928794624", // Business UID
        "payAccountType": "CONTRACT", // Payment account type
        "payTag": "DEFAULT", // Payment account sub-type
        "remark": "", // User remarks
        "recAccountType": "MAIN", // Receiving account type
        "recTag": "DEFAULT", // Receiving account sub-type
        "recRemark": "", // Receiving account cashflow remarks
        "recSystem": "KUCOIN", // Receiving service party
        "status": "APPLY", // Status
        "currency": "USDT", // Currency type
        "amount": "22.0000000000", // Transfer amount
        "fee": "0.0000000000", // Transfer handling fees
        "sn": 30626177504780288, // Unique S/No.
        "reason": "", // Reason for failure
        "createdAt": 1650773821000, // Creation time
        "updatedAt": 1650773822000 // Updated time
    }
}
```
### HTTP Request
`POST /api/v2/account/transfer-out`
### API Permissions
This API requires `Trade` permissions
### Frequency Limits
The request frequency of this API is limited to `30 times/3s` for each account
### Parameters  
Parameters|Data Type|Compulsory?|Definitions
---|---|---|---
amount|Number|YES|Transfer amount
currency|String|YES|Currency type, such as USDT or BTC
recAccountType|String|YES|Receiving account type. Can only be `MAIN` \- main account, or `TRADE` \- trading account
### Return Value  
Field|Definitions
---|---
applyId|Transfer out request ID. Can use this ID to revoke the request
bizNo|Business Primary Key ID
payAccountType|Payment account type
payTag|Payment account sub-type
remark|User remarks
recAccountType|Receiving account type
recTag|Receiving account sub-type
recRemark|Receiving account cashflow remarks
recSystem|Receiving service party
status|Status: `APPLY` \- pending, `PROCESSING` \- processing, `PENDING_APPROVAL` \- awaiting review, `APPROVED` \- review passed, `REJECTED` \- review rejected, `PENDING_CANCEL` \- awaiting refund, `CANCEL` \- canceled, `SUCCESS` \- successful
currency|Currency Type
amount|Transfer amount
fee|Transfer handling fees
sn|S/No.
reason|Reason for failure
createdAt|Creation time
updatedAt|Updated time



## Query transfer out request record
``` json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": [
        {
            "applyId": "30626177500585984", // Request ID
            "currency": "USDT", // Currency type
            "recRemark": "", // Receiving account cashflow remarks
            "recSystem": "KUCOIN", // Receiving service party
            "status": "PROCESSING", // Status
            "amount": "22.0000000000", // Transfer amount
            "reason": "", // Reason for failure
            "sn": 30626177504780288, // Unique S/No.
            "createdAt": 1650773821000, // Time of business
            "remark": "" // User remarks
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
### HTTP Request
`GET /api/v2/transfer-list`
### API Permissions
This API requires `General` permissions
### Frequency Limits
The request frequency of this API is limited to `30 times/3s` for each account
### Parameters  
Parameters|Data Type|Compulsory?|Definitions
---|---|---|---
startAt|Long|NO|Start time of business
endAt|Long|NO|End time of business
limit|Integer|NO|Number of records, default `100`, max `1000`
currency|String|NO|Currency Type
status|String|NO|Status: `PROCESSING` \- processing, `SUCCESS` \- success, `FAILURE` \- failure

<aside class="notice">
If <code>startAt</code> and <code>endAt</code> is not transmitted, returns the past <code>7</code>  days’ data by default.
The interval between <code>startAt</code> and <code>startAt</code> cannot exceed <code>3</code> months
</aside>

### Return Value
Field|Definitions
---|---
applyId|Transfer out request ID
currency|Currency Type
recRemark|Receiving account cashflow remarks
recSystem|Receiving service party
status|Status
amount|Transfer out amount
reason|Reason for failure
sn|S/No.
createdAt|Date
remark|Payment account remarks


## Fund transfer into futures account
``` json
{
    "code": "200", // The code 200 represents a successful transfer out, while other codes represent failure.
    "msg": "",
    "retry": true,
    "success": true
}
```
### HTTP Request
`POST /api/v2/transfer-in`
### API Permissions
This API requires `Trade` permissions
### Frequency Limits
The request frequency of this API is limited to `30 times/3s` for each account
### Parameters  
Parameters|Data Type|Compulsory?|Definitions
---|---|---|---
amount|Number|YES|Transaction amount
currency|String|YES|Currency Type
payAccountType|String|YES|Receiving account type: Can only be `MAIN` \- main account, or `TRADE` \- trading account
### Return Value
The `code` `200` represents a successful query, while other codes represent failure.



---
# Trade
This portion requires signature verification.

# Order

## Order placement
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
### Place Order Limit
The maximum active orders for a single trading pair in one account is `100` (untriggered stop orders included).
### HTTP Request
`POST /api/v2/order`
### API Permissions
This API requires `Trade` permissions
### Parameters
Parameters | Data Type | Compulsory? | Definitions 
--------- | ------- | -----------| -----------
 symbol       | STRING   | YES      | Trading pair                                                       
 side         | ENUM     | YES      | Buy-sell side: `SELL` or `BUY`                                           
 price        | DECIMAL  | NO       | Price                                                         
 type         | ENUM     | YES      | Order type: `LIMIT`, `MARKET`, `STOP`, `TAKE_PROFIT`, `STOP_MARKET`, `TAKE_PROFIT_MARKET`
 size         | INT      | NO       | Order placement quantity. This parameter is not supported when using `closeOrder`                          
 clientOid    | STRING   | NO       | Unique order ID. Can be used to identify orders. E.g.: UUID, can only contain numbers, letters, underscore (_) or dash (-) 
 reduceOnly   | BOOLEAN  | NO       | \[Optional] Reduce only mark, default value is `false`. When this value is `true`, you need to configure the close position quantity. 
 closeOrder   | BOOLEAN  | NO       | \[Optional] Close position order mark, default value is `false`. When this value is `true`, represents closing all positions 
 hidden       | BOOLEAN  | NO       | \[Optional] Orders are not displayed in the order book. Select `hidden`, and `postOnly` is not allowed to be selected. 
 visibleSize  | INT      | NO       | \[Optional] Maximum quantity of hidden orders displayed.                              
 stopPrice    | DECIMAL  | NO       | Stop price. Only `STOP`, `STOP_MARKET`, `TAKE_PROFIT` and `TAKE_PROFIT_MARKET` require this parameter 
 postOnly     | BOOLEAN  | NO       | \[Optional] Only maker ID. Select `postOnly`, and `hidden` is not allowed to be selected. This parameter is invalid when the order aging is an `IOC` strategy. 
 workingType  | STRING   | NO       | \[Optional] Stop loss order stop price type, including `TP` and `MP`                          
 timeInForce  | STRING   | NO       | Effective method                                                     
 clientTimestamp| TIMESTAMP | NO | \[Optional] Client order placement time
 allowMaxTimeWindow| INT | NO | \[Optional] The maximum time interval between the time the server is allowed to receive the request and the `clientTimestamp`

According to the different order placement `type`, the corresponding required parameters are as follows:

| Type                               | Parameters                          |
| ---------------------------------- | ---------------------------- |
| `LIMIT`                            | `size`, `price`              |
| `MARKET`                           | `size`                       |
| `STOP`,`TAKE_PROFIT`               | `size`, `price`, `stopPrice `|
| `STOP_MARKET`,`TAKE_PROFIT_MARKET` | `size`, `stopPrice`          |

* Conditional order triggering description:
    * `STOP` and `STOP_MARKET` stop loss order:
        * Buy: The latest contract price/mark price is higher than or equal to the stop price `stopPrice`
        * Sell: The latest contract price/mark price is lower than or equal to the stop price `stopPrice`
    * `TAKE_PROFIT`, `TAKE_PROFIT_MARKET` take profit order:
        * Buy: The latest contract price/mark price is lower than or equal to the stop price `stopPrice`
        * Sell: The latest contract price/mark price is higher than or equal to the stop price `stopPrice`
<br/><br/>
* Description of `clientTimestamp` and `allowMaxTimeWindow` parameters: It will take effect when these two parameters are passed `at the same time`
      * Validation logic:
          * 1. Time when the server received the request: `receiveTime`
          * 2. The interval between the time when the server receives the request (`receiveTime`) and the time when the client places an order (`clientTimestamp`): `receiveTime` - `clientTimestamp` = \`timeWindow\`\`
          * 3. When `clientTimestamp` <= `receiveTime` and `timeWindow` <= `allowMaxTimeWindow`, the order request will be accepted, otherwise the order fails and returns error code `300019`
<br/><br/>

### Return Value
Attribute  | Definitions 
--------- | -----------
orderId | Order ID 

<aside class="notice">
If the return information of the order interface is <code>{"success":false,"code":"100000","msg":"error.timeOut","retry":false,"data":null}</code >represents an order timeout. In this case, you need to specify the <code>clientOid</code> parameter to query the <code>GET /api/v2/order/detail</code> endpoint to determine whether the order request is successful
</aside>

## Single Order Cancellation
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

### HTTP Request
`DELETE /api/v2/order`
### API Permissions
This API requires `Trade` permissions
### Parameters
Parameters | Data Type | Compulsory? | Definitions 
--------- | ------- | -----------| -----------
symbol | STRING | YES | Contract Symbol
orderId | STRING | NO | Order ID 
clientOid | STRING | NO | User-defined “orderId” 

<aside class="notice">
Select either <code>orderId</code> or <code>clientOid</code>. If both are passed, <code>orderId</code> is used by default.
</aside>



## Batch Order Cancellation
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
### HTTP Request
`DELETE /api/v2/orders`
### API Permissions
This API requires `Trade` permissions
### Parameters
Parameters | Data Type | Compulsory? | Definitions 
--------- | ------- | -----------| -----------
symbol | STRING | YES | Contract Symbol
### Return Value
Attribute  | Definitions 
--------- | -----------
orderIds | Successfully canceled order’s ID 


## Query Transaction Records
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
### HTTP Request
`GET /api/v2/orders/historical-trades`
### API Permissions
This API requires `General` permissions
### Parameters
Parameters | Data Type | Compulsory? | Definitions 
--------- | ------- | -----------| -----------
| symbol | String | YES | Contract Symbol	|
| startAt | Long  | NO | Start time (ms)	|
| endAt | Long | NO | End time (ms)	|
| limit | Integer | NO | Default `50`, max `1000`.	|
| fromId | Long | NO |Which transaction ID to start returning from. By default, the latest transaction record will be returned	|
<aside class="notice">
If <code>startAt</code> and <code>endAt</code> is not transmitted, returns the past <code>7</code>  days’ data by default.
The interval between <code>startAt</code> and <code>startAt</code> cannot exceed <code>3</code> months
</aside>
### Return Value
| Field   | Definitions   |
| ------ | ------ |
| id | Transaction record ID |
| orderId | Order ID |
| symbol | Futures trading pair name |
| time | Matching time |
| side | Side: `BUY`\: buy/long `SELL`\: sell/short |
| size | Quantity |
| price | Price |
| fee | Handling fees |
| feeRate | Handling fees rate |
| feeCurrency | Handling fees currency type |
| pnl | PNL |
| pnlCurrency | PNL currency type |
| value | Value |
| orderType | Order Type |
| placeType | Order type: `DEFAULT`, `OTOCO`, `STEP_REDUCE_POSITION`, `LIQUIDATION_TAKE_OVER`, `ADL_TRIGGER`, `ADL_LUCKY` |
| maker | Maker? |
| forceTaker | Force to be processed as “taker”?|


## Query Individual Order’s Details
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
### HTTP Request
`GET /api/v2/order/detail`
### API Permissions
This API requires `General` permissions
### Parameters

Parameters | Data Type | Compulsory? | Definitions 
--------- | ------- | -----------| -----------
| orderId | String | NO | Order ID	|
| clientOid | String | NO | User-defined “orderId”	|

<aside class="notice">
Select either <code>orderId</code> or <code>clientOid</code>. If both are passed, <code>orderId</code> is used by default.
</aside>
<aside class="notice">
If it is not an active order, you can only query the detailed data within <code>1</code> month.
</aside>

### Return Value
| Field   | Definitions   |
| ------ | ------ |
| id | Order ID |
| symbol | Contract Name |
| type | Order Type |
| side | Side: `BUY`: buy/long `SELL`: sell/short |
| price | Order Price |
| size | Quantity |
| dealSize | Order Volume |
| dealValue | Transaction value |
| workingType | Stop price method: `MP` (marked price), `TP` (latest transaction price) |
| stopPrice | Stop price, order take profit stop loss stop-loss price |
| timeInForce | `GTC`: Expires only if the user cancels manually. `IOC`: The part that can be traded is executed immediately, and the remaining part is canceled without entering the order book. `FOK`: If the order cannot be fully executed, it will be canceled. |
| postOnly | Only be a “maker”? |
| hidden | Hidden order? |
| leverage | Leverage |
| closeOrder | Close position order? |
| visibleSize | Access volume corresponding to the hidden order |
| remark | Order description |
| orderTime | Order time |
| reduceOnly | Reduce only? |
| status | Order status |
| placeType | Order type: `DEFAULT`, `OTOCO`, `STEP_REDUCE_POSITION`, `LIQUIDATION_TAKE_OVER`, `ADL_TRIGGER, ADL_LUCKY` |
| takeProfitPrice| Order take profit stop loss take-profit price |
| cancelSize | Cancellation quantity |
| clientOid	| Customer order S/No. |


## Query Active Orders
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
### HTTP Request
`GET /api/v2/orders/active`
### API Permissions
This API requires `General` permissions
### Parameters
Parameters | Data Type | Compulsory? | Definitions 
--------- | ------- | -----------| -----------
| symbol | String | YES | Contract Symbol	|
### Return Value
| Field   | Definitions   |
| ------ | ------ |
| id | Order ID |
| symbol | Contract Name |
| type | Order Type |
| side | Side: `BUY`\: buy/long `SELL`\: sell/short |
| price | Order Price |
| size | Quantity |
| dealSize | Order Volume |
| dealValue | Transaction value |
| workingType | Stop price method: `MP` (marked price), `TP` (latest transaction price) |
| stopPrice | Stop price, order take profit stop loss stop-loss price |
| timeInForce | `GTC`\: Expires only if the user cancels manually. `IOC`\: The part that can be traded is executed immediately, and the remaining part is canceled without entering the order book. `FOK`\: If the order cannot be fully executed, it will be canceled. |
| postOnly | Only be a “maker”? |
| hidden | Hidden order? |
| leverage | Leverage |
| closeOrder | Close position order? |
| visibleSize | Access volume corresponding to the hidden order |
| remark | Order description |
| orderTime | Order time |
| reduceOnly | Reduce only? |
| status | Order status |
| placeType | Order type: `DEFAULT`, `OTOCO`, `STEP_REDUCE_POSITION`, `LIQUIDATION_TAKE_OVER`, `ADL_TRIGGER`, `ADL_LUCKY` |
| takeProfitPrice| Order take profit stop loss take-profit price |
| cancelSize | Cancellation quantity |
| clientOid	| Customer order S/No. |

## Query All Active Orders
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
### HTTP Request
`GET /api/v2/orders/all-active`
### API Permissions
This API requires `General` permissions
### Parameters
`N/A`
### Return Value
| Field   | Definitions   |
| ------ | ------ |
| id | Order ID |
| symbol | Contract Name |
| type | Order Type |
| side | Side: BUY: buy/long SELL: sell/short |
| price | Order Price |
| size | Quantity |
| dealSize | Order Volume |
| dealValue | Transaction value |
| workingType | Stop price method: `MP` (marked price), `TP` (latest transaction price) |
| stopPrice | Stop price, order take profit stop loss stop-loss price |
| timeInForce | `GTC`\: Expires only if the user cancels manually. `IOC`\: The part that can be traded is executed immediately, and the remaining part is canceled without entering the order book. `FOK`\: If the order cannot be fully executed, it will be canceled. |
| postOnly | Only be a “maker”? |
| hidden | Hidden order? |
| leverage | Leverage |
| closeOrder | Close position order? |
| visibleSize | Access volume corresponding to the hidden order |
| remark | Order description |
| orderTime | Order time |
| reduceOnly | Reduce only? |
| status | Order status |
| placeType | Order type: `DEFAULT`, `OTOCO`, `STEP_REDUCE_POSITION`, `LIQUIDATION_TAKE_OVER`, `ADL_TRIGGER`, `ADL_LUCKY` |
| takeProfitPrice| Order take profit stop loss take-profit price |
| cancelSize | Cancellation quantity |
| clientOid	| Customer order S/No. |


## Query Historical Orders
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
### HTTP Request
`GET /api/v2/orders/history`
### API Permissions
This API requires `General` permissions
### Parameters
Parameters | Data Type | Compulsory? | Definitions 
--------- | ------- | -----------| -----------
| symbol | String | YES | Contract Symbol	|
| startAt | Long  | NO | Start time (ms)	|
| endAt | Long  | NO | End time (ms)	|
| fromId | Long | NO | Which transaction ID to start returning from.	|
| limit | Integer | NO | Default `50`, max `1000`.	|
<aside class="notice">
If <code>startAt</code> and <code>endAt</code> is not transmitted, returns the past <code>7</code>  days’ data by default.
The interval between <code>startAt</code> and <code>startAt</code> cannot exceed <code>3</code> months
</aside>
### Return Value
| Field   | Definitions   |
| ------ | ------ |
| id | Order ID |
| symbol | Contract Name |
| type | Order Type |
| side | Side: `BUY`\: buy/long `SELL`\: sell/short |
| price | Order Price |
| size | Quantity |
| dealSize | Order Volume |
| dealValue | Transaction value |
| workingType | Stop price method: `MP` (marked price), `TP` (latest transaction price) |
| stopPrice | Stop price, order take profit stop loss stop-loss price |
| timeInForce | `GTC`\: Expires only if the user cancels manually. `IOC`\: The part that can be traded is executed immediately, and the remaining part is canceled without entering the order book. `FOK`\: If the order cannot be fully executed, it will be canceled. |
| postOnly | Only be a “maker”? |
| hidden | Hidden order? |
| leverage | Leverage |
| closeOrder | Close position order? |
| visibleSize | Access volume corresponding to the hidden order |
| remark | Order description |
| orderTime | Order time |
| reduceOnly | Reduce only? |
| status | Order status |
| placeType | Order type: `DEFAULT`, `OTOCO`, `STEP_REDUCE_POSITION`, `LIQUIDATION_TAKE_OVER`, `ADL_TRIGGER`, `ADL_LUCKY` |
| takeProfitPrice| Order take profit stop loss take-profit price |
| cancelSize | Cancellation quantity |
| clientOid	| Customer order S/No. |

# Position

## Get the position of a contract
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
### HTTP Request
`GET /api/v2/symbol-position`
### API Permissions
This API requires `General` permissions
### Parameters
Parameters | Data Type | Compulsory? | Definitions |  
--------- | ------- | -----------| -----------|
symbol | String | Yes | Contract Symbol
### Return Value
Attribute  | Definitions |  
--------- | -----------|
symbol | Contract Symbol | 
qty | Position quantity (negative represents short, while positive represents long) | 
leverage | Global leverage | 
marginType | Margin mode (`ISOLATED` \- isolated, `CROSSED` \- global) | 
side | Position side (`BOTH`, `LONG`, `SHORT`), when the position mode is a one-way position (currently only one-way positions are supported), the contract can only have one position, `side=BOTH`. When the position mode is two-way position, the contract can have two reverse positions, `side=LONG or SHORT` | 
autoDeposit | Auto deposit margin? (when `true`, liquidation triggers an automatic deposit margin) | 
entryPrice | Average entry price | 
entryValue | Open position value | 
margin | Position margin | 
totalMargin | Position total margin (includes unrealized PNL) | 
liquidationPrice | Liquidation price | 
unrealisedPnl | Unrealized PNL | 
riskRate | Position risk rate (`<1`, the greater the value, the greater the risk) | 
maintenanceMarginRate | Position maintenance margin rate | 
maintenanceMargin | Maintenance margin | 
adlPercentile | ADL ranking information | 

## Get the position of all contracts
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
### HTTP Request
`GET /api/v2/all-position`
### API Permissions
This API requires `General` permissions
### Parameters
`N/A`
### Return Value
Attribute  | Definitions |  
--------- | -----------|
symbol | Contract Symbol | 
qty | Position quantity (negative represents short, while positive represents long) | 
leverage | Global leverage | 
marginType | Margin mode (`ISOLATED` \- isolated, `CROSSED` \- global) | 
side | Position side (`BOTH`, `LONG`, `SHORT`), when the position mode is a one-way position (currently only one-way positions are supported), the contract can only have one position, `side=BOTH`. When the position mode is two-way position, the contract can have two reverse positions, `side=LONG or SHORT` | 
autoDeposit | Auto deposit margin? (when `true`, liquidation triggers an automatic deposit margin) | 
entryPrice | Average entry price | 
entryValue | Open position value | 
margin | Position margin | 
totalMargin | Position total margin (includes unrealized PNL) | 
liquidationPrice | Liquidation price | 
unrealisedPnl | Unrealized PNL | 
riskRate | Position risk rate (`<1`, the greater the value, the greater the risk) | 
maintenanceMarginRate | Position maintenance margin rate | 
maintenanceMargin | Maintenance margin | 
adlPercentile | ADL ranking information | 

## Increase or decrease position margin
```json
{
    "code": "200000",
    "data": true
}
```
### HTTP Request
`POST /api/v2/change-margin`
### API Permissions
This API requires `Trade` permissions
### Parameters
Parameters | Data Type | Compulsory? | Definitions |  
--------- | ------- | -----------| -----------|
symbol | String | Yes | Contract Symbol
positionSide | String | Yes | Currently only supports `BOTH`
amount | BigDecimal | Yes | Increase in margin amount. Negative means withdraw margin, while positive means increase position margin
### Return Value
Attribute  | Definitions |  
--------- | -----------|
data | `true` represents successful increase or decrease in margin | 

## Position PNL History
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
### HTTP Request
`GET /api/v2/close-pnl-his`
### API Permissions
This API requires `General` permissions
### Parameters
Parameters | Data Type | Compulsory? | Definitions |  
--------- | ------- | -----------| -----------|
symbol | String | No | Contract Symbol
type | String | No | Close position type
settleCurrency | String | No | Contract settlement currency type
startAt | String | No | Start time (maximum time span `3` months)
endAt | String | No | End time (maximum time span `3` months)
limit | int | No | Number of records (default `50`, max `1000`)

<aside class="notice">
If <code>startAt</code> and <code>endAt</code> is not transmitted, returns the past <code>7</code>  days’ data by default.
The interval between <code>startAt</code> and <code>startAt</code> cannot exceed <code>3</code> months
</aside>

* `type` Close position type description:
    * `CLOSE_LONG` (manual close position)
    * `CLOSE_SHORT` (manual close position)
    * `LIQUID_LONG` (forced close position)
    * `LIQUID_SHORT` (forced close position)
    * `ADL_LONG` (ADL close position)
    * `ADL_SHORT` (ADL close position)


### Return Value
Attribute  | Definitions |  
--------- | -----------|
symbol | Contract Symbol| 
settleCurrency | Contract settlement currency type | 
type | Close position type | 
leverage | Global leverage when position was closed | 
pnl | Close position PNL | 
roe | ROE | 
openTime | Open position time | 
closeTime |Close position time  | 
openPrice | Entry price | 
closePrice | Closing Price | 

# Funding Fees

## Query Funding Fee Settlement History
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
### HTTP Request
`GET /api/v2/funding-history`
### Parameters
Parameters | Data Type | Compulsory? | Definitions |  
--------- | ------- | -----------| -----------|
| symbol  | String   | Yes | Contract Symbol                                            |
| startAt | Long     | No  | Start time (ms)                               |
| endAt   | Long     | No  | End time (ms)                               |
| limit   | Integer  | No  | Default `50`, max `1000`.                             |
| fromId  | Long     | No  | Which transaction `ID` to start returning from. By default, the latest transaction record will be returned |

<aside class="notice">
If <code>startAt</code> and <code>endAt</code> is not transmitted, returns the past <code>7</code>  days’ data by default.
The interval between <code>startAt</code> and <code>startAt</code> cannot exceed <code>3</code> months
</aside>

### Return Value
| Field   | Definitions   |
| ------ | ------ |
| id | id |
| uid | User UID |
| symbol | Contract Symbol |
| timePoint | Time point (ms) |
| fundingRate | Funding rate |
| markPrice | Mark Price: |
| qty | Position quantity at settlement |
| entryValue | Position value at settlement |
| funding | Funding fee at settlement. Positive value represents income, while negative value represents expense. |
| settleCurrency | Settlement currency type |

---
# Market Data
Market data is public and doesn’t require a validated signature.

# Contract

## Get the information for all open contracts
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
### HTTP Request
`GET /api/v2/contracts/active`
### Parameters
`N/A`
### Return Value
Attribute  | Definitions |  
--------- | -----------|
symbol | Contract Symbol | 
type | Contract type: `FFWCSX` (perpetual), `FFICSX`(delivery) | 
firstOpenDate | First open date | 
expireDate | Contract maturity date (perpetual contracts do not expire) | 
settleDate | Delivery contract delivery time | 
baseCurrency | Base currency | 
quoteCurrency | Quote currency | 
settleCurrency | Settlement currency | 
maxOrderQty | Max Order Quantity | 
maxPrice | Max Order Price | 
lotSize | Minimum contrat size | 
tickSize | Minimum price change | 
indexPriceTickSize | Index price change step size | 
multiplier | Contract multiplier | 
makerFeeRate | “maker” handling fee rate | 
takerFeeRate | “taker” handling fee rate | 
settlementFeeRate | Delivery contract delivery time handling fee rate | 
isInverse | Is it a coin-margined contract? | 
fundingBaseSymbol | Funding fee base currency symbol | 
fundingQuoteSymbol | Funding fee quote currency symbol | 
fundingRateSymbol | Funding rate symbol | 
indexSymbol | Index symbol | 
settlementSymbol | Settlement symbol | 
status | Status: `Open` (online)`, PrepareSettled` (ready to settle), `BeingSettled` (settlement in progress), `Paused` (paused), `CancelOnly` (can only be canceled) |  


## Get a Certain Contract
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
### HTTP Request
`GET /api/v2/contracts/{symbol}`
### Parameters
Parameters | Data Type | Compulsory? | Definitions |  
--------- | ------- | -----------| -----------|
symbol | String | Yes | Contract Symbol
### Return Value
Attribute  | Definitions |  
--------- | -----------|
symbol | Contract Symbol | 
type | Contract type: `FFWCSX` (perpetual), `FFICSX`(delivery) | 
firstOpenDate | First open date | 
expireDate | Contract maturity date (perpetual contracts do not expire) | 
settleDate | Delivery contract delivery time | 
baseCurrency | Base currency | 
quoteCurrency | Quote currency | 
settleCurrency | Settlement currency | 
maxOrderQty | Max Order Quantity | 
maxPrice | Max Order Price | 
lotSize | Minimum contrat size | 
tickSize | Minimum price change | 
indexPriceTickSize | Index price change step size | 
multiplier | Contract multiplier | 
makerFeeRate | “maker” handling fee rate | 
takerFeeRate | “taker” handling fee rate | 
settlementFeeRate | Delivery contract delivery time handling fee rate | 
isInverse | Is it a coin-margined contract? | 
fundingBaseSymbol | Funding fee base currency symbol | 
fundingQuoteSymbol | Funding fee quote currency symbol | 
fundingRateSymbol | Funding rate symbol | 
indexSymbol | Index symbol | 
settlementSymbol | Settlement symbol | 
status | Status: `Open` (online)`, PrepareSettled` (ready to settle), `BeingSettled` (settlement in progress), `Paused` (paused), `CancelOnly` (can only be canceled) | 

## Get Contract’s Risk Limit List
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
### HTTP Request
`GET /api/v2/contracts/risk-limit/{symbol}`
### Parameters
Parameters | Data Type | Compulsory? | Definitions |  
--------- | ------- | -----------| -----------|
symbol | String | Yes | Contract Symbol
### Return Value
Attribute  | Definitions |  
--------- | -----------|
symbol | Contract Symbol | 
level | Limit level | 
maxRiskLimit | Maximum risk limit for that level | 
minRiskLimit | Minimum risk limit for that level | 
maxLeverage | Maximum available leverage for that level | 
initialMarginRate | Initial margin rate for that level | 
maintenanceMarginRate | Maintenance margin rate used when the position value is at the level’s limit | 

## Get the Contract’s K-Line Data
```json
{
    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": [
        [
            "1649642340000", // Date
            15.748, //Opening price
            15.748, //Max price
            15.736, //Min price
            15.736, //Closing price
            656 //Volume
        ]
    ]
}
```
### HTTP Request
`GET /api/v2/kline/query`
### Parameters
Parameters | Data Type | Compulsory? | Definitions |  
--------- | ------- | -----------| -----------|
| granularity | Integer | Yes |Represents minute count. Selectable range: `1`, `5`, `15`, `30`, `60`, `120`, `240`, `480`, `720`, `1440`, `10080` |
| symbol   | String | Yes |Contract Symbol |
| from   |  Long  | No | Initial time (ms) |
| to   | Long | No | Ending time (ms) |

## Query Funding Rate List
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
### HTTP Request
`GET /api/v2/contract/{symbol}/funding-rates`
### Parameters
Parameters | Data Type | Compulsory? | Definitions | 
--------- | ------- | -----------| -----------|
| symbol   | String   | Yes | Contract Symbol |
| startAt | Long   | No | Start time |
| endAt | Long   | No | End time |
| offset | Long | No | offset time point (ms)	|
| limit | Integer | No | Default `50`, max `1000`.	|

<aside class="notice">
   When querying the funding rate, when the <code>startAt</code> and <code>endAt</code> parameters are specified, the funding rate during this period will be queried. If <code>hasMore</code> is <code>true</code> in the returned data, it means that there is data on the next page, then you can set the <code>offset</code> to the <code>timePoint</code> of the last funding rate, and then continue to turn pages to query the data.
</aside>

### Return Value
| Field   | Definitions   |
| ------ | ------ |
| symbol | Contract Symbol |
| granularity | Time granulity |
| timePoint | Time point (ms) |
| value | Funding rate |
| hasMore | Is there data on the next page |

## Get Current Funding Rate
```json
{
    "code": "200000",
    "data": {
        "symbol": ".BTCUSDTMFPI8H",
        "granularity": "28800000",
        "timePoint": "1659312000000",
        "value": "0.000100",
        "predictedValue": "0.000081"
    }
}
```
### HTTP Request
`GET /api/v2/funding-rate/{symbol}/current`
### Parameters
Parameters | Data Type | Compulsory? | Definitions |  
--------- | ------- | -----------| -----------|
| symbol   | String | Yes |Contract Symbol |
### Return Value
| Field   | Definitions   |
| ------ | ------ |
| symbol | Funding Rate Symbol |
| granularity | Granularity (milisecond)) |
| timePoint | Time point (milisecond) |
| value | Funding rate |
| predictedValue | Predicted funding rate |

## Get the Contract’s Mark price
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
### HTTP Request
`GET /api/v2/mark-price/{symbol}/current`
### Parameters
Parameters | Data Type | Compulsory? | Definitions |  
--------- | ------- | -----------| -----------|
| symbol   | String   | Yes | Contract Symbol |
### Return Value
| Field   | Definitions   |
--------- | -----------|
symbol | Contract Symbol
timePoint | Time point (ms)
value | mark price value
indexPrice | indexPrice |

# Order Book

## Get Order Book
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
Return Order Book Data
### HTTP Request
`GET /api/v2/order-book`
### Parameters
| Field   | Type   | Compulsory? | Description                                |
| ------ | ------ | -------- | ----------------------------------- |
| symbol | String | Yes        | Contract Name                            |
| limit  | Numeral   | No        | Order book depth, default `500`, range `1` - `1000` |
### Return Value
| Field       | Definitions     |
| ---------- | -------- |
| contractId | Contract ID   |
| symbol     | Contract Name |
| ts         | Timestamp   |
| sequence   | Snapshot s/no. |
| asks       | Asks     |
| bids       | Bids     |

## Best Maker
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
Returns the best bid and offer price in the order book
### HTTP Request
`GET /api/v2/ticker/bookTicker`
### Parameters
| Field   | Type   | Compulsory? | Description     |
| ------ | ------ | -------- | -------- |
| symbol | String | Y        | Contract Name |
### Return Value
| Field     | Definitions       |
| -------- | ---------- |
| symbol   | Contract Name   |
| askPrice | Best offer price |
| askSize  | Offer quantity   |
| bidPrice | Best Bid Price |
| bidSize  | Bid quantity   |


# Quotes Snapshot

## Get the latest transaction price
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
Return the latest transaction price
### HTTP Request
`GET /api/v2/ticker/price`
### Parameters
| Field   | Type   | Compulsory? | Description     |
| ------ | ------ | -------- | -------- |
| symbol | String | Y        | Contract Name |
### Return Value
| Field   | Definitions       |
| ------ | ---------- |
| symbol | Contract Name   |
| ts     | Transaction timestamp |
| price  | Fill price   |




## Get Most Recent Record
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
Return the Most Recent Transaction Record
### HTTP Request
`GET /api/v2/trades`
### Parameters
| Field   | Type   | Compulsory? | Description                                 |
| ------ | ------ | -------- | --------------------------------- |
| symbol | String | Y        | Contract Name                            |
| limit  | Numeral   | N        | Number of records returned. Defaults to `20`, range `1` - `1000`|
### Return Value
| Field   | Definitions                           |
| ------ | ----------------------------- |
| symbol | Contract Name                       |
| ts     | Transaction timestamp                     |
| side   | “taker” side, enumeration value: `buy` or `sell` |
| price  | Fill price                       |
| size   | Order volume                       |


# Date
## Get Server Time

```json
{
    "code": "200000",
    "msg": "success",
    "data": 1546837113087
}
```

Get API server time. This is a unix timestamp.

### HTTP Request
`GET /api/v1/timestamp`

### Return Value
Field | Definitions
--------- | -------
data | Server time, Unix timestamp.


# Server Status

## Get the current server status.
```json
{
    "code": "200000",
    "data": {
        "status": "open", // “open”: normal operation, “close”: server shutdown, “cancelonly”: can only be canceled
        "msg": "upgrade match engine" // Remarks
    }
}
```
Get the current server status.
### HTTP Request
`GET /api/v1/status`

### Return Value
Field | Definitions
--------- | -------
status | Server status: `open` (normal operation), `close` (server shutdown), `cancelonly` (can only be canceled)
msg | Remarks



---
# Websocket
The use of REST API is limited by the frequency of access, so we recommend that you use Websocket to get real-time data.

**We recommend that you create a Websocket connection and multi-channel subscription to get real-time data.**

## Request Connection Token

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

Before creating a Websocket connection, you need to request a token (Token).



### Public token (sign-in not required)

If you subscribe to public channel data only, please request for server list and temporary public token as follows.

#### HTTP Request
`POST /api/v1/bullet-public`

### Private Channel (sign-in required)

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

If you need to request data from private channels (e.g., account fund changes), please obtain the service instance of Websocket and the verified token after signature verification as follows.


#### HTTP Request
`POST /api/v1/bullet-private`


### Return Value

|Field | Definitions|
-----|-----
|pingInterval| Time interval between messages sent (ms)|
|pingTimeout| If you haven’t received a “pong” message after the “pingTimeout” time, then the connection may have been broken. |
|endpoint| Server address for Websocket to establish a connection |
|protocol| Supported protocol |
|encrypt| Represents whether SSL encryption is used |
|token | Token |

## Establish Connection

```javascript
var socket = new WebSocket("wss://push.kucoin.com/endpoint?token=xxx&[connectId=xxxxx]");
```

After successfully establishing the connection, you will receive a welcome message sent to you by the system.

```json
{
    "id": "hQvf8jkno",
    "type": "welcome"
}
```

**connectId**\: Connection ID. This is the client’s generated UID. The ID of the welcome message you receive when you create a connection and the ID of the error message both belong to the connection ID (connectId).


## Ping
```json
{
    "id": "1545910590801",
    "type": "ping"
}
```

To prevent the server from disconnecting the TCP connection, the client needs to send a ping message to the server to keep the connection alive.

After the server receives the ping message, the system returns a pong message to the client.

If the server does not receive a ping message from the client within 60 seconds, the connection will be dropped.


```json
{
    "id": "1545910590801",
    "type": "pong"
}
```

## Subscription Data

```json
{
    "id": 1545910660739, // A unique value representing an ID 
    "type": "subscribe",
    "topic": "/futuresMarket/ticker:BTCUSDTM", // The subscribed channel. Some channels support the use of "," to separately subscribe to the information push of multiple contracts.
    "privateChannel": false, // Whether to use private channels. The default setting is "false".
    "response": true // Whether the server needs to return the information pushed by this channel. Default configuration is “false”.
}
```

When using a server to subscribe to messages, the client should send a subscribe message to the server.

After successfully subscribing, when the `response` parameter is `true`, then the server will send you an `ack` message.

```json
{
    "id": "1545910660739",
    "type": "ack"
}
```

When the subscription channel generates new messages, the system will push messages to the client. For the message format, check out the channel introduction.


### Parameters
#### ID
The ID is a unique string used to identify requests and acks.

#### Topic
The content of the channel you subscribed to.

#### PrivateChannel

You can subscribe to some user-private “topic”s (eg: `/contractMarket/level2`) through the privateChannel parameter. This parameter’s default configuration is `false`. When set to `true`,you can only receive content pushes related to the topics you subscribe to.

#### Response
When set to `true`, after a user successfully subscribes, the system will return an ack message.

## Unsubscribe
Used to unsubscribe to a “topic” you previously subscribed to

```json
  {
    "id": "1545910840805",                            // A unique value representing an ID 
    "type": "unsubscribe",
    "topic": "/futuresMarket/ticker:BTCUSDTM",      // The channel you unsubscribed from. Some channels support the use of "," to separately unsubscribe to messages for multiple trading pairs.
    "privateChannel": false, 
    "response": true,                                  // Whether the server needs to return the information pushed by this channel. Default configuration is “false”.


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

### Parameters

#### ID
A unique string used to identify requests.

#### Topic
The content of a “topic” you subscribed to.

#### PrivateChannel
You can subscribe to some public “topic”s (eg: `/contractMarket/tradeOrders`) through the privateChannel parameter. This parameter’s default configuration is `false`. When set to `true`, you can only receive content pushes related to the topics you subscribe to.

#### Response
If set to `true`, after a user successfully subscribes, the system will return an `ack` message.

## Multiplexing
 On a physical connection, you can turn on multiple multiplexing channels to subscribe to different topics and get multiple data pushes.

Eg:
Please enter the following command to open multiple “bt1” channels
 `{"id": "1Jpg30DEdU", "type": "openTunnel", "newTunnelId": "bt1", "response": true}`

Add the parameter \*\*`tunnelId`\** to the designation:
`{"id": "1JpoPamgFM", "type": "subscribe", "topic": "/futuresMarket/ticker:BTCUSDTM"，"tunnelId": "bt1", "response": true}`

Upon successful request, you will receive a push message corresponding to the **`tunnelIId`**:
`{"id": "1JpoPamgFM", "type": "message", "topic": "/futuresMarket/ticker:BTCUSDTM", "subject": "trade.ticker", "tunnelId": "bt1", "data": {...}}`

To close the \*\*`tunnel`\*\*, enter the following command.
`{"id": "1JpsAHsxKS", "type": "closeTunnel", "tunnelId": "bt1", "response": true}`

##### Restrictions

1. Multiplexing is restricted to API users only.
2. The maximum number of multiplexing channels that can be opened: 5.

## Serial Numbering
Order book datamining, transaction history data, and snapshot messages all return the “sequence” field by default. You can determine whether data is lost and whether the connection is stable from the “sequence”s in the “Level 2” quotes ticker data. If the connection is unstable, please follow the calibration procedure.

## Client Mesage Determination Logic

1. Determine message type. Currently, there are three types of messages:
    message (common push message)
    notice (notice)
    command (continuous commands)
2. The type is determined through the “userId”. Messages with “userId” are private messages, otherwise they are general messages.
3. Determine the message type bythe “topic”. The message type can be determined through the “topic”.
4. Determine the message type through the “subject”. For different types of messages under the same “topic”, the message type can be determined by the “subject”.


# Public Channel

## Quotes Real Time Ticker
```json
//Subscription Example
  {
    "id": 1545910660740,                          
    "type": "subscribe",
    "topic": "/futuresMarket/ticker:BTCUSDTM",
    "response": true                              
  }
```
```json
//Return Example
{
    "type": "message",
    "topic": "/futuresMarket/ticker:BTCUSDTM",
    "subject": "ticker",
    "sn": 355878165,
    "data": {
        "symbol": "BTCUSDTM",// Contract Symbol
        "sequence": 355878165,// Sequence
        "bestBidSize": 18180,// Best bid price total quantity
        "bestBidPrice": "21198.0",// Best bid price
        "bestAskPrice": "21200.0",// Best offer price
        "bestAskSize": 27,// Best offer price total quantity
        "ts": 1658902750616// Transaction time - ms
    }
}
```

Topic: `/futuresMarket/ticker:{symbol}`

* Push frequency: real-time

Subscribe to this topic to get data pushes on the best bid and offer prices (BBO) for the specified trading pair.
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

## Level2 Market Quotes

```json
//Subscription Example
{
    "id": 1545910660740,
    "type": "subscribe",
    "topic": "/futuresMarket/level2:BTCUSDTM",
    "response": true
}
```
```json
//Return Example
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

* Push frequency: once every `100ms`

Subscribe to this topic, then get the “Level 2” order book data. After subscribing, the “Websocket” system will push you messages of incremental data.


Calibration Process:

1. Subscribe to `/futuresMarket/level2:BTCUSDTM`
2. Start caching incoming updates. For the same price, updates received later will overwrite the previous ones.
3. Access the “Rest” API `/api/v2/order-book?symbol=BTCUSDTM&limit=1000` to get a 1000-depth snapshot.
4. Discard the part of the currently cashed information: `end`< `start` of the snapshot obtained in step 3 from, the information currently cached (discard the earlier information which has expired)
5. Update the contents of the depth snapshot to the local “orderbook” copy, save the `lastUpdateId` = `end` of the last incremental message locally, and continue updating the local copy from the first event received by the “websocket” with `start <= lastUpdateId` and `end > lastUpdateId`.
6. The `start` of each new event should be less than or equal to the `end` of the previous “event”, otherwise packet loss may occur, and you should re-initialize from “step 3”.
7. The maker order volume in each “event” represents the absolute value of the current maker volume at this price, instead of the relative change.
8. If the corresponding maker volume at a certain price is `0`, it means that the maker at that price has been canceled or eaten and should be removed.
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

## Transaction records 
```json
//Subscription Example
{
    "id": 1545910660741,
    "type": "subscribe",
    "topic": "/futuresMarket/execution:BTCUSDTM",
    "response": true
}
```
```json
//Return Example
{
    "topic": "/futuresMarket/execution:BTCUSDTM",
    "subject": "execution",
    "data": {
        "symbol": "BTCUSDTM", // Contract
        "matchSide": "sell", // Transaction side (buy/sell)
        "size": 1, // Fill quantity
        "price": 3200.00, // Fill price
        "tradeId": 21518, // Transaction s/no.
        "ts": 1650447324950 // Time in ms
    }
}
```

Topic: `/futuresMarket/execution:{symbol}`

* Push frequency: real-time

Every time an order is matched, the system will push the order transaction record message


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


## 5-depth full volume data push channel of “Level2” 

Topic: `/futuresMarket/level2Depth5:{symbol}`

* Push frequency: once every `100ms`

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
       "ts": 1650447324950 // Time in ms
    }
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
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>



## 50-depth full volume data push channel of “Level2”

Topic: `/futuresMarket/level2Depth50:{symbol}`

* Push frequency: once every `100ms`

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
        "ts": 1650447324950 // Time in ms
    }
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
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>


## Mark price, index price

Topic: `/futuresContract/markPrice`

* Push frequency: once every `1s`

```json
  //Mark price, index price
{
    "type": "message",
    "topic": "/futuresContract/markPrice",
    "subject": "mark.index.price",
    "sn": 123123,
    "data": {
        "symbol": "BTCUSDTM", // Contract Symbol
        "granularity": 1000, // Granularity
        "indexPrice": 4000.23, // Index price
        "markPrice": 4010.52, // Mark price
        "ts": 1551770400000 // Time in ms
    }
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


## Funding rate

Topic: `/futuresContract/fundingRate:{symbol}`

* Push frequency: once every `8h`

```json
 //Funding rate
{
    "type": "message",
    "topic": "/futuresContract/fundingRate:BTCUSDTM",
    "subject": "funding.rate",
    "sn": 25997405694459904,
    "data": {
        "granularity": 60000, // Granularity (predicted funding rate: 1-minute granularity 60,000; funding rate: 8-hour granularity 28800000)
        "fundingRate": -0.002966, // Funding rate
        "ts": 1551770400000
    }
}
```

* `granularity` Granularity description:
    * `60000` (1-minute granularity)
    * `28800000` (8-hour granularity)

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

# Private Message

## Order Private Message

Topic: `/futuresTrade/orders`

* Push frequency: real-time

```json
{
    "type": "message",
    "topic": "/futuresTrade/orders",
    "subject": "orderChange",
    "channelType": "private",
    "data": {
        "orderId": "5cdfc138b21023a909e5ad55", // Order number
        "tradeId": "123", // Matching trade ID
        "symbol": "BTCUSDTM", // Contract symbol
        "eventType": "match", // Message type. List of values: "open", "match", "filled", "canceled" , "adl", "liquidation"
        "status": "MATCHING", // Order status "MATCHING", "FINISH"
        "matchSize": "", // Order volume (included when type is “match”)
        "matchPrice": "", // Fill price (included when type is “match”)
        "orderType": "LIMIT", // Order type, where "MARKET" represents a market order and "LIMIT" represents a limit order
        "side": "BUY", // Order side, buy or sell
        "price": "3600", // Order Price
        "size": "20000", // Order quantity
        "remainSize": "20001", // Order’s remaining quantity that can be used for trades
        "filledSize": "20000", // Order transacted volume
        "canceledSize": "0", // “canceled” message in progress, reduced quantity for the order
        "clientOid": "5ce24c16b210233c36ee321d", // User-defined ID
        "orderTime": , // Order placement time in ms
        "liquidity": "maker", // Liquidity: “taker“ or “maker“.
        "ts": // Timestamp in ms,
        "sn": //Serial No., sn
    }
}
```

* `eventType` Message Type Descriptions:
    * `open` (message sent when an order enters the order book)
    * `match` (message sent when an order is filled)
    * `filled` (message sent when the order status changes to DONE due to the transaction is filled)
    * `canceled` (message sent when the order status changes to DONE due to the transaction being canceled)
    * `adl` (ADL order)
    * `liquidation` (liquidation order)
<br/><br/>
* `status` Order Status Descriptions
    * `MATCHING` (matching in progress, means that the order is posted on the spread)
    * `FINISH` (order completed)
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

## Stop Loss Order Lifecycle Listening Events

Topic: `/futuresTrade/stopOrder`

* Push frequency: real-time

```json
{
    "topic": "/futuresTrade/stopOrder",
    "subject": "stopOrder",
    "data": {
        "orderId": "5cdfc138b21023a909e5ad55", // Order S/No.
        "symbol": "BTCUSDTM", // Contract symbol
        "eventType": "open", // Message type: open (stop loss order placement successful), triggered (stop loss order triggered), cancel (stop loss order canceled)
        "orderType": "STOP", // Order Type: STOP, STOP_MARKET, TAKE_PROFIT, TAKE_PROFIT_MARKET
        "side": "BUY", // Order buy-sell side
        "size": "1000", // Quantity
        "orderPrice": "9000", // Order price
        "stopPrice": "9100", // Stop loss order stop price grid
        "workingType": "TP", // Stop loss order stop price grid type
        "triggerSuccess": true, // Trigger success mark, only required by “triggered” type messages
        "error": "error.createOrder.accountBalanceInsufficient", // Error code, used when triggering failed
        "createdAt": 1558074652423, // Creation time
        "ts": 1233123123 // Message time in ms
    }
}
```
* `eventType` Descriptions:
    * `open` (stop loss order successful)
    * `triggered` (stop loss order triggered)
    * `canceled` (stop loss order canceled)
<br/><br/>
* `orderType` Descriptions:
    * `STOP` (stop limit order)
    * `STOP_MARKET` (stop loss market order)
    * `TAKE_PROFIT` (take profit limit order)
    * `TAKE_PROFIT_MARKET` (take profit market order)

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

## Available Balance Change Events

Topic: `/futuresAccount/accountChange`

* Push frequency: real-time

```json
{
    "type": "message",
    "topic": "/futuresAccount/accountChange",
    "channelType": "private",
    "subject": "futures.availableBalance.change",
    "data": {
        "accountEquity": "999922.7850290000",// Futures account total equity
        "availableBalance": "814058.3002530800",// Available balance
        "availableTransferBalance": "814058.3002530800",// Transferable amount
        "event": "ORDER_MARGIN_CHANGE_EVENT",// Message event
        "currency": "USDT",// Transfer out frozen
        "holdBalance": "0.0000000000",// Transfer out frozen
        "orderMargin": "185863.9384800000",// Order margin
        "positionMargin": "0.5594959200",// Position margin
        "walletBalance": "999922.7982290000",// Wallet balance
        "sn": 11004,// S/No.
        "ts": 1650426007488//Message time in ms
    }
}
```

* `event` Descriptions:
    * `ORDER_MARGIN_CHANGE_EVENT` (Order margin change event)
    * `POSITION_CHANGE_EVENT` (Position change event)
    * `TRANSFER_EVENT` (Transfer event)


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


## Position Change
### Position change caused by position manipulations

Topic: `futuresPosition/position`

* Push frequency: real-time

```json
{
    "type": "message",
    "topic": "/futuresPosition/position",
    "channelType": "private",
    "subject": "position.change",
    "sn": 45300,
    "data": {
        "autoDeposit": false,// Auto deposit margin?
        "changeType": "POSITION_CHANGE",// Position change type
        "entryPrice": 14.431,// Average entry price
        "entryValue": -17.3178,// Entry price
        "leverage": 5,// Global leverage
        "liquidationPrice": 17.161,// Liquidation price
        "liquidationValue": -20.59413818,
        "maintenanceMargin": 0.173178,// Maintenance margin
        "maintenanceMarginRate": 0.01,// Position maintenance margin rate
        "margin": 3.44951618,// Position margin
        "marginType": "ISOLATED",// Margin mode
        "markPrice": 14.15,// Mark price
        "openTime": 1650424933902,// Open position time
        "qty": -12,// Position quantity
        "riskRate": 0.0458,// Position risk rate (<1, the greater the value, the greater the risk)
        "settleCurrency": "USDT",// Contract settlement currency type
        "side": "BOTH",// Position side
        "snapshotId": 6558,
        "symbol": "LINKUSDTM",// Contract symbol
        "totalMargin": 3.78731618,// Position total margin (includes unrealized PNL)
        "unrealisedPnl": 0.3378,// Unrealized PNL
        "sn": 12323,// S/No.
        "ts": 1650424935025//Message time in ms
    }
}
```

* `changeType` Descriptions:
    * `MARGIN_CHANGE` (increase or decrease margin position change)
    * `POSITION_CHANGE` (change in the position quantity caused by user transactions)
    * `LIQUIDATION` (liquidation position change)
    * `ADL` (auto deleverage position change)
    * `LEVERAGE_CHANGE` (global leverage position change)
    * `FUNDING_SETTLE` (funding fee settlement position change)
    * `AUTO_DEPOSIT` (auto deposit margin status position change)
    * `SETTLEMENT` (delivery position changes)

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
# Log In to KuCoin
<a href="https://www.kucoin.com">Log In to KuCoin</a>
