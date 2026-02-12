---
sidebar_position: 10
title: 自定义命令
description: 创建自定义命令以打开您的界面菜单
---

# 自定义命令（Custom Commands）


## 自定义命令

zMenu 允许您创建自定义命令来打开界面菜单。玩家无需输入 `/zm open shop`，只需输入 `/shop` 即可。

## 配置文件

自定义命令定义在 `plugins/zMenu/commands/commands.yml` 文件中。

## 基本结构

```yaml
commands:
  shop:
    command: /shop
    inventory: shop_menu
```

此配置将创建 `/shop` 命令，用于打开名为 `shop_menu` 的界面。

## 配置选项

### 概览表

| 键（Key） | 类型 | 是否必需 | 说明 |
|----------|------|----------|------|
| `command` | 字符串 | 是 | 玩家将输入的主命令 |
| `inventory` | 字符串 | 是 | 要打开的界面名称 |
| `permission` | 字符串 | 否 | 使用该命令所需的权限 |
| `deny-message` | 字符串 | 否 | 权限不足时显示的消息 |
| `aliases` | 列表 | 否 | 命令的替代名称（别名） |
| `arguments` | 列表 | 否 | 带验证功能的命令参数 |
| `actions` | 列表 | 否 | 命令执行时触发的操作 |

### `command`（命令）

必需项。玩家将输入的命令。

```yaml
command: /shop
```

### `inventory`（界面）

必需项。命令执行时要打开的界面。可选择性地添加插件名称前缀。

```yaml
inventory: "shop"  # 打开 inventories/shop.yml

# 或带插件前缀
inventory: "MyPlugin:shop"  # 打开 MyPlugin 插件中的 shop 界面
```

### `permission`（权限）

可选。使用该命令所需的权限节点。

```yaml
permission: "myserver.shop"
```

### `deny-message`（拒绝消息）

当玩家缺少所需权限时显示的自定义消息。

```yaml
deny-message: "&c您没有访问商店的权限！"
```

### `aliases`（别名）

命令的替代名称。

```yaml
aliases:
  - store
  - market
  - buy
```

这将使 `/store`、`/market` 和 `/buy` 与 `/shop` 具有相同功能。

### `actions`（操作）

命令执行时触发的操作（在打开界面之前）。

```yaml
actions:
  - type: message
    messages:
      - "&a正在打开商店..."
  - type: sound
    sound: BLOCK_CHEST_OPEN
```

### `arguments`（参数）

定义带验证和自动补全功能的命令参数。

```yaml
arguments:
  - name: "category"
    isRequired: false
    auto-completion:
      - "weapons"
      - "armor"
      - "tools"
```

#### 参数选项

| 键（Key） | 类型 | 说明 |
|----------|------|------|
| `name` | 字符串 | 必需。参数的标识符 |
| `type` | 字符串 | 参数的验证器类型（见下文） |
| `isRequired` | 布尔值 | 该参数是否必须提供（默认：`false`） |
| `performMainAction` | 布尔值 | 使用此参数时是否执行主命令操作（默认：`true`） |
| `auto-completion` | 列表 | Tab 补全时建议的值 |
| `actions` | 列表 | 该参数特有的操作 |

#### 参数类型验证器

| 类型 | 说明 |
|------|------|
| `online-player` | 验证参数是否为在线玩家名称 |
| `player` | 验证参数是否为玩家（在线或离线） |
| `integer` | 验证参数是否为整数 |
| `double` | 验证参数是否为小数 |
| `boolean` | 验证参数是否为 true/false |
| `material` | 验证参数是否为有效的 Minecraft 物品材料 |
| `world` | 验证参数是否为有效的世界名称 |
| `entity-type` | 验证参数是否为有效的实体类型 |
| `location` | 验证参数是否为有效的坐标位置 |

#### 带类型验证的参数示例

```yaml
arguments:
  - name: "player"
    type: online-player
    isRequired: true
    auto-completion: "@players"  # 自动补全在线玩家列表
  - name: "amount"
    type: integer
    isRequired: false
    auto-completion:
      - "1"
      - "10"
      - "64"
```

## 示例

### 简单商店命令

```yaml
commands:
  shop:
    command: /shop
    inventory: shop
    permission: "server.shop"
    deny-message: "&c您需要成为会员才能访问商店！"
    aliases:
      - store
      - market
```

### 传送点命令

```yaml
commands:
  warps:
    command: /warps
    inventory: warps_menu
    permission: "server.warps"
    deny-message: "&c您没有使用传送点的权限！"
    aliases:
      - warp
      - teleport
    actions:
      - type: message
        messages:
          - "&7正在打开传送点菜单..."
```

### 管理员菜单

```yaml
commands:
  adminmenu:
    command: /adminmenu
    inventory: admin_panel
    permission: "server.admin.menu"
    aliases:
      - admin
      - apanel
```

### 带分类的菜单

```yaml
commands:
  menu:
    command: /menu
    inventory: main_menu
    aliases:
      - gui
      - m

  help:
    command: /help
    inventory: help_menu
    permission: "server.help"

  rules:
    command: /rules
    inventory: rules_menu
```

