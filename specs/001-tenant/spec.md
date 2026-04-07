# 001-租户管理 - 功能规格

**模块编号：** 001  
**模块名称：** 租户管理 (Tenant Management)  
**所属模块：** 系统管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、功能概述

租户管理是 RuoYi-Vue-Plus 的核心特性模块，为 SaaS 多租户场景提供完整的租户生命周期管理能力。通过该模块，系统管理员可以创建、管理和监控不同租户，实现数据隔离、资源分配和权限控制。

### 1.1 核心功能

- **租户 CRUD**：新增、查询、修改、删除系统租户
- **套餐管理**：为租户分配功能套餐，控制可用菜单
- **租户初始化**：创建租户时自动初始化角色、部门、用户、字典、参数
- **状态控制**：启用/停用租户账号
- **动态切换**：超级管理员可动态切换到指定租户进行运维
- **数据同步**：同步字典、参数、套餐到所有租户
- **租户隔离**：基于 MyBatis-Plus 插件实现数据自动隔离

### 1.2 与 RuoYi-Vue 的主要差异

| 特性 | RuoYi-Vue | RuoYi-Vue-Plus | 说明 |
|------|-----------|----------------|------|
| **多租户** | 不支持 | 支持 | 核心特性，数据物理隔离 |
| **租户套餐** | 无 | 支持 | 控制租户可用功能 |
| **自动初始化** | 无 | 支持 | 创建租户自动初始化资源 |
| **动态切换** | 无 | 支持 | 管理员可切换租户运维 |
| **租户 ID** | 无 | 6 位数字 | 自动生成唯一租户 ID |

### 1.3 业务规则

1. **租户 ID 唯一性**：租户 ID 为 6 位数字，全系统唯一
2. **企业名称唯一性**：企业名称在全系统必须唯一
3. **超管租户保护**：不允许删除或修改默认租户（000000）
4. **套餐使用校验**：已被租户使用的套餐不允许删除
5. **逻辑删除**：租户删除采用逻辑删除（del_flag=1）
6. **租户隔离**：多租户模式下，数据按租户 ID 自动隔离

---

## 二、功能详细设计

### 2.1 租户列表查询

**功能描述：** 分页查询系统租户列表，支持多条件筛选

**查询条件：**
- 租户 ID（精确匹配）
- 企业名称（模糊匹配）
- 联系人（模糊匹配）
- 联系电话（模糊匹配）
- 租户状态（0-正常 / 1-停用）

**列表字段：**
- 租户 ID、企业名称、联系人、联系电话
- 套餐名称、用户数量、过期时间、状态
- 创建时间、操作列

**权限标识：** `system:tenant:list`

**API 接口：** `GET /system/tenant/list`

### 2.2 新增租户

**功能描述：** 创建新的系统租户

**必填字段：**
- 企业名称（2-50 字符，唯一）
- 联系人姓名（0-30 字符）
- 联系电话（11 位手机号）
- 租户套餐（选择可用套餐）
- 管理员账号（用户名、密码）

**可选字段：**
- 用户数量限制（-1 表示不限制）
- 过期时间（不填表示永久）
- 备注

**业务校验：**
1. 校验企业名称是否已存在
2. 校验联系电话格式
3. 校验套餐是否存在且启用
4. 校验管理员账号是否合法
5. 生成唯一 6 位数字租户 ID

**初始化流程：**
1. 创建租户记录
2. 创建租户管理员角色（role_key='admin'）
3. 根据套餐关联菜单权限
4. 创建公司部门（部门名称=企业名称）
5. 创建管理员用户并关联角色和部门
6. 同步字典数据（从默认租户）
7. 同步参数配置（从默认租户）

**权限标识：** `system:tenant:add`

**API 接口：** `POST /system/tenant`

### 2.3 修改租户

**功能描述：** 修改现有租户信息

**可修改字段：**
- 联系人姓名、联系电话
- 用户数量限制、过期时间
- 租户套餐（可更换）
- 备注

**不可修改字段：**
- 租户 ID
- 企业名称

**业务校验：**
1. 校验租户是否允许操作（不能操作超管租户）
2. 校验套餐是否存在且启用

**权限标识：** `system:tenant:edit`

**API 接口：** `PUT /system/tenant`

### 2.4 删除租户

**功能描述：** 删除一个或多个租户（逻辑删除）

**业务规则：**
1. 不允许删除超管租户（000000）
2. 校验租户是否已被使用
3. 级联删除租户相关数据

**权限标识：** `system:tenant:remove`

**API 接口：** `DELETE /system/tenant/{tenantIds}`

### 2.5 状态修改

**功能描述：** 启用或停用租户

**状态值：**
- `0` - 正常（可登录）
- `1` - 停用（禁止登录）

**业务规则：**
1. 不允许停用超管租户
2. 状态变更立即生效

**权限标识：** `system:tenant:edit`

**API 接口：** `PUT /system/tenant/changeStatus`

### 2.6 租户套餐管理

**功能描述：** 管理租户可使用的功能套餐

**套餐字段：**
- 套餐名称（唯一）
- 关联菜单（多选）
- 套餐状态（0-正常 / 1-停用）
- 备注

**业务规则：**
1. 套餐名称唯一
2. 已被使用的套餐不允许删除
3. 停用套餐不影响已关联租户

