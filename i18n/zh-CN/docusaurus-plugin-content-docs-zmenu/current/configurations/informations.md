---
sidebar_position: 1
title: 配置信息
description: 关于如何配置 zMenu 的必要信息。
---

# 配置信息

本页面提供关于如何配置 zMenu 的必要信息。

## 理解 YAML

zMenu 在所有配置文件中使用 YAML（YAML Ain't Markup Language）。如果您是 YAML 新手，请注意以下重要事项：

### YAML 基础规则

- **缩进很重要** - 使用空格（而非 Tab）进行缩进
- **保持一致的间距** - 每级缩进使用 2 个空格
- **区分大小写** - `Material` 与 `material` 不同
- **冒号后需加空格** - `key: value` 而非 `key:value`

### 示例结构

```yaml
# 这是一条注释
inventory-name: "My Inventory"    # 字符串值
size: 54                          # 数值
enabled: true                     # 布尔值

items:                            # 区段开始
  my-button:                      # 按钮名称（键）
    slot: 0                       # my-button 的属性
    item:                         # 嵌套区段
      material: DIAMOND
      name: "&bDiamond"
```

### 实用工具

- **YAML 验证器**：[YAML Lint](http://www.yamllint.com/) - 检查 YAML 语法
- **VS Code 扩展**：YAML by Red Hat - 语法高亮与验证
- **Notepad++**：支持 YAML 的轻量级编辑器

## 配置理念

zMenu 遵循模块化配置方法：

### 文件组织结构

```
plugins/zMenu/
├── config.yml                 # 全局设置
├── global-placeholders.yml    # 共享值
├── commands/                  # 命令定义
│   └── commands.yml
├── inventories/               # 您的菜单
│   ├── menu1.yml
│   └── subfolder/
│       └── menu2.yml
├── patterns/                  # 可复用模板
├── items/                     # 可复用物品
└── actions_patterns/          # 默认动作
```

### 核心原则

- **一文件一库存** - `inventories/` 中的每个 YAML 文件定义一个库存
- **支持子文件夹** - 可在子文件夹中组织库存
- **可复用性** - 使用模式（patterns）和物品（items）避免重复
- **热重载** - 使用 `/zm reload` 无需重启即可应用更改

## 颜色代码

zMenu 支持多种颜色代码格式：

### 传统颜色代码

使用 `&` 符号：

| 代码 | 颜色 | 代码 | 颜色 |
|------|------|------|------|
| `&0` | 黑色 | `&8` | 深灰色 |
| `&1` | 深蓝色 | `&9` | 蓝色 |
| `&2` | 深绿色 | `&a` | 绿色 |
| `&3` | 深青色 | `&b` | 青色 |
| `&4` | 深红色 | `&c` | 红色 |
| `&5` | 深紫色 | `&d` | 浅紫色 |
| `&6` | 金色 | `&e` | 黄色 |
| `&7` | 灰色 | `&f` | 白色 |

### 格式代码

| 代码 | 效果 |
|------|------|
| `&l` | 粗体 |
| `&o` | 斜体 |
| `&n` | 下划线 |
| `&m` | 删除线 |
| `&k` | 混淆 |
| `&r` | 重置 |

### 十六进制颜色

```yaml
name: "&#FF5555这是红色 &#55FF55这是绿色"
```

### MiniMessage（Paper/Purpur）

如果您使用 Paper、Purpur 或 Pufferfish，可在 `config.yml` 中启用 MiniMessage 格式：

```yaml
enable-mini-message-format: true
```

然后使用 MiniMessage 语法：

```yaml
name: " <gradient:red:blue>渐变文本</gradient> "
lore:
  - " <rainbow>彩虹文本！</rainbow> "
  - " <bold><gold>粗体金色文本</gold></bold> "
  - " <click:run_command:/spawn>点击传送到出生点</click> "
```

## 占位符

zMenu 在整个配置中支持占位符：

### PlaceholderAPI

任何 PlaceholderAPI 占位符均可在 zMenu 中使用：

```yaml
name: "&6%player_name%'s Profile"
lore:
  - "&7Balance: &a$%vault_eco_balance%"
  - "&7Level: &e%player_level%"
```

### zMenu 内置占位符

| 占位符 | 描述 |
|--------|------|
| `%player%` | 玩家名称 |
| `%page%` | 当前页码 |
| `%maxPage%` 或 `%max-page%` | 总页数 |
| `%zmenu_player_page%` | 当前页 |
| `%zmenu_player_max_page%` | 最大页数 |

完整列表请参阅 [占位符页面](./placeholders)。

## 重载配置

修改后重载配置：

```
/zm reload                    # 重载全部
/zm reload config             # 仅重载 config.yml
/zm reload inventory [name]   # 重载指定库存
/zm reload command [name]     # 重载指定命令
```

:::tip
测试特定库存的更改时，使用 `/zm reload inventory <name>` 比重载全部更快。
:::

## 常见错误

### 1. 缩进错误

```yaml
# 错误
items:
my-button:
  slot: 0

# 正确
items:
  my-button:
    slot: 0
```

### 2. 特殊字符未加引号

```yaml
# 错误 - 将导致解析错误
name: &6My Menu

# 正确
name: "&6My Menu"
```

### 3. 使用 Tab 字符

YAML 不允许使用 Tab。始终使用空格：

```yaml
# 错误（使用 Tab）
items:
→ my-button:
→ → slot: 0

# 正确（使用空格）
items:
  my-button:
    slot: 0
```

### 4. 重复键

```yaml
# 错误 - 第二个 'slot' 会覆盖第一个
my-button:
  slot: 0
  slot: 1

# 正确
my-button:
  slot: 0
another-button:
  slot: 1
```

## 文件编码

始终使用 **UTF-8 编码** 保存文件，以正确支持：
- 特殊字符
- 非英文文本
- 颜色代码

大多数现代文本编辑器默认使用 UTF-8，但如果遇到特殊字符问题，请检查编辑器设置。

## 后续步骤

了解基础后，接下来：

- 学习 [命令与权限](./commands-permissions)
- 探索可用的 [占位符](./placeholders)
- [创建您的第一个库存](./inventories/create-inventory)
