---
title: StockBot
summary: Open source Python application for testing finance trading algorithms.
tags: [Python, Finance, Open Source]
weight: 3
featured: true
link: https://github.com/ryantcullen/stock-bot
linkName: Github
---

StockBot is a Python application for designing and testing your own daily stock trading algorithms.

## Installation

1. Clone this repository to your machine.

2. Use the package manager [pip](https://pip.pypa.io/en/stable/) to install matplotlib and yfinance from your command line.

```bash
pip install matplotlib
pip install yfinance
```

## Usage

Run the script from your command line

You will be asked to enter a ticker for the stock on which you want to test the algorithm.

## Designing Your Own Algorithm
If you want to modify the algorithm and design your own, you only need to change the Decide() function so that the followig parameters are updated based on your algorithm logic:
```
prob_profit = 0.501                             # probability of profit (eg, 50.1%)
expected_return = 1.2                           # expected return (eg. 120%)
modifier = 0.2                                  # value from 0-1; higher values = more aggressive bet
```
The Decide() function also takes three inputs, which correspond to moving averages of the stock over various tine frames. These may be useful when designing your algorithm. 

## Example Output
Here are a couple images depicting the output from an algorithm I developed. 

![PEN](https://github.com/ryantcullen/stock-bot/blob/master/Example%20Pictures/B4myQ0t.png?raw=true)

![BABA](https://github.com/ryantcullen/stock-bot/blob/master/Example%20Pictures/TwOShiK.png?raw=true)

![IBM](https://github.com/ryantcullen/stock-bot/blob/master/Example%20Pictures/lhWY5yX.png?raw=true)




