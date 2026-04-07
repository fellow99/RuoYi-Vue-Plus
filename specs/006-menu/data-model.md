# 006-菜单管理 - 数据模型

**模块编号：** 006  
**模块名称：** 菜单管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 菜单权限表 (sys_menu)

**表名：** `sys_menu`  
**说明：** 存储系统菜单权限信息

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| menu_id | BIGINT | - | YES | - | 菜单 ID（主键） |
| parent_id | BIGINT | - | NO | 0 | 父菜单 ID |
| menu_name | VARCHAR | 50 | YES | - | 菜单名称 |
| order_num | INT | - | NO | 0 | 显示顺序 |
| path | VARCHAR | 200 | NO | - | 路由地址 |
| component | VARCHAR | 255 | NO | - | 组件路径 |
| query_param | VARCHAR | 255 | NO | - | 路由参数 |
| is_frame | CHAR | 1 | NO | '1' | 是否外链（0 是 1 否） |
| is_cache | CHAR | 1 | NO | '0' | 是否缓存（0 缓存 1 不缓存） |
| menu_type | CHAR | 1 | NO | - | 类型（M 目录 C 菜单 F 按钮） |
| visible | CHAR | 1 | NO | '0' | 显示状态（0 显示 1 隐藏） |
| status | CHAR | 1 | NO | '0' | 菜单状态（0 正常 1 停用） |
| perms | VARCHAR | 100 | NO | - | 权限标识 |
| icon | VARCHAR | 100 | NO | '#' | 菜单图标 |
| remark | VARCHAR | 500 | NO | - | 备注 |
| create_by | BIGINT | - | NO | NULL | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| update_by | BIGINT | - | NO | NULL | 更新者 |
| update_time | DATETIME | - | NO | NULL | 更新时间 |

**索引：**
- PRIMARY KEY (`menu_id`)
- KEY `idx_parent_id` (`parent_id`)
- KEY `idx_menu_name` (`menu_name`)
- KEY `idx_visible` (`visible`)
- KEY `idx_status` (`status`)

**约束：**
- `menu_name` 在同级菜单中唯一（业务约束）
- `path` 和路由名称在全系统唯一（业务约束）

---

## 二、数据对象类

### 2.1 SysMenu (DO - 数据对象)

```java
@Data
@EqualsAndHashCode(callSuper = true)
@TableName("sys_menu")
public class SysMenu extends BaseEntity {

    /**
     * 菜单 ID
     */
    @TableId(value = "menu_id")
    private Long menuId;

    /**
     * 父菜单 ID
     */
    private Long parentId;

    /**
     * 菜单名称
     */
    private String menuName;

    /**
     * 显示顺序
     */
    private Integer orderNum;

    /**
     * 路由地址
     */
    private String path;

    /**
     * 组件路径
     */
    private String component;

    /**
     * 路由参数
     */
    private String queryParam;

    /**
     * 是否为外链（0 是 1 否）
     */
    private String isFrame;

    /**
     * 是否缓存（0 缓存 1 不缓存）
     */
    private String isCache;

    /**
     * 类型（M 目录 C 菜单 F 按钮）
     */
    private String menuType;

    /**
     * 显示状态（0 显示 1 隐藏）
     */
    private String visible;

    /**
     * 菜单状态（0 正常 1 停用）
     */
    private String status;

    /**
     * 权限标识
     */
    private String perms;

    /**
     * 菜单图标
     */
    private String icon;

    /**
     * 备注
     */
    private String remark;

    /**
     * 父菜单名称（非数据库字段）
     */
    @TableField(exist = false)
    private String parentName;

    /**
     * 子菜单（非数据库字段）
     */
    @TableField(exist = false)
    private List<SysMenu> children = new ArrayList<>();
}
```

### 2.2 SysMenuBo (BO - 业务对象)

