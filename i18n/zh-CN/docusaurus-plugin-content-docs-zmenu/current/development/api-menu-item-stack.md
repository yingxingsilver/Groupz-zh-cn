---
sidebar_position: 8
title: MenuItemStack
description: 使用 MenuItemStack 创建与操作动态物品
---

# MenuItemStack API 文档



## MenuItemStack

学习如何在 zMenu 中使用 `MenuItemStack` 创建支持占位符、具备玩家上下文感知能力的动态物品。

### 概述

`MenuItemStack` 是对 Bukkit `ItemStack` 的抽象封装，提供以下能力：

- **占位符支持**：在名称、描述（lore）及其他属性中解析占位符
- **动态构建**：基于玩家上下文动态生成物品
- **便捷操作**：简化物品属性的修改流程
- **高级特性**：支持自定义模型数据、附魔效果等

---

## 获取 MenuItemStack

### 从按钮配置中获取

在自定义按钮中获取 YAML 配置定义的物品：

```java
public class MyButton extends Button {

    @Override
    public ItemStack getCustomItemStack(Player player) {
        // 从 YAML 配置获取 MenuItemStack
        MenuItemStack menuItemStack = this.getItemStack();

        // 构建并返回 ItemStack
        return menuItemStack.build(player);
    }
}
```

### 从 InventoryManager 加载

通过配置路径动态加载 MenuItemStack：

```java
InventoryManager manager = menuPlugin.getInventoryManager();
MenuItemStack itemStack = manager.loadItemStack(configuration, "path.to.item");
```

---

## 构建 ItemStack

### 基础构建

```java
MenuItemStack menuItemStack = getItemStack();

// 带占位符解析的基础构建
ItemStack item = menuItemStack.build(player);
```

### 缓存控制构建

```java
// 无缓存构建（始终生成最新物品）
ItemStack item = menuItemStack.build(player, false);

// 启用缓存（在可能的情况下复用缓存结果）
ItemStack itemCached = menuItemStack.build(player, true);
```

### 使用自定义占位符构建

```java
import fr.maxlego08.menu.api.utils.Placeholders;

MenuItemStack menuItemStack = getItemStack();
Placeholders placeholders = new Placeholders();

// 注册自定义占位符
placeholders.register("price", "1000");
placeholders.register("currency", "coins");
placeholders.register("stock", "42");

// 使用自定义占位符构建物品
ItemStack item = menuItemStack.build(player, false, placeholders);
```

---

## 自定义占位符

### 创建占位符

```java
Placeholders placeholders = new Placeholders();

// 字符串值
placeholders.register("player_name", player.getName());
placeholders.register("server_name", "MyServer");

// 数值（转换为字符串）
placeholders.register("balance", String.valueOf(economy.getBalance(player)));
placeholders.register("level", String.valueOf(player.getLevel()));

// 布尔状态
placeholders.register("is_premium", player.hasPermission("premium") ? "Yes" : "No");

// 格式化数值
placeholders.register("health", String.format("%.1f", player.getHealth()));
```

### YAML 中的使用示例

```yaml
item:
  material: DIAMOND
  name: "&6以 %price% %currency% 购买"
  lore:
    - "&7剩余库存: &e%stock%"
    - "&7卖家: &b%seller_name%"
    - ""
    - "&a点击购买！"
```

---

## 完整示例：动态商店物品

### SellBuyButton.java

