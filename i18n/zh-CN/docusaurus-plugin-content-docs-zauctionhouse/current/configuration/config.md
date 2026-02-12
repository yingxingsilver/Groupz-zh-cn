---
sidebar_position: 1
title: Main Configuration
description: Main configuration options for zAuctionHouse
---

# Main Configuration

The main configuration file `config.yml` controls the core behavior of zAuctionHouse.

## Storage Configuration

Configure how auction data is stored:

```yaml
storage:
  # Storage type: SQLITE, MYSQL, MARIADB
  type: SQLITE

  # MySQL/MariaDB settings (ignored for SQLite)
  host: localhost
  port: 3306
  database: zauctionhouse
  user: root
  password: password
  useSSL: false

  # Connection pool settings
  pool:
    maximum-pool-size: 10
    minimum-idle: 5
    connection-timeout: 30000
    idle-timeout: 600000
    max-lifetime: 1800000
```

## Multi-Server Configuration

Enable auction synchronization across multiple servers:

```yaml
multi-server:
  # Enable multi-server synchronization
  enabled: false

  # Sync interval in seconds (0 for real-time via database triggers)
  sync-interval: 5

  # Server identifier (unique per server)
  server-id: server-1
```

## Number Formatting

Configure how numbers are displayed:

```yaml
formatting:
  # Enable compact number format (1K, 1M, 1B)
  compact-numbers: true

  # Decimal places for compact numbers
  compact-decimals: 1

  # Suffixes for compact numbers
  suffixes:
    thousand: "K"
    million: "M"
    billion: "B"
    trillion: "T"

  # Number of decimal places for prices
  price-decimals: 2

  # Thousand separator
  thousand-separator: ","

  # Decimal separator
  decimal-separator: "."
```

Example output with these settings:
- `1500` displays as `1.5K`
- `2500000` displays as `2.5M`
- `1234.56` displays as `1,234.56`

## Expiration Settings

Configure item expiration behavior:

```yaml
expiration:
  # Default expiration time for listed items
  default: 7d

  # Time format: s (seconds), m (minutes), h (hours), d (days)

  # Maximum expiration time allowed
  maximum: 30d

  # Check expired items interval (in minutes)
  check-interval: 5

  # Delete expired items after this time (0 to keep forever)
  delete-after: 30d

  # Notify players about expiring items
  notify-before-expiration: true
  notify-time: 1h
```

## Item Limits

Configure listing limits per player:

```yaml
limits:
  # Default limit for players without specific permissions
  default: 10

  # Permission-based limits (highest takes priority)
  permissions:
    - permission: zauctionhouse.limit.5
      limit: 5
    - permission: zauctionhouse.limit.10
      limit: 10
    - permission: zauctionhouse.limit.25
      limit: 25
    - permission: zauctionhouse.limit.50
      limit: 50
    - permission: zauctionhouse.limit.100
      limit: 100
    - permission: zauctionhouse.limit.unlimited
      limit: -1  # -1 for unlimited
```

## Cooldowns

Configure cooldowns for various actions:

```yaml
cooldowns:
  # Cooldown between listing items (in seconds)
  sell: 5

  # Cooldown between purchases (in seconds)
  purchase: 3

  # Cooldown for using the search feature (in seconds)
  search: 2
```

## Banned Worlds

Prevent auction house usage in specific worlds:

```yaml
banned-worlds:
  - minigames
  - pvp_arena
  - creative
```

## Sorting Options

Configure default sorting and available sort options:

```yaml
sorting:
  # Default sort method
  default: NEWEST

  # Available sort methods
  # NEWEST, OLDEST, PRICE_LOW, PRICE_HIGH, NAME_AZ, NAME_ZA

  # Remember player's sort preference
  remember-preference: true
```

## Anti-Exploit Settings

Protection against exploits and duplication:

```yaml
protection:
  # Prevent selling items with specific NBT tags
  block-creative-items: true

  # Maximum stack size to sell (0 for no limit)
  max-stack-size: 64

  # Minimum time between identical item listings (seconds)
  duplicate-listing-cooldown: 10

  # Check TPS before allowing actions
  tps-protection:
    enabled: true
    minimum-tps: 15.0
```

## Logging

Configure action logging:

```yaml
logging:
  # Log to file
  file-logging: true

  # Log file location
  log-file: logs/auction.log

  # Actions to log
  log-actions:
    - SELL
    - PURCHASE
    - EXPIRE
    - REMOVE
    - CLAIM
```

## Notifications

Configure player notifications:

```yaml
notifications:
  # Notify seller when item is purchased
  notify-seller-on-sale: true

  # Notify player when item expires
  notify-on-expiration: true

  # Sound effects
  sounds:
    enabled: true
    on-sale: ENTITY_PLAYER_LEVELUP
    on-purchase: ENTITY_EXPERIENCE_ORB_PICKUP
    on-expiration: BLOCK_NOTE_BLOCK_BASS
```

## Full Example

Here's a complete `config.yml` example:

```yaml
storage:
  type: SQLITE

multi-server:
  enabled: false
  sync-interval: 5
  server-id: server-1

formatting:
  compact-numbers: true
  compact-decimals: 1
  suffixes:
    thousand: "K"
    million: "M"
    billion: "B"
  price-decimals: 2
  thousand-separator: ","
  decimal-separator: "."

expiration:
  default: 7d
  maximum: 30d
  check-interval: 5
  delete-after: 30d
  notify-before-expiration: true
  notify-time: 1h

limits:
  default: 10
  permissions:
    - permission: zauctionhouse.limit.25
      limit: 25
    - permission: zauctionhouse.limit.50
      limit: 50
    - permission: zauctionhouse.limit.unlimited
      limit: -1

cooldowns:
  sell: 5
  purchase: 3
  search: 2

banned-worlds:
  - minigames

sorting:
  default: NEWEST
  remember-preference: true

protection:
  block-creative-items: true
  max-stack-size: 64
  tps-protection:
    enabled: true
    minimum-tps: 15.0

logging:
  file-logging: true
  log-file: logs/auction.log

notifications:
  notify-seller-on-sale: true
  notify-on-expiration: true
  sounds:
    enabled: true
    on-sale: ENTITY_PLAYER_LEVELUP
```
