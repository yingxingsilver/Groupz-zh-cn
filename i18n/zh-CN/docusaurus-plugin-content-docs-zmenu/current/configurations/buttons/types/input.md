---
sidebar_position: 12
title: 输入
description: 通过聊天捕捉玩家对话
---



# 输入按钮（INPUT Button）

```
sidebar_position: 12
title: INPUT 按钮
description: 通过聊天栏捕获玩家文本输入
```

### INPUT 按钮

:::warning 需要 zMenu+
此按钮类型需要安装 [zMenu+](../../../zmenu-plus) 才能正常工作。
:::

`INPUT` 按钮类型允许您捕获玩家的文本输入。当玩家点击该按钮时，界面将自动关闭，并提示玩家在聊天栏中输入文本。输入内容可用于后续动作处理或存储供以后使用。

### 用法示例

```yaml
items:
  input-button:
    type: INPUT
    slot: 13
    input-message: "&e请输入您的昵称："
    input-cancel: "cancel"
    item:
      material: NAME_TAG
      name: "&a&l更改昵称"
```

### 配置参数

| 属性 | 说明 | 是否必需 |
|------|------|----------|
| `type` | 必须为 `INPUT` | 是 |
| `slot` | 按钮所在槽位 | 是 |
| `input-message` | 等待玩家输入时显示的提示消息 | 是 |
| `input-cancel` | 用于取消输入的关键词（默认：`"cancel"`） | 否 |
| `item` | 按钮的视觉外观配置 | 是 |

### 完整示例

#### 昵称修改器

```yaml
size: 27
name: "&8更改昵称"

items:
  change-nick:
    type: INPUT
    slot: 13
    input-message:
      - " &7 "
      - " &e&l请输入您的新昵称 "
      - " &7请在聊天栏中输入您想要的昵称 "
      - " &7输入 &ccancel &7可中止操作 "
      - " &7 "
    input-cancel: "cancel"
    item:
      material: NAME_TAG
      name: " &a&l更改昵称 "
      lore:
        - " &7点击以更改您的显示名称 "
    actions:
      - type: console-command
        commands:
          - "nick %player% %input%"
      - type: message
        messages:
          - " &a您的昵称已更改为 &e%input%"
  
  back:
    type: BACK
    slot: 22
    item:
      material: ARROW
      name: " &c&l返回 "
```

#### 搜索功能

```yaml
items:
  search:
    type: INPUT
    slot: 4
    input-message: " &e请输入搜索关键词："
    input-cancel: "cancel"
    item:
      material: COMPASS
      name: " &e&l搜索 "
      lore:
        - " &7点击进行搜索 "
    actions:
      - type: inventory
        inventory: "search_results"
        arguments:
          query: "%input%"
```

#### 数量输入

```yaml
items:
  set-amount:
    type: INPUT
    slot: 13
    input-message:
      - " &e请输入购买数量："
      - " &7(1-64) "
    input-cancel: "cancel"
    item:
      material: HOPPER
      name: " &6&l设置数量 "
      lore:
        - " &7当前：&e%amount%"
        - " &7点击修改 "
    click-requirement:
      requirements:
        - type: regex
          input: "%input%"
          regex: "^[1-9][0-9]?$|^64$"
          deny:
            - type: message
              messages:
                - " &c请输入 1 到 64 之间的数字 "
      success:
        - type: data
          key: "amount"
          value: "%input%"
        - type: refresh
```

### 占位符

| 占位符 | 说明 |
|--------|------|
| `%input%` | 玩家输入的文本内容 |

### 注意事项

- 玩家需要输入时，界面将自动关闭
- 使用 `input-cancel` 允许玩家通过输入特定关键词中止输入流程
- 可通过 `click-requirement` 配合正则表达式（regex）验证输入内容的有效性

### 后续学习

- 了解 [动作系统](../actions) 以处理输入内容
- 查看 [NONE 按钮](./none) 了解标准按钮类型