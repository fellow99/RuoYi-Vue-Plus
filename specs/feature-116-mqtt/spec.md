# MQTT 消息协议功能规格 (spec.md)

> 模块：feature-116-mqtt | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
提供 MQTT 物联网消息协议客户端集成，支持连接管理和消息收发。

### 1.2 范围
- ✅ Mica-MQTT 2.6.8 客户端集成
- ✅ 连接/消息监听器
- ✅ Demo 控制器演示

## 2. 功能需求
- FR-116-001: 系统 MUST 支持 MQTT 客户端连接配置
- FR-116-002: 系统 SHOULD 支持 MQTT 消息订阅和发布

## 3. 依赖
- Mica-MQTT 2.6.8
- ruoyi-common-mqtt (MqttAutoConfiguration)
- Demo: MqttController (/demo/mqtt)
