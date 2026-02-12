---
sidebar_position: 4
title: 自定义动作
description: 在 zMenu 中创建并注册自定义动作类型
---

# 自定义动作（Custom Actions）

## 概述
学习如何在 zMenu 中创建并注册自定义动作类型。

动作（Action）在玩家与按钮交互时执行。自定义动作允许你扩展内置动作类型，实现更复杂的行为逻辑。

## 创建动作加载器（Action Loader）

`ActionLoader` 接口定义了如何从 YAML 配置中解析你的自定义动作：

```java
import fr.maxlego08.menu.api.action.Action;
import fr.maxlego08.menu.api.loader.ActionLoader;
import org.bukkit.configuration.file.YamlConfiguration;

public class MyActionLoader implements ActionLoader {

    @Override
    public String getKey() {
        return "my-custom-action"; // YAML 中使用的类型标识符
    }

    @Override
    public Action load(YamlConfiguration configuration, String path) {
        // 从配置中读取自定义属性
        String message = configuration.getString(path + ".message", "Default message");
        int times = configuration.getInt(path + ".times", 1);

        return new MyCustomAction(message, times);
    }
}
```

## 实现动作逻辑

```java
import fr.maxlego08.menu.api.action.Action;
import org.bukkit.entity.Player;

public class MyCustomAction implements Action {

    private final String message;
    private final int times;

    public MyCustomAction(String message, int times) {
        this.message = message;
        this.times = times;
    }

    @Override
    public void execute(Player player) {
        for (int i = 0; i < times; i++) {
            player.sendMessage(message);
        }
    }
}
```

## 注册自定义动作

在插件启用时向 zMenu 注册你的动作加载器：

```java
@Override
public void onEnable() {
    MenuPlugin menuPlugin = (MenuPlugin) Bukkit.getPluginManager().getPlugin("zMenu");

    if (menuPlugin != null) {
        // 通过按钮管理器的动作加载器注册表进行注册
        menuPlugin.getButtonManager().registerAction(new MyActionLoader());
        getLogger().info("已注册自定义动作: my-custom-action");
    }
}
```

## YAML 配置示例

```yaml
items:
  my-button:
    slot: 13
    item:
      material: DIAMOND
      name: "&bClick me"
    actions:
      - type: my-custom-action
        message: "&aHello, %player%!"
        times: 3
```

## 完整示例：粒子效果动作

**ParticleActionLoader.java**
```java
package com.example.actions;

import fr.maxlego08.menu.api.action.Action;
import fr.maxlego08.menu.api.loader.ActionLoader;
import org.bukkit.Particle;
import org.bukkit.configuration.file.YamlConfiguration;

public class ParticleActionLoader implements ActionLoader {

    @Override
    public String getKey() {
        return "particle";
    }

    @Override
    public Action load(YamlConfiguration config, String path) {
        String particleName = config.getString(path + ".particle", "HEART");
        int count = config.getInt(path + ".count", 10);
        double offsetX = config.getDouble(path + ".offset-x", 0.5);
        double offsetY = config.getDouble(path + ".offset-y", 0.5);
        double offsetZ = config.getDouble(path + ".offset-z", 0.5);
        double speed = config.getDouble(path + ".speed", 0.1);

        Particle particle;
        try {
            particle = Particle.valueOf(particleName.toUpperCase());
        } catch (IllegalArgumentException e) {
            particle = Particle.HEART;
        }

        return new ParticleAction(particle, count, offsetX, offsetY, offsetZ, speed);
    }
}
```

**ParticleAction.java**
```java
package com.example.actions;

import fr.maxlego08.menu.api.action.Action;
import org.bukkit.Location;
import org.bukkit.Particle;
import org.bukkit.entity.Player;

public class ParticleAction implements Action {

    private final Particle particle;
    private final int count;
    private final double offsetX;
    private final double offsetY;
    private final double offsetZ;
    private final double speed;

    public ParticleAction(Particle particle, int count,
                         double offsetX, double offsetY, double offsetZ,
                         double speed) {
        this.particle = particle;
        this.count = count;
        this.offsetX = offsetX;
        this.offsetY = offsetY;
        this.offsetZ = offsetZ;
        this.speed = speed;
    }

    @Override
    public void execute(Player player) {
        Location location = player.getLocation().add(0, 1, 0);
        player.getWorld().spawnParticle(
            particle,
            location,
            count,
            offsetX, offsetY, offsetZ,
            speed
        );
    }
}
```

**YAML 使用示例**
```yaml
items:
  reward-button:
    slot: 13
    item:
      material: NETHER_STAR
      name: "&6&lClaim Reward"
    actions:
      - type: particle
        particle: VILLAGER_HAPPY
        count: 30
        offset-x: 1.0
        offset-y: 1.0
        offset-z: 1.0
        speed: 0.2
      - type: message
        messages:
          - "&aReward claimed!"
```

## 完整示例：经济系统动作

