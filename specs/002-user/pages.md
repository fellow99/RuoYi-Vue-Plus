# 002-用户管理 - 页面清单

**模块编号：** 002  
**模块名称：** 用户管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、用户管理页面

### 1.1 用户列表页

**页面路径：** `/system/user`  
**页面标题：** 用户管理  
**权限标识：** `system:user:list`

**页面布局：**

```
┌─────────────────────────────────────────────────────────────┐
│ 用户管理                                      [+新增用户]    │
├─────────────────────────────────────────────────────────────┤
│ 搜索条件：                                                   │
│  用户账号：[____]  用户昵称：[____]  手机号码：[____]       │
│  部门：[选择部门▼]  状态：[全部▼]           [查询] [重置]   │
├─────────────────────────────────────────────────────────────┤
│  [+批量删除] [导出]                                          │
├─────────────────────────────────────────────────────────────┤
│  用户 ID  │ 账号    │ 昵称  │ 部门  │ 手机      │ 状态  │
│  1        │ admin   │ 管理员│ 研发部│ 138****   │ 正常  │
│  2        │ zhangsan│ 张三  │ 产品部│ 139****   │ 正常  │
│  ...                                                        │
├─────────────────────────────────────────────────────────────┤
│  共 50 条  每页 10 条  < 1 2 3 4 5 >                          │
└─────────────────────────────────────────────────────────────┘
```

**功能列表：**

| 功能 | 说明 | 权限标识 |
|------|------|---------|
| 搜索 | 按条件筛选用户 | system:user:list |
| 新增 | 打开新增用户弹窗 | system:user:add |
| 编辑 | 打开编辑用户弹窗 | system:user:edit |
| 删除 | 删除选中用户 | system:user:remove |
| 批量删除 | 批量删除用户 | system:user:remove |
| 导出 | 导出 Excel | system:user:export |
| 状态切换 | 快速切换用户状态 | system:user:edit |

**操作列按钮：**

| 按钮 | 权限标识 | 说明 |
|------|---------|------|
| 编辑 | system:user:edit | 打开编辑弹窗 |
| 删除 | system:user:remove | 确认后删除 |
| 重置密码 | system:user:resetPwd | 打开重置密码弹窗 |
| 授权角色 | system:user:edit | 打开授权弹窗 |

---

### 1.2 新增用户弹窗

**页面路径：** 弹窗（无独立路径）  
**页面标题：** 新增用户  
**权限标识：** `system:user:add`

**表单字段：**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| 用户账号 | 输入框 | 是 | 2-30 字符，唯一 |
| 用户昵称 | 输入框 | 是 | 0-30 字符 |
| 所属部门 | 树形选择器 | 是 | 选择部门 |
| 用户性别 | 单选框 | 否 | 0-男/1-女/2-未知 |
| 手机号码 | 输入框 | 否 | 11 位，唯一 |
| 邮箱 | 输入框 | 否 | 50 字符内，唯一 |
| 登录密码 | 密码框 | 是 | 5-20 字符 |
| 角色 | 多选框 | 是 | 选择角色 |
| 岗位 | 多选框 | 否 | 选择岗位 |
| 备注 | 文本域 | 否 | 500 字符内 |

**表单验证：**

```javascript
rules: {
  userName: [
    { required: true, message: '用户账号不能为空', trigger: 'blur' },
    { min: 2, max: 30, message: '用户账号长度必须介于 2 和 30 之间', trigger: 'blur' }
  ],
  nickName: [
    { required: true, message: '用户昵称不能为空', trigger: 'blur' }
  ],
  deptId: [
    { required: true, message: '所属部门不能为空', trigger: 'change' }
  ],
  password: [
    { required: true, message: '用户密码不能为空', trigger: 'blur' },
    { min: 5, max: 20, message: '用户密码长度必须介于 5 和 20 之间', trigger: 'blur' }
  ],
  roleIds: [
    { required: true, message: '请选择角色', trigger: 'change' }
  ]
}
```

---

### 1.3 编辑用户弹窗

**页面路径：** 弹窗（无独立路径）  
**页面标题：** 编辑用户  
**权限标识：** `system:user:edit`

**表单字段：** 同新增用户，但以下字段不可编辑：

| 字段 | 状态 | 说明 |
|------|------|------|
| 用户账号 | 禁用 | 不可修改 |

**特殊逻辑：**

- 超级管理员（userId=1）不允许编辑
- 修改密码需使用重置密码功能

---

### 1.4 重置密码弹窗

**页面路径：** 弹窗（无独立路径）  
**页面标题：** 重置密码  
**权限标识：** `system:user:resetPwd`

**表单字段：**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| 用户账号 | 输入框 | - | 只读显示 |
| 新密码 | 密码框 | 是 | 5-20 字符，加密传输 |

---

### 1.5 授权角色弹窗

**页面路径：** 弹窗（无独立路径）  
**页面标题：** 分配角色  
**权限标识：** `system:user:edit`

**表单字段：**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| 用户信息 | 文本 | - | 显示用户账号和昵称 |
| 角色列表 | 多选框 | 是 | 选择角色 |

---

## 二、通用组件

### 2.1 部门树选择器

**组件名称：** `DeptSelect`  
**使用场景：** 用户表单选择部门

**组件功能：**

- 树形展示部门
- 支持搜索部门
- 显示部门状态（禁用部门不可选）

---

### 2.2 状态标签

**组件名称：** `StatusTag`  
**使用场景：** 显示用户状态

**状态映射：**

| 值 | 标签文本 | 标签颜色 |
|---|---------|---------|
| 0 | 正常 | 绿色 |
| 1 | 停用 | 红色 |

---

## 三、路由配置

```javascript
{
  path: '/system/user',
  component: () => import('@/views/system/user/index'),
  name: 'User',
  meta: { title: '用户管理', icon: 'user', perms: 'system:user:list' }
}
```

---

## 四、前端 API 调用

```javascript
// 查询用户列表
export function listUser(query) {
  return request({
    url: '/system/user/list',
    method: 'get',
    params: query
  })
}

// 获取用户详情
export function getUser(userId) {
  return request({
    url: '/system/user/' + userId,
    method: 'get'
  })
}

// 新增用户
export function addUser(data) {
  return request({
    url: '/system/user',
    method: 'post',
    data: data
  })
}

// 修改用户
export function updateUser(data) {
  return request({
    url: '/system/user',
    method: 'put',
    data: data
  })
}

// 删除用户
export function delUser(userIds) {
  return request({
    url: '/system/user/' + userIds,
    method: 'delete'
  })
}

// 重置密码
export function resetUserPwd(data) {
  return request({
    url: '/system/user/resetPwd',
    method: 'put',
    data: data
  })
}

// 修改用户状态
export function changeUserStatus(data) {
  return request({
    url: '/system/user/changeStatus',
    method: 'put',
    data: data
  })
}

// 获取部门树
export function deptTree() {
  return request({
    url: '/system/user/deptTree',
    method: 'get'
  })
}

// 获取当前用户信息
export function getInfo() {
  return request({
    url: '/system/user/getInfo',
    method: 'get'
  })
}
```