```java
package com.example.shop.buttons;

import fr.maxlego08.menu.api.MenuItemStack;
import fr.maxlego08.menu.api.button.Button;
import fr.maxlego08.menu.api.engine.InventoryEngine;
import fr.maxlego08.menu.api.utils.Placeholders;
import com.example.shop.ShopPlugin;
import com.example.shop.ShopItem;
import org.bukkit.entity.Player;
import org.bukkit.event.inventory.InventoryClickEvent;
import org.bukkit.inventory.ItemStack;
import org.bukkit.plugin.Plugin;

import java.math.BigDecimal;

public class SellBuyButton extends Button {

    private final ShopPlugin plugin;

    public SellBuyButton(Plugin plugin) {
        this.plugin = (ShopPlugin) plugin;
    }

    @Override
    public ItemStack getCustomItemStack(Player player) {
        // 从配置获取 MenuItemStack
        MenuItemStack menuItemStack = this.getItemStack();

        // 从玩家缓存获取数据
        var cache = plugin.getCacheManager().getCache(player);
        ShopItem shopItem = cache.get("current_item");
        BigDecimal price = cache.get("price", BigDecimal.ZERO);

        // 创建占位符
        Placeholders placeholders = new Placeholders();
        placeholders.register("price", plugin.getEconomyManager().format(price));
        placeholders.register("item_name", shopItem.getName());
        placeholders.register("quantity", String.valueOf(shopItem.getQuantity()));

        // 检查购买能力并添加状态提示
        boolean canAfford = plugin.getEconomyManager().has(player, price);
        placeholders.register("status", canAfford ? "&a资金充足！" : "&c余额不足！");

        // 构建并返回物品
        return menuItemStack.build(player, false, placeholders);
    }

    @Override
    public void onClick(Player player, InventoryClickEvent event,
                       InventoryEngine inventory, int slot, Placeholders placeholders) {
        super.onClick(player, event, inventory, slot, placeholders);

        var cache = plugin.getCacheManager().getCache(player);
        ShopItem shopItem = cache.get("current_item");
        BigDecimal price = cache.get("price", BigDecimal.ZERO);

        if (plugin.getEconomyManager().withdraw(player, price)) {
            shopItem.give(player);
            player.sendMessage("§a购买成功！");
            player.closeInventory();
        } else {
            player.sendMessage("§c余额不足！");
        }
    }
}
```

### YAML 配置

```yaml
items:
  buy-button:
    type: MY_SELL_BUY
    slot: 22
    item:
      material: EMERALD
      name: "&a&l确认购买"
      lore:
        - "&7物品: &f%item_name%"
        - "&7数量: &e%quantity%"
        - "&7价格: &6%price%"
        - ""
        - "%status%"
        - ""
        - "&e点击完成购买！"
```

---

## 完整示例：带状态的排序按钮

### SortButton.java

```java
package com.example.sort.buttons;

import fr.maxlego08.menu.api.MenuItemStack;
import fr.maxlego08.menu.api.button.Button;
import fr.maxlego08.menu.api.engine.InventoryEngine;
import fr.maxlego08.menu.api.utils.Placeholders;
import com.example.sort.SortPlugin;
import com.example.sort.SortType;
import org.bukkit.entity.Player;
import org.bukkit.event.inventory.InventoryClickEvent;
import org.bukkit.inventory.ItemStack;
import org.bukkit.plugin.Plugin;

import java.util.List;

public class SortButton extends Button {

    private final SortPlugin plugin;
    private final String enableText;
    private final String disableText;
    private final MenuItemStack loadingItemStack;
    private final List<SortType> sortTypes;

    public SortButton(Plugin plugin, String enableText, String disableText,
                     MenuItemStack loadingItemStack, List<SortType> sortTypes) {
        this.plugin = (SortPlugin) plugin;
        this.enableText = enableText;
        this.disableText = disableText;
        this.loadingItemStack = loadingItemStack;
        this.sortTypes = sortTypes;
    }

    @Override
    public boolean isPermanent() {
        return true;
    }

    @Override
    public ItemStack getCustomItemStack(Player player) {
        var cache = plugin.getCacheManager().getCache(player);
        SortType currentSort = cache.get("current_sort", SortType.DATE_DESC);

        MenuItemStack itemStack = this.getItemStack();
        Placeholders placeholders = new Placeholders();

        // 为每种排序类型注册状态
        for (SortType sortType : sortTypes) {
            String state = (sortType == currentSort) ? enableText : disableText;
            String displayName = state.replace("%sorting%", sortType.getDisplayName());
            placeholders.register(sortType.name(), displayName);
        }

        return itemStack.build(player, false, placeholders);
    }

    @Override
    public void onClick(Player player, InventoryClickEvent event,
                       InventoryEngine inventory, int slot, Placeholders placeholders) {
        super.onClick(player, event, inventory, slot, placeholders);

        var cache = plugin.getCacheManager().getCache(player);
        SortType currentSort = cache.get("current_sort", SortType.DATE_DESC);

        // 显示加载状态
        for (Integer buttonSlot : getSlots()) {
            inventory.getSpigotInventory().setItem(buttonSlot, loadingItemStack.build(player));
        }

        // 计算下一个排序类型
        int currentIndex = sortTypes.indexOf(currentSort);
        int direction = event.isRightClick() ? -1 : 1;
        int nextIndex = (currentIndex + direction + sortTypes.size()) % sortTypes.size();
        SortType nextSort = sortTypes.get(nextIndex);

        // 更新缓存并刷新
        cache.set("current_sort", nextSort);

        plugin.getScheduler().runAsync(task -> {
            // 异步执行排序
            plugin.getSortManager().sortItems(player, nextSort);

            // 在主线程更新库存
            plugin.getScheduler().runNextTick(w -> {
                inventory.updateInventory();
            });
        });
    }
}
```

