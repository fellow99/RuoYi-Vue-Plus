# 006-菜单管理 - API 接口

**模块编号：** 006  
**模块名称：** 菜单管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、接口概述

菜单管理模块提供完整的菜单 CRUD、路由获取、权限分配等 API 接口。所有接口均采用 RESTful 风格设计，使用 JSON 格式进行数据交换。

### 1.1 基础信息

- **基础路径：** `/system/menu`
- **认证方式：** Sa-Token 会话认证
- **数据格式：** application/json
- **字符编码：** UTF-8

### 1.2 权限标识

| 接口 | 权限标识 | 说明 |
|------|----------|------|
| 菜单列表 | `system:menu:list` | 查询菜单列表 |
| 菜单详情 | `system:menu:query` | 查询菜单详情 |
| 菜单新增 | `system:menu:add` | 新增菜单 |
| 菜单修改 | `system:menu:edit` | 修改菜单 |
| 菜单删除 | `system:menu:remove` | 删除菜单 |
| 路由获取 | - | 登录用户默认权限 |

---

## 二、接口详细

### 2.1 获取路由信息

获取当前用户可访问的路由菜单。

**接口地址：** `GET /system/menu/getRouters`

**认证要求：** 登录用户

**请求参数：** 无

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": [
    {
      "name": "System",
      "path": "/system",
      "hidden": false,
      "redirect": "user",
      "meta": {
        "title": "系统管理",
        "icon": "system",
        "isLink": "",
        "isKeepAlive": true,
        "isHidden": false
      },
      "children": [
        {
          "name": "User",
          "path": "user",
          "hidden": false,
          "meta": {
            "title": "用户管理",
            "icon": "user",
            "isLink": "",
            "isKeepAlive": true,
            "isHidden": false
          }
        }
      ]
    }
  ]
}
```

**响应字段说明：**

| 字段 | 类型 | 说明 |
|------|------|------|
| name | String | 路由名称 |
| path | String | 路由地址 |
| hidden | Boolean | 是否隐藏 |
| redirect | String | 重定向地址 |
| meta | Object | 路由元信息 |
| meta.title | String | 菜单标题 |
| meta.icon | String | 菜单图标 |
| meta.isLink | Boolean | 是否外链 |
| meta.isKeepAlive | Boolean | 是否缓存 |
| meta.isHidden | Boolean | 是否隐藏 |
| children | Array | 子路由列表 |

---

### 2.2 获取菜单列表

查询系统菜单列表。

**接口地址：** `GET /system/menu/list`

**认证要求：** `system:menu:list`

**请求参数：**

| 参数名 | 类型 | 位置 | 必填 | 说明 |
|--------|------|------|------|------|
| menuName | String | query | 否 | 菜单名称（模糊） |
| visible | String | query | 否 | 显示状态（0 显示 1 隐藏） |
| status | String | query | 否 | 菜单状态（0 正常 1 停用） |

**请求示例：**

```http
GET /system/menu/list?menuName=用户&visible=0&status=0
```

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": [
    {
      "menuId": 1,
      "parentId": 0,
      "menuName": "系统管理",
      "orderNum": 1,
      "path": "/system",
      "component": "Layout",
      "menuType": "M",
      "visible": "0",
      "status": "0",
      "perms": "",
      "icon": "system",
      "remark": "",
      "parentName": "",
      "children": []
    }
  ]
}
```

---

### 2.3 获取菜单详情

根据菜单编号获取详细信息。

**接口地址：** `GET /system/menu/{menuId}`

**认证要求：** `system:menu:query`

**路径参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| menuId | Long | 是 | 菜单 ID |

**请求示例：**

```http
GET /system/menu/1
```

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "menuId": 1,
    "parentId": 0,
    "menuName": "系统管理",
    "orderNum": 1,
    "path": "/system",
    "component": "Layout",
    "menuType": "M",
    "visible": "0",
    "status": "0",
    "perms": "",
    "icon": "system",
    "remark": "",
    "parentName": "",
    "children": []
  }
}
```

---

### 2.4 获取菜单下拉树

获取菜单树形结构，用于菜单选择。

**接口地址：** `GET /system/menu/treeselect`

**认证要求：** `system:menu:query`

**请求参数：**

| 参数名 | 类型 | 位置 | 必填 | 说明 |
|--------|------|------|------|------|
| menuName | String | query | 否 | 菜单名称（模糊） |
| visible | String | query | 否 | 显示状态 |
| status | String | query | 否 | 菜单状态 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": [
    {
      "id": 1,
      "label": "系统管理",
      "children": [
        {
          "id": 2,
          "label": "用户管理",
          "children": []
        }
      ]
    }
  ]
}
```

