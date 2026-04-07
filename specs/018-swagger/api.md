# 018-API 文档 - 接口规范

**模块编号：** 018  
**模块名称：** API 文档  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、接口概述

**文档访问地址：**
- Swagger UI: `/doc.html`
- OpenAPI JSON: `/v3/api-docs`

**认证方式：** Sa-Token（Header 方式）

**数据格式：** JSON

---

## 二、文档接口清单

### 2.1 文档访问接口

| 接口名称 | 请求方式 | 接口路径 | 说明 |
|---------|---------|---------|------|
| Swagger UI | GET | `/doc.html` | 文档页面 |
| OpenAPI JSON | GET | `/v3/api-docs` | OpenAPI 规范 JSON |
| OpenAPI JSON（分组） | GET | `/v3/api-docs/{group}` | 分组 OpenAPI JSON |

---

## 三、接口注解规范

### 3.1 Controller 注解

**类级别注解：**

```java
@Tag(name = "用户管理", description = "用户相关接口")
@RestController
@RequestMapping("/system/user")
public class SysUserController extends BaseController {
    // ...
}
```

**方法级别注解：**

```java
@Operation(summary = "查询用户列表", description = "分页查询用户信息")
@ApiResponses({
    @ApiResponse(responseCode = "200", description = "成功"),
    @ApiResponse(responseCode = "401", description = "未授权"),
    @ApiResponse(responseCode = "500", description = "服务器错误")
})
@GetMapping("/list")
public TableDataInfo<SysUser> list(SysUser user, PageQuery pageQuery) {
    // ...
}
```

---

### 3.2 参数注解规范

**路径参数：**

```java
@Parameter(name = "userId", description = "用户 ID", required = true)
@PathVariable("userId")
Long userId
```

**查询参数：**

```java
@Parameter(name = "pageNum", description = "页码")
@RequestParam("pageNum")
Integer pageNum
```

**请求体参数：**

```java
@Parameter(description = "用户信息")
@RequestBody
SysUser user
```

---

### 3.3 数据模型注解规范

**类注解：**

```java
@Schema(description = "用户信息")
public class SysUser {
    // ...
}
```

**字段注解：**

```java
@Schema(name = "userId", description = "用户 ID", example = "1")
private Long userId;

@Schema(name = "userName", description = "用户名", requiredMode = RequiredMode.REQUIRED)
private String userName;
```

---

## 四、接口示例

### 4.1 用户管理接口

```java
@Tag(name = "用户管理", description = "用户相关接口")
@RestController
@RequestMapping("/system/user")
public class SysUserController extends BaseController {

    @Operation(summary = "查询用户列表")
    @SaCheckPermission("system:user:list")
    @GetMapping("/list")
    public TableDataInfo<SysUser> list(SysUser user, PageQuery pageQuery) {
        return userService.selectPageUserList(user, pageQuery);
    }

    @Operation(summary = "获取用户详情", description = "根据用户 ID 获取用户信息")
    @SaCheckPermission("system:user:query")
    @GetMapping(value = "/{userId}")
    public R<SysUser> getInfo(@Parameter(name = "用户 ID", required = true) 
                              @PathVariable("userId") Long userId) {
        return R.ok(userService.selectUserById(userId));
    }

    @Operation(summary = "新增用户")
    @SaCheckPermission("system:user:add")
    @Log(title = "用户管理", businessType = BusinessType.INSERT)
    @PostMapping
    public R<Void> add(@Validated @RequestBody SaveUserReq req) {
        return toAjax(userService.insertUser(req));
    }

    @Operation(summary = "修改用户")
    @SaCheckPermission("system:user:edit")
    @Log(title = "用户管理", businessType = BusinessType.UPDATE)
    @PutMapping
    public R<Void> edit(@Validated @RequestBody UpdateUserReq req) {
        return toAjax(userService.updateUser(req));
    }

    @Operation(summary = "删除用户")
    @SaCheckPermission("system:user:remove")
    @Log(title = "用户管理", businessType = BusinessType.DELETE)
    @DeleteMapping("/{userIds}")
    public R<Void> remove(@Parameter(name = "用户 ID 数组") 
                         @PathVariable Long[] userIds) {
        return toAjax(userService.deleteUserByIds(userIds));
    }
}
```

---

### 4.2 数据模型示例

**请求模型：**

