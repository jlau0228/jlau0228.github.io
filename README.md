# Analysis of [The Random Forest Model for Analyzing and Forecasting the US Stock Market in the Context of Smart Finance](https://arxiv.org/pdf/2402.17194)
## Introduction
The goal of this project was to analyze this paper and recreate the outcomes to verify it's validity, since the quality of the paper lacks sources, methods, and other important standards in academic writing/.
The goal of the algorithm in the paper was to determine how a stock would change in price categorically, either increasing or decreasing, based on known prior data. It looks at windows of 30, 60, and 90 days to determine whether t he target day of that stock increases or decreases\. 
## Filling in the Blanks
The algorithm used six financial indicators of stocks to classify stock criteria, which were not explained so I will first explain the indicators used\.
**RSI** is the relative strength index, which measures the speed and magnitude of a stock's recent price changes[^1]. The RSI is an oscillator that takes on values from 0 to 100. The calculation of RSI has two parts\.
```math
\text{RSI} = 100 - \left( \frac{100}{1 + \frac{\text{Average Gain}}{\text{Average Loss}}} \right)
```

[^1]:  [https://www.investopedia.com/terms/r/rsi.asp](https://www.investopedia.com/terms/r/rsi.asp)
