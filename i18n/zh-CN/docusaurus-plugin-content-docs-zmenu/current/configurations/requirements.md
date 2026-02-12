---
sidebar_position: 7
title: 条件系统
description: zMenu 中按钮与库存的完整条件系统指南
---

# 条件系统（Requirements）



## 条件系统

条件系统用于定义按钮显示、点击或库存打开前必须满足的条件。它是 zMenu 创建动态、条件化菜单的核心机制。

### 条件系统工作原理

条件系统会检查特定条件（权限、占位符值、背包物品等）。根据检查结果：

- **所有条件满足** → 执行 `success`（成功）动作
- **任一条件失败** → 执行该条件的 `deny`（拒绝）动作

```yaml
click-requirement:
  requirements:
    - type: permission
      permission: "server.vip"
      deny:
        - type: message
          messages:
            - "&c你需要 VIP 权限才能使用此功能！"
  success:
    - type: message
      messages:
        - "&a欢迎，VIP 用户！"
```

### 条件系统的使用场景

条件系统可在三种上下文中使用：

#### view-requirement（按钮可见性）

控制按钮是否显示。条件不满足时，按钮将完全隐藏。

```yaml
items:
  vip-only:
    slot: 0
    view-requirement:
      requirements:
        - type: permission
          permission: "server.vip"
    item:
      material: DIAMOND
      name: "&b&lVIP 专属物品"
```

#### click-requirement（按钮点击）

控制按钮是否可被点击。按钮保持可见，但条件不满足时点击将被拒绝。

```yaml
items:
  purchase:
    slot: 0
    click-requirement:
      requirements:
        - type: placeholder
          value: "%vault_eco_balance%"
          compare: ">="
          number: 100
          deny:
            - type: message
              messages:
                - "&c你需要 $100 才能购买此物品！"
            - type: sound
              sound: ENTITY_VILLAGER_NO
      success:
        - type: currency-withdraw
          amount: 100
        - type: message
          messages:
            - "&a购买成功！"
    item:
      material: GOLD_INGOT
      name: "&e&l购买 - $100"
```

#### view-requirement / open-requirement（库存打开）

控制库存是否可被打开。应用于库存级别（非单个按钮）。`open-requirement` 是库存级别 `view-requirement` 的别名。

```yaml
# 库存文件中
name: "&6VIP 专属菜单"
size: 54

view-requirement:
  requirements:
    - type: permission
      permission: "server.vip"
      deny:
        - type: message
          messages:
            - "&c你需要 VIP 权限才能访问此菜单！"
```

条件不满足时，库存将无法打开并执行拒绝动作。

---

## 条件类型详解

### permission（权限）

检查玩家是否拥有特定权限。

```yaml
requirements:
  - type: permission
    permission: "server.admin"
```

| 键名 | 类型 | 说明 |
|------|------|------|
| permission | String | 要检查的权限节点 |

:::tip
可使用任何权限插件（LuckPerms 等）的权限。这是最简单、最常见的条件类型。
:::

### placeholder（占位符）

将占位符值与目标值进行比较。这是最灵活的条件类型。

**数值比较**

```yaml
requirements:
  - type: placeholder
    value: "%player_level%"
    compare: ">="
    number: 10
```

| 键名 | 类型 | 说明 |
|------|------|------|
| value | String | 要评估的占位符 |
| compare | String | 比较运算符 |
| number | Number | 要比较的数值 |

**字符串比较**

```yaml
requirements:
  - type: placeholder
    value: "%player_world%"
    compare: "equals_string"
    target: "world_nether"
```

| 键名 | 类型 | 说明 |
|------|------|------|
| value | String | 要评估的占位符 |
| compare | String | 比较运算符 |
| target | String | 要比较的字符串值 |

**比较运算符**

