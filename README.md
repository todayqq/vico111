# VICO 接口文档

1. Wss Base Url：wss://fstream.vicoam.com/ws/
2. HTTP Base Url： https://api.vicoam.com
3. Websocket 服务器每 10 秒发送Ping消息，
   * 如果Websocket服务器在 3 分钟之内没有收到 Pong 消息应答，连接会被断开
   * 每过 12 个小时需要断开重新连接
4. 认证 Token 由 Vico 提供，为确认策略隐私安全，连接前还另外需要设置 IP 白名单，请联系 Vico 客服获取 认证 Token 并提供 IP 地址。
5. 所有 U 本位订单均为[双向持仓模式](https://binance-docs.github.io/apidocs/futures/cn/#trade)、且使用全仓保证金模式
6. 所有现货交易使用 USDT 作为稳定币
7. 妥善保存认证 Token，请勿泄露
