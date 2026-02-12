---
sidebar_position: 6
title: 首页
description: 返回导航历史中首个库存界面的按钮
---

# 首页按钮


## HOME 按钮类型

`HOME` 按钮类型可将玩家直接返回至其导航历史中的**第一个**库存界面。此功能适用于深层菜单结构，方便玩家快速返回起始位置。

## 用法

```yaml
items:
  home:
    type: HOME
    slot: 49
    item:
      material: NETHER_STAR
      name: "&e&l首页"
      lore:
        - "&7返回主菜单"
```

## 工作原理

与 [BACK](./back) 按钮（仅后退一步）不同，`HOME` 按钮会**清空整个导航历史**并直接返回首个库存界面：

1. 玩家打开菜单 A（根菜单）
2. 玩家导航路径：A → B → C → D
3. 玩家点击 HOME → 直接返回菜单 A
4. 导航历史被清空

## 与其他按钮的区别

| 按钮类型 | 行为 |
|----------|------|
| BACK | 返回上一个库存界面（后退一步） |
| HOME | 返回导航历史中的第一个库存界面 |
| MAIN_MENU | 打开预设的主菜单（可能与根菜单不同） |

## 示例

### 基础 HOME 按钮
```yaml
items:
  home:
    type: HOME
    slot: 49
    item:
      material: NETHER_STAR
      name: "&e&l首页"
    sound: ENTITY_ENDERMAN_TELEPORT
```

### 美化版 HOME 按钮
```yaml
items:
  home:
    type: HOME
    slot: 49
    item:
      material: PLAYER_HEAD
      url: "eyJ0ZXh0dXJlcyI6eyJTS0lOIjp7InVybCI6Imh0dHA6Ly90ZXh0dXJlcy5taW5lY3JhZnQubmV0L3RleHR1cmUvYzVhMzFjNjQ1MzNlNTRhZmFjZjA0YzNhMTY3YzM4YjBhYjIzMjI3NjdmYjE2MWVmMTU5MjI5YTI4ZmJiN2EifX0="
      name: "&6&l⌂ 首页"
      lore:
        - "&7返回主菜单"
        - ""
        - "&e▸ 点击返回首页"
    sound: BLOCK_NOTE_BLOCK_CHIME
```

### 含 HOME 按钮的导航栏
```yaml
items:
  back:
    type: BACK
    slot: 45
    is-permanent: true
    item:
      material: ARROW
      name: " &c &l← 返回 "
    sound: UI_BUTTON_CLICK

  previous:
    type: PREVIOUS
    slot: 47
    is-permanent: true
    item:
      material: ARROW
      name: " &7上一页 "

  home:
    type: HOME
    slot: 49
    is-permanent: true
    item:
      material: NETHER_STAR
      name: " &e &l首页 "
    sound: UI_BUTTON_CLICK

  next:
    type: NEXT
    slot: 51
    is-permanent: true
    item:
      material: ARROW
      name: " &7下一页 "

  close:
    slot: 53
    is-permanent: true
    close-inventory: true
    item:
      material: BARRIER
      name: " &c &l关闭 "
```

### 条件显示的 HOME 按钮
仅在非根菜单时显示：
```yaml
items:
  home:
    type: HOME
    slot: 49
    view-requirement:
      requirements:
        - type: placeholder
          value: "%zmenu_player_previous_inventories%"
          compare: ">"
          number: 0
    item:
      material: NETHER_STAR
      name: "&e&l首页"
    else:
      item:
        material: GRAY_STAINED_GLASS_PANE
        name: "&8"
```

## 适用场景

建议在以下情况使用 `HOME` 按钮：
- 菜单层级较深（3 层及以上）
- 用户需要快速重置导航路径
- 菜单结构存在多分支分类

## 最佳实践

- **居中放置**：置于导航栏中央位置（槽位 49）
- **醒目图标**：使用下界之星或房屋造型的玩家头颅
- **全局覆盖**：在所有非根菜单中均添加此按钮
- **组合使用**：与 BACK 按钮配合，提供多级导航选项
- **永久显示**：在分页菜单中设置 `is-permanent: true` 确保始终可见

## 相关按钮类型

- [BACK](./back) - 后退一步
- [MAIN_MENU](./mainmenu) - 打开预设主菜单
- [INVENTORY](./inventory) - 打开指定库存界面

## 后续学习

- 了解 [MAIN_MENU](./mainmenu) 按钮
- 查看 [JUMP](./jump) 按钮实现页面跳转
- 浏览全部 [按钮类型](./none)