### YAML 配置

```yaml
items:
  sort-button:
    type: MY_SORT_BUTTON
    slot: 49
    is-permanent: true
    item:
      material: HOPPER
      name: "&e&l排序物品"
      lore:
        - "&7当前排序选项："
        - ""
        - "%DATE_DESC%"
        - "%DATE_ASC%"
        - "%PRICE_DESC%"
        - "%PRICE_ASC%"
        - "%NAME_ASC%"
        - ""
        - "&e左键点击 &7切换至下一项"
        - "&e右键点击 &7切换至上一项"
```

---

## 完整示例：切换按钮

### ToggleButton.java

```java
package com.example.settings.buttons;

import fr.maxlego08.menu.api.MenuItemStack;
import fr.maxlego08.menu.api.button.Button;
import fr.maxlego08.menu.api.engine.InventoryEngine;
import fr.maxlego08.menu.api.utils.Placeholders;
import org.bukkit.entity.Player;
import org.bukkit.event.inventory.InventoryClickEvent;
import org.bukkit.inventory.ItemStack;
import org.bukkit.plugin.Plugin;

public class ToggleButton extends Button {

    private final Plugin plugin;
    private final String settingKey;
    private final MenuItemStack enabledItem;
    private final MenuItemStack disabledItem;

    public ToggleButton(Plugin plugin, String settingKey,
                       MenuItemStack enabledItem, MenuItemStack disabledItem) {
        this.plugin = plugin;
        this.settingKey = settingKey;
        this.enabledItem = enabledItem;
        this.disabledItem = disabledItem;
    }

    @Override
    public ItemStack getCustomItemStack(Player player) {
        boolean isEnabled = getSettingValue(player);

        // 根据状态选用不同的 MenuItemStack
        MenuItemStack itemStack = isEnabled ? enabledItem : disabledItem;

        Placeholders placeholders = new Placeholders();
        placeholders.register("status", isEnabled ? "&a已启用" : "&c已禁用");
        placeholders.register("action", isEnabled ? "禁用" : "启用");

        return itemStack.build(player, false, placeholders);
    }

    @Override
    public void onClick(Player player, InventoryClickEvent event,
                       InventoryEngine inventory, int slot, Placeholders placeholders) {
        super.onClick(player, event, inventory, slot, placeholders);

        // 切换设置状态
        boolean currentValue = getSettingValue(player);
        setSettingValue(player, !currentValue);

        // 刷新库存
        inventory.updateInventory();

        // 通知玩家
        String newStatus = !currentValue ? "已启用" : "已禁用";
        player.sendMessage("§a设置 " + settingKey + " " + newStatus + "！");
    }

    private boolean getSettingValue(Player player) {
        // 实现取决于你的存储方案
        return plugin.getConfig().getBoolean("players." + player.getUniqueId() + "." + settingKey, false);
    }

    private void setSettingValue(Player player, boolean value) {
        plugin.getConfig().set("players." + player.getUniqueId() + "." + settingKey, value);
        plugin.saveConfig();
    }
}
```

