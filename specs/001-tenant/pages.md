# 001-租户管理 - 页面清单

**模块编号：** 001  
**模块名称：** 租户管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、租户管理页面

### 1.1 租户列表页

**页面路径：** `/system/tenant`  
**页面标题：** 租户管理  
**权限标识：** `system:tenant:list`

**页面布局：**

```
┌─────────────────────────────────────────────────────────────┐
│ 租户管理                                      [+新增租户]    │
├─────────────────────────────────────────────────────────────┤
│ 搜索条件：                                                   │
│  租户 ID: [____]  企业名称：[____]  联系人：[____]          │
│  联系电话：[____]  状态：[全部▼]         [查询] [重置]      │
├─────────────────────────────────────────────────────────────┤
│  [+批量删除] [导出]                                          │
├─────────────────────────────────────────────────────────────┤
│  租户 ID  │ 企业名称  │ 联系人 │ 电话      │ 套餐  │ 状态  │
│  000001   │ 示例企业  │ 张三   │ 138****   │ 基础版│ 正常  │
│  000002   │ 测试公司  │ 李四   │ 139****   │ 高级版│ 停用  │
│  ...                                                        │
├─────────────────────────────────────────────────────────────┤
│  共 10 条  每页 10 条  < 1 2 3 >                              │
└─────────────────────────────────────────────────────────────┘
```

**功能列表：**

| 功能 | 说明 | 权限标识 |
|------|------|---------|
| 搜索 | 按条件筛选租户 | system:tenant:list |
| 新增 | 打开新增租户弹窗 | system:tenant:add |
| 编辑 | 打开编辑租户弹窗 | system:tenant:edit |
| 删除 | 删除选中租户 | system:tenant:remove |
| 批量删除 | 批量删除租户 | system:tenant:remove |
| 导出 | 导出 Excel | system:tenant:export |
| 状态切换 | 快速切换租户状态 | system:tenant:edit |

**操作列按钮：**

| 按钮 | 权限标识 | 说明 |
|------|---------|------|
| 编辑 | system:tenant:edit | 打开编辑弹窗 |
| 删除 | system:tenant:remove | 确认后删除 |
| 更多 | - | 下拉菜单：状态切换、查看详情 |

---

### 1.2 新增租户弹窗

**页面路径：** 弹窗（无独立路径）  
**页面标题：** 新增租户  
**权限标识：** `system:tenant:add`

**表单字段：**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| 企业名称 | 输入框 | 是 | 2-50 字符，唯一 |
| 统一社会信用代码 | 输入框 | 否 | 18 位 |
| 联系人姓名 | 输入框 | 否 | 0-30 字符 |
| 联系电话 | 输入框 | 否 | 11 位手机号 |
| 企业地址 | 输入框 | 否 | 0-200 字符 |
| 企业域名 | 输入框 | 否 | URL 格式 |
| 租户套餐 | 下拉选择 | 是 | 选择可用套餐 |
| 用户数量限制 | 数字输入框 | 否 | -1 表示不限制 |
| 过期时间 | 日期选择器 | 否 | 不填表示永久 |
| 备注 | 文本域 | 否 | 0-500 字符 |
| 管理员账号 | 输入框 | 是 | 登录账号 |
| 管理员密码 | 密码框 | 是 | 5-20 字符，加密传输 |
| 管理员昵称 | 输入框 | 否 | 0-30 字符 |

**表单验证：**

```javascript
rules: {
  companyName: [
    { required: true, message: '企业名称不能为空', trigger: 'blur' },
    { min: 2, max: 50, message: '企业名称长度必须介于 2 和 50 之间', trigger: 'blur' }
  ],
  contactPhone: [
    { pattern: /^1[3-9]\d{9}$/, message: '请输入正确的手机号码', trigger: 'blur' }
  ],
  packageId: [
    { required: true, message: '请选择租户套餐', trigger: 'change' }
  ],
  username: [
    { required: true, message: '管理员账号不能为空', trigger: 'blur' }
  ],
  password: [
    { required: true, message: '管理员密码不能为空', trigger: 'blur' },
    { min: 5, max: 20, message: '密码长度必须介于 5 和 20 之间', trigger: 'blur' }
  ]
}
```

**按钮操作：**

| 按钮 | 说明 |
|------|------|
| 提交 | 验证表单并提交，成功后关闭弹窗刷新列表 |
| 取消 | 关闭弹窗 |

---

### 1.3 编辑租户弹窗

**页面路径：** 弹窗（无独立路径）  
**页面标题：** 编辑租户  
**权限标识：** `system:tenant:edit`

