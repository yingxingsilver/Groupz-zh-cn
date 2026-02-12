---
sidebar_position: 1
title: 按钮配置
description: zMenu 中按钮配置的完整参考指南
---

# 按钮配置（Button Configuration）



## 按钮配置

按钮是库存菜单中的交互元素。本文档详述所有可用的按钮配置选项。

---

## 基础结构

```yaml
items:
  my-button:
    slot: 0
    item:
      material: DIAMOND
      name: "&b我的按钮"
    actions:
      - type: message
        messages:
          - "&a你点击了按钮！"
```

---

## 配置选项

### slot（槽位）

按钮在库存中的位置。槽位从 0 开始编号。

```yaml
items:
  button:
    slot: 13
```

![库存槽位示意图](/img/slots.png)

### slots（多槽位）

为同一按钮指定多个槽位。

```yaml
items:
  border:
    slots:
      - 0
      - 1
      - 2
      - 8
    item:
      material: BLACK_STAINED_GLASS_PANE
```

**范围语法：**

```yaml
items:
  row:
    slots:
      - 0-8      # 槽位 0 至 8
      - 45-53    # 槽位 45 至 53
```

### type（按钮类型）

按钮类型决定特殊行为。默认值为 `NONE`。

```yaml
items:
  back:
    slot: 45
    type: BACK
    item:
      material: ARROW
      name: "&c&l返回"
```

**可用类型：**

| 类型 | 说明 |
|------|------|
| `NONE` | 默认按钮，无特殊行为 |
| `INVENTORY` | 打开另一个库存菜单 |
| `BACK` | 返回上一个库存菜单 |
| `HOME` | 返回历史记录中的第一个库存 |
| `NEXT` | 跳转到下一页 |
| `PREVIOUS` | 跳转到上一页 |
| `JUMP` | 跳转到指定页码 |
| `MAIN_MENU` | 打开主菜单 |
| `SWITCH` | 根据条件显示不同物品 |
| `PAGINATION` | 显示分页内容（仅 zMenu+） |
| `DYNAMIC_PAGINATION` | 动态分页（内容可变，仅 zMenu+） |
| `INPUT` | 玩家输入按钮（仅 zMenu+） |

详见 [按钮类型](./types/none) 获取每种类型的详细文档。

### item（物品）

按钮的视觉外观。详见 [物品配置](../items/item) 了解所有可用选项。

```yaml
items:
  button:
    slot: 0
    item:
      material: DIAMOND
      name: "&b&l钻石"
      lore:
        - "&7一颗闪亮的钻石"
```

### actions（动作）

点击按钮时执行的动作。

```yaml
items:
  button:
    slot: 0
    item:
      material: EMERALD
    actions:
      - type: message
        messages:
          - "&a你点击了！"
      - type: sound
        sound: UI_BUTTON_CLICK
```

详见 [动作类型](./actions) 了解所有可用动作。

### sound（声音）

点击按钮时播放声音（简写形式）。

```yaml
items:
  button:
    slot: 0
    sound: UI_BUTTON_CLICK
    item:
      material: DIAMOND
```

如需更精细控制，请使用 `sound` 动作：

```yaml
actions:
  - type: sound
    sound: UI_BUTTON_CLICK
    pitch: 1.5
    volume: 0.5
```

### messages（消息）

点击时发送消息（简写形式）。

```yaml
items:
  button:
    slot: 0
    messages:
      - "&a你好！"
      - "&7欢迎来到服务器。"
    item:
      material: BOOK
```

### close-inventory（关闭库存）

点击按钮时关闭库存菜单。

```yaml
items:
  close:
    slot: 49
    close-inventory: true
    item:
      material: BARRIER
      name: "&c&l关闭"
```

### refresh-on-click（点击刷新）

点击后刷新整个库存菜单。

```yaml
items:
  refresh:
    slot: 0
    refresh-on-click: true
    item:
      material: COMPASS
      name: "&e&l刷新"
```

### update-on-click（点击更新）

仅更新当前按钮（而非整个库存）。

```yaml
items:
  toggle:
    slot: 0
    update-on-click: true
    item:
      material: LEVER
      name: "&e&l切换"
```

### update（自动更新）

为按钮启用自动更新（刷新占位符和动态内容）。

```yaml
items:
  dynamic-info:
    slot: 0
    update: true
    item:
      material: PAPER
      name: "&e&l余额: &a$%vault_eco_balance%"
```

### update-master-button（更新主按钮）

更新此按钮时同时更新主按钮。适用于 `SWITCH` 按钮。

```yaml
items:
  child-button:
    slot: 0
    update-master-button: true
    item:
      material: DIAMOND
```

### use-cache（启用缓存）

为按钮启用缓存以提升性能。

```yaml
items:
  cached-button:
    slot: 0
    use-cache: true
    item:
      material: DIAMOND
      name: "&b缓存物品"
```

启用后，按钮物品将被缓存并复用，而非每次重建。

### is-permanent（永久显示）

在分页库存的所有页面上显示此按钮。

```yaml
items:
  navigation:
    slot: 49
    is-permanent: true
    item:
      material: ARROW
      name: "&7导航"
```

