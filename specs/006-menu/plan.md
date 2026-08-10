# 006-menu 技术方案 (plan.md)

> 对应规格：spec.md | 模块：006-menu | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, MyBatis-Plus 3.5.17, Sa-Token

### 1.2 依赖
- ruoyi-common-core（R 响应、BaseController、TreeBuildUtils、StringUtils、Constants）
- ruoyi-common-mybatis（BaseMapperPlus、MPJBaseMapper、QueryBuilder）
- ruoyi-common-security（Sa-Token、LoginHelper）
- ruoyi-common-redis（@RepeatSubmit）
- ruoyi-common-log（@Log、BusinessType）
- Hutool（Tree 结构、CollUtil）

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅ | Controller → ISysMenuService/SysMenuServiceImpl → SysMenuMapper → Entity |
| 注解驱动 | ✅ | @SaCheckPermission + @SaCheckRole（超级管理员）双重权限控制 |
| 树构建 | ✅ | TreeBuildUtils 动态规划算法避免递归 |

## 3. 数据模型

### 3.1 核心表
- `sys_menu` — 菜单主表，自引用树结构（parentId），无逻辑删除
- `sys_role_menu` — 角色-菜单关联（多对多）

### 3.2 关键字段规则
- `menuId`: 雪花ID主键
- `parentId`: 父菜单ID，根节点为 0（Constants.TOP_PARENT_ID）
- `menuType`: M=目录（SystemConstants.TYPE_DIR）、C=菜单（SystemConstants.TYPE_MENU）、F=按钮（SystemConstants.TYPE_BUTTON）
- `menuName`: 在同一 parentId 下唯一（FR-006-006）
- `path`: 路由地址，目录/菜单类型需唯一性校验（FR-006-007）
- `component`: 组件路径，如 `system/user/index`，空时根据父级类型自动推断（LAYOUT/INNER_LINK/PARENT_VIEW）
- `perms`: 权限标识字符串，格式如 `system:user:list`，正则 `^[a-zA-Z0-9:_*-]+$`（FR-006-016）
- `isFrame`: Y=外链（需以 http(s):// 开头），N=非外链（FR-006-008）
- `isCache`: Y=缓存，N=不缓存
- `visible`: 0=显示，1=隐藏（侧边栏不可见）
- `status`: 0=正常，1=停用
- `queryParam`: JSON 格式的路由参数
- `icon`: 菜单图标
- `activeMenu`: 激活菜单路径（高亮用）
- `ext`: 扩展字段，透传到 RouterVo

### 3.3 特殊字段（非持久化）
- `parentName`: @TableField(exist=false)，前端展示用
- `children`: @TableField(exist=false)，树构建用

## 4. 接口契约

### 4.1 提供接口

| 端点 | 方法 | 权限 | 说明 | 对应 FR |
|------|------|------|------|---------|
| `/system/menu/getRouters` | GET | 登录即可 | 获取前端路由 | FR-006-013~015 |
| `/system/menu/list` | GET | SUPER_ADMIN or system:menu:list | 查询菜单列表 | FR-006-001 |
| `/system/menu/treeselect` | GET | system:menu:query | 菜单下拉树 | FR-006-002 |
| `/system/menu/roleMenuTreeselect/{roleId}` | GET | system:menu:query | 角色菜单树（回显） | FR-006-003 |
| `/system/menu/{menuId}` | GET | SUPER_ADMIN or system:menu:query | 查询菜单详情 | — |
| `/system/menu` | POST | SUPER_ADMIN + system:menu:add | 新增菜单 | FR-006-005~008 |
| `/system/menu` | PUT | SUPER_ADMIN + system:menu:edit | 修改菜单 | FR-006-006~009 |
| `/system/menu/{menuId}` | DELETE | SUPER_ADMIN + system:menu:remove | 删除单个菜单 | FR-006-010 |
| `/system/menu/cascade/{menuIds}` | DELETE | SUPER_ADMIN + system:menu:remove | 级联批量删除 | FR-006-011 |

### 4.2 提供给其他模块
- `selectMenuPermsByUserId()` — 登录时加载用户权限标识（FR-006-017）
- `selectMenuPermsByRoleId()` — 按角色查询权限（FR-006-018）
- `selectMenuPermsByRoleIds()` — 批量查询角色权限映射（FR-006-019）
- `selectMenuTreeByUserId()` — 查询用户菜单树（FR-006-012）
- `selectMenuListByRoleId()` — 查询角色已分配菜单 ID（FR-006-003）
- `buildMenus()` — 构建前端 RouterVo（FR-006-013~015）

