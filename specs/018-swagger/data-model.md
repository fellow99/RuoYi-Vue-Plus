# 018-API 文档 - 数据模型

**模块编号：** 018  
**模块名称：** API 文档  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、核心数据模型

### 1.1 OpenAPI 配置对象

**类名：** `org.dromara.common.doc.config.properties.SpringDocProperties`

**说明：** SpringDoc 文档配置属性

| 属性名 | 类型 | 说明 |
|-------|------|------|
| info | InfoProperties | 文档基本信息 |
| externalDocs | ExternalDocumentation | 扩展文档地址 |
| tags | List<Tag> | 标签列表 |
| paths | Paths | 路径配置 |
| components | Components | 组件配置 |

---

### 1.2 文档基本信息

**类名：** `SpringDocProperties.InfoProperties`

**说明：** 文档基本信息配置

| 属性名 | 类型 | 说明 |
|-------|------|------|
| title | String | 文档标题 |
| description | String | 文档描述 |
| contact | Contact | 联系人信息 |
| license | License | 许可证信息 |
| version | String | 版本信息 |

---

### 1.3 联系人信息

**类名：** `io.swagger.v3.oas.models.info.Contact`

| 属性名 | 类型 | 说明 |
|-------|------|------|
| name | String | 联系人姓名 |
| email | String | 联系邮箱 |
| url | String | 联系网址 |

---

### 1.4 许可证信息

**类名：** `io.swagger.v3.oas.models.info.License`

| 属性名 | 类型 | 说明 |
|-------|------|------|
| name | String | 许可证名称 |
| url | String | 许可证地址 |

---

### 1.5 安全组件配置

**类名：** `io.swagger.v3.oas.models.Components`

**说明：** 安全认证组件配置

| 属性名 | 类型 | 说明 |
|-------|------|------|
| securitySchemes | Map<String, SecurityScheme> | 安全方案 |

**Sa-Token 配置示例：**
```java
Map<String, SecurityScheme> securitySchemes = new HashMap<>();
securitySchemes.put("Authorization", 
    new ApiKey()
        .name("Authorization")
        .in(In.HEADER)
);
```

---

## 二、文档注解模型

### 2.1 接口注解

**@Operation** - 接口描述

| 属性 | 类型 | 说明 |
|------|------|------|
| summary | String | 接口摘要 |
| description | String | 详细描述 |
| tags | String[] | 标签 |
| operationId | String | 操作 ID |

**示例：**
```java
@Operation(summary = "查询用户列表", description = "分页查询用户信息")
```

---

### 2.2 参数注解

**@Parameter** - 参数描述

| 属性 | 类型 | 说明 |
|------|------|------|
| name | String | 参数名 |
| description | String | 参数描述 |
| required | boolean | 是否必填 |
| in | ParameterIn | 参数位置 |

**示例：**
```java
@Parameter(name = "pageNum", description = "页码", required = true)
```

---

### 2.3 响应注解

**@ApiResponse** - 响应描述

| 属性 | 类型 | 说明 |
|------|------|------|
| responseCode | String | 响应码 |
| description | String | 响应描述 |
| content | Content | 响应内容 |

**示例：**
```java
@ApiResponses({
    @ApiResponse(responseCode = "200", description = "成功"),
    @ApiResponse(responseCode = "401", description = "未授权"),
    @ApiResponse(responseCode = "500", description = "服务器错误")
})
```

---

### 2.4 标签注解

**@Tag** - 模块标签

| 属性 | 类型 | 说明 |
|------|------|------|
| name | String | 标签名 |
| description | String | 标签描述 |

**示例：**
```java
@Tag(name = "用户管理", description = "用户相关接口")
```

---

## 三、数据模型示例

### 3.1 统一响应结构

**类名：** `org.dromara.common.core.domain.R<T>`

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {}
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| code | Integer | 响应码 |
| msg | String | 响应消息 |
| data | T | 响应数据 |

---

### 3.2 分页响应结构

**类名：** `org.dromara.common.mybatis.core.page.TableDataInfo<T>`

```json
{
  "code": 200,
  "msg": "查询成功",
  "data": {
    "rows": [],
    "total": 100,
    "pageNum": 1,
    "pageSize": 10,
    "pages": 10
  }
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| rows | List<T> | 数据列表 |
| total | Long | 总记录数 |
| pageNum | Integer | 当前页码 |
| pageSize | Integer | 每页数量 |
| pages | Integer | 总页数 |

---

### 3.3 分页查询参数

**类名：** `org.dromara.common.mybatis.core.page.PageQuery`

```json
{
  "pageNum": 1,
  "pageSize": 10,
  "beginTime": "2026-03-13",
  "endTime": "2026-03-14"
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| pageNum | Integer | 页码 |
| pageSize | Integer | 每页数量 |
| beginTime | String | 开始时间 |
| endTime | String | 结束时间 |

---

## 四、OpenAPI 配置结构

### 4.1 OpenAPI 对象

```yaml
openapi: 3.0.1
info:
  title: RuoYi-Vue-Plus API
  description: RuoYi-Vue-Plus 接口文档
  version: 5.X
paths:
  /system/user/list:
    get:
      summary: 查询用户列表
      tags:
        - 用户管理
      parameters:
        - name: pageNum
          in: query
          schema:
            type: integer
      responses:
        '200':
          description: 成功
components:
  securitySchemes:
    Authorization:
      type: apiKey
      name: Authorization
      in: header
```

---

## 五、数据流转

### 5.1 文档生成流程

```
Controller 扫描 → 解析注解 → 生成 OpenAPI 模型 → 渲染 UI
```

### 5.2 请求测试流程

```
UI 填写参数 → 发送请求 → 后端处理 → 返回响应 → UI 展示
```

---

## 六、配置示例

### 6.1 application.yml 配置

```yaml
springdoc:
  api-docs:
    enabled: true
    path: /v3/api-docs
  swagger-ui:
    enabled: true
    path: /swagger-ui.html
    tags-sorter: alpha
    operations-sorter: alpha
  show-actuator: false
  default-flat-param-object: true

knife4j:
  enable: true
  setting:
    language: zh_cn
```

### 6.2 Java 配置

```java
@Configuration
public class SpringDocConfig {
    
    @Bean
    public OpenAPI openApi() {
        return new OpenAPI()
            .info(new Info()
                .title("RuoYi-Vue-Plus API")
                .description("RuoYi-Vue-Plus 接口文档")
                .version("5.X")
            )
            .addSecurityItem(new SecurityRequirement()
                .addList("Authorization")
            )
            .components(new Components()
                .addSecuritySchemes("Authorization",
                    new ApiKey()
                        .name("Authorization")
                        .in(In.HEADER)
                )
            );
    }
}
```

---

**文档版本：** 1.0  
**创建日期：** 2026-03-13  
**审核状态：** 待审核
