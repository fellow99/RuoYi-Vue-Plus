# 004-dept 技术方案 (plan.md)

> 对应规格：spec.md | 模块：004-dept | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, MyBatis-Plus 3.5.17, Sa-Token

### 1.2 依赖
- ruoyi-common-core（R 响应、BaseController、TreeBuildUtils、StringUtils）
- ruoyi-common-mybatis（BaseMapperPlus、@DataPermission、LambdaQueryBuilder）
- ruoyi-common-security（Sa-Token、LoginHelper）
- ruoyi-common-redis（@RepeatSubmit、@Cacheable/@CacheEvict、CacheUtils）
- ruoyi-common-log（@Log、BusinessType）
- ruoyi-api（DeptService 接口供其他模块调用）
- Hutool（Tree 结构、Convert、BeanUtil）

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅ | Controller → ISysDeptService/SysDeptServiceImpl → SysDeptMapper → Entity |
| 注解驱动 | ✅ | @SaCheckPermission 权限控制，@DataPermission 数据权限过滤 |
| 缓存规范 | ✅ | @Cacheable 查询缓存，@CacheEvict 修改后清除缓存 |

## 3. 数据模型

### 3.1 核心表
- `sys_dept` — 部门主表，自引用树结构（parentId + ancestors）

### 3.2 关键字段规则
- `deptId`: 雪花ID主键
- `parentId`: 父部门ID，根节点为 0
- `deptName`: 唯一（同父级下），新增/修改时校验（FR-004-004, FR-004-006）
- `ancestors`: 祖级列表，格式 `"0,100,200"`，逗号分隔（FR-004-005, FR-004-007）
- `orderNum`: 排序号，升序排列
- `status`: 0=正常，1=停用，停用前校验子部门状态和用户关联（FR-004-009）
- `delFlag`: @TableLogic 逻辑删除标记
- `children`: @TableField(exist=false)，非持久化字段，仅用于树构建

### 3.3 树结构策略
- 自引用树：通过 `parent_id` 字段关联父节点
- 祖级列表：`ancestors` 字段冗余存储所有祖先 ID，用于 `FIND_IN_SET` 快速查询子树
- 子节点查询：`SysDeptMapper.selectListByParentId()` 使用 `findInSet(parentId, SysDept::getAncestors)` 查找所有子部门

## 4. 接口契约

### 4.1 提供接口

| 端点 | 方法 | 说明 | 对应 FR |
|------|------|------|---------|
| `/system/dept/list` | GET | 查询部门列表 | FR-004-001 |
| `/system/dept/list/exclude/{deptId}` | GET | 查询部门列表（排除节点及子节点） | FR-004-003 |
| `/system/dept/{deptId}` | GET | 查询部门详情 | — |
| `/system/dept` | POST | 新增部门 | FR-004-004, FR-004-005 |
| `/system/dept` | PUT | 修改部门 | FR-004-006, FR-004-007 |
| `/system/dept/{deptId}` | DELETE | 删除部门 | FR-004-010 |
| `/system/dept/optionselect` | GET | 部门选择框列表 | — |

### 4.2 消费接口
- ISysPostService.countPostByDeptId() — 删除部门前检查岗位关联（FR-004-010）
- SysUserMapper — 检查部门下是否有用户（FR-004-009, FR-004-010）

### 4.3 提供给其他模块
- `selectDeptTreeList()` — 提供部门树供角色数据权限配置（SysRoleController）
- `selectDeptListByRoleId()` — 根据角色 ID 查询已选部门（FR-004-014）
- `selectDeptByIds()` — 按 ID 列表查询部门
- `checkDeptDataScope()` — 部门数据权限校验
- `DeptService` (ruoyi-api) — 跨模块调用接口

## 5. 实现策略

### 5.1 架构模式
- Controller: SysDeptController(/system/dept)，注入 ISysDeptService + ISysPostService
- Service: ISysDeptService 接口 + SysDeptServiceImpl 实现（同时实现 DeptService API）
- Mapper: SysDeptMapper（主表），额外依赖 SysRoleMapper、SysUserMapper 做关联校验

### 5.2 关键算法
- **新增部门时设置 ancestors**（FR-004-005）：
  ```
  父部门 ancestors = "0,100"
  → 新部门 ancestors = "0,100," + parentId = "0,100,100"
  ```
- **修改上级部门时刷新子部门 ancestors**（FR-004-007）：
  1. 调用 `deptMapper.selectDeptAndChildById(deptId)` 获取自身及所有子部门 ID
  2. 遍历这些部门，重新计算 ancestors（新父部门.ancestors + "," + 新父部门.deptId）
  3. 批量更新
- **排除节点查询**（FR-004-003）：`deptMapper.selectListByParentId(deptId)` 返回 ancestors 包含 deptId 的部门列表，Controller 层 removeIf 过滤
- **部门树构建**：`buildDeptTreeSelect()` 使用 TreeBuildUtils 工具类构建 Hutool Tree 结构

### 5.3 错误处理
- ServiceException: 名称重复、默认部门不可删、存在子部门/用户/岗位不可删
- Controller 层：R.warn() 返回警告提示，R.fail() 返回校验失败

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| SysDeptController | REST API | system/controller/system/SysDeptController.java |
| ISysDeptService | 业务接口 | system/service/ISysDeptService.java |
| SysDeptServiceImpl | 业务实现 | system/service/impl/SysDeptServiceImpl.java |
| SysDeptMapper | 数据访问 | system/mapper/SysDeptMapper.java |
| SysDept.java | 部门实体（含 children/@TableLogic） | system/domain/SysDept.java |
| SysDeptBo.java | 请求体 | system/domain/bo/SysDeptBo.java |
| SysDeptVo.java | 响应体 | system/domain/vo/SysDeptVo.java |
