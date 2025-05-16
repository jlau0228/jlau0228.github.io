# Analysis of [The Random Forest Model for Analyzing and Forecasting the US Stock Market in the Context of Smart Finance](https://arxiv.org/pdf/2402.17194)
## Introduction
The goal of this project was to analyze this paper and recreate the outcomes to verify it's validity, since the quality of the paper lacks sources, methods, and other important standards in academic writing/.
The goal of the algorithm in the paper was to determine how a stock would change in price categorically, either increasing or decreasing, based on known prior data. It looks at windows of 30, 60, and 90 days to determine whether t he target day of that stock increases or decreases\. 
## Filling in the Blanks
The algorithm used six financial indicators of stocks to classify stock criteria, which were not explained so I will first explain the indicators used\.
<br>
\
\
**RSI** is the relative strength index, which measures the speed and magnitude of a stock's recent price changes[^1]. The RSI is an oscillator that takes on values from 0 to 100. The calculation of RSI has two parts. Since RSI is a momentum oscillator, the first 13 values are not defined, since we need those 13 days as a baseline for calculating momentum. For the first 13 periods of data, the RSI is calulated as followed:
```math
\text{RSI}_1 = 100 - \left( \frac{100}{1 + \frac{\text{Average Gain}}{\text{Average Loss}}} \right)
```
Once 14 periods of data are available, the second part of the RSI calculation can be done with the following equation:
```math
\text{RSI}_2 = 100 - \left[ \frac{100}{1 + \left( \frac{(\text{PrevAvgGain} \times 13 + \text{CurrentGain})}{(\text{PrevAvgLoss} \times 13 + \text{CurrentLoss})} \right)} \right]
```
When the RSI indicator is close to 0, it means the momentum for the price movement of the stock is weak. When the RSI indicator is close to 100, it means the momentum for the price movement of the stock is strong\.
<br>
\
\
**Stochastic Oscillator** compares a closing price of a stock with a range of its prices over a certain period of time[^2]. The formula for the stochastic oscillator is below, where C is the most recent closing price, L14 is the lowest price traded of the 14 previous trading sessions, H14 is the highest price traded during the same 14 day session. The range of the stochastic oscillator is between 0 and 100\.
```math
\%K = \frac{\text{Current Close} - \text{Lowest Low}}{\text{Highest High} - \text{Lowest Low}} \times 100
```
When the Stochastic Oscillator is close to 0, it means the latest closing price is close to the lowest price in the 14 day range. When the Stochastic Oscillator is close to 100, it means the latest closing price is close to the highest price in the 14 day range\.
<br>
\
\
**Williams %R** indicator identifies if a stock is overbought or oversold[^3]. This indicator is used similarly to the stochastic oscillator. The range of the Williams %R is from -100 to 0. The overbought range is from -20 to 0, and oversold range from -80 to -100.
The equation for calculating the Williams %R is as follows:
```math
\text{Williams \%R} = \frac{\text{Highest High} - \text{Current Close}}{\text{Highest High} - \text{Lowest Low}} \times (-100)
```
<br>
\
\
**MACD** is the moving average convergence divergence indicator. This indicator shows the relationship between two exponential moving averages (EMA) of a stock’s price[^4]. The calculation for MACD uses the EMA formula:

