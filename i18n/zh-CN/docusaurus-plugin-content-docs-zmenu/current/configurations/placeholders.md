---
sidebar_position: 3
title: 占位符
description: zMenu 中所有可用的占位符
---

# 占位符（Placeholders）




## 占位符

zMenu 提供内置占位符，并支持 PlaceholderAPI，可在物品栏菜单中实现动态内容展示。

## 内置占位符

以下占位符无需额外插件即可使用：

### 玩家占位符

| 占位符 | 说明 | 示例输出 |
|--------|------|----------|
| `%player%` | 玩家名称 | Notch |

### 分页占位符

| 占位符 | 说明 | 示例输出 |
|--------|------|----------|
| `%page%` | 当前页码 | 1 |
| `%maxPage%` | 最大页码 | 5 |
| `%max-page%` | 最大页码（别名） | 5 |
| `%zmenu_player_page%` | 当前页码（PAPI 格式） | 1 |
| `%zmenu_player_next_page%` | 下一页页码 | 2 |
| `%zmenu_player_previous_page%` | 上一页页码 | 0 |
| `%zmenu_player_max_page%` | 最大页码（PAPI 格式） | 5 |

### 物品栏历史占位符

| 占位符 | 说明 | 示例输出 |
|--------|------|----------|
| `%zmenu_player_previous_inventories%` | 历史记录中的上一个物品栏数量 | 3 |

### 数学占位符

直接在配置中执行数学运算：

| 占位符 | 说明 | 示例 |
|--------|------|------|
| `%zmenu_math_<表达式>%` | 计算数学表达式 | `%zmenu_math_5+5%` → 10 |
| `%zmenu_formatted_math_<表达式>%` | 格式化数学结果 | `%zmenu_formatted_math_1000+500%` → 1,500 |

**支持的运算符：**
- 加法：`+`
- 减法：`-`
- 乘法：`*`
- 除法：`/`
- 括号：`()`

**示例：**
```yaml
lore:
  - "&7结果: %zmenu_math_10*5%"           # 输出: 50
  - "&7余额: %zmenu_formatted_math_%vault_eco_balance%*2%"
```

### 玩家数据占位符

| 占位符 | 说明 |
|--------|------|
| `%zmenu_player_value_<键名>%` | 获取玩家数据值 |

**示例：**
```yaml
lore:
  - "&7金币: &6%zmenu_player_value_coins%"
  - "&7击杀数: &c%zmenu_player_value_kills%"
```

### 命令参数占位符

| 占位符 | 说明 |
|--------|------|
| `%zmenu_argument_<键名>%` | 获取命令参数值 |

此占位符用于获取通过 zMenu 自定义命令传递的参数。

**示例：**

命令配置：
```yaml
# commands/commands.yml
commands:
  punish:
    command: punish
    arguments:
      - player
    inventory: example_punish
```

在物品栏中使用参数：
```yaml
items:
  player-info:
    item:
      material: PLAYER_HEAD
      playerHead: "%zmenu_argument_player%"
      name: "&c惩罚 %zmenu_argument_player%"
      lore:
        - "&7点击惩罚该玩家"
```

当玩家执行 `/punish Notch` 时，占位符 `%zmenu_argument_player%` 将返回 `Notch`。

### 全局占位符

| 占位符 | 说明 |
|--------|------|
| `%zmenu_global_placeholders_<键名>%` | 获取全局占位符值 |

**示例：**
```yaml
# global-placeholders.yml
server-name: "我的服务器"

# 物品栏配置
name: "&6%zmenu_global_placeholders_server-name%"
```

### 时间占位符

| 占位符 | 说明 | 示例输出 |
|--------|------|----------|
| `%zmenu_time_unix_timestamp%` | 当前 Unix 时间戳 | 1704067200 |
| `%zmenu_time_next_day_unix_timestamp%` | 明天的 Unix 时间戳 | 1704153600 |
| `%zmenu_time_today_start_unix_timestamp%` | 今天开始的时间戳 | 1704067200 |

### 统计占位符

| 占位符 | 说明 | 示例输出 |
|--------|------|----------|
| `%zmenu_statistic_hours_played%` | 游玩小时数（四舍五入） | 42 |
| `%zmenu_statistic_time_played%` | 格式化游玩时间 | 1d 18h 30m |

## PlaceholderAPI 集成

zMenu 完全支持 PlaceholderAPI。所有 PAPI 占位符可用于：
- 物品栏标题
- 物品名称
- 物品 lore
- 消息文本
- 条件要求
- 操作配置

