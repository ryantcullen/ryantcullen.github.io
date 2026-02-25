---
title: StockBot
summary: Open source Python backtesting engine for designing and evaluating stock trading algorithms.
tags: [Python, Finance, Open Source]
weight: 3
featured: true
link: https://github.com/ryantcullen/stock-bot
linkName: GitHub
---

StockBot is an open-source Python backtesting engine that lets you design, test, and visualize daily stock trading algorithms against real historical market data.

## How It Works

The user enters a stock ticker and StockBot fetches up to 10 years of historical price data via the Yahoo Finance API. It then iterates through every trading day, computing four moving averages (10, 50, 100, and 200-day windows) along with derived indicators like slope, concavity, and crossover signals. On each day, a customizable `Decide()` function evaluates these indicators and executes buy or sell orders.

Position sizing uses the **Kelly Criterion**, which calculates the mathematically optimal bet size given an expected return and probability of profit. The result is compared against a simple buy-and-hold baseline to evaluate the algorithm's performance.

## Architecture

- **Portfolio** — Manages capital, share count, and order execution. Uses the Kelly Criterion for position sizing.
- **MovingAverage** — Tracks a rolling average over a configurable window, computing first and second derivatives (slope and concavity), percent deviation from the mean, and price crossover detection.
- **Main loop** — Fetches data, runs the simulation day-by-day, and renders the results as a matplotlib chart with buy/sell markers and moving average overlays.

## Example Output

![PEN](https://github.com/ryantcullen/stock-bot/blob/master/Example%20Pictures/B4myQ0t.png?raw=true)

![BABA](https://github.com/ryantcullen/stock-bot/blob/master/Example%20Pictures/TwOShiK.png?raw=true)

![IBM](https://github.com/ryantcullen/stock-bot/blob/master/Example%20Pictures/lhWY5yX.png?raw=true)


