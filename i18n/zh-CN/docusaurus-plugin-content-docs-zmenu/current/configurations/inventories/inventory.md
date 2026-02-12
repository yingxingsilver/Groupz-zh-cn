---
sidebar_position: 1
title: 库存配置
description: 本页面详述了在 zMenu 中创建库存时所有可用的配置选项。
---

# 库存配置

本页面详述了在 zMenu 中创建库存时所有可用的配置选项。

## 文件位置

库存文件存储在 `plugins/zMenu/inventories/` 文件夹中。每个 YAML 文件代表一个库存。您可以在子文件夹中组织库存。

```
plugins/zMenu/inventories/
├── main_menu.yml
├── shop.yml
├── warps/
│   ├── spawn.yml
│   └── hub.yml
└── admin/
    └── admin_menu.yml
```

## 基础结构

```yaml
# 显示在标题栏的库存名称
name: "&6我的库存"

# 库存大小（9、18、27、36、45 或 54）
size: 54

# 启用或禁用此库存
enable: true

# 按钮定义
items:
  button-name:
    # 按钮配置...
```

## 配置选项

### name

显示在库存顶部的标题。

```yaml
name: "&6&l我的服务器菜单"
```

**特性：**
- 支持颜色代码（`&6`、`&#FF5500`）
- 支持 MiniMessage（如已启用）
- 支持占位符（`%player%`、`%page%`）

**分页占位符示例：**
```yaml
name: "&6商店 &7(第 %page%/%max-page% 页)"
```

### size

库存中的槽位数量。必须是 9 的倍数（适用于 CHEST 类型）。

| 大小 | 行数 | 描述 |
|------|------|------|
| 9 | 1 | 单行 |
| 18 | 2 | 两行 |
| 27 | 3 | 三行（小箱子） |
| 36 | 4 | 四行 |
| 45 | 5 | 五行 |
| 54 | 6 | 六行（大箱子） |

```yaml
size: 54
```

### type

库存类型。定义库存的视觉布局。

```yaml
type: CHEST
```

**可用类型：**

| 类型 | 大小 | 描述 |
|------|------|------|
| `CHEST` | 9-54 | 标准箱子库存（默认） |
| `DISPENSER` | 9 | 3×3 投掷器布局 |
| `DROPPER` | 9 | 3×3 滴漏器布局 |
| `FURNACE` | 3 | 熔炉布局 |
| `WORKBENCH` | 10 | 工作台布局 |
| `ENCHANTING` | 2 | 附魔台布局 |
| `BREWING` | 5 | 酿造台布局 |
| `ANVIL` | 3 | 铁砧布局 |
| `BEACON` | 1 | 信标布局 |
| `HOPPER` | 5 | 漏斗布局（5 槽位） |
| `SHULKER_BOX` | 27 | 潜影盒布局 |
| `BARREL` | 27 | 木桶布局 |
| `BLAST_FURNACE` | 3 | 高炉布局 |
| `LECTERN` | 1 | 讲台布局 |
| `SMOKER` | 3 | 熏炉布局 |
| `LOOM` | 4 | 织布机布局 |
| `CARTOGRAPHY` | 3 | 制图台布局 |
| `GRINDSTONE` | 3 | 砂轮布局 |
| `STONECUTTER` | 2 | 切石机布局 |
| `SMITHING` | 4 | 锻造台布局 |

:::info
当使用 `CHEST` 以外的类型时，`size` 选项将被忽略，因为每种类型都有固定大小。
:::

**漏斗示例：**
```yaml
name: "&8快速选择"
type: HOPPER

items:
  option1:
    slot: 0
    item:
      material: DIAMOND
      name: "&b选项 1"
```

### enable

启用或禁用此库存。禁用的库存无法被打开。

```yaml
enable: true
```

此选项适用于临时禁用库存而无需删除文件。

### update-interval

以固定间隔（毫秒）自动刷新库存。

```yaml
update-interval: 1000  # 每秒刷新一次（1000 毫秒）
```

:::info
1000ms = 1 秒。适用于包含需要频繁更新的动态占位符的库存。
:::

### title-animation

使用多个帧为库存标题添加动画效果。这将创建一个循环显示不同文本的动态标题。

