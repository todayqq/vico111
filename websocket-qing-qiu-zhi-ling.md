# WebSocket 请求指令

* Base Url：wss://fstream.vicoam.com/ws/

请求指令

1. spot-account 请求现货账户信息
2. spot-openOrders 请求现货账户挂单
3. contract-account 请求合约账户信息
4. contract-openOrders 请求合约账户挂单
5. contract-symbol-config 请求合约交易对配置
6. auth 登录认证
7. 心跳
8. received 通知消息确认

### 心跳

**Response:**

```
{
    "action": "ping"
}
```

需要在 3 分钟内主动回复 _{"action": "pong"}_ 否则会断开链接

### received 通知消息确认

<pre><code><strong>消息通知示例：
</strong><strong>{
</strong>    'action': 'notify', 
    'strategy_slug': 'sqc_test', 
    'data': {
        'e': 'ORDER_OPEN',
        ...
    }, 
    'uuid': '643_66e16bfb4e018'
}
</code></pre>

在收到消息时，每一条消息都有一个唯一的uuid，需要及时按下面的消息格式进行回复

未收到客户端消息确认消息前，服务器端会每隔 30秒 发送一次消息，最多发送 5 次，发送 5 次消息还没有收到客户端消息确认，将不再发送

**参数:**

```
{
    "action": "received",
    "uuid": xxx
}
```

### WebSocket 认证

认证时填写 Token 和 strategy\_slug 策略标识符\
客户端订阅 A 策略认证成功后，该客户端只会收到 A 策略的开平仓消息

一个客户端只能接收一个策略信息！

**参数:**

```
{
    "action": "auth", 
    "token": token, // token 必填
    "strategy_slug": "steady_policy" // 策略标识符、必填
}
```

**Response：**[\
](#user-content-fn-1)[^1]认证成功

```
{
    "code"   => 200,
    "status" => "OK"
}
```

认证失败

<pre><code><strong>{
</strong><strong>    "code"   => 403,
</strong><strong>    "status" => "unauthenticated"
</strong><strong>}
</strong></code></pre>

### 查询U本位策略账户信息

请求限制：5秒内最多请求一次，请求次数过多将会返回 1003 TOO\_MANY\_REQUESTS

请求参数

```
{
    "action": "contract-account"
}
```

Response：

Response 参数详细请查看币安 [账户信息V2 (USER\_DATA)API 文档](https://binance-docs.github.io/apidocs/futures/cn/#v2-user_data-2)

totalWalletBalance 账户U本位总资产

positions 是一个二维数组 表示当前用户持仓

**positions 数组详解：**

```
entryPrice 持仓成本价
positionInitialMargin 持仓保证金，通过持仓保证金除以仓位总资产 可以得到仓位比
leverage 杠杆倍率
positionAmt  持仓数量
"isolated": true,  // true 全仓 false 逐仓
```

```
{
    "feeTier": 0,  // 手续费等级
    "feeBurn": true,        // "true": 手续费抵扣开; "false": 手续费抵扣关
    "canTrade": true,  // 是否可以交易
    "canDeposit": true,  // 是否可以入金
    "canWithdraw": true, // 是否可以出金
    "updateTime": 0,     // 保留字段，请忽略
    "multiAssetsMargin": false,
    "tradeGroupId": -1,
    "totalInitialMargin": "0.00000000",  // 当前所需起始保证金总额(存在逐仓请忽略), 仅计算usdt资产
    "totalMaintMargin": "0.00000000",  // 维持保证金总额, 仅计算usdt资产
    "totalWalletBalance": "23.72469206",   // 账户总余额, 仅计算usdt资产
    "totalUnrealizedProfit": "0.00000000",  // 持仓未实现盈亏总额, 仅计算usdt资产
    "totalMarginBalance": "23.72469206",  // 保证金总余额, 仅计算usdt资产
    "totalPositionInitialMargin": "0.00000000",  // 持仓所需起始保证金(基于最新标记价格), 仅计算usdt资产
    "totalOpenOrderInitialMargin": "0.00000000",  // 当前挂单所需起始保证金(基于最新标记价格), 仅计算usdt资产
    "totalCrossWalletBalance": "23.72469206",  // 全仓账户余额, 仅计算usdt资产
    "totalCrossUnPnl": "0.00000000",    // 全仓持仓未实现盈亏总额, 仅计算usdt资产
    "availableBalance": "23.72469206",       // 可用余额, 仅计算usdt资产
    "maxWithdrawAmount": "23.72469206"     // 最大可转出余额, 仅计算usdt资产
    "assets": [
        {
            "asset": "USDT",        //资产
            "walletBalance": "23.72469206",  //余额
            "unrealizedProfit": "0.00000000",  // 未实现盈亏
            "marginBalance": "23.72469206",  // 保证金余额
            "maintMargin": "0.00000000",    // 维持保证金
            "initialMargin": "0.00000000",  // 当前所需起始保证金
            "positionInitialMargin": "0.00000000",  // 持仓所需起始保证金(基于最新标记价格)
            "openOrderInitialMargin": "0.00000000", // 当前挂单所需起始保证金(基于最新标记价格)
            "crossWalletBalance": "23.72469206",  //全仓账户余额
            "crossUnPnl": "0.00000000" // 全仓持仓未实现盈亏
            "availableBalance": "126.72469206",       // 可用余额
            "maxWithdrawAmount": "23.72469206",     // 最大可转出余额
            "marginAvailable": true,   // 是否可用作联合保证金
            "updateTime": 1625474304765  //更新时间
        },
        {
            "asset": "BUSD",        //资产
            "walletBalance": "103.12345678",  //余额
            "unrealizedProfit": "0.00000000",  // 未实现盈亏
            "marginBalance": "103.12345678",  // 保证金余额
            "maintMargin": "0.00000000",    // 维持保证金
            "initialMargin": "0.00000000",  // 当前所需起始保证金
            "positionInitialMargin": "0.00000000",  // 持仓所需起始保证金(基于最新标记价格)
            "openOrderInitialMargin": "0.00000000", // 当前挂单所需起始保证金(基于最新标记价格)
            "crossWalletBalance": "103.12345678",  //全仓账户余额
            "crossUnPnl": "0.00000000" // 全仓持仓未实现盈亏
            "availableBalance": "126.72469206",       // 可用余额
            "maxWithdrawAmount": "103.12345678",     // 最大可转出余额
            "marginAvailable": true,   // 否可用作联合保证金
            "updateTime": 0  // 更新时间
            }
    ],
    "positions": [  // 头寸，将返回所有市场symbol。
        //根据用户持仓模式展示持仓方向，即单向模式下只返回BOTH持仓情况，双向模式下只返回 LONG 和 SHORT 持仓情况
        {
            "symbol": "BTCUSDT",  // 交易对
            "initialMargin": "0",   // 当前所需起始保证金(基于最新标记价格)
            "maintMargin": "0", //维持保证金
            "unrealizedProfit": "0.00000000",  // 持仓未实现盈亏
            "positionInitialMargin": "0",  // 持仓所需起始保证金(基于最新标记价格)
            "openOrderInitialMargin": "0",  // 当前挂单所需起始保证金(基于最新标记价格)
            "leverage": "100",  // 杠杆倍率
            "isolated": true,  // 是否是逐仓模式
            "entryPrice": "0.00000",  // 持仓成本价
            "maxNotional": "250000",  // 当前杠杆下用户可用的最大名义价值
            "bidNotional": "0",  // 买单净值，忽略
            "askNotional": "0",  // 卖单净值，忽略
            "positionSide": "BOTH",  // 持仓方向
            "positionAmt": "0",      // 持仓数量
            "updateTime": 0         // 更新时间 
        }
    ]
}
```

### 查询U本位策略当前所有挂单

请求限制：5秒内最多请求一次，请求次数过多将会返回 -1003 TOO\_MANY\_REQUESTS

参数：

```
{
    "action": "contract-openOrders"
}
```

**Response：**

Response 参数详细请查看币安 [查看当前全部挂单 (USER\_DATA) API 文档](https://binance-docs.github.io/apidocs/futures/cn/#user_data-5)

```
origType 挂单类型 STOP_MARKET 止损 TAKE_PROFIT_MARKET 止赢
```

```
{
    "code"   => 200,
    "status" => "OK",
    "data"   =>  {
        {
            "avgPrice": "0.00000",              // 平均成交价
            "clientOrderId": "abc",             // 用户自定义的订单号
            "cumQuote": "0",                        // 成交金额
            "executedQty": "0",                 // 成交量
            "orderId": 1917641,                 // 系统订单号
            "origQty": "0.40",                  // 原始委托数量
            "origType": "TRAILING_STOP_MARKET", // 触发前订单类型
            "price": "0",                   // 委托价格
            "reduceOnly": false,                // 是否仅减仓
            "side": "BUY",                      // 买卖方向
            "positionSide": "SHORT", // 持仓方向
            "status": "NEW",                    // 订单状态
            "stopPrice": "9300",                    // 触发价，对`TRAILING_STOP_MARKET`无效
            "closePosition": false,   // 是否条件全平仓
            "symbol": "BTCUSDT",                // 交易对
            "time": 1579276756075,              // 订单时间
            "timeInForce": "GTC",               // 有效方法
            "type": "TRAILING_STOP_MARKET",     // 订单类型
            "activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
            "priceRate": "0.3", // 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
            "updateTime": 1579276756075,       // 更新时间
            "workingType": "CONTRACT_PRICE",   // 条件价格触发类型
            "priceProtect": false,             // 是否开启条件单触发保护
            "priceMatch": "NONE",              // 盘口价格下单模式
            "selfTradePreventionMode": "NONE", // 订单自成交保护模式
            "goodTillDate": 0                  // 订单TIF为GTD时的自动取消时间
        }
        ...
    }

}
```

### 查询U本位策略交易对配置

请求限制：5秒内最多请求一次，请求次数过多将会返回 -1003 TOO\_MANY\_REQUESTS

所有交易对U本位合约开仓均使用全仓模式，初次使用策略时，可以读取交易对 leverage 用于初始化杠杆配置

参数：

```
{
    "action": "contract-symbol-config"
}
```

**Response：**

```
{
    "code"   => 200,
    "status" => "OK",
    "data"   =>  {
        {
            "symbol": "BTCUSDT",      // 交易对
            "marginType": "CROSSED",  // CROSSED 全仓
            "leverage": 10,  // 杠杆
            "isAutoAddMargin": false,
            "maxNotionalValue" : "1000000"
        }
        ...
    }

}
```

### 查询现货策略账户信息

请求限制：1分钟内最多请求一次，请求次数过多将会返回 1003 TOO\_MANY\_REQUESTS

请求参数

```
{
    "action": "spot-account"
}
```

Response：

Response 参数详细请查看币安 [账户信息 (USER\_DATA)](https://binance-docs.github.io/apidocs/spot/cn/#user_data-42)

```
{
    "code"   => 200,
    "status" => "OK",
    "data"   =>  {
        "makerCommission": 15,
        "takerCommission": 15,
        "buyerCommission": 0,
        "sellerCommission": 0,
        "commissionRates": {
        "maker": "0.00150000",
        "taker": "0.00150000",
        "buyer": "0.00000000",
        "seller": "0.00000000"
        },
        "canTrade": true,
        "canWithdraw": true,
        "canDeposit": true,
        "brokered": false,
        "requireSelfTradePrevention": false,
        "preventSor": false,
        "updateTime": 123456789,
        "accountType": "SPOT",
        "balances": [
        {
          "asset": "BTC",
          "free": "4723846.89208129",
          "locked": "0.00000000"
        },
        {
          "asset": "LTC",
          "free": "4763368.68006011",
          "locked": "0.00000000"
        }
        ],
        "permissions": [
        "SPOT"
        ],
        "uid": 354937868
    }
}
```

### 查询现货策略当前所有挂单

请求限制：1分钟内最多请求一次，请求次数过多将会返回 -1003 TOO\_MANY\_REQUESTS

参数：

```
{
    "action": "spot-openOrders"
}
```

**Response：**

Response 参数详细请查看币安 [当前挂单 (USER\_DATA)](https://binance-docs.github.io/apidocs/spot/cn/#user_data-36)

```
{
	"code": 200,
	"status": "OK",
	"data": [{
		"symbol": "BNBUSDT",
		"orderId": 5698128000,
		"orderListId": -1,
		"clientOrderId": "yD6mETo1WJfSP3PH5eI8J3",
		"price": "500.00000000",
		"origQty": "0.01000000",
		"executedQty": "0.00000000",
		"cummulativeQuoteQty": "0.00000000",
		"status": "NEW",
		"timeInForce": "GTC",
		"type": "LIMIT",
		"side": "BUY",
		"stopPrice": "0.00000000",
		"icebergQty": "0.00000000",
		"time": 1724141547764,
		"updateTime": 1724141547764,
		"isWorking": true,
		"workingTime": 1724141547764,
		"origQuoteOrderQty": "0.00000000",
		"selfTradePreventionMode": "EXPIRE_MAKER"
	}]
}
```

[^1]: 