---

### 2.5 获取角色菜单树

获取角色已分配的菜单和菜单树。

**接口地址：** `GET /system/menu/roleMenuTreeselect/{roleId}`

**认证要求：** `system:menu:query`

**路径参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| roleId | Long | 是 | 角色 ID |

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "checkedKeys": [1, 2, 3],
    "menus": [
      {
        "id": 1,
        "label": "系统管理",
        "children": [
          {
            "id": 2,
            "label": "用户管理",
            "children": []
          }
        ]
      }
    ]
  }
}
```

**响应字段说明：**

| 字段 | 类型 | 说明 |
|------|------|------|
| checkedKeys | Array<Long> | 已选中的菜单 ID 列表 |
| menus | Array | 完整的菜单树形结构 |

---

### 2.6 获取套餐菜单树

获取租户套餐已分配的菜单和菜单树。

**接口地址：** `GET /system/menu/tenantPackageMenuTreeselect/{packageId}`

**认证要求：** `system:menu:query` + 超级管理员角色

**路径参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| packageId | Long | 是 | 租户套餐 ID |

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "checkedKeys": [1, 2, 3],
    "menus": [
      {
        "id": 1,
        "label": "系统管理",
        "children": []
      }
    ]
  }
}
```

**特殊说明：**
- 租户管理菜单（ID=6）对租户套餐不可见
- 仅超级管理员可访问此接口

---

### 2.7 新增菜单

创建新的系统菜单。

**接口地址：** `POST /system/menu`

**认证要求：** `system:menu:add` + 超级管理员角色

**请求头：**

```http
Content-Type: application/json
```

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| menuName | String | 是 | 菜单名称（2-50 字符） |
| orderNum | Integer | 是 | 显示顺序 |
| parentId | Long | 否 | 父菜单 ID（默认 0） |
| path | String | 否 | 路由地址（最大 200 字符） |
| component | String | 否 | 组件路径（最大 255 字符） |
| queryParam | String | 否 | 路由参数（最大 255 字符） |
| isFrame | String | 否 | 是否外链（0 是 1 否） |
| isCache | String | 否 | 是否缓存（0 缓存 1 不缓存） |
| menuType | String | 是 | 菜单类型（M 目录 C 菜单 F 按钮） |
| visible | String | 否 | 显示状态（0 显示 1 隐藏） |
| status | String | 否 | 菜单状态（0 正常 1 停用） |
| perms | String | 否 | 权限标识（最大 100 字符） |
| icon | String | 否 | 菜单图标（最大 100 字符） |
| remark | String | 否 | 备注（最大 500 字符） |

**请求示例：**

```json
{
  "menuName": "用户管理",
  "orderNum": 1,
  "parentId": 1,
  "path": "user",
  "component": "system/user/index",
  "menuType": "C",
  "visible": "0",
  "status": "0",
  "perms": "system:user:list",
  "icon": "user",
  "remark": ""
}
```

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功"
}
```

**错误响应：**

```json
{
  "code": 400,
  "msg": "新增菜单'用户管理'失败，菜单名称已存在"
}
```

```json
{
  "code": 400,
  "msg": "新增菜单'外链'失败，地址必须以 http(s)://开头"
}
```

```json
{
  "code": 400,
  "msg": "新增菜单'用户管理'失败，路由名称或地址已存在"
}
```

---

### 2.8 修改菜单

修改系统菜单信息。

**接口地址：** `PUT /system/menu`

**认证要求：** `system:menu:edit` + 超级管理员角色

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| menuId | Long | 是 | 菜单 ID |
| menuName | String | 是 | 菜单名称 |
| orderNum | Integer | 是 | 显示顺序 |
| parentId | Long | 否 | 父菜单 ID |
| path | String | 否 | 路由地址 |
| component | String | 否 | 组件路径 |
| menuType | String | 是 | 菜单类型 |
| visible | String | 否 | 显示状态 |
| status | String | 否 | 菜单状态 |
| perms | String | 否 | 权限标识 |
| icon | String | 否 | 菜单图标 |
| remark | String | 否 | 备注 |

**请求示例：**

```json
{
  "menuId": 2,
  "menuName": "用户管理",
  "orderNum": 1,
  "parentId": 1,
  "path": "user",
  "component": "system/user/index",
  "menuType": "C",
  "visible": "0",
  "status": "0",
  "perms": "system:user:list",
  "icon": "user",
  "remark": "修改后的备注"
}
```

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功"
}
```

