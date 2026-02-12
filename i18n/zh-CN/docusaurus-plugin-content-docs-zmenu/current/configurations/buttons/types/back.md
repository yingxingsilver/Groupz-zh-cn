---
sidebar_position: 3
title: 返回
description: 按钮类型用于将玩家返回至上一个打开的菜单界面。
---

# 返回按钮（BACK Button）

## 概述
`BACK` 按钮类型用于将玩家返回至上一个打开的菜单界面。zMenu 会维护已打开菜单的历史记录，从而实现流畅的导航体验。

## 基本用法
```yaml
items:
  back:
    type: BACK
    slot: 45
    item:
      material: ARROW
      name: "&c&l返回"
      lore:
        - "&7返回上一级菜单"
```

## 工作原理
1. 玩家打开菜单 A  
2. 玩家点击进入菜单 B（历史记录：[菜单 A]）  
3. 玩家点击进入菜单 C（历史记录：[菜单 A, 菜单 B]）  
4. 玩家点击 `BACK` 按钮 → 返回至菜单 B  
5. 玩家再次点击 `BACK` 按钮 → 返回至菜单 A  

> 若历史记录中不存在上一级菜单，点击 `BACK` 按钮将不执行任何操作。

## 支持的属性
`BACK` 按钮支持所有标准按钮属性：

| 属性 | 说明 |
|------|------|
| `slot` / `slots` | 在界面中的位置 |
| `item` | 按钮的视觉外观 |
| `sound` | 点击时播放的音效 |
| `is-permanent` | 是否在所有分页中显示 |
| `view-requirement` | 显示该按钮的条件 |

## 示例

### 基础返回按钮
```yaml
items:
  back:
    type: BACK
    slot: 45
    item:
      material: ARROW
      name: "&c&l返回"
    sound: UI_BUTTON_CLICK
```

### 美化版返回按钮
```yaml
items:
  back:
    type: BACK
    slot: 45
    item:
      material: PLAYER_HEAD
      url: "eyJ0ZXh0dXJlcyI6eyJTS0lOIjp7InVybCI6Imh0dHA6Ly90ZXh0dXJlcy5taW5lY3JhZnQubmV0L3RleHR1cmUvYmQ2OWUwNmU1ZGFkZmQ4NGU1ZjNkMWMyMTA2M2YyNTUzYjJmYTk0NWVlMWQ0ZDcxNTJmZGM1NDI1YmMxMmE5In19"
      name: "&c&l← 返回"
      lore:
        - "&7返回至上一级菜单"
    sound: UI_BUTTON_CLICK
```

### 永久显示的返回按钮
```yaml
items:
  back:
    type: BACK
    slot: 45
    is-permanent: true
    item:
      material: ARROW
      name: "&c&l返回"
```
> 在分页菜单中，设置 `is-permanent: true` 可使返回按钮在所有页面中显示。

### 导航栏组合（返回 + 首页 + 关闭）
```yaml
items:
  back:
    type: BACK
    slot: 45
    item:
      material: ARROW
      name: "&c&l返回"
    sound: UI_BUTTON_CLICK

  home:
    type: HOME
    slot: 49
    item:
      material: NETHER_STAR
      name: "&e&l主菜单"
    sound: UI_BUTTON_CLICK

  close:
    slot: 53
    close-inventory: true
    item:
      material: BARRIER
      name: "&c&l关闭"
    sound: UI_BUTTON_CLICK
```

### 条件显示的返回按钮
仅当存在上一级菜单时才显示返回按钮：
```yaml
items:
  back:
    type: BACK
    slot: 45
    view-requirement:
      requirements:
        - type: placeholder
          value: "%zmenu_player_previous_inventories%"
          compare: ">"
          number: 0
    item:
      material: ARROW
      name: "&c&l返回"
    else:
      item:
        material: GRAY_STAINED_GLASS_PANE
        name: "&8"
```

### 替代方案：使用动作（Actions）
也可通过普通按钮配合 `back` 动作实现相同功能：
```yaml
items:
  back:
    slot: 45
    item:
      material: ARROW
      name: "&c&l返回"
    actions:
      - type: back
```
> `BACK` 按钮类型本质上是上述配置的简写形式。

## 常见布局模式

### 底部导航栏
```yaml
items:
  nav-border-left:
    slots:
      - 45
      - 46
      - 47
    item:
      material: BLACK_STAINED_GLASS_PANE
      name: " &8 "

  back:
    type: BACK
    slot: 48
    item:
      material: ARROW
      name: " &c &l返回 "

  info:
    slot: 49
    item:
      material: BOOK
      name: " &e &l第 %page%/%max-page% 页 "

  home:
    type: HOME
    slot: 50
    item:
      material: NETHER_STAR
      name: " &6 &l首页 "

  nav-border-right:
    slots:
      - 51
      - 52
      - 53
    item:
      material: BLACK_STAINED_GLASS_PANE
      name: " &8 "
```

## 最佳实践
- **位置统一**：在所有菜单中将返回按钮放置于相同位置（通常为左下角）
- **图标直观**：使用箭头或朝左的玩家头像等易于识别的图标
- **音效反馈**：添加点击音效提升交互体验
- **搭配首页按钮**：在层级较深的菜单结构中，建议同时提供 `HOME` 按钮
- **分页菜单中设为永久**：在分页菜单中使用 `is-permanent: true` 确保每页都有返回功能

## 相关按钮类型
- [HOME](./home) - 返回历史记录中的首个菜单
- [MAIN_MENU](./mainmenu) - 打开预设的主菜单
- [INVENTORY](./inventory) - 打开指定菜单

## 下一步学习
- 了解 [HOME 按钮](./home) 的用法
- 查看 [PREVIOUS](./previous) 实现分页导航
- 浏览全部 [按钮类型](./none)