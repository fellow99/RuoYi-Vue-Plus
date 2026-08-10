# 003-role 技术方案 (plan.md)

> 对应规格：spec.md | 模块：003-role | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, MyBatis-Plus 3.5.17, Sa-Token

### 1.2 依赖
- ruoyi-common-core（R 响应、BaseController、MapstructUtils）
- ruoyi-common-mybatis（BaseMapperPlus、@DataPermission、PageQuery）
- ruoyi-common-security（Sa-Token、LoginHelper）
- ruoyi-common-excel（ExcelBuilder 导出）
- ruoyi-common-redis（@RepeatSubmit、CacheNames）
- ruoyi-common-log（@Log、BusinessType）
- ruoyi-api（RoleService 接口供其他模块调用）

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅ | Controller → ISysRoleService/SysRoleServiceImpl → SysRoleMapper → Entity |
| 注解驱动 | ✅ | @SaCheckPermission 权限控制，@DataPermission 数据权限过滤 |
| 缓存规范 | ✅ | @CacheEvict 清除 SYS_ROLE_CUSTOM 缓存 |

## 3. 数据模型

### 3.1 核心表
- `sys_role` — 角色主表（继承 BaseEntity 审计字段）
- `sys_role_menu` — 角色-菜单关联（多对多）
- `sys_role_dept` — 角色-部门关联（数据权限）（多对多）
- `sys_user_role` — 用户-角色关联（多对多，同属 002-user 模块）

### 3.2 关键字段规则
- `roleName`: 唯一，≤30 字符，新增/修改时校验（FR-003-002, FR-003-003）
- `roleKey`: 唯一，≤100 字符，禁止使用 superadmin 标识符（FR-003-007）
- `dataScope`: 1=全部 2=自定 3=本部门 4=本部门及以下 5=仅本人 6=部门及以下或本人（FR-003-012）
- `status`: 0=正常，1=停用，停用时踢出在线用户（FR-003-005）
- `delFlag`: @TableLogic 逻辑删除标记

### 3.3 关联关系
- SysRole → SysRoleMenu ← SysMenu: 角色分配菜单权限（FR-003-004）
- SysRole → SysRoleDept ← SysDept: 角色数据权限范围（FR-003-012）
- SysUser → SysUserRole ← SysRole: 用户分配角色（FR-003-009, FR-003-010, FR-003-011）

## 4. 接口契约

### 4.1 提供接口

| 端点 | 方法 | 说明 | 对应 FR |
|------|------|------|---------|
| `/system/role/list` | GET | 分页查询角色列表 | FR-003-001 |
| `/system/role/export` | POST | 导出角色 Excel | FR-003-008 |
| `/system/role/{roleId}` | GET | 查询角色详情 | — |
| `/system/role` | POST | 新增角色 | FR-003-002 |
| `/system/role` | PUT | 修改角色基础信息 | FR-003-003 |
| `/system/role/permission` | PUT | 修改角色权限信息 | FR-003-004 |
| `/system/role/changeStatus` | PUT | 修改角色状态 | FR-003-005 |
| `/system/role/{roleIds}` | DELETE | 批量删除角色 | FR-003-006 |
| `/system/role/optionselect` | GET | 角色选择框列表 | — |
| `/system/role/authUser/allocatedList` | GET | 已分配用户列表 | FR-003-009 |
| `/system/role/authUser/unallocatedList` | GET | 未分配用户列表 | FR-003-009 |
| `/system/role/authUser/cancel` | PUT | 取消授权单个用户 | FR-003-011 |
| `/system/role/authUser/cancelAll` | PUT | 批量取消授权 | FR-003-011 |
| `/system/role/authUser/selectAll` | PUT | 批量授权用户 | FR-003-010 |
| `/system/role/deptTree/{roleId}` | GET | 角色部门树（数据权限回显） | — |

### 4.2 消费接口
- ISysDeptService.selectDeptTreeList() — 部门树构建
- ISysDeptService.selectDeptListByRoleId() — 角色已关联部门查询
- ISysUserService.selectAllocatedList() / selectUnallocatedList() — 用户分配查询

## 5. 实现策略

### 5.1 架构模式
标准 CRUD + 关联管理：
- Controller: SysRoleController(/system/role)，注入 ISysRoleService + ISysUserService + ISysDeptService
- Service: ISysRoleService 接口 + SysRoleServiceImpl 实现（同时实现 RoleService API）
- Mapper: SysRoleMapper(主表) + SysRoleMenuMapper(菜单关联) + SysRoleDeptMapper(部门关联) + SysUserRoleMapper(用户关联)

### 5.2 关键算法
- **角色权限更新**（FR-003-004）：`updateRolePermission()` 先更新 role 表的 dataScope/menuCheckStrictly/deptCheckStrictly 字段，然后删除旧的 roleMenu 和 roleDept 记录，再重新批量插入
- **角色数据权限校验**（FR-003-013）：通过 SysRoleMapper.selectRoleCount() 使用 @DataPermission 注解自动过滤，对比返回的 count 与请求的 roleIds 数量判断是否有权限
- **角色操作保护**（FR-003-007）：checkRoleAllowed() 禁止操作 roleId==1 的记录，禁止使用/修改 superadmin 标识符
- **在线用户踢出**（FR-003-015）：修改角色信息后通过 SpringUtils.context().publishEvent(OnlineUserCleanEvent.byRole(roleId)) 发布事件

### 5.3 错误处理
- ServiceException: 角色名称重复、权限字符重复、无数据权限、超级管理员保护
- Controller 层：toAjax() 包装插入结果，R.fail() 返回业务校验失败

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| SysRoleController | REST API | system/controller/system/SysRoleController.java |
| ISysRoleService | 业务接口 | system/service/ISysRoleService.java |
| SysRoleServiceImpl | 业务实现 | system/service/impl/SysRoleServiceImpl.java |
| SysRoleMapper | 主表数据访问 | system/mapper/SysRoleMapper.java |
| SysRoleMenuMapper | 菜单关联数据访问 | system/mapper/SysRoleMenuMapper.java |
| SysRoleDeptMapper | 部门关联数据访问 | system/mapper/SysRoleDeptMapper.java |
| SysRole.java | 角色实体 | system/domain/SysRole.java |
| SysRoleMenu.java | 角色-菜单关联实体 | system/domain/SysRoleMenu.java |
| SysRoleDept.java | 角色-部门关联实体 | system/domain/SysRoleDept.java |
| SysRoleBo.java | 请求体（含 menuIds, deptIds） | system/domain/bo/SysRoleBo.java |
| SysRoleVo.java | 响应体 | system/domain/vo/SysRoleVo.java |
