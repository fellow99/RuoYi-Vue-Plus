# 002-user 技术方案 (plan.md)

> 对应规格：spec.md | 模块：002-user | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, MyBatis-Plus 3.5.17

### 1.2 依赖
- ruoyi-common-core（基础工具、R 响应）
- ruoyi-common-mybatis（BaseMapperPlus、数据权限）
- ruoyi-common-security（Sa-Token 认证）
- ruoyi-common-excel（导入导出）
- ruoyi-common-oss（头像存储）
- ruoyi-api（跨模块 UserService 接口）

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅ | Controller → Service(接口+实现) → Mapper → Entity |
| 注解驱动 | ✅ | @SaCheckPermission 权限控制 |
| 数据权限 | ✅ | Mapper 方法标注 @DataPermission |

## 3. 数据模型

### 3.1 核心表
- `sys_user` — 用户主表（继承 BaseEntity 审计字段）
- `sys_user_role` — 用户-角色关联（多对多）
- `sys_user_post` — 用户-岗位关联（多对多）

### 3.2 关键字段规则
- `userName`: 唯一，不可重复
- `password`: BCrypt 加密，新增时必填
- `status`: 0=正常，1=停用
- `delFlag`: 逻辑删除标记（@TableLogic）

## 4. 接口契约

### 4.1 提供接口
- `UserService` (ruoyi-api): selectUserNameById, selectNicknameByIds, selectPhonenumberById, selectById 等
- 供 workflow、job、demo 等模块调用

### 4.2 消费接口
- DeptService, RoleService, PostService (ruoyi-api)
- OssService (ruoyi-api, 头像)

## 5. 实现策略

### 5.1 架构模式
标准四层 CRUD：Controller(/system/user) → ISysUserService/SysUserServiceImpl → SysUserMapper → MySQL

### 5.2 关键算法
- 密码验证：BCrypt.checkpw()
- 账号锁定：Redis 计数器，key=`login:failed:{username}`，expire=10min
- 数据权限：@DataPermission 插件自动注入 SQL 条件

### 5.3 错误处理
- `UserException` 系列（CaptchaException, CaptchaExpireException 等）
- 全局异常转 R 响应

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| SysUserController | REST API | system/controller/system/ |
| ISysUserService | 业务接口 | system/service/ |
| SysUserServiceImpl | 业务实现 | system/service/impl/ |
| SysUserMapper | 数据访问 | system/mapper/ |
| SysUser.java | 用户实体 | system/domain/ |
| SysUserBo.java | 请求体 | system/domain/bo/ |
| SysUserVo.java | 响应体 | system/domain/vo/ |
