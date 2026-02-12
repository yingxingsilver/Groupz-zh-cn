---
sidebar_position: 9
title: 玩家数据
description: 存储和检索玩家专属数据
---

# 玩家数据（Player Data）



## 玩家数据

zMenu 内置了玩家数据系统，允许您存储和检索每位玩家专属的数值。该功能非常适合用于追踪进度、创建开关设置、存储统计数据等场景。

## 工作原理

玩家数据存储在数据库中（默认使用 SQLite，可选 MySQL/MariaDB），并在服务器重启后依然保留。每位玩家都拥有自己独立的键值对集合。

## 访问玩家数据

### 占位符

使用 `%zmenu_player_value_<key>%` 占位符：

```yaml
item:
  name: "&6金币: %zmenu_player_value_coins%"
  lore:
    - "&7击杀数: %zmenu_player_value_kills%"
    - "&7死亡数: %zmenu_player_value_deaths%"
```

### 命令

```bash
# 设置数值
/zm players set <玩家> <键> <值>

# 增加数值（仅限数字）
/zm players add <玩家> <键> <值>

# 获取数值
/zm players get <玩家> <键>

# 删除键
/zm players remove <玩家> <键>

# 从所有玩家中删除键
/zm players removeall <键>

# 列出玩家的所有键
/zm players keys <玩家>
```

## 通过操作修改数据

### 数据操作（Data Action）

```yaml
actions:
  - type: data
    action: SET      # SET（设置）、ADD（增加）、SUBTRACT（减少）、MULTIPLY（乘）、DIVIDE（除）、REMOVE（移除）
    key: "coins"
    value: "100"
    math: true       # 启用数学表达式
```

### 操作类型

| 操作 | 说明 | 示例 |
|------|------|------|
| `SET` | 设置为指定值 | `value: "100"` |
| `ADD` | 在当前值上增加 | `value: "50"` |
| `SUBTRACT` | 从当前值中减去 | `value: "25"` |
| `MULTIPLY` | 乘以指定值 | `value: "2"` |
| `DIVIDE` | 除以指定值 | `value: "2"` |
| `REMOVE` | 完全移除该键 | — |

### 数学表达式

启用 `math: true` 以使用数学表达式：

```yaml
actions:
  - type: data
    action: SET
    key: "total"
    value: "%zmenu_player_value_base%*1.5+100"
    math: true
```

## 示例

### 金币/货币系统

```yaml
# 显示金币
items:
  coin-display:
    slot: 4
    item:
      material: GOLD_NUGGET
      name: "&6&l您的金币"
      lore:
        - "&7余额: &e%zmenu_player_value_coins%"

  # 增加金币按钮
  add-coins:
    slot: 11
    item:
      material: GOLD_INGOT
      name: "&a&l+100 金币"
    actions:
      - type: data
        action: ADD
        key: "coins"
        value: "100"
      - type: message
        messages:
          - "&a获得 100 金币！"
      - type: refresh

  # 消费金币
  spend-coins:
    slot: 15
    item:
      material: DIAMOND
      name: "&b&l购买钻石"
      lore:
        - "&7价格: &e50 金币"
    click-requirement:
      requirements:
        - type: placeholder
          value: "%zmenu_player_value_coins%"
          compare: ">="
          number: 50
          deny:
            - type: message
              messages:
                - "&c您需要 50 金币！"
      success:
        - type: data
          action: SUBTRACT
          key: "coins"
          value: "50"
        - type: console-command
          commands:
            - "give %player% diamond 1"
        - type: refresh
```

### 切换设置（Toggle）

```yaml
items:
  toggle-notifications:
    type: SWITCH
    slot: 13
    placeholder: "%zmenu_player_value_notifications%"
    buttons:
      "true":
        item:
          material: LIME_DYE
          name: "&a&l通知: 开启"
          lore:
            - "&7点击关闭通知"
        actions:
          - type: data
            action: SET
            key: "notifications"
            value: "false"
          - type: refresh

      "false":
        item:
          material: GRAY_DYE
          name: "&7&l通知: 关闭"
          lore:
            - "&7点击开启通知"
        actions:
          - type: data
            action: SET
            key: "notifications"
            value: "true"
          - type: refresh

    default:
      item:
        material: LIME_DYE
        name: "&a&l通知: 开启"
      actions:
        - type: data
          action: SET
          key: "notifications"
          value: "false"
        - type: refresh
```

### 每日奖励系统

