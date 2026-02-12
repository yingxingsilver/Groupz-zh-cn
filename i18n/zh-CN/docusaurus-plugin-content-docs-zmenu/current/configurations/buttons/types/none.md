---
sidebar_position: 1
title: NONE
description: 默认按钮类型，无特殊行为
---

# NONE 按钮



## NONE 按钮类型

`NONE` 是默认的按钮类型。它仅显示物品并在点击时执行自定义操作，**不包含任何内置特殊行为**。

## 使用方法

```yaml
items:
  my-button:
    type: NONE  # 可选 - NONE 为默认类型，可省略
    slot: 13
    item:
      material: DIAMOND
      name: "&b&l我的按钮"
    actions:
      - type: message
        messages:
          - "&a你点击了按钮！"
```

由于 `NONE` 是默认类型，可直接省略 `type` 属性：

```yaml
items:
  my-button:
    slot: 13
    item:
      material: DIAMOND
      name: "&b&l我的按钮"
```

## 适用场景

`NONE` 按钮适用于以下用途：

- 信息展示
- 执行命令
- 打开链接
- 发送消息
- 播放音效
- 任何自定义交互行为

## 配置示例

### 信息展示按钮

```yaml
items:
  info:
    slot: 4
    item:
      material: BOOK
      name: "&6&l服务器信息"
      lore:
        - "&7欢迎来到我们的服务器！"
        - ""
        - "&7在线玩家: &a%server_online%"
        - "&7您的等级: &e%luckperms_primary_group_name%"
```

### 命令执行按钮

```yaml
items:
  spawn:
    slot: 11
    item:
      material: RED_BED
      name: "&c&l出生点"
      lore:
        - "&7传送至出生点"
    actions:
      - type: close
      - type: player-command
        commands:
          - "spawn"
```

### 链接跳转按钮

```yaml
items:
  website:
    slot: 15
    open-link: "https://myserver.com"
    item:
      material: PAPER
      name: "&a&l官方网站"
      lore:
        - "&7点击访问我们的网站"
```

### 带条件判断的交互按钮

```yaml
items:
  reward:
    slot: 22
    item:
      material: CHEST
      name: "&e&l每日奖励"
      lore:
        - "&7点击领取今日奖励！"
    click-requirement:
      requirements:
        - type: placeholder
          value: "%dailyreward_claimed%"
          compare: "equals_string"
          target: "false"
          deny:
            - type: message
              messages:
                - "&c你今天已经领取过奖励了！"
    success:
      - type: console-command
        commands:
          - "dailyreward claim %player%"
      - type: sound
        sound: ENTITY_PLAYER_LEVELUP
      - type: message
        messages:
          - "&a奖励领取成功！"
```

## 支持的属性

`NONE` 按钮支持所有标准按钮属性：

| 属性 | 说明 |
|------|------|
| `slot` / `slots` | 界面中的位置 |
| `item` | 视觉外观 |
| `actions` | 点击时执行的操作 |
| `click-requirement` | 点击所需的条件 |
| `view-requirement` | 显示所需的条件 |
| `sound` | 点击时播放的音效 |
| `messages` | 点击时发送的消息 |
| `close-inventory` | 点击后关闭界面 |
| `refresh-on-click` | 点击后刷新整个界面 |
| `update-on-click` | 点击后更新按钮状态 |
| `is-permanent` | 在所有分页中永久显示 |
| `page` | 仅在指定页码显示 |
| `else` | 条件不满足时的替代显示 |

## 最佳实践

- ✅ 作为基础按钮类型，适用于绝大多数自定义功能
- ✅ 结合 `click-requirement` 实现权限/状态判断
- ✅ 使用 `actions` 链式配置实现复杂交互逻辑
- ✅ 通过 `view-requirement` 实现动态显示控制
- ✅ 为提升体验，建议添加音效和视觉反馈

## 相关资源

- 了解 [INVENTORY](./inventory) 按钮 —— 打开其他菜单界面
- 探索 [BACK](./back) 按钮 —— 实现导航返回功能
- 查看全部 [操作类型（Actions）](../actions) —— 丰富按钮交互能力

## 后续学习

- 学习 [INVENTORY](./inventory) 按钮类型
- 探索 [BACK](./back) 导航按钮
- 浏览所有可用的 [按钮类型](./none)