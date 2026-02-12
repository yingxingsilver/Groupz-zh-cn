---
sidebar_position: 8
title: 模式
---

# zMenu 模式（Patterns）

zMenu 提供三种类型的模式，每种用于解决不同的复用问题：

| 类型 | 文件夹 | 用途 | 引用方式 |
|------|--------|------|----------|
| 界面模式 (Inventory Pattern) | `patterns/` | 可跨界面复用的装饰/布局按钮 | 在界面中通过 `patterns: ["名称"]` 引用 |
| 按钮模式 (Button Pattern) | `patterns/` | 带变量替换的可复用单按钮模板 | 在按钮内通过 `pattern:` 部分引用 |
| 动作模式 (Action Pattern) | `actions_patterns/` | 应用于界面中所有按钮的默认动作 | 在界面中通过 `action-patterns: ["名称"]` 引用 |

> 前两种模式位于同一文件夹，通过文件内的 `type` 键区分。

## 文件位置

```
plugins/zMenu/
├── patterns/               # 界面模式和按钮模式
│   ├── my_decoration.yml   # type: INVENTORY（默认）
│   └── my_button.yml       # type: BUTTON
└── actions_patterns/       # 动作模式
    └── default-actions.yml
```

支持子文件夹 - 例如 `patterns/borders/simple.yml` 可正常工作。

---

## 界面模式 (Inventory Pattern)

界面模式定义一组可应用于任意界面的按钮（装饰、导航等），可视为可复用的装饰层。

### 结构

```yaml
name: "pattern_example"        # 唯一名称，用于在界面中引用此模式
size: 54                       # 界面大小（必须是 9 的倍数）
# type: INVENTORY              # 可选 - INVENTORY 为默认值
# enableMultiPage: false       # 设为 true 以启用分页按钮功能
# matrix:                      # 可选槽位矩阵（语法同界面文件）

items:
  example1:
    isPermanent: true
    item:
      material: IRON_INGOT
      name: "&f装饰"
    slots:
      - 0
      - 8
      - 45
      - 53
```

| 键 (Key) | 必填 | 说明 |
|----------|------|------|
| `name` | 是 | 唯一标识符，界面通过此名称引用 |
| `size` | 否 | 默认 54，必须是 9 的倍数 |
| `type` | 否 | `INVENTORY`（默认）或 `BUTTON`。设为 `BUTTON` 时文件将作为按钮模式加载 |
| `enableMultiPage` | 否 | 设为 `true` 时，此模式内的 `NEXT`/`PREVIOUS` 按钮将与界面分页功能联动。默认 `false` |
| `matrix` | 否 | 可选槽位矩阵，格式同界面文件 |
| `items` | 是 | 要放置的按钮，语法同界面文件的 `items` 部分 |

### 在界面中使用

在界面的 `patterns` 列表中通过名称引用一个或多个界面模式：

```yaml
name: "&6商店"
size: 54
patterns:
  - "pattern_example"
  - "pagination"

items:
  # 您的界面专属按钮...
```

> 模式按顺序应用 - 后续模式中的按钮若占据相同槽位，将覆盖先前模式的按钮。

### 示例：装饰

文件 `patterns/pattern_example.yml`（随 zMenu 附带）：

```yaml
name: "pattern_example"
size: 54
items:
  example1:
    isPermanent: true
    item:
      material: IRON_INGOT
      name: "&f模式示例"
    slots:
      - 0
      - 8
      - 45
      - 53
```

在界面中使用：

```yaml
name: "&e示例"
size: 54
patterns:
  - "pattern_example"

items:
  # ...
```

### 示例：带分页的导航

文件 `patterns/pagination.yml`：

```yaml
name: "pagination"
size: 54
enableMultiPage: true
items:
  next:
    type: NEXT
    slot: 52
    is-permanent: true
    item:
      material: PAPER
      name: "&f下一页"

  previous:
    type: PREVIOUS
    slot: 49
    is-permanent: true
    item:
      material: PAPER
      name: "&f上一页"
```

