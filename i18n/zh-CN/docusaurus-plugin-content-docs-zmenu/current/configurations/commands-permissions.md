---
sidebar_position: 2
title: 命令与权限
description: 本页面列出了 zMenu 中所有可用的命令与权限
---

# 命令与权限

本页面列出了 zMenu 中所有可用的命令与权限。

## 主命令

主命令为 `/zm`（别名：`/zmenu`）。

基础权限：`zmenu.use`

## 命令列表

### 通用命令

| 命令 | 权限 | 描述 |
|------|------|------|
| `/zm` | `zmenu.use` | 显示帮助菜单 |
| `/zm version` | - | 显示插件版本信息 |
| `/zm list` | `zmenu.use` | 列出所有已加载的库存 |
| `/zm documentation` | `zmenu.documentation` | 查看文档链接 |
| `/zm addons` | `zmenu.use` | 列出官方扩展插件 |
| `/zm contributors` | `zmenu.use` | 显示插件贡献者 |

### 库存命令

| 命令 | 权限 | 描述 |
|------|------|------|
| `/zm open <inventory>` | `zmenu.open` | 为自己打开库存 |
| `/zm open <inventory> <player>` | `zmenu.open` | 为其他玩家打开库存 |
| `/zm open <inventory> <player> <args...>` | `zmenu.open` | 带参数打开库存 |

| `/zm create [name]` | `zmenu.create` | 从模板创建新库存 |
| `/zm editor [name]` | `zmenu.editor` | 打开库存编辑器 |

**示例：**
```
/zm open shop
/zm open shop Notch
/zm open shop Notch "Welcome to the shop!"
```

### 重载命令

| 命令 | 权限 | 描述 |
|------|------|------|
| `/zm reload` | `zmenu.reload` | 重载所有配置 |
| `/zm reload config` | `zmenu.reload` | 仅重载 config.yml 和消息文件 |
| `/zm reload inventory` | `zmenu.reload` | 重载所有库存 |
| `/zm reload inventory <name>` | `zmenu.reload` | 重载指定库存 |
| `/zm reload command` | `zmenu.reload` | 重载所有命令 |
| `/zm reload command <name>` | `zmenu.reload` | 重载指定命令 |

**示例：**
```
/zm reload
/zm reload inventory shop
/zm reload command warp
```

### 物品命令

| 命令 | 权限 | 描述 |
|------|------|------|
| `/zm giveitem <inventory> <player>` | `zmenu.giveitem` | 给玩家可点击物品 |
| `/zm giveopenitem <inventory> <player>` | `zmenu.giveopenitem` | 给玩家菜单开启物品 |
| `/zm save <name>` | `zmenu.save` | 将手持物品保存为库存模板 |

### 玩家数据命令

| 命令 | 权限 | 描述 |
|------|------|------|
| `/zm players add <player> <key> <value>` | `zmenu.players` | 为玩家数据添加值 |
| `/zm players set <player> <key> <value>` | `zmenu.players` | 设置玩家数据值 |
| `/zm players get <player> <key>` | `zmenu.players` | 获取玩家数据值 |
| `/zm players remove <player> <key>` | `zmenu.players` | 移除玩家数据键 |
| `/zm players removeall <key>` | `zmenu.players` | 从所有玩家移除指定键 |
| `/zm players keys <player>` | `zmenu.players` | 列出玩家的所有数据键 |
| `/zm players convert` | `zmenu.players` | 将 JSON 数据转换为 SQL |

**示例：**
```
/zm players set Notch coins 100
/zm players add Notch coins 50
/zm players get Notch coins
/zm players keys Notch
```

### 对话框命令

| 命令 | 权限 | 描述 |
|------|------|------|
| `/zm dialog open <dialog>` | `zmenu.dialog` | 为自己打开对话框 |
| `/zm dialog open <dialog> <player>` | `zmenu.dialog` | 为玩家打开对话框 |

### 实用命令

| 命令 | 权限 | 描述 |
|------|------|------|
| `/zm convert` | `zmenu.convert` | 从其他插件转换配置 |
| `/zm testdupe` | `zmenu.dupe` | 测试防复制系统 |
| `/zm dumplog` | `zmenu.dumplog` | 导出调试日志 |

## 权限列表

### 核心权限

| 权限 | 描述 | 默认 |
|------|------|------|
| `zmenu.use` | 访问基础命令 | `true` |
| `zmenu.open` | 打开库存 | `op` |
| `zmenu.open.bypass` | 绕过库存开启条件 | `op` |
| `zmenu.reload` | 重载配置 | `op` |
| `zmenu.create` | 创建新库存 | `op` |
| `zmenu.editor` | 访问库存编辑器 | `op` |

### 物品权限

| 权限 | 描述 | 默认 |
|------|------|------|
| `zmenu.giveitem` | 给予可点击物品 | `op` |
| `zmenu.giveopenitem` | 给予菜单开启物品 | `op` |
| `zmenu.save` | 保存物品为库存 | `op` |

### 玩家数据权限

| 权限 | 描述 | 默认 |
|------|------|------|
| `zmenu.players` | 管理玩家数据 | `op` |

### 实用权限

| 权限 | 描述 | 默认 |
|------|------|------|
| `zmenu.convert` | 从其他插件转换 | `op` |
| `zmenu.dupe` | 测试防复制系统 | `op` |
| `zmenu.documentation` | 查看文档 | `op` |
| `zmenu.dialog` | 打开对话框 | `op` |
| `zmenu.dumplog` | 导出调试日志 | `op` |

## 自定义命令权限

在 `commands/commands.yml` 中创建自定义命令时，可定义自定义权限：

```yaml
commands:
  shop:
    command: /shop
    inventory: shop_menu
    permission: myserver.shop
    aliases:
      - store
      - market
```

玩家需要拥有 `myserver.shop` 权限才能使用 `/shop` 命令。

## 库存专属权限

可通过查看条件（view requirements）要求特定权限才能打开库存：

```yaml
# 在库存文件中
view-requirement:
  requirements:
    - type: permission
      permission: "myserver.vip.menu"
      deny:
        - type: message
          messages:
            - "&c你需要 VIP 权限才能访问此菜单！"
```

## 管理员权限

为服务器管理员授予所有 zMenu 权限：

```
zmenu.*
```

此权限授予对所有 zMenu 命令的访问权限。

## LuckPerms 示例

授予基础使用权限：
```
/lp user <玩家> permission set zmenu.use true
```

授予全部管理员权限：
```
/lp group admin permission set zmenu.* true
```

授予特定库存访问权限：
```
/lp group vip permission set myserver.vip.menu true
```

## 权限插件兼容性

zMenu 支持任何支持 Bukkit 权限的权限插件：
- LuckPerms（推荐）
- PermissionsEx
- GroupManager
- UltraPermissions
- 以及其他多种插件

## 后续步骤

- 了解 [占位符](./placeholders)
- 创建 [自定义命令](./custom-commands)
- 设置 [玩家数据](./player-data) 实现持久化存储