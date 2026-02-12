---
sidebar_position: 3
title: 支持的插件
description: 本页面列出了所有官方支持的插件
---

# 支持的插件

zMenu 与众多流行的 Minecraft 插件集成，以扩展其功能。本页面列出了所有官方支持的插件及其启用的功能。

## 占位符插件

### PlaceholderAPI

**下载地址**：[SpigotMC](https://www.spigotmc.org/resources/placeholderapi.6245/)

PlaceholderAPI 强烈推荐与 zMenu 配合使用。它允许您在库存中显示动态值，例如玩家统计数据、经济余额等。

**启用功能：**
- 在物品名称、lore 和消息中使用 `%placeholder%` 语法
- 数千种可用的扩展
- 为性能优化的缓存占位符解析

**示例：**
```yaml
item:
  material: DIAMOND
  name: "&6%player_name%'s Menu"
  lore:
    - "&7Balance: &a$%vault_eco_balance%"
    - "&7Playtime: &e%statistic_hours_played%h"
```

---

## 经济插件

### Vault

**下载地址**：[SpigotMC](https://www.spigotmc.org/resources/vault.34315/)

Vault 提供统一的经济 API，zMenu 使用它来实现基于货币的操作。

**启用功能：**
- `currency-deposit` 动作
- `currency-withdraw` 动作
- 经济类占位符

---

## 权限插件

### LuckPerms

**下载地址**：[https://luckperms.net/](https://luckperms.net/)

LuckPerms 集成支持高级基于权限的功能。

**启用功能：**
- `luckperm` 条件类型
- `luckperm-set` 动作用于修改玩家组
- 基于组的查看条件

**示例：**
```yaml
click-requirement:
  requirements:
    - type: luckperm
      group: vip
      deny:
        - type: message
          messages:
            - "&cYou need VIP rank to use this!"
```

---

## 自定义物品插件

### ItemsAdder

**下载地址**：[SpigotMC](https://www.spigotmc.org/resources/itemsadder.73355/)

ItemsAdder 允许您在菜单中使用带自定义纹理的物品。

**用法：**
```yaml
item:
  material: ITEMSADDER:namespace:item_id
```

---

### Oraxen

**下载地址**：[SpigotMC](https://www.spigotmc.org/resources/oraxen.72448/)

支持 Oraxen 的自定义物品和模型。

**用法：**
```yaml
item:
  material: ORAXEN:item_id
```

---

### HeadDatabase

**下载地址**：[SpigotMC](https://www.spigotmc.org/resources/head-database.14280/)

HeadDatabase 提供数千种自定义玩家头颅纹理。

**用法：**
```yaml
item:
  material: HEAD_DATABASE:12345
```

---

### Slimefun

**下载地址**：[GitHub](https://github.com/Slimefun/Slimefun4)

在菜单中使用 Slimefun 物品。

**用法：**
```yaml
item:
  material: SLIMEFUN:ITEM_ID
```

---

### MythicMobs

**下载地址**：[SpigotMC](https://www.spigotmc.org/resources/mythicmobs.5702/)

MythicMobs 自定义物品集成。

**用法：**
```yaml
item:
  material: MYTHICMOBS:item_id
```

---

### ExecutableItems

**下载地址**：[SpigotMC](https://www.spigotmc.org/resources/executableitems.77578/)

ExecutableItems 特殊物品支持。

**用法：**
```yaml
item:
  material: EXECUTABLE_ITEM:item_id
```

---

### ExecutableBlocks

**下载地址**：[SpigotMC](https://www.spigotmc.org/resources/executableblocks.96914/)

ExecutableBlocks 特殊方块物品支持。

---

### BreweryX

**下载地址**：[SpigotMC](https://www.spigotmc.org/resources/breweryx.114777/)

BreweryX 酿造系统物品集成。

**用法：**
```yaml
item:
  material: BREWERYX:recipe_name
```

---

## 头颅插件

### zHead

**下载地址**：[SpigotMC](https://www.spigotmc.org/resources/zhead.115717/)

同开发者出品的另一款头颅纹理插件。

**用法：**
```yaml
item:
  material: ZHEAD:category:head_name
```

---

### HMCCosmetics

**下载地址**：[SpigotMC](https://www.spigotmc.org/resources/hmccosmetics.100107/)

HMCCosmetics 装饰性物品集成。

---

### MagicCosmetics

MagicCosmetics 装饰性物品集成。

---

## 职业插件

### Jobs Reborn

**下载地址**：[SpigotMC](https://www.spigotmc.org/resources/jobs-reborn.4216/)

Jobs Reborn 职业系统集成，支持基于职业的条件判断。

**启用功能：**
- `job` 条件类型用于检查职业等级

**示例：**
```yaml
click-requirement:
  requirements:
    - type: job
      job: Miner
      level: 10
```

---

## 店铺插件

### Shopkeepers

Shopkeepers 插件集成。

**启用功能：**
- `shopkeeper` 动作类型

---

## 数据包插件

### PacketEvents

**下载地址**：[SpigotMC](https://www.spigotmc.org/resources/packetevents-api.80279/)

PacketEvents 是对话框系统（Minecraft 1.20.5+）的必需依赖。

**启用功能：**
- 对话框系统支持
- 高级 UI 功能

---

## 支持插件汇总表

| 插件 | 类别 | 物品前缀 | 功能 |
|------|------|----------|------|
| PlaceholderAPI | 占位符 | - | 动态值显示 |
| Vault | 经济 | - | 货币操作 |
| LuckPerms | 权限 | - | 组权限条件 |
| ItemsAdder | 自定义物品 | `ITEMSADDER:` | 自定义物品/纹理 |
| Oraxen | 自定义物品 | `ORAXEN:` | 自定义物品/模型 |
| HeadDatabase | 头颅 | `HEAD_DATABASE:` | 自定义头颅 |
| Slimefun | 自定义物品 | `SLIMEFUN:` | Slimefun 物品 |
| MythicMobs | 自定义物品 | `MYTHICMOBS:` | MythicMobs 物品 |
| ExecutableItems | 自定义物品 | `EXECUTABLE_ITEM:` | EI 物品 |
| BreweryX | 自定义物品 | `BREWERYX:` | 酿造物品 |
| zHead | 头颅 | `ZHEAD:` | 自定义头颅 |
| Jobs Reborn | 职业 | - | 职业条件判断 |
| PacketEvents | 数据包 | - | 对话框系统 |

## 使用自定义物品材质

使用自定义物品插件时，材质格式通常遵循以下模式：

```yaml
item:
  material: PLUGIN_PREFIX:item_identifier
```

### 示例：

```yaml
# ItemsAdder
item:
  material: ITEMSADDER:my_namespace:ruby

# Oraxen
item:
  material: ORAXEN:emerald_sword

# HeadDatabase
item:
  material: HEAD_DATABASE:1234

# Slimefun
item:
  material: SLIMEFUN:ELECTRIC_MOTOR

# MythicMobs
item:
  material: MYTHICMOBS:SkeletonKingSword
```

## 插件自动检测

zMenu 会自动检测已安装的插件并启用其对应功能。您无需进行任何配置——只需安装插件并重启服务器即可。

要验证 zMenu 检测到的插件，可查看服务器启动时的控制台输出，或使用 `/zm version` 命令。