```java
@AutoMapper(target = SysMenu.class, reverseConvertGenerate = false)
public class SysMenuBo extends BaseEntity {

    private Long menuId;

    @NotBlank(message = "菜单名称不能为空")
    @Size(min = 2, max = 50, message = "菜单名称长度不能超过{max}个字符")
    private String menuName;

    @NotNull(message = "显示顺序不能为空")
    private Integer orderNum;

    private Long parentId;

    @Size(max = 200, message = "路由地址长度不能超过{max}个字符")
    private String path;

    @Size(max = 255, message = "组件路径长度不能超过{max}个字符")
    private String component;

    @Size(max = 255, message = "路由参数长度不能超过{max}个字符")
    private String queryParam;

    @Size(min = 0, max = 1, message = "是否外链长度不能超过{max}个字符")
    private String isFrame;

    @Size(min = 0, max = 1, message = "是否缓存长度不能超过{max}个字符")
    private String isCache;

    @Size(min = 1, max = 1, message = "菜单类型长度必须为{max}个字符")
    private String menuType;

    @Size(min = 0, max = 1, message = "显示状态长度不能超过{max}个字符")
    private String visible;

    @Size(min = 0, max = 1, message = "菜单状态长度不能超过{max}个字符")
    private String status;

    @Size(max = 100, message = "权限标识长度不能超过{max}个字符")
    private String perms;

    @Size(max = 100, message = "菜单图标长度不能超过{max}个字符")
    private String icon;

    @Size(max = 500, message = "备注长度不能超过{max}个字符")
    private String remark;
}
```

### 2.3 SysMenuVo (VO - 视图对象)

```java
@AutoMapper(target = SysMenu.class)
public class SysMenuVo implements Serializable {

    private Long menuId;
    private Long parentId;
    private String menuName;
    private Integer orderNum;
    private String path;
    private String component;
    private String queryParam;
    private String isFrame;
    private String isCache;
    private String menuType;
    private String visible;
    private String status;
    private String perms;
    private String icon;
    private String remark;
    private String parentName;
    private List<SysMenuVo> children;
}
```

### 2.4 RouterVo (路由视图对象)

```java
@Data
public class RouterVo implements Serializable {

    /**
     * 路由名字
     */
    private String name;

    /**
     * 路由地址
     */
    private String path;

    /**
     * 隐藏路由
     */
    private boolean hidden;

    /**
     * 重定向地址
     */
    private String redirect;

    /**
     * 路由参数
     */
    private String query;

    /**
     * 路由描述
     */
    private MetaVo meta;

    /**
     * 子路由
     */
    private List<RouterVo> children;
}
```

### 2.5 MetaVo (路由元信息)

```java
@Data
public class MetaVo implements Serializable {

    /**
     * 设置该路由的名称，如果不设置，无法使用 keep-alive
     */
    private String title;

    /**
     * 设置该路由的图标
     */
    private String icon;

    /**
     * 是否外链（true 是 false 否）
     */
    private boolean isLink;

    /**
     * 内链地址
     */
    private String link;

    /**
     * 是否缓存（true 缓存 false 不缓存）
     */
    private boolean isKeepAlive;

    /**
     * 是否隐藏（true 隐藏 false 显示）
     */
    private boolean isHidden;
}
```

---

## 三、关联关系

### 3.1 角色菜单关联表 (sys_role_menu)

**表名：** `sys_role_menu`  
**说明：** 存储角色与菜单的关联关系

| 字段名 | 类型 | 长度 | 必填 | 说明 |
|-------|------|------|------|------|
| role_id | BIGINT | - | YES | 角色 ID |
| menu_id | BIGINT | - | YES | 菜单 ID |

**索引：**
- PRIMARY KEY (`role_id`, `menu_id`)
- KEY `idx_menu_id` (`menu_id`)

### 3.2 租户套餐菜单关联

租户套餐菜单通过 `sys_tenant_package` 表的 `menu_ids` 字段存储，使用逗号分隔的菜单 ID 列表。

---

## 四、常量定义

### 4.1 菜单类型常量

```java
public class SystemConstants {
    /**
     * 目录类型
     */
    public static final String TYPE_DIR = "M";
    
    /**
     * 菜单类型
     */
    public static final String TYPE_MENU = "C";
    
    /**
     * 按钮类型
     */
    public static final String TYPE_BUTTON = "F";
}
```

### 4.2 是否常量

```java
public class SystemConstants {
    /**
     * 是
     */
    public static final String YES = "0";
    
    /**
     * 否
     */
    public static final String NO = "1";
}
```

### 4.3 显示状态常量

```java
public class SystemConstants {
    /**
     * 显示
     */
    public static final String SHOW = "0";
    
    /**
     * 隐藏
     */
    public static final String HIDE = "1";
}
```

