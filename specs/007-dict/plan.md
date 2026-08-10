# 007-dict 技术方案 (plan.md)

> 对应规格：spec.md | 模块：007-dict | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, MyBatis-Plus 3.5.17

### 1.2 依赖
- ruoyi-common-core（R 响应、CacheNames、DictService 接口）
- ruoyi-common-mybatis（BaseMapperPlus、分页）
- ruoyi-common-redis（CacheUtils、Spring Cache）
- ruoyi-common-security（Sa-Token 权限）
- ruoyi-common-excel（ExcelBuilder 导出）
- ruoyi-common-log（@Log 操作日志）
- Lock4j（@Lock4j 分布式锁，刷新缓存防并发）

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅ | Controller → Service(接口+实现) → Mapper → Entity |
| 注解驱动 | ✅ | @SaCheckPermission 权限控制，@Cacheable/@CachePut/@CacheEvict 缓存 |
| 跨模块接口 | ✅ | ISysDictTypeServiceImpl 实现 DictService 接口，供其他模块通过 AOP 代理调用 |

## 3. 数据模型

### 3.1 核心表
- `sys_dict_type` — 字典类型表（dictId 主键，dictType 业务唯一键）
- `sys_dict_data` — 字典数据表（dictCode 主键，dictType + dictValue 联合唯一）

### 3.2 关键字段规则
- `SysDictType.dictType`: 业务唯一标识，如 "sys_user_status"，新增/修改时校验唯一性
- `SysDictData.dictType`: 外键关联字典类型，修改类型时级联更新
- `SysDictData.dictValue`: 同一 dictType 下唯一
- `SysDictData.isDefault`: Y=默认值 / N=非默认
- 继承 BaseEntity：createBy, createTime, updateBy, updateTime, remark

## 4. 接口契约

### 4.1 提供接口

**SysDictTypeController** (`/system/dict/type`):

| 方法 | 路径 | 权限 | 说明 |
|------|------|------|------|
| GET | `/list` | system:dict:list | 分页查询字典类型 |
| POST | `/export` | system:dict:export | 导出 Excel |
| GET | `/{dictId}` | system:dict:query | 查询字典类型详情 |
| POST | `/` | system:dict:add | 新增字典类型 |
| PUT | `/` | system:dict:edit | 修改字典类型（级联更新数据） |
| DELETE | `/{dictIds}` | system:dict:remove | 批量删除字典类型 |
| DELETE | `/refreshCache` | system:dict:remove | 刷新字典缓存（@Lock4j 防并发） |
| GET | `/optionselect` | — | 获取字典类型下拉列表 |

**SysDictDataController** (`/system/dict/data`):

| 方法 | 路径 | 权限 | 说明 |
|------|------|------|------|
| GET | `/list` | system:dict:list | 分页查询字典数据 |
| POST | `/export` | system:dict:export | 导出 Excel |
| GET | `/{dictCode}` | system:dict:query | 查询字典数据详情 |
| GET | `/type/{dictType}` | — | 根据字典类型查询数据列表 |
| POST | `/` | system:dict:add | 新增字典数据 |
| PUT | `/` | system:dict:edit | 修改字典数据 |
| DELETE | `/{dictCodes}` | system:dict:remove | 批量删除字典数据 |

### 4.2 消费接口
- 无外部依赖，字典模块为基础数据模块

### 4.3 跨模块接口
- `ISysDictTypeServiceImpl` 实现 `DictService`（ruoyi-api），提供 `getDictLabel()`、`getDictValue()`、`getAllDictByDictType()` 等方法
- 其他模块通过 `SpringUtils.getAopProxy(this)` 调用以走缓存代理

## 5. 实现策略

### 5.1 架构模式
标准四层 CRUD，双 Controller 模式：

```
SysDictTypeController(/system/dict/type) → ISysDictTypeService → SysDictTypeMapper → MySQL
SysDictDataController(/system/dict/data) → ISysDictDataService → SysDictDataMapper → MySQL
```

### 5.2 关键算法
- **缓存策略**：使用 Spring Cache 注解 + CacheUtils 手动驱逐
  - `CacheNames.SYS_DICT` — 缓存字典数据（key=dictType，value=List<SysDictDataVo>）
  - `CacheNames.SYS_DICT_TYPE` — 缓存字典类型（key=dictType，value=SysDictTypeVo）
  - 查询：`@Cacheable(cacheNames=CacheNames.SYS_DICT, key="#dictType")`
  - 新增：`@CachePut(cacheNames=CacheNames.SYS_DICT, key="#bo.dictType")`
  - 删除：`CacheUtils.evict(CacheNames.SYS_DICT, dictType)` 手动清除
  - 刷新缓存：`CacheUtils.clear(CacheNames.SYS_DICT)` 清空所有
- **级联更新**：修改 dictType 时，通过 MyBatis-Plus Lambda 更新关联的 dictData.dictType，同时驱逐新旧缓存
- **跨模块访问**：`SpringUtils.getAopProxy(this)` 获取代理对象调用 `@Cacheable` 方法，确保缓存生效

### 5.3 错误处理
- 新增/修改字典类型时 `dictType` 重复 → R.fail("字典类型已存在")
- 删除已分配数据的字典类型 → ServiceException("{}已分配,不能删除")
- 新增/修改字典数据时 `dictValue` 重复 → R.fail("字典键值已存在")
- 全局异常转 R 响应

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| SysDictTypeController | 字典类型 REST API | system/controller/system/ |
| SysDictDataController | 字典数据 REST API | system/controller/system/ |
| ISysDictTypeService | 字典类型业务接口 | system/service/ |
| SysDictTypeServiceImpl | 字典类型业务实现（含 DictService） | system/service/impl/ |
| ISysDictDataService | 字典数据业务接口 | system/service/ |
| SysDictDataServiceImpl | 字典数据业务实现 | system/service/impl/ |
| SysDictTypeMapper | 字典类型数据访问 | system/mapper/ |
| SysDictDataMapper | 字典数据数据访问 | system/mapper/ |
| SysDictType.java | 字典类型实体 | system/domain/ |
| SysDictData.java | 字典数据实体 | system/domain/ |
| SysDictTypeBo.java | 字典类型请求体 | system/domain/bo/ |
| SysDictDataBo.java | 字典数据请求体 | system/domain/bo/ |
| SysDictTypeVo.java | 字典类型响应体 | system/domain/vo/ |
| SysDictDataVo.java | 字典数据响应体 | system/domain/vo/ |