```yaml
items:
  daily-reward:
    slot: 22
    item:
      material: CHEST
      name: "&e&l每日奖励"
      lore:
        - "&7领取您的每日奖励！"
        - ""
        - "&7上次领取: &f%zmenu_player_value_last_daily%"
    click-requirement:
      requirements:
        - type: placeholder
          value: "%zmenu_math_%zmenu_time_unix_timestamp%-%zmenu_player_value_last_daily%%"
          compare: ">="
          number: 86400  # 24 小时（秒）
          deny:
            - type: message
              messages:
                - "&c您今天已领取过奖励！"
      success:
        - type: data
          action: SET
          key: "last_daily"
          value: "%zmenu_time_unix_timestamp%"
        - type: console-command
          commands:
            - "give %player% diamond 5"
        - type: data
          action: ADD
          key: "daily_streak"
          value: "1"
        - type: message
          messages:
            - "&a已领取每日奖励！"
            - "&7连续天数: &e%zmenu_player_value_daily_streak%"
        - type: sound
          sound: ENTITY_PLAYER_LEVELUP
```

### 进度追踪

```yaml
items:
  quest-progress:
    slot: 13
    type: SWITCH
    placeholder: "%zmenu_player_value_quest_1_stage%"
    buttons:
      "0":
        item:
          material: PAPER
          name: "&7&l任务: 未开始"
          lore:
            - "&7点击开始任务"
        actions:
          - type: data
            action: SET
            key: "quest_1_stage"
            value: "1"
          - type: message
            messages:
              - "&a任务已开始！"
          - type: refresh

      "1":
        item:
          material: WRITABLE_BOOK
          name: "&e&l任务: 进行中"
          lore:
            - "&7阶段 1: 收集 10 个木头"

      "2":
        item:
          material: WRITABLE_BOOK
          name: "&e&l任务: 进行中"
          lore:
            - "&7阶段 2: 制作一把剑"

      "complete":
        item:
          material: ENCHANTED_BOOK
          name: "&a&l任务: 已完成！"
          lore:
            - "&7点击领取奖励"
          glow: true
        actions:
          - type: console-command
            commands:
              - "give %player% emerald 10"
          - type: data
            action: SET
            key: "quest_1_stage"
            value: "claimed"
          - type: refresh

      "claimed":
        item:
          material: BOOK
          name: "&8&l任务: 已领取"
          lore:
            - "&7您已完成此任务"

    default:
      item:
        material: PAPER
        name: "&7&l任务: 未开始"
```

### 击杀计数器

```yaml
items:
  stats-display:
    slot: 4
    item:
      material: PLAYER_HEAD
      playerHead: "%player%"
      name: "&a&l%player% 的统计数据"
      lore:
        - "&8&m───────────────"
        - ""
        - "&7击杀: &a%zmenu_player_value_kills%"
        - "&7死亡: &c%zmenu_player_value_deaths%"
        - "&7K/D 比: &e%zmenu_math_%zmenu_player_value_kills%/%zmenu_player_value_deaths%%"
        - ""
        - "&8&m───────────────"
```

## 数据库配置

在 `config.yml` 中配置存储方式：

```yaml
# 存储类型: SQLITE, MYSQL, MARIADB, 或 NONE
storage-type: SQLITE

# 数据库配置（用于 MySQL/MariaDB）
database-configuration:
  table-prefix: "zmenu_"
  host: "localhost"
  port: 3306
  user: "username"
  password: "password"
  database: "zmenu"
```

## JSON 转 SQL

如果您之前使用 JSON 存储数据：

```bash
/zm players convert
```

此命令将数据从 JSON 文件迁移到配置的数据库中。

## 默认值

如果键不存在，占位符将返回空字符串。可使用以下方式处理默认值：

```yaml
# 在条件判断中检查是否存在
- type: placeholder
  value: "%zmenu_player_value_coins%"
  compare: ">="
  number: 0  # 即使键不存在也能正常工作
```

或在首次访问时设置默认值：

```yaml
items:
  initialize:
    slot: 0
    view-requirement:
      requirements:
        - type: placeholder
          value: "%zmenu_player_value_initialized%"
          compare: "!="
          target: "true"
    item:
      material: AIR
    actions:
      - type: data
        action: SET
        key: "coins"
        value: "0"
      - type: data
        action: SET
        key: "initialized"
        value: "true"
      - type: refresh
```

## 最佳实践

- **使用描述性键名**：如 `daily_reward_claimed` 而非 `drc`
- **初始化默认值**：为新玩家设置初始数值
- **谨慎使用数学运算**：确保参与运算的值为数字类型
- **清理旧数据**：对不再使用的键使用 `removeall` 清理
- **备份数据库**：在进行重大更改前务必备份
- **记录键名用途**：维护一份数据键的文档说明

## 键名命名规范

```yaml
# 推荐示例
player_coins
quest_tutorial_complete
settings_notifications
stats_kills
daily_last_claim

# 不推荐示例
c        # 过于简短
data1    # 描述性不足
x        # 无实际意义
```

## 后续步骤

- 使用玩家数据创建 [切换按钮（Toggle Buttons）](./buttons/types/switch)
- 设置 [自定义命令](./custom-commands)
- 配置 [config.yml](./config-yml) 中的数据库设置