```math
\text{EMA}_t = \left( \text{Price}_t \times \frac{2}{N + 1} \right) + \left( \text{EMA}_{t-1} \times \left( 1 - \frac{2}{N + 1} \right) \right)
```
The MACD is calculated using EMA of 12 periods and 26 periods as denoted below:
```math
\text{MACD} = \text{EMA}_{12} - \text{EMA}_{26}
```
There is no bound for the range of the MACD since it is based on the stock price and not normalized\.
<br>
\
\
**PROC** is the price rate of change indicator. This indication measures the percentage change in price between the current price and the price a certain number of periods ago[^5]. In the following equation, t is the time, and n is the number of days ago which we are measuring in respect to\.
```math
\text{ROC}_t = \left( \frac{\text{Price}_t - \text{Price}_{t - n}}{\text{Price}_{t - n}} \right) \times 100
```
The range of PROC is from -100% to ∞%. When the PROC is 0, it means there is no change in price. When the PROC is negative, price has decreased. When the PROC is positive, price has increased. There is no limit to how much price can increase\.
<br>
\
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
## Approach
Since the paper mainly uses random forest, I am going to focus on using random forest as a method to verify the results of the paper. Random forest is a machine learning algorithm that combines the output of multiple decision trees to reach a single result. This result is an average of the decision trees, which helps with combatting overfitting.  
To validate the model, I will use OOB(out-of-bag) error rate on the random forest model. The paper also uses OOB as a method of evaluating the model. OOB is calculated by averaging the total error of the forest with n trees, which is the total number of misclassified points over the amount of data.

Cross validation is also a statistical method to evaluate a model’s performance on new data. It is typically done by dividing the training, or available data, into subsets, and using one of the subsets a validation set, and training the model on the rest of the subsets. This process is repeated and the results are averaged in some way to have a better estimate of the model’s performance. This method, however, does not work well with time series because if the validation set has an earlier window than the testing subsets, it would no longer be a predictive model of the future. It doesn’t make sense to use future data to forecast the past. To combat this and preserve the past-future relationship, there is a modified version of cross validation. When splitting data into subsets, the validation subset should be the most recent one amongst the subsets. To cross validate throughout the data, different markers for where the validation subset will end will be set.  
Although OOB and cross validation are two different methods, OOB makes more sense to use in this case, since I, and the paper, used bootstrapping, and the sklearn random forest classifier has a built-in OOB score function.

The goal and method of this paper is to use a random forest with different parameters of trading window periods and number of estimators to determine the best model for predicting if a stock rises or falls. The paper also shows a plot of the accuracy of using SVM, logistic regression, gaussian discriminant analysis, but since there was no explanation of methodology, I did not try to recreate this part of the paper.
## Methods
I opted to only use sklearn and not xgboost, as OOB error is not a feature in xgboost, and I wanted to stick as closely to the paper as possible to recreate the results.

I was able to plot a two moons dataset and ran a decision tree classifier on it. I only plotted the points of the two moons and ran a decision tree classifier for the sake of the paper using two moons as an example of how decision trees and random forests work. They did not and could not have used the same model for stocks as they did on the two moons data set, as they are completely different types of data, one being an x,y coordinate, and the other having 6 extra features and being a time series.