:::warning 要求
此功能需要在服务器上安装 [PacketEvents](https://github.com/retrooper/packetevents)。没有 PacketEvents，标题动画将无法工作。
:::

```yaml
title-animation:
  titles:
    - "&b欢迎来到 &a菜单！"
    - "&e享受我们的 &9特色功能！"
    - "&c旋转的 &6动画 &2标题！"
  cycles: 5
  initial-delay: 1
  interval: 2
  time-unit: SECONDS
  show-items-after-animation: true
  item-update-interval: 1
```

**配置选项：**

| 选项 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| `titles` | 列表 | - | 要循环显示的标题帧列表 |
| `cycles` | 整数 | -1 | 动画循环次数（-1 表示无限循环） |
| `initial-delay` | 整数 | 0 | 动画开始前的延迟 |
| `interval` | 整数 | 1 | 每帧之间的间隔时间 |
| `time-unit` | 字符串 | `SECONDS` | 延迟的时间单位（`SECONDS`、`MILLISECONDS`、`TICKS`） |
| `show-items-after-animation` | 布尔值 | `true` | 动画完成后是否显示物品 |
| `item-update-interval` | 整数 | 1 | 动画期间更新物品的频率 |

**时间单位：**
`NANOSECONDS`、`MICROSECONDS`、`MILLISECONDS`、`SECONDS`、`MINUTES`、`HOURS`、`DAYS`

**完整示例：**
```yaml
name: "&6加载中..."
size: 27

title-animation:
  titles:
    - "&6&l⏳ 加载中. "
    - "&6&l⏳ 加载中.. "
    - "&6&l⏳ 加载中... "
    - "&a&l✓ 欢迎！"
  cycles: 1
  initial-delay: 0
  interval: 500
  time-unit: MILLISECONDS
  show-items-after-animation: true

items:
  content:
    slot: 13
    item:
      material: NETHER_STAR
      name: "&e菜单内容"
```

### clear-inventory

在玩家打开菜单时清空其库存，并在关闭时恢复。

```yaml
clear-inventory: true
```

**使用场景：**
- 用于覆盖整个屏幕的自定义材质包
- 防止在菜单中与物品交互

### cancel-item-pickup

防止玩家在库存打开时拾取物品。

```yaml
cancel-item-pickup: true
```

**使用场景：**
- 防止菜单交互期间意外拾取物品
- 提供不受干扰的干净 GUI 体验

### target-player-name-placeholder

为被查看玩家的名称定义自定义占位符。在为其他玩家打开库存时非常有用。

```yaml
target-player-name-placeholder: "%target%"
```

**默认值：** `%player_name%`

**示例用法：**
```yaml
name: "&6%target% 的档案"
target-player-name-placeholder: "%target%"

items:
  info:
    slot: 13
    item:
      material: PLAYER_HEAD
      name: "&e%target%"
      lore:
        - "&7正在查看玩家档案"
```

### action-patterns

将动作模式应用于此库存中的所有按钮。动作模式是在 `plugins/zMenu/action_patterns/` 文件夹中定义的可复用动作集合。

```yaml
action-patterns:
  - "default-actions"
  - "click-sound"
```

**动作模式文件示例**（`plugins/zMenu/action_patterns/default-actions.yml`）：
```yaml
name: default-actions
actions:
  - type: sound
    sound: ENTITY_VILLAGER_YES
deny-actions:
  - type: sound
    sound: ENTITY_VILLAGER_NO
```

应用后，库存中的所有按钮将继承这些动作。按钮特定动作将在模式动作之后执行。

### fill-item

定义用于填充所有空槽位的物品。

```yaml
fill-item:
  material: BLACK_STAINED_GLASS_PANE
  name: "&8"
```

这将自动填充所有未被按钮占用的槽位。

### patterns

将可复用模式应用于此库存。

```yaml
patterns:
  - "navigation_bar"
  - "border_pattern"
```

模式必须在 `plugins/zMenu/patterns/` 文件夹中定义。详情请参阅 [模式](../patterns)。

### matrix

使用字符直观地定义库存布局。

```yaml
matrix:
  - "AAAAAAAAA"
  - "A       A"
  - "A   B   A"
  - "A       A"
  - "AAAAAAAAA"

items:
  A:
    item:
      material: BLACK_STAINED_GLASS_PANE
      name: "&8"
  B:
    item:
      material: DIAMOND
      name: "&b中心物品"
```

每个字符代表一个物品。空格表示空槽位。

### items

库存中的按钮。此处定义所有可点击元素。

```yaml
items:
  my-button:
    slot: 0
    item:
      material: DIAMOND
      name: "&b钻石"
    actions:
      - type: message
        messages:
          - "&a你点击了钻石！"

  another-button:
    slot: 8
    item:
      material: EMERALD
      name: "&a绿宝石"
```

完整按钮文档请参阅 [按钮配置](../buttons/button)。

### view-requirement

打开此库存前必须满足的条件。

```yaml
view-requirement:
  requirements:
    - type: permission
      permission: "myserver.vip"
      deny:
        - type: message
          messages:
            - "&c你需要 VIP 权限才能访问此菜单！"
```

如果条件不满足，库存将无法打开，且会执行拒绝动作。

### open-requirement

`view-requirement` 的替代名称（功能相同）。

```yaml
open-requirement:
  requirements:
    - type: permission
      permission: "myserver.premium"
```

### open-with-item

定义一个物品，当玩家手持并点击时可打开此库存。

```yaml
open-with-item:
  item:
    material: COMPASS
    name: "&6&l菜单"
    lore:
      - "&7右键点击打开"
  actions:
    - RIGHT_CLICK
    - RIGHT_CLICK_BLOCK
  type: full  # full、contains 或 starts_with
```

**动作类型：**
- `RIGHT_CLICK` - 在空中右键点击
- `LEFT_CLICK` - 在空中左键点击
- `RIGHT_CLICK_BLOCK` - 右键点击方块
- `LEFT_CLICK_BLOCK` - 左键点击方块

**类型选项：**
- `full` - 物品必须完全匹配
- `contains` - 物品名称必须包含指定名称
- `starts_with` - 物品名称必须以指定名称开头

**给予玩家该物品：**
```
/zm giveopenitem <库存名> <玩家>
```

### open-actions

库存打开时执行的动作。

```yaml
open-actions:
  - type: sound
    sound: ENTITY_EXPERIENCE_ORB_PICKUP
  - type: message
    messages:
      - "&a欢迎来到菜单！"
```

### close-actions

库存关闭时执行的动作。

```yaml
close-actions:
  - type: message
    messages:
      - "&7感谢您的访问！"
```

### local-placeholders

定义库存专属的占位符。

```yaml
local-placeholders:
  server-ip: "play.myserver.com"
  discord: "discord.gg/myserver"

items:
  info:
    item:
      lore:
        - "&7IP: &f%server-ip%"
        - "&7Discord: &f%discord%"
```

## 完整示例

```yaml
name: "&6&l服务器菜单 &7(&f%page%&7/&f%maxPage%&7)"
size: 54
enable: true
update-interval: 2000  # 2 秒

fill-item:
  material: GRAY_STAINED_GLASS_PANE
  name: "&8"

patterns:
  - "navigation"

view-requirement:
  requirements:
    - type: permission
      permission: "server.menu.use"
      deny:
        - type: message
          messages:
            - "&c你没有使用此菜单的权限！"

open-actions:
  - type: sound
    sound: BLOCK_CHEST_OPEN
    pitch: 1.2

close-actions:
  - type: sound
    sound: BLOCK_CHEST_CLOSE

local-placeholders:
  menu-version: "1.0"

items:
  title:
    slot: 4
    item:
      material: NETHER_STAR
      name: "&6&l服务器菜单"
      lore:
        - "&8版本 %menu-version%"
        - ""
        - "&7欢迎，&f%player%&7！"
        - ""
        - "&7请在下方选择一个选项。"

  teleports:
    slot: 20
    item:
      material: ENDER_PEARL
      name: "&b&l传送"
      lore:
        - "&7访问传送选项"
    actions:
      - type: inventory
        inventory: "teleports_menu"

  shop:
    slot: 22
    item:
      material: GOLD_INGOT
      name: "&e&l商店"
      lore:
        - "&7买卖物品"
    actions:
      - type: inventory
        inventory: "shop_menu"

  settings:
    slot: 24
    item:
      material: COMPARATOR
      name: "&c&l设置"
      lore:
        - "&7配置你的偏好"
    actions:
      - type: inventory
        inventory: "settings_menu"

  close:
    slot: 49
    item:
      material: BARRIER
      name: "&c&l关闭"
      lore:
        - "&7关闭此菜单"
    actions:
      - type: close
```

## 多页库存

当按钮定义了多个槽位或页面时，zMenu 会自动创建分页：

```yaml
items:
  shop-item:
    slots:
      - 10-16
      - 19-25
      - 28-34
    item:
      # 如果物品数量超过槽位，将自动创建多页
```

使用 `NEXT` 和 `PREVIOUS` 按钮类型进行导航。详情请参阅 [按钮类型](../buttons/types/next)。

## 最佳实践

- 按类别在子文件夹中组织库存
- 对重复元素（如边框）使用模式
- 对多次使用的值使用本地占位符
- 使用 `/zm reload inventory <名称>` 快速测试迭代
- 使用有意义的按钮名称以便于维护
- 保持适当的库存大小——如果只需 27 槽位，不要使用 54 槽位

## 后续步骤

- 逐步学习如何 [创建库存](./create-inventory)
- 为库存配置 [按钮](../buttons/button)
- 添加 [动作](../buttons/actions) 使按钮具有交互性