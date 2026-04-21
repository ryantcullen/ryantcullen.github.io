---
title: StockBot
summary: Open source Python backtesting engine for designing and evaluating stock trading algorithms.
tags: [Python, Finance, Open Source]
weight: 3
featured: true
link: https://github.com/ryantcullen/stock-bot
linkName: GitHub
---

An open-source Python backtesting engine for designing and testing daily stock trading algorithms against real historical market data.

## How It Works

Enter a stock ticker and StockBot fetches up to 10 years of price data from Yahoo Finance. It then steps through every trading day, computing four moving averages (10, 50, 100, and 200-day) along with derived indicators like slope, concavity, and crossover signals. A customizable `Decide()` function evaluates these on each day and places buy or sell orders. Position sizing uses the **Kelly Criterion** for mathematically optimal bet sizing, and results are benchmarked against a buy-and-hold baseline.

## Architecture

- **Portfolio**: Manages capital, share count, and order execution with Kelly Criterion position sizing.
- **MovingAverage**: Tracks a rolling average over a configurable window, computing slope, concavity, percent deviation, and crossover signals.
- **Main loop**: Fetches data, runs the day-by-day simulation, and renders a matplotlib chart with buy/sell markers and moving average overlays.

## Example Output

![PEN](https://github.com/ryantcullen/stock-bot/blob/master/Example%20Pictures/B4myQ0t.png?raw=true)

![BABA](https://github.com/ryantcullen/stock-bot/blob/master/Example%20Pictures/TwOShiK.png?raw=true)

![IBM](https://github.com/ryantcullen/stock-bot/blob/master/Example%20Pictures/lhWY5yX.png?raw=true)


