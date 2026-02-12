---
sidebar_position: 9
title: 条件按钮
description: 根据条件动态显示不同物品的按钮
---

# 动态选择按钮（SWITCH 按钮）



## SWITCH 按钮类型

`SWITCH` 按钮类型可根据占位符的值动态显示不同的物品。这使您能够创建外观随玩家数据或其他条件变化的动态按钮。

### 用法示例

```yaml
items:
  toggle:
    type: SWITCH
    slot: 13
    placeholder: "%zmenu_player_value_notifications%"
    buttons:
      "true":
        item:
          material: LIME_DYE
          name: " &a&l通知：开启 "
          lore:
            - " &7点击以禁用 "
        actions:
          - type: data
            action: SET
            key: "notifications"
            value: "false"
          - type: refresh

      "false":
        item:
          material: GRAY_DYE
          name: " &7&l通知：关闭 "
          lore:
            - " &7点击以启用 "
        actions:
          - type: data
            action: SET
            key: "notifications"
            value: "true"
          - type: refresh
```

### 配置参数

| 属性 | 说明 | 是否必需 |
|------|------|----------|
| `type` | 必须为 `SWITCH` | 是 |
| `slot` | 按钮所在槽位 | 是 |
| `placeholder` | 用于判断显示哪个按钮的占位符 | 是 |
| `buttons` | 值到按钮配置的映射表。键为占位符返回值，值为对应按钮配置 | 是 |
| `default` | 当占位符值不匹配任何已定义按钮时显示的默认按钮 | 否 |

#### `placeholder`（必需）
用于评估以确定显示哪个按钮的占位符。

```yaml
placeholder: "%zmenu_player_value_setting%"
```

#### `buttons`（必需）
值到按钮配置的映射表。键为占位符结果，值为要显示的按钮配置。

```yaml
buttons:
  "value1":
    item:
      material: DIAMOND
    actions:
      - type: message
        messages: ["当前值为 1"]
  "value2":
    item:
      material: EMERALD
    actions:
      - type: message
        messages: ["当前值为 2"]
```

#### `default`（可选）
当占位符值不匹配任何已定义按钮时显示的默认按钮。

```yaml
default:
  item:
    material: BARRIER
    name: "&c未知状态"
```

### 完整示例

#### 切换设置（PvP 开关）

```yaml
items:
  toggle-pvp:
    type: SWITCH
    slot: 13
    placeholder: "%zmenu_player_value_pvp_enabled%"
    buttons:
      "true":
        item:
          material: DIAMOND_SWORD
          name: " &c&lPvP：已启用 "
          lore:
            - " &7您可能被其他玩家攻击 "
            - " "
            - " &e▸ 点击禁用 "
          glow: true
        actions:
          - type: data
            action: SET
            key: "pvp_enabled"
            value: "false"
          - type: message
            messages:
              - " &aPvP 已禁用！"
          - type: sound
            sound: BLOCK_NOTE_BLOCK_PLING
          - type: refresh

      "false":
        item:
          material: SHIELD
          name: " &a&lPvP：已禁用 "
          lore:
            - " &7您受到玩家攻击保护 "
            - " "
            - " &e▸ 点击启用 "
        actions:
          - type: data
            action: SET
            key: "pvp_enabled"
            value: "true"
          - type: message
            messages:
              - " &cPvP 已启用！"
          - type: sound
            sound: ENTITY_ENDER_DRAGON_GROWL
          - type: refresh

    default:
      item:
        material: SHIELD
        name: " &a&lPvP：已禁用 "
      actions:
        - type: data
          action: SET
          key: "pvp_enabled"
          value: "true"
        - type: refresh
```

#### 多状态按钮（难度选择）

```yaml
items:
  difficulty:
    type: SWITCH
    slot: 22
    placeholder: "%zmenu_player_value_difficulty%"
    buttons:
      "easy":
        item:
          material: LIME_WOOL
          name: " &a&l难度：简单 "
          lore:
            - " &7当前：简单模式 "
            - " "
            - " &e▸ 点击切换至普通 "
        actions:
          - type: data
            action: SET
            key: "difficulty"
            value: "normal"
          - type: refresh

      "normal":
        item:
          material: YELLOW_WOOL
          name: " &e&l难度：普通 "
          lore:
            - " &7当前：普通模式 "
            - " "
            - " &e▸ 点击切换至困难 "
        actions:
          - type: data
            action: SET
            key: "difficulty"
            value: "hard"
          - type: refresh

      "hard":
        item:
          material: RED_WOOL
          name: " &c&l难度：困难 "
          lore:
            - " &7当前：困难模式 "
            - " "
            - " &e▸ 点击切换至简单 "
        actions:
          - type: data
            action: SET
            key: "difficulty"
            value: "easy"
          - type: refresh

    default:
      item:
        material: YELLOW_WOOL
        name: " &e&l难度：普通 "
      actions:
        - type: data
          action: SET
          key: "difficulty"
          value: "hard"
        - type: refresh
```

