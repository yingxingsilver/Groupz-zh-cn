---
sidebar_position: 11
title: Config.yml
description: 主配置文件参考指南
---

# zMenu 主配置文件（config.yml）


## Config.yml

`config.yml` 是 zMenu 插件的主配置文件，用于控制插件全局设置、数据库配置、性能选项等。

### 文件位置
`plugins/zMenu/config.yml`

---

## 完整配置参考

```yaml
#######################################
#          调试设置                   #
#######################################

# 启用控制台调试信息
enable-debug: false

# 启用调试计时信息
enable-debug-time: false

#######################################
#          存储设置                   #
#######################################

# 玩家数据存储类型
# 可选值: SQLITE, MYSQL, MARIADB, NONE
storage-type: SQLITE

# 数据库配置（用于 MySQL/MariaDB）
database-configuration:
  table-prefix: "zmenu_"
  host: "localhost"
  port: 3306
  user: "username"
  password: "password"
  database: "zmenu"

#######################################
#          格式化设置                 #
#######################################

# 启用 MiniMessage 格式（仅限 Paper/Purpur 服务器）
# 启用后可使用现代文本格式，如 <gradient:red:blue>
enable-mini-message-format: true

#######################################
#          性能设置                   #
#######################################

# 缓存物品栈以提升性能
enable-cache-item-stack: true

# 启用 PlaceholderAPI 缓存
enable-cache-placeholder-api: false

# PlaceholderAPI 缓存时长（游戏刻，20 = 1 秒）
cache-placeholder-api: 20

# 离线玩家数据缓存时长（秒）
cache-offline-player: 300

#######################################
#          点击设置                   #
#######################################

# 启用点击冷却以防止连点刷屏
enable-cooldown-click: true

# 冷却时长（毫秒）
cooldown-click-milliseconds: 100

#######################################
#          安全设置                   #
#######################################

# 启用反复制保护
enable-anti-dupe: true

# 记录玩家打开库存的行为
enable-player-open-inventory-logs: true

#######################################
#          菜单设置                   #
#######################################

# 默认主菜单库存名称
main-menu: "example"

# 使用副手交换键（F 键）打开主菜单
use-swap-item-off-hand-key-to-open-main-menu: false
```

---

## 配置选项详解

### 调试设置

**enable-debug**  
启用详细的控制台调试信息，便于排查问题。

```yaml
enable-debug: false
```

**适用场景：**
- 排查库存加载问题
- 调试占位符问题
- 向开发者提交错误报告

**enable-debug-time**  
显示操作的耗时信息。

```yaml
enable-debug-time: false
```

---

### 存储设置

**storage-type**  
决定玩家数据的存储方式。

| 类型      | 说明                     |
|-----------|--------------------------|
| SQLITE    | 本地文件数据库（默认）   |
| MYSQL     | MySQL 服务器             |
| MARIADB   | MariaDB 服务器           |
| NONE      | 不使用持久化存储         |

```yaml
storage-type: SQLITE
```

**推荐方案：**
- 单服务器：使用 `SQLITE`
- 网络/BungeeCord：使用 `MYSQL` 或 `MARIADB`
- 无需存储玩家数据：使用 `NONE`

**database-configuration**  
MySQL/MariaDB 连接配置。

```yaml
database-configuration:
  table-prefix: "zmenu_"
  host: "localhost"
  port: 3306
  user: "minecraft"
  password: "secure_password"
  database: "minecraft_db"
```

| 选项          | 说明               |
|---------------|--------------------|
| table-prefix  | 数据库表前缀       |
| host          | 数据库服务器地址   |
| port          | 数据库端口（默认 3306）|
| user          | 数据库用户名       |
| password      | 数据库密码         |
| database      | 数据库名称         |

---

### 格式化设置

**enable-mini-message-format**  
启用 MiniMessage 文本格式（仅 Paper/Purpur/Pufferfish 支持）。

```yaml
enable-mini-message-format: true
```

启用后可使用以下格式：

```yaml
name: "<gradient:red:blue>渐变文字</gradient>"
lore:
  - "<rainbow>彩虹文字！</rainbow>"
  - "<bold><gold>加粗金色</gold></bold>"
```

---

### 性能设置

**enable-cache-item-stack**  
缓存已创建的 ItemStack 以提升性能。

```yaml
enable-cache-item-stack: true
```

**建议：** 除非遇到问题，否则保持启用。

**enable-cache-placeholder-api**  
缓存 PlaceholderAPI 结果以减少解析开销。

```yaml
enable-cache-placeholder-api: false
```

