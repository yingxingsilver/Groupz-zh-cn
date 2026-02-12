---
sidebar_position: 5
title: 玩家数据 API
description: 持久化保存玩家数据
---

# Player Data API（玩家数据 API）

## 概述
zMenu 提供内置的玩家数据存储系统，用于持久化保存玩家专属数据。该系统具备以下特性：

- 数据存储于数据库（SQLite、MySQL 或 MariaDB）
- 可通过占位符 `%zmenu_player_value_<键名>%` 访问
- 服务端重启后数据持久保留
- 提供完整的编程 API 接口

## DataManager（数据管理器）

获取数据管理器实例以操作玩家数据：

```java
DataManager dataManager = menuPlugin.getDataManager();
```

## 读取数据

### 获取单个值
```java
// 返回 Optional<String>
Optional<String> value = dataManager.getData(player, "coins");

// 检查并使用值
if (value.isPresent()) {
    int coins = Integer.parseInt(value.get());
    player.sendMessage("您拥有 " + coins + " 枚金币");
} else {
    player.sendMessage("未找到金币数据");
}
```

### 获取带默认值
```java
String coins = dataManager.getData(player, "coins").orElse("0");
int coinAmount = Integer.parseInt(coins);
```

### 通过 UUID 获取
```java
UUID uuid = player.getUniqueId();
Optional<String> value = dataManager.getData(uuid, "coins");
```

### 检查数据是否存在
```java
boolean hasCoins = dataManager.getData(player, "coins").isPresent();
```

## 写入数据

### 设置值
```java
// 设置字符串值
dataManager.setData(player, "coins", "100");

// 设置数值（以字符串形式）
dataManager.setData(player, "level", String.valueOf(5));

// 设置布尔值（以字符串形式）
dataManager.setData(player, "premium", "true");
```

### 数值累加
```java
// 当前值增加 50
dataManager.addData(player, "coins", 50);
```

### 数值递减
```java
// 通过添加负值实现减法
dataManager.addData(player, "coins", -25);
```

### 移除数据
```java
// 移除玩家的指定键数据
dataManager.removeData(player, "coins");
```

## 批量操作

### 获取玩家所有键值
```java
// 此功能依赖具体实现
// 通常需自行维护键名列表
List<String> knownKeys = Arrays.asList("coins", "level", "premium", "last-login");

for (String key : knownKeys) {
    dataManager.getData(player, key).ifPresent(value -> {
        player.sendMessage(key + ": " + value);
    });
}
```

## 完整示例：货币系统

```java
package com.example.currency;

import fr.maxlego08.menu.api.MenuPlugin;
import fr.maxlego08.menu.api.DataManager;
import org.bukkit.entity.Player;

public class CurrencyManager {

    private final DataManager dataManager;
    private static final String COINS_KEY = "coins";

    public CurrencyManager(MenuPlugin menuPlugin) {
        this.dataManager = menuPlugin.getDataManager();
    }

    public int getCoins(Player player) {
        return dataManager.getData(player, COINS_KEY)
            .map(Integer::parseInt)
            .orElse(0);
    }

    public void setCoins(Player player, int amount) {
        dataManager.setData(player, COINS_KEY, String.valueOf(Math.max(0, amount)));
    }

    public void addCoins(Player player, int amount) {
        int current = getCoins(player);
        setCoins(player, current + amount);
    }

    public boolean removeCoins(Player player, int amount) {
        int current = getCoins(player);
        if (current >= amount) {
            setCoins(player, current - amount);
            return true;
        }
        return false;
    }

    public boolean hasCoins(Player player, int amount) {
        return getCoins(player) >= amount;
    }
}
```

### 使用示例
```java
CurrencyManager currency = new CurrencyManager(menuPlugin);

// 查询余额
int balance = currency.getCoins(player);
player.sendMessage("余额: " + balance);

// 添加金币
currency.addCoins(player, 100);
player.sendMessage("已添加 100 枚金币！");

// 消耗金币（带余额检查）
if (currency.removeCoins(player, 50)) {
    player.sendMessage("已消费 50 枚金币");
} else {
    player.sendMessage("金币不足！");
}
```

## 完整示例：统计数据追踪器

```java
package com.example.stats;

import fr.maxlego08.menu.api.MenuPlugin;
import fr.maxlego08.menu.api.DataManager;
import org.bukkit.entity.Player;

public class StatsTracker {

    private final DataManager dataManager;

    public StatsTracker(MenuPlugin menuPlugin) {
        this.dataManager = menuPlugin.getDataManager();
    }

    // 击杀数
    public int getKills(Player player) {
        return getIntValue(player, "kills");
    }

    public void incrementKills(Player player) {
        dataManager.addData(player, "kills", 1);
    }

    // 死亡数
    public int getDeaths(Player player) {
        return getIntValue(player, "deaths");
    }

    public void incrementDeaths(Player player) {
        dataManager.addData(player, "deaths", 1);
    }

    // K/D 比率
    public double getKDRatio(Player player) {
        int kills = getKills(player);
        int deaths = getDeaths(player);
        if (deaths == 0) return kills;
        return (double) kills / deaths;
    }

    // 游玩时长（分钟）
    public long getPlaytime(Player player) {
        return getLongValue(player, "playtime");
    }

    public void addPlaytime(Player player, long minutes) {
        long current = getPlaytime(player);
        dataManager.setData(player, "playtime", String.valueOf(current + minutes));
    }

    // 辅助方法
    private int getIntValue(Player player, String key) {
        return dataManager.getData(player, key)
            .map(Integer::parseInt)
            .orElse(0);
    }

    private long getLongValue(Player player, String key) {
        return dataManager.getData(player, key)
            .map(Long::parseLong)
            .orElse(0L);
    }
}
```

