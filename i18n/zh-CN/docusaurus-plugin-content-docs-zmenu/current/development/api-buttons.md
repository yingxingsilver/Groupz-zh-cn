---
sidebar_position: 3
title: 自定义按钮
description: 在 zMenu 中创建自定义按钮类型
---

# 自定义按钮（Custom Buttons）



## 概述

自定义按钮允许您扩展 zMenu 的功能，实现自定义的按钮行为。该过程包含以下三个核心步骤：

1. 创建 `ButtonLoader` 用于解析 YAML 配置
2. 实现 `Button` 接口定义按钮行为逻辑
3. 通过 `ButtonManager` 注册按钮加载器

## ButtonManager

通过以下方式获取按钮管理器以注册自定义按钮：

```java
ButtonManager buttonManager = menuPlugin.getButtonManager();
```

## 创建按钮加载器（ButtonLoader）

`ButtonLoader` 接口定义了如何从 YAML 配置中加载自定义按钮：

```java
import fr.maxlego08.menu.api.button.Button;
import fr.maxlego08.menu.api.loader.ButtonLoader;
import org.bukkit.configuration.file.YamlConfiguration;
import org.bukkit.plugin.Plugin;

public class MyButtonLoader implements ButtonLoader {

    @Override
    public String getName() {
        return "MY_CUSTOM_BUTTON"; // YAML 中使用的类型名称
    }

    @Override
    public Button load(YamlConfiguration configuration, String path, Plugin plugin) {
        // 从配置中读取自定义属性
        String customProperty = configuration.getString(path + ".custom-property", "default");
        int customNumber = configuration.getInt(path + ".custom-number", 0);
        boolean customFlag = configuration.getBoolean(path + ".custom-flag", false);

        // 创建并返回按钮实例
        return new MyCustomButton(customProperty, customNumber, customFlag);
    }
}
```

## 实现按钮逻辑（Button Implementation）

实现 `Button` 接口（或继承基础类）以定义按钮点击行为：

```java
import fr.maxlego08.menu.api.button.Button;
import fr.maxlego08.menu.api.button.DefaultButtonValue;
import fr.maxlego08.menu.inventory.inventories.InventoryDefault;
import org.bukkit.entity.Player;
import org.bukkit.event.inventory.InventoryClickEvent;

public class MyCustomButton extends DefaultButtonValue implements Button {

    private final String customProperty;
    private final int customNumber;
    private final boolean customFlag;

    public MyCustomButton(String customProperty, int customNumber, boolean customFlag) {
        this.customProperty = customProperty;
        this.customNumber = customNumber;
        this.customFlag = customFlag;
    }

    @Override
    public String getName() {
        return "MY_CUSTOM_BUTTON";
    }

    @Override
    public void onClick(Player player, InventoryClickEvent event,
                       InventoryDefault inventory, int slot) {
        // 自定义点击逻辑
        player.sendMessage("自定义按钮已点击！");
        player.sendMessage("属性: " + customProperty);
        player.sendMessage("数值: " + customNumber);
        player.sendMessage("标志: " + customFlag);

        // 基于配置执行不同操作
        if (customFlag) {
            player.sendMessage("标志已启用！");
        }

        // 可与界面进行交互
        // inventory.refresh(player);
    }
}
```

## 注册自定义按钮

在插件启用时注册按钮加载器：

```java
@Override
public void onEnable() {
    MenuPlugin menuPlugin = (MenuPlugin) Bukkit.getPluginManager().getPlugin("zMenu");

    if (menuPlugin != null) {
        // 注册自定义按钮
        menuPlugin.getButtonManager().register(new MyButtonLoader());
        getLogger().info("已注册自定义按钮: MY_CUSTOM_BUTTON");
    }
}
```

## YAML 配置示例

在菜单配置文件中使用自定义按钮：

```yaml
items:
  my-custom-item:
    type: MY_CUSTOM_BUTTON
    slot: 13
    custom-property: "Hello World"
    custom-number: 42
    custom-flag: true
    item:
      material: DIAMOND
      name: "&b&l自定义按钮"
      lore:
        - "&7这是一个自定义按钮！"
```

## 完整示例：传送按钮（Teleport Button）

### TeleportButtonLoader.java

```java
package com.example.buttons;

import fr.maxlego08.menu.api.button.Button;
import fr.maxlego08.menu.api.loader.ButtonLoader;
import org.bukkit.Bukkit;
import org.bukkit.Location;
import org.bukkit.World;
import org.bukkit.configuration.file.YamlConfiguration;
import org.bukkit.plugin.Plugin;

public class TeleportButtonLoader implements ButtonLoader {

    @Override
    public String getName() {
        return "TELEPORT";
    }

    @Override
    public Button load(YamlConfiguration config, String path, Plugin plugin) {
        String worldName = config.getString(path + ".world", "world");
        double x = config.getDouble(path + ".x", 0);
        double y = config.getDouble(path + ".y", 64);
        double z = config.getDouble(path + ".z", 0);
        float yaw = (float) config.getDouble(path + ".yaw", 0);
        float pitch = (float) config.getDouble(path + ".pitch", 0);
        String message = config.getString(path + ".message", "&a传送成功！");

        World world = Bukkit.getWorld(worldName);
        Location location = new Location(world, x, y, z, yaw, pitch);

        return new TeleportButton(location, message);
    }
}
```

### TeleportButton.java

