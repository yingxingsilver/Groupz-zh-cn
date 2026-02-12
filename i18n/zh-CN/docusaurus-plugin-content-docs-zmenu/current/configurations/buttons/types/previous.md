---
sidebar_position: 5
title: 上一页
description: 导航至上一页的按钮
---
# 上一页按钮（PREVIOUS 按钮）


## PREVIOUS 按钮类型

`PREVIOUS` 按钮类型用于在分页菜单中导航至上一页。

### 基本用法

```yaml
items:
  previous:
    type: PREVIOUS
    slot: 45
    item:
      material: ARROW
      name: "&c&l← 上一页"
      lore:
        - "&7跳转至第 %zmenu_player_previous_page% 页"
```

### 属性说明

| 属性 | 说明 |
|------|------|
| `slot` / `slots` | 菜单位置 |
| `item` | 视觉外观 |
| `sound` | 点击时播放的音效 |
| `is-permanent` | 是否在所有页面显示（推荐启用） |

### 示例

#### 基础上一页按钮

```yaml
items:
  previous:
    type: PREVIOUS
    slot: 45
    is-permanent: true
    item:
      material: ARROW
      name: "&c&l← 上一页"
    sound: UI_BUTTON_CLICK
```

#### 样式化上一页按钮

```yaml
items:
  previous:
    type: PREVIOUS
    slot: 45
    is-permanent: true
    item:
      material: PLAYER_HEAD
      url: "eyJ0ZXh0dXJlcyI6eyJTS0lOIjp7InVybCI6Imh0dHA6Ly90ZXh0dXJlcy5taW5lY3JhZnQubmV0L3RleHR1cmUvYmQ2OWUwNmU1ZGFkZmQ4NGU1ZjNkMWMyMTA2M2YyNTUzYjJmYTk0NWVlMWQ0ZDcxNTJmZGM1NDI1YmMxMmE5In19"
      name: "&c&l← 上一页"
      lore:
        - "&7当前页: &f%page%"
        - "&7跳转至: &f%zmenu_player_previous_page%"
    sound: ITEM_BOOK_PAGE_TURN
```

#### 首页时自动隐藏

```yaml
items:
  previous:
    type: PREVIOUS
    slot: 45
    is-permanent: true
    view-requirement:
      requirements:
        - type: placeholder
          value: "%zmenu_player_page%"
          compare: ">"
          number: 1
    item:
      material: ARROW
      name: "&c&l← 上一页"
    else:
      item:
        material: GRAY_STAINED_GLASS_PANE
        name: "&8"
```

#### 完整导航栏

```yaml
items:
  nav-left-border:
    slots:
      - 45
      - 46
    is-permanent: true
    item:
      material: BLACK_STAINED_GLASS_PANE
      name: "&8"

  previous:
    type: PREVIOUS
    slot: 47
    is-permanent: true
    item:
      material: ARROW
      name: " &c&l← 上一页 "
    sound: UI_BUTTON_CLICK

  back:
    type: BACK
    slot: 48
    is-permanent: true
    item:
      material: DARK_OAK_DOOR
      name: " &7&l返回 "
    sound: UI_BUTTON_CLICK

  page-info:
    slot: 49
    is-permanent: true
    item:
      material: BOOK
      name: " &6&l第 %page% 页 / 共 %maxPage% 页 "
      lore:
        - ""
        - " &7使用箭头进行翻页 "

  home:
    type: HOME
    slot: 50
    is-permanent: true
    item:
      material: NETHER_STAR
      name: " &e&l首页 "
    sound: UI_BUTTON_CLICK

  next:
    type: NEXT
    slot: 51
    is-permanent: true
    item:
      material: ARROW
      name: " &a&l下一页 → "
    sound: UI_BUTTON_CLICK

  nav-right-border:
    slots:
      - 52
      - 53
    is-permanent: true
    item:
      material: BLACK_STAINED_GLASS_PANE
      name: "&8"
```

### 最佳实践

- 导航按钮**务必**设置 `is-permanent: true`，确保在所有页面可见
- 与 [NEXT](./next) 按钮配对使用，提供完整的翻页体验
- 在导航按钮之间添加页码指示器，提升用户感知
- 所有分页菜单采用统一的导航位置布局
- 通过音效（如 `UI_BUTTON_CLICK`）提供操作反馈

### 相关按钮类型

- [NEXT](./next) — 导航至下一页
- [JUMP](./jump) — 跳转至指定页码
- [BACK](./back) — 返回上一级菜单

### 后续学习

- 了解 [NEXT](./next) 按钮类型
- 学习 [JUMP](./jump) 按钮的高级用法
- 查看完整的 [按钮类型列表](./none)