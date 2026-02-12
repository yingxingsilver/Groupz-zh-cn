---
sidebar_position: 8
title: 对话框
description: 创建包含表单、确认提示和多操作按钮的交互式弹窗
---

# 对话框（Dialogues）


## 对话框

对话框是出现在屏幕上的交互式弹窗，允许玩家与表单、确认提示和操作按钮进行交互。它们提供了超越传统物品栏菜单的现代化 UI 体验。

:::warning 环境要求
对话框功能需要 **Paper 1.21.4+** 以及在服务器上安装 [PacketEvents](https://github.com/retrooper/packetevents) 插件。
:::

## 文件位置

对话框存储在 `plugins/zMenu/dialogs/` 文件夹中。每个 YAML 文件代表一个对话框。

```
plugins/zMenu/dialogs/
├── welcome.yml
├── confirmation.yml
├── server-links.yml
└── feedback-form.yml
```

## 对话框类型

zMenu 支持四种对话框类型：

| 类型 | 说明 |
|------|------|
| `notice` | 简单的信息提示对话框，可包含可选输入字段 |
| `confirmation` | 带“是/否”按钮的确认对话框 |
| `multi_action` | 以网格布局显示多个操作按钮 |
| `server_links` | 以可点击按钮形式展示服务器链接 |

## 基础结构

```yaml
name: "&6&l对话框标题"
external_title: "窗口标题"
type: notice

can-close-with-escape: true
pause: false
after_action: CLOSE

body:
  # 主体内容部分...

inputs:
  # 输入字段...
```

## 配置选项

### `name`（名称）

对话框的内部名称，支持颜色格式。

```yaml
name: "&6&l欢迎对话框"
```

### `external_title`（窗口标题）

对话框标题栏中显示的窗口标题。

```yaml
external_title: "服务器信息"
```

### `type`（类型）

对话框类型。详见 [对话框类型](#对话框类型)。

```yaml
type: notice
```

### `can-close-with-escape`（允许 ESC 关闭）

允许玩家按 Esc 键关闭对话框。

```yaml
can-close-with-escape: true
```

### `pause`（暂停游戏）

打开对话框时暂停游戏（类似单人游戏行为）。

```yaml
pause: false
```

### `after_action`（操作后行为）

执行操作后的行为。

| 值 | 说明 |
|----|------|
| `CLOSE` | 关闭对话框 |
| `PAUSE` | 保持对话框打开 |
| `NONE` | 不执行任何操作 |

```yaml
after_action: CLOSE
```

## 主体内容（Body）

`body` 部分定义对话框中显示的内容。可组合多种内容类型。

### `plain_message`（纯文本）

显示文本消息。

```yaml
body:
  welcome:
    type: plain_message
    messages:
      - "&6&l欢迎来到我们的服务器！"
      - ""
      - "&7服务器地址: &fplay.example.com"
      - "&7Discord: &fdiscord.gg/example"
    width: 400
```

### `item`（物品展示）

展示物品并可选显示提示信息。

```yaml
body:
  featured-item:
    type: item
    item:
      material: DIAMOND_SWORD
      name: "&b&l传奇之剑"
      lore:
        - "&7一把强大的武器"
    show-decoration: true
    show-tooltip: true
    width: 200
    height: 150
```

## 输入字段（Inputs）

对话框可在 `inputs` 部分包含交互式输入字段。

### `dialog_text`（文本输入）

文本输入框。

```yaml
inputs:
  feedback:
    type: dialog_text
    label: "&6分享您的想法："
    width: 400
    max-length: 200
    multiline:
      max-lines: 4
```

| 选项 | 类型 | 说明 |
|------|------|------|
| `label` | 字符串 | 输入框上方显示的标签 |
| `width` | 整数 | 输入框宽度 |
| `max-length` | 整数 | 最大字符数 |
| `multiline.max-lines` | 整数 | 多行输入的行数 |

### `dialog_boolean`（布尔开关）

是/否切换开关。

```yaml
inputs:
  subscribe:
    type: dialog_boolean
    label: "&e是否接收更新通知？"
    text-true: "&a&l是"
    text-false: "&c&l否"
```

### `dialog_single_option`（单选）

从多个选项中单选。

```yaml
inputs:
  gamemode:
    type: dialog_single_option
    label: "&6选择游戏模式："
    options:
      survival:
        id: "survival"
        display: "&2生存模式"
      creative:
        id: "creative"
        display: "&b创造模式"
      adventure:
        id: "adventure"
        display: "&6冒险模式"
```

### `dialog_number_range`（数字范围）

数字滑块选择器。

```yaml
inputs:
  amount:
    type: dialog_number_range
    label: "&e选择数量："
    start: 1
    end: 64
    step: 1
```

## 通知对话框（Notice Dialog）

带可选输入字段的简单信息提示对话框。

```yaml
name: "&6&l服务器规则"
external_title: "规则"
type: notice

can-close-with-escape: true
after_action: CLOSE

body:
  rules:
    type: plain_message
    messages:
      - "&c&l服务器规则"
      - ""
      - "&71. 尊重所有玩家"
      - "&72. 禁止作弊或利用漏洞"
      - "&73. 遵守管理员指令"
      - "&74. 玩得开心！"
    width: 350

inputs:
  accept:
    type: dialog_boolean
    label: "&e我同意以上规则"
    text-true: "&a&l同意"
    text-false: "&c&l拒绝"
```

## 确认对话框（Confirmation Dialog）

带“确认”和“取消”按钮的对话框。

```yaml
name: "&c&l确认购买"
external_title: "确认"
type: confirmation

can-close-with-escape: true
after_action: CLOSE

body:
  message:
    type: plain_message
    messages:
      - "&7您确定要以 &a$500 &7购买"
      - "&6钻石剑 &7吗？"
    width: 300

confirmation:
  yes-text: "&a&l确认购买"
  yes-tooltip: "点击确认"
  yes-width: 200
  no-text: "&c&l取消"
  no-tooltip: "点击取消"
  no-width: 200

yes-actions:
  1:
    success:
      - type: console-command
        commands:
          - "give %player% diamond_sword 1"
      - type: currency-withdraw
        amount: 500
      - type: message
        messages:
          - "&a购买成功！"

no-actions:
  1:
    success:
      - type: message
      messages:
          - "&7购买已取消。"
```

### 确认选项

| 选项 | 说明 |
|------|------|
| `yes-text` | “是”按钮上的文字 |
| `yes-tooltip` | 悬停“是”按钮时的提示 |
| `yes-width` | “是”按钮宽度 |
| `no-text` | “否”按钮上的文字 |
| `no-tooltip` | 悬停“否”按钮时的提示 |
| `no-width` | “否”按钮宽度 |

## 多操作对话框（Multi-Action Dialog）

以网格形式展示多个操作按钮的对话框。

```yaml
name: "&6&l传送菜单"
external_title: "传送"
type: multi_action

can-close-with-escape: true
after_action: CLOSE
number-of-columns: 2

body:
  header:
    type: plain_message
    messages:
      - "&7请选择目的地："
    width: 300

multi-actions:
  spawn:
    text: "&a&l出生点"
    tooltip: "&7传送到出生点"
    width: 150
    actions:
      1:
        success:
          - type: close
          - type: player-command
            commands:
              - "spawn"
          - type: message
            messages:
              - "&a正在传送到出生点..."

  shop:
    text: "&e&l商店"
    tooltip: "&7访问商店"
    width: 150
    actions:
      1:
        success:
          - type: close
          - type: player-command
            commands:
              - "warp shop"

  pvp:
    text: "&c&lPvP 竞技场"
    tooltip: "&7进入 PvP 竞技场"
    width: 150
    actions:
      1:
        success:
          - type: close
          - type: player-command
            commands:
              - "warp pvp"

  home:
    text: "&b&l家园"
    tooltip: "&7传送回家园"
    width: 150
    actions:
      1:
        success:
          - type: close
          - type: player-command
            commands:
              - "home"
```

### 多操作选项

| 选项 | 说明 |
|------|------|
| `number-of-columns` | 每行按钮数量 |
| `multi-actions` | 按钮映射 |
| `text` | 按钮文字 |
| `tooltip` | 按钮提示信息 |
| `width` | 按钮宽度 |
| `actions` | 点击时执行的操作 |

## 服务器链接对话框（Server Links Dialog）

以可点击按钮形式展示服务器链接。

```yaml
name: "&6&l服务器链接"
external_title: "链接"
type: server_links

can-close-with-escape: true
after_action: CLOSE

server-links:
  text: "&6&l实用链接"
  tooltip: "&7点击打开"
  width: 300
  number-of-columns: 2
  actions:
    1:
      success:
        - type: message
          messages:
            - "&a链接已打开！"
```

## 打开对话框

### 通过按钮操作

在物品栏按钮中使用 `dialog` 操作类型：

```yaml
items:
  open-dialog:
    slot: 13
    item:
      material: BOOK
      name: "&6打开对话框"
    actions:
      - type: dialog
        dialog: "welcome"
```

### 带参数打开

向对话框传递参数：

```yaml
actions:
  - type: dialog
    dialog: "confirmation"
    arguments:
      - "diamond_sword"
      - "500"
```

### 从外部插件调用

从其他插件打开对话框：

```yaml
actions:
  - type: dialog
    dialog: "my-dialog"
    plugin: "MyPlugin"
```

## 完整示例

### 反馈表单

```yaml
name: "&6&l反馈表单"
external_title: "分享您的反馈"
type: notice

can-close-with-escape: true
after_action: CLOSE

body:
  header:
    type: plain_message
    messages:
      - "&6&l我们重视您的反馈！"
      - ""
      - "&7请分享您对服务器的看法。"
    width: 400

inputs:
  rating:
    type: dialog_single_option
    label: "&e您如何评价本次体验？"
    options:
      excellent:
        id: "5"
        display: "&a⭐⭐⭐⭐⭐ 优秀"
      good:
        id: "4"
        display: "&e⭐⭐⭐⭐ 良好"
      average:
        id: "3"
        display: "&6⭐⭐⭐ 一般"
      poor:
        id: "2"
        display: "&c⭐⭐ 较差"

  comments:
    type: dialog_text
    label: "&7其他意见："
    width: 400
    max-length: 500
    multiline:
      max-lines: 5

  subscribe:
    type: dialog_boolean
    label: "&e接收新功能更新通知？"
    text-true: "&a是，保持更新！"
    text-false: "&7不了，谢谢"
```

### 套装选择

```yaml
name: "&6&l选择您的套装"
external_title: "套装选择"
type: multi_action

can-close-with-escape: false
after_action: CLOSE
number-of-columns: 3

body:
  info:
    type: plain_message
    messages:
      - "&7请谨慎选择您的初始套装！"
      - "&7此选择后续无法更改。"
    width: 400

multi-actions:
  warrior:
    text: "&c&l战士"
    tooltip: "&7铁制盔甲与剑"
    width: 120
    actions:
      1:
        success:
          - type: console-command
            commands:
              - "kit warrior %player%"
          - type: message
            messages:
              - "&a您选择了战士套装！"

  archer:
    text: "&a&l弓箭手"
    tooltip: "&7皮革盔甲与弓"
    width: 120
    actions:
      1:
        success:
          - type: console-command
            commands:
              - "kit archer %player%"
          - type: message
            messages:
              - "&a您选择了弓箭手套装！"

  mage:
    text: "&5&l法师"
    tooltip: "&7法袍与魔法物品"
    width: 120
    actions:
      1:
        success:
          - type: console-command
            commands:
              - "kit mage %player%"
          - type: message
            messages:
              - "&a您选择了法师套装！"
```

## 最佳实践

- **保持专注**：每个对话框只实现单一功能
- **清晰标注**：使输入字段易于理解
- **提供提示**：为按钮操作添加悬停提示
- **版本验证**：确保服务器运行 Paper 1.21.4+ 且已安装 PacketEvents
- **处理所有结果**：为所有可能的选择定义对应操作
- **选择合适类型**：根据使用场景选用正确的对话框类型

## 后续步骤

- 了解 [按钮操作](./buttons/actions) 配置对话框按钮行为
- 配置 [按钮条件](./buttons/button#requirements) 实现条件化对话框访问
- 查看 [物品栏菜单](./inventories/inventory) 了解传统菜单替代方案