| 运算符 | 说明 | 示例 |
|--------|------|------|
| `==` | 等于（数值） | `compare: "=="` |
| `!=` | 不等于（数值） | `compare: "!="` |
| `>=` | 大于或等于 | `compare: ">="` |
| `>` | 大于 | `compare: ">"` |
| `<=` | 小于或等于 | `compare: "<="` |
| `<` | 小于 | `compare: "<"` |
| `equals_string` | 精确字符串匹配 | `compare: "equals_string"` |
| `equalsIgnoreCase` | 忽略大小写字符串匹配 | `compare: "equalsIgnoreCase"` |

:::info 替代语法
在模板（Patterns）中，也可使用 `action` 键替代 `compare`：
- `SUPERIOR_OR_EQUAL`（等同于 `>=`）
- `SUPERIOR`（等同于 `>`）
- `EQUAL_TO`（等同于 `==`）
- `LOWER`（等同于 `<`）

```yaml
requirements:
  - type: placeholder
    placeholder: "%player_level%"
    value: '10'
    action: SUPERIOR_OR_EQUAL
```
:::

**数学表达式**

启用 `math: true` 可对占位符值进行数学表达式求值：

```yaml
requirements:
  - type: placeholder
    math: true
    placeholder: "%zmenu_player_value_cookie%"
    value: "%price%+(%price%*%zmenu_player_value_upgrades%*1.15)"
    action: SUPERIOR_OR_EQUAL
```

此功能在比较前先计算数学表达式，适用于动态定价、成本缩放或复杂条件。

### money（金钱）

