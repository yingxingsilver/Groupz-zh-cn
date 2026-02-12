---
sidebar_position: 10
title: 分页
description: 在菜单中展示分页内容
---

# 分页按钮（PAGINATION Button）

## 概述
`PAGINATION` 按钮类型用于在菜单中展示分页内容，可自动将物品跨多页进行分页处理。

:::warning 需要 zMenu+
此按钮类型需要安装 [zMenu+](../../../zmenu-plus) 才能正常使用。
:::

## 基本用法
```yaml
items:
  paginated-items:
    type: PAGINATION
    slots:
      - 10-16
      - 19-25
      - 28-34
    item:
      material: STONE
      name: "&7物品 %index%"
```

## 配置属性
| 属性 | 说明 | 是否必需 |
|------|------|----------|
| `type` | 必须为 `PAGINATION` | 是 |
| `slots` | 分页物品显示的槽位范围 | 是 |
| `item` | 每个条目的物品模板 | 是 |

## 示例

### 基础分页
```yaml
size: 54
name: " &8分页菜单 "

items:
  # 分页内容区域
  content:
    type: PAGINATION
    slots:
      - 10-16
      - 19-25
      - 28-34
    item:
      material: PAPER
      name: " &e &l物品 #%index% "
      lore:
        - " &7这是第 %index% 个物品 "

  # 导航按钮
  previous:
    type: PREVIOUS
    slot: 48
    is-permanent: true
    item:
      material: ARROW
      name: " &c &l上一页 "

  next:
    type: NEXT
    slot: 50
    is-permanent: true
    item:
      material: ARROW
      name: " &a &l下一页 "
```

## 可用占位符
| 占位符 | 说明 |
|--------|------|
| `%index%` | 当前物品的索引编号 |
| `%page%` | 当前页码 |
| `%max_page%` | 总页数 |

## 与其他分页方式的区别
| 特性 | `PAGINATION` 按钮 | 普通按钮 + 多槽位 |
|------|------------------|------------------|
| 内容来源 | 自动根据 `item` 模板生成序列化物品 | 需手动定义多个物品条目 |
| 索引支持 | 支持 `%index%` 占位符 | 不支持自动索引 |
| 适用场景 | 规则化、大量相似物品 | 少量或差异较大的物品 |
| 依赖要求 | 需要 zMenu+ | 仅需基础 zMenu |

## 最佳实践
- **配合导航按钮使用**：务必添加 `PREVIOUS` 和 `NEXT` 按钮以便翻页
- **设置永久显示**：导航按钮应设置 `is-permanent: true`
- **预留足够槽位**：合理规划每页显示的物品数量（如 3×7=21 个槽位）
- **使用索引占位符**：通过 `%index%` 动态生成物品名称和描述

## 下一步学习
- 了解 [DYNAMIC_PAGINATION](./dynamic-pagination) 实现动态数据分页
- 查看 [NEXT](./next) 与 [PREVIOUS](./previous) 按钮的导航功能