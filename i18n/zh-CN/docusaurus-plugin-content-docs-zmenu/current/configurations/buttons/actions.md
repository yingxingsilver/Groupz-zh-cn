---
sidebar_position: 3
title: 动作
description: 动作是在玩家与按钮交互时执行的指令
---

# 动作（Actions）

动作是在玩家与按钮交互时执行的指令。zMenu 提供 28+ 种动作类型，涵盖消息、声音、命令、传送、数据操作等多种用途。

## 基础用法

```yaml
items:
  my-button:
    slot: 0
    item:
      material: DIAMOND
    actions:
      - type: message
        messages:
          - "&a你好，%player%！"
      - type: sound
        sound: UI_BUTTON_CLICK
```

---

## 消息类动作

### message（发送消息）

向玩家发送消息。

```yaml
- type: message
  messages:
    - "&a第一行"
    - "&b第二行"
  minimessage: true  # 可选：启用 MiniMessage 格式
```

### messages

`message` 的别名。

```yaml
- type: messages
  messages:
    - "&a多行文本"
    - "&b第二行"
```

### message-to（定向消息）

向指定玩家发送消息。

```yaml
- type: message-to
  player: "Notch"
  messages:
    - "&a来自 %player% 的问候！"
```

### broadcast（广播）

向所有在线玩家发送消息。

```yaml
- type: broadcast
  messages:
    - "&6[公告] &f%player% 购买了一件稀有物品！"
```

### action-bar（动作栏）

在屏幕底部显示动作栏消息。

```yaml
- type: action-bar
  message: "&a欢迎光临商店！"
```

### title（标题）

显示标题和副标题。

```yaml
- type: title
  title: "&6&l欢迎！"
  subtitle: "&7祝您购物愉快"
  fadeIn: 20      # 淡入时长（刻）
  stay: 60        # 停留时长（刻）
  fadeOut: 20     # 淡出时长（刻）
```

### toast（吐司通知）

显示成就式弹窗通知（Minecraft 1.12+）。

```yaml
- type: toast
  icon: DIAMOND
  message: "&a成就已解锁！"
  frame: TASK  # 可选值：TASK（任务）、GOAL（目标）、CHALLENGE（挑战）
```

---

## 声音类动作

### sound（播放声音）

向玩家播放声音。

```yaml
- type: sound
  sound: ENTITY_EXPERIENCE_ORB_PICKUP
  pitch: 1.0    # 音调（0.5 - 2.0）
  volume: 1.0   # 音量（影响传播距离）
  category: MASTER  # 可选：声音类别（如 MASTER, MUSIC, BLOCK 等）
```

**常用声音：**
- `UI_BUTTON_CLICK` - 按钮点击
- `ENTITY_EXPERIENCE_ORB_PICKUP` - 经验球拾取
- `ENTITY_PLAYER_LEVELUP` - 玩家升级
- `ENTITY_VILLAGER_YES` / `ENTITY_VILLAGER_NO` - 村民同意/拒绝
- `BLOCK_NOTE_BLOCK_PLING` - 音符盒
- `BLOCK_CHEST_OPEN` / `BLOCK_CHEST_CLOSE` - 箱子开关

### broadcast-sound（全局播放）

向所有在线玩家播放声音。

```yaml
- type: broadcast-sound
  sound: ENTITY_ENDER_DRAGON_DEATH
  pitch: 1.0
  volume: 1.0
```

---

## 命令类动作

### player-command（玩家命令）

以玩家身份执行命令。

```yaml
- type: player-command
  commands:
    - "spawn"
    - "kit starter"
```

### player-command-as-op（OP 玩家命令）

以拥有 OP 权限的玩家身份执行命令。

```yaml
- type: player-command-as-op
  commands:
    - "gamemode creative"
```

:::warning
谨慎使用！此操作会临时赋予玩家 OP 级别权限。
:::

### console-command（控制台命令）

以服务器控制台身份执行命令。

```yaml
- type: console-command
  commands:
    - "give %player% diamond 64"
    - "eco give %player% 1000"
```

### player-chat（强制聊天）

强制玩家发送聊天消息。

```yaml
- type: player-chat
  messages:
    - "大家好！"
```

### random-player-command（随机玩家命令）【zMenu+】