检查玩家账户余额是否充足。支持以下经济插件：
[BeastTokens](https://www.spigotmc.org/resources/beasttokens-custom-currency.20806/)、
[Vault](https://www.spigotmc.org/resources/34315/)、
[PlayerPoints](https://www.spigotmc.org/resources/80745/)、
[ElementalTokens](https://builtbybit.com/resources/16707/)、
[ElementalGems](https://builtbybit.com/resources/14920/)、
[Level](https://www.minecraft.net/)、
[Experience](https://www.minecraft.net/)、
[zEssentials](https://www.spigotmc.org/resources/118014/)、
[EcoBits](https://www.spigotmc.org/resources/109967/)、
[CoinsEngine](https://www.spigotmc.org/resources/84121/)、
[VotingPlugin](https://www.spigotmc.org/resources/15358/)

基于 [CurrenciesAPI](https://github.com/Traqueur-dev/CurrenciesAPI) 实现。

```yaml
requirements:
  - type: money
    amount: 1000
```

| 键名 | 类型 | 说明 |
|------|------|------|
| amount | Number | 所需最低余额 |
| currency | String | 货币名称（可选） |
| economy | String | 经济系统名称（仅 zEssentials、CoinsEngine 和 EcoBits 需要） |

指定货币和经济系统的示例：

```yaml
requirements:
  - type: money
    amount: 500
    currency: "gems"
    economy: "CoinsEngine"
```

:::warning
此条件仅检查余额。若需在成功时实际扣除金钱，请在 `success` 区块中使用 `currency-withdraw` 动作。
:::

### item（物品）

检查玩家背包中是否拥有特定物品。

```yaml
requirements:
  - type: item
    item:
      material: DIAMOND
      amount: 5
```

| 键名 | 类型 | 说明 |
|------|------|------|
| item.material | String | Minecraft 物料名称 |
| item.amount | Number | 所需最低数量 |

也可检查具有特定属性的物品：

```yaml
requirements:
  - type: item
    item:
      material: DIAMOND_SWORD
      name: "&6传奇之剑"
      amount: 1
```

### luckperm（LuckPerms 组）

检查玩家是否属于特定 LuckPerms 组。需安装 [LuckPerms](https://luckperms.net/)。

```yaml
requirements:
  - type: luckperm
    group: vip
```

| 键名 | 类型 | 说明 |
|------|------|------|
| group | String | LuckPerms 组名称 |

### job（职业）

检查玩家是否达到特定职业等级。需安装 [Jobs Reborn](https://www.spigotmc.org/resources/jobs-reborn.4216/)。

```yaml
requirements:
  - type: job
    job: Miner
    level: 10
```

| 键名 | 类型 | 说明 |
|------|------|------|
| job | String | 职业名称 |
| level | Number | 所需最低等级 |

### regex（正则表达式）

检查值是否匹配正则表达式模式。常与 `INPUT` 按钮类型配合使用以验证玩家输入。

```yaml
requirements:
  - type: regex
    input: "%input%"
    regex: "^[1-9][0-9]?$|^64$"
```

| 键名 | 类型 | 说明 |
|------|------|------|
| input | String | 要测试正则表达式的值 |
| regex | String | 正则表达式模式 |

示例：验证 1-64 之间的数字输入：

```yaml
items:
  set-amount:
    type: INPUT
    slot: 13
    input-message:
      - "&e请输入购买数量："
      - "&7(1-64)"
    input-cancel: "cancel"
    item:
      material: HOPPER
      name: "&6&l设置数量"
    click-requirement:
      requirements:
        - type: regex
          input: "%input%"
          regex: "^[1-9][0-9]?$|^64$"
          deny:
            - type: message
              messages:
                - "&c请输入 1 到 64 之间的数字"
      success:
        - type: data
          key: "amount"
          value: "%input%"
        - type: refresh
```

### player-name（玩家名称）

检查玩家名称。

```yaml
requirements:
  - type: player-name
    name: "Notch"
```

| 键名 | 类型 | 说明 |
|------|------|------|
| name | String | 要检查的玩家名称 |

### cuboid（立方体区域）

检查玩家是否位于立方体区域内。需定义世界名称及立方体两个对角点的坐标。

```yaml
requirements:
  - type: cuboid
    cuboids:
      - "world,0,60,0,100,120,100"
```

格式：`<世界名称>,<x1>,<y1>,<z1>,<x2>,<y2>,<z2>`

| 键名 | 类型 | 说明 |
|------|------|------|
| cuboids | List of Strings | 立方体区域列表，格式为 `world,x1,y1,z1,x2,y2,z2` |

可定义多个立方体区域。玩家位于任一区域内即视为条件满足：

```yaml
requirements:
  - type: cuboid
    cuboids:
      - "world,0,60,0,100,120,100"
      - "world,-50,40,-50,50,100,50"
      - "world_nether,10,30,10,50,80,50"
```

### and（逻辑与）

:::warning 仅限 zMenu+
此功能仅在 [zMenu+](../zmenu-plus) 中可用。
:::

组合多个条件，要求**全部**满足。可用于将条件分组，并与 `or` 块嵌套实现复杂逻辑。

```yaml
requirements:
  - type: and
    requirements:
      - type: permission
        permission: "server.vip"
      - type: placeholder
        value: "%player_level%"
        compare: ">="
        number: 10
```

| 键名 | 类型 | 说明 |
|------|------|------|
| requirements | List | 必须全部通过的子条件列表 |

### or（逻辑或）

:::warning 仅限 zMenu+
此功能仅在 [zMenu+](../zmenu-plus) 中可用。
:::

组合多个条件，要求**至少满足指定数量**。

```yaml
requirements:
  - type: or
    minimum: 1
    random: false
    requirements:
      - type: permission
        permission: "server.vip"
      - type: permission
        permission: "server.premium"
```

| 键名 | 类型 | 说明 |
|------|------|------|
| minimum | Number | 必须通过的最少条件数量 |
| random | Boolean | 是否随机遍历条件列表 |
| requirements | List | 子条件列表 |

**组合 and + or**

可组合 `and` 和 `or` 创建复杂条件。例如：玩家必须同时拥有权限 A 和 B，且拥有权限 C 或 D：

```yaml
name: "&8And/Or 测试"
size: 54
items:
  test:
    slot: 22
    view-requirement:
      requirements:
        - type: and
          requirements:
            - type: permission
              permission: zmenu.test
            - type: permission
              permission: zmenu.test2
        - type: or
          minimum: 1
          random: true
          requirements:
            - type: permission
              permission: zmenu.test3
            - type: permission
              permission: zmenu.test4
    item:
      material: PAPER
      name: "&aAnd/Or 测试"
    else:
      item:
        material: PAPER
        name: "&cAnd/Or 测试"
```

此例中，玩家必须同时拥有 `zmenu.test` 和 `zmenu.test2`（`and` 块），且拥有 `zmenu.test3` 或 `zmenu.test4`（`or` 块，`minimum: 1`）。

---

## Success 与 Deny 动作

### deny（拒绝动作）

条件不满足时执行的动作。在每个单独条件内部定义。

```yaml
requirements:
  - type: permission
    permission: "server.vip"
    deny:
      - type: message
        messages:
          - "&c你需要 VIP 等级！"
      - type: sound
        sound: ENTITY_VILLAGER_NO
```

### success（成功动作）

**所有**条件满足时执行的动作。在条件块级别定义（`requirements` 列表外部）。

```yaml
click-requirement:
  requirements:
    - type: placeholder
      value: "%vault_eco_balance%"
      compare: ">="
      number: 500
      deny:
        - type: message
          messages:
            - "&c你需要 $500！"
  success:
    - type: currency-withdraw
      amount: 500
    - type: console-command
      commands:
        - "give %player% diamond_sword 1"
    - type: message
      messages:
      - "&a购买成功！"
    - type: sound
      sound: ENTITY_PLAYER_LEVELUP
```

所有 [动作类型](./buttons/actions) 均可用于 `deny` 和 `success` 区块。

---

## 多条件组合

可组合多个条件。**所有**条件必须同时满足才能通过。任一条件失败即执行其对应的 `deny` 动作。

```yaml
click-requirement:
  requirements:
    - type: permission
      permission: "server.vip"
      deny:
        - type: message
          messages:
            - "&c你需要 VIP 等级！"
    - type: placeholder
      value: "%vault_eco_balance%"
      compare: ">="
      number: 1000
      deny:
        - type: message
          messages:
            - "&c你需要 $1000！"
    - type: item
      item:
        material: DIAMOND
        amount: 5
      deny:
        - type: message
          messages:
            - "&c你需要 5 颗钻石！"
  success:
    - type: message
      messages:
        - "&a所有条件已满足！"
```

此例中，玩家必须同时拥有 VIP 权限、$1000 余额和 5 颗钻石。任一条件失败即显示对应拒绝消息。

---

## else 系统

`else` 块与 `view-requirement` 配合使用，可在条件不满足时显示替代按钮，而非完全隐藏按钮。

### 基础 else 用法

```yaml
items:
  vip-button:
    slot: 0
    view-requirement:
      requirements:
        - type: permission
          permission: "vip.access"
    item:
      material: DIAMOND_BLOCK
      name: "&b&lVIP 专属内容"
    else:
      item:
        material: COAL_BLOCK
        name: "&7&l已锁定"
        lore:
          - "&c需要 VIP 等级"
```

玩家拥有 `vip.access` 权限时显示钻石块，否则显示带有"已锁定"提示的煤炭块。

### 嵌套 else 链

可链式使用多个 `else` 块创建具有多种视觉状态的状态机。每个 `else` 可拥有自己的 `view-requirement`：

```yaml
items:
  reward:
    slot: 0

    # 状态 1：已领取
    view-requirement:
      requirements:
        - type: placeholder
          placeholder: "%zmenu_player_value_claimed%"
          value: "true"
          action: EQUAL_TO
    item:
      material: MINECART
      name: "&a&l奖励已领取"
      lore:
        - "&7你已领取此奖励"

    else:
      # 状态 2：等级不足
      view-requirement:
        requirements:
          - type: placeholder
            placeholder: "%player_level%"
            value: "10"
            action: LOWER
      item:
        material: BARRIER
        name: "&c&l已锁定"
        lore:
          - "&7需要等级 10"

      else:
        # 状态 3：可领取
        item:
          material: CHEST
          name: "&e&l领取奖励"
          lore:
            - "&7点击领取！"
        click-requirement:
          requirements:
            - type: placeholder
              placeholder: "%player_level%"
              value: "10"
              action: SUPERIOR_OR_EQUAL
          success:
            - type: data
              action: SET
              key: "claimed"
              value: "true"
            - type: console-command
              commands:
                - "give %player% diamond 10"
            - type: sound
              sound: ENTITY_PLAYER_LEVELUP
```

系统从上至下依次评估每个 `view-requirement`：
1. 若首个条件匹配，显示该按钮状态
2. 否则进入 `else` 块并检查其条件
3. 依此类推直至链尾
4. 最终无 `view-requirement` 的 `else` 作为默认状态

---

## 命名点击条件

在 `click-requirement` 中，可定义带名称的条件组并指定特定点击类型。这允许为不同点击动作设置不同条件。

```yaml
click-requirement:
  purchase:
    clicks:
      - ALL
    requirements:
      - type: placeholder
        value: "%vault_eco_balance%"
        compare: ">="
        number: 100
        deny:
          - type: message
            messages:
              - "&c你需要 $100！"
    success:
      - type: currency-withdraw
        amount: 100
      - type: message
        messages:
          - "&a购买成功！"
```

| 键名 | 说明 |
|------|------|
| clicks | 此条件组响应的点击类型列表 |
| requirements | 要检查的条件 |
| success | 所有条件通过时执行的动作 |

可用点击类型：`ALL`、`LEFT`、`RIGHT`、`SHIFT_LEFT`、`SHIFT_RIGHT`、`MIDDLE`、`DROP`、`CONTROL_DROP`

**不同点击执行不同动作的示例：**

```yaml
click-requirement:
  buy-one:
    clicks:
      - LEFT
    requirements:
      - type: placeholder
        value: "%vault_eco_balance%"
        compare: ">="
        number: 100
        deny:
          - type: message
            messages:
            - "&c你需要 $100！"
    success:
      - type: currency-withdraw
        amount: 100
      - type: console-command
        commands:
          - "give %player% diamond 1"
  buy-stack:
    clicks:
      - RIGHT
    requirements:
      - type: placeholder
        value: "%vault_eco_balance%"
        compare: ">="
        number: 6400
        deny:
          - type: message
            messages:
            - "&c你需要 $6400！"
    success:
      - type: currency-withdraw
        amount: 6400
      - type: console-command
        commands:
          - "give %player% diamond 64"
```

---

## 完整示例

### 商店物品价格检查

仅当玩家余额充足时才可购买的物品：

```yaml
items:
  shop-sword:
    slot: 13
    type: NONE
    item:
      material: DIAMOND_SWORD
      name: "&6&l钻石剑"
      lore:
        - "&8&m─────────────────"
        - ""
        - "&7价格: &a$500"
        - "&7你的余额: &e$%vault_eco_balance%"
        - ""
        - "&8&m─────────────────"
        - ""
        - "&e▸ 点击购买"
      glow: true
    click-requirement:
      requirements:
        - type: placeholder
          value: "%vault_eco_balance%"
          compare: ">="
          number: 500
          deny:
            - type: message
              messages:
                - "&c你需要 $500 才能购买此物品！"
            - type: sound
              sound: ENTITY_VILLAGER_NO
      success:
        - type: currency-withdraw
          amount: 500
        - type: console-command
          commands:
            - "give %player% diamond_sword 1"
        - type: message
          messages:
            - "&a购买成功！"
        - type: sound
          sound: ENTITY_PLAYER_LEVELUP
        - type: close
```

### 每日奖励冷却系统

使用玩家数据和时间戳实现 24 小时冷却的按钮：

```yaml
items:
  daily-reward:
    slot: 22
    item:
      material: CHEST
      name: "&e&l每日奖励"
      lore:
        - "&7领取你的每日奖励！"
    click-requirement:
      requirements:
        - type: placeholder
          value: "%zmenu_math_%zmenu_time_unix_timestamp%-%zmenu_player_value_last_daily%%"
          compare: ">="
          number: 86400
          deny:
            - type: message
              messages:
                - "&c你今天已领取过奖励！"
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
            - "&a每日奖励已领取！"
            - "&7连续天数: &e%zmenu_player_value_daily_streak%"
        - type: sound
          sound: ENTITY_PLAYER_LEVELUP
```

### VIP 专属菜单

仅 VIP 玩家可打开的库存：

```yaml
name: "&6&lVIP 休息室"
size: 54

view-requirement:
  requirements:
    - type: permission
      permission: "server.vip"
      deny:
        - type: message
          messages:
            - "&c此菜单仅限 VIP 成员访问！"
        - type: sound
          sound: ENTITY_VILLAGER_NO

open-actions:
  - type: sound
    sound: BLOCK_CHEST_OPEN
  - type: message
    messages:
      - "&a欢迎来到 VIP 休息室！"

items:
  # ... 你的 VIP 专属按钮
```

### 基于等级的按钮（else 链）

根据玩家等级显示不同内容的按钮：

```yaml
items:
  rank-display:
    slot: 4

    # 钻石等级
    view-requirement:
      requirements:
        - type: luckperm
          group: diamond
    item:
      material: DIAMOND_BLOCK
      name: "&b&l钻石等级"
      lore:
        - "&7你的等级: &b钻石"

    else:
      # 黄金等级
      view-requirement:
        requirements:
          - type: luckperm
            group: gold
      item:
        material: GOLD_BLOCK
        name: "&6&l黄金等级"
        lore:
          - "&7你的等级: &6黄金"

      else:
        # 默认（无等级）
        item:
          material: STONE
          name: "&7&l无等级"
          lore:
            - "&7你尚未拥有等级"
            - "&e访问我们的商店获取等级！"
```

### 玩家数据消费系统

检查存储在玩家数据中的自定义货币：

```yaml
items:
  spend-coins:
    slot: 15
    item:
      material: DIAMOND
      name: "&b&l购买钻石"
      lore:
        - "&7价格: &e50 金币"
        - "&7你的金币: &f%zmenu_player_value_coins%"
    click-requirement:
      requirements:
        - type: placeholder
          value: "%zmenu_player_value_coins%"
          compare: ">="
          number: 50
          deny:
            - type: message
              messages:
                - "&c你需要 50 金币！"
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

### 初始化默认值

使用隐藏按钮配合 `view-requirement` 在玩家首次访问时设置默认玩家数据：

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

此隐形按钮检查玩家是否已完成初始化。若未初始化，则设置默认值并刷新库存。

---

## 最佳实践

✅ **条件顺序优化**  
将最可能失败或计算成本最低的条件放在前面，提高性能

✅ **提供明确反馈**  
始终为条件失败提供清晰的 `deny` 消息，告知玩家如何满足条件

✅ **避免过度嵌套**  
复杂逻辑优先使用 `and`/`or` 组合，而非过深的 `else` 链

✅ **测试边界情况**  
特别注意数值比较的边界值（如 `>=` 与 `>` 的区别）

✅ **文档化复杂条件**  
在 YAML 中添加注释说明复杂条件的业务逻辑

❌ **避免**  
- 条件过多导致难以维护
- 忽略 `deny` 动作导致玩家困惑
- 在高频触发场景使用高成本条件（如数据库查询）

---

## 后续步骤

- 了解 [按钮动作类型](./buttons/actions) 完整列表
- 学习 [玩家数据系统](./player-data) 实现个性化条件
- 探索 [模板系统](./patterns) 复用条件逻辑