关键点：`enableMultiPage: true` - 若无此设置，模式中的 `NEXT` 和 `PREVIOUS` 按钮将无法工作。

---

## 按钮模式 (Button Pattern)

按钮模式是单个按钮的模板。使用它的界面传递变量（`%key%` 占位符），在按钮加载前进行替换。这是功能最强大的模式类型。

### 结构

```yaml
name: "pattern_cookies"       # 唯一名称
type: BUTTON                  # 必须为 BUTTON

button:                       # 此部分下的所有内容将成为按钮定义
  slot: '%slot%'
  item:
    material: '%material%'
    name: "&a%name%"
    lore:
      - "&7点击购买"
  # click-requirement, actions, view_requirement, else... 均可在此定义
```

| 键 (Key) | 必填 | 说明 |
|----------|------|------|
| `name` | 是 | 唯一标识符 |
| `type` | 是 | 必须为 `BUTTON`。若省略，文件将作为界面模式处理且 `button:` 部分被忽略 |
| `button` | 是 | 按钮定义。支持普通按钮的所有属性（slot, item, actions, click-requirement, view_requirement, else 等） |
| `default-values` | 否 | 变量的默认回退值（见下文） |
| `local-placeholders` | 否 | 在模式文件中定义的额外占位符（见下文） |

### 在界面中使用

在界面的 `items` 部分，为按钮添加 `pattern` 键：

```yaml
items:
  grandma:
    pattern:
      file-name: "pattern_cookies"   # 模式文件名
      slot: 20                       # 作为 %slot% 传入
      material: CAKE                 # 作为 %material% 传入
      name: "奶奶"                   # 作为 %name% 传入
      key: "grandma"                 # 作为 %key% 传入
      price: 10                      # 作为 %price% 传入
```

`pattern:` 下的所有键（除 `file-name`、`plugin-name` 及其别名外）均作为变量传递。插件收集所有键后，在加载前替换模式 `button` 部分中的 `%key%` 占位符。

### 变量替换

模式文件中任何被 `%...%` 包裹的值，将被界面提供的值替换。例如：

模式文件：
```yaml
button:
  slot: '%slot%'
  item:
    material: '%material%'
    name: "&a%name%"
```

界面使用：
```yaml
pattern:
  file-name: "my_pattern"
  slot: 13
  material: DIAMOND
  name: "闪亮"
```

结果：槽位 `13`，材质 `DIAMOND`，名称 `&a闪亮`。

### 变量修饰符

zMenu 提供内置修饰符，可转换变量值。若变量名为 `key`，可用以下修饰符：

| 修饰符 | 语法 | 示例（key = hello） | 结果 |
|--------|------|---------------------|------|
| 大写 | `%upper_key%` | `%upper_key%` | `HELLO` |
| 小写 | `%lower_key%` | `%lower_key%` | `hello` |
| 首字母大写 | `%capitalize_key%` | `%capitalize_key%` | `Hello` |
| 加一 | `%add_one_key%` | `%add_one_key%`（key = 5） | `6` |
| 减一 | `%remove_one_key%` | `%remove_one_key%`（key = 5） | `4` |

`add_one` 和 `remove_one` 将值解析为整数。若解析失败，占位符保持不变。

这些修饰符适用于任何变量 - 例如界面传入 `level: 3`，则 `%remove_one_level%` 解析为 `2`。

### 特殊键

以下键不作为变量传递，而是控制模式加载方式：

| 键 | 别名 | 说明 |
|----|------|------|
| `file-name` | `fileName`, `file` | 模式文件名（不含 `.yml`） |
| `plugin-name` | `pluginName`, `plugin` | 从其他插件的 `patterns/` 文件夹加载模式 |

### 默认值 (default-values)

可在模式文件中定义回退值。若界面未提供某变量，则使用默认值：

```yaml
name: "my_template"
type: BUTTON

default-values:
  material: STONE
  name: "默认名称"
  slot: 0

button:
  slot: '%slot%'
  item:
    material: '%material%'
    name: "%name%"
```