![accuracyscore](https://github.com/jlau0228/jlau0228.github.io/blob/cc8dda1d6ee1ad989a08edd6eebeaea18f816b8a/stock%20pics/2moons%20accuracy%20score.PNG)
![classificationreport](https://github.com/jlau0228/jlau0228.github.io/blob/main/stock%20pics/two%20moons%20forest%20classifier.PNG)
![2moons](https://github.com/jlau0228/jlau0228.github.io/blob/main/stock%20pics/2moons.PNG)

For the stocks, I recreated the process using all available stock data up until December 18th, 2024. The results I got were similar to the results of the paper, so I did not try to recreate it with only 7000 data points. I used Apple, Samsung, and GE stock data, since those were the 3 stocks used in the paper. My steps were:

1. Import the closing price data of each stock using yahoo finance library.
2. Create a dataframe for each stock so I can add a column for each feature.
3. Define functions to smooth and calculate each feature: RSI, stochastic oscillator, Williams %R, EMA, MACD, PROC, OBV
   - I picked alpha = 0.2 as the smoothing factor because in practice, alpha is usually set between 0.1 and 0.3. Since the paper did not explicitly explain how they got their alpha or if they even ran any tests to find an optimal alpha, I would assume they also followed industry standards, therefore I did the same.
   - I plotted the features of each stock to make sure the feature functions have acceptable outputs
![appleindicators](https://github.com/jlau0228/jlau0228.github.io/blob/e32db4af93d924d14f7c9f3c68776a43c2264847/stock%20pics/appleindicators.png)
![samsungindicators](https://github.com/jlau0228/jlau0228.github.io/blob/e32db4af93d924d14f7c9f3c68776a43c2264847/stock%20pics/samsungindicators.png)
![geindicators](https://github.com/jlau0228/jlau0228.github.io/blob/e32db4af93d924d14f7c9f3c68776a43c2264847/stock%20pics/geindicators.png)
4. Defined a function that assigns 0 or 1 to a given data point if the window date after the given point is larger or smaller.
   - This function is repeated for windows of 30, 60, and 90. Each point is given a value based on a future point of the window. This way, the 30, 60, 90 columns will still hold information of its relative future and can combat issues with classifying time series.
5. To recreate the OOB error rate plot, for each 30, 60, 90 day window, I looped from 1 to 100 number of trees and got the OOB score for each random forest classifier. I fit the classifier to the data available.
   - This was only done with Apple since the figure in the paper for OOB error rate was also done with Apple stock. How I know they used Apple stock without specifying it in the paper will be revealed later on.
   - The results I got were very similar to the results in the paper.
![ooberror](https://github.com/jlau0228/jlau0228.github.io/blob/8b6289c51be72a14b4677b44281df93deaa4ba01/stock%20pics/ooberror.PNG)
6. To recreate the OOB error specific result, I put my OOB error data into a table and included the same day window, number of trees, and OOB error.
   - The OOB errors were very similar. They were probably not the same exact number because I have more recent stock data included in my model, as I do not know the exact dates they trained their model on.
![oobtable](https://github.com/jlau0228/jlau0228.github.io/blob/d3d350c518b11d04f260d6588ea9718a416c6aa5/stock%20pics/oob.PNG)
7. To recreate the ROC curves, I used the same model, but set the number of estimators to be 100, as it was shown from the previous step that 100 estimators had lower OOB error rate.
   - My graphs also had the result of a 90 day window having the highest AUC score.
![appleroc](https://github.com/jlau0228/jlau0228.github.io/blob/d008b38900a9d24dfd9fbef865a1b762d42ff541/stock%20pics/rocapple.png)
![samsungroc](https://github.com/jlau0228/jlau0228.github.io/blob/d008b38900a9d24dfd9fbef865a1b762d42ff541/stock%20pics/rocsamsung.png)
![geroc](https://github.com/jlau0228/jlau0228.github.io/blob/d008b38900a9d24dfd9fbef865a1b762d42ff541/stock%20pics/rocge.png)
## Analysis of the paper from an academic writing lens
As a professionally published paper, many parts of the paper are not up to professional standards. To start, the abstract of a paper is supposed to be a summary of the paper, which should highlight the paper’s methods, main points, and conclusions. Most of the abstract was explaining the context of the stock market and why predicting stock trends is important. Only the last two sentences refer to the paper, “This paper evaluates the predictive performance of random forest models combined with artificial intelligence on a test set of four stocks using optimal parameters. The evaluation considers both predictive accuracy and time efficiency.” (Zheng). These two sentences mentioned using random forest as a method, but did not specify which stocks or optimal parameters were used. The results of their research were also not stated, which is one of the important aspects of an abstract.

In terms of writing quality, there are typos throughout the paper, as well as a sentence being repeated three times,“In the analysis and prediction of stock data using machine learning, various algorithms are commonly used, which are based on machine learning methods. In the analysis and prediction of stock data using machine learning, various algorithms are commonly used, which are based on machine learning methods. In the analysis and prediction of stock data using machine learning, various algorithms are commonly used, which are based on machine learning methods.”

The paper labels the figures and tables, but only refers to one of the tables once throughout the paper. “Table 2 shows that OOB errors decrease gradually and converge, reaching a steady state when the number of decision trees exceeds 45.”(Zheng) The figures are not labeled with titles and specific data that the figures used were not mentioned.

Furthermore, there are no quotes or footnotes that reference outside sources. The paper mentions some terms without any explanation of what it means. “Technical indicators are important signals used to judge bear and bull in stock analysis.” Bear and bull markets were not explained, but were the reasoning behind the research, to judge if a stock goes up or down. Despite having a reference section, there isn’t evidence to back up non-research result claims in the paper. This indicates that the paper was not properly vetted or proof-read prior to publishing.
## Analysis of the paper from a machine learning and content lens
Firstly, in section 2.2, the paper explains correctly the concepts of random forest and bootstrapping with an example of the two moons dataset, but fails to make a valid reasoning for why it is relevant for using random forest as a method for predicting stocks. The two moons data set is not a time series, and has different features and number of features as the stock data. This section of the paper was irrelevant and did not contribute to the reasoning behind why random forest was used as a method.

The explanation for using EMA(exponential moving average) as a smoothing method made sense, as well as their reasoning for placing greater weight on more recent data (Section 3.1). 

The features that were used as indicators were RSI(relative strength index), Stochastic Oscillator, Williams %R, MACD(moving average convergence divergence), Price ROC(Rate of Change), and OBV(On-balance volume). This paper did not include a publishing date, but the latest publishing date in the references is December 12, 2023 for “Improvements and Challenges in StarCraft II Macro-Management: A Study on the MSC Dataset”. According to Investopedia, the top 7 best indicators for day trading are: OBV, Accumulation/distribution line, Average Directional Index, Aroon Oscillator, MACD, RSI, and Stochastic Oscillator(Investopedia, 2024). As this article was written in June 2024, the top indicators of the stock market would not have changed drastically in 6 months. The paper included Williams %R and Price ROC, which were not one of the top 7 indicators. Since the paper did not explain why each of these indicators were used, I would assume it was because the researchers believed these were the best indicators at the time they conducted the research. Williams %R and Price ROC are also a momentum indicator that technical analysts use, so it is reasonable these features were included in their model.

I agreed with the choice of using random forest for stock price prediction based on their explanation, even though they did not provide evidence behind their reasoning. “However, it was discovered that the stock trend prediction problem was not linearly divisible. This was due to the significant overlap of the convex hull when projected into two dimensional space. Therefore, algorithms related to linear discriminant analysis, such as SVM, were deemed inapplicable.”

In section 3.5, it was stated that the study, “compares the Random Forest algorithm with SVM, logistic regression, Gaussian discriminant analysis, quadratic discriminant analysis, and other models to determine its superiority.” Figure 5, shown in section 3.6, is a graph of the accuracy of SVM, logistic regression, Gaussian discriminant analysis, and quadratic discriminant analysis, but did not specify any parameters in which they modeled each method with. Furthermore, as mentioned before, SVM was deemed inapplicable, but they were able to apply it with the stock data, and SVM performed the best of the 4 shown methods, with accuracy ranging from 60% to 80%. 
## Graphs, Figures, Tables Analysis
Figure 2 - Did not specify which stock the OOB Error graph used for the data plotted, description of figure was not correct
Table 2 - Used different sample sizes for 30, 60, and 90 trading days, missing 65 number of trees for 90 day trading period.
Figure 3 - Did not specify which is Apple and Samsung
Figure 4 - Same exact figure as left plot of Figure 3


[^1]:  [https://www.investopedia.com/terms/r/rsi.asp](https://www.investopedia.com/terms/r/rsi.asp)
[^2]:  [https://www.investopedia.com/terms/s/stochasticoscillator.asp](https://www.investopedia.com/terms/s/stochasticoscillator.asp)
[^3]:  [https://www.investopedia.com/terms/w/williamsr.asp](https://www.investopedia.com/terms/w/williamsr.asp)
[^4]:  [https://www.investopedia.com/terms/m/macd.asp](https://www.investopedia.com/terms/m/macd.asp)
[^5]:  [https://www.investopedia.com/terms/p/pricerateofchange.asp](https://www.investopedia.com/terms/p/pricerateofchange.asp)
[^6]:  [https://www.investopedia.com/terms/o/onbalancevolume.asp](https://www.investopedia.com/terms/o/onbalancevolume.asp)
