# LiteFlow 规则引擎功能规格 (spec.md)

> 模块：feature-115-liteflow | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
LiteFlow 规则引擎模块提供组件化业务编排能力，通过 EL 表达式定义规则链，支持复杂的业务流程编排。

### 1.2 范围
- ✅ LiteFlow 2.16.0 集成
- ✅ EL 表达式规则定义（classpath:liteflow/*.el.xml）
- ✅ 工作流模块集成（流程节点组件）

## 2. 功能需求
- FR-115-001: 系统 MUST 支持 LiteFlow EL 规则定义
- FR-115-002: 工作流模块 SHOULD 使用 LiteFlow 组件进行流程节点编排

## 3. 依赖
- LiteFlow 2.16.0
- ruoyi-common-liteflow
- ruoyi-modules/ruoyi-workflow（消费方）
