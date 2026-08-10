# 005-post 技术方案 (plan.md)

> 对应规格：spec.md | 模块：005-post | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, MyBatis-Plus 3.5.17, Sa-Token

### 1.2 依赖
- ruoyi-common-core（R 响应、BaseController、MapstructUtils）
- ruoyi-common-mybatis（BaseMapperPlus、@DataPermission、PageQuery）
- ruoyi-common-security（Sa-Token）
- ruoyi-common-excel（ExcelBuilder 导出）
- ruoyi-common-redis（@RepeatSubmit）
- ruoyi-common-log（@Log、BusinessType）
- ruoyi-api（PostService 接口供其他模块调用）

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅ | Controller → ISysPostService/SysPostServiceImpl → SysPostMapper → Entity |
| 注解驱动 | ✅ | @SaCheckPermission 权限控制，@DataPermission 数据权限过滤 |
| 查询过滤 | ✅ | MyBatis-Plus LambdaQueryWrapper 构建查询条件 |

## 3. 数据模型

### 3.1 核心表
- `sys_post` — 岗位主表
- `sys_user_post` — 用户-岗位关联（多对多，同属 002-user 模块）

### 3.2 关键字段规则
- `postId`: 雪花ID主键
- `postCode`: 唯一，全局不可重复（FR-005-004），用于业务代码逻辑判断
- `postName`: 唯一，新增/修改时校验（FR-005-003）
- `deptId`: 关联部门，一个岗位属于一个部门
- `postSort`: 排序号
- `postCategory`: 岗位类别编码
- `status`: 0=正常，1=停用，停用时校验是否有已分配用户（FR-005-006）
- `delFlag`: @TableLogic 逻辑删除标记

### 3.3 关联关系
- SysPost → SysUserPost ← SysUser: 用户分配岗位
- SysPost.deptId → SysDept.deptId: 岗位属于部门

## 4. 接口契约

### 4.1 提供接口

| 端点 | 方法 | 说明 | 对应 FR |
|------|------|------|---------|
| `/system/post/list` | GET | 分页查询岗位列表 | FR-005-001 |
| `/system/post/export` | POST | 导出岗位 Excel | FR-005-009 |
| `/system/post/{postId}` | GET | 查询岗位详情 | — |
| `/system/post` | POST | 新增岗位 | FR-005-002 |
| `/system/post` | PUT | 修改岗位 | FR-005-005 |
| `/system/post/{postIds}` | DELETE | 批量删除岗位 | FR-005-007, FR-005-008 |
| `/system/post/optionselect` | GET | 岗位选择框列表（支持按 deptId 或 postIds 筛选） | FR-005-012 |
| `/system/post/deptTree` | GET | 获取部门树（用于岗位筛选） | — |

### 4.2 消费接口
- ISysDeptService.selectDeptTreeList() — 部门树展示

### 4.3 提供给其他模块
- `selectPostsByUserId()` — 按用户 ID 查询岗位
- `countPostByDeptId()` — 按部门 ID 统计岗位数量（FR-005-010）
- `countUserPostById()` — 按岗位 ID 统计已分配用户（FR-005-006, FR-005-008）
- `selectPostByIds()` — 按 ID 列表查询（FR-005-012）
- `PostService` (ruoyi-api) — 跨模块调用接口

## 5. 实现策略

### 5.1 架构模式
标准 CRUD：
- Controller: SysPostController(/system/post)，注入 ISysPostService + ISysDeptService
- Service: ISysPostService 接口 + SysPostServiceImpl 实现（同时实现 PostService API）
- Mapper: SysPostMapper（主表）+ SysUserPostMapper（用户关联） + SysDeptMapper（部门树）

### 5.2 关键算法
- **岗位查询条件构建**：`buildQueryWrapper()` 使用 LambdaQueryBuilder 构建，支持 postCode、postName、status、deptId、创建时间范围筛选
- **删除前置校验**（FR-005-008）：遍历 postIds，通过 `userPostMapper.lambda().eq(SysUserPost::getPostId, postId).exists()` 逐一检查是否有关联用户
- **停用校验**（FR-005-006）：`countUserPostById(postId) > 0` 时抛出 ServiceException
- **选择框筛选**（FR-005-012）：deptId 非空时按部门筛选岗位列表，否则按 postIds 列表查询

### 5.3 错误处理
- ServiceException: 岗位名称重复、岗位编码重复、岗位已被用户使用
- Controller 层：toAjax() 包装操作结果，R.fail() 返回业务校验失败

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| SysPostController | REST API | system/controller/system/SysPostController.java |
| ISysPostService | 业务接口 | system/service/ISysPostService.java |
| SysPostServiceImpl | 业务实现 | system/service/impl/SysPostServiceImpl.java |
| SysPostMapper | 数据访问 | system/mapper/SysPostMapper.java |
| SysPost.java | 岗位实体 | system/domain/SysPost.java |
| SysPostBo.java | 请求体 | system/domain/bo/SysPostBo.java |
| SysPostVo.java | 响应体 | system/domain/vo/SysPostVo.java |