### page（指定页码）

仅在特定页面显示按钮。

```yaml
items:
  page1-only:
    slot: 0
    page: 1
    item:
      material: DIAMOND
      name: "&b第 1 页物品"
```

**范围格式：**

```yaml
items:
  pages-1-to-3:
    slot: 0
    page: 1-3
    item:
      material: EMERALD
      name: "&a第 1-3 页"
```

### else（替代状态）

当 `view-requirement` 条件不满足时显示的替代按钮。

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

### open-link（打开链接）

:::warning 已弃用
此配置已弃用。请改用 MiniMessage 格式打开链接：

```yaml
item:
  name: "<click:open_url:'https://discord.gg/myserver'>&9&l点击加入 Discord</click>"
```
:::

打开 URL 或提示玩家加入 Discord 服务器。

```yaml
items:
  discord:
    slot: 0
    open-link: "https://discord.gg/myserver"
    item:
      material: PLAYER_HEAD
      url: "discord_head_texture"
      name: "&9&lDiscord"
      lore:
        - "&7点击加入我们的 Discord！"
```

### permission（权限）

需要特定权限才能看到按钮。无权限时按钮将隐藏。

```yaml
items:
  admin-button:
    slot: 0
    permission: "server.admin"
    item:
      material: COMMAND_BLOCK
      name: "&c&l管理面板"
```

也可指定权限列表（需同时满足所有权限）：

```yaml
items:
  special-button:
    slot: 0
    permission:
      - "server.vip"
      - "server.premium"
    item:
      material: DIAMOND_BLOCK
      name: "&b&lVIP 专属"
```

### or-permission（或权限）

满足列表中任一权限即可看到按钮。

```yaml
items:
  staff-button:
    slot: 0
    or-permission:
      - "server.admin"
      - "server.moderator"
      - "server.helper"
    item:
      material: BOOK
      name: "&6&l管理菜单"
```

### placeholder（占位符条件）

通过检查占位符值决定按钮是否显示。

```yaml
items:
  level-button:
    slot: 0
    placeholder: "%player_level%"
    action: ">="
    value: "10"
    item:
      material: EXPERIENCE_BOTTLE
      name: "&a&l等级 10+ 奖励"
```

**可用操作符：**

| 操作符 | 说明 |
|--------|------|
| `==` | 等于 |
| `!=` | 不等于 |
| `>=` | 大于或等于 |
| `>` | 大于 |
| `<=` | 小于或等于 |
| `<` | 小于 |
| `equals_string` | 字符串精确匹配 |
| `equalsIgnoreCase` | 忽略大小写的字符串匹配 |

### commands（玩家命令）

:::tip 建议
建议使用 [动作系统](./actions) 替代 `commands`，以获得更灵活的功能。
:::

点击按钮时以玩家身份执行命令。

```yaml
items:
  warp-button:
    slot: 0
    commands:
      - "warp spawn"
      - "say 我传送了！"
    item:
      material: ENDER_PEARL
      name: "&a&l传送到出生点"
```

也可为特定点击类型指定命令：

```yaml
items:
  multi-command:
    slot: 0
    left-commands:
      - "warp spawn"
    right-commands:
      - "warp hub"
    item:
      material: COMPASS
```

### console-commands（控制台命令）

:::tip 建议
建议使用 `type: console-command` 动作替代，以获得更灵活的功能。
:::

点击按钮时以控制台身份执行命令。

```yaml
items:
  reward-button:
    slot: 0
    console-commands:
      - "give %player% diamond 64"
      - "eco give %player% 1000"
    item:
      material: CHEST
      name: "&6&l领取奖励"
```

也可基于权限执行不同命令：

```yaml
items:
  vip-reward:
    slot: 0
    console-permission: "server.vip"
    console-permission-commands:
      - "give %player% diamond_block 16"
    item:
      material: DIAMOND_BLOCK
      name: "&b&lVIP 奖励"
```

### error-item（错误状态物品）

:::warning 需要 zMenu+
此功能需要 [zMenu+](../../zmenu-plus) 才能使用。
:::

条件不满足时显示的替代物品（例如余额不足）。

```yaml
items:
  purchase-button:
    slot: 0
    click-requirement:
      requirements:
        - type: placeholder
          value: "%vault_eco_balance%"
          compare: ">="
          number: 1000
    item:
      material: DIAMOND
      name: "&b&l购买 - $1000"
    error-item:
      material: BARRIER
      name: "&c&l余额不足"
      lore:
        - "&7你需要 $1000"
        - "&7当前余额: &c$%vault_eco_balance%"
```

### player-head（玩家头颅）

显示当前玩家的头颅。

```yaml
items:
  profile:
    slot: 0
    player-head: "%player%"
    item:
      material: PLAYER_HEAD
      name: "&a&l%player%"
```

### player-inventory（玩家背包）

将物品直接放入玩家背包而非显示在菜单中。

:::warning 需要 zMenu+
此功能需要 [zMenu+](../../zmenu-plus) 才能使用。
:::

