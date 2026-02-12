---
sidebar_position: 2
title: 物品组件
description: 组件系统(Minecraft 1.20.5+)
---

# 物品组件

从 Minecraft 1.20.5 开始，物品使用新的组件系统。zMenu 通过 `components` 配置区段提供对该系统的完整支持。

:::info
组件功能需要 Minecraft 1.20.5 或更高版本。部分组件仅在更新的版本中可用，每个组件名称旁的徽章会标明所需版本。
:::

## 基础结构

```yaml
item:
  material: DIAMOND_SWORD
  components:
    custom-name: "&6&l传奇之剑"
    lore:
      - "&7一把强大的武器"
    enchantments:
      sharpness: 5
```

## attack-range（1.21.11+）

定义物品的攻击范围（以方块为单位）。

Minecraft Wiki：[attack_range](https://minecraft.wiki/w/Data_component_format#attack_range)

| 键 | 类型 | 默认值 | 范围 | 描述 |
|---|------|--------|------|------|
| min-reach | 浮点数 | 0 | 0-64 | 生存模式下的最小攻击距离 |
| max-reach | 浮点数 | 3 | 0-64 | 生存模式下的最大攻击距离 |
| min-creative-reach | 浮点数 | 0 | 0-64 | 创造模式下的最小攻击距离 |
| max-creative-reach | 浮点数 | 5 | 0-64 | 创造模式下的最大攻击距离 |
| hitbox-margin | 浮点数 | 0.3 | 0-1 | 命中检测的额外边距 |
| mob-factor | 浮点数 | 1 | 0-2 | 应用于生物命中箱的倍率 |

```yaml
components:
  attack-range:
    min-reach: 0
    max-reach: 5
    min-creative-reach: 0
    max-creative-reach: 6
    hitbox-margin: 0.3
    mob-factor: 1.0
```

## attribute-modifiers

为物品添加属性修饰符。

Minecraft Wiki：[attribute_modifiers](https://minecraft.wiki/w/Data_component_format#attribute_modifiers)

| 键 | 类型 | 必需 | 描述 |
|---|------|------|------|
| type | 字符串 | 是 | 属性类型（例如：`generic.attack_damage`） |
| amount | 双精度浮点数 | 是 | 修饰符数值 |
| operation | 字符串 | 是 | 操作类型：`add_value`、`add_multiplied_base`、`add_multiplied_total` |
| slot | 字符串 | 否 | 装备槽位：`any`、`mainhand`、`offhand`、`head`、`chest`、`legs`、`feet` |
| id | 字符串 | 否 | 修饰符的唯一标识符 |

```yaml
components:
  attribute-modifiers:
    - type: generic.attack_damage
      amount: 10
      operation: add_value
      slot: mainhand
      id: "my_plugin:attack_bonus"
    - type: generic.movement_speed
      amount: 0.1
      operation: add_multiplied_base
      slot: feet
```

**可用属性类型：**
- `generic.armor` - 护甲值
- `generic.armor_toughness` - 护甲韧性
- `generic.attack_damage` - 攻击伤害
- `generic.attack_knockback` - 击退力度
- `generic.attack_speed` - 攻击速度
- `generic.flying_speed` - 飞行速度
- `generic.follow_range` - 跟随范围
- `generic.knockback_resistance` - 击退抗性
- `generic.luck` - 幸运值
- `generic.max_absorption` - 最大吸收值
- `generic.max_health` - 最大生命值
- `generic.movement_speed` - 移动速度
- `generic.scale` - 实体缩放比例
- `generic.step_height` - 台阶高度

## banner-patterns

定义旗帜物品上的图案。

Minecraft Wiki：[banner_patterns](https://minecraft.wiki/w/Data_component_format#banner_patterns)

```yaml
components:
  banner-patterns:
    - pattern: stripe_top
      color: red
    - pattern: cross
      color: blue
```

## base-color

设置盾牌和旗帜的基础颜色。

Minecraft Wiki：[base_color](https://minecraft.wiki/w/Data_component_format#base_color)

```yaml
components:
  base-color: RED
```

**可用颜色：** `WHITE`、`ORANGE`、`MAGENTA`、`LIGHT_BLUE`、`YELLOW`、`LIME`、`PINK`、`GRAY`、`LIGHT_GRAY`、`CYAN`、`PURPLE`、`BLUE`、`BROWN`、`GREEN`、`RED`、`BLACK`

## block-state

设置方块物品放置时的方块状态属性。

Minecraft Wiki：[block_state](https://minecraft.wiki/w/Data_component_format#block_state)

```yaml
components:
  block-state:
    facing: north
    powered: true
    waterlogged: false
```

## blocks-attacks（1.21.5+）

为物品配置类似盾牌的格挡行为。

Minecraft Wiki：[blocks_attacks](https://minecraft.wiki/w/Data_component_format#blocks_attacks)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| block-delay-seconds | 浮点数 | 0 | 格挡生效前的延迟时间 |
| disable-cooldown-scale | 浮点数 | 1 | 盾牌失效时的冷却缩放比例 |
| block-sound | 字符串 | - | 格挡时播放的声音 |
| disabled-sound | 字符串 | - | 盾牌失效时播放的声音 |
| item-damage.threshold | 浮点数 | 0 | 物品开始损耗前的伤害阈值 |
| item-damage.base | 浮点数 | 0 | 基础物品损耗值 |
| item-damage.factor | 浮点数 | 1.5 | 损耗倍率 |
| damage-reductions | 列表 | [] | 伤害减免规则列表 |

**伤害减免属性：**
| 键 | 类型 | 描述 |
|---|------|------|
| `type` | 字符串/列表 | 需要减免的伤害类型 |
| `base` | 浮点数 | 基础伤害减免值 |
| `factor` | 浮点数 | 伤害减免倍率 |
| `horizontal-blocking-angle` | 浮点数 | 格挡角度（度），默认 90 |

```yaml
components:
  blocks-attacks:
    block-delay-seconds: 0.25
    disable-cooldown-scale: 1.0
    block-sound: item.shield.block
    disabled-sound: item.shield.break
    item-damage:
      threshold: 3.0
      base: 0
      factor: 1.5
    damage-reductions:
      - type: player_attack
        base: 5.0
        factor: 0.5
        horizontal-blocking-angle: 90
```

## break-sound（1.21.5+）

设置物品损坏时播放的声音。

Minecraft Wiki：[break_sound](https://minecraft.wiki/w/Data_component_format#break_sound)

```yaml
components:
  break-sound: "entity.item.break"
```

## bundle-contents

在 bundle（捆绑包）中存储物品。

Minecraft Wiki：[bundle_contents](https://minecraft.wiki/w/Data_component_format#bundle_contents)

```yaml
components:
  bundle-contents:
    - material: DIAMOND
      amount: 10
    - material: EMERALD
      amount: 5
    - material: GOLD_INGOT
      amount: 20
```

## charged-projectiles

在弩中存储已装填的投射物。

Minecraft Wiki：[charged_projectiles](https://minecraft.wiki/w/Data_component_format#charged_projectiles)

```yaml
components:
  charged-projectiles:
    - material: ARROW
      amount: 1
    - material: SPECTRAL_ARROW
      amount: 1
```

## consumable（1.21.2+）

配置物品的消耗方式。

Minecraft Wiki：[consumable](https://minecraft.wiki/w/Data_component_format#consumable)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| consume-seconds | 浮点数 | 1.6 | 消耗物品所需时间（秒） |
| animation | 字符串 | EAT | 动画类型 |
| consume-sound | 字符串 | entity.generic.eat | 消耗时播放的声音 |
| has-consume-particles | 布尔值 | true | 消耗时是否显示粒子效果 |
| on-consume-effects | 列表 | [] | 消耗时应用的效果列表 |

```yaml
components:
  consumable:
    consume-seconds: 1.6
    animation: EAT
    consume-sound: entity.generic.eat
    has-consume-particles: true
    on-consume-effects:
      - type: APPLY_EFFECTS
        probability: 1.0
        effects:
          - type: speed
            amplifier: 1
            duration: 600
      - type: PLAY_SOUND
        sound: entity.player.burp
      - type: TELEPORT_RANDOMLY
        diameter: 16.0
      - type: CLEAR_ALL_EFFECTS
      - type: REMOVE_EFFECTS
        effects:
          - poison
          - wither
```

**动画类型：** `EAT`、`DRINK`、`BLOCK`、`BOW`、`SPEAR`、`CROSSBOW`、`SPYGLASS`、`TOOT_HORN`、`BRUSH`、`BUNDLE`

**效果类型：**
- `APPLY_EFFECTS` - 以指定概率应用药水效果
- `PLAY_SOUND` - 播放声音
- `TELEPORT_RANDOMLY` - 在指定直径内随机传送
- `CLEAR_ALL_EFFECTS` - 清除所有药水效果
- `REMOVE_EFFECTS` - 移除特定效果

## container

在物品内部存储物品（如潜影盒）。

Minecraft Wiki：[container](https://minecraft.wiki/w/Data_component_format#container)

| 键 | 类型 | 描述 |
|---|------|------|
| slot | 整数 | 槽位索引（潜影盒为 0-26） |
| material | 字符串 | 物品材质 |
| amount | 整数 | 物品数量 |

```yaml
components:
  container:
    - slot: 0
      material: DIAMOND
      amount: 64
    - slot: 1
      material: EMERALD
      amount: 32
```

## container-loot

为容器物品设置战利品表。

Minecraft Wiki：[container_loot](https://minecraft.wiki/w/Data_component_format#container_loot)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| loot-table | 字符串 | - | 战利品表名称（例如：`DESERT_PYRAMID`） |
| seed | 长整型 | 0 | 战利品生成的随机种子 |

```yaml
components:
  container-loot:
    loot-table: DESERT_PYRAMID
    seed: 12345
```

**可用战利品表：** `ABANDONED_MINESHAFT`、`BASTION_BRIDGE`、`BURIED_TREASURE`、`DESERT_PYRAMID`、`END_CITY_TREASURE`、`JUNGLE_TEMPLE`、`NETHER_BRIDGE`、`PILLAGER_OUTPOST`、`SHIPWRECK_TREASURE`、`SIMPLE_DUNGEON`、`STRONGHOLD_CORRIDOR`、`VILLAGE_ARMORER`、`WOODLAND_MANSION` 等。

## custom-data

在物品上存储自定义持久化数据（NBT）。

Minecraft Wiki：[custom_data](https://minecraft.wiki/w/Data_component_format#custom_data)

支持多种数据类型并自动进行类型检测：
- 字符串
- 布尔值
- 整数
- 长整型
- 双精度浮点数
- 单精度浮点数
- 字节
- 短整型
- byte[]、int[]、long[] 数组

```yaml
components:
  custom-
    "my_plugin:item_id": "legendary_sword"
    "my_plugin:level": 10
    "my_plugin:enchanted": true
    "my_plugin:damage_bonus": 5.5
```

## custom-model-data

用于资源包集成的自定义模型数据。

Minecraft Wiki：[custom_model_data](https://minecraft.wiki/w/Data_component_format#custom_model_data)

| 键 | 类型 | 描述 |
|---|------|------|
| floats | 列表(浮点数) | 用于谓词的浮点数值 |
| flags | 列表(布尔值) | 用于谓词的布尔标志 |
| strings | 列表(字符串) | 用于谓词的字符串值 |
| colors | 列表(颜色) | 颜色值（十六进制格式） |

```yaml
components:
  custom-model-
    floats:
      - 1.0
      - 2.5
    flags:
      - true
      - false
    strings:
      - "variant_a"
    colors:
      - "#FF5555"
      - "#55FF55"
```

## damage

设置物品的当前耐久损耗值。

Minecraft Wiki：[damage](https://minecraft.wiki/w/Data_component_format#damage)

```yaml
components:
  damage: 100
```

## damage-resistant

使物品对特定伤害类型具有抗性。

Minecraft Wiki：[damage_resistant](https://minecraft.wiki/w/Data_component_format#damage_resistant)

| 键 | 类型 | 描述 |
|---|------|------|
| types | 字符串 | 需要抵抗的伤害类型标签 |

```yaml
components:
  damage-resistant:
    types: "minecraft:is_fire"
```

**可用伤害类型标签：** `is_fire`、`is_explosion`、`is_projectile`、`is_fall`、`bypasses_armor`、`bypasses_shield`、`is_drowning`、`is_freezing`

## damage-type（1.21.11+）

设置物品作为武器使用时造成的伤害类型。

Minecraft Wiki：[damage_type](https://minecraft.wiki/w/Data_component_format#damage_type)

```yaml
components:
  damage-type: "minecraft:player_attack"
```

**常见伤害类型：** `player_attack`、`mob_attack`、`arrow`、`fireball`、`magic`、`wither`、`dragon_breath`、`freeze`、`sonic_boom`

## dye-color

为皮革盔甲及其他可染色物品设置染料颜色。

Minecraft Wiki：[dyed_color](https://minecraft.wiki/w/Data_component_format#dyed_color)

```yaml
components:
  dye-color: "#FF5555"
```

## enchantable（1.21.2+）

设置物品的附魔能力值。

Minecraft Wiki：[enchantable](https://minecraft.wiki/w/Data_component_format#enchantable)

```yaml
components:
  enchantable: 15
```

## enchantment-glint-override

强制启用或禁用附魔闪光效果。

Minecraft Wiki：[enchantment_glint_override](https://minecraft.wiki/w/Data_component_format#enchantment_glint_override)

```yaml
components:
  enchantment-glint-override: true   # 强制显示闪光
```

```yaml
components:
  enchantment-glint-override: false  # 移除闪光
```

## enchantments

为物品添加附魔。

Minecraft Wiki：[enchantments](https://minecraft.wiki/w/Data_component_format#enchantments)

```yaml
components:
  enchantments:
    sharpness: 5
    unbreaking: 3
    fire_aspect: 2
    mending: 1
```

## equippable（1.21.2+）

为任意物品配置装备属性。

Minecraft Wiki：[equippable](https://minecraft.wiki/w/Data_component_format#equippable)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| slot | 字符串 | - | 装备槽位：`HEAD`、`CHEST`、`LEGS`、`FEET`、`MAINHAND`、`OFFHAND`、`BODY` |
| equip-sound | 字符串 | - | 装备时播放的声音 |
| asset-id | 字符串 | - | 装备模型的资源位置 |
| dispensable | 布尔值 | true | 是否可被发射器装备 |
| swappable | 布尔值 | true | 是否可与其他装备交换 |
| damage-on-hurt | 布尔值 | true | 实体受伤时物品是否损耗 |
| equip-on-interact | 布尔值 | false | 是否可通过右键交互装备 |
| camera-overlay | 字符串 | - | 相机覆盖层的资源位置 |
| can-be-sheared | 布尔值 | false | 是否可被剪刀剪下 |
| shearing-sound | 字符串 | - | 剪下时播放的声音 |
| allowed-entities | 列表 | - | 可穿戴此物品的实体 |

```yaml
components:
  equippable:
    slot: HEAD
    equip-sound: item.armor.equip_leather
    asset-id: "minecraft:leather"
    dispensable: true
    swappable: true
    damage-on-hurt: true
    equip-on-interact: false
    can-be-sheared: false
    allowed-entities:
      - minecraft:player
      - minecraft:armor_stand
```

## firework-explosion

配置单个烟花之星的爆炸效果。

Minecraft Wiki：[firework_explosion](https://minecraft.wiki/w/Data_component_format#firework_explosion)

| 键 | 类型 | 描述 |
|---|------|------|
| shape | 字符串 | 形状：`BALL`、`LARGE_BALL`、`BURST`、`CREEPER`、`STAR` |
| colors | 颜色 | 主要颜色（十六进制格式） |
| fade_colors | 颜色 | 渐变颜色（十六进制格式） |
| has_trail | 布尔值 | 是否有拖尾效果 |
| has_twinkle | 布尔值 | 是否有闪烁效果 |

```yaml
components:
  firework-explosion:
    shape: STAR
    colors: "#FF0000"
    fade_colors: "#FFFF00"
    has_trail: true
    has_twinkle: true
```

## fireworks

配置烟花火箭。

Minecraft Wiki：[fireworks](https://minecraft.wiki/w/Data_component_format#fireworks)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| flight-duration | 整数 | 1 | 飞行持续时间（1-3） |
| explosions | 列表 | [] | 爆炸效果列表 |

**爆炸属性：**
| 键 | 类型 | 描述 |
|---|------|------|
| `shape` | 字符串 | 形状：`BALL`、`LARGE_BALL`、`BURST`、`CREEPER`、`STAR` |
| `colors` | 列表 | 主要颜色（十六进制格式） |
| `fade-colors` | 列表 | 渐变颜色（十六进制格式） |
| `has-trail` | 布尔值 | 是否有拖尾效果 |
| `has-twinkle` | 布尔值 | 是否有闪烁效果 |

```yaml
components:
  fireworks:
    flight-duration: 2
    explosions:
      - shape: LARGE_BALL
        colors:
          - "#FF0000"
          - "#00FF00"
        fade-colors:
          - "#FFFF00"
        has-trail: true
        has-twinkle: true
```

## food

使任意物品可食用。

Minecraft Wiki：[food](https://minecraft.wiki/w/Data_component_format#food)

| 键 | 类型 | 默认值 | 必需 | 描述 |
|---|------|--------|------|------|
| nutrition | 整数 | - | 是 | 恢复的食物点数（饥饿值） |
| saturation | 浮点数 | - | 是 | 饱食度修正值 |
| can-always-eat | 布尔值 | false | 否 | 即使不饥饿也能食用 |

```yaml
components:
  food:
    nutrition: 6
    saturation: 0.8
    can-always-eat: true
```

## glider（1.21.2+）

使物品在装备到胸甲槽位时具有鞘翅功能。

Minecraft Wiki：[glider](https://minecraft.wiki/w/Data_component_format#glider)

```yaml
components:
  glider: true
```

## instrument

配置山羊角乐器属性。

Minecraft Wiki：[instrument](https://minecraft.wiki/w/Data_component_format#instrument)

**简单配置（通过乐器名称）：**
```yaml
components:
  instrument: "minecraft:ponder_goat_horn"
```

**高级配置：**
| 键 | 类型 | 描述 |
|---|------|------|
| sound-event | 字符串 | 播放的声音 |
| use-duration | 浮点数 | 使用持续时间（秒） |
| range | 浮点数 | 听力范围（方块） |
| description | 字符串 | 乐器描述 |

```yaml
components:
  instrument:
    sound-event: "minecraft:ponder_goat_horn"
    use-duration: 7.0
    range: 256.0
    description: "一支神秘的山羊角"
```

**可用乐器：** `ponder_goat_horn`、`sing_goat_horn`、`seek_goat_horn`、`feel_goat_horn`、`admire_goat_horn`、`call_goat_horn`、`yearn_goat_horn`、`dream_goat_horn`

## item-model（1.21.2+）

直接设置物品模型。

Minecraft Wiki：[item_model](https://minecraft.wiki/w/Data_component_format#item_model)

```yaml
components:
  item-model: "minecraft:custom/my_model"
```

## item-name

设置物品的基础名称（无法在铁砧中修改，以斜体显示）。

Minecraft Wiki：[item_name](https://minecraft.wiki/w/Data_component_format#item_name)

```yaml
components:
  item-name: "&6特殊物品"
```

## jukebox-playable（1.21+）

使物品可在唱片机中播放。

Minecraft Wiki：[jukebox_playable](https://minecraft.wiki/w/Data_component_format#jukebox_playable)

```yaml
components:
  jukebox-playable: "minecraft:music_disc.cat"
```

## kinetic-weapon（1.21.11+）

配置骑乘战斗时类似骑枪的武器行为。

Minecraft Wiki：[kinetic_weapon](https://minecraft.wiki/w/Data_component_format#kinetic_weapon)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| delay-ticks | 整数 | 0 | 攻击前的延迟（游戏刻） |
| forward-movement | 浮点数 | 0 | 命中时的向前移动 |
| damage-multiplier | 浮点数 | 1 | 伤害倍率 |
| sound | 字符串 | - | 充能时的声音 |
| hit-sound | 字符串 | - | 命中时的声音 |
| damage-conditions | 对象 | - | 造成伤害的条件 |
| dismount-conditions | 对象 | - | 使目标下坐骑的条件 |
| knockback-conditions | 对象 | - | 击退的条件 |

**条件属性：**
| 键 | 类型 | 描述 |
|---|------|------|
| `max-duration-ticks` | 整数 | 条件的最大持续时间（游戏刻） |
| `min-speed` | 浮点数 | 玩家的最小速度 |
| `min-relative-speed` | 浮点数 | 相对于目标的最小速度 |

```yaml
components:
  kinetic-weapon:
    delay-ticks: 0
    forward-movement: 0.5
    damage-multiplier: 2.0
    sound: "entity.player.attack.sweep"
    hit-sound: "entity.player.attack.strong"
    damage-conditions:
      max-duration-ticks: 100
      min-speed: 0.5
      min-relative-speed: 0.3
    dismount-conditions:
      max-duration-ticks: 50
      min-speed: 0.8
      min-relative-speed: 0.5
```

## lodestone-tracker

配置指南针的磁石追踪功能。

Minecraft Wiki：[lodestone_tracker](https://minecraft.wiki/w/Data_component_format#lodestone_tracker)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| tracked | 布尔值 | true | 是否追踪磁石 |
| target.post | 列表-整数 | - | 目标坐标 [x, y, z] |
| target.dimension | 字符串 | - | 世界名称 |

```yaml
components:
  lodestone-tracker:
    tracked: true
    target:
      post: [100, 64, -200]
      dimension: world
```

## lore

设置物品的描述文本行。

Minecraft Wiki：[lore](https://minecraft.wiki/w/Data_component_format#lore)

```yaml
components:
  lore:
    - "&7第一行"
    - "&7第二行"
    - ""
    - "&e点击使用！"
```

或单行形式：
```yaml
components:
  lore: "&7简单描述"
```

## map-color

为已填充地图设置地图标记颜色。

Minecraft Wiki：[map_color](https://minecraft.wiki/w/Data_component_format#map_color)

```yaml
components:
  map-color: "#FF5555"
```

## map-id

为已填充地图设置地图 ID。

Minecraft Wiki：[map_id](https://minecraft.wiki/w/Data_component_format#map_id)

```yaml
components:
  map-id: 1234
```

## max-damage

设置物品的最大耐久度。

Minecraft Wiki：[max_damage](https://minecraft.wiki/w/Data_component_format#max_damage)

```yaml
components:
  max-damage: 2000
```

## max-stack-size

覆盖物品的最大堆叠数量（1-99）。

Minecraft Wiki：[max_stack_size](https://minecraft.wiki/w/Data_component_format#max_stack_size)

```yaml
components:
  max-stack-size: 16
```

## minimum-attack-charge（1.21.11+）

设置造成伤害所需的最小攻击蓄力值。

Minecraft Wiki：[minimum_attack_charge](https://minecraft.wiki/w/Data_component_format#minimum_attack_charge)

```yaml
components:
  minimum-attack-charge: 0.5
```

## ominous-bottle-amplifier

设置不祥之瓶的不祥预感等级。

Minecraft Wiki：[ominous_bottle_amplifier](https://minecraft.wiki/w/Data_component_format#ominous_bottle_amplifier)

```yaml
components:
  ominous-bottle-amplifier: 4
```

## piercing-weapon（1.21.11+）

配置类似三叉戟的武器行为。

Minecraft Wiki：[piercing_weapon](https://minecraft.wiki/w/Data_component_format#piercing_weapon)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| deals-knockback | 布尔值 | true | 命中是否造成击退 |
| dismounts | 布尔值 | false | 命中是否使骑乘者下坐骑 |
| sound | 字符串 | - | 投掷时的声音 |
| hit-sound | 字符串 | - | 命中时的声音 |

```yaml
components:
  piercing-weapon:
    deals-knockback: true
    dismounts: true
    sound: "item.trident.throw"
    hit-sound: "item.trident.hit"
```

## potion-contents

配置药水效果和外观。

Minecraft Wiki：[potion_contents](https://minecraft.wiki/w/Data_component_format#potion_contents)

| 键 | 类型 | 描述 |
|---|------|------|
| potion | 字符串 | 基础药水类型（例如：`speed`、`healing`） |
| custom-color | 字符串 | 自定义药水颜色（十六进制格式） |
| custom-name | 字符串 | 自定义药水名称 |
| custom-effects | 列表 | 自定义药水效果列表 |

**自定义效果属性：**
| 键 | 类型 | 描述 |
|---|------|------|
| `type` | 字符串 | 效果类型（例如：`speed`、`jump_boost`） |
| `amplifier` | 整数 | 效果等级（0 = 1 级） |
| `duration` | 整数 | 持续时间（游戏刻，20 游戏刻 = 1 秒） |
| `ambient` | 布尔值 | 是否显示环境粒子（如信标） |
| `show-particles` | 布尔值 | 是否显示效果粒子 |
| `show-icon` | 布尔值 | 是否显示效果图标 |

```yaml
components:
  potion-contents:
    potion: speed
    custom-color: "#FF0000"
    custom-name: "&c超级速度药水"
    custom-effects:
      - type: speed
        amplifier: 1
        duration: 600
        ambient: false
        show-particles: true
        show-icon: true
      - type: jump_boost
        amplifier: 2
        duration: 600
```

## potion-duration-scale（1.21.5+）

缩放药水效果的持续时间。

Minecraft Wiki：[potion_duration_scale](https://minecraft.wiki/w/Data_component_format#potion_duration_scale)

```yaml
components:
  potion-duration-scale: 1.5
```

## profile（1.21.9+）

配置玩家头颅外观。

Minecraft Wiki：[profile](https://minecraft.wiki/w/Data_component_format#profile)

**简单配置（通过玩家名称或 UUID）：**
```yaml
components:
  profile: "Notch"
```

```yaml
components:
  profile: "069a79f4-44e9-4726-a5be-fca90e38aaf5"
```

**通过纹理 URL：**
```yaml
components:
  profile: "http://textures.minecraft.net/texture/abc123..."
```

**高级配置：**
```yaml
components:
  profile:
    id: "069a79f4-44e9-4726-a5be-fca90e38aaf5"
    name: "Notch"
    texture: "http://textures.minecraft.net/texture/..."
    model: classic  # 或 "slim"
    cape: "http://textures.minecraft.net/texture/..."
```

## rarity

设置物品稀有度，影响名称颜色。

Minecraft Wiki：[rarity](https://minecraft.wiki/w/Data_component_format#rarity)

```yaml
components:
  rarity: EPIC
```

**可用值：**
- `COMMON` - 白色名称
- `UNCOMMON` - 黄色名称
- `RARE` - 青色名称
- `EPIC` - 浅紫色名称

## recipes

获得物品时解锁配方（知识之书）。

Minecraft Wiki：[recipes](https://minecraft.wiki/w/Data_component_format#recipes)

```yaml
components:
  recipes:
    - "minecraft:diamond_sword"
    - "minecraft:diamond_pickaxe"
    - "minecraft:enchanting_table"
```

## repair-cost

设置铁砧修复成本。

Minecraft Wiki：[repair_cost](https://minecraft.wiki/w/Data_component_format#repair_cost)

```yaml
components:
  repair-cost: 5
```

## stored-enchantments

存储在附魔书中的附魔。

Minecraft Wiki：[stored_enchantments](https://minecraft.wiki/w/Data_component_format#stored_enchantments)

```yaml
components:
  stored-enchantments:
    mending: 1
    unbreaking: 3
```

## suspicious-stew-effects

为可疑炖菜设置药水效果。

Minecraft Wiki：[suspicious_stew_effects](https://minecraft.wiki/w/Data_component_format#suspicious_stew_effects)

```yaml
components:
  suspicious-stew-effects:
    - type: blindness
      amplifier: 0
      duration: 100
    - type: saturation
      amplifier: 0
      duration: 200
```

## swing-animation（1.21.11+）

配置使用物品时的挥动动画。

Minecraft Wiki：[swing_animation](https://minecraft.wiki/w/Data_component_format#swing_animation)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| duration | 整数 | 6 | 动画持续时间（游戏刻） |
| type | 字符串 | WHACK | 动画类型 |

```yaml
components:
  swing-animation:
    duration: 6
    type: WHACK
```

**动画类型：** `WHACK`、`BRUSH`

## tool

将任意物品转变为具有挖掘能力的工具。

Minecraft Wiki：[tool](https://minecraft.wiki/w/Data_component_format#tool)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| default-mining-speed | 浮点数 | 1.0 | 未在规则中指定方块的默认挖掘速度 |
| damage-per-block | 整数 | 1 | 每挖掘一个方块损失的耐久度 |
| can-destroy-blocks-in-creative | 布尔值 | true | 创造模式下是否可破坏方块 |
| rules | 列表 | [] | 方块专属挖掘规则 |

**规则属性：**
| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| `blocks` | 列表/字符串 | - | 方块或方块标签 |
| `speed` | 浮点数 | `1.0` | 这些方块的挖掘速度 |
| `correct-for-drops` | 布尔值 | `false` | 挖掘时方块是否正常掉落物品 |

```yaml
components:
  tool:
    default-mining-speed: 1.0
    damage-per-block: 1
    can-destroy-blocks-in-creative: true
    rules:
      - blocks:
          - minecraft:stone
          - minecraft:granite
          - minecraft:diorite
        speed: 8.0
        correct-for-drops: true
      - blocks: "#minecraft:mineable/pickaxe"
        speed: 4.0
        correct-for-drops: true
```

## tooltip-display（1.21.5+）

控制工具提示的可见性。

Minecraft Wiki：[tooltip_display](https://minecraft.wiki/w/Data_component_format#tooltip_display)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| hide-tooltip | 布尔值 | false | 隐藏整个工具提示 |

```yaml
components:
  tooltip-display:
    hide-tooltip: true
```

## tooltip-style（1.21.2+）

从资源包设置自定义工具提示样式。

Minecraft Wiki：[tooltip_style](https://minecraft.wiki/w/Data_component_format#tooltip_style)

```yaml
components:
  tooltip-style: "minecraft:custom_tooltip"
```

## trim

应用盔甲镶边图案。

Minecraft Wiki：[trim](https://minecraft.wiki/w/Data_component_format#trim)

| 键 | 类型 | 必需 | 描述 |
|---|------|------|------|
| material | 字符串 | 是 | 镶边材质 |
| pattern | 字符串 | 是 | 镶边图案 |

```yaml
components:
  trim:
    material: gold
    pattern: sentry
```

**材质：** `amethyst`、`copper`、`diamond`、`emerald`、`gold`、`iron`、`lapis`、`netherite`、`quartz`、`redstone`

**图案：** `coast`、`dune`、`eye`、`host`、`raiser`、`rib`、`sentry`、`shaper`、`silence`、`snout`、`spire`、`tide`、`vex`、`ward`、`wayfinder`、`wild`

## unbreakable

使物品不可损坏。

Minecraft Wiki：[unbreakable](https://minecraft.wiki/w/Data_component_format#unbreakable)

```yaml
components:
  unbreakable: true
```

## use-cooldown（1.21.2+）

为物品使用后添加冷却时间。

Minecraft Wiki：[use_cooldown](https://minecraft.wiki/w/Data_component_format#use_cooldown)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| seconds | 浮点数 | 0 | 冷却持续时间（秒） |
| cooldown-group | 字符串 | - | 冷却组标识符（同组物品共享冷却） |

```yaml
components:
  use-cooldown:
    seconds: 1.0
    cooldown-group: "my_plugin:special_items"
```

## use-effects（1.21.11+）

配置使用物品时的效果（如望远镜）。

Minecraft Wiki：[use_effects](https://minecraft.wiki/w/Data_component_format#use_effects)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| can-sprint | 布尔值 | false | 使用时是否可疾跑 |
| speed-multiplier | 浮点数 | 0.2 | 使用时的移动速度倍率 |
| interact-vibrations | 布尔值 | true | 交互时是否发出幽匿感测器振动 |

```yaml
components:
  use-effects:
    can-sprint: false
    speed-multiplier: 0.2
    interact-vibrations: true
```

## use-remainder（1.21.2+）

使用后留下的物品（如炖菜使用后的碗）。

Minecraft Wiki：[use_remainder](https://minecraft.wiki/w/Data_component_format#use_remainder)

```yaml
components:
  use-remainder:
    material: BOWL
    amount: 1
```

## weapon（1.21.5+）

配置武器伤害属性。

Minecraft Wiki：[weapon](https://minecraft.wiki/w/Data_component_format#weapon)

| 键 | 类型 | 默认值 | 描述 |
|---|------|--------|------|
| item-damage-per-attack | 整数 | 1 | 每次攻击损失的耐久度 |
| disable-blocking-for-seconds | 浮点数 | 0 | 禁用盾牌格挡的持续时间 |

```yaml
components:
  weapon:
    item-damage-per-attack: 1
    disable-blocking-for-seconds: 5.0
```

## writable-book-content

书写之书（可书写书籍）的内容。

Minecraft Wiki：[writable_book_content](https://minecraft.wiki/w/Data_component_format#writable_book_content)

| 键 | 类型 | 描述 |
|---|------|------|
| pages | 列表 | 包含原始文本内容的页面列表 |

```yaml
components:
  writable-book-content:
    pages:
      - title: "我的日记"
        raw: "第1天：开始了我的冒险..."
      - raw: "第2天：发现了一个村庄..."
      - raw: "第3天：发现了一座要塞！"
```

## written-book-content

已签名成书的内容。

Minecraft Wiki：[written_book_content](https://minecraft.wiki/w/Data_component_format#written_book_content)

| 键 | 类型 | 描述 |
|---|------|------|
| title | 字符串 | 书籍标题 |
| author | 字符串 | 书籍作者名称 |
| generation | 字符串 | 书籍生成代数：`ORIGINAL`、`COPY_OF_ORIGINAL`、`COPY_OF_COPY`、`TATTERED` |
| pages | 列表 | 包含原始文本内容的页面列表 |

```yaml
components:
  written-book-content:
    title: "Herobrine 的传说"
    author: "Notch"
    generation: ORIGINAL
    pages:
      - raw: "第一章\n\n那是一个风雨交加的夜晚..."
      - raw: "第二章\n\n神秘的身影出现了..."
      - raw: "完"
```

## 完整示例

以下是一个使用组件创建的完整传奇之剑示例：

```yaml
item:
  material: DIAMOND_SWORD
  components:
    custom-name: "&6&l✦ 石中剑 ✦"
    lore:
      - "&7王者的传奇之剑"
      - ""
      - "&7伤害: &c+15"
      - "&7攻击速度: &a+1.6"
      - ""
      - "&5&l由龙焰锻造"
      - ""
      - "&e右键使用特殊能力！"
    rarity: EPIC
    enchantments:
      sharpness: 10
      fire_aspect: 2
      unbreaking: 5
      mending: 1
    enchantment-glint-override: true
    attribute-modifiers:
      - type: generic.attack_damage
        amount: 15
        operation: add_value
        slot: mainhand
      - type: generic.attack_speed
        amount: 1.6
        operation: add_value
        slot: mainhand
    unbreakable: true
    max-damage: 5000
    attack-range:
      max-reach: 4
      max-creative-reach: 6
```

## 版本兼容性

| 组件 | 最低版本 |
|------|----------|
| 大多数组件 | 1.20.5 |
| `food`、`tool`、`container-loot`、`custom-data`、`damage-resistant`、`ominous-bottle-amplifier` | 1.20.5 |
| `jukebox-playable` | 1.21 |
| `consumable`、`enchantable`、`equippable`、`glider`、`item-model`、`tooltip-style`、`use-cooldown`、`use-remainder` | 1.21.2 |
| `blocks-attacks`、`break-sound`、`potion-duration-scale`、`tooltip-display`、`weapon` | 1.21.5 |
| `profile` | 1.21.9 |
| `attack-range`、`damage-type`、`kinetic-weapon`、`minimum-attack-charge`、`piercing-weapon`、`swing-animation`、`use-effects` | 1.21.11 |

## 后续步骤

- 学习如何在 [物品配置](./item) 中使用组件
- 将物品添加到 [按钮](../buttons/button)
- 创建可复用模板的 [模式](../patterns)