:::warning
启用后占位符将不会实时更新，请谨慎使用。
:::

**cache-placeholder-api**  
占位符缓存时长（游戏刻）。

```yaml
cache-placeholder-api: 20  # 1 秒
```

**cache-offline-player**  
离线玩家数据缓存时长（秒）。

```yaml
cache-offline-player: 300  # 5 分钟
```

---

### 点击设置

**enable-cooldown-click**  
通过添加点击冷却防止连点。

```yaml
enable-cooldown-click: true
```

**cooldown-click-milliseconds**  
点击冷却时长。

```yaml
cooldown-click-milliseconds: 100  # 0.1 秒
```

**调整建议：**
- 值越低：响应更灵敏，但可能误触双击
- 值越高：更安全，但可能感觉迟滞

---

### 安全设置

**enable-anti-dupe**  
启用反物品复制保护系统。

```yaml
enable-anti-dupe: true
```

可检测并阻止通过库存 GUI 进行的常见物品复制漏洞。

**enable-player-open-inventory-logs**  
记录玩家打开 zMenu 库存的行为。

```yaml
enable-player-open-inventory-logs: true
```

**适用场景：**
- 监控玩家活动
- 问题排查
- 安全审计

---

### 菜单设置

**main-menu**  
`MAIN_MENU` 按钮类型和 F 键快捷方式默认打开的库存。

```yaml
main-menu: "example"
```

此值应与 `inventories/` 文件夹中的库存文件名（不含 `.yml` 后缀）匹配。

**use-swap-item-off-hand-key-to-open-main-menu**  
允许玩家按 F 键（副手交换键）打开主菜单。

```yaml
use-swap-item-off-hand-key-to-open-main-menu: false
```

**启用场景：**
- 希望无需命令即可快速访问菜单
- 服务器设有主枢纽菜单

**禁用场景：**
- 玩家需要 F 键进行实际物品交换
- 使用了自定义资源包并绑定了 F 键功能

---

## 示例配置

### 基础服务器（SQLite）
```yaml
enable-debug: false
storage-type: SQLITE
enable-mini-message-format: true
enable-cache-item-stack: true
enable-anti-dupe: true
enable-cooldown-click: true
cooldown-click-milliseconds: 100
main-menu: "main"
```

### 网络服务器（MySQL）
```yaml
enable-debug: false
storage-type: MYSQL
database-configuration:
  table-prefix: "zmenu_"
  host: "mysql.mynetwork.com"
  port: 3306
  user: "zmenu_user"
  password: "secure_password_here"
  database: "minecraft_network"
enable-mini-message-format: true
enable-cache-item-stack: true
enable-cache-placeholder-api: true
cache-placeholder-api: 40
enable-anti-dupe: true
main-menu: "hub_menu"
```

### 高性能配置
```yaml
enable-debug: false
storage-type: SQLITE
enable-cache-item-stack: true
enable-cache-placeholder-api: true
cache-placeholder-api: 60
cache-offline-player: 600
enable-cooldown-click: true
cooldown-click-milliseconds: 150
```

### 开发/测试环境
```yaml
enable-debug: true
enable-debug-time: true
storage-type: SQLITE
enable-cache-item-stack: false
enable-cache-placeholder-api: false
enable-cooldown-click: false
enable-player-open-inventory-logs: true
```

---

## 重载配置

修改后执行以下命令重载配置：
```
/zm reload config
```

或重载全部内容：
```
/zm reload
```

:::note
数据库配置更改需要重启服务器才能生效。
:::

---

## 最佳实践

- **从默认值开始**：仅修改必需项
- **大型服务器启用缓存**：显著提升性能
- **网络服使用 MySQL**：实现跨服务器数据共享
- **保持反复制启用**：保护经济系统安全
- **调试模式仅用于开发**：生产环境应禁用
- **合理设置点击冷却**：平衡响应速度与安全性

---

## 故障排除

### 数据库连接失败
- 核实凭据是否正确
- 检查数据库服务是否运行
- 确认数据库是否存在
- 检查防火墙是否允许连接
- 验证用户权限是否充足

### 占位符未更新
- 临时禁用占位符缓存
- 检查是否已安装 PlaceholderAPI
- 确认相关扩展插件已下载

### 性能问题
- 启用物品栈缓存
- 启用占位符缓存
- 适当增加点击冷却时长
- 检查是否存在库存循环（A 打开 B，B 又打开 A）

---

## 后续步骤

- 设置 [自定义命令](./custom-commands)
- 配置 [玩家数据](./player-data) 存储
- 了解 [开发 API](../development/api-introduction)