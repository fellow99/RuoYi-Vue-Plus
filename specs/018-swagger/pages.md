# 018-API 文档 - 页面交互

**模块编号：** 018  
**模块名称：** API 文档  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、页面概述

**访问地址：** `/doc.html`  
**页面名称：** API 文档  
**认证要求：** 需要登录

---

## 二、页面布局

```
┌─────────────────────────────────────────────────────────────┐
│  RuoYi-Vue-Plus API 文档                              [认证] │
├──────────────┬──────────────────────────────────────────────┤
│              │  接口详情                                     │
│  接口列表    │  ┌────────────────────────────────────────┐  │
│  ┌────────┐  │  │ GET /system/user/list                  │  │
│  │用户管理 ▼│  │  │                                       │  │
│  │角色管理  │  │  │ 请求参数：                             │  │
│  │菜单管理  │  │  │ - pageNum: Integer                   │  │
│  │字典管理  │  │  │ - pageSize: Integer                  │  │
│  │...     │  │  │                                       │  │
│  └────────┘  │  │ 响应数据：                             │  │
│              │  │ {                                      │  │
│              │  │   "code": 200,                         │  │
│              │  │   "data": {...}                        │  │
│              │  │ }                                      │  │
│              │  │                                       │  │
│              │  │ [Try it out]  [Execute]               │  │
│              │  └────────────────────────────────────────┘  │
└──────────────┴──────────────────────────────────────────────┘
```

---

## 三、页面功能

### 3.1 接口列表

**位置：** 页面左侧

**功能：**
- 按模块分组显示所有接口
- 支持展开/收起模块
- 支持模块搜索
- 显示接口方法和路径

**交互：**
1. 点击模块名展开/收起
2. 点击接口名在右侧显示详情
3. 搜索框输入关键字过滤接口

---

### 3.2 接口详情

**位置：** 页面右侧

**显示内容：**
- 接口方法和路径
- 接口描述
- 请求参数
- 响应数据
- 数据模型

**交互：**
1. 点击"Try it out"进入测试模式
2. 填写请求参数
3. 点击"Execute"发送请求
4. 查看响应结果

---

### 3.3 认证配置

**位置：** 页面右上角

**功能：**
- 输入 Token
- 自动携带 Token 到所有请求

**交互：**
1. 点击右上角"Authorize"按钮
2. 输入 Token（格式：`Bearer <token>`）
3. 点击"Authorize"保存
4. 所有请求自动携带 Token

---

## 四、页面交互流程

### 4.1 查看接口文档

**流程：**
1. 访问 `/doc.html`
2. 系统自动登录（如已登录）
3. 显示接口列表
4. 点击接口查看详情

---

### 4.2 测试接口

**流程：**
1. 找到要测试的接口
2. 点击"Try it out"
3. 填写请求参数
4. 点击"Execute"
5. 查看请求详情
6. 查看响应结果

---

### 4.3 配置认证

**流程：**
1. 点击右上角"Authorize"
2. 输入 Token
3. 点击"Authorize"保存
4. 关闭对话框
5. 测试接口时自动携带 Token

---

## 五、页面配置

### 5.1 页面参数

```yaml
springdoc:
  swagger-ui:
    path: /swagger-ui.html
    tags-sorter: alpha  # 按字母排序
    operations-sorter: alpha  # 按字母排序
    display-request-duration: true  # 显示请求耗时
```

---

### 5.2 自定义配置

**application.yml:**

```yaml
springdoc:
  api-docs:
    enabled: true
    path: /v3/api-docs
  swagger-ui:
    enabled: true
    path: /doc.html
    tags-sorter: alpha
    operations-sorter: method  # 按方法排序
    display-request-duration: true
    filter: true  # 显示搜索框
    show-common-extensions: true
    show-extensions: true
```

---

## 六、UI 组件

### 6.1 依赖库

- **springdoc-openapi-starter-webmvc-ui** - Swagger UI
- **knife4j** - 增强 UI（可选）

### 6.2 主题样式

- 默认主题：Swagger UI 默认样式
- 支持自定义 CSS 样式
- 支持自定义 Logo

---

## 七、快捷操作

### 7.1 键盘快捷键

| 快捷键 | 功能 |
|-------|------|
| `/` | 聚焦搜索框 |
| `Esc` | 关闭对话框 |

### 7.2 鼠标操作

| 操作 | 功能 |
|------|------|
| 点击模块名 | 展开/收起模块 |
| 点击接口名 | 显示接口详情 |
| 点击"Try it out" | 进入测试模式 |
| 点击"Execute" | 发送请求 |
| 点击"Clear" | 清空响应 |

---

## 八、响应展示

### 8.1 成功响应

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "userId": 1,
    "userName": "admin"
  }
}
```

**显示：** 绿色背景，显示响应码 200

---

### 8.2 错误响应

```json
{
  "code": 400,
  "msg": "参数验证失败",
  "data": null
}
```

**显示：** 红色背景，显示响应码 400

---

## 九、数据模型展示

### 9.1 Schema 显示

**位置：** 接口详情底部

**内容：**
- 请求体 Schema
- 响应体 Schema
- 数据模型详情

**示例：**

```
Schemas

SysUser
  userId: integer (int64)
  userName: string
  nickName: string
  email: string
  phonenumber: string
  sex: string
  avatar: string
  status: string
```

---

### 9.2 枚举类型显示

```
SysUserStatus
  0 - 正常
  1 - 停用
```

---

## 十、注意事项

### 10.1 浏览器兼容性

- Chrome（推荐）
- Firefox
- Safari
- Edge

### 10.2 性能优化

- 接口数量多时，使用搜索功能
- 按需展开模块
- 避免频繁刷新页面

### 10.3 安全注意

- 生产环境关闭文档
- 不要在公共网络使用文档
- 不要在文档中暴露敏感信息

---

**文档版本：** 1.0  
**创建日期：** 2026-03-13  
**审核状态：** 待审核
