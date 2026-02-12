---
sidebar_position: 6
title: 事件系统
description: 在您的插件中监听 zMenu 事件
---

# 事件系统（API Events）



## 事件概述

zMenu 在运行时会触发多种事件，您可以在插件中注册监听器进行响应。这使您能够对菜单操作、按钮加载等行为进行自定义处理与扩展。

## 可用事件列表

| 事件类型 | 说明 |
|----------|------|
| `ButtonLoadEvent` | 从配置文件加载按钮时触发 |
| `InventoryLoadEvent` | 从配置文件加载菜单时触发 |
| `PlayerOpenInventoryEvent` | 玩家打开 zMenu 菜单时触发（可取消） |
| `ButtonLoaderRegisterEvent` | 按钮加载器注册到 ButtonManager 时触发 |
| `ZMenuItemsLoad` | 自定义物品配置加载完成时触发 |

---

## ButtonLoadEvent

当按钮从配置文件加载时触发。

```java
import fr.maxlego08.menu.api.event.ButtonLoadEvent;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;

public class ButtonListener implements Listener {

    @EventHandler
    public void onButtonLoad(ButtonLoadEvent event) {
        Button button = event.getButton();
        String buttonName = button.getName();

        getLogger().info("按钮已加载: " + buttonName);

        // 可在此修改按钮属性或添加自定义逻辑
    }
}
```

### 典型应用场景
- 记录所有已加载的按钮
- 验证按钮配置的合法性
- 为按钮添加自定义处理逻辑

---

## InventoryLoadEvent

当菜单从配置文件加载时触发。

```java
import fr.maxlego08.menu.api.event.InventoryLoadEvent;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;

public class InventoryListener implements Listener {

    @EventHandler
    public void onInventoryLoad(InventoryLoadEvent event) {
        Inventory inventory = event.getInventory();
        String name = inventory.getName();
        String fileName = inventory.getFileName();
        int size = inventory.size();

        getLogger().info("菜单已加载: " + fileName);
        getLogger().info("  标题: " + name);
        getLogger().info("  容器大小: " + size);
        getLogger().info("  按钮数量: " + inventory.getButtons().size());
    }
}
```

### 典型应用场景
- 追踪已加载的菜单资源
- 验证菜单配置完整性
- 对菜单进行后置处理

---

## PlayerOpenInventoryEvent

玩家打开 zMenu 菜单时触发。**此事件可被取消**。

```java
import fr.maxlego08.menu.api.event.PlayerOpenInventoryEvent;
import org.bukkit.entity.Player;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;

public class OpenInventoryListener implements Listener {

    @EventHandler
    public void onPlayerOpenInventory(PlayerOpenInventoryEvent event) {
        Player player = event.getPlayer();
        Inventory inventory = event.getInventory();

        getLogger().info(player.getName() + " 打开了菜单: " + inventory.getFileName());

        // 根据条件阻止菜单打开
        if (!player.hasPermission("myplugin.use.menus")) {
            event.setCancelled(true);
            player.sendMessage("您没有使用菜单的权限！");
        }
    }
}
```

### 取消事件示例

```java
@EventHandler
public void onPlayerOpenInventory(PlayerOpenInventoryEvent event) {
    Player player = event.getPlayer();
    Inventory inventory = event.getInventory();

    // 阻止非 VIP 玩家访问 VIP 菜单
    if (inventory.getFileName().startsWith("vip-") &&
        !player.hasPermission("server.vip")) {
        event.setCancelled(true);
        player.sendMessage(ChatColor.RED + "该菜单仅限 VIP 用户使用！");
    }
}
```

### 典型应用场景
- 记录玩家菜单访问行为
- 基于权限/状态限制菜单访问
- 追踪玩家活动数据
- 在打开前执行自定义校验逻辑

---

## ButtonLoaderRegisterEvent

当按钮加载器注册到 ButtonManager 时触发。

```java
import fr.maxlego08.menu.api.event.ButtonLoaderRegisterEvent;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;

public class LoaderListener implements Listener {

    @EventHandler
    public void onButtonLoaderRegister(ButtonLoaderRegisterEvent event) {
        ButtonLoader loader = event.getButtonLoader();
        String name = loader.getName();

        getLogger().info("按钮加载器已注册: " + name);
    }
}
```

### 典型应用场景
- 追踪可用的按钮类型
- 记录插件集成情况
- 调试加载器注册流程

---

## ZMenuItemsLoad

当 zMenu 完成自定义物品配置加载时触发。

```java
import fr.maxlego08.menu.api.event.ZMenuItemsLoad;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;

public class ItemsLoadListener implements Listener {

    @EventHandler
    public void onItemsLoad(ZMenuItemsLoad event) {
        getLogger().info("zMenu 自定义物品加载完成！");

        // 可在此添加自定义物品或处理逻辑
    }
}
```

---

## 注册事件监听器

在插件的 `onEnable()` 方法中注册监听器：

```java
@Override
public void onEnable() {
    // 注册事件监听器
    getServer().getPluginManager().registerEvents(new ButtonListener(), this);
    getServer().getPluginManager().registerEvents(new InventoryListener(), this);
    getServer().getPluginManager().registerEvents(new OpenInventoryListener(), this);
}
```

---

## 完整示例：菜单使用分析

追踪菜单使用数据用于分析：

```java
package com.example.analytics;

import fr.maxlego08.menu.api.Inventory;
import fr.maxlego08.menu.api.event.PlayerOpenInventoryEvent;
import org.bukkit.entity.Player;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;
import org.bukkit.plugin.java.JavaPlugin;

import java.util.HashMap;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;

public class MenuAnalytics implements Listener {

    private final JavaPlugin plugin;
    private final Map<String, Integer> menuOpenCounts = new ConcurrentHashMap<>();
    private final Map<UUID, Long> lastMenuOpen = new ConcurrentHashMap<>();

    public MenuAnalytics(JavaPlugin plugin) {
        this.plugin = plugin;
    }

    @EventHandler
    public void onMenuOpen(PlayerOpenInventoryEvent event) {
        Player player = event.getPlayer();
        Inventory inventory = event.getInventory();
        String menuName = inventory.getFileName();

        // 累计打开次数
        menuOpenCounts.merge(menuName, 1, Integer::sum);

        // 记录最后打开时间
        lastMenuOpen.put(player.getUniqueId(), System.currentTimeMillis());

        // 调试日志
        plugin.getLogger().info(String.format(
            "[Analytics] %s 打开了 %s (累计打开次数: %d)",
            player.getName(),
            menuName,
            menuOpenCounts.get(menuName)
        ));
    }

    public int getOpenCount(String menuName) {
        return menuOpenCounts.getOrDefault(menuName, 0);
    }

    public Map<String, Integer> getAllStats() {
        return new HashMap<>(menuOpenCounts);
    }

    public void printStats() {
        plugin.getLogger().info("=== 菜单使用统计 ===");
        menuOpenCounts.entrySet().stream()
            .sorted((a, b) -> b.getValue().compareTo(a.getValue()))
            .forEach(entry -> {
                plugin.getLogger().info(entry.getKey() + ": " + entry.getValue() + " 次");
            });
    }
}
```

---

## 完整示例：菜单访问控制

基于自定义条件控制菜单访问权限：

```java
package com.example.access;

import fr.maxlego08.menu.api.Inventory;
import fr.maxlego08.menu.api.event.PlayerOpenInventoryEvent;
import org.bukkit.ChatColor;
import org.bukkit.entity.Player;
import org.bukkit.event.EventHandler;
import org.bukkit.event.EventPriority;
import org.bukkit.event.Listener;

import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;
import java.util.UUID;

public class MenuAccessController implements Listener {

    // 需要 VIP 权限的菜单集合
    private final Set<String> vipMenus = new HashSet<>();

    // 菜单冷却时间配置（毫秒）
    private final Map<String, Long> menuCooldowns = new HashMap<>();
    private final Map<UUID, Map<String, Long>> playerCooldowns = new HashMap<>();

    public MenuAccessController() {
        // 配置 VIP 菜单
        vipMenus.add("vip-shop");
        vipMenus.add("vip-rewards");
        vipMenus.add("premium-features");

        // 配置冷却时间
        menuCooldowns.put("daily-rewards", 60000L);  // 1 分钟
        menuCooldowns.put("spin-wheel", 300000L);    // 5 分钟
    }

    @EventHandler(priority = EventPriority.HIGH)
    public void onMenuOpen(PlayerOpenInventoryEvent event) {
        if (event.isCancelled()) return;

        Player player = event.getPlayer();
        Inventory inventory = event.getInventory();
        String menuName = inventory.getFileName();

        // VIP 权限校验
        if (vipMenus.contains(menuName) && !player.hasPermission("server.vip")) {
            event.setCancelled(true);
            player.sendMessage(ChatColor.RED + "该菜单需要 VIP 身份！");
            return;
        }

        // 冷却时间校验
        if (menuCooldowns.containsKey(menuName)) {
            long cooldown = menuCooldowns.get(menuName);
            long lastOpen = getLastOpen(player.getUniqueId(), menuName);
            long now = System.currentTimeMillis();

            if (now - lastOpen < cooldown) {
                event.setCancelled(true);
                long remaining = (cooldown - (now - lastOpen)) / 1000;
                player.sendMessage(ChatColor.RED + "请等待 " + remaining +
                    " 秒后再打开此菜单！");
                return;
            }

            // 更新最后打开时间
            setLastOpen(player.getUniqueId(), menuName, now);
        }
    }

    private long getLastOpen(UUID uuid, String menu) {
        return playerCooldowns
            .getOrDefault(uuid, new HashMap<>())
            .getOrDefault(menu, 0L);
    }

    private void setLastOpen(UUID uuid, String menu, long time) {
        playerCooldowns
            .computeIfAbsent(uuid, k -> new HashMap<>())
            .put(menu, time);
    }

    public void addVipMenu(String menuName) {
        vipMenus.add(menuName);
    }

    public void setMenuCooldown(String menuName, long milliseconds) {
        menuCooldowns.put(menuName, milliseconds);
    }
}
```

---

## 完整示例：菜单操作审计日志

记录所有菜单交互行为用于审计：

```java
package com.example.logging;

import fr.maxlego08.menu.api.event.InventoryLoadEvent;
import fr.maxlego08.menu.api.event.PlayerOpenInventoryEvent;
import fr.maxlego08.menu.api.event.ButtonLoadEvent;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;
import org.bukkit.plugin.java.JavaPlugin;

import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;
import java.text.SimpleDateFormat;
import java.util.Date;

public class MenuLogger implements Listener {

    private final JavaPlugin plugin;
    private final SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    private final File logFile;

    public MenuLogger(JavaPlugin plugin) {
        this.plugin = plugin;
        this.logFile = new File(plugin.getDataFolder(), "menu-log.txt");
    }

    @EventHandler
    public void onInventoryLoad(InventoryLoadEvent event) {
        log("INVENTORY_LOAD", "加载菜单: " + event.getInventory().getFileName());
    }

    @EventHandler
    public void onButtonLoad(ButtonLoadEvent event) {
        log("BUTTON_LOAD", "加载按钮: " + event.getButton().getName());
    }

    @EventHandler
    public void onMenuOpen(PlayerOpenInventoryEvent event) {
        String message = String.format("玩家 %s 打开了菜单 %s",
            event.getPlayer().getName(),
            event.getInventory().getFileName());
        log("MENU_OPEN", message);
    }

    private void log(String type, String message) {
        String timestamp = dateFormat.format(new Date());
        String logLine = String.format("[%s] [%s] %s", timestamp, type, message);

        // 控制台日志
        plugin.getLogger().info(logLine);

        // 文件日志
        try (PrintWriter writer = new PrintWriter(new FileWriter(logFile, true))) {
            writer.println(logLine);
        } catch (IOException e) {
            plugin.getLogger().severe("写入日志文件失败: " + e.getMessage());
        }
    }
}
```

---

## 事件优先级（Event Priorities）

使用事件优先级控制监听器执行顺序：

```java
// 最先执行（优先于其他插件）
@EventHandler(priority = EventPriority.LOWEST)
public void onMenuOpenFirst(PlayerOpenInventoryEvent event) {
    // ...
}

// 最后执行（晚于其他插件）
@EventHandler(priority = EventPriority.HIGHEST)
public void onMenuOpenLast(PlayerOpenInventoryEvent event) {
    // ...
}

// 即使事件已被取消也执行（仅用于监控）
@EventHandler(priority = EventPriority.MONITOR, ignoreCancelled = false)
public void onMenuOpenMonitor(PlayerOpenInventoryEvent event) {
    // 仅观察，不修改事件状态
}
```

---

## 最佳实践

| 建议 | 说明 |
|------|------|
| **避免不必要的事件阻断** | 仅在必要时取消事件，避免影响其他插件功能 |
| **合理设置优先级** | `LOWEST` 用于修改，`MONITOR` 用于日志记录 |
| **异常处理** | 确保事件处理器不会因异常导致崩溃 |
| **保持轻量级** | 避免在事件处理器中执行耗时操作 |
| **正确注册监听器** | 使用 PluginManager 规范注册 |

---

## 后续学习

- 创建 [自定义按钮类型](./api-buttons)
- 开发 [自定义动作](./api-actions)
- 操作 [玩家数据](./api-player-data)
- 查阅 [API 入门指南](./api-introduction)