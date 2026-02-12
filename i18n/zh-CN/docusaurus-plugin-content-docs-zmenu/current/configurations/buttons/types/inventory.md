---
sidebar_position: 2
title: 菜单界面
description: 点击后打开另一个菜单界面的按钮
---

# 菜单界面按钮（INVENTORY）


`INVENTORY` 按钮类型可在玩家点击时打开另一个菜单界面。

### 基本用法

```yaml
items:
  open-shop:
    type: INVENTORY
    slot: 13
    inventory: "shop"
    item:
      material: GOLD_INGOT
      name: "&e&l商店"
      lore:
        - "&7点击打开商店"
```

### 属性说明

#### inventory（必需）
要打开的目标菜单名称。

```yaml
items:
  open-menu:
    type: INVENTORY
    slot: 0
    inventory: "my_other_menu"
```

> 菜单名称对应 `inventories/` 文件夹中 `.yml` 文件的文件名（不含扩展名）。

#### plugin
指定要打开哪个插件的菜单界面（用于跨插件支持）。

```yaml
items:
  open-external:
    type: INVENTORY
    slot: 0
    inventory: "external_menu"
    plugin: "OtherPlugin"
```

#### arguments
向目标菜单传递参数。

```yaml
items:
  open_with_args:
    type: INVENTORY
    slot: 0
    inventory: "category_menu"
    arguments:
      - "swords"
      - "%player%"
```

#### page
在指定页码打开分页菜单。

```yaml
items:
  open_page_3:
    type: INVENTORY
    slot: 0
    inventory: "paginated_menu"
    page: 3
```

### 示例

#### 基础菜单导航

```yaml
items:
  shop:
    type: INVENTORY
    slot: 11
    inventory: "shop"
    item:
      material: GOLD_INGOT
      name: "&e&l商店"
      lore:
        - "&7购买与出售物品"
        - ""
        - "&e▸ 点击打开"
    sound: UI_BUTTON_CLICK

  warps:
    type: INVENTORY
    slot: 13
    inventory: "warps"
    item:
      material: ENDER_PEARL
      name: "&5&l传送点"
      lore:
        - "&7在世界中快速传送"
        - ""
        - "&e▸ 点击打开"
    sound: UI_BUTTON_CLICK

  settings:
    type: INVENTORY
    slot: 15
    inventory: "settings"
    item:
      material: COMPARATOR
      name: "&c&l设置"
      lore:
        - "&7配置您的个人偏好"
        - ""
        - "&e▸ 点击打开"
    sound: UI_BUTTON_CLICK
```

#### 分类选择菜单

```yaml
# 主菜单配置
items:
  swords:
    type: INVENTORY
    slot: 10
    inventory: "shop_swords"
    item:
      material: DIAMOND_SWORD
      name: "&b&l剑类"

  armor:
    type: INVENTORY
    slot: 12
    inventory: "shop_armor"
    item:
      material: DIAMOND_CHESTPLATE
      name: "&9&l护甲"

  tools:
    type: INVENTORY
    slot: 14
    inventory: "shop_tools"
    item:
      material: DIAMOND_PICKAXE
      name: "&a&l工具"

  food:
    type: INVENTORY
    slot: 16
    inventory: "shop_food"
    item:
      material: GOLDEN_APPLE
      name: "&6&l食物"
```

#### 带权限限制的菜单

```yaml
items:
  vip-shop:
    type: INVENTORY
    slot: 22
    inventory: "vip_shop"
    item:
      material: DIAMOND_BLOCK
      name: "&b&lVIP 商店"
      lore:
        - "&7专属 VIP 物品"
    view-requirement:
      requirements:
        - type: permission
          permission: "server.vip"
    else:
      item:
        material: COAL_BLOCK
        name: "&8&lVIP 商店"
        lore:
          - "&c需要 VIP 权限"
```

#### 指定页码打开

```yaml
items:
  page_1:
    type: INVENTORY
    slot: 10
    inventory: "items_catalog"
    page: 1
    item:
      material: PAPER
      name: "&7第 1 页"

  page_2:
    type: INVENTORY
    slot: 11
    inventory: "items_catalog"
    page: 2
    item:
      material: PAPER
      name: "&7第 2 页"

  page_3:
    type: INVENTORY
    slot: 12
    inventory: "items_catalog"
    page: 3
    item:
      material: PAPER
      name: "&7第 3 页"
```

### 替代方案：使用 Actions

您也可以通过 `NONE` 按钮配合 `inventory` 动作实现相同功能：

```yaml
items:
  open-shop:
    slot: 13
    item:
      material: GOLD_INGOT
      name: "&e&l商店"
    actions:
      - type: inventory
        inventory: "shop"
```

> `INVENTORY` 按钮类型本质上是此常用模式的简写形式。

### 导航历史

使用 `INVENTORY` 按钮时，zMenu 会自动维护导航历史记录，使 [BACK](./back) 按钮能够返回上一级菜单。

> 历史记录由系统自动管理，无需额外配置。

### 最佳实践

- 为菜单文件使用清晰的命名，便于识别和维护
- 为菜单打开操作添加音效反馈，提升用户体验
- 使用权限/条件限制控制特定菜单的访问权限
- 为受限菜单提供锁定状态的视觉提示（通过 `else` 配置）
- 对于复杂菜单结构，建议使用文件夹进行组织管理

### 后续学习

- 了解 [BACK](./back) 按钮实现菜单返回功能
- 创建支持分页的 [多页菜单](./next)
- 查看完整的 [按钮属性列表](../button)