```java
package com.example.buttons;

import fr.maxlego08.menu.api.button.Button;
import fr.maxlego08.menu.api.button.DefaultButtonValue;
import fr.maxlego08.menu.inventory.inventories.InventoryDefault;
import org.bukkit.ChatColor;
import org.bukkit.Location;
import org.bukkit.entity.Player;
import org.bukkit.event.inventory.InventoryClickEvent;

public class TeleportButton extends DefaultButtonValue implements Button {

    private final Location location;
    private final String message;

    public TeleportButton(Location location, String message) {
        this.location = location;
        this.message = message;
    }

    @Override
    public String getName() {
        return "TELEPORT";
    }

    @Override
    public void onClick(Player player, InventoryClickEvent event,
                       InventoryDefault inventory, int slot) {
        // 先关闭界面
        player.closeInventory();

        // 执行传送
        player.teleport(location);

        // 发送格式化消息
        player.sendMessage(ChatColor.translateAlternateColorCodes('&', message));
    }
}
```

### YAML 使用示例

```yaml
items:
  spawn-teleport:
    type: TELEPORT
    slot: 13
    world: "world"
    x: 0
    y: 100
    z: 0
    yaw: 90
    pitch: 0
    message: "&a您已被传送到出生点！"
    item:
      material: ENDER_PEARL
      name: "&5&l传送至出生点"
      lore:
        - "&7点击进行传送"
```

## 完整示例：计数按钮（Counter Button）

实现按玩家追踪点击次数的按钮：

### CounterButtonLoader.java

```java
package com.example.buttons;

import fr.maxlego08.menu.api.button.Button;
import fr.maxlego08.menu.api.loader.ButtonLoader;
import org.bukkit.configuration.file.YamlConfiguration;
import org.bukkit.plugin.Plugin;

import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

public class CounterButtonLoader implements ButtonLoader {

    // 所有计数按钮共享的计数器存储
    private static final Map<UUID, Integer> counters = new HashMap<>();

    @Override
    public String getName() {
        return "COUNTER";
    }

    @Override
    public Button load(YamlConfiguration config, String path, Plugin plugin) {
        int maxCount = config.getInt(path + ".max-count", 10);
        String rewardCommand = config.getString(path + ".reward-command", "");

        return new CounterButton(counters, maxCount, rewardCommand);
    }
}
```

### CounterButton.java

```java
package com.example.buttons;

import fr.maxlego08.menu.api.button.Button;
import fr.maxlego08.menu.api.button.DefaultButtonValue;
import fr.maxlego08.menu.inventory.inventories.InventoryDefault;
import org.bukkit.Bukkit;
import org.bukkit.entity.Player;
import org.bukkit.event.inventory.InventoryClickEvent;

import java.util.Map;
import java.util.UUID;

public class CounterButton extends DefaultButtonValue implements Button {

    private final Map<UUID, Integer> counters;
    private final int maxCount;
    private final String rewardCommand;

    public CounterButton(Map<UUID, Integer> counters, int maxCount, String rewardCommand) {
        this.counters = counters;
        this.maxCount = maxCount;
        this.rewardCommand = rewardCommand;
    }

    @Override
    public String getName() {
        return "COUNTER";
    }

    @Override
    public void onClick(Player player, InventoryClickEvent event,
                       InventoryDefault inventory, int slot) {
        UUID uuid = player.getUniqueId();

        // 获取当前计数
        int currentCount = counters.getOrDefault(uuid, 0);
        currentCount++;

        // 更新计数器
        counters.put(uuid, currentCount);

        player.sendMessage("§a点击次数: " + currentCount + "/" + maxCount);

        // 检查是否达到最大值
        if (currentCount >= maxCount) {
            player.sendMessage("§6已达到最大点击次数！");

            // 执行奖励命令
            if (!rewardCommand.isEmpty()) {
                String command = rewardCommand.replace("%player%", player.getName());
                Bukkit.dispatchCommand(Bukkit.getConsoleSender(), command);
            }

            // 重置计数器
            counters.put(uuid, 0);
        }

        // 刷新界面以更新显示
        inventory.refresh(player);
    }
}
```

## 高级用法：动态物品显示

通过重写 `getCustomItemStack` 方法实现基于玩家状态的动态物品显示：

```java
@Override
public ItemStack getCustomItemStack(Player player) {
    // 根据玩家状态返回自定义物品
    int count = counters.getOrDefault(player.getUniqueId(), 0);

    ItemStack item = new ItemStack(Material.PAPER);
    ItemMeta meta = item.getItemMeta();
    meta.setDisplayName("§6计数器: " + count + "/" + maxCount);
    item.setItemMeta(meta);

    return item;
}
```

## 最佳实践

| 实践要点 | 说明 |
|----------|------|
| **命名规范** | 使用唯一且具有描述性的类型名称（建议全大写） |
| **配置验证** | 检查缺失或无效的配置值，提供合理的默认值 |
| **空值处理** | 对可选属性使用默认值，避免 `NullPointerException` |
| **职责单一** | 一个按钮类型应专注于单一功能 |
| **文档完善** | 详细说明必需的配置选项及参数含义 |
| **边界测试** | 测试空配置、缺失属性等边缘情况 |

## 后续学习

- 创建 [自定义动作（Custom Actions）](./api-actions)
- 操作 [玩家数据（Player Data）](./api-player-data)
- 监听 [事件系统（Events）](./api-events)