```yaml
items:
  reward-item:
    slot: 0
    player-inventory: true
    item:
      material: DIAMOND_SWORD
      name: "&6&l奖励之剑"
      enchantments:
        - type: SHARPNESS
          level: 5
```

当 `player-inventory` 为 `true` 时，物品将直接放入玩家背包的对应槽位，而非显示在 GUI 中。

---

## 条件系统

### view-requirement（可见性条件）

控制按钮是否显示。条件不满足时按钮将完全隐藏。

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

### click-requirement（点击条件）

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
                - "&c你需要 $100 才能购买！"
            - type: sound
              sound: ENTITY_VILLAGER_NO
      success:
        - type: message
          messages:
            - "&a购买成功！"
        - type: console-command
          commands:
            - "eco take %player% 100"
    item:
      material: GOLD_INGOT
      name: "&e&l购买 - $100"
```

### 条件类型

完整条件类型指南请参阅 [条件系统](../requirements) 页面。

| 类型 | 说明 |
|------|------|
| `permission` | 检查玩家权限 |
| `placeholder` | 比较占位符值 |
| `money` | 检查玩家余额 |
| `item` | 检查玩家背包物品 |
| `job` | 检查 Jobs Reborn 职业等级 |
| `luckperm` | 检查 LuckPerms 组 |
| `regex` | 正则表达式匹配 |
| `player-name` | 检查玩家名称 |
| `cuboid` | 检查玩家是否在区域内 |
| `and` | 所有子条件必须满足（仅 zMenu+） |
| `or` | 满足最少数量的子条件（仅 zMenu+） |

**权限条件示例：**
```yaml
requirements:
  - type: permission
    permission: "server.admin"
```

**占位符条件示例：**
```yaml
requirements:
  - type: placeholder
    value: "%player_level%"
    compare: ">="
    number: 10
```
可用比较运算符：`==`、`!=`、`>=`、`>`、`<=`、`<`、`equals_string`、`equalsIgnoreCase`

**金钱条件示例：**
```yaml
requirements:
  - type: money
    amount: 1000
```

**物品条件示例：**
```yaml
requirements:
  - type: item
    item:
      material: DIAMOND
      amount: 5
```

---

## 点击类型

可为不同点击类型指定不同动作：

```yaml
items:
  multi-click:
    slot: 0
    item:
      material: CHEST
      name: "&e&l多功能按钮"
      lore:
        - "&7左键：打开商店"
        - "&7右键：查看余额"
        - "&7Shift+左键：帮助"
    clicks:
      LEFT:
        actions:
          - type: inventory
            inventory: "shop"
      RIGHT:
        actions:
          - type: message
            messages:
              - "&7你的余额: &a$%vault_eco_balance%"
      SHIFT_LEFT:
        actions:
          - type: message
            messages:
              - "&e这是一个多功能按钮！"
```

**可用点击类型：**
- `LEFT` - 左键点击
- `RIGHT` - 右键点击
- `SHIFT_LEFT` - Shift + 左键
- `SHIFT_RIGHT` - Shift + 右键
- `MIDDLE` - 中键点击（滚轮）
- `DROP` - 按 Q 丢弃
- `CONTROL_DROP` - Ctrl + Q

---

## 完整示例

```yaml
items:
  shop-item:
    slot: 13
    type: NONE

    item:
      material: DIAMOND_SWORD
      name: "&6&l钻石剑"
      lore:
        - "&8&m─────────────────"
        - ""
        - "&7一把强力的剑！"
        - ""
        - "&7价格: &a$500"
        - "&7你的余额: &e$%vault_eco_balance%"
        - ""
        - "&8&m─────────────────"
        - ""
        - "&e▸ 点击购买"
      enchantments:
        - type: SHARPNESS
          level: 5
      flags:
        - HIDE_ENCHANTS
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
                - "&c你需要 $500 才能购买！"
            - type: sound
              sound: ENTITY_VILLAGER_NO
      success:
        - type: console-command
          commands:
            - "eco take %player% 500"
            - "give %player% diamond_sword{Enchantments:[{id:sharpness,lvl:5}]} 1"
        - type: message
          messages:
            - "&a购买成功！"
            - "&7你购买了一把 &6钻石剑 &7！"
        - type: sound
          sound: ENTITY_PLAYER_LEVELUP
        - type: close
```

---

## 最佳实践

✅ **使用有意义的按钮名称** - 便于后期维护  
✅ **按功能分组命名** - 采用描述性命名规范  
✅ **提供反馈** - 使用声音和消息告知玩家操作结果  
✅ **合理使用条件** - 适当控制访问权限  
✅ **使用 `else` 显示锁定状态** - 提升用户体验  
✅ **添加 lore 提示** - 说明按钮功能  

❌ **避免**  
- 按钮命名模糊不清
- 忽略用户反馈（无消息/声音）
- 过度复杂的嵌套条件
- 未处理边界情况（如余额不足）

---

## 后续步骤

- 了解每种 [按钮类型](./types/none) 的详细用法
- 探索所有可用的 [动作类型](./actions)
- 创建可复用的 [模板（Patterns）](../patterns)