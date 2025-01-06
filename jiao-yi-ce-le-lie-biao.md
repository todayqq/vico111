# 交易策略列表

> GET    api/trading-strategies

获取所有交易策略列表

参数：无

Response：

```
[
    {
        "id": 1, // ID 
        "title": "test", // 策略名称
        "slug": "test", // 策略标识符
        "content": "test", // 策略介绍
        "type": 1 // 策略类型 1 现货 2 U本位合约 3 币本位合约 4 期权
        "lever": 10, // 杠杆
        "max_position_count": 3, // 最大持仓币种
        "open_times": 0, // 开仓次数
        "annualized_income": 0, // 收益率
        "max_back": 0, // 最大回测
        "min_order_amount": 10, // 最小跟单金额
        "symbols": [ // 交易交易对
            "BTCUSDT",
            "ETHUSDT"
        ]
    },
    ...
]
```

> GET    api/trading-strategies/{id}/log

获取所有交易策略历史交易记录

参数：id 策略ID

Response：

```
[
    {
        "symbol": "BTCUSDT",  // 交易对
        "date": "2024-11-01 00:00:00", // 日期
        "open_price": "68000.00000000", // 开仓价
        "close_price": "72000.00000000", // 平仓价
        "lever": 10, // 杠杆
        "side": 1, // 方向 1做多 2做空
        "income": 100, // 收益金额
        "income_rate": 20 //收益率
    }
]
```
