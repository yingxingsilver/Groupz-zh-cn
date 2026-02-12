---
sidebar_position: 7
title: 分页按钮  
description: 在 zMenu 中创建分页按钮列表
---

# 分页按钮（Paginate Button）



## 概述

`PaginateButton` 是一个抽象类，用于简化分页内容按钮的创建。它自动处理以下功能：

- 跨多个槽位的物品分布
- 基于可用槽位的页码计算
- 通过 PREVIOUS/NEXT 按钮实现页面导航

## 基础结构

```java
import fr.maxlego08.menu.api.button.PaginateButton;
import fr.maxlego08.menu.api.engine.InventoryEngine;
import org.bukkit.entity.Player;
import org.bukkit.plugin.Plugin;

public class MyPaginatedButton extends PaginateButton {

    private final MyPlugin plugin;

    public MyPaginatedButton(Plugin plugin) {
        this.plugin = (MyPlugin) plugin;
    }

    @Override
    public void onRender(Player player, InventoryEngine inventoryEngine) {
        // 获取物品列表
        List<MyItem> items = getItems(player);

        // 使用 paginate 辅助方法
        paginate(items, inventoryEngine, (slot, item) -> {
            // 创建显示用的 ItemStack
            ItemStack itemStack = createDisplayItem(item);

            // 向界面添加物品并设置点击处理器
            inventoryEngine.addItem(slot, itemStack)
                .setClick(event -> handleClick(player, item, event));
        });
    }

    @Override
    public int getPaginationSize(Player player) {
        // 返回需分页的物品总数
        return getItems(player).size();
    }

    private List<MyItem> getItems(Player player) {
        return plugin.getItemManager().getItems(player);
    }

    private ItemStack createDisplayItem(MyItem item) {
        // 创建并返回显示用的 ItemStack
        return item.toItemStack();
    }

    private void handleClick(Player player, MyItem item, InventoryClickEvent event) {
        // 处理点击事件
        player.sendMessage("您点击了: " + item.getName());
    }
}
```

## 核心方法

### `onRender(Player, InventoryEngine)`

界面渲染时调用。用于填充物品内容。

```java
@Override
public void onRender(Player player, InventoryEngine inventoryEngine) {
    List<MyItem> items = getMyItems(player);

    paginate(items, inventoryEngine, (slot, item) -> {
        ItemStack display = item.toItemStack();
        inventoryEngine.addItem(slot, display)
            .setClick(event -> processClick(player, item, event));
    });
}
```

### `getPaginationSize(Player)`

返回分页计算所需的物品总数。

```java
@Override
public int getPaginationSize(Player player) {
    return getMyItems(player).size();
}
```

### `paginate(List, InventoryEngine, BiConsumer)`

辅助方法，将物品按当前页分布到可用槽位。

```java
paginate(items, inventoryEngine, (slot, item) -> {
    // slot: 当前使用的库存槽位
    // item: 列表中的当前物品
});
```

### `getSlots()`

返回该按钮配置的槽位列表（来自 YAML 配置）。

```java
List<Integer> availableSlots = getSlots();
```

## 按钮注册

使用 `NoneLoader` 注册分页按钮：

```java
@Override
public void onEnable() {
    MenuPlugin menuPlugin = (MenuPlugin) Bukkit.getPluginManager().getPlugin("zMenu");

    if (menuPlugin != null) {
        ButtonManager buttonManager = menuPlugin.getButtonManager();

        // 使用 NoneLoader 注册仅含 Plugin 构造函数的按钮
        buttonManager.register(new NoneLoader(this, MyPaginatedButton.class, "MY_PAGINATED_BUTTON"));
    }
}
```

## YAML 配置示例

```yaml
name: " &6我的分页菜单 (%page%/%max-page%) "
size: 54

items:
  myItems:
    type: MY_PAGINATED_BUTTON
    slots:
      - 10-16
      - 19-25
      - 28-34
      - 37-43
    # 可选：无物品时的显示
    else:
      slots:
        - 22
      item:
        material: BARRIER
        name: " &c未找到物品 "
        lore:
          - " &7当前无可显示的物品。 "

  # 导航按钮
  previous:
    type: PREVIOUS
    is-permanent: true
    slot: 48
    item:
      material: ARROW
      name: " &e上一页 "
      lore:
        - " &7跳转至第 %page-1% 页 "

  next:
    type: NEXT
    is-permanent: true
    slot: 50
    item:
      material: ARROW
      name: " &e下一页 "
      lore:
        - " &7跳转至第 %page+1% 页 "
```

## 完整示例：商店物品按钮

**ShopItemsButton.java**

```java
package com.example.shop.buttons;

import fr.maxlego08.menu.api.button.PaginateButton;
import fr.maxlego08.menu.api.engine.InventoryEngine;
import com.example.shop.ShopPlugin;
import com.example.shop.ShopItem;
import org.bukkit.entity.Player;
import org.bukkit.event.inventory.ClickType;
import org.bukkit.event.inventory.InventoryClickEvent;
import org.bukkit.inventory.ItemStack;
import org.bukkit.inventory.meta.ItemMeta;
import org.bukkit.plugin.Plugin;

import java.util.ArrayList;
import java.util.List;
import java.util.function.Consumer;

public class ShopItemsButton extends PaginateButton {

    private final ShopPlugin plugin;

    public ShopItemsButton(Plugin plugin) {
        this.plugin = (ShopPlugin) plugin;
    }

    @Override
    public void onRender(Player player, InventoryEngine inventoryEngine) {
        List<ShopItem> items = plugin.getShopManager().getAvailableItems(player);

        paginate(items, inventoryEngine, (slot, shopItem) -> {
            ItemStack display = createShopDisplay(player, shopItem);
            inventoryEngine.addItem(slot, display)
                .setClick(createClickHandler(player, inventoryEngine, shopItem));
        });
    }

    @Override
    public int getPaginationSize(Player player) {
        return plugin.getShopManager().getAvailableItems(player).size();
    }

    private ItemStack createShopDisplay(Player player, ShopItem shopItem) {
        ItemStack item = shopItem.getItem().clone();
        ItemMeta meta = item.getItemMeta();

        if (meta != null) {
            // 在 lore 中添加价格信息
            List<String> lore = meta.hasLore() ? new ArrayList<>(meta.getLore()) : new ArrayList<>();
            lore.add(" ");
            lore.add("§7价格: §e" + shopItem.getFormattedPrice());
            lore.add(" ");
            lore.add("§e左键点击 §7购买 x1");
            lore.add("§e右键点击 §7购买 x64");
            lore.add("§eShift+点击 §7出售");
            meta.setLore(lore);
            item.setItemMeta(meta);
        }

        return item;
    }

    private Consumer<InventoryClickEvent> createClickHandler(
            Player player,
            InventoryEngine inventoryEngine,
            ShopItem shopItem) {

        return event -> {
            ClickType click = event.getClick();

            if (click == ClickType.LEFT) {
                // 购买 1 个
                plugin.getShopManager().buyItem(player, shopItem, 1);
            } else if (click == ClickType.RIGHT) {
                // 购买 64 个
                plugin.getShopManager().buyItem(player, shopItem, 64);
            } else if (click.isShiftClick()) {
                // 出售
                plugin.getShopManager().sellItem(player, shopItem);
            }

            // 刷新界面以更新显示
            inventoryEngine.updateInventory();
        };
    }
}
```

## 完整示例：在线玩家列表按钮

**OnlinePlayersButton.java**

```java
package com.example.admin.buttons;

import fr.maxlego08.menu.api.button.PaginateButton;
import fr.maxlego08.menu.api.engine.InventoryEngine;
import org.bukkit.Bukkit;
import org.bukkit.Material;
import org.bukkit.entity.Player;
import org.bukkit.event.inventory.InventoryClickEvent;
import org.bukkit.inventory.ItemStack;
import org.bukkit.inventory.meta.SkullMeta;
import org.bukkit.plugin.Plugin;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.function.Consumer;

public class OnlinePlayersButton extends PaginateButton {

    private final Plugin plugin;

    public OnlinePlayersButton(Plugin plugin) {
        this.plugin = plugin;
    }

    @Override
    public void onRender(Player player, InventoryEngine inventoryEngine) {
        List<Player> onlinePlayers = new ArrayList<>(Bukkit.getOnlinePlayers());

        // 从列表中移除当前查看者
        onlinePlayers.remove(player);

        paginate(onlinePlayers, inventoryEngine, (slot, targetPlayer) -> {
            ItemStack head = createPlayerHead(targetPlayer);
            inventoryEngine.addItem(slot, head)
                .setClick(createClickHandler(player, targetPlayer));
        });
    }

    @Override
    public int getPaginationSize(Player player) {
        // 减 1，因为移除了查看者自身
        return Math.max(0, Bukkit.getOnlinePlayers().size() - 1);
    }

    private ItemStack createPlayerHead(Player targetPlayer) {
        ItemStack head = new ItemStack(Material.PLAYER_HEAD);
        SkullMeta meta = (SkullMeta) head.getItemMeta();

        if (meta != null) {
            meta.setOwningPlayer(targetPlayer);
            meta.setDisplayName("§e" + targetPlayer.getName());
            meta.setLore(Arrays.asList(
                "§7世界: §f" + targetPlayer.getWorld().getName(),
                "§7生命值: §c" + (int) targetPlayer.getHealth() + "§7/§c20",
                " ",
                "§a左键点击 §7传送至该玩家",
                "§c右键点击 §7踢出该玩家"
            ));
            head.setItemMeta(meta);
        }

        return head;
    }

    private Consumer<InventoryClickEvent> createClickHandler(Player admin, Player target) {
        return event -> {
            if (event.isLeftClick()) {
                admin.closeInventory();
                admin.teleport(target.getLocation());
                admin.sendMessage("§a已传送至 " + target.getName());
            } else if (event.isRightClick()) {
                if (admin.hasPermission("admin.kick")) {
                    target.kickPlayer("被 " + admin.getName() + " 踢出");
                    admin.sendMessage("§c已踢出 " + target.getName());
                }
            }
        };
    }
}
```

