# feature-101-workflow 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-101-workflow | 最后更新：2026-08-10

## 1. 技术上下文
- Warm-Flow 1.8.9, 条件启用(@ConditionalOnEnable, warm-flow.enabled=true)

## 2. 模块结构
- ruoyi-modules/ruoyi-workflow: 6个Controller + 9个Service + 8个Mapper + LiteFlow组件
- 配置: WarmFlowConfig.java
- 规则链: liteflow/instance-chain.el.xml, task-chain.el.xml

## 3. 控制器清单

| Controller | 路径 | 用途 |
|------------|------|------|
| FlwTaskController | /workflow/task | 任务管理 |
| FlwInstanceController | /workflow/instance | 实例管理 |
| FlwDefinitionController | /workflow/definition | 流程定义 |
| FlwCategoryController | /workflow/category | 流程分类 |
| FlwSpelController | /workflow/spel | SpEL 表达式 |
| TestLeaveController | /workflow/leave | 请假示例 |

## 4. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-modules/ruoyi-workflow/controller/*.java | 6个控制器 |
| ruoyi-modules/ruoyi-workflow/service/*.java | 9个服务接口 |
| ruoyi-modules/ruoyi-workflow/config/WarmFlowConfig.java | 工作流配置 |
| ruoyi-modules/ruoyi-workflow/liteflow/ | LiteFlow 组件(18个) |
