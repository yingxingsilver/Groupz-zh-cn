---
sidebar_position: 2
title: 创建库存
description: 引导您从零开始创建第一个 zMenu 库存。
---

# 创建库存

本指南将逐步引导您从零开始创建第一个 zMenu 库存。

## 步骤 1：创建文件

在 `plugins/zMenu/inventories/` 文件夹中创建一个新文件。为其命名具有描述性的名称，例如 `my_first_menu.yml`。

```
plugins/zMenu/inventories/my_first_menu.yml
```

:::tip 命名规范
文件名请使用小写字母、数字和下划线。避免使用空格和特殊字符。
:::

## 步骤 2：定义基础属性

从库存的必要属性开始：

```yaml
# 显示在库存顶部的标题
name: "&6&l我的第一个菜单"

# 库存大小（必须为 9、18、27、36、45 或 54）
size: 27

# 此库存是否启用
enable: true
```

## 步骤 3：添加填充物品（可选）

使用装饰性物品填充空槽位：

```yaml
name: "&6&l我的第一个菜单"
size: 27
enable: true

fill-item:
  material: GRAY_STAINED_GLASS_PANE
  name: "&8"
```

这将通过填充空白区域使您的库存外观更加整洁。

## 步骤 4：添加第一个按钮

现在让我们添加一个可交互的按钮。按钮在 `items` 区段中定义：

```yaml
name: "&6&l我的第一个菜单"
size: 27
enable: true

fill-item:
  material: GRAY_STAINED_GLASS_PANE
  name: "&8"

items:
  welcome-button:
    slot: 13
    item:
      material: DIAMOND
      name: "&b&l欢迎！"
      lore:
        - "&7点击我查看消息"
    actions:
      - type: message
        messages:
          - "&a你好，%player%！"
          - "&7感谢使用 zMenu！"
```

各部分功能说明：

| 属性 | 描述 |
|------|------|
| `welcome-button` | 此按钮的唯一名称 |
| `slot: 13` | 库存中的位置（第 2 行中央） |
| `item` | 按钮的视觉外观 |
| `material` | Minecraft 物品类型 |
| `name` | 物品显示名称 |
| `lore` | 名称下方的描述文本 |
| `actions` | 点击时执行的操作 |

## 步骤 5：测试您的库存

1. 保存文件
2. 执行 `/zm reload inventory my_first_menu`
3. 使用 `/zm open my_first_menu` 打开库存

您应该会看到一个包含中央钻石的 27 槽位库存！

## 步骤 6：添加更多按钮

让我们扩展菜单功能：

```yaml
name: "&6&l我的第一个菜单"
size: 27
enable: true

fill-item:
  material: GRAY_STAINED_GLASS_PANE
  name: "&8"

items:
  # 标题物品（不可交互）
  title:
    slot: 4
    item:
      material: BOOK
      name: "&6&l服务器指南"
      lore:
        - "&7欢迎来到我们的服务器！"
        - ""
        - "&7请在下方选择一个选项。"

  # 传送到出生点
  spawn:
    slot: 11
    item:
      material: RED_BED
      name: "&c&l出生点"
      lore:
        - "&7传送到出生点"
        - ""
        - "&e点击进行传送！"
    actions:
      - type: close
      - type: player-command
        commands:
          - "spawn"

  # 查看玩家信息
  info:
    slot: 13
    item:
      material: PLAYER_HEAD
      player-head: "%player%"
      name: "&a&l%player%"
      lore:
        - "&8&m─────────────"
        - ""
        - "&7生命值: &c%player_health%"
        - "&7等级: &a%player_level%"
        - ""
        - "&8&m─────────────"

  # 打开商店
  shop:
    slot: 15
    item:
      material: GOLD_INGOT
      name: "&e&l商店"
      lore:
        - "&7浏览我们的商店"
        - ""
        - "&e点击打开！"
    actions:
      - type: inventory
        inventory: "shop"

  # 关闭按钮
  close:
    slot: 22
    item:
      material: BARRIER
      name: "&c&l关闭"
      lore:
        - "&7关闭此菜单"
    actions:
      - type: close
```

## 步骤 7：添加音效

通过音效增强菜单交互性：

```yaml
items:
  spawn:
    slot: 11
    item:
      material: RED_BED
      name: "&c&l出生点"
    sound: ENTITY_ENDERMAN_TELEPORT
    actions:
      - type: close
      - type: player-command
        commands:
          - "spawn"
```

或在动作中添加音效：

```yaml
actions:
  - type: sound
    sound: UI_BUTTON_CLICK
    pitch: 1.2
    volume: 0.5
  - type: player-command
    commands:
      - "spawn"
```

## 步骤 8：添加条件限制（可选）

限制谁可以点击按钮：