### 常用 PlaceholderAPI 扩展

使用 `/papi ecloud download <名称>` 安装扩展：

| 扩展 | 占位符示例 | 示例输出 |
|------|------------|----------|
| Player | `%player_name%`, `%player_health%`, `%player_level%` | `%player_name%` → Notch |
| Vault | `%vault_eco_balance%`, `%vault_eco_balance_formatted%` | `%vault_eco_balance%` → 1500.00 |
| Statistic | `%statistic_deaths%`, `%statistic_kills%` | `%statistic_deaths%` → 42 |
| Server | `%server_online%`, `%server_max_players%` | `%server_online%` → 50 |
| LuckPerms | `%luckperms_prefix%`, `%luckperms_primary_group_name%` | `%luckperms_prefix%` → [Admin] |

### 使用示例

```yaml
items:
  profile:
    slot: 4
    item:
      material: PLAYER_HEAD
      playerHead: "%player_name%"
      name: "&6%player_name% 的资料"
      lore:
        - "&8&m─────────────────"
        - ""
        - "&7生命值: &c%player_health% &7/ &c%player_max_health%"
        - "&7等级: &a%player_level%"
        - "&7余额: &6$%vault_eco_balance_formatted%"
        - ""
        - "&7击杀: &a%statistic_player_kills%"
        - "&7死亡: &c%statistic_deaths%"
        - ""
        - "&8&m─────────────────"
```

## 本地占位符

为特定物品栏定义专属占位符：

```yaml
# 物品栏配置文件
local-placeholders:
  price: "100"
  item-name: "特殊之剑"

items:
  shop-item:
    item:
      name: "&6%item-name%"
      lore:
        - "&7价格: &a$%price%"
```

## 占位符缓存

为提升性能，可在 `config.yml` 中启用占位符缓存：

```yaml
# 启用 PlaceholderAPI 缓存
enable-cache-placeholder-api: true

# 缓存持续时间（单位：游戏刻，20 刻 = 1 秒）
cache-placeholder-api: 20
```

:::warning 警告
启用缓存后占位符不会实时更新。仅在遇到性能问题且了解其影响时才启用此功能。
:::

## 嵌套占位符

可在占位符内部嵌套其他占位符：

```yaml
# 在数学运算中使用玩家数据
lore:
  - "&7双倍金币: %zmenu_math_%zmenu_player_value_coins%*2%"
```

## 带占位符的条件要求

在条件要求中使用占位符创建动态判断：

```yaml
click-requirement:
  requirements:
    - type: placeholder
      value: "%vault_eco_balance%"
      compare: ">="
      number: 100
      deny:
        - type: message
          messages:
            - "&c您至少需要 $100！"
```

## 占位符格式化

### 数字格式化

优先使用带 `_formatted` 后缀的格式化版本：

- `%vault_eco_balance_formatted%` 优于 `%vault_eco_balance%`
- `%zmenu_formatted_math_<表达式>%` 优于 `%zmenu_math_<表达式>%`

### 占位符中的颜色

部分占位符自带颜色代码。如需保留颜色：
```yaml
name: "%luckperms_prefix% %player_name%"  # 保留颜色
```

## 调试占位符

如果占位符无法正常工作：

1. 检查是否已安装 PlaceholderAPI
   ```
   /papi info
   ```

2. 测试占位符
   ```
   /papi parse me %占位符名称%
   ```

3. 检查扩展是否已安装
   ```
   /papi list
   ```

4. 安装缺失的扩展
   ```
   /papi ecloud download <扩展名>
   /papi reload
   ```

## 快速参考表

| 类别 | 占位符 | 说明 |
|------|--------|------|
| 玩家 | `%player%` | 玩家名称 |
| 分页 | `%page%` | 当前页码 |
| 分页 | `%maxPage%` | 最大页码 |
| 数学 | `%zmenu_math_<表达式>%` | 计算表达式 |
| 数据 | `%zmenu_player_value_<键名>%` | 玩家数据 |
| 参数 | `%zmenu_argument_<键名>%` | 命令参数 |
| 全局 | `%zmenu_global_placeholders_<键名>%` | 全局值 |
| 时间 | `%zmenu_time_unix_timestamp%` | Unix 时间 |
| 统计 | `%zmenu_statistic_hours_played%` | 游玩小时数 |

## 后续步骤

- 了解 [全局占位符](./global-placeholders) 配置
- 设置 [玩家数据](./player-data) 存储自定义值
- 创建基于占位符的动态 [条件要求](./buttons/button#requirements)