**错误响应：**

```json
{
  "code": 400,
  "msg": "修改菜单'用户管理'失败，菜单名称已存在"
}
```

```json
{
  "code": 400,
  "msg": "修改菜单'用户管理'失败，上级菜单不能选择自己"
}
```

---

### 2.9 删除菜单

删除系统菜单。

**接口地址：** `DELETE /system/menu/{menuId}`

**认证要求：** `system:menu:remove` + 超级管理员角色

**路径参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| menuId | Long | 是 | 菜单 ID |

**请求示例：**

```http
DELETE /system/menu/2
```

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功"
}
```

**错误响应：**

```json
{
  "code": 400,
  "msg": "存在子菜单，不允许删除"
}
```

```json
{
  "code": 400,
  "msg": "菜单已分配，不允许删除"
}
```

---

### 2.10 批量删除菜单

批量级联删除系统菜单。

**接口地址：** `DELETE /system/menu/cascade/{menuIds}`

**认证要求：** `system:menu:remove` + 超级管理员角色

**路径参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| menuIds | Long[] | 是 | 菜单 ID 列表（逗号分隔） |

**请求示例：**

```http
DELETE /system/menu/cascade/2,3,4
```

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功"
}
```

**错误响应：**

```json
{
  "code": 400,
  "msg": "存在子菜单，不允许删除"
}
```

---

## 三、错误码

| 错误码 | 说明 |
|--------|------|
| 200 | 操作成功 |
| 400 | 请求参数错误 / 业务校验失败 |
| 401 | 未授权 / 未登录 |
| 403 | 无权限 |
| 500 | 服务器内部错误 |

---

## 四、业务校验规则

### 4.1 菜单名称校验

- 菜单名称不能为空
- 菜单名称长度：2-50 字符
- 同级菜单名称必须唯一

### 4.2 路由地址校验

- 外链地址必须以 `http://` 或 `https://` 开头
- 路由名称和地址在全系统必须唯一

### 4.3 上级菜单校验

- 上级菜单不能选择自己
- 不能选择已删除的菜单作为上级

### 4.4 删除校验

- 存在子菜单的菜单不允许删除
- 已分配给角色的菜单不允许删除

---

## 五、使用示例

### 5.1 JavaScript (Axios)

```javascript
// 获取路由信息
const getRouters = () => {
  return request({
    url: '/system/menu/getRouters',
    method: 'get'
  })
}

// 获取菜单列表
const getMenuList = (params) => {
  return request({
    url: '/system/menu/list',
    method: 'get',
    params
  })
}

// 新增菜单
const addMenu = (data) => {
  return request({
    url: '/system/menu',
    method: 'post',
    data: data
  })
}

// 修改菜单
const updateMenu = (data) => {
  return request({
    url: '/system/menu',
    method: 'put',
    data: data
  })
}

// 删除菜单
const deleteMenu = (menuId) => {
  return request({
    url: '/system/menu/' + menuId,
    method: 'delete'
  })
}
```

### 5.2 Java (RestTemplate)

```java
// 获取菜单列表
RestTemplate restTemplate = new RestTemplate();
String url = "http://localhost:8080/system/menu/list?menuName=用户";
ResponseEntity<R<List<SysMenuVo>>> response = restTemplate.exchange(
    url,
    HttpMethod.GET,
    null,
    new ParameterizedTypeReference<R<List<SysMenuVo>>>() {}
);
```