**EconomyActionLoader.java**
```java
package com.example.actions;

import fr.maxlego08.menu.api.action.Action;
import fr.maxlego08.menu.api.loader.ActionLoader;
import org.bukkit.configuration.file.YamlConfiguration;

public class EconomyActionLoader implements ActionLoader {

    @Override
    public String getKey() {
        return "custom-economy";
    }

    @Override
    public Action load(YamlConfiguration config, String path) {
        String operation = config.getString(path + ".operation", "give");
        double amount = config.getDouble(path + ".amount", 0);
        String successMessage = config.getString(path + ".success-message", " &aDone! ");
        String failMessage = config.getString(path + ".fail-message", " &cFailed! ");

        return new EconomyAction(operation, amount, successMessage, failMessage);
    }
}
```

**EconomyAction.java**
```java
package com.example.actions;

import fr.maxlego08.menu.api.action.Action;
import net.milkbowl.vault.economy.Economy;
import net.milkbowl.vault.economy.EconomyResponse;
import org.bukkit.Bukkit;
import org.bukkit.ChatColor;
import org.bukkit.entity.Player;
import org.bukkit.plugin.RegisteredServiceProvider;

public class EconomyAction implements Action {

    private final String operation;
    private final double amount;
    private final String successMessage;
    private final String failMessage;

    public EconomyAction(String operation, double amount,
                        String successMessage, String failMessage) {
        this.operation = operation;
        this.amount = amount;
        this.successMessage = successMessage;
        this.failMessage = failMessage;
    }

    @Override
    public void execute(Player player) {
        Economy economy = getEconomy();
        if (economy == null) {
            player.sendMessage(ChatColor.RED + "Economy not available!");
            return;
        }

        EconomyResponse response;
        boolean success;

        switch (operation.toLowerCase()) {
            case "give":
            case "deposit":
                response = economy.depositPlayer(player, amount);
                success = response.transactionSuccess();
                break;
            case "take":
            case "withdraw":
                if (economy.has(player, amount)) {
                    response = economy.withdrawPlayer(player, amount);
                    success = response.transactionSuccess();
                } else {
                    success = false;
                }
                break;
            default:
                success = false;
        }

        String message = success ? successMessage : failMessage;
        message = message.replace("%amount%", String.valueOf(amount));
        message = message.replace("%balance%", String.valueOf(economy.getBalance(player)));
        player.sendMessage(ChatColor.translateAlternateColorCodes('&', message));
    }

    private Economy getEconomy() {
        if (Bukkit.getPluginManager().getPlugin("Vault") == null) {
            return null;
        }
        RegisteredServiceProvider<Economy> rsp =
            Bukkit.getServicesManager().getRegistration(Economy.class);
        return rsp != null ? rsp.getProvider() : null;
    }
}
```

## 完整示例：权限检查动作

**CheckPermissionActionLoader.java**
```java
package com.example.actions;

import fr.maxlego08.menu.api.action.Action;
import fr.maxlego08.menu.api.loader.ActionLoader;
import org.bukkit.configuration.file.YamlConfiguration;
import java.util.List;

public class CheckPermissionActionLoader implements ActionLoader {

    @Override
    public String getKey() {
        return "check-permission";
    }

    @Override
    public Action load(YamlConfiguration config, String path) {
        String permission = config.getString(path + ".permission", "");
        List<String> successCommands = config.getStringList(path + ".success-commands");
        List<String> failCommands = config.getStringList(path + ".fail-commands");

        return new CheckPermissionAction(permission, successCommands, failCommands);
    }
}
```

**CheckPermissionAction.java**
```java
package com.example.actions;

import fr.maxlego08.menu.api.action.Action;
import org.bukkit.Bukkit;
import org.bukkit.entity.Player;
import java.util.List;

public class CheckPermissionAction implements Action {

    private final String permission;
    private final List<String> successCommands;
    private final List<String> failCommands;

    public CheckPermissionAction(String permission,
                                List<String> successCommands,
                                List<String> failCommands) {
        this.permission = permission;
        this.successCommands = successCommands;
        this.failCommands = failCommands;
    }

    @Override
    public void execute(Player player) {
        List<String> commands = player.hasPermission(permission)
            ? successCommands
            : failCommands;

        for (String command : commands) {
            String parsedCommand = command.replace("%player%", player.getName());
            Bukkit.dispatchCommand(Bukkit.getConsoleSender(), parsedCommand);
        }
    }
}
```

## 占位符支持

在动作中支持占位符解析：

```java
import fr.maxlego08.menu.api.utils.Placeholders;

@Override
public void execute(Player player) {
    // 执行前解析占位符
    String parsedMessage = Placeholders.parse(player, this.message);
    player.sendMessage(ChatColor.translateAlternateColorCodes('&', parsedMessage));
}
```

## 最佳实践

- **命名规范**：使用清晰、唯一的动作标识符（如 `give-item` 而非 `gi`）
- **输入验证**：对缺失或无效的配置参数进行容错处理
- **默认值**：为可选参数提供合理的默认值
- **占位符支持**：在文本类参数中支持 `%player%` 等动态占位符
- **错误处理**：优雅处理异常情况，避免插件崩溃
- **文档完善**：为每个配置参数提供详细说明

## 下一步学习

- 探索 [玩家数据操作](./api-player-data)
- 了解 [事件监听机制](./api-events)
- 查阅 [API 入门指南](./api-introduction)