**表单字段：** 同新增租户，但以下字段不可编辑：

| 字段 | 状态 | 说明 |
|------|------|------|
| 企业名称 | 禁用 | 不可修改 |
| 租户 ID | 隐藏 | 不可修改 |
| 管理员账号 | 隐藏 | 不可修改 |

**特殊逻辑：**

- 超管租户（000000）不允许编辑
- 修改套餐时提示是否同步菜单权限

---

### 1.4 租户详情页

**页面路径：** `/system/tenant/detail/:id`  
**页面标题：** 租户详情  
**权限标识：** `system:tenant:query`

**页面布局：**

```
┌─────────────────────────────────────────────────────────────┐
│ 租户详情                                    [返回列表]      │
├─────────────────────────────────────────────────────────────┤
│ 基本信息                                                     │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 租户 ID: 000001          │ 企业名称：示例企业        │   │
│  │ 联系人：张三             │ 联系电话：13800138000     │   │
│  │ 统一社会信用代码：...    │ 企业地址：上海市...       │   │
│  │ 企业域名：example.com    │ 备注：...                │   │
│  └─────────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────────┤
│ 套餐信息                                                     │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 套餐名称：基础版         │ 用户数量：100            │   │
│  │ 已注册用户：25           │ 过期时间：2026-12-31     │   │
│  │ 账户余额：100.00 元      │ 状态：正常               │   │
│  └─────────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────────┤
│ 包含菜单：系统管理、用户管理、角色管理、部门管理、...        │
└─────────────────────────────────────────────────────────────┘
```

---

## 二、租户套餐管理页面

### 2.1 套餐列表页

**页面路径：** `/system/tenant/package`  
**页面标题：** 租户套餐管理  
**权限标识：** `system:tenantPackage:list`

**页面布局：**

```
┌─────────────────────────────────────────────────────────────┐
│ 租户套餐管理                                  [+新增套餐]    │
├─────────────────────────────────────────────────────────────┤
│ 搜索条件：                                                   │
│  套餐名称：[____]  状态：[全部▼]           [查询] [重置]    │
├─────────────────────────────────────────────────────────────┤
│  [+批量删除] [导出]                                          │
├─────────────────────────────────────────────────────────────┤
│  套餐 ID  │ 套餐名称  │ 包含菜单数 │ 状态  │ 创建时间      │
│  1        │ 基础版    │ 15         │ 正常  │ 2026-01-01   │
│  2        │ 高级版    │ 30         │ 正常  │ 2026-01-01   │
│  3        │ 企业版    │ 50         │ 停用  │ 2026-01-01   │
│  ...                                                        │
├─────────────────────────────────────────────────────────────┤
│  共 5 条  每页 10 条  < 1 2 >                                 │
└─────────────────────────────────────────────────────────────┘
```

**功能列表：**

| 功能 | 说明 | 权限标识 |
|------|------|---------|
| 搜索 | 按条件筛选套餐 | system:tenantPackage:list |
| 新增 | 打开新增套餐弹窗 | system:tenantPackage:add |
| 编辑 | 打开编辑套餐弹窗 | system:tenantPackage:edit |
| 删除 | 删除选中套餐 | system:tenantPackage:remove |
| 批量删除 | 批量删除套餐 | system:tenantPackage:remove |
| 导出 | 导出 Excel | system:tenantPackage:export |
| 状态切换 | 快速切换套餐状态 | system:tenantPackage:edit |

**操作列按钮：**

| 按钮 | 权限标识 | 说明 |
|------|---------|------|
| 编辑 | system:tenantPackage:edit | 打开编辑弹窗 |
| 删除 | system:tenantPackage:remove | 确认后删除 |
| 查看菜单 | system:tenantPackage:query | 查看套餐包含的菜单 |

---

### 2.2 新增套餐弹窗

**页面路径：** 弹窗（无独立路径）  
**页面标题：** 新增套餐  
**权限标识：** `system:tenantPackage:add`

**表单字段：**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| 套餐名称 | 输入框 | 是 | 2-30 字符，唯一 |
| 关联菜单 | 树形选择器 | 是 | 多选，选择菜单树 |
| 状态 | 单选框 | 否 | 0-正常 / 1-停用，默认 0 |
| 备注 | 文本域 | 否 | 0-500 字符 |

**菜单树选择器：**

```
☑ 系统管理
  ☑ 用户管理
  ☑ 角色管理
  ☑ 部门管理
  ☑ 岗位管理
  ☐ 租户管理
☑ 系统监控
  ☑ 在线用户
  ☐ 定时任务
...
```

**表单验证：**

