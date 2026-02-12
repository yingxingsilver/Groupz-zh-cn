---
sidebar_position: 11
title: DYNAMIC_PAGINATION 按钮
description: 从数据源显示动态分页内容
---

# DYNAMIC_PAGINATION按钮

:::警告 需要 zMenu+
此按钮类型需要 [zMenu+](../../../zmenu-plus) 才能正常工作。
:::

`DYNAMIC_PAGINATION` 按钮类型允许您显示动态生成的分页内容。与常规的 `PAGINATION` 不同，此类型可以从外部数据源、占位符或插件集成中获取内容。

## 使用方法

```
items:
  dynamic-list:
    type: DYNAMIC_PAGINATION
    slots:
      - 10-16
      - 19-25
    source: "players"
    item:
      material: PLAYER_HEAD
      player-head: "%entry_name%"
      name: "&a%entry_name%"
```

## 配置项

| 属性 | 描述 | 是否必需 |
|------|------|----------|
| type | 必须为 `DYNAMIC_PAGINATION` | 是 |
| slots | 显示分页物品的槽位 | 是 |
| source | 数据源标识符 | 是 |
| item | 每个条目的物品模板 | 是 |

## 示例

### 玩家列表

```
size: 54
name: " &8在线玩家 "

items:
  player-list:
    type: DYNAMIC_PAGINATION
    slots:
      - 10-16
      - 19-25
      - 28-34
    source: "online_players"
    item:
      material: PLAYER_HEAD
      player-head: "%entry_name%"
      name: "&a&l%entry_name%"
      lore:
        - "&7点击查看详情"
    actions:
      - type: player-command
        commands:
          - "profile %entry_name%"

  # 导航
  previous:
    type: PREVIOUS
    slot: 48
    is-permanent: true
    item:
      material: ARROW
      name: "&c&l上一页"

  next:
    type: NEXT
    slot: 50
    is-permanent: true
    item:
      material: ARROW
      name: "&a&l下一页"

  # 页面信息
  info:
    slot: 49
    is-permanent: true
    item:
      material: PAPER
      name: "&7第 %page%/%max_page% 页"
```

## 占位符

| 占位符 | 描述 |
|--------|------|
| %entry_name% | 条目名称/标识符 |
| %entry_index% | 列表中的条目索引 |
| %page% | 当前页码 |
| %max_page% | 总页数 |

## 后续步骤

了解 [PAGINATION](./pagination) 以使用静态分页
查看 [INPUT](./input) 了解玩家输入处理