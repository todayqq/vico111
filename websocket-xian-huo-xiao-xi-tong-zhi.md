# Websocket 现货消息通知

消息通知

```
{
	"action": "notify",
	"strategy_slug": "spot_test",
	"data": {
		"e": "ORDER_FILLED",
		"symbol": "BNBUSDT",
		"price": "567.20000000",
		"order_side": "BUY",  // 订单方向 SELL 卖出 BUY 买入
		"quantity": "0.01000000", , // 成交数量
		"time": 1724141930193
	}
}
```