**权限标识：** `system:tenantPackage:list/edit/add/remove`

**API 接口：** `GET/POST/PUT/DELETE /system/tenant/package/*`

### 2.7 动态切换租户

**功能描述：** 超级管理员动态切换到指定租户

**业务规则：**
1. 仅超级管理员可用
2. 切换后以该租户身份操作
3. 支持清除切换恢复原身份

**API 接口：** 
- `GET /system/tenant/dynamic/{tenantId}` - 切换租户
- `GET /system/tenant/dynamic/clear` - 清除切换

### 2.8 同步租户数据

**功能描述：** 从默认租户同步数据到所有租户

**同步类型：**
- **同步套餐**：更新租户菜单权限
- **同步字典**：同步字典类型和字典数据
- **同步参数**：同步系统参数配置

**业务规则：**
1. 从默认租户（000000）同步
2. 跳过租户已有数据
3. 仅同步正常状态租户
4. 同步后清除缓存

**API 接口：**
- `GET /system/tenant/syncTenantPackage`
- `GET /system/tenant/syncTenantDict`
- `GET /system/tenant/syncTenantConfig`

### 2.9 导出租户

**功能描述：** 导出租户列表为 Excel 文件

**导出字段：** 同列表显示字段

**权限标识：** `system:tenant:export`

**API 接口：** `POST /system/tenant/export`

---

## 三、权限设计

### 3.1 权限标识清单

| 权限标识 | 权限名称 | 说明 | 对应方法 |
|---------|---------|------|---------|
| `system:tenant:list` | 租户查询 | 查询租户列表 | list |
| `system:tenant:query` | 租户详情 | 查询租户详细信息 | getInfo |
| `system:tenant:add` | 租户新增 | 新增租户 | add |
| `system:tenant:edit` | 租户修改 | 修改租户信息、状态 | edit |
| `system:tenant:remove` | 租户删除 | 删除租户 | remove |
| `system:tenant:export` | 导出租户 | 导出租户数据 | export |
| `system:tenantPackage:list` | 套餐查询 | 查询套餐列表 | list |
| `system:tenantPackage:query` | 套餐详情 | 查询套餐详细信息 | getInfo |
| `system:tenantPackage:add` | 套餐新增 | 新增套餐 | add |
| `system:tenantPackage:edit` | 套餐修改 | 修改套餐信息 | edit |
| `system:tenantPackage:remove` | 套餐删除 | 删除套餐 | remove |

### 3.2 租户隔离

多租户模式下，所有数据操作自动附加租户条件：

```java
// 租户 helper
TenantHelper.isEnable()  // 检查是否启用租户
TenantHelper.getTenantId()  // 获取当前租户 ID
TenantHelper.ignore(() -> ...)  // 忽略租户过滤
```

### 3.3 超级管理员特权

超级管理员（tenant_id=000000）可查看所有租户数据，不受租户隔离限制。

---

## 四、接口设计

详见 [api.md](./api.md)

---

## 五、数据模型

详见 [data-model.md](./data-model.md)

---

## 六、前端页面

详见 [pages.md](./pages.md)

---

## 七、关联模块

| 关联模块 | 关联关系 | 说明 |
|---------|---------|------|
| 用户管理 (002-user) | 一对多 | 租户包含多个用户 |
| 角色管理 (003-role) | 一对多 | 租户包含多个角色 |
| 部门管理 (004-dept) | 一对多 | 租户包含多个部门 |
| 菜单管理 (006-menu) | 多对多 | 通过套餐关联菜单 |

---

## 八、技术实现要点

### 8.1 核心注解

| 注解 | 说明 | 示例 |
|------|------|------|
| `@SaCheckPermission` | Sa-Token 权限检查 | `@SaCheckPermission("system:tenant:add")` |
| `@Log` | 操作日志记录 | `@Log(title = "租户管理", businessType = BusinessType.INSERT)` |
| `@Validated` | 参数校验 | `@Validated @RequestBody SysTenantBo tenant` |

### 8.2 租户初始化

```java
// 创建租户时自动初始化
@Transactional
public void add(SysTenantBo bo) {
    // 1. 生成租户 ID
    String tenantId = generateTenantId();
    
    // 2. 创建租户记录
    mapper.insert(toEntity(bo));
    
    // 3. 创建角色
    createTenantRole(tenantId);
    
    // 4. 创建部门
    createTenantDept(tenantId, bo.getCompanyName());
    
    // 5. 创建管理员用户
    createTenantUser(tenantId, bo);
    
    // 6. 同步字典和参数
    syncDictAndConfig(tenantId);
}
```

### 8.3 租户 ID 生成

```java
// 随机生成 6 位数字租户 ID
private String generateTenantId() {
    Random random = new Random();
    String tenantId;
    do {
        tenantId = String.format("%06d", random.nextInt(999999));
    } while (existsTenantId(tenantId));
    return tenantId;
}
```

---

## 九、注意事项

1. **超管租户保护**：tenant_id=000000 为默认租户，不允许删除、停用
2. **事务管理**：创建租户涉及多表操作，必须使用事务
3. **缓存清理**：租户信息修改后需清除 SYS_TENANT 缓存
4. **租户上下文**：业务代码需考虑租户上下文，禁止硬编码租户 ID
5. **套餐校验**：删除套餐前需校验是否被租户使用

