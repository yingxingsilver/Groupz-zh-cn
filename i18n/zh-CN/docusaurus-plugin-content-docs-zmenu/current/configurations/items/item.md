---
sidebar_position: 1
title: 物品配置
description: 所有可用的物品配置选项。
---

# 物品配置（Item Configuration）

物品是界面按钮中显示的视觉元素。本文档详述所有可用的物品配置选项。

## 基础物品结构

```yaml
item:
  material: DIAMOND
  name: "&b我的物品"
  lore:
    - "&7第一行"
    - "&7第二行"
```

## 配置选项

### material（材质）

物品的材质类型。zMenu 支持原版 Minecraft 材质及众多自定义物品插件。

```yaml
item:
  material: DIAMOND_SWORD
```

#### 材质加载器（Material Loaders）

zMenu 通过加载器系统支持多种材质来源：

| 插件 | 语法 | 价格 | 链接 |
|------|------|------|------|
| Bukkit | `STONE`, `DIAMOND_SWORD` | 内置 | 原版 Minecraft |
| PlaceholderAPI | `%your_placeholder_material%` | 免费 | [SpigotMC](https://www.spigotmc.org/resources/6245/) |
| 装备栏（盔甲） | `armor:HEAD`, `armor:CHEST`, `armor:LEGS`, `armor:FEET` | 内置 | 玩家已装备的盔甲 |
| zHead | `zhd:<id>` | 免费 | [Polymart](https://polymart.org/resource/2070) |
| MagicCosmetics | `magic_cosmetics:HAT`, `magic_cosmetics:BAG` 等 | 付费 | [Polymart](https://polymart.org/product/6981/magic-cosmetics-gui-non-animated) |
| HMCCosmetics | `hmc_cosmetics:<type>`, `hmc_cosmetics:<type>-<player>` | 付费 | [SpigotMC](https://www.spigotmc.org/resources/100107/) |
| zItems | `zitems:<id>` | 付费 | [SpigotMC](https://www.spigotmc.org/resources/118638/) |
| HeadDatabase | `hdb:<id>` | 付费 | [SpigotMC](https://www.spigotmc.org/resources/14280/) |
| Oraxen | `oraxen:<物品名称>` | 付费 | [SpigotMC](https://www.spigotmc.org/resources/72448/) |
| ItemsAdder | `itemsadder:<物品名称>` | 付费 | [SpigotMC](https://www.spigotmc.org/resources/73355/) |
| SlimeFun | `slimefun:<物品名称>` | 免费 | [GitHub](https://github.com/Slimefun/Slimefun4) |
| Nova | `nova:<物品/方块名称>` | 免费 | [GitHub](https://github.com/xenondevs/Nova) |
| Base64 | `base64:<base64编码的物品>` | 内置 | 以 Base64 字符串导入物品 |
| 玩家头颅 | `playerHead:<玩家名>`, `playerHead:%player%` | 内置 | 显示玩家头颅 |
| CraftEngine | `craftengine:<物品ID>` | 免费 | [Modrinth](https://modrinth.com/plugin/craftengine) |
| ExecutableItems | `ei:<物品ID>` | 付费 | [SpigotMC](https://www.spigotmc.org/resources/83070/) |
| ExecutableBlocks | `eb:<方块ID>` | 付费 | [SpigotMC](https://www.spigotmc.org/resources/94696/) |
| Nexo | `nexo:<物品ID>` | 付费 | [MCModels](https://mcmodels.net/products/13172/nexo) |

**示例：**

```yaml
# 原版材质
material: DIAMOND_SWORD

# 玩家头颅
material: "playerHead:%player%"

# zHead 自定义头颅
material: "zhd:123"

# ItemsAdder 自定义物品
material: "itemsadder:my_namespace:ruby_gem"

# Oraxen 自定义物品
material: "oraxen:amethyst_sword"
```

### name（名称）

物品的显示名称。

```yaml
item:
  material: DIAMOND
  name: "&b&l闪亮钻石"
```

**特性：**
- 支持颜色代码（`&6`, `&#FF5500`）
- 支持 MiniMessage（若已启用）
- 支持占位符（`%player%`）

### translated-name（翻译名称）

使用 Minecraft 内置翻译系统显示物品名称。

```yaml
item:
  material: DIAMOND_SWORD
  translated-name: "item.minecraft.diamond_sword"
```

此配置将根据玩家客户端语言显示物品名称。

### center-name（居中名称）

使物品名称在悬浮提示中居中显示。

```yaml
item:
  material: DIAMOND
  name: "&6&l稀有物品"
  center-name: true
```

### lore（描述）

物品名称下方显示的描述文本。

```yaml
item:
  material: DIAMOND_SWORD
  name: "&6&l传奇之剑"
  lore:
    - "&7一把强大的武器"
    - ""
    - "&7伤害: &c+50"
    - "&7速度: &a+10%"
    - ""
    - "&e点击装备！"
```

列表中的每个元素为一行。空字符串（`""`）可创建空白行。

### lore-type（描述类型）

定义 lore 的处理方式。

```yaml
item:
  material: DIAMOND_SWORD
  lore:
    - "&7一把强大的武器"
  lore-type: LEGACY  # 或 MINIMESSAGE, NONE
```

**可用类型：**

| 类型 | 说明 |
|------|------|
| `LEGACY` | 使用传统颜色代码（`&6`, `&l`） |
| `MINIMESSAGE` | 使用 MiniMessage 格式 |
| `NONE` | 不进行颜色处理 |

### translated-lore（翻译描述）

使用 Minecraft 内置翻译系统显示 lore 行。

```yaml
item:
  material: DIAMOND_SWORD
  translated-lore:
    - "item.minecraft.diamond_sword.desc"
```

### center-lore（居中描述）

使所有 lore 行在悬浮提示中居中显示。

```yaml
item:
  material: DIAMOND
  lore:
    - "&7稀有宝石"
    - "&7价值连城"
  center-lore: true
```

### amount（数量）

物品堆叠数量（1-64）。

```yaml
item:
  material: DIAMOND
  amount: 64
```

也可使用占位符实现动态数量：

```yaml
item:
  material: DIAMOND
  amount: "%zmenu_player_value_coins%"  # 动态数量
```

### durability（耐久度）

为工具和盔甲设置耐久度/损伤值。

```yaml
item:
  material: DIAMOND_SWORD
  durability: 100  # 已损耗 100 点耐久
```

### model-id（自定义模型 ID）

为资源包集成设置自定义模型数据。

```yaml
item:
  material: DIAMOND
  model-id: 12345
```

此配置允许资源包为物品显示自定义模型。

### item-model（物品模型）1.21+

使用命名空间键设置物品模型（Minecraft 1.21+）。

```yaml
item:
  material: DIAMOND_SWORD
  item-model: "minecraft:custom/my_sword"
```

或使用自定义命名空间：

```yaml
item:
  material: DIAMOND_SWORD
  item-model: "mypack:weapons/legendary_blade"
```

### equipped-model（装备模型）1.21+

设置物品被装备时显示的模型（Minecraft 1.21+）。

```yaml
item:
  material: DIAMOND_CHESTPLATE
  equipped-model: "minecraft:custom/my_armor"
```

此配置用于盔甲等装备类物品，可在穿戴时改变外观。

### glow（发光效果）

为物品添加附魔发光效果，但不显示实际附魔。

```yaml
item:
  material: NETHER_STAR
  name: "&6&l特殊物品"
  glow: true
```

### player-head（玩家头颅）

显示玩家头颅。材质将自动设为 `PLAYER_HEAD`。

```yaml
item:
  player-head: "%player%"  # 当前玩家的头颅
  name: "&a%player%的头颅"
```

也可指定特定玩家名称：

```yaml
item:
  player-head: "Notch"
```

:::tip
使用 `player-head` 时无需额外指定 `material: PLAYER_HEAD`，插件会自动处理。
:::

### url（头颅纹理 URL）

使用自定义头颅纹理 URL（Base64 编码）。

```yaml
item:
  material: PLAYER_HEAD
  url: "eyJ0ZXh0dXJlcyI6eyJTS0lOIjp7InVybCI6Imh0dHA6Ly90ZXh0dXJlcy5taW5lY3JhZnQubmV0L3RleHR1cmUv..."
  name: "&6自定义头颅"
```

可从 [minecraft-heads.com](https://minecraft-heads.com/) 等网站获取头颅纹理。

### enchantments（附魔）

为物品添加附魔效果。

```yaml
item:
  material: DIAMOND_SWORD
  enchantments:
    - type: SHARPNESS
      level: 5
    - type: UNBREAKING
      level: 3
    - type: FIRE_ASPECT
      level: 2
```

**常见附魔类型：**
- 剑类：`SHARPNESS`（锋利）、`SMITE`（亡灵杀手）、`FIRE_ASPECT`（火焰附加）、`KNOCKBACK`（击退）
- 盔甲：`PROTECTION`（保护）、`THORNS`（荆棘）、`UNBREAKING`（耐久）
- 工具：`EFFICIENCY`（效率）、`FORTUNE`（时运）、`SILK_TOUCH`（精准采集）
- 弓类：`POWER`（力量）、`PUNCH`（冲击）、`INFINITY`（无限）、`FLAME`（火焰）

### flags（隐藏标志）

使用物品标志隐藏特定属性。

```yaml
item:
  material: DIAMOND_SWORD
  enchantments:
    - type: SHARPNESS
      level: 5
  flags:
    - HIDE_ENCHANTS
    - HIDE_ATTRIBUTES
```

**可用标志：**

| 标志 | 说明 |
|------|------|
| `HIDE_ENCHANTS` | 隐藏附魔列表 |
| `HIDE_ATTRIBUTES` | 隐藏属性修饰符 |
| `HIDE_UNBREAKABLE` | 隐藏"不可破坏"标签 |
| `HIDE_DESTROYS` | 隐藏"可破坏方块"列表 |
| `HIDE_PLACED_ON` | 隐藏"可放置于"列表 |
| `HIDE_POTION_EFFECTS` | 隐藏药水效果 |
| `HIDE_DYE` | 隐藏皮革盔甲染色 |
| `HIDE_ARMOR_TRIM` | 隐藏盔甲镶边图案 |

### components（组件）1.20.5+

自 Minecraft 1.20.5 起，物品采用新的组件系统。zMenu 完整支持 40+ 种组件类型，包括食物、工具、武器、属性等。

```yaml
item:
  material: DIAMOND_SWORD
  components:
    custom-name: "&6&l传奇之剑"
    rarity: EPIC
    enchantments:
      - enchantment: sharpness
        level: 5
    fire-resistant: true
```

完整组件参考请见专用 [Components（组件）](./components) 页面。

## 完整示例

### 简单物品

```yaml
item:
  material: DIAMOND
  name: "&b&l钻石"
  lore:
    - "&7珍贵的宝石"
```

### 带纹理的玩家头颅

```yaml
item:
  player-head: "%player%"
  name: "&6&l玩家资料"
  lore:
    - "&7点击查看详情！"
  glow: true
```

或使用自定义纹理 URL：

```yaml
item:
  url: "eyJ0ZXh0dXJlcyI6eyJTS0lOIjp7InVybCI6Imh0dHA..."
  name: "&6&l宝箱"
  lore:
    - "&7点击开启！"
  glow: true
```

### 附魔工具

```yaml
item:
  material: DIAMOND_PICKAXE
  name: "&a&l矿工之梦"
  lore:
    - "&7终极采矿工具"
    - ""
    - "&7效率 V"
    - "&7时运 III"
    - "&7耐久 III"
  enchantments:
    - type: EFFICIENCY
      level: 5
    - type: FORTUNE
      level: 3
    - type: UNBREAKING
      level: 3
  flags:
    - HIDE_ENCHANTS
  glow: true
```

### 带占位符的动态物品

```yaml
item:
  player-head: "%player%"
  name: "&6&l%player%的资料"
  lore:
    - "&8&m─────────────────"
    - ""
    - "&7等级: &a%player_level%"
    - "&7生命: &c%player_health%/%player_max_health%"
    - "&7余额: &6$%vault_eco_balance_formatted%"
    - ""
    - "&7击杀: &a%statistic_player_kills%"
    - "&7死亡: &c%statistic_deaths%"
    - ""
    - "&8&m─────────────────"
```

### 自定义模型物品

```yaml
item:
  material: PAPER
  name: "&e&l魔法杖"
  lore:
    - "&7施展强大法术！"
  model-id: 10001
  glow: true
```

### 使用 ItemsAdder/Oraxen

ItemsAdder 自定义物品：

```yaml
item:
  material: ITEMSADDER:my_namespace:ruby_gem
  name: "&c&l红宝石"
```

Oraxen 物品：

```yaml
item:
  material: ORAXEN:amethyst_sword
  name: "&d&l紫水晶剑"
```

## 最佳实践

- **使用有意义的名称** - 使物品名称清晰易懂
- **统一描述格式** - 使用分隔线和间距提升可读性
- **隐藏不必要信息** - 使用 flags 隐藏不需要显示的附魔/属性
- **利用占位符** - 通过 PlaceholderAPI 使物品动态化
- **配合资源包测试** - 若使用 customModelData，需验证模型正确显示

## 后续步骤

- 学习如何在 [按钮（Buttons）](../buttons/button) 中使用物品
- 添加 [动作（Actions）](../buttons/actions) 使物品具备交互功能
- 创建 [模式（Patterns）](../patterns) 以复用物品模板
