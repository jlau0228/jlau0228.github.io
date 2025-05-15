# Analysis of [The Random Forest Model for Analyzing and Forecasting the US Stock Market in the Context of Smart Finance](https://arxiv.org/pdf/2402.17194)
## Introduction
The goal of this project was to analyze this paper and recreate the outcomes to verify it's validity, since the quality of the paper lacks sources, methods, and other important standards in academic writing/.
The goal of the algorithm in the paper was to determine how a stock would change in price categorically, either increasing or decreasing, based on known prior data. It looks at windows of 30, 60, and 90 days to determine whether t he target day of that stock increases or decreases\. 
## Filling in the Blanks
The algorithm used six financial indicators of stocks to classify stock criteria, which were not explained so I will first explain the indicators used\.
**RSI** is the relative strength index, which measures the speed and magnitude of a stock's recent price changes[^1]. The RSI is an oscillator that takes on values from 0 to 100. The calculation of RSI has two parts. Since RSI is a momentum oscillator, the first 13 values are not defined, since we need those 13 days as a baseline for calculating momentum. For the first 13 periods of data, the RSI is calulated as followed:\
```math
\text{RSI~stepone} = 100 - \left( \frac{100}{1 + \frac{\text{Average Gain}}{\text{Average Loss}}} \right)
```
Once 14 periods of data are available, the second part of the RSI calculation can be done with the following equation:\
```math
\text{RSI~steptwo} = 100 - \left[ \frac{100}{1 + \left( \frac{(\text{PrevAvgGain} \times 13 + \text{CurrentGain})}{(\text{PrevAvgLoss} \times 13 + \text{CurrentLoss})} \right)} \right]
```
When the RSI indicator is close to 0, it means the momentum for the price movement of the stock is weak. When the RSI indicator is close to 100, it means the momentum for the price movement of the stock is strong\.
<br>
**Stochastic Oscillator** compares a closing price of a stock with a range of its prices over a certain period of time[^2].



[^1]:  [https://www.investopedia.com/terms/r/rsi.asp](https://www.investopedia.com/terms/r/rsi.asp)
[^2]:  [https://www.investopedia.com/terms/s/stochasticoscillator.asp](https://www.investopedia.com/terms/s/stochasticoscillator.asp)