### 4.4 菜单状态常量

```java
public class SystemConstants {
    /**
     * 正常
     */
    public static final String NORMAL = "0";
    
    /**
     * 停用
     */
    public static final String DISABLE = "1";
}
```

### 4.5 组件常量

```java
public class SystemConstants {
    /**
     * Layout 布局组件
     */
    public static final String LAYOUT = "Layout";
    
    /**
     * 内链组件
     */
    public static final String INNER_LINK = "InnerLink";
    
    /**
     * 父级视图组件
     */
    public static final String PARENT_VIEW = "ParentView";
}
```

---

## 五、树形结构处理

### 5.1 菜单树构建

菜单数据使用树形结构展示，通过 `parentId` 字段构建父子关系：

```java
/**
 * 构建菜单树
 * @param menus 菜单列表
 * @return 树形结构
 */
public List<Tree<Long>> buildMenuTreeSelect(List<SysMenuVo> menus) {
    // 使用 Hutool Tree 工具构建
    return TreeUtil.build(menus, Constants.TOP_PARENT_ID);
}
```

### 5.2 路由树构建

根据用户角色构建可访问的路由树：

```java
/**
 * 根据用户 ID 查询菜单权限
 * @param userId 用户 ID
 * @return 菜单列表
 */
public List<SysMenu> selectMenuTreeByUserId(Long userId) {
    // 查询用户角色菜单
    // 构建菜单树
    // 转换为路由对象
}
```

---

## 六、数据校验

### 6.1 菜单名称唯一性校验

```java
/**
 * 校验菜单名称是否唯一
 * @param menu 菜单信息
 * @return true-唯一 false-不唯一
 */
public boolean checkMenuNameUnique(SysMenuBo menu) {
    // 同级菜单名称必须唯一
    LambdaQueryWrapper<SysMenu> wrapper = new LambdaQueryWrapper<>();
    wrapper.eq(SysMenu::getMenuName, menu.getMenuName())
           .eq(SysMenu::getParentId, menu.getParentId());
    return menuService.count(wrapper) == 0;
}
```

### 6.2 路由配置唯一性校验

```java
/**
 * 校验路由配置是否唯一
 * @param menu 菜单信息
 * @return true-唯一 false-不唯一
 */
public boolean checkRouteConfigUnique(SysMenuBo menu) {
    // 路由名称和地址必须唯一
    LambdaQueryWrapper<SysMenu> wrapper = new LambdaQueryWrapper<>();
    wrapper.and(w -> w.eq(SysMenu::getPath, menu.getPath())
                      .or().eq(SysMenu::getComponent, menu.getComponent()));
    return menuService.count(wrapper) == 0;
}
```

---

## 七、特殊处理

### 7.1 内链地址处理

内链地址需要进行特殊字符替换：

```java
/**
 * 内链域名特殊字符替换
 * @param path 路径
 * @return 替换后的路径
 */
public static String innerLinkReplaceEach(String path) {
    return StringUtils.replaceEach(path, 
        new String[]{Constants.HTTP, Constants.HTTPS, Constants.WWW, ".", ":"},
        new String[]{"", "", "", "/", "/"});
}
```

### 7.2 路由名称生成

```java
/**
 * 获取路由名称
 * @return 路由名称
 */
public String getRouteName() {
    String routerName = StringUtils.capitalize(path);
    // 非外链并且是一级目录（类型为目录）
    if (isMenuFrame()) {
        routerName = StringUtils.EMPTY;
    }
    return routerName;
}
```

---

## 八、数据示例

### 8.1 系统管理目录

```json
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
  "icon": "system",
  "children": []
}
```

### 8.2 用户管理菜单

```json
{
  "menuId": 2,
  "parentId": 1,
  "menuName": "用户管理",
  "orderNum": 1,
  "path": "user",
  "component": "system/user/index",
  "menuType": "C",
  "visible": "0",
  "status": "0",
  "icon": "user",
  "perms": "system:user:list",
  "children": []
}
```

### 8.3 新增用户按钮

```json
{
  "menuId": 3,
  "parentId": 2,
  "menuName": "用户新增",
  "orderNum": 1,
  "menuType": "F",
  "visible": "0",
  "status": "0",
  "perms": "system:user:add",
  "children": []
}
```

