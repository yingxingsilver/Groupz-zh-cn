---
sidebar_position: 7
title: 跳转
description: 可直接跳转至指定页面的按钮
---

# 跳转按钮（JUMP）



## JUMP 按钮类型
`JUMP` 按钮类型用于在分页库存界面中直接导航至指定页码。

## 基础用法
```yaml
items:
  go_to_page_5:
    type: JUMP
    slot: 49
    page: 5
    item:
      material: PAPER
      name: "&e&l跳转至第 5 页"
```

## 属性说明

### page（必需）
要跳转的目标页码（必须为静态数值）。

```yaml
items:
  jump:
    type: JUMP
    slot: 0
    page: 3
```

## 实用示例

### 页面索引
创建页面索引实现快速导航：
```yaml
items:
  page_1:
    type: JUMP
    slot: 45
    page: 1
    item:
      material: PAPER
      amount: 1
      name: " &7第 1 页 "
    sound: UI_BUTTON_CLICK

  page_2:
    type: JUMP
    slot: 46
    page: 2
    item:
      material: PAPER
      amount: 2
      name: " &7第 2 页 "
    sound: UI_BUTTON_CLICK

  page_3:
    type: JUMP
    slot: 47
    page: 3
    item:
      material: PAPER
      amount: 3
      name: " &7第 3 页 "
    sound: UI_BUTTON_CLICK

  page_4:
    type: JUMP
    slot: 48
    page: 4
    item:
      material: PAPER
      amount: 4
      name: " &7第 4 页 "
    sound: UI_BUTTON_CLICK

  page_5:
    type: JUMP
    slot: 49
    page: 5
    item:
      material: PAPER
      amount: 5
      name: " &7第 5 页 "
    sound: UI_BUTTON_CLICK
```

### 高亮当前页面
```yaml
items:
  page_1:
    type: JUMP
    slot: 45
    page: 1
    view-requirement:
      requirements:
        - type: placeholder
          value: "%page%"
          compare: "!="
          number: 1
    item:
      material: PAPER
      amount: 1
      name: "&7第 1 页"
    else:
      item:
        material: ENCHANTED_BOOK
        amount: 1
        name: "&e&l第 1 页 &7(当前页)"
        glow: true
```

### 分类导航
在大型库存中跳转至不同分类区域：
```yaml
items:
  weapons:
    type: JUMP
    slot: 0
    page: 1
    item:
      material: DIAMOND_SWORD
      name: " &b &l武器 "
      lore:
        - " &7跳转至武器分类 "

  armor:
    type: JUMP
    slot: 1
    page: 3
    item:
      material: DIAMOND_CHESTPLATE
      name: " &9 &l护甲 "
      lore:
        - " &7跳转至护甲分类 "

  tools:
    type: JUMP
    slot: 2
    page: 5
    item:
      material: DIAMOND_PICKAXE
      name: " &a &l工具 "
      lore:
        - " &7跳转至工具分类 "

  food:
    type: JUMP
    slot: 3
    page: 7
    item:
      material: GOLDEN_APPLE
      name: " &6 &l食物 "
      lore:
        - " &7跳转至食物分类 "
```

### 首页/末页快捷方式
```yaml
items:
  first-page:
    type: JUMP
    slot: 45
    page: 1
    is-permanent: true
    item:
      material: PLAYER_HEAD
      url: "first_arrow_texture"
      name: " &7 &l<< 首页 "
    sound: UI_BUTTON_CLICK

  previous:
    type: PREVIOUS
    slot: 47
    is-permanent: true
    item:
      material: ARROW
      name: " &c &l< 上一页 "

  page-info:
    slot: 49
    is-permanent: true
    item:
      material: BOOK
      name: " &e &l第 %page%/%max-page% 页 "

  next:
    type: NEXT
    slot: 51
    is-permanent: true
    item:
      material: ARROW
      name: " &a &l下一页 > "

  # 注意：末页跳转需自定义解决方案，因 JUMP 要求静态页码
```

### 动态数量显示
将页码以物品数量形式直观展示：
```yaml
items:
  page-selector:
    type: JUMP
    slot: 13
    page: 1
    item:
      material: PAPER
      amount: 1  # 角标显示"1"
      name: "&7第 1 页"
```

### 结合视图条件
隐藏不存在的页面按钮：
```yaml
items:
  page_3:
    type: JUMP
    slot: 47
    page: 3
    view-requirement:
      requirements:
        - type: placeholder
          value: "%zmenu_player_max_page%"
          compare: ">="
          number: 3
    item:
      material: PAPER
      amount: 3
      name: "&7第 3 页"
```

## 典型应用场景
- 大型商品目录：快速访问特定分类区域
- 结构化商店：基于类别的导航系统
- 页面选择器：直观的页码视觉化展示
- 书签功能：跳转至常用/重要页面

## 限制说明
- 页码必须为静态值（在配置文件中预定义）
- 如需动态页码跳转，需结合自定义动作或插件实现

## 最佳实践
- **标识当前页**：结合 `view-requirement` 与 `else` 分支高亮当前页面
- **视觉化数量**：将 `amount`（数量）设置为对应页码，增强直观性
- **逻辑分组**：按功能/类别合理组织跳转按钮
- **添加提示**：使用 lore（描述）说明各页面内容
- **组合导航**：搭配 PREVIOUS/NEXT 按钮提供顺序导航能力

## 相关按钮类型
- [NEXT](./next) - 跳转至下一页
- [PREVIOUS](./previous) - 跳转至上一页
- [INVENTORY](./inventory) - 打开其他库存界面

## 后续学习
- 查看 [NEXT](./next) 与 [PREVIOUS](./previous) 了解顺序导航
- 学习 [MAIN_MENU](./mainmenu) 按钮用法
- 浏览全部 [按钮类型](./none)