```java
@Schema(description = "新增用户请求")
public class SaveUserReq {
    
    @Schema(name = "userName", description = "用户名", requiredMode = RequiredMode.REQUIRED)
    @NotBlank(message = "用户名不能为空")
    private String userName;
    
    @Schema(name = "nickName", description = "昵称", requiredMode = RequiredMode.REQUIRED)
    @NotBlank(message = "昵称不能为空")
    private String nickName;
    
    @Schema(name = "password", description = "密码", requiredMode = RequiredMode.REQUIRED)
    @NotBlank(message = "密码不能为空")
    private String password;
    
    @Schema(name = "email", description = "邮箱")
    @Email(message = "邮箱格式不正确")
    private String email;
    
    @Schema(name = "phonenumber", description = "手机号")
    @Pattern(regexp = "^1[3-9]\\d{9}$", message = "手机号格式不正确")
    private String phonenumber;
    
    @Schema(name = "sex", description = "性别（0 男 1 女）")
    private String sex;
    
    @Schema(name = "deptId", description = "部门 ID")
    private Long deptId;
    
    @Schema(name = "postIds", description = "岗位 ID 数组")
    private Long[] postIds;
    
    @Schema(name = "remark", description = "备注")
    private String remark;
    
    // getters and setters
}
```

**响应模型：**

```java
@Schema(description = "用户信息")
public class SysUser extends BaseEntity {
    
    @Schema(name = "userId", description = "用户 ID", example = "1")
    private Long userId;
    
    @Schema(name = "userName", description = "用户名", example = "admin")
    private String userName;
    
    @Schema(name = "nickName", description = "昵称", example = "管理员")
    private String nickName;
    
    @Schema(name = "email", description = "邮箱", example = "admin@example.com")
    private String email;
    
    @Schema(name = "phonenumber", description = "手机号", example = "13800138000")
    private String phonenumber;
    
    @Schema(name = "sex", description = "性别（0 男 1 女）", example = "0")
    private String sex;
    
    @Schema(name = "avatar", description = "头像地址")
    private String avatar;
    
    @Schema(name = "status", description = "帐号状态（0 正常 1 停用）", example = "0")
    private String status;
    
    @Schema(name = "dept", description = "部门信息")
    private SysDept dept;
    
    @Schema(name = "posts", description = "岗位信息列表")
    private List<SysPost> posts;
    
    @Schema(name = "roles", description = "角色信息列表")
    private List<SysRole> roles;
    
    // getters and setters
}
```

---

## 五、错误响应规范

### 5.1 错误响应结构

```json
{
  "code": 400,
  "msg": "参数验证失败",
  "data": null
}
```

### 5.2 常见错误码

| 错误码 | 说明 |
|-------|------|
| 200 | 成功 |
| 400 | 参数错误 |
| 401 | 未授权 |
| 403 | 无权限 |
| 404 | 资源不存在 |
| 500 | 服务器错误 |

---

## 六、安全要求

### 6.1 认证要求

- 所有接口默认需要认证（除公开接口外）
- 认证 Token 通过 Header 传递：`Authorization: Bearer <token>`
- 公开接口使用 `@Anonymous` 注解标记

### 6.2 权限要求

- 需要权限的接口使用 `@SaCheckPermission` 注解
- 权限标识格式：`模块：操作`（如：`system:user:list`）

### 6.3 生产环境安全

- 生产环境默认关闭文档功能
- 通过配置 `springdoc.api-docs.enabled=false` 关闭
- 敏感接口不显示在文档中

---

## 七、最佳实践

### 7.1 注解使用建议

1. **所有 Controller 类** 添加 `@Tag` 注解
2. **所有接口方法** 添加 `@Operation` 注解
3. **所有参数** 添加 `@Parameter` 注解（重要参数）
4. **所有数据模型** 添加 `@Schema` 注解
5. **所有字段** 添加 `@Schema` 注解（重要字段）

### 7.2 文档描述规范

1. **接口摘要** 简洁明了（不超过 50 字）
2. **接口描述** 详细说明（可选）
3. **参数描述** 清晰准确
4. **响应描述** 包含所有可能情况

### 7.3 示例代码

```java
@Tag(name = "字典管理", description = "字典相关接口")
@RestController
@RequestMapping("/system/dict")
public class SysDictController extends BaseController {

    @Operation(summary = "查询字典类型列表")
    @SaCheckPermission("system:dict:list")
    @GetMapping("/list")
    public TableDataInfo<SysDictType> list(SysDictType dictType, PageQuery pageQuery) {
        return dictTypeService.selectPageDictTypeList(dictType, pageQuery);
    }

    @Operation(
        summary = "查询字典类型详情",
        description = "根据字典类型 ID 获取字典类型信息，包含字典数据列表"
    )
    @ApiResponses({
        @ApiResponse(responseCode = "200", description = "成功"),
        @ApiResponse(responseCode = "404", description = "字典类型不存在")
    })
    @SaCheckPermission("system:dict:query")
    @GetMapping(value = "/{dictId}")
    public R<SysDictType> getInfo(
        @Parameter(name = "字典类型 ID", required = true, example = "1")
        @PathVariable("dictId") Long dictId
    ) {
        return R.ok(dictTypeService.selectDictTypeById(dictId));
    }
}
```

---

**文档版本：** 1.0  
**创建日期：** 2026-03-13  
**审核状态：** 待审核