#### 基于权限组的显示

```yaml
items:
  rank-display:
    type: SWITCH
    slot: 4
    placeholder: "%luckperms_primary_group_name%"
    buttons:
      "default":
        item:
          material: COAL
          name: " &7&l成员 "
          lore:
            - " &7您当前的权限组 "

      "vip":
        item:
          material: IRON_INGOT
          name: " &a&lVIP "
          lore:
            - " &7您当前的权限组 "
          glow: true

      "mvp":
        item:
          material: GOLD_INGOT
          name: " &6&lMVP "
          lore:
            - " &7您当前的权限组 "
          glow: true

      "admin":
        item:
          material: DIAMOND
          name: " &b&l管理员 "
          lore:
            - " &7您当前的权限组 "
          glow: true

    default:
      item:
        material: COAL
        name: " &7&l成员 "
```

#### 任务进度指示器

```yaml
items:
  quest-status:
    type: SWITCH
    slot: 31
    placeholder: "%zmenu_player_value_quest_stage%"
    buttons:
      "0":
        item:
          material: PAPER
          name: " &7&l任务：未开始 "
          lore:
            - " &7开启您的冒险之旅！"
            - " "
            - " &e▸ 点击开始任务 "
        actions:
          - type: data
            action: SET
            key: "quest_stage"
            value: "1"
          - type: message
            messages:
              - " &a任务已开始！请与村庄长老对话。"
          - type: refresh

      "1":
        item:
          material: WRITABLE_BOOK
          name: " &e&l任务：进行中 "
          lore:
            - " &7与村庄长老对话 "
            - " "
            - " &7进度：&e1/3 "

      "2":
        item:
          material: WRITABLE_BOOK
          name: " &e&l任务：进行中 "
          lore:
            - " &7收集 10 个小麦 "
            - " "
            - " &7进度：&e2/3 "

      "3":
        item:
          material: ENCHANTED_BOOK
          name: " &a&l任务：已完成！"
          lore:
            - " &7返回领取奖励 "
            - " "
            - " &e▸ 点击领取 "
          glow: true
        actions:
          - type: console-command
            commands:
              - "give %player% diamond 5"
          - type: data
            action: SET
            key: "quest_stage"
            value: "done"
          - type: message
            messages:
              - " &a您获得了 5 颗钻石！"
          - type: refresh

      "done":
        item:
          material: BOOK
          name: " &8&l任务：已完结 "
          lore:
            - " &7您已完成此任务 "

    default:
      item:
        material: PAPER
        name: " &7&l任务：未开始 "
```

### 重要注意事项

- **务必使用 `refresh` 动作**：状态变更后始终包含刷新操作以更新界面
- **字符串值需加引号**：如 `"true"` 和 `"false"` 等值必须使用双引号包裹
- **提供默认状态**：始终为意外值定义 `default` 按钮，避免显示空白
- **玩家数据集成**：与 `%zmenu_player_value_*%` 占位符配合使用效果最佳
- **注意空格问题**：配置中键值（如 `"true "`）末尾的多余空格可能导致匹配失败，建议移除

### 常见应用场景

- 设置开关（开启/关闭）
- 多选项选择器
- 进度状态指示器
- 基于权限组的差异化显示
- 状态机实现
- 条件化奖励系统

### 最佳实践

- 状态变更后立即刷新界面
- 为玩家数据使用描述性明确的键名
- 提供视觉反馈（音效、消息提示）
- 为新玩家提供默认状态
- 不同状态间使用清晰的视觉区分（材质、颜色、发光效果等）

### 相关功能

- [玩家数据](../../player-data) - 存储玩家特定状态值
- [占位符](../../placeholders) - 动态值系统
- [动作系统](../actions) - 点击后执行的操作

### 后续学习

- 了解 [玩家数据](../../player-data) 以存储状态
- 查看所有可用 [动作类型](../actions)
- 浏览其他 [按钮类型](./none)