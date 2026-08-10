# feature-116-mqtt 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-116-mqtt | 最后更新：2026-08-10

## 1. 技术上下文
- Mica-MQTT 2.6.8, 配置: mqtt.client.enabled=false(默认关闭)

## 2. 模块结构
- ruoyi-common-mqtt: MqttAutoConfiguration + MqttConnectListener + MqttMessageListener
- Demo: MqttController (/demo/mqtt)

## 3. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-mqtt/.../MqttAutoConfiguration.java | MQTT 自动配置 |
| ruoyi-common-mqtt/.../listener/MqttConnectListener.java | 连接事件监听 |
| ruoyi-common-mqtt/.../listener/MqttMessageListener.java | 消息订阅监听 |
| ruoyi-modules/ruoyi-demo/.../MqttController.java | Demo 控制器 |
