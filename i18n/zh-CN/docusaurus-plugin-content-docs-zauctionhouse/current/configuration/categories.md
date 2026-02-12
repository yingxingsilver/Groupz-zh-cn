---
sidebar_position: 4
title: Categories
description: Configure item categories in zAuctionHouse
---

# Categories Configuration

Categories organize items in the auction house, making it easier for players to find what they're looking for. Configure them in `categories.yml`.

## Basic Category Structure

```yaml
categories:
  weapons:
    # Display name
    name: "&cWeapons"

    # Icon in category menu
    icon:
      material: DIAMOND_SWORD
      name: "&cWeapons"
      lore:
        - "&7Swords, bows, and more"

    # Slot in category menu (0-53)
    slot: 10

    # Rules to match items (AND logic - all must match)
    rules:
      - type: material
        values:
          - DIAMOND_SWORD
          - IRON_SWORD
          - NETHERITE_SWORD
          - BOW
          - CROSSBOW
```

## Rule Types

### Material Rule

Match items by material type:

```yaml
rules:
  - type: material
    values:
      - DIAMOND_SWORD
      - DIAMOND_AXE
      - DIAMOND_PICKAXE
```

You can use wildcards:

```yaml
rules:
  - type: material
    values:
      - "*_SWORD"      # All swords
      - "*_AXE"        # All axes
      - "DIAMOND_*"    # All diamond items
```

### Name Rule

Match items by display name:

```yaml
rules:
  - type: name
    # Match mode: CONTAINS, EQUALS, STARTS_WITH, ENDS_WITH, REGEX
    mode: CONTAINS
    value: "Legendary"
    # Case sensitive matching
    case-sensitive: false
```

Examples:

```yaml
# Match items with "Epic" in the name
- type: name
  mode: CONTAINS
  value: "Epic"

# Match items starting with "[Mythic]"
- type: name
  mode: STARTS_WITH
  value: "[Mythic]"

# Match using regex (items with Roman numerals)
- type: name
  mode: REGEX
  value: ".*[IVX]+$"
```

### Lore Rule

Match items by lore content:

```yaml
rules:
  - type: lore
    mode: CONTAINS
    value: "Soulbound"
    # Check specific line (optional, -1 for any line)
    line: -1
```

### NBT Tag Rule

Match items by NBT tags (useful for custom items):

```yaml
rules:
  - type: tag
    # NBT path
    path: "CustomItem.type"
    value: "weapon"
```

For checking if a tag exists:

```yaml
rules:
  - type: tag
    path: "Enchantments"
    exists: true
```

### Custom Model Data Rule

Match items by custom model data:

```yaml
rules:
  - type: model-data
    # Exact value
    value: 1001

# Or range
- type: model-data
  min: 1000
  max: 1999
```

### Enchantment Rule

Match items by enchantments:

```yaml
rules:
  - type: enchantment
    # Enchantment name
    enchantment: SHARPNESS
    # Minimum level (optional)
    min-level: 1
```

## Rule Logic

### AND Logic (Default)

All rules must match for the item to be in the category:

```yaml
categories:
  legendary-weapons:
    name: "&6Legendary Weapons"
    # Item must be a sword AND have "Legendary" in name
    rules:
      - type: material
        values:
          - "*_SWORD"
      - type: name
        mode: CONTAINS
        value: "Legendary"
```

### OR Logic

Use `any-of` for OR logic - item matches if any rule matches:

```yaml
categories:
  weapons:
    name: "&cWeapons"
    any-of:
      - type: material
        values:
          - "*_SWORD"
      - type: material
        values:
          - BOW
          - CROSSBOW
```

### Combined Logic

Mix AND and OR:

```yaml
categories:
  epic-gear:
    name: "&5Epic Gear"
    # Must match the material rule AND at least one in any-of
    rules:
      - type: material
        values:
          - "*_SWORD"
          - "*_HELMET"
          - "*_CHESTPLATE"
    any-of:
      - type: name
        mode: CONTAINS
        value: "Epic"
      - type: lore
        mode: CONTAINS
        value: "Rare Item"
```

## Complete Categories Example

```yaml
# Default category for unmatched items
default-category: misc

categories:
  weapons:
    name: "&cWeapons"
    icon:
      material: DIAMOND_SWORD
      name: "&cWeapons"
      lore:
        - "&7Swords, axes, bows, and more"
        - ""
        - "&eClick to browse!"
    slot: 10
    rules:
      - type: material
        values:
          - "*_SWORD"
          - "*_AXE"
          - BOW
          - CROSSBOW
          - TRIDENT

  armor:
    name: "&9Armor"
    icon:
      material: DIAMOND_CHESTPLATE
      name: "&9Armor"
      lore:
        - "&7Helmets, chestplates, and more"
    slot: 11
    rules:
      - type: material
        values:
          - "*_HELMET"
          - "*_CHESTPLATE"
          - "*_LEGGINGS"
          - "*_BOOTS"
          - SHIELD
          - ELYTRA

  tools:
    name: "&6Tools"
    icon:
      material: DIAMOND_PICKAXE
      name: "&6Tools"
      lore:
        - "&7Pickaxes, shovels, and more"
    slot: 12
    rules:
      - type: material
        values:
          - "*_PICKAXE"
          - "*_SHOVEL"
          - "*_HOE"
          - SHEARS
          - FISHING_ROD
          - FLINT_AND_STEEL

  potions:
    name: "&dPotions"
    icon:
      material: POTION
      name: "&dPotions"
      lore:
        - "&7Potions and brewing materials"
    slot: 13
    rules:
      - type: material
        values:
          - POTION
          - SPLASH_POTION
          - LINGERING_POTION
          - TIPPED_ARROW

  enchanted:
    name: "&bEnchanted Items"
    icon:
      material: ENCHANTED_BOOK
      name: "&bEnchanted Items"
      lore:
        - "&7All enchanted items"
    slot: 14
    rules:
      - type: tag
        path: "Enchantments"
        exists: true

  blocks:
    name: "&aBlocks"
    icon:
      material: GRASS_BLOCK
      name: "&aBlocks"
      lore:
        - "&7Building blocks"
    slot: 15
    rules:
      - type: material
        values:
          - "*_BLOCK"
          - "*_BRICKS"
          - "*_PLANKS"
          - "*_SLAB"
          - "*_STAIRS"
          - "*_WALL"
          - "*_FENCE"

  food:
    name: "&6Food"
    icon:
      material: GOLDEN_APPLE
      name: "&6Food"
      lore:
        - "&7Food and consumables"
    slot: 16
    rules:
      - type: material
        values:
          - APPLE
          - GOLDEN_APPLE
          - ENCHANTED_GOLDEN_APPLE
          - BREAD
          - COOKED_BEEF
          - COOKED_PORKCHOP
          - COOKED_CHICKEN
          - COOKED_SALMON
          - GOLDEN_CARROT
          - CAKE

  misc:
    name: "&7Miscellaneous"
    icon:
      material: CHEST
      name: "&7Miscellaneous"
      lore:
        - "&7Everything else"
    slot: 19
    # No rules - catches everything not in other categories
```

## Category Priority

When an item matches multiple categories, it goes to the first matching category. Order your categories from most specific to least specific:

```yaml
categories:
  # Specific category first
  legendary-weapons:
    name: "&6Legendary Weapons"
    rules:
      - type: material
        values: ["*_SWORD"]
      - type: name
        mode: CONTAINS
        value: "Legendary"

  # General category after
  weapons:
    name: "&cWeapons"
    rules:
      - type: material
        values: ["*_SWORD"]
```
