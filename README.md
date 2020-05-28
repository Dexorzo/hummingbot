# HummingBot with Peatio Adapter

Assuming, that you have Docker installed https://docs.docker.com/get-docker/


## Getting Docker image

Login to private Docker registry first using your Gitlab account credentials:

```
docker login gitlab.tunex.io:5050
```

If you want to pull latest version run following command:

```
docker pull gitlab.tunex.io:5050/trading-bot/hummingbot
```

## Preparing barong api_keys

1. Turn on 2FA for user

![2FA](https://gitlab.tunex.io/trading-bot/hummingbot/-/raw/master/images/profile.png)

2. Add new api_key

![add key](https://gitlab.tunex.io/trading-bot/hummingbot/-/raw/master/images/keys.png)

3. Save key and secret

![add key](https://gitlab.tunex.io/trading-bot/hummingbot/-/raw/master/images/key.png)

## Global configurations

```
openware_api_url: https://domain_name/api/v2/peatio
openware_ranger_url: wss://domain_name/api/v2/ranger
trading_pair_splitter: ETH|USD|EUR
```

## Pure market making strategy config

```
# Exchange and token parameters
maker_market: openware
maker_market_trading_pair: ETH-EUR

# size of each order
order_amount: 7.17

# How far away from mid price to place the bid order
# Expressed in decimals : 0.01 = 1% away from mid price at that time
# Example if mid price is 100 and bid_place threshold is 0.01
# Your bid is placed at 99
bid_place_threshold: 0.0001

# How far away from mid price to place the ask order
# Expressed in decimals : 0.01 = 1% away from mid price at that time
# Example if mid price is 100 and ask_place threshold is 0.01
ask_place_threshold: 0.0001

# Ox ONLY - Sets the expiration of limit orders. Minimum is 130 seconds.
expiration_seconds: 130.0

# Time in seconds before cancelling and rerunning strategy
# If tick size is 60, the bot cancels active orders and reruns after a minute
# Your bid is placed at 101
cancel_order_wait_time: 32.0

# If you want to have single or multiple bids and asks on each side
mode: multiple

# If you have multiple orders number of orders you want to place on one side of the orderbook
number_of_orders: 5

# Size of the first bid and ask order for multiple order mode
order_start_size: 7.17

# Increment size of consecutive orders after the first order in multiple order mode
order_step_size: 1.42

# Spacing between orders in multiple order mode
order_interval_percent: 0.0001

# Toggle enabling of Inventory skew
inventory_skew_enabled: true

# Target base asset inventory percentage target to be maintained
inventory_target_base_percent: 0.5

# How long to wait before placing the next order in case your order gets filled for single order mode
filled_order_replenish_wait_time: 30.0

# Do you want to stop cancellations of orders on the other side when one side is filled
enable_order_filled_stop_cancellation: true

# Do you want to enable best bid ask jumping mode
best_bid_ask_jump_mode: false

# If best_bid_ask_jump_mode is True, what is the depth in base currency to be used for finding top bid and ask
best_bid_ask_jump_orders_depth: 0.0

# Parameter to enable/disable adding transaction costs to order prices
add_transaction_costs: false

# If external price source will be used for the mid price, true or false.
external_pricing_source: true

# The type of external pricing source (exchange/feed/custom_api).
external_price_source_type: exchange

# An external exchange name (for external exchange pricing source)
# IMPORTANT: this eschange must be have exactly exchange
external_price_source_exchange: coinbase_pro

# A base asset (for external feed pricing source), e.g. ETH
external_price_source_feed_base_asset:

# A quote asset (for external feed pricing source), e.g. USD
external_price_source_feed_quote_asset:

# An external api that returns price (for external custom_api pricing source)
external_price_source_custom_api:

# Include orders that will immediately match on the exchange? (Default is False)
include_matching_orders: true
```

## Creating configuration files

1. Create directory:

```
-- /pathTo/container_name
    |
    --- /conf
    |
    --- /data
    |
    --- /logs
```

2. Run bot with keeping STDIN open and allocating a pseudo-TTY

```
docker run -it --name container_name \
    --mount "type=bind,source=/pathTo/container_name/conf,destination=/conf/" \
    --mount "type=bind,source=/pathTo/container_name/logs,destination=/logs/" \
    --mount "type=bind,source=/pathTo/container_name/data,destination=/data/" \
    gitlab.tunex.io:5050/trading-bot/hummingbot
```

3. In bot graphic interface enter ```config``` and input all param values

Important! Remember password which you input during configurating process

## Running bot

```
docker run -itd \
    -e STRATEGY="pure_market_making" \
    -e CONFIG_FILE_NAME="conf_pure_market_making_strategy_0.yml" \
    -e CONFIG_PASSWORD="password" \
    --name container_name \
    --mount "type=bind,source=/pathTo/container_name/conf,destination=/conf/" \
    --mount "type=bind,source=/pathTo/container_name/logs,destination=/logs/" \
    --mount "type=bind,source=/pathTo/container_name/data,destination=/data/" \
    --restart "always" \
    --log-opt max-size=10m --log-opt max-file=5 \
    gitlab.tunex.io:5050/trading-bot/hummingbot
```