### 服务器信息

```yaml
commands:
  info:
    command: /info
    inventory: server_info
    aliases:
      - serverinfo
      - about
    actions:
      - type: sound
        sound: ENTITY_EXPERIENCE_ORB_PICKUP
```

## 多文件命令配置

您可以将命令组织在多个文件中：

```
plugins/zMenu/commands/
├── commands.yml
├── shop/
│   └── shop_commands.yml
├── admin/
│   └── admin_commands.yml
└── social/
    └── social_commands.yml
```

每个文件遵循相同格式：

```yaml
# commands/shop/shop_commands.yml
commands:
  shop:
    command: /shop
    inventory: shop_main

  buyweapons:
    command: /buyweapons
    inventory: shop_weapons
```

## 命令参数进阶用法

### 基础参数

```yaml
commands:
  give-menu:
    command: /givemenu
    inventory: give_menu
    permission: "server.admin"
    arguments:
      - name: "player"
        type: online-player
        isRequired: true
        auto-completion: "@players"  # 使用在线玩家自动补全
```

### 多参数

```yaml
commands:
  category:
    command: /category
    inventory: category_menu
    arguments:
      - name: "type"
        isRequired: true
        auto-completion:
          - "weapons"
          - "armor"
          - "tools"
          - "food"
      - name: "page"
        type: integer
        isRequired: false
        auto-completion:
          - "1"
          - "2"
          - "3"
```

### 带 `performMainAction` 的参数

使用 `performMainAction: false` 可创建仅处理参数而不打开主界面的命令。

```yaml
commands:
  pay:
    command: /pay
    inventory: payment_confirm
    arguments:
      - name: "player"
        type: online-player
        isRequired: true
        auto-completion: "@players"
      - name: "amount"
        type: integer
        isRequired: true
        performMainAction: true  # 验证通过后打开确认界面
        actions:
          - type: message
            messages:
              - "&a正在准备向 %args_0% 支付 %args_1%..."
```

### 带专属操作的参数

每个参数可拥有独立的操作，在提供该参数时执行。

```yaml
commands:
  teleport:
    command: /tp
    permission: "server.teleport"
    arguments:
      - name: "player"
        type: online-player
        isRequired: true
        performMainAction: false  # 不打开界面
        actions:
          - type: console_command
            commands:
              - "tp %player% %args_0%"
          - type: message
            messages:
              - "&a已传送到 %args_0%！"
```

## 命令冲突处理

如果您的命令与其他插件冲突：

- 将命令名称更改为唯一名称
- 使用别名作为主要入口
- 检查服务器配置中的插件加载顺序

:::tip 提示
如果 `/shop` 已被其他插件占用，可使用唯一命令如 `/zmshop`，并将 `/shop` 设为别名。zMenu 会尝试注册该别名。
:::

## 权限示例

### 配合 LuckPerms 使用

```bash
# 为默认组授予商店权限
/lp group default permission set server.shop true

# 仅限管理员组访问管理菜单
/lp group admin permission set server.admin.menu true
```

### 拒绝消息

权限不足的玩家将看到默认的“无权限”消息。您可在消息配置文件中自定义该提示。

## 重载命令

修改命令文件后：

```bash
/zm reload command           # 重载所有命令
/zm reload command shop      # 重载特定命令
```

## 完整示例

```yaml
# commands/commands.yml
commands:
  # 主服务器菜单
  menu:
    command: /menu
    inventory: main_menu
    aliases:
      - gui
      - server
    actions:
      - type: sound
        sound: BLOCK_CHEST_OPEN

  # 带权限的商店
  shop:
    command: /shop
    inventory: shop_main
    permission: "server.shop"
    aliases:
      - store
      - market
      - buy

  # VIP 商店
  vipshop:
    command: /vipshop
    inventory: vip_shop
    permission: "server.vip.shop"
    aliases:
      - vs

  # 传送点菜单
  warps:
    command: /warps
    inventory: warps_menu
    permission: "server.warps"
    aliases:
      - warp
      - w

  # 玩家资料
  profile:
    command: /profile
    inventory: player_profile
    aliases:
      - stats
      - me

  # 帮助菜单
  help:
    command: /serverhelp
    inventory: help_menu
    aliases:
      - faq
      - info

  # 管理面板
  admin:
    command: /adminpanel
    inventory: admin_menu
    permission: "server.admin"
    aliases:
      - ap
      - adminmenu
```

## 最佳实践

- **使用直观名称**：如 `/shop` 而非 `/spm`
- **添加实用别名**：包含常用变体和快捷方式
- **设置适当权限**：限制敏感菜单的访问
- **保持结构清晰**：大型配置使用子文件夹组织
- **编写文档注释**：为 YAML 文件添加注释说明
- **充分测试**：验证命令按预期工作

## 后续步骤

- 配置主配置文件 [config.yml](./config-yml)
- 了解 [玩家数据](./player-data) 功能
- 设置 [全局占位符](./global-placeholders)