## 完整示例：每日奖励系统

```java
package com.example.rewards;

import fr.maxlego08.menu.api.MenuPlugin;
import fr.maxlego08.menu.api.DataManager;
import org.bukkit.entity.Player;

public class DailyRewardsManager {

    private final DataManager dataManager;
    private static final long DAY_IN_MILLIS = 24 * 60 * 60 * 1000;

    public DailyRewardsManager(MenuPlugin menuPlugin) {
        this.dataManager = menuPlugin.getDataManager();
    }

    public boolean canClaimDaily(Player player) {
        long lastClaim = getLastClaimTime(player);
        long now = System.currentTimeMillis();
        return (now - lastClaim) >= DAY_IN_MILLIS;
    }

    public long getTimeUntilNextClaim(Player player) {
        long lastClaim = getLastClaimTime(player);
        long now = System.currentTimeMillis();
        long nextClaim = lastClaim + DAY_IN_MILLIS;
        return Math.max(0, nextClaim - now);
    }

    public void claimDaily(Player player) {
        dataManager.setData(player, "daily-last-claim",
            String.valueOf(System.currentTimeMillis()));
        incrementStreak(player);
    }

    public int getStreak(Player player) {
        return dataManager.getData(player, "daily-streak")
            .map(Integer::parseInt)
            .orElse(0);
    }

    private void incrementStreak(Player player) {
        dataManager.addData(player, "daily-streak", 1);
    }

    public void resetStreak(Player player) {
        dataManager.setData(player, "daily-streak", "0");
    }

    private long getLastClaimTime(Player player) {
        return dataManager.getData(player, "daily-last-claim")
            .map(Long::parseLong)
            .orElse(0L);
    }

    // 格式化剩余时间
    public String formatTimeRemaining(Player player) {
        long millis = getTimeUntilNextClaim(player);
        long hours = millis / (60 * 60 * 1000);
        long minutes = (millis % (60 * 60 * 1000)) / (60 * 1000);
        return String.format("%dh %dm", hours, minutes);
    }
}
```

## 处理复杂数据结构

对于复杂数据类型，建议序列化为 JSON 格式存储：

```java
import com.google.gson.Gson;

public class ComplexDataManager {

    private final DataManager dataManager;
    private final Gson gson = new Gson();

    public ComplexDataManager(MenuPlugin menuPlugin) {
        this.dataManager = menuPlugin.getDataManager();
    }

    // 保存字符串列表
    public void saveStringList(Player player, String key, List<String> list) {
        String json = gson.toJson(list);
        dataManager.setData(player, key, json);
    }

    // 读取字符串列表
    public List<String> loadStringList(Player player, String key) {
        return dataManager.getData(player, key)
            .map(json -> gson.fromJson(json,
                new TypeToken<List<String>>(){}.getType()))
            .orElse(new ArrayList<>());
    }

    // 保存自定义对象
    public void savePlayerStats(Player player, PlayerStats stats) {
        String json = gson.toJson(stats);
        dataManager.setData(player, "player-stats", json);
    }

    // 读取自定义对象
    public PlayerStats loadPlayerStats(Player player) {
        return dataManager.getData(player, "player-stats")
            .map(json -> gson.fromJson(json, PlayerStats.class))
            .orElse(new PlayerStats());
    }
}

// 示例数据类
public class PlayerStats {
    public int kills = 0;
    public int deaths = 0;
    public long playtime = 0;
    public List<String> achievements = new ArrayList<>();
}
```

## 在 GUI 中使用数据

设置的数据可自动通过占位符在物品栏中使用：

```yaml
items:
  stats-display:
    slot: 4
    item:
      material: PLAYER_HEAD
      playerHead: "%player%"
      name: "&6&l%player% 的统计数据"
      lore:
        - "&7金币: &e%zmenu_player_value_coins%"
        - "&7击杀: &a%zmenu_player_value_kills%"
        - "&7死亡: &c%zmenu_player_value_deaths%"
        - "&7连签: &b%zmenu_player_value_daily-streak%"
```

## 最佳实践

- **统一键名规范**：建立并遵循命名约定
- **处理缺失数据**：始终使用 `orElse()` 提供默认值
- **验证数值解析**：数值转换时使用 try-catch 捕获异常
- **避免覆盖重要数据**：写入前进行存在性检查
- **选择合适的数据类型**：存储为字符串，使用时按需解析
- **清理无用数据**：及时移除不再使用的键

## 键名命名规范

```java
// 推荐命名方式
"coins"              // 简洁明确
"daily-streak"       // 多单词使用短横线分隔（kebab-case）
"quest-tutorial-1"   // 包含标识符
"stats-kills"        // 使用类别前缀

// 应避免的命名
"c"                  // 过于简略
"playerCoinsData"    // 驼峰命名（应使用短横线）
"x1y2z3"             // 无实际意义
```

## 后续步骤

- 监听 [事件系统](./api-events)
- 创建使用玩家数据的 [自定义按钮](./api-buttons)
- 开发修改玩家数据的 [自定义动作](./api-actions)