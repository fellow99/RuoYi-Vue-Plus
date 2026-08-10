# feature-115-liteflow 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-115-liteflow | 最后更新：2026-08-10

## 1. 技术上下文
- LiteFlow 2.16.0, 配置: liteflow.rule-source=classpath:liteflow/*.el.xml

## 2. 模块结构
- ruoyi-common/ruoyi-common-liteflow: LiteFlowAutoConfiguration + 5个基础组件
- ruoyi-modules/ruoyi-workflow/src/main/resources/liteflow/: instance-chain.el.xml, task-chain.el.xml
- 工作流模块下的 liteflow/ 包: complete/, instance/, operation/, start/ 各含组件实现

## 3. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-liteflow/.../LiteFlowAutoConfiguration.java | 自动配置 |
| ruoyi-workflow/.../liteflow/*/ | 工作流组件实现 |
| ruoyi-workflow/.../resources/liteflow/*.el.xml | 规则链定义 |
