---
sidebar_position: 4
title: 下一页
description: 在分页菜单中导航至下一页
---

# 下一页按钮（NEXT Button）

## 概述
`NEXT` 按钮类型用于在分页菜单中导航至下一页。

## 基本用法
```yaml
items:
  next:
    type: NEXT
    slot: 53
    item:
      material: ARROW
      name: "&a&l下一页 →"
      lore:
        - "&7跳转至第 %zmenu_player_next_page% 页"
```

## 分页机制说明
当按钮使用多个槽位（slots）且内容数量超过可用槽位时，系统将自动启用分页功能：

```yaml
items:
  shop-items:
    slots:
      - 10-16
      - 19-25
      - 28-34
    # 每页共 21 个槽位
    # 若共有 42 件商品，则自动生成 2 页
```

> `NEXT` 按钮仅在存在后续页面时才会显示并生效。

## 支持的属性
| 属性 | 说明 |
|------|------|
| `slot` / `slots` | 在界面中的位置 |
| `item` | 按钮的视觉外观 |
| `sound` | 点击时播放的音效 |
| `is-permanent` | 是否在所有页面中显示（推荐启用） |

## 示例

### 基础下一页按钮
```yaml
items:
  next:
    type: NEXT
    slot: 53
    is-permanent: true
    item:
      material: ARROW
      name: "&a&l下一页 →"
    sound: UI_BUTTON_CLICK
```

### 美化版下一页按钮
```yaml
items:
  next:
    type: NEXT
    slot: 53
    is-permanent: true
    item:
      material: PLAYER_HEAD
      url: "eyJ0ZXh0dXJlcyI6eyJTS0lOIjp7InVybCI6Imh0dHA6Ly90ZXh0dXJlcy5taW5lY3JhZnQubmV0L3RleHR1cmUvMmE2ZDE1YTUyNzNlNjE0YjY0YTQ4ZjE2OTIxMzYyMmZjNGRkOTJlMWVhMTc4YzJiZDY1NzI3NDVhYWE2NTRiIn19"
      name: "&a&l下一页 →"
      lore:
        - "&7当前页: &f%page%"
        - "&7跳转至: &f%zmenu_player_next_page%"
    sound: ITEM_BOOK_PAGE_TURN
```

### 导航组合（上一页 + 页码信息 + 下一页）
```yaml
items:
  previous:
    type: PREVIOUS
    slot: 45
    is-permanent: true
    item:
      material: ARROW
      name: " &c &l← 上一页 "
    sound: UI_BUTTON_CLICK

  page-info:
    slot: 49
    is-permanent: true
    item:
      material: PAPER
      name: " &e &l第 %page%/%max-page% 页 "
      lore:
        - " &7浏览不同页面 "

  next:
    type: NEXT
    slot: 53
    is-permanent: true
    item:
      material: ARROW
      name: " &a &l下一页 → "
    sound: UI_BUTTON_CLICK
```

### 无后续页面时自动隐藏
```yaml
items:
  next:
    type: NEXT
    slot: 53
    is-permanent: true
    view-requirement:
      requirements:
        - type: placeholder
          value: "%zmenu_player_page%"
          compare: "<"
          number_placeholder: "%zmenu_player_max_page%"
    item:
      material: ARROW
      name: "&a&l下一页 →"
    else:
      item:
        material: GRAY_STAINED_GLASS_PANE
        name: "&8"
```

### 完整分页菜单示例
```yaml
name: " &6 &l商店  &8( &7第 %page%/%max-page% 页 &8) "
size: 54
enable: true

fillItem:
  material: BLACK_STAINED_GLASS_PANE
  name: " &8 "

items:
  # 顶部边框
  top-border:
    slots:
      - 0-8
    item:
      material: BLUE_STAINED_GLASS_PANE
      name: " &8 "

  # 商品区域（自动分页）
  shop-items:
    slots:
      - 10-16
      - 19-25
      - 28-34
    # 此处定义的商品将自动填充槽位
    # 商品数量超过槽位时将自动生成多页

  # 底部边框
  bottom-border:
    slots:
      - 36-44
    is-permanent: true
    item:
      material: BLUE_STAINED_GLASS_PANE
      name: " &8 "

  # 导航区域
  previous:
    type: PREVIOUS
    slot: 45
    is-permanent: true
    item:
      material: ARROW
      name: " &c &l← 上一页 "
    sound: UI_BUTTON_CLICK

  back:
    type: BACK
    slot: 48
    is-permanent: true
    item:
      material: DARK_OAK_DOOR
      name: " &7 &l返回菜单 "
    sound: UI_BUTTON_CLICK

  page-info:
    slot: 49
    is-permanent: true
    item:
      material: BOOK
      name: " &e &l第 %page%/%max-page% 页 "

  close:
    slot: 50
    is-permanent: true
    close-inventory: true
    item:
      material: BARRIER
      name: " &c &l关闭 "
    sound: UI_BUTTON_CLICK

  next:
    type: NEXT
    slot: 53
    is-permanent: true
    item:
      material: ARROW
      name: " &a &l下一页 → "
    sound: UI_BUTTON_CLICK
```

## 分页相关占位符
| 占位符 | 说明 |
|--------|------|
| `%page%` | 当前页码 |
| `%maxPage%` / `%max-page%` | 总页数 |
| `%zmenu_player_page%` | 当前页码（PAPI 格式） |
| `%zmenu_player_max_page%` | 总页数（PAPI 格式） |
| `%zmenu_player_next_page%` | 下一页页码 |
| `%zmenu_player_previous_page%` | 上一页页码 |

## 最佳实践
- **启用永久显示**：务必设置 `is-permanent: true`，确保导航按钮在所有页面中可见
- **添加页码信息**：在上一页/下一页按钮之间放置页码指示器，提升用户体验
- **添加音效反馈**：翻页时播放音效增强交互感
- **智能隐藏按钮**：在末页时隐藏下一页按钮，避免无效操作
- **统一布局位置**：将导航按钮固定放置于底部行右侧（通常为槽位 53）

## 相关按钮类型
- [PREVIOUS](./previous) - 导航至上一页
- [JUMP](./jump) - 跳转至指定页码
- [BACK](./back) - 返回至上一级菜单

## 下一步学习
- 了解 [PREVIOUS 按钮](./previous) 的用法
- 查看 [JUMP](./jump) 实现直接页码跳转
- 浏览全部 [按钮类型](./none)