:::info zMenu+
此动作需要安装 [zMenu+](https://minecraft-music-inventory.com/resources/zmenu.music.331)。
:::

从列表中随机选择命令（不重复）以玩家身份执行。

```yaml
- type: random_player_command
  commands:
    - "say 我抽到了选项 1！"
    - "say 我抽到了选项 2！"
    - "say 我抽到了选项 3！"
    - "say 我抽到了选项 4！"
  amount: 1               # 随机执行的命令数量（默认：1）
  command-in-chat: false  # 可选：是否通过聊天发送（默认：false）
```

| 选项 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `commands` | 列表 | 必填 | 可选命令列表 |
| `amount` | 整数 | 1 | 随机执行的命令数量 |
| `command-in-chat` | 布尔 | false | `true` 时通过 `player.chat()` 发送；`false` 时作为命令分发 |

**别名：** `random_player_command`, `random_player_commands`, `random-player-command`, `random-player-commands`

### random-console-command（随机控制台命令）【zMenu+】

:::info zMenu+
此动作需要安装 [zMenu+](https://minecraft-music-inventory.com/resources/zmenu.music.331)。
:::

从列表中随机选择控制台命令（不重复）执行，支持 `%player%` 占位符。

```yaml
- type: random_console_command
  commands:
    - "give %player% diamond 1"
    - "give %player% emerald 1"
    - "give %player% gold_ingot 1"
    - "give %player% iron_ingot 1"
  amount: 2    # 从列表中随机执行 2 条命令
```

| 选项 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `commands` | 列表 | 必填 | 可选控制台命令列表 |
| `amount` | 整数 | 1 | 随机执行的命令数量 |

**别名：** `random_console_command`, `random_console_commands`, `random-console-command`, `random-console-commands`, `random-command`, `random-commands`

---

## 界面类动作

### inventory（打开界面）

打开另一个界面。

```yaml
- type: inventory
  inventory: "shop"
  page: 1          # 可选：指定页码
  arguments:       # 可选：传递参数
    - "arg1"
```

### close（关闭界面）

关闭当前界面。

```yaml
- type: close
```

### back（返回）

返回上一个界面。

```yaml
- type: back
```

### refresh（刷新按钮）

仅刷新当前按钮。

```yaml
- type: refresh
```

### refresh-inventory（刷新整个界面）

重新绘制界面中所有按钮。

```yaml
- type: refresh-inventory
```

---

## 玩家类动作

### teleport（传送）

将玩家传送到指定坐标。

```yaml
- type: teleport
  world: "world"
  x: 0
  y: 100
  z: 0
  yaw: 0       # 可选：水平朝向
  pitch: 0     # 可选：垂直朝向
```

### connect（跨服连接）

将玩家连接到其他服务器（需 BungeeCord/Velocity）。

```yaml
- type: connect
  server: "lobby"
```

---

## 数据类动作

### data（玩家数据操作）

修改玩家数据值（通过 zMenu 内置数据系统）。

```yaml
- type: data
  action: SET       # 操作类型：SET, ADD, SUBTRACT, MULTIPLY, DIVIDE, REMOVE
  key: "coins"
  value: "100"
  math: true        # 可选：启用数学表达式计算
```

**操作类型：**

| 操作 | 说明 | 示例 |
|------|------|------|
| `SET` | 设置为指定值 | `value: "100"` |
| `ADD` | 增加当前值 | `value: "50"` |
| `SUBTRACT` | 减少当前值 | `value: "25"` |
| `MULTIPLY` | 乘以指定值 | `value: "2"` |
| `DIVIDE` | 除以指定值 | `value: "2"` |
| `REMOVE` | 完全移除该键 | - |

**数学表达式示例：**

```yaml
- type: data
  action: ADD
  key: "total"
  value: "%zmenu_player_value_base%*1.5"
  math: true
```

---

## 经济类动作

### currency-deposit（存款）

向玩家账户存入货币。

```yaml
- type: currency-deposit
  amount: 100
  reason: "奖励"    # 可选：存款原因
```

### currency-withdraw（取款）

从玩家账户扣除货币。

```yaml
- type: currency-withdraw
  amount: 50
  reason: "购买"    # 可选：取款原因
```

> 需要安装 Vault 及兼容的经济插件（如 EssentialsX, CMI 等）。

---

## 书籍类动作

### book（打开书籍）

为玩家打开一本预设的成书。

```yaml
- type: book
  author: "服务器"
  title: "&6欢迎手册"
  pages:
    - |
      &6欢迎！

      &7这是欢迎手册的
      第一页内容。
    - |
      &6第二页

      &7更多内容在此。
```

---

## 对话框动作

### dialog（打开对话框）

打开现代化的对话框弹窗，支持表单、确认按钮和交互式组件。

:::warning 环境要求
对话框功能需要 Paper 1.21.4+ 及 [PacketEvents](https://github.com/retrooper/packetevents) 插件。
:::

**基础用法：**

```yaml
- type: dialog
  dialog: "confirmation"
```

**带参数：**

```yaml
- type: dialog
  dialog: "purchase-confirm"
  arguments:
    - "diamond_sword"
    - "500"
```

**来自外部插件：**

```yaml
- type: dialog
  dialog: "custom-dialog"
  plugin: "MyPlugin"
```

| 选项 | 类型 | 说明 |
|------|------|------|
| `dialog` | 字符串 | 对话框文件名（不含 `.yml` 后缀） |
| `plugin` | 字符串 | 使用外部插件提供的对话框时指定插件名 |
| `arguments` | 列表 | 传递给对话框的参数列表 |

完整配置请参阅 [对话框（Dialogues）](../dialogues) 文档。

---

## Discord 动作

### discord（发送 Discord 消息）

通过 Webhook 向 Discord 频道发送消息。

```yaml
- type: discord
  webhook: "https://discord.com/api/webhooks/..."
  content: "%player% 完成了一次购买！"
  username: "商店机器人"    # 可选
  avatar_url: "https://..." # 可选
```

### discord-component（带组件的 Discord 消息）

发送包含按钮/嵌入内容的 Discord 消息。

```yaml
- type: discord-component
  webhook: "https://discord.com/api/webhooks/..."
  embeds:
    - title: "新购买"
      description: "%player% 购买了物品"
      color: "#00FF00"
```

---

## 集成类动作

### luckperm-set（LuckPerms 集成）

修改玩家的 LuckPerms 权限组。

```yaml
- type: luckperm-set
  group: "vip"
  duration: 2592000  # 持续时间（秒，30 天）
```

> 需要安装 [LuckPerms](https://luckperms.net/)。

### shopkeeper（Shopkeepers 集成）

与 Shopkeepers 插件的商人交互。

```yaml
- type: shopkeeper
  name: "shop_name"
```

> 需要安装 [Shopkeepers](https://www.spigotmc.org/resources/1140/)。

---

## 完整示例

### 购买流程（含反馈）

```yaml
items:
  buy-item:
    slot: 13
    item:
      material: DIAMOND_SWORD
      name: "&6&l钻石剑"
      lore:
        - "&7价格: &a$500"
    click-requirement:
      requirements:
        - type: placeholder
          value: "%vault_eco_balance%"
          compare: ">="
          number: 500
          deny:
            - type: message
              messages:
                - "&c余额不足 $500！"
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

### 切换设置（使用数据存储）

```yaml
items:
  toggle:
    slot: 22
    type: SWITCH
    placeholder: "%zmenu_player_value_setting%"
    buttons:
      "on":
        item:
          material: LIME_DYE
          name: "&a&l设置: 开启"
        actions:
          - type: data
            action: SET
            key: "setting"
            value: "off"
          - type: message
            messages:
              - "&7设置已 &c关闭"
          - type: sound
            sound: UI_BUTTON_CLICK
          - type: refresh
      "off":
        item:
          material: GRAY_DYE
          name: "&7&l设置: 关闭"
        actions:
          - type: data
            action: SET
            key: "setting"
            value: "on"
          - type: message
            messages:
              - "&7设置已 &a开启"
          - type: sound
            sound: UI_BUTTON_CLICK
          - type: refresh
```

### 多动作奖励按钮

```yaml
items:
  reward:
    slot: 13
    item:
      material: CHEST
      name: "&e&l每日奖励"
    actions:
      - type: console-command
        commands:
          - "give %player% diamond 5"
      - type: currency-deposit
        amount: 1000
      - type: data
        action: SET
        key: "last_reward"
        value: "%zmenu_time_unix_timestamp%"
      - type: title
        title: "&6&l奖励已领取！"
        subtitle: "&75 颗钻石 + $1000"
      - type: sound
        sound: ENTITY_PLAYER_LEVELUP
      - type: broadcast
        messages:
          - "&6%player% &7领取了每日奖励！"
      - type: close
```

---

## 动作执行顺序

动作按列表顺序依次执行。如需在传送前关闭界面，应将 `close` 置于 `teleport` 之前：

```yaml
actions:
  - type: close
  - type: teleport
    world: "world"
    x: 0
    y: 100
    z: 0
```

---

## 在条件要求中使用动作

动作也可用于 `click-requirement` 的 `deny`（拒绝）和 `success`（成功）区块：

```yaml
click-requirement:
  requirements:
    - type: permission
      permission: "server.vip"
      deny:
        - type: message
          messages:
            - "&c需要 VIP 权限！"
  success:
    - type: message
      messages:
        - "&a权限验证通过！"
```

---

## 快速参考表

| 动作类型 | 说明 |
|----------|------|
| `message` | 向玩家发送消息 |
| `broadcast` | 向全体玩家广播 |
| `action-bar` | 显示动作栏消息 |
| `title` | 显示标题/副标题 |
| `toast` | 显示成就吐司通知 |
| `sound` | 播放声音 |
| `player-command` | 以玩家身份执行命令 |
| `console-command` | 以控制台身份执行命令 |
| `random-player-command` | 随机执行玩家命令（zMenu+） |
| `random-console-command` | 随机执行控制台命令（zMenu+） |
| `inventory` | 打开其他界面 |
| `close` | 关闭当前界面 |
| `back` | 返回上一界面 |
| `refresh` | 刷新当前按钮 |
| `teleport` | 传送玩家 |
| `connect` | 跨服连接 |
| `data` | 操作玩家数据 |
| `currency-deposit` | 存入货币 |
| `currency-withdraw` | 扣除货币 |
| `book` | 打开成书 |
| `dialog` | 打开对话框（需 Paper 1.21.4+） |
| `discord` | 发送 Discord 消息 |
| `luckperm-set` | 修改 LuckPerms 权限组 |

---

## 后续步骤

- 学习 [玩家数据（Player Data）](../player-data) 以持久化存储玩家信息
- 了解 [按钮条件要求（Requirements）](./button#requirements) 实现条件化动作
- 创建带默认动作的 [模式（Patterns）](../patterns)