若界面仅提供 `slot: 5`，按钮将使用材质 `STONE` 和名称 `默认名称`。

> 优先级：界面值 > 默认值。

### 本地占位符 (local-placeholders)

可在模式文件中通过 `local-placeholders` 定义额外占位符。它们在界面值和默认值之后加载，因此在模式定义的值中优先级最低：

```yaml
name: "my_template"
type: BUTTON

local-placeholders:
  prefix: "&7[商店] "

button:
  item:
    name: "%prefix%%name%"
```

### 列表变量

当变量值为 YAML 列表时，zMenu 会将每个条目展开为单独行，适用于 `lore` 等场景：

界面：
```yaml
items:
  reward:
    pattern:
      fileName: "playtime_reward"
      rewards:
        - '&f50$'
        - '&fx4 铁锭'
      commands:
        - 'eco give %player% 50'
        - 'give %player% iron_ingot 4'
```

模式文件（lore 部分）：
```yaml
lore:
  - '&e奖励:'
  - '%rewards%'
  - ''
```

结果：`%rewards%` 行被展开为两行（`&f50$` 和 `&fx4 铁锭`），lore 显示为：
```
&e奖励:
&f50$
&fx4 铁锭
```

同样适用于动作中的 `commands` 列表 - `console_command` 动作的 `commands` 列表中的 `%commands%` 占位符将展开为多条命令。

### 项目级 view-requirement 和 else

可在界面侧的按钮定义中，在 `pattern` 外部添加 `view-requirement` 和 `else`。它们会被单独加载并应用到模式按钮之上：

```yaml
items:
  my_button:
    pattern:
      file-name: "my_template"
      slot: 10
      name: "VIP 物品"
    view-requirement:
      requirements:
        - type: permission
          permission: "vip.access"
    else:
      item:
        material: BARRIER
        name: "&c仅限 VIP"
```

这允许您复用同一模式，但为不同界面添加不同的可见性条件，无需修改模式文件。

> 注意：模式文件自身的 `button` 部分内也可包含 `view-requirement` 和 `else` - 两者均受支持。

### 示例：Cookie Clicker

模式文件 `patterns/pattern_cookies.yml`：
```yaml
name: "pattern_cookies"
type: BUTTON

button:
  click-requirement:
    purchase:
      clicks:
        - ALL
      requirements:
        - type: placeholder
          math: true
          placeholder: "%zmenu_player_value_cookie%"
          value: "%price%+(%price%*%zmenu_player_value_%key%%*1.15)"
          action: SUPERIOR_OR_EQUAL
      success:
        - type: data
          action: SUBTRACT
          key: 'cookie'
          value: '%price%+(%price%*%zmenu_player_value_%key%%*1.15)'
          math: true
        - type: data
          action: ADD
          key: '%key%'
          value: '1'
        - type: inventory
          inventory: 'cookies'
      deny:
        - type: message
          messages:
            - "&c饼干不足"

  slot: '%slot%'

  item:
    material: '%material%'
    name: "&a%name% #353535(&7%zmenu_player_value_%key%%#353535)"
    lore:
      - ""
      - "&f价格 &8: &b%zmenu_formatted_math_%price%+(%price%*{zmenu_player_value_%key%}*1.15)% 饼干"
      - ""
      - "&a点击购买"
```