## 5. 实现策略

### 5.1 架构模式
- Controller: SysMenuController(/system/menu)，注入 ISysMenuService
- Service: ISysMenuService 接口 + SysMenuServiceImpl 实现
- Mapper: SysMenuMapper（主表）+ SysRoleMapper + SysRoleMenuMapper

### 5.2 关键算法

#### A. 菜单列表查询（FR-006-001）
- 超级管理员：直接 `menuMapper.lambda().条件查询().voList()` 返回全部菜单
- 普通用户：`menuMapper.selectMenuListByUserId()` 通过 MPJBaseMapper 联表查询（sys_menu JOIN sys_role_menu JOIN sys_user_role JOIN sys_role），按角色过滤

#### B. 用户菜单树查询（FR-006-012）
- 超级管理员：`menuMapper.selectMenuTreeAll()` 查询全部正常状态的目录和菜单
- 普通用户：`menuMapper.selectMenuTreeByUserId()` 联表过滤
- 树构建：`TreeBuildUtils.build(menus, TOP_PARENT_ID, SysMenu::getParentId, ...)` 使用动态规划算法构建

#### C. 前端路由构建 buildMenus()（FR-006-013~015）
核心逻辑位于 SysMenuServiceImpl.buildMenus()：
1. 遍历菜单树，为每个菜单创建 RouterVo
2. 路由名称：`menu.getRouteName() + menuId`（path 首字母大写 + menuId）
3. 路由路径：`menu.getRouterPath()` 处理三种情况：
   - 一级目录：`"/" + path`
   - 一级菜单（非外链）：`"/"`
   - 其他：直接使用 path
4. 组件路径：`menu.getComponentInfo()` 处理四种情况：
   - 有显式 component 且非 menuFrame → 使用显式组件
   - 无 component 且是内链 → `SystemConstants.INNER_LINK`
   - 无 component 且是 parentView → `SystemConstants.PARENT_VIEW`
   - 其他 → `SystemConstants.LAYOUT`（默认布局组件）
5. 子路由处理（三种分支）：
   - **目录类型有子菜单**：设置 alwaysShow=true, redirect="noRedirect"，递归构建 children
   - **一级菜单（menuFrame）**：清空 meta，将菜单包装为单子节点的 children 结构
   - **一级外链（innerLink）**：path 设为 "/"，内链域名替换后作为子路由

#### D. 路由唯一性校验（FR-006-007）
checkRouteConfigUnique() 方法检测三种冲突：
1. **同级路由冲突**：同 parentId 下 path 不能重复
2. **根目录路由冲突**：TOP_PARENT_ID 下 path 全局唯一
3. **路由名称冲突**：routeName（path 首字母大写）同 menuType 全局唯一

#### E. 权限标识查询（FR-006-017~019）
- 单个用户：`selectMenuPermsByUserId()` 通过 MPJBaseMapper 四表联查，过滤空白 perms，返回 HashSet
- 单个角色：`selectMenuPermsByRoleId()` 三表联查
- 批量角色：`selectMenuPermsByRoleIds()` 查询后按 roleId 分组构建 Map<Long, Set<String>>

#### F. 级联批量删除（FR-006-011）
SysMenuController.remove(menuIds[]) 接收数组，调用 `menuService.deleteMenuById(menuIdList)`，内部同时清理 sys_role_menu 关联记录（roleMenuMapper.deleteByMenuIds）

### 5.3 错误处理
- ServiceException: 菜单名称重复、路由冲突
- Controller 层短路校验: 外链地址格式、上级菜单不能选自己
- R.warn(): 存在子菜单不允许删除、菜单已分配不允许删除

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| SysMenuController | REST API | system/controller/system/SysMenuController.java |
| ISysMenuService | 业务接口 | system/service/ISysMenuService.java |
| SysMenuServiceImpl | 业务实现（含 buildMenus/路由构建） | system/service/impl/SysMenuServiceImpl.java |
| SysMenuMapper | 数据访问（含权限查询/树查询/联表查询） | system/mapper/SysMenuMapper.java |
| SysMenu.java | 菜单实体（含路由计算方法） | system/domain/SysMenu.java |
| SysMenuBo.java | 请求体（含权限标识正则校验） | system/domain/bo/SysMenuBo.java |
| SysMenuVo.java | 响应体 | system/domain/vo/SysMenuVo.java |
| RouterVo.java | 前端路由 VO | system/domain/vo/RouterVo.java |
| MetaVo.java | 路由元信息 VO | system/domain/vo/MetaVo.java |