```yaml
items:
  vip-area:
    slot: 16
    item:
      material: DIAMOND_BLOCK
      name: "&b&lVIP 区域"
      lore:
        - "&7专属 VIP 传送"
    click-requirement:
      requirements:
        - type: permission
          permission: "server.vip"
          deny:
            - type: message
              messages:
                - "&c你需要 VIP 等级才能使用此功能！"
            - type: sound
              sound: ENTITY_VILLAGER_NO
      success:
        - type: player-command
          commands:
            - "warp vip"
```

## 理解槽位编号

槽位从 0 到（大小 - 1）编号，从左上角开始：

```
第 1 行： 0  1  2  3  4  5  6  7  8
第 2 行： 9 10 11 12 13 14 15 16 17
第 3 行：18 19 20 21 22 23 24 25 26
第 4 行：27 28 29 30 31 32 33 34 35
第 5 行：36 37 38 39 40 41 42 43 44
第 6 行：45 46 47 48 49 50 51 52 53
```

对于 27 槽位库存（3 行），可用槽位为 0-26。

## 使用多个槽位

一个按钮可占据多个槽位：

```yaml
items:
  big-button:
    slots:
      - 11
      - 12
      - 13
      - 20
      - 21
      - 22
    item:
      material: EMERALD_BLOCK
      name: "&a&l大按钮"
```

或使用范围表示法：

```yaml
items:
  row-button:
    slots:
      - 10-16  # 槽位 10 至 16
    item:
      material: GOLD_BLOCK
      name: "&e&l金条"
```

## 完整示例

以下是一个完整可用的菜单：

```yaml
name: "&6&l服务器菜单"
size: 45
enable: true

fill-item:
  material: BLACK_STAINED_GLASS_PANE
  name: "&8"

open-actions:
  - type: sound
    sound: BLOCK_CHEST_OPEN
    pitch: 1.0

items:
  # 装饰性顶部边框
  top-border:
    slots:
      - 0-8
    item:
      material: BLUE_STAINED_GLASS_PANE
      name: "&8"

  # 标题
  title:
    slot: 4
    item:
      material: NETHER_STAR
      name: "&6&l✦ 服务器菜单 ✦"
      lore:
        - ""
        - "&7欢迎，&f%player%"
        - ""
        - "&7请在下方选择一个选项"
      glow: true

  # 出生点传送
  spawn:
    slot: 19
    item:
      material: RED_BED
      name: "&c&l出生点"
      lore:
        - "&7返回出生点"
        - ""
        - "&e▸ 点击进行传送"
    sound: UI_BUTTON_CLICK
    actions:
      - type: close
      - type: player-command
        commands:
          - "spawn"
      - type: message
        messages:
          - "&a正在传送到出生点..."

  # 传送点菜单
  warps:
    slot: 21
    item:
      material: ENDER_PEARL
      name: "&5&l传送点"
      lore:
        - "&7浏览传送位置"
        - ""
        - "&e▸ 点击打开"
    sound: UI_BUTTON_CLICK
    actions:
      - type: inventory
        inventory: "warps"

  # 玩家信息
  profile:
    slot: 23
    item:
      material: PLAYER_HEAD
      player-head: "%player%"
      name: "&a&l你的档案"
      lore:
        - "&8&m────────────────"
        - ""
        - "&7名称: &f%player%"
        - "&7余额: &6$%vault_eco_balance%"
        - "&7游戏时间: &e%zmenu_statistic_time_played%"
        - ""
        - "&8&m────────────────"

  # 设置
  settings:
    slot: 25
    item:
      material: COMPARATOR
      name: "&e&l设置"
      lore:
        - "&7配置偏好设置"
        - ""
        - "&e▸ 点击打开"
    sound: UI_BUTTON_CLICK
    actions:
      - type: inventory
        inventory: "settings"

  # 底部边框
  bottom-border:
    slots:
      - 36-44
    item:
      material: BLUE_STAINED_GLASS_PANE
      name: "&8"

  # 关闭按钮
  close:
    slot: 40
    item:
      material: BARRIER
      name: "&c&l关闭菜单"
      lore:
        - "&7关闭此菜单"
    sound: UI_BUTTON_CLICK
    actions:
      - type: close
```

## 优秀菜单设计技巧

- **保持风格一致** - 在所有菜单中保持颜色和格式相似
- **添加视觉边框** - 使用玻璃板或其他物品框住内容
- **提供反馈** - 使用音效和消息确认操作
- **分组相关物品** - 将相似选项放置在相邻位置
- **包含导航元素** - 始终提供返回或关闭的方式
- **使用清晰图标** - 选择能直观代表功能的材质
- **保持 lore 简洁** - 避免在按钮上堆砌过多文本

## 后续步骤

现在您已掌握创建基础库存的方法：

- 了解所有 [按钮选项](../buttons/button)
- 探索不同 [按钮类型](../buttons/types/none)
- 为按钮添加复杂的 [动作](../buttons/actions)
- 创建可复用元素的 [模式](../patterns)