### 从配置加载 MenuItemStack

#### Custom ButtonLoader

```java
package com.example.loader;

import fr.maxlego08.menu.api.MenuItemStack;
import fr.maxlego08.menu.api.InventoryManager;
import fr.maxlego08.menu.api.button.Button;
import fr.maxlego08.menu.api.loader.ButtonLoader;
import com.example.buttons.ToggleButton;
import org.bukkit.configuration.file.YamlConfiguration;
import org.bukkit.plugin.Plugin;

public class ToggleButtonLoader implements ButtonLoader {

    private final Plugin plugin;
    private final InventoryManager inventoryManager;

    public ToggleButtonLoader(Plugin plugin, InventoryManager inventoryManager) {
        this.plugin = plugin;
        this.inventoryManager = inventoryManager;
    }

    @Override
    public String getName() {
        return "MY_TOGGLE";
    }

    @Override
    public Button load(YamlConfiguration config, String path, Plugin plugin) {
        // 加载基础属性
        String settingKey = config.getString(path + ".setting-key", "default");

        // 加载启用状态的 MenuItemStack
        MenuItemStack enabledItem = inventoryManager.loadItemStack(config, path + ".enabled-item");

        // 加载禁用状态的 MenuItemStack
        MenuItemStack disabledItem = inventoryManager.loadItemStack(config, path + ".disabled-item");

        return new ToggleButton(this.plugin, settingKey, enabledItem, disabledItem);
    }
}
```

### YAML 配置

```yaml
items:
  notifications-toggle:
    type: MY_TOGGLE
    slot: 11
    setting-key: "notifications"
    enabled-item:
      material: LIME_DYE
      name: "&a&l通知: %status%"
      lore:
        - "&7您将接收游戏通知"
        - ""
        - "&e点击%action%"
    disabled-item:
      material: GRAY_DYE
      name: "&c&l通知: %status%"
      lore:
        - "&7通知功能已关闭"
        - ""
        - "&e点击%action%"
```

---

## MenuItemStack YAML 属性

YAML 配置中支持的物品属性：

```yaml
item:
  # 材质
  material: DIAMOND_SWORD

  # 显示名称
  name: "&6&l传奇之剑"

  # 描述文本（多行）
  lore:
    - "&7一把强大的武器"
    - "&7伤害: &c+50"

  # 数量
  amount: 1

  # 自定义模型数据 (1.14+)
  modelId: 12345

  # 耐久度/损伤值
  durability: 0

  # 附魔
  enchants:
    - SHARPNESS,5
    - UNBREAKING,3

  # 物品标志（隐藏属性）
  flags:
    - HIDE_ENCHANTS
    - HIDE_ATTRIBUTES

  # 发光效果（无附魔文本）
  glow: true

  # 玩家头颅
  playerHead: "%player%"
  # 或自定义纹理 URL
  url: "eyJ0ZXh0dXJlcyI6..."

  # 药水效果
  potion: SPEED
  potionExtended: true
  potionUpgraded: false

  # 皮革护甲染色
  color: "#FF5555"

  # 旗帜图案
  banner:
    - RED,STRIPE_TOP
    - BLUE,CROSS
```

---

## 最佳实践

- **善用占位符**：充分利用占位符系统实现动态内容
- **合理缓存**：对静态物品使用 `build(player, true)` 启用缓存
- **封装占位符**：为常用占位符创建辅助类提高复用性
- **空值处理**：为占位符值提供默认回退方案
- **描述精简**：避免过长 lore 导致显示异常
- **兼容测试**：确保 PlaceholderAPI 占位符正常工作

---

## 后续学习

- 创建使用 MenuItemStack 的 [自定义按钮](./api-buttons)
- 开发支持动态物品的 [分页按钮](./api-paginate-button)
- 结合 [玩家数据](./api-player-data) 实现持久化状态