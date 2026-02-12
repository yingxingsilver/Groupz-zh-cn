---
sidebar_position: 7
title: 全局占位符
description: 定义可在所有库存中复用的值
---

# 全局占位符（Global Placeholders）



## 全局占位符

全局占位符允许您在单一位置定义值，并在所有库存配置中复用。适用于服务器信息、URL 链接及其他需要集中管理的配置项。

### 配置文件位置

全局占位符存储在：`plugins/zMenu/global-placeholders.yml`

---

## 基础用法

### 定义全局占位符

```yaml
# global-placeholders.yml

server-name: "我的超棒服务器"
server-ip: "play.myserver.com"
discord: "discord.gg/myserver"
website: "https://myserver.com"
store: "https://store.myserver.com"
version: "1.0.0"
```

### 使用全局占位符

通过 `%zmenu_global_placeholders_<键名>%` 调用：

```yaml
# 库存配置示例
items:
  server-info:
    slot: 4
    item:
      material: BOOK
      name: "&6&l%zmenu_global_placeholders_server-name%"
      lore:
        - "&7IP: &f%zmenu_global_placeholders_server-ip%"
        - "&7Discord: &9%zmenu_global_placeholders_discord%"
        - "&7官网: &b%zmenu_global_placeholders_website%"
```

---

## 高级特性

### 列表值

支持定义值列表：

```yaml
# global-placeholders.yml
rules:
  - "&7- 尊重他人"
  - "&7- 禁止作弊或使用外挂"
  - "&7- 禁止广告"
  - "&7- 玩得开心！"
```

### 嵌套值

使用点号（`.`）进行层级组织（点号会成为键名的一部分）：

```yaml
# global-placeholders.yml
social.discord: "discord.gg/myserver"
social.twitter: "@MyServer"
social.youtube: "youtube.com/myserver"

prices.vip: "9.99"
prices.mvp: "19.99"
prices.legend: "49.99"
```

**使用示例：**

```yaml
lore:
  - "&7Discord: %zmenu_global_placeholders_social.discord%"
  - "&7VIP 价格: $%zmenu_global_placeholders_prices.vip%"
```

---

## 实用示例

### 服务器信息菜单

```yaml
# global-placeholders.yml
server-name: "创世世界"
server-ip: "play.craftworld.net"
discord: "discord.gg/craftworld"
website: "https://craftworld.net"
owner: "Notch"
founded: "2020"
```

```yaml
# inventories/server_info.yml
name: "&6服务器信息"
size: 27

items:
  info:
    slot: 13
    item:
      material: BOOK
      name: "&6&l%zmenu_global_placeholders_server-name%"
      lore:
        - "&8&m─────────────────"
        - ""
        - "&7IP: &f%zmenu_global_placeholders_server-ip%"
        - "&7Discord: &9%zmenu_global_placeholders_discord%"
        - "&7官网: &b%zmenu_global_placeholders_website%"
        - ""
        - "&7服主: &e%zmenu_global_placeholders_owner%"
        - "&7创立时间: &a%zmenu_global_placeholders_founded%"
        - ""
        - "&8&m─────────────────"
```

### 商店价格集中管理

```yaml
# global-placeholders.yml
rank.vip.price: "9.99"
rank.vip.name: "&a&lVIP"
rank.mvp.price: "19.99"
rank.mvp.name: "&b&lMVP"
rank.legend.price: "49.99"
rank.legend.name: "&6&l传奇"
```

```yaml
# inventories/ranks.yml
items:
  vip:
    slot: 11
    item:
      material: IRON_BLOCK
      name: "%zmenu_global_placeholders_rank.vip.name%"
      lore:
        - "&7价格: &a$%zmenu_global_placeholders_rank.vip.price%"

  mvp:
    slot: 13
    item:
      material: GOLD_BLOCK
      name: "%zmenu_global_placeholders_rank.mvp.name%"
      lore:
        - "&7价格: &a$%zmenu_global_placeholders_rank.mvp.price%"

  legend:
    slot: 15
    item:
      material: DIAMOND_BLOCK
      name: "%zmenu_global_placeholders_rank.legend.name%"
      lore:
        - "&7价格: &a$%zmenu_global_placeholders_rank.legend.price%"
```

### 集中式颜色/主题管理

```yaml
# global-placeholders.yml
color.primary: "&6"
color.secondary: "&7"
color.success: "&a"
color.error: "&c"
color.info: "&b"

prefix.shop: "&6&l商店 &8»"
prefix.warp: "&5&l传送 &8»"
prefix.admin: "&c&l管理 &8»"
```

**使用示例：**

```yaml
items:
  item:
    item:
      name: "%zmenu_global_placeholders_color.primary%我的物品"
```

---

## 核心优势

| 优势 | 说明 |
|------|------|
| **集中管理** | 一处修改，全局生效 |
| **一致性** | 确保所有菜单使用相同值 |
| **便捷更新** | 服务器 IP、Discord 链接等只需更新一次 |
| **配置整洁** | 将通用配置值统一归档 |
| **无需重启** | 执行 `/zm reload` 即可生效 |

---

## 与本地占位符对比

| 特性 | 全局占位符 | 本地占位符 |
|------|------------|------------|
| **作用域** | 所有库存 | 单个库存文件 |
| **配置位置** | `global-placeholders.yml` | 库存文件内的 `local-placeholders` 区块 |
| **调用方式** | `%zmenu_global_placeholders_键名%` | `%键名%` |
| **适用场景** | 服务器级通用值 | 库存专属临时值 |

### 本地占位符示例

```yaml
# 库存文件内
local-placeholders:
  category: "武器"
  discount: "20%"

items:
  header:
    item:
      name: "&6%category% 商店"
      lore:
        - "&7特惠: 限时 %discount% 折扣！"
```

---

## 重载配置

修改 `global-placeholders.yml` 后执行：

```
/zm reload config
```

或重载全部配置：

```
/zm reload
```

---

## 最佳实践

✅ **使用描述性键名**  
`server-ip` 优于 `ip`

✅ **按逻辑分组**  
使用点号组织相关值：`social.discord`、`prices.vip`

✅ **添加注释说明**  
在 YAML 文件中为复杂值添加注释

✅ **按用途分类**  
将社交链接、价格、颜色等分组存放

✅ **避免过度使用**  
仅对真正需要全局复用的值使用全局占位符

❌ **避免**  
- 键名过短导致含义模糊
- 将库存专属值放入全局配置
- 忽略注释导致后期维护困难

---

## 后续步骤

- 了解 [模板（Patterns）](./patterns) 创建可复用的物品模板
- 学习 [玩家数据（Player Data）](./player-data) 实现个性化占位符
- 配置主 [config.yml](./config-yml) 文件优化插件行为