## 高级用法：实时更新

在数据变更时实时更新分页物品：

```java
public class LiveItemsButton extends PaginateButton {

    private final MyPlugin plugin;

    public LiveItemsButton(Plugin plugin) {
        this.plugin = (MyPlugin) plugin;
    }

    @Override
    public void onRender(Player player, InventoryEngine inventoryEngine) {
        List<MyItem> items = plugin.getItemManager().getItems(player);

        paginate(items, inventoryEngine, (slot, item) -> {
            ItemStack display = item.toItemStack();
            inventoryEngine.addItem(slot, display)
                .setClick(createClick(player, inventoryEngine, slot, item, display));
        });
    }

    @Override
    public int getPaginationSize(Player player) {
        return plugin.getItemManager().getItems(player).size();
    }

    private Consumer<InventoryClickEvent> createClick(
            Player player,
            InventoryEngine inventoryEngine,
            int slot,
            MyItem item,
            ItemStack itemStack) {

        return event -> {
            // 处理点击
            boolean success = processItemClick(player, item, event);

            if (success) {
                // 仅更新当前槽位
                updateSlot(player, inventoryEngine, slot, item);
            }
        };
    }

    /**
     * 仅更新指定槽位，无需完全刷新界面
     */
    private void updateSlot(Player player, InventoryEngine inventoryEngine, int slot, MyItem item) {
        ItemStack newDisplay = item.toItemStack();
        inventoryEngine.getSpigotInventory().setItem(slot, newDisplay);
    }

    /**
     * 由外部事件调用，更新所有已打开的界面
     */
    public void updateAllInventories(MyItem changedItem, boolean wasAdded) {
        for (Player onlinePlayer : Bukkit.getOnlinePlayers()) {
            var holder = onlinePlayer.getOpenInventory().getTopInventory().getHolder();

            if (holder instanceof InventoryEngine inventoryEngine) {
                var buttons = inventoryEngine.getMenuInventory()
                    .getButtons(LiveItemsButton.class);

                if (!buttons.isEmpty()) {
                    // 触发界面刷新
                    inventoryEngine.updateInventory();
                }
            }
        }
    }
}
```

## 空状态处理

当无物品时显示备用物品：

```yaml
items:
  myItems:
    type: MY_PAGINATED_BUTTON
    slots:
      - 10-16
      - 19-25
    else:
      slots:
        - 13  # 居中槽位
      item:
        material: STRUCTURE_VOID
        name: " &c &l暂无物品 "
        lore:
          - " &7您尚未拥有任何物品。"
          - " "
          - " &e点击此处开始！"
        actions:
          - type: message
            messages:
              - " &a前往商店购买物品！"
```

## 最佳实践

- **精确返回数量**：`getPaginationSize()` 必须返回准确的物品总数
- **处理空列表**：使用 `else` 配置项处理空状态
- **使用点击处理器**：通过 `setClick()` 设置点击处理器以确保正确处理事件
- **缓存计算结果**：避免在每次渲染时重复计算耗时操作
- **高效更新**：使用 `updateInventory()` 替代重新打开界面
- **全面测试**：验证不同物品数量下的分页导航功能

## 后续步骤

- 学习 [MenuItemStack](./api-menu-item-stack) 以实现动态物品创建
- 创建 [自定义动作](./api-actions) 处理按钮点击
- 使用 [玩家数据](./api-player-data) 实现持久化存储