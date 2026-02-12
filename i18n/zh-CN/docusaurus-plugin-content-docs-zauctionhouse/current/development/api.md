---
sidebar_position: 1
title: API Introduction
description: Getting started with zAuctionHouse Developer API
---

# Developer API

zAuctionHouse provides a comprehensive API for developers to integrate with the auction system.

## Architecture

zAuctionHouse is built with a modular architecture:

```
zAuctionHouse/
├── API         # Interfaces and abstract classes
├── Core        # Implementation
└── Hooks       # Third-party integrations
```

This separation allows you to depend only on the API module, keeping your plugin lightweight.

## Maven Setup

Add the JitPack repository:

```xml
<repositories>
    <repository>
        <id>jitpack.io</id>
        <url>https://jitpack.io</url>
    </repository>
</repositories>
```

Add the dependency:

```xml
<dependencies>
    <dependency>
        <groupId>com.github.Maxlego08</groupId>
        <artifactId>zAuctionHouse-API</artifactId>
        <version>4.0.0</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

## Gradle Setup

Add the JitPack repository:

```groovy
repositories {
    maven { url 'https://jitpack.io' }
}
```

Add the dependency:

```groovy
dependencies {
    compileOnly 'com.github.Maxlego08:zAuctionHouse-API:4.0.0'
}
```

## Getting the API

Access the API via Bukkit's service manager:

```java
import fr.maxlego08.zauctionhouse.api.AuctionPlugin;
import fr.maxlego08.zauctionhouse.api.AuctionManager;
import org.bukkit.Bukkit;
import org.bukkit.plugin.RegisteredServiceProvider;
import org.bukkit.plugin.java.JavaPlugin;

public class MyPlugin extends JavaPlugin {

    private AuctionPlugin auctionPlugin;

    @Override
    public void onEnable() {
        // Get the zAuctionHouse API
        RegisteredServiceProvider<AuctionPlugin> provider =
            Bukkit.getServicesManager().getRegistration(AuctionPlugin.class);

        if (provider == null) {
            getLogger().severe("zAuctionHouse not found!");
            getServer().getPluginManager().disablePlugin(this);
            return;
        }

        this.auctionPlugin = provider.getProvider();
        getLogger().info("Successfully hooked into zAuctionHouse!");
    }

    public AuctionPlugin getAuctionPlugin() {
        return auctionPlugin;
    }
}
```

## Main Interfaces

### AuctionPlugin

The main entry point to the API:

```java
public interface AuctionPlugin {

    // Get the auction manager
    AuctionManager getAuctionManager();

    // Get the economy manager
    EconomyManager getEconomyManager();

    // Get the category manager
    CategoryManager getCategoryManager();

    // Get the configuration manager
    ConfigurationManager getConfigurationManager();

    // Get sell service
    AuctionSellService getSellService();

    // Get purchase service
    AuctionPurchaseService getPurchaseService();

    // Get remove service
    AuctionRemoveService getRemoveService();

    // Get expire service
    AuctionExpireService getExpireService();
}
```

### AuctionManager

Core auction operations:

```java
public interface AuctionManager {

    // Get all listed items
    List<AuctionItem> getListedItems();

    // Get items by player
    List<AuctionItem> getListedItems(UUID playerUuid);

    // Get items by category
    List<AuctionItem> getListedItems(Category category);

    // Get expired items for a player
    List<AuctionItem> getExpiredItems(UUID playerUuid);

    // Get purchased items to claim
    List<AuctionItem> getPurchasedItems(UUID playerUuid);

    // Get item by ID
    Optional<AuctionItem> getItem(UUID itemId);

    // Search items
    List<AuctionItem> search(String query);

    // Get player's listing count
    int getListingCount(UUID playerUuid);

    // Get player's listing limit
    int getListingLimit(Player player);
}
```

## Quick Examples

### List All Items

```java
AuctionManager manager = auctionPlugin.getAuctionManager();
List<AuctionItem> items = manager.getListedItems();

for (AuctionItem item : items) {
    getLogger().info(String.format(
        "Item: %s, Price: %d, Seller: %s",
        item.getItemStack().getType(),
        item.getPrice(),
        item.getSellerName()
    ));
}
```

### Get Player Statistics

```java
UUID playerUuid = player.getUniqueId();
AuctionManager manager = auctionPlugin.getAuctionManager();

int listed = manager.getListingCount(playerUuid);
int expired = manager.getExpiredItems(playerUuid).size();
int toClaim = manager.getPurchasedItems(playerUuid).size();

player.sendMessage(String.format(
    "Listed: %d, Expired: %d, To Claim: %d",
    listed, expired, toClaim
));
```

### Sell an Item Programmatically

```java
AuctionSellService sellService = auctionPlugin.getSellService();
EconomyManager economyManager = auctionPlugin.getEconomyManager();

// Get the Vault economy
AuctionEconomy economy = economyManager.getEconomy("vault")
    .orElseThrow(() -> new IllegalStateException("Vault not found"));

// Sell the item in player's hand
ItemStack itemStack = player.getInventory().getItemInMainHand();
long price = 1000;

sellService.sell(player, itemStack, price, economy)
    .thenAccept(result -> {
        if (result.isSuccess()) {
            player.sendMessage("Item listed successfully!");
        } else {
            player.sendMessage("Failed: " + result.getMessage());
        }
    });
```

## Asynchronous Operations

All API operations that interact with the database return `CompletableFuture`:

```java
// Async operation example
auctionPlugin.getSellService()
    .sell(player, item, price, economy)
    .thenAccept(result -> {
        // Handle result on main thread if needed
        Bukkit.getScheduler().runTask(plugin, () -> {
            player.sendMessage(result.getMessage());
        });
    })
    .exceptionally(throwable -> {
        getLogger().severe("Error selling item: " + throwable.getMessage());
        return null;
    });
```

## JavaDoc

Full API documentation is available at:
[javadocs.groupez.dev/zauctionhouse](https://javadocs.groupez.dev/zauctionhouse)

## Next Steps

- [Services](./services) - Learn about the service-based architecture
- [Events](./events) - Listen to auction events
- [Items](./items) - Work with Item and AuctionItem interfaces
- [Custom Economy](./custom-economy) - Create custom economy implementations
