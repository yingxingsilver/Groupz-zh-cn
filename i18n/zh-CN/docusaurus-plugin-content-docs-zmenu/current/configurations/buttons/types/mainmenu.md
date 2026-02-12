---
sidebar_position: 8
title: 主菜单
description: 打开已配置主菜单的按钮
---

# 主菜单按钮（MAIN_MENU）



## MAIN_MENU 按钮类型

`MAIN_MENU` 按钮类型用于打开在 `config.yml` 中配置为主菜单的界面。

## 配置方法

首先，在 `config.yml` 中设置主菜单：

```yaml
# config.yml
main-menu: "main"  # 主菜单界面的名称
```

## 使用方式

```yaml
items:
  main-menu:
    type: MAIN_MENU
    slot: 49
    item:
      material: NETHER_STAR
      name: "&6&l主菜单"
      lore:
        - "&7返回主菜单"
```

## 与 HOME 按钮的区别

| 按钮类型 | 行为说明 |
|---------|---------|
| `HOME` | 返回玩家在当前会话中**首次打开**的界面 |
| `MAIN_MENU` | 始终打开 `config.yml` 中**指定配置**的主菜单界面 |

### 示例场景：

1. 玩家通过命令 `/zm open shop` 直接打开商店菜单
2. 玩家导航路径：商店 → 剑类 → 钻石剑详情
3. 此时：
   - `HOME` 按钮将返回**商店**（会话中首次打开的界面）
   - `MAIN_MENU` 按钮将打开配置的主菜单（例如名为 "main" 的界面）

## 配置示例

### 基础主菜单按钮

```yaml
items:
  main-menu:
    type: MAIN_MENU
    slot: 49
    item:
      material: NETHER_STAR
      name: "&6&l主菜单"
    sound: UI_BUTTON_CLICK
```

### 美化版主菜单按钮

```yaml
items:
  main-menu:
    type: MAIN_MENU
    slot: 49
    item:
      material: PLAYER_HEAD
      url: "eyJ0ZXh0dXJlcyI6eyJTS0lOIjp7InVybCI6Imh0dHA6Ly90ZXh0dXJlcy5taW5lY3JhZnQubmV0L3RleHR1cmUvYzVhMzFjNjQ1MzNlNTRhZmFjZjA0YzNhMTY3YzM4YjBhYjIzMjI3NjdmYjE2MWVmMTU5MjI5YTI4ZmJiN2EifX0="
      name: "&6&l⌂ 主菜单"
      lore:
        - "&7前往服务器主菜单"
        - ""
        - "&e▸ 点击打开"
    sound: BLOCK_NOTE_BLOCK_CHIME
```

### 导航栏中的应用

```yaml
items:
  back:
    type: BACK
    slot: 45
    is-permanent: true
    item:
      material: ARROW
      name: " &c &l返回 "

  previous:
    type: PREVIOUS
    slot: 47
    is-permanent: true
    item:
      material: ARROW
      name: " &7上一页 "

  main-menu:
    type: MAIN_MENU
    slot: 49
    is-permanent: true
    item:
      material: NETHER_STAR
      name: " &6 &l主菜单 "

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

### 在所有子菜单中永久显示

```yaml
items:
  main-menu:
    type: MAIN_MENU
    slot: 49
    is-permanent: true
    item:
      material: NETHER_STAR
      name: "&6&l主菜单"
      lore:
        - "&7返回主菜单"
    sound: UI_BUTTON_CLICK
```

## 适用场景

建议在以下情况使用 `MAIN_MENU` 按钮：

- 拥有一个玩家可从任意位置访问的中心枢纽菜单
- 玩家可能通过命令直接打开子菜单
- 需要在所有菜单中提供一致的导航体验
- 服务器存在一个作为所有功能入口的主菜单

## 最佳实践

- ✅ 首先在 `config.yml` 中正确配置 `main-menu`
- ✅ 在所有子菜单中统一使用该按钮
- ✅ 放置在中心位置 —— 槽位 49 是常见选择
- ✅ 使用醒目图标 —— 下界之星（NETHER_STAR）效果良好
- ✅ 纳入模式（Pattern）— 将其加入导航模式以保证一致性

## 创建主菜单导航模式

创建可复用的导航栏模式：

```yaml
# patterns/navigation.yml
name: "navigation"
size: 54

items:
  back:
    type: BACK
    slot: 45
    is-permanent: true
    item:
      material: ARROW
      name: "&c&l返回"
    sound: UI_BUTTON_CLICK

  main-menu:
    type: MAIN_MENU
    slot: 49
    is-permanent: true
    item:
      material: NETHER_STAR
      name: "&6&l主菜单"
    sound: UI_BUTTON_CLICK

  close:
    slot: 53
    is-permanent: true
    close-inventory: true
    item:
      material: BARRIER
      name: "&c&l关闭"
    sound: UI_BUTTON_CLICK
```

在菜单配置中引用该模式：

```yaml
# inventories/shop.yml
name: "&6商店"
size: 54
patterns:
  - "navigation"

items:
  # 您的商店物品配置...
```

## 相关按钮类型

- [HOME](./home) —— 返回会话中首次打开的界面
- [BACK](./back) —— 后退一步
- [INVENTORY](./inventory) —— 打开指定界面

## 后续学习

- 了解 [SWITCH](./switch) 按钮类型
- 创建可复用的 [模式（Patterns）](../../patterns)
- 查看全部 [按钮类型](./none)