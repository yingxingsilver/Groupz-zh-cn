---
sidebar_position: 2
title: 安装 zMenu
description: 如何在Minecraft 服务器上安装 zMenu
---

# 安装 zMenu

本指南将引导您完成在 Minecraft 服务器上安装 zMenu 的全过程。

## 系统要求

安装 zMenu 前，请确保您的服务器满足以下要求：

| 要求项 | 版本 |
|--------|------|
| Minecraft | 1.19 至 1.21+ |
| Java | **Java 21**（必需） |
| 服务端软件 | Spigot、Paper、Purpur、Pufferfish 或 Folia |

:::tip 推荐配置
为获得最佳体验，我们推荐使用 **Paper** 或 **Purpur**。这将启用 MiniMessage 格式支持并提供更佳性能。
:::

## 下载

您可从以下来源下载 zMenu：

- **Modrinth**（主站）：[https://modrinth.com/plugin/zmenu](https://modrinth.com/plugin/zmenu)
- **SpigotMC**：[https://www.spigotmc.org/resources/zmenu.110402/](https://www.spigotmc.org/resources/zmenu.110402/)

:::info 开发版本
最新开发版本可在 [Discord](https://discord.groupez.dev) 的 `#builds` 频道获取。
:::

## 安装步骤

### 步骤 1：下载插件

从上述任一来源下载最新版本的 `zMenu.jar`。

### 步骤 2：安装插件

1. 如果服务器正在运行，请先停止服务器
2. 将 `zMenu.jar` 文件放入服务器的 `plugins/` 文件夹
3. 启动服务器

### 步骤 3：验证安装

服务器启动后，验证 zMenu 是否正确加载：

1. 检查服务器控制台是否出现：
   ```
   [zMenu] zMenu has been enabled!
   ```

2. 在游戏内或控制台执行命令 `/zm version` 查看版本信息

### 步骤 4：初始配置

首次启动后，zMenu 将创建以下文件夹结构：

```
plugins/zMenu/
├── config.yml                    # 主配置文件
├── global-placeholders.yml       # 全局占位符值
├── commands/
│   └── commands.yml              # 自定义命令定义
├── inventories/
│   ├── basic_inventory.yml       # 基础示例库存
│   ├── advanced_inventory.yml    # 高级示例
│   └── pro_inventory.yml         # 专业示例
├── patterns/
│   └── pattern_example.yml       # 模式示例
├── items/
│   └── default-items.yml         # 可复用物品定义
├── actions_patterns/
│   └── default-actions.yml       # 默认动作模式
└── dialogs/                      # 对话框模板（1.20.5+）
```

## 可选依赖插件

zMenu 可独立运行，但与以下流行插件集成可增强功能：

### 推荐插件

| 插件 | 用途 |
|------|------|
| [PlaceholderAPI](https://www.spigotmc.org/resources/placeholderapi.6245/) | 在物品和消息中显示动态值 |
| [Vault](https://www.spigotmc.org/resources/vault.34315/) | 经济系统集成 |
| [LuckPerms](https://luckperms.net/) | 基于权限的条件判断 |

### 物品插件（可选）

这些插件允许您在菜单中使用自定义物品：

| 插件 | 集成支持 |
|------|----------|
| [ItemsAdder](https://www.spigotmc.org/resources/itemsadder.73355/) | 自定义物品与纹理 |
| [Oraxen](https://www.spigotmc.org/resources/oraxen.72448/) | 自定义物品与模型 |
| [HeadDatabase](https://www.spigotmc.org/resources/head-database.14280/) | 自定义玩家头颅 |
| [Slimefun](https://github.com/Slimefun/Slimefun4) | Slimefun 物品 |
| [MythicMobs](https://www.spigotmc.org/resources/mythicmobs.5702/) | MythicMobs 物品 |
| [ExecutableItems](https://www.spigotmc.org/resources/executableitems.77578/) | ExecutableItems 支持 |

### 其他集成

| 插件 | 集成支持 |
|------|----------|
| [Jobs Reborn](https://www.spigotmc.org/resources/jobs-reborn.4216/) | 职业等级条件判断 |
| [zHead](https://www.spigotmc.org/resources/zhead.115717/) | 自定义头颅纹理 |
| [packetevents](https://www.spigotmc.org/resources/packetevents-api.80279/) | 对话框系统必需（1.20.5+） |

## 配置文件

### config.yml

主配置文件控制插件的全局设置。以下是关键选项概览：

```yaml
# 启用调试模式以进行故障排查
enable-debug: false

# 数据库设置（SQLITE、MYSQL、MARIADB 或 NONE）
storage-type: SQLITE

# 启用 MiniMessage 格式（仅限 Paper/Purpur）
enable-mini-message-format: true

# 性能缓存设置
enable-cache-item-stack: true
enable-cache-placeholder-api: false

# 防复制保护
enable-anti-dupe: true

# 点击冷却时间以防止刷屏
enable-cooldown-click: true
cooldown-click-milliseconds: 100

# 主菜单按钮默认打开的库存
main-menu: "example"
```

完整选项请参阅 [config.yml 文档](./configurations/config-yml)。

## 故障排查

### 插件未加载

如果 zMenu 未出现在 `/plugins` 列表中：

1. 检查启动时控制台是否有错误
2. 确认 JAR 文件未损坏（必要时重新下载）
3. 确保使用兼容的服务端版本

### 配置错误

如果出现 YAML 解析错误：

1. 使用 YAML 验证工具如 [YAML Lint](http://www.yamllint.com/)
2. 检查缩进是否正确（使用空格，勿用 Tab）
3. 确保特殊字符已正确加引号

### 占位符不生效

如果 PlaceholderAPI 占位符无法工作：

1. 确认已安装 PlaceholderAPI
2. 安装所需扩展：`/papi ecloud download <扩展名>`
3. 重载 PlaceholderAPI：`/papi reload`

## 更新 zMenu

更新步骤：

1. 下载最新版本
2. 停止服务器
3. 用新版本替换旧的 `zMenu.jar`
4. 启动服务器
5. 若配置未更改，可执行 `/zm reload` 重载

:::warning 先备份
更新前务必备份 `plugins/zMenu/` 文件夹，特别是已自定义配置的情况下。
:::

## 后续步骤

zMenu 安装完成后，接下来学习如何：

1. [了解配置系统](./configurations/informations)
2. [创建您的第一个库存](./configurations/inventories/create-inventory)
3. [使用命令与权限](./configurations/commands-permissions)