---
sidebar_position: 1
title: 入门指南
description: zMenu 简介 - Minecraft 最强大的库存插件
---

# 入门指南



## 开始使用 zMenu

zMenu 是 Minecraft 服务器最强大、最灵活的库存（GUI）插件。它允许您通过完善的配置系统创建精美、可交互的菜单，让您完全掌控库存的每个方面。

## 什么是 zMenu？

zMenu 是一款高品质的库存插件，使服务器管理员能够为 Minecraft 服务器创建自定义 GUI（图形用户界面）。无论您需要简单的服务器选择器、复杂的商店系统，还是可交互的任务菜单，zMenu 都提供了您所需的所有工具。

## 核心特性

### 高度可定制的库存
- 创建任意大小的库存（9 至 54 个槽位）
- 支持多页并自动分页
- 支持占位符的自定义标题
- 为空槽位填充物品
- 基于矩阵的布局系统，设计更轻松

### 强大的按钮系统
- 9 种内置按钮类型：NONE（无）、INVENTORY（库存）、BACK（返回）、NEXT（下一页）、PREVIOUS（上一页）、HOME（首页）、JUMP（跳转）、MAIN_MENU（主菜单）、SWITCH（切换）
- 为每个鼠标按键设置自定义点击动作
- 查看与点击权限要求
- 支持自定义纹理的玩家头颅
- 动态物品更新

### 丰富的动作系统
- 28+ 种动作类型，包括消息、音效、命令、传送等
- 以玩家、控制台或 OP 权限执行动作
- 支持多个动作链式执行
- 带条件判断的动作（基于权限要求）

### 高级特性
- **模式系统**：创建可复用的按钮模板
- **玩家数据**：存储和读取玩家专属数据
- **全局占位符**：定义在所有库存中通用的值
- **PlaceholderAPI 支持**：完整集成 PAPI
- **MiniMessage 支持**：为 Paper 服务器提供现代化文本格式
- **防复制系统**：内置防止物品复制漏洞的保护机制
- **数据库支持**：支持 MySQL、MariaDB 和 SQLite 进行数据持久化

### 开发者友好
- 清晰、文档完善的 API
- 支持注册自定义按钮类型
- 支持注册自定义动作类型
- 库存交互事件系统

## 为什么选择 zMenu？

| 特性 | zMenu | 其他插件 |
|------|-------|----------|
| 按钮类型 | 9+ 种内置 | 有限 |
| 动作类型 | 28+ | 基础 |
| 多页支持 | 原生支持 | 通常需要变通方案 |
| 模式系统 | 支持 | 不支持 |
| 玩家数据存储 | 支持 | 很少支持 |
| MiniMessage 支持 | 支持 | 很少支持 |
| 数据库支持 | MySQL/MariaDB/SQLite | 通常仅支持文件存储 |
| API 质量 | 全面完善 | 通常较简陋 |
| 活跃开发 | 是 | 因插件而异 |
| 免费 | 是 | 通常收费 |

## 工作原理

zMenu 使用 YAML 配置文件定义您的库存。以下是简化的工作流程：

- 库存在 `inventories/` 文件夹中定义
- 每个库存包含按钮（玩家可交互的物品）
- 按钮可设置点击时执行的动作
- 权限要求控制谁可以查看或点击按钮
- 模式（Patterns）允许复用按钮配置

### 简单示例

```yaml
# inventories/my_menu.yml
name: " &6我的第一个菜单 "
size: 27

items:
  welcome-button:
    slot: 13
    item:
      material: DIAMOND
      name: " &b欢迎！ "
      lore:
        - " &7点击我接收消息 "
    actions:
      - type: message
        messages:
          - " &a你好，%player%！ "
          - " &7感谢使用 zMenu！ "
```

此配置将创建一个 27 槽位的库存，中央放置一颗钻石，点击后发送消息。

## 支持的 Minecraft 版本

zMenu 支持 Minecraft 1.19 至 1.21+ 版本，兼容以下服务端：
- Spigot
- Paper（推荐）
- Purpur
- Pufferfish
- Folia

## 后续步骤

准备开始？请按以下步骤操作：
- 在服务器上[安装 zMenu](./installation)
- 了解[配置系统](./configurations/informations)
- [创建您的第一个库存](./configurations/inventories/create-inventory)
- 探索[按钮类型](./configurations/buttons/button)和[动作类型](./configurations/buttons/actions)

## 获取帮助

- Discord：加入我们的 [Discord 服务器](https://discord.groupez.dev) 获取支持
- Modrinth：从 [Modrinth](https://modrinth.com/plugin/zmenu) 下载插件
- GitHub：在 [GitHub](https://github.com/Maxlego08/zMenu) 上报告问题
- 开发版本：在 Discord 的 `#builds` 频道获取开发构建版本