界面文件 `inventories/examples/cookies.yml`：
```yaml
name: "&8饼干"
size: 36
items:
  cookie:
    slot: 13
    item:
      material: COOKIE
      name: "#77ff77饼干#353535: &f%zmenu_player_value_cookie%"
      lore:
        - ""
        - "&f每次点击获得 &8: &b%zmenu_formatted_math_1+({zmenu_player_value_grandma}*2)+({zmenu_player_value_factory}*5)+({zmenu_player_value_mine}*10)%"
        - ""
        - "&7点击我！"
    actions:
      - type: data
        action: ADD
        key: 'cookie'
        value: '1+(%zmenu_player_value_grandma%*2)+(%zmenu_player_value_factory%*5)+(%zmenu_player_value_mine%*10)'
        math: true
      - type: refresh

  grandma:
    pattern:
      file-name: "pattern_cookies"
      plugin-name: "zMenu"
      slot: 20
      material: CAKE
      name: "奶奶"
      key: "grandma"
      price: 10

  factory:
    pattern:
      file-name: "pattern_cookies"
      plugin-name: "zMenu"
      slot: 22
      material: WHEAT
      name: "工厂"
      key: "factory"
      price: 20

  mine:
    pattern:
      file-name: "pattern_cookies"
      plugin-name: "zMenu"
      slot: 24
      material: IRON_PICKAXE
      name: "矿场"
      key: "mine"
      price: 50
```

同一 `pattern_cookies` 模板被复用三次，通过不同变量创建三个升级按钮。

### 示例：在线时长奖励

此示例展示嵌套的 `else` 链和 `%remove_one_level%` 修饰符。模式根据玩家状态显示不同物品（已领取、时长不足、需先领取上一级、可领取）。

模式文件 `patterns/playtime_reward.yml`（简化版）：
```yaml
name: "playtime_reward"
type: BUTTON
button:
  updateOnClick: true
  page: '%page%'
  slot: '%slot%'

  # 状态 1：已领取（等级 >= 当前等级）
  view-requirement:
    requirements:
      - type: placeholder
        placeholder: "%zmenu_player_value_playtime_level%"
        value: '%level%'
        action: SUPERIOR_OR_EQUAL
  item:
    url: '%head%'
    name: '%color%等级 %level%'
    lore:
      - '%rewards%'          # 列表变量 - 展开为多行
      - '&c已领取'

  else:
    # 状态 2：时长不足
    view-requirement:
      requirements:
        - type: placeholder
          placeholder: "%zmenu_statistic_hours_played%"
          value: '%hour%'
          action: LOWER
    item:
      url: '%head%'
      name: '%color%等级 %level%'
      lore:
        - '%rewards%'
        - '&c在线时长不足'

    else:
      # 状态 3：需先领取上一级
      view-requirement:
        requirements:
          - type: placeholder
            placeholder: "%zmenu_player_value_playtime_level%"
            value: '%remove_one_level%'    # 等级减 1
            action: LOWER
      item:
        url: '%head%'
        name: '%color%等级 %level%'
        lore:
          - '%rewards%'
          - '&c请先领取上一级奖励'

      else:
        # 状态 4：可领取
        click-requirement:
          right_click:
            clicks:
              - ALL
            requirements:
              - type: placeholder
                placeholder: "%zmenu_player_value_playtime_level%"
                value: '%remove_one_level%'
                action: EQUAL_TO
              - type: placeholder
                placeholder: "%zmenu_statistic_hours_played%"
                value: '%hour%'
                action: SUPERIOR_OR_EQUAL
            success:
              - type: data
                action: SET
                key: 'playtime_level'
                value: '%level%'
              - type: console_command
                commands:
                  - '%commands%'       # 列表变量 - 展开为多条命令
              - type: sound
                sound: ENTITY_PLAYER_LEVELUP
        item:
          url: '%head%'
          name: '%color%等级 %level%'
          lore:
            - '%rewards%'
            - '&a点击领取'
```

界面使用（仅展示一个按钮）：
```yaml
items:
  btn-10:
    pattern:
      fileName: "playtime_reward"
      slot: 10
      level: 1
      hour: 1
      color: "#7f7f7f"
      head: "eyJ0ZXh0dXJlcyI6..."
      rewards:
        - '&f50$'
        - '&fx4 铁锭'
      commands:
        - 'eco give %player% 50'
        - 'give %player% iron_ingot 4'
```

关键点：
- `%remove_one_level%` 使用 `remove_one` 修饰符处理 `level` 变量 - 若 `level` 为 `3`，则变为 `2`
- `%rewards%` 为列表变量 - 展开为两行 lore
- `%commands%` 为列表变量 - 展开为两条控制台命令
- 嵌套的 `else` 链创建了包含四种视觉状态的状态机

---

## 动作模式 (Action Pattern)

动作模式定义应用于界面中所有按钮的默认动作（除非按钮自行定义动作）。

### 结构

```yaml
name: default-actions
actions:
  - type: sound
    sound: ENTITY_VILLAGER_YES

deny-actions:
  - type: sound
    sound: ENTITY_VILLAGER_NO
```

| 键 | 说明 |
|----|------|
| `name` | 此动作模式的唯一标识符 |
| `actions` | 按钮点击成功时执行的动作 |
| `deny-actions` | 要求条件阻止点击时执行的动作 |

### 在界面中使用

在界面的 `action-patterns` 列表中通过名称引用动作模式：

```yaml
name: "&6商店"
size: 54
action-patterns:
  - "default-actions"

items:
  # ...
```

> ⚠️ 与 `patterns` 类似，`action-patterns` 列表仅接受字符串，不支持对象。

### 示例

文件 `actions_patterns/default-actions.yml`（随 zMenu 附带）：
```yaml
name: default-actions
actions:
  - type: sound
    sound: ENTITY_VILLAGER_YES
deny-actions:
  - type: sound
    sound: ENTITY_VILLAGER_NO
```

应用此模式后，界面中每个按钮在成功点击时将播放 `ENTITY_VILLAGER_YES` 音效，被拒绝时播放 `ENTITY_VILLAGER_NO` 音效（除非按钮自行覆盖这些动作）。

---

## 变量解析顺序

使用按钮模式时，变量按以下顺序解析（优先级从高到低）：

1. **界面值** - 在界面文件 `pattern:` 下定义的键
2. **默认值** - 在模式文件中定义的 `default-values`
3. **全局占位符** - 通过 API 注册的插件级占位符
4. **本地占位符** - 在模式文件中定义的 `local-placeholders`

若同一键存在于多个层级，优先级最高的来源生效。

---

## 跨插件模式

要从其他插件的 `patterns/` 文件夹加载模式，使用 `plugin-name`：

```yaml
items:
  my_button:
    pattern:
      file-name: "shared_template"
      plugin-name: "OtherPlugin"
      slot: 13
```

这将查找文件 `plugins/OtherPlugin/patterns/shared_template.yml`。

接受的别名：`plugin-name`、`pluginName`、`plugin`。

---

## 重载

```
/zm reload
```

插件重启或执行重载命令时，所有模式（界面、按钮、动作）将被重新加载。

---

## 常见错误

### 在 `patterns` 列表中使用对象

```yaml
# 错误 - 将被静默忽略
patterns:
  - pattern:
      file-name: "my_decoration"

# 正确
patterns:
  - "my_decoration"
```

`patterns` 列表内部使用 `getStringList()`，仅接受字符串。要使用带变量的按钮模式，请将 `pattern:` 键放在 `items` 部分的特定按钮内 - 而非顶层 `patterns` 列表中。

### 忘记 `type: BUTTON`

```yaml
# 错误 - 被视为界面模式，"button:" 部分被忽略
name: "my_template"
button:
  slot: '%slot%'
  item:
    material: DIAMOND

# 正确
name: "my_template"
type: BUTTON
button:
  slot: '%slot%'
  item:
    material: DIAMOND
```

缺少 `type: BUTTON` 时，文件将作为界面模式加载。由于它没有 `items` 部分，将加载失败。

### 混淆界面模式与按钮模式

- **界面模式**：在顶层 `patterns: ["名称"]` 列表中引用。定义一组按钮。**不支持**变量替换。
- **按钮模式**：在单个按钮定义内通过 `pattern:` 引用。定义一个带 `%变量%` 占位符的按钮。

### 期望在界面模式中使用变量

界面模式**不执行**变量替换。如需 `%占位符%`，请改用按钮模式（`type: BUTTON`）。