```javascript
rules: {
  name: [
    { required: true, message: '套餐名称不能为空', trigger: 'blur' },
    { min: 2, max: 30, message: '套餐名称长度必须介于 2 和 30 之间', trigger: 'blur' }
  ],
  menuIds: [
    { required: true, message: '请选择关联菜单', trigger: 'change' }
  ]
}
```

---

### 2.3 编辑套餐弹窗

**页面路径：** 弹窗（无独立路径）  
**页面标题：** 编辑套餐  
**权限标识：** `system:tenantPackage:edit`

**表单字段：** 同新增套餐

**特殊逻辑：**

- 已被使用的套餐删除时会提示
- 修改菜单后提示是否同步到已关联租户

---

## 三、通用组件

### 3.1 租户选择器

**组件名称：** `TenantSelect`  
**使用场景：** 超级管理员切换租户

**组件功能：**

- 下拉选择租户
- 搜索租户（按名称、ID）
- 显示租户状态（停用租户禁用）

---

### 3.2 状态标签

**组件名称：** `StatusTag`  
**使用场景：** 显示租户/套餐状态

**状态映射：**

| 值 | 标签文本 | 标签颜色 |
|---|---------|---------|
| 0 | 正常 | 绿色 |
| 1 | 停用 | 红色 |

---

## 四、路由配置

```javascript
{
  path: '/system',
  component: Layout,
  children: [
    {
      path: 'tenant',
      component: () => import('@/views/system/tenant/index'),
      name: 'Tenant',
      meta: { title: '租户管理', icon: 'dashboard', perms: 'system:tenant:list' }
    },
    {
      path: 'tenant/package',
      component: () => import('@/views/system/tenant/package'),
      name: 'TenantPackage',
      meta: { title: '租户套餐', icon: 'dashboard', perms: 'system:tenantPackage:list' }
    }
  ]
}
```

---

## 五、前端 API 调用

### 5.1 租户管理 API

```javascript
// 查询租户列表
export function listTenant(query) {
  return request({
    url: '/system/tenant/list',
    method: 'get',
    params: query
  })
}

// 获取租户详情
export function getTenant(id) {
  return request({
    url: '/system/tenant/' + id,
    method: 'get'
  })
}

// 新增租户
export function addTenant(data) {
  return request({
    url: '/system/tenant',
    method: 'post',
    data: data
  })
}

// 修改租户
export function updateTenant(data) {
  return request({
    url: '/system/tenant',
    method: 'put',
    data: data
  })
}

// 删除租户
export function delTenant(ids) {
  return request({
    url: '/system/tenant/' + ids,
    method: 'delete'
  })
}

// 修改租户状态
export function changeTenantStatus(data) {
  return request({
    url: '/system/tenant/changeStatus',
    method: 'put',
    data: data
  })
}

// 导出租户
export function exportTenant(query) {
  return request({
    url: '/system/tenant/export',
    method: 'post',
    params: query
  })
}

// 动态切换租户
export function dynamicTenant(tenantId) {
  return request({
    url: '/system/tenant/dynamic/' + tenantId,
    method: 'get'
  })
}

// 清除动态租户
export function clearDynamic() {
  return request({
    url: '/system/tenant/dynamic/clear',
    method: 'get'
  })
}
```

### 5.2 租户套餐 API

```javascript
// 查询套餐列表
export function listPackage(query) {
  return request({
    url: '/system/tenant/package/list',
    method: 'get',
    params: query
  })
}

// 获取套餐详情
export function getPackage(packageId) {
  return request({
    url: '/system/tenant/package/' + packageId,
    method: 'get'
  })
}

// 新增套餐
export function addPackage(data) {
  return request({
    url: '/system/tenant/package',
    method: 'post',
    data: data
  })
}

// 修改套餐
export function updatePackage(data) {
  return request({
    url: '/system/tenant/package',
    method: 'put',
    data: data
  })
}

// 删除套餐
export function delPackage(packageIds) {
  return request({
    url: '/system/tenant/package/' + packageIds,
    method: 'delete'
  })
}

// 查询套餐下拉列表
export function selectPackageList() {
  return request({
    url: '/system/tenant/package/selectList',
    method: 'get'
  })
}
```

---

## 六、注意事项

1. **超管租户保护**：前端隐藏超管租户（000000）的删除和停用按钮
2. **密码加密**：管理员密码使用 `@ApiEncrypt` 加密后传输
3. **套餐菜单**：套餐编辑时使用树形选择器选择菜单
4. **状态同步**：修改套餐菜单后提示是否同步到已关联租户
5. **数据权限**：仅超级管理员可见租户管理菜单

