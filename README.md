# Analysis of [The Random Forest Model for Analyzing and Forecasting the US Stock Market in the Context of Smart Finance](https://arxiv.org/pdf/2402.17194)
## Introduction
The goal of this project was to analyze this paper and recreate the outcomes to verify it's validity, since the quality of the paper lacks sources, methods, and other important standards in academic writing/.
The goal of the algorithm in the paper was to determine how a stock would change in price categorically, either increasing or decreasing, based on known prior data. It looks at windows of 30, 60, and 90 days to determine whether t he target day of that stock increases or decreases\. 
## Filling in the Blanks
The algorithm used six financial indicators of stocks to classify stock criteria, which were not explained so I will first explain the indicators used\.
**RSI** is the relative strength index, which measures the speed and magnitude of a stock's recent price changes[^1]. The RSI is an oscillator that takes on values from 0 to 100. The calculation of RSI has two parts. Since RSI is a momentum oscillator, the first 13 values are not defined, since we need those 13 days as a baseline for calculating momentum. For the first 13 periods of data, the RSI is calulated as followed:\
```math
\text{RSI}_1 = 100 - \left( \frac{100}{1 + \frac{\text{Average Gain}}{\text{Average Loss}}} \right)
```
Once 14 periods of data are available, the second part of the RSI calculation can be done with the following equation:\
```math
\text{RSI}_2 = 100 - \left[ \frac{100}{1 + \left( \frac{(\text{PrevAvgGain} \times 13 + \text{CurrentGain})}{(\text{PrevAvgLoss} \times 13 + \text{CurrentLoss})} \right)} \right]
```
When the RSI indicator is close to 0, it means the momentum for the price movement of the stock is weak. When the RSI indicator is close to 100, it means the momentum for the price movement of the stock is strong\.
<br>
\
**Stochastic Oscillator** compares a closing price of a stock with a range of its prices over a certain period of time[^2]. The formula for the stochastic oscillator is below, where C is the most recent closing price, L14 is the lowest price traded of the 14 previous trading sessions, H14 is the highest price traded during the same 14 day session. The range of the stochastic oscillator is between 0 and 100\.
```math
\%K = \frac{\text{Current Close} - \text{Lowest Low}}{\text{Highest High} - \text{Lowest Low}} \times 100
```
When the Stochastic Oscillator is close to 0, it means the latest closing price is close to the lowest price in the 14 day range. When the Stochastic Oscillator is close to 100, it means the latest closing price is close to the highest price in the 14 day range\.
<br>
\
**Williams %R** indicator identifies if a stock is overbought or oversold[^3]. This indicator is used similarly to the stochastic oscillator. The range of the Williams %R is from -100 to 0. The overbought range is from -20 to 0, and oversold range from -80 to -100.
The equation for calculating the Williams %R is as follows:\
```math
\text{Williams \%R} = \frac{\text{Highest High} - \text{Current Close}}{\text{Highest High} - \text{Lowest Low}} \times (-100)
```
<br>
\
**MACD** is the moving average convergence divergence indicator. This indicator shows the relationship between two exponential moving averages (EMA) of a stock’s price[^4]. The calculation for MACD uses the EMA formula:\

```math
\text{EMA}_t = \left( \text{Price}_t \times \frac{2}{N + 1} \right) + \left( \text{EMA}_{t-1} \times \left( 1 - \frac{2}{N + 1} \right) \right)
```
The MACD is calculated using EMA of 12 periods and 26 periods as denoted below:\
```math
\text{MACD} = \text{EMA}_{12} - \text{EMA}_{26}
```
There is no bound for the range of the MACD since it is based on the stock price and not normalized\.
<br>
\
**PROC** is the price rate of change indicator. This indication measures the percentage change in price between the current price and the price a certain number of periods ago[^5]. In the following equation, t is the time, and n is the number of days ago which we are measuring in respect to\.
```math
\text{ROC}_t = \left( \frac{\text{Price}_t - \text{Price}_{t - n}}{\text{Price}_{t - n}} \right) \times 100
```
The range of PROC is from -100% to ∞%. When the PROC is 0, it means there is no change in price. When the PROC is negative, price has decreased. When the PROC is positive, price has increased. There is no limit to how much price can increase\.
<br>
\
**OBV** is the on-balance volume indicator, which uses volume flow to predict changes in stock price[^6]. The formula for the OBV is below, where volume is the last trading volume amount\.
```math
\text{OBV} = \text{OBV}_{\text{prev}} + 
\begin{cases}
+\text{Volume}, & \text{if } \text{close} > \text{close}_{\text{prev}} \\
0, & \text{if } \text{close} = \text{close}_{\text{prev}} \\
-\text{Volume}, & \text{if } \text{close} < \text{close}_{\text{prev}}
\end{cases}
```
There is also no set range for OBV since it is a cumulative total of trading volume and is not normalized\.

[^1]:  [https://www.investopedia.com/terms/r/rsi.asp](https://www.investopedia.com/terms/r/rsi.asp)
[^2]:  [https://www.investopedia.com/terms/s/stochasticoscillator.asp](https://www.investopedia.com/terms/s/stochasticoscillator.asp)
[^3]:  [https://www.investopedia.com/terms/w/williamsr.asp](https://www.investopedia.com/terms/w/williamsr.asp)
[^4]:  [https://www.investopedia.com/terms/m/macd.asp](https://www.investopedia.com/terms/m/macd.asp)
[^5]:  [https://www.investopedia.com/terms/p/pricerateofchange.asp](https://www.investopedia.com/terms/p/pricerateofchange.asp)
[^6]:  [https://www.investopedia.com/terms/o/onbalancevolume.asp](https://www.investopedia.com/terms/o/onbalancevolume.asp)
