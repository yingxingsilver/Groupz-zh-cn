---
sidebar_position: 5
title: Custom Economy
description: Create custom economy implementations for zAuctionHouse
---

# Custom Economy

zAuctionHouse supports custom economy implementations. This allows you to integrate any currency system with the auction house.

## AuctionEconomy Interface

To create a custom economy, implement the `AuctionEconomy` interface:

```java
public interface AuctionEconomy {

    /**
     * Get the unique name of this economy.
     * Used in configuration and storage.
     */
    String getName();

    /**
     * Get the display name (can include colors).
     */
    String getDisplayName();

    /**
     * Check if a player has enough currency.
     *
     * @param player The player
     * @param amount The required amount
     * @return true if the player has enough
     */
    boolean has(OfflinePlayer player, long amount);

    /**
     * Withdraw currency from a player.
     *
     * @param player The player
     * @param amount The amount to withdraw
     * @return true if successful
     */
    boolean withdraw(OfflinePlayer player, long amount);

    /**
     * Deposit currency to a player.
     *
     * @param player The player
     * @param amount The amount to deposit
     * @return true if successful
     */
    boolean deposit(OfflinePlayer player, long amount);

    /**
     * Get the player's current balance.
     *
     * @param player The player
     * @return The balance
     */
    long getBalance(OfflinePlayer player);

    /**
     * Format an amount for display.
     *
     * @param amount The amount
     * @return Formatted string (e.g., "$1,000" or "100 Tokens")
     */
    String format(long amount);

    /**
     * Get the icon for this economy (optional).
     * Used in inventory displays.
     */
    default ItemStack getIcon() {
        return new ItemStack(Material.GOLD_INGOT);
    }

    /**
     * Check if this economy is enabled.
     */
    default boolean isEnabled() {
        return true;
    }
}
```

## Example: Token Economy

Here's a complete example of a custom token economy:

```java
import fr.maxlego08.zauctionhouse.api.economy.AuctionEconomy;
import org.bukkit.Material;
import org.bukkit.OfflinePlayer;
import org.bukkit.inventory.ItemStack;

import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

public class TokenEconomy implements AuctionEconomy {

    private final Map<UUID, Long> balances = new HashMap<>();

    @Override
    public String getName() {
        return "tokens";
    }

    @Override
    public String getDisplayName() {
        return "§bTokens";
    }

    @Override
    public boolean has(OfflinePlayer player, long amount) {
        return getBalance(player) >= amount;
    }

    @Override
    public boolean withdraw(OfflinePlayer player, long amount) {
        UUID uuid = player.getUniqueId();
        long balance = getBalance(player);

        if (balance < amount) {
            return false;
        }

        balances.put(uuid, balance - amount);
        return true;
    }

    @Override
    public boolean deposit(OfflinePlayer player, long amount) {
        UUID uuid = player.getUniqueId();
        long balance = getBalance(player);
        balances.put(uuid, balance + amount);
        return true;
    }

    @Override
    public long getBalance(OfflinePlayer player) {
        return balances.getOrDefault(player.getUniqueId(), 0L);
    }

    @Override
    public String format(long amount) {
        return amount + " Tokens";
    }

    @Override
    public ItemStack getIcon() {
        ItemStack icon = new ItemStack(Material.EMERALD);
        // Customize icon if needed
        return icon;
    }

    // Custom method to set balance (for admin commands, etc.)
    public void setBalance(OfflinePlayer player, long amount) {
        balances.put(player.getUniqueId(), amount);
    }
}
```

## Registering Your Economy

Register your economy with the `EconomyManager`:

```java
import fr.maxlego08.zauctionhouse.api.AuctionPlugin;
import fr.maxlego08.zauctionhouse.api.economy.EconomyManager;
import org.bukkit.Bukkit;
import org.bukkit.plugin.RegisteredServiceProvider;
import org.bukkit.plugin.java.JavaPlugin;

public class MyPlugin extends JavaPlugin {

    private TokenEconomy tokenEconomy;

    @Override
    public void onEnable() {
        // Wait for zAuctionHouse to be ready
        Bukkit.getScheduler().runTaskLater(this, this::registerEconomy, 1L);
    }

    private void registerEconomy() {
        // Get zAuctionHouse API
        RegisteredServiceProvider<AuctionPlugin> provider =
            Bukkit.getServicesManager().getRegistration(AuctionPlugin.class);

        if (provider == null) {
            getLogger().warning("zAuctionHouse not found!");
            return;
        }

        AuctionPlugin auctionPlugin = provider.getProvider();
        EconomyManager economyManager = auctionPlugin.getEconomyManager();

        // Create and register economy
        this.tokenEconomy = new TokenEconomy();
        economyManager.registerEconomy(tokenEconomy);

        getLogger().info("Token economy registered with zAuctionHouse!");
    }

    public TokenEconomy getTokenEconomy() {
        return tokenEconomy;
    }
}
```

## Example: Item-Based Economy

A more complex example using a specific item as currency:

```java
public class DiamondShardEconomy implements AuctionEconomy {

    private final ItemStack currencyItem;

    public DiamondShardEconomy() {
        // Define the currency item
        this.currencyItem = new ItemStack(Material.PRISMARINE_SHARD);
        ItemMeta meta = currencyItem.getItemMeta();
        meta.setDisplayName("§bDiamond Shard");
        meta.setLore(Arrays.asList("§7A precious currency", "§7used in the auction house"));
        meta.setCustomModelData(1001); // Optional custom model
        currencyItem.setItemMeta(meta);
    }

    @Override
    public String getName() {
        return "diamond_shards";
    }

    @Override
    public String getDisplayName() {
        return "§bDiamond Shards";
    }

    @Override
    public boolean has(OfflinePlayer player, long amount) {
        if (!player.isOnline()) return false;
        return countCurrency(player.getPlayer()) >= amount;
    }

    @Override
    public boolean withdraw(OfflinePlayer player, long amount) {
        if (!player.isOnline()) return false;
        Player online = player.getPlayer();

        if (countCurrency(online) < amount) {
            return false;
        }

        return removeCurrency(online, (int) amount);
    }

    @Override
    public boolean deposit(OfflinePlayer player, long amount) {
        if (!player.isOnline()) return false;
        Player online = player.getPlayer();

        ItemStack toGive = currencyItem.clone();
        toGive.setAmount((int) amount);

        HashMap<Integer, ItemStack> leftover = online.getInventory().addItem(toGive);

        // Drop leftover items at player's feet
        for (ItemStack item : leftover.values()) {
            online.getWorld().dropItemNaturally(online.getLocation(), item);
        }

        return true;
    }

    @Override
    public long getBalance(OfflinePlayer player) {
        if (!player.isOnline()) return 0;
        return countCurrency(player.getPlayer());
    }

    @Override
    public String format(long amount) {
        return amount + " Diamond Shard" + (amount != 1 ? "s" : "");
    }

    @Override
    public ItemStack getIcon() {
        return currencyItem.clone();
    }

    private long countCurrency(Player player) {
        long count = 0;
        for (ItemStack item : player.getInventory().getContents()) {
            if (item != null && isCurrencyItem(item)) {
                count += item.getAmount();
            }
        }
        return count;
    }

    private boolean removeCurrency(Player player, int amount) {
        int remaining = amount;
        ItemStack[] contents = player.getInventory().getContents();

        for (int i = 0; i < contents.length && remaining > 0; i++) {
            ItemStack item = contents[i];
            if (item != null && isCurrencyItem(item)) {
                int remove = Math.min(item.getAmount(), remaining);
                item.setAmount(item.getAmount() - remove);
                remaining -= remove;

                if (item.getAmount() <= 0) {
                    player.getInventory().setItem(i, null);
                }
            }
        }

        return remaining == 0;
    }

    private boolean isCurrencyItem(ItemStack item) {
        if (item.getType() != currencyItem.getType()) return false;
        if (!item.hasItemMeta()) return false;

        ItemMeta meta = item.getItemMeta();
        ItemMeta expected = currencyItem.getItemMeta();

        // Check custom model data
        if (expected.hasCustomModelData()) {
            if (!meta.hasCustomModelData()) return false;
            if (meta.getCustomModelData() != expected.getCustomModelData()) return false;
        }

        // Check display name
        if (expected.hasDisplayName()) {
            if (!meta.hasDisplayName()) return false;
            if (!meta.getDisplayName().equals(expected.getDisplayName())) return false;
        }

        return true;
    }
}
```

## Configuration File

Create a configuration file for your economy in `economies/`:

```yaml
# economies/tokens.yml
enabled: true
name: "Tokens"

icon:
  material: EMERALD
  name: "&bTokens"
  lore:
    - "&7Custom token currency"

price:
  min: 1
  max: 1000000

tax:
  enabled: false

auto-claim:
  enabled: true
```

## Best Practices

1. **Thread Safety**: Ensure your economy implementation is thread-safe if using async operations.

2. **Offline Support**: Consider what happens when a player is offline. For item-based economies, you may need to store pending deposits.

3. **Logging**: Log transactions for debugging:

```java
@Override
public boolean withdraw(OfflinePlayer player, long amount) {
    boolean success = doWithdraw(player, amount);
    if (success) {
        plugin.getLogger().info(String.format(
            "Withdrew %d %s from %s",
            amount, getName(), player.getName()
        ));
    }
    return success;
}
```

4. **Validation**: Validate amounts before processing:

```java
@Override
public boolean withdraw(OfflinePlayer player, long amount) {
    if (amount <= 0) {
        throw new IllegalArgumentException("Amount must be positive");
    }
    // ...
}
```

5. **Error Handling**: Handle edge cases gracefully:

```java
@Override
public boolean deposit(OfflinePlayer player, long amount) {
    try {
        // Deposit logic
        return true;
    } catch (Exception e) {
        plugin.getLogger().severe("Failed to deposit: " + e.getMessage());
        return false;
    }
}
```

## Testing Your Economy

Test your economy thoroughly:

```java
// Unit test example
@Test
public void testWithdrawDeposit() {
    TokenEconomy economy = new TokenEconomy();
    OfflinePlayer player = mockPlayer();

    // Initial balance
    assertEquals(0, economy.getBalance(player));

    // Deposit
    assertTrue(economy.deposit(player, 1000));
    assertEquals(1000, economy.getBalance(player));

    // Withdraw
    assertTrue(economy.withdraw(player, 500));
    assertEquals(500, economy.getBalance(player));

    // Insufficient funds
    assertFalse(economy.withdraw(player, 1000));
    assertEquals(500, economy.getBalance(player));
}
```
