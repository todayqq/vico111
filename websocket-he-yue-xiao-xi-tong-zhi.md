# Websocket 合约消息通知

* Base Url：wss://fstream.vicoam.com/ws/

### 开单

```
{
    'action': 'notify', // 通知
    'strategy': 'steady_policy', // 策略类型
    'uuid': '643_66e16bfb4e018', // UUID
    'data': {
        'e': 'OPEN_ORDER', // 
        'symbol': 'BTCUSDT',
        'side': 'BUY', // BUY 做多 SELL 做空
        'position_side' => 'SHORT', // 持仓方向 Long 多 SHORT 空
        'price': '66200.00',// 开单价格
        'quantity': '0.007', // 数量
        'time': 1723103622464 // 微秒时间戳
    }
}
```

参数解释

price 为开仓价格、quantity 为开仓数量

开仓保证金 = (开仓价格\*开仓数量) / 开仓杠杆

开仓杠杆为固定不变的，可以在账户的持仓信息中读取、或者在策略信息中读取

### 止盈止损

```
{
	'action': 'notify',
	'strategy_slug': 'sqc_test',
	'uuid': '643_66e16bfb4e018', // UUID
	'data': {
		'e': 'TAKE_PROFIT_MARKET',
		'symbol': 'BTCUSDT',
		'price': '54000',
		'order_status': 'NEW',
		'order_type': 'TAKE_PROFIT_MARKET',
		'order_side': 'BUY',
		'position_side': 'SHORT',
		'quantity': '0.001',
		'profit_amount': '0',
		'close_position': False,
		'time': 1725866730364
	}
}
```

参数解释

* e：STOP\_MARKET (止损) / TAKE\_PROFIT\_MARKET(止盈)
* order\_status：NEW 为前挂单 / CANCELED 撤销订单&#x20;
* price 触发价&#x20;
* quantity 触发数量，当触发数量为 0 且 close\_position 为 true 时，此时为全仓止盈止损

### 平仓

```
{
	'action': 'notify',
	'strategy_slug': 'sqc_test',
	'uuid': '643_66e16bfb4e018', // UUID
	'data': {
		'e': 'CLOSE_ORDER',
		'order_status': 'FILLED',
		'symbol': 'BTCUSDT',
		'price': '54882',
		'quantity': '0.002',
		'order_type': 'MARKET',
		'order_side': 'BUY',
		'position_side': 'SHORT',
		'profit_amount': '-0.33660000',
		'close_position': False,
		'time': 1725868127799
	}
}
```

参数解释

* order\_type： LIMIT(限价) / MARKET(市价) / STOP\_MARKET(止损) / TAKE\_PROFIT\_MARKET(止盈)
* price 触发价&#x20;
* quantity 触发数量
* profit\_amount 盈亏金额

### 账户充值

充值、提现之后，策略的初始资产会更改

```
{
    'action': 'notify',
    'strategy_slug': 'sqc_test',
    'uuid': '643_66e16bfb4e018', // UUID
    'data': {
        'e': 'CONTRACT-ACCOUNT-DEPOSIT',
        'symbol': 'USDT',
        'balance_change': '1000', // 提现金额
        'current_balance': '1000', // 当前钱包余额
        'time': 1725868127799
    }
}
```

### 账户提现

充值、提现之后，策略的初始资产会更改

```
{
    'action': 'notify',
    'strategy_slug': 'sqc_test',
    'uuid': '643_66e16bfb4e018', // UUID
    'data': {
        'e': 'CONTRACT-ACCOUNT-WITHDRAW',
        'symbol': 'USDT',
        'balance_change': '-1000', // 提现金额
        'current_balance': 0, // 当前钱包余额
        'time': 1725868127799
    }
}
```
