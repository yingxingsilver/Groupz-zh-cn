---
sidebar_position: 1
title: API 介绍
description: zMenu API 入门指南
---

# API 介绍



## API 概述

zMenu 提供了一套完整的应用程序编程接口（API），供开发者集成并扩展插件功能。本指南将帮助您快速上手 zMenu API。

## Javadoc 文档

完整 API 文档地址：  
[https://repo.groupez.dev/javadoc/releases/fr/maxlego08/menu/zmenu-api/1.1.0.6](https://repo.groupez.dev/javadoc/releases/fr/maxlego08/menu/zmenu-api/1.1.0.6)

## Maven 仓库配置

在 `pom.xml` 中添加 GroupeZ 仓库：

```xml
<repositories>
    <repository>
        <id>groupez</id>
        <url>https://repo.groupez.dev/releases</url>
    </repository>
</repositories>
```

## Maven 依赖配置

添加 zMenu API 依赖项：

```xml
<dependencies>
    <dependency>
        <groupId>fr.maxlego08.menu</groupId>
        <artifactId>zmenu-api</artifactId>
        <version>1.1.0.0</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

## Gradle 配置

Gradle 项目配置：

```gradle
repositories {
    maven { url 'https://repo.groupez.dev/releases' }
}

dependencies {
    compileOnly 'fr.maxlego08.menu:zmenu-api:1.1.0.0'
}
```

## Kotlin DSL 配置

```kotlin
repositories {
    maven("https://repo.groupez.dev/releases")
}

dependencies {
    compileOnly("fr.maxlego08.menu:zmenu-api:1.1.0.0")
}
```

## 插件依赖声明

在 `plugin.yml` 中声明依赖关系：

```yaml
# 硬依赖（缺少 zMenu 时插件将无法加载）
depend: [zMenu]

# 软依赖（即使缺少 zMenu 插件仍可正常加载）
softdepend: [zMenu]
```

## 获取 API 实例

### 获取插件主实例

```java
import fr.maxlego08.menu.api.MenuPlugin;
import org.bukkit.Bukkit;
import org.bukkit.plugin.java.JavaPlugin;

public class MyPlugin extends JavaPlugin {

    private MenuPlugin menuPlugin;

    @Override
    public void onEnable() {
        // 获取 zMenu 实例
        this.menuPlugin = (MenuPlugin) Bukkit.getPluginManager().getPlugin("zMenu");

        if (this.menuPlugin == null) {
            getLogger().severe("未找到 zMenu！正在禁用插件...");
            Bukkit.getPluginManager().disablePlugin(this);
            return;
        }

        getLogger().info("成功集成 zMenu！");
    }

    public MenuPlugin getMenuPlugin() {
        return this.menuPlugin;
    }
}
```

### 软依赖安全集成

```java
import fr.maxlego08.menu.api.MenuPlugin;
import org.bukkit.Bukkit;
import org.bukkit.plugin.Plugin;
import org.bukkit.plugin.java.JavaPlugin;

public class MyPlugin extends JavaPlugin {

    private MenuPlugin menuPlugin;
    private boolean zMenuEnabled = false;

    @Override
    public void onEnable() {
        // 尝试集成 zMenu
        hookZMenu();

        if (zMenuEnabled) {
            getLogger().info("已启用 zMenu 集成功能！");
            // 注册 zMenu 专属特性
        } else {
            getLogger().info("未检测到 zMenu，将以独立模式运行。");
        }
    }

    private void hookZMenu() {
        Plugin plugin = Bukkit.getPluginManager().getPlugin("zMenu");
        if (plugin != null && plugin.isEnabled()) {
            this.menuPlugin = (MenuPlugin) plugin;
            this.zMenuEnabled = true;
        }
    }

    public boolean isZMenuEnabled() {
        return this.zMenuEnabled;
    }

    public MenuPlugin getMenuPlugin() {
        return this.menuPlugin;
    }
}
```

## 核心接口

zMenu API 提供以下核心接口：

### MenuPlugin

主插件接口，提供对所有管理器的访问入口：

```java
MenuPlugin menuPlugin = ...;

// 界面管理
InventoryManager inventoryManager = menuPlugin.getInventoryManager();

// 按钮类型注册
ButtonManager buttonManager = menuPlugin.getButtonManager();

// 命令管理
CommandManager commandManager = menuPlugin.getCommandManager();

// 玩家数据管理
DataManager dataManager = menuPlugin.getDataManager();
```

### 关键管理器

| 管理器 | 用途 |
|--------|------|
| `InventoryManager` | 加载、管理及打开界面 |
| `ButtonManager` | 注册自定义按钮类型 |
| `CommandManager` | 注册自定义命令 |
| `DataManager` | 访问玩家数据存储 |

## API 结构

```
fr.maxlego08.menu.api
├── MenuPlugin              # 主插件接口
├── InventoryManager        # 界面操作管理器
├── ButtonManager           # 按钮注册管理器
├── CommandManager          # 命令处理管理器
├── DataManager             # 玩家数据访问管理器
├── button/
│   ├── Button              # 按钮接口
│   └── ButtonLoader        # 按钮加载器
├── action/
│   ├── Action              # 操作接口
│   └── ActionLoader        # 操作加载器
├── requirement/
│   ├── Requirement         # 条件接口
│   └── Permissible         # 权限校验
└── event/
    ├── ButtonLoadEvent
    ├── InventoryLoadEvent
    └── PlayerOpenInventoryEvent
```

## 最佳实践

- ✅ 获取 zMenu 插件实例时务必进行空值校验
- ✅ 若插件可脱离 zMenu 独立运行，应使用软依赖（softdepend）
- ✅ 避免缓存管理器引用，应在需要时动态获取
- ✅ 处理界面操作时应妥善捕获并处理异常
- ✅ 在不同 zMenu 版本环境下进行充分测试

## 完整示例插件

```java
package com.example.myplugin;

import fr.maxlego08.menu.api.MenuPlugin;
import fr.maxlego08.menu.api.InventoryManager;
import org.bukkit.Bukkit;
import org.bukkit.command.Command;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;
import org.bukkit.plugin.java.JavaPlugin;

public class MyPlugin extends JavaPlugin {

    private MenuPlugin menuPlugin;

    @Override
    public void onEnable() {
        // 集成 zMenu
        this.menuPlugin = (MenuPlugin) Bukkit.getPluginManager().getPlugin("zMenu");

        if (this.menuPlugin == null) {
            getLogger().severe("需要 zMenu 插件！");
            Bukkit.getPluginManager().disablePlugin(this);
            return;
        }

        getLogger().info("MyPlugin 已启用 zMenu 集成功能！");
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

        // 打开 zMenu 界面
        manager.getInventory("my-menu").ifPresent(inv -> {
            manager.openInventory(player, inv);
        });

        return true;
    }
}
```

## 后续学习

- 学习如何通过编程方式 [打开界面](./api-inventory)
- 创建 [自定义按钮](./api-buttons)
- 创建 [自定义操作](./api-actions)
- 操作 [玩家数据](./api-player-data)
- 监听 [事件](./api-events)

## 技术支持

如需 API 技术支持，请通过以下渠道联系：

- Discord: [https://discord.groupez.dev](https://discord.groupez.dev)
- GitHub Issues: [https://github.com/Maxlego08/zMenu/issues](https://github.com/Maxlego08/zMenu/issues)