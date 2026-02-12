---
sidebar_position: 2
title: 库存界面 API  
description: 通过编程方式打开与管理库存界面
---

# 库存界面 API（Inventory API）


## 概述
学习如何使用 zMenu API 以编程方式打开和管理库存界面（Inventory）。

## 库存管理器（InventoryManager）
`InventoryManager` 是操作库存界面的核心接口。

```java
InventoryManager manager = menuPlugin.getInventoryManager();
```

## 获取库存界面

### 按名称获取
```java
// 返回 Optional<Inventory>
Optional<Inventory> inventory = manager.getInventory("shop");

// 检查库存是否存在
if (inventory.isPresent()) {
    Inventory inv = inventory.get();
    // 操作库存对象
}

// 或使用 ifPresent 简化处理
manager.getInventory("shop").ifPresent(inv -> {
    // 操作库存对象
});
```

### 获取全部库存
```java
// 获取所有已加载的库存界面
Collection<Inventory> allInventories = manager.getInventories();

for (Inventory inventory : allInventories) {
    String name = inventory.getName();
    int size = inventory.size();
    // 处理库存对象
}
```

### 检查库存是否存在
```java
boolean exists = manager.getInventory("shop").isPresent();
```

## 打开库存界面

### 基础打开方式
```java
manager.getInventory("shop").ifPresent(inv -> {
    manager.openInventory(player, inv);
});
```

### 打开指定页码
```java
manager.getInventory("catalog").ifPresent(inv -> {
    manager.openInventory(player, inv, 3); // 打开第 3 页
});
```

### 携带参数打开
向库存传递自定义参数，可在占位符中使用：
```java
List<String> arguments = new ArrayList<>();
arguments.add("swords");      // 参数 1
arguments.add("diamond");     // 参数 2

manager.getInventory("category-shop").ifPresent(inv -> {
    manager.openInventory(player, inv, 1, arguments);
});
```

### 为其他玩家打开
```java
Player target = Bukkit.getPlayer("Notch");

manager.getInventory("admin-view").ifPresent(inv -> {
    manager.openInventory(target, inv);
});
```

## 库存接口（Inventory Interface）
`Inventory` 接口提供已加载库存的元数据信息：
```java
manager.getInventory("shop").ifPresent(inv -> {
    // 获取库存属性
    String name = inv.getName();           // 显示名称
    String fileName = inv.getFileName();   // 文件名（不含扩展名）
    int size = inv.size();                 // 库存大小（9-54 格）
    Plugin plugin = inv.getPlugin();       // 注册该库存的插件

    // 获取按钮列表
    List<Button> buttons = inv.getButtons();

    // 检查库存是否支持分页
    int maxPage = inv.getMaxPage(player);
});
```

## 创建库存引擎（InventoryEngine）
如需更精细的控制，可直接操作 `InventoryEngine`：
```java
manager.getInventory("shop").ifPresent(inv -> {
    // 为当前玩家创建库存引擎实例
    InventoryEngine engine = manager.createInventoryEngine(player, inv);

    // 打开库存
    engine.open();

    // 或打开指定页码
    engine.open(2);
});
```

## 关闭库存界面

### 关闭玩家当前库存
```java
// 关闭玩家当前打开的库存界面
player.closeInventory();
```

### 检查玩家是否打开 zMenu 库存
```java
// 检查玩家是否正打开任意 zMenu 库存界面
boolean hasMenuOpen = manager.hasPlayerInventory(player);
```

## 操作插件专属库存
当多个插件注册同名库存时，需指定目标插件。

### 通过插件实例获取
```java
// 使用本插件实例获取专属库存
Optional<Inventory> inventory = manager.getInventory(myPlugin, "shop");

inventory.ifPresent(inv -> {
    manager.openInventory(player, inv);
});
```

### 通过插件名称获取
```java
// 通过插件名称获取指定插件的库存
Optional<Inventory> inventory = manager.getInventory("shop", "MyPlugin");

// 示例：获取 zAuctionHouse 插件的拍卖行库存
Optional<Inventory> auctionInventory = manager.getInventory("auction", "zAuctionHouse");

auctionInventory.ifPresent(inv -> {
    manager.openInventory(player, inv);
});
```

## 完整示例：插件集成服务
```java
public class InventoryService {

    private final Plugin plugin;
    private final InventoryManager inventoryManager;

    public InventoryService(Plugin plugin, MenuPlugin menuPlugin) {
        this.plugin = plugin;
        this.inventoryManager = menuPlugin.getInventoryManager();
    }

    /**
     * 打开本插件注册的库存界面
     */
    public void openOwnInventory(Player player, String inventoryName) {
        inventoryManager.getInventory(plugin, inventoryName).ifPresent(inv -> {
            inventoryManager.openInventory(player, inv);
        });
    }

    /**
     * 打开其他插件注册的库存界面
     */
    public void openExternalInventory(Player player, String pluginName, String inventoryName) {
        inventoryManager.getInventory(inventoryName, pluginName).ifPresent(inv -> {
            inventoryManager.openInventory(player, inv);
        });
    }

    /**
     * 打开支持分页的库存界面
     */
    public void openInventoryAtPage(Player player, String inventoryName, int page) {
        inventoryManager.getInventory(plugin, inventoryName).ifPresent(inv -> {
            inventoryManager.openInventory(player, inv, page);
        });
    }

    /**
     * 打开库存并保留导航历史
     * 允许玩家返回至上一个库存界面
     */
    public void openWithHistory(Player player, String inventoryName, int page) {
        inventoryManager.getInventory(plugin, inventoryName).ifPresent(inv -> {
            inventoryManager.openInventoryWithOldInventories(player, inv, page);
        });
    }
}
```

## 使用枚举管理库存名称
推荐使用枚举集中管理库存文件名：
```java
public enum Inventories {

    MAIN_MENU("main-menu"),
    SHOP("shop"),
    SETTINGS("settings"),
    CONFIRM("confirm");

    private final String fileName;

    Inventories(String fileName) {
        this.fileName = fileName;
    }

    public String getFileName() {
        return fileName;
    }
}
```

配合加载器使用：
```java
public class MyInventoryLoader {

    private final Plugin plugin;
    private final InventoryManager inventoryManager;

    public MyInventoryLoader(Plugin plugin, InventoryManager inventoryManager) {
        this.plugin = plugin;
        this.inventoryManager = inventoryManager;
    }

    public void openInventory(Player player, Inventories inventory) {
        openInventory(player, inventory, 1);
    }

    public void openInventory(Player player, Inventories inventory, int page) {
        var optional = inventoryManager.getInventory(plugin, inventory.getFileName());

        if (optional.isEmpty()) {
            plugin.getLogger().warning("库存未找到: " + inventory.getFileName());
            player.sendMessage("§c库存界面不存在！");
            return;
        }

        inventoryManager.openInventoryWithOldInventories(player, optional.get(), page);
    }
}
```

## 刷新库存界面
```java
// 库存刷新通常由内部机制自动处理
// 可通过按钮动作或事件触发界面更新
```

## 应用示例：商店系统
```java
public class ShopManager {

    private final MenuPlugin menuPlugin;

    public ShopManager(MenuPlugin menuPlugin) {
        this.menuPlugin = menuPlugin;
    }

    public void openShop(Player player) {
        InventoryManager manager = menuPlugin.getInventoryManager();

        manager.getInventory("shop-main").ifPresent(inv -> {
            manager.openInventory(player, inv);
        });
    }

    public void openCategory(Player player, String category) {
        InventoryManager manager = menuPlugin.getInventoryManager();

        List<String> args = Collections.singletonList(category);

        manager.getInventory("shop-category").ifPresent(inv -> {
            manager.openInventory(player, inv, 1, args);
        });
    }

    public void openPlayerShop(Player viewer, Player shopOwner) {
        InventoryManager manager = menuPlugin.getInventoryManager();

        List<String> args = Arrays.asList(
            shopOwner.getName(),
            shopOwner.getUniqueId().toString()
        );

        manager.getInventory("player-shop").ifPresent(inv -> {
            manager.openInventory(viewer, inv, 1, args);
        });
    }
}
```

## 应用示例：命令打开菜单
```java
public class MenuCommand implements CommandExecutor {

    private final MenuPlugin menuPlugin;

    public MenuCommand(MenuPlugin menuPlugin) {
        this.menuPlugin = menuPlugin;
    }

    @Override
    public boolean onCommand(CommandSender sender, Command command,
                            String label, String[] args) {
        if (!(sender instanceof Player)) {
            sender.sendMessage("仅限玩家使用！");
            return true;
        }

        Player player = (Player) sender;
        InventoryManager manager = menuPlugin.getInventoryManager();

        if (args.length == 0) {
            // 打开默认菜单
            manager.getInventory("main-menu").ifPresent(inv -> {
                manager.openInventory(player, inv);
            });
        } else {
            // 打开指定菜单
            String menuName = args[0];
            Optional<Inventory> inventory = manager.getInventory(menuName);

            if (inventory.isPresent()) {
                manager.openInventory(player, inventory.get());
            } else {
                player.sendMessage("菜单不存在: " + menuName);
            }
        }

        return true;
    }
}
```

## 应用示例：事件触发打开菜单
```java
public class JoinListener implements Listener {

    private final MenuPlugin menuPlugin;
    private final Plugin plugin;

    public JoinListener(Plugin plugin, MenuPlugin menuPlugin) {
        this.plugin = plugin;
        this.menuPlugin = menuPlugin;
    }

    @EventHandler
    public void onPlayerJoin(PlayerJoinEvent event) {
        Player player = event.getPlayer();

        // 延迟 1 秒打开欢迎菜单
        Bukkit.getScheduler().runTaskLater(plugin, () -> {
            if (player.isOnline()) {
                InventoryManager manager = menuPlugin.getInventoryManager();

                manager.getInventory("welcome-menu").ifPresent(inv -> {
                    manager.openInventory(player, inv);
                });
            }
        }, 20L); // 20 ticks = 1 秒
    }
}
```

## 最佳实践
- **始终处理 Optional**：`getInventory()` 返回 `Optional`，需妥善处理空值情况
- **验证玩家在线状态**：打开前确认玩家仍在线
- **使用语义化命名**：库存名称应与配置文件名严格对应
- **处理缺失库存**：库存不存在时应提供用户反馈
- **避免缓存库存对象**：按需获取最新库存引用

## 后续学习
- 创建 [自定义按钮](./api-buttons)
- 创建 [自定义动作](./api-actions)
- 操作 [玩家数据](./api-player-data)