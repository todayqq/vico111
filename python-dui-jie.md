# Python对接

PS: 请先联系 Vico 客服获取认证 Token、并提供 IP 地址设置 IP 白名单

1. 发送 Token 认证并订阅策略
2. 接收消息通知
3. 主动请求策略账户信息



消息类型

* ping，需要主动回复 pong

> // 接收消息\
> {"action" => "ping"}\
> \
> // 主动回复\
> ws.send(json.dumps({"action": "pong"}))

* 开单消息

```
{
	'action': 'notify', // 
	'strategy': 'steady_policy', // 
	'data': {
		'e': 'open_order', //
		'symbol': 'BTCUSDT',
		'lever': 50, // 杠杆
		'side': 'Long', // Long 做多 Short 做空
		'price': '66200.00',// 开单价格
		'quantity': '0.007', // 数量
		'margin': '10', // 保证金
		'total_wallet_balance': '400', // 
		'stop_profit_price': '66600.16', // 
		'stop_loss_price': '66000.00' // 
	}
}
```

代码示例

```
pip install websocket-client
```

```
import websocket
import json
import hashlib

def on_message(ws, message):
    data = json.loads(message)
    if data.get("action") == "ping":
        ws.send(json.dumps({"action": "pong"}))
    elif data.get("action") == "notify":
        # 开平仓信息
        print(data)

def on_error(ws, error):
    print(f"Error: {error}")

def on_close(ws, close_status_code, close_msg):
    print("Connection closed")

def on_open(ws):
    print("Connection established")
    # 发送认证
    ws.send(json.dumps({
        "action": "auth", 
        "token": "0e08a4c731e4fa6484a0f07dc5aeda80",
        "strategy_slug": "spot_test"
    }))
    # 认证成功之后获取U本位合约策略账户信息
    get_account_details('contract-account')
    # 认证成功之后获取U本位合约策略账户挂单列表
    get_account_details('contract-openOrders')
    # 认证成功之后获取现货策略账户信息
    get_account_details('spot-account')
    # 认证成功之后获取现货策略账户挂单列表
    get_account_details('spot-openOrders')

def get_account_details(action):
    ws.send(json.dumps({"action": action}))

if __name__ == "__main__":
    websocket.enableTrace(True)
    url = "wss://fstream.vicoam.com/ws/"
    # url = "ws://localhost:8088"
    ws = websocket.WebSocketApp(url,
                                on_open=on_open,
                                on_message=on_message,
                                on_error=on_error,
                                on_close=on_close)
    ws.run_forever()


```



