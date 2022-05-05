---
title:  "Pair Trading Strategies Using R"
date:   2021-11-01 22:07:07
categories: [Projects]
tags: [Projects]
---
First of all, I would like to talk about the reason why I choose this topic. In the assignment 7, we went through a simple example of beta hedging. The problem mentioned the market neutral strategies and really gave me some ideas for a possible project.

In the CAPM theory, we have this formula, in which the variable beta indicates the correlation of the portfolio and the market. Then by combining more than one assets in the portfolio, we can in some degree get rid of the parameter beta and therefore turn our portfolio into market neutral. In this case, it doesn’t matter whether the market goes up or down, the return of our portfolio stays untouchable. Therefore, if we vanish the β, according to equation 3.2, the return is based only on the residuals term. Since the expected value of the residuals is zero, we expect a mean reverting behavior which makes it easier for the investor to apply a strategy rule on this, creating trading signals and trying to maximize the possibilities of making profit. Such portfolios are referred to the literature as market neutral portfolios.

Pair trading is one of the most common market neutral strategy and I am going to implement it using R.

The statistics/econometric knowledge from the class I used in this project includes time series which involve the price of stocks, CAPM model which the strategy derives from and Risk management, including the calculation of value at risk, when evaluating the performance of the strategies.

Then we come into the part of data and methodology.

In this project, I try to find pairs from 13 stocks in the banking industry in China which are listed in the right.

I set the time interval as January 1st in 2018 to the last day of 2019 to avoid the influence of covid-19. Then I define a function of downloading prices from yahoo finance and use it to download the prices of the 13 stocks and interpolate the missing values. We can use the head function to view the data.

Also, I divide the data into training set and test set. The training set is used to estimate the parameters and the test set is used to evaluate the performance of our strategy.

First of all, I use the cointegration approach to discover stock pairs.

Cointegration is a statistical property of a collection (X1, X2, ..., Xk) of time series variables. all of the series must be integrated of order d.

And if a linear combination of this collection is integrated of order less than d, then the collection is said to be co-integrated.

We try to find a stock pair which is co-integrated. In other words, the prices of the stocks satisfy this equation, where w is the residual and is mean-reverting. Then we can construct a portfolio of the first stock minus gamma times the second stock. If we use small p to denote the log price, then the return can be expressed like this. As I mentioned, w is mean-reverting and we can construct signals based on that.

The first task is to find out which pair is co-integrated. In R, we have a package called egcm, and it uses Engle-Granger cointegration test and shows the result of different unit root tests of the residual. By using the function “is-cointegrated” of the log prices, I discovered 15 co-integrated pairs among the altogether 78 pairs.

We can take a close look at the test, if the p-value of the unit root test of the residual is less than 0.05, then we can say that the pairs are co-integrated. In this example, the p-value is bigger than 0.05 so 600000.SS and 600015.SS

do not appear to be cointegrated. And in this example, 601398.SS and 600000.SS appear to be cointegrated

After this, I used this function to see the p-values of ADF test. I found out that there are four pairs which had p-value smaller than 0.01 and I decided to choose the pair 600015.SS and 601169.SS as an example, which corresponded to Hua Xia Bank Co., Limited and Bank of Beijing Co., Ltd.

I plot the log prices of these two stocks and we can easily notice their similar trends from the plot.

Then I apply a least squares regression on the two log prices in the training set and got the parameters. I plot the result of the regression and notice that it fits quite well not only in the training period which is on the left of the blue line, but also in the test set which is on the right of the blue line.

After that, I calculate and plot the spread, which equals to y1 mimus y2 multiplied by b, and in order to have a portfolio whose sum of weights equal to 1, I try to normalize the portfolio by dividing the original weights by (1+b). We can see that the plots of the spread before and after normalization is similar, except for the y coordinate.

In the next step, I calculate the z-score of the spread, which equals to spread minus the mean and then divide it by the standard deviation. I set the threshold as 0.7.

Based on this, I can define the function to generate the signals.

As to the Initial position

If z-score <= -0.7, then we take the long position

If z-score >= 0.7, then we take the short position

After that,

If we are not in a position,

Similar with above

If we are in a long position,

If z-score >= 0,  close the position

If we are in a short position,

If z-score <= 0, close the position

Then I use the function to generate the signals and the red line indicates the signals.

We can calculate the returns and signals. Similarly, on the left of the blue line is the training set and on the right of the blue line is the test set.

Also, we can use the PerformanceAnalytics package to plot the drawdown and calculate some important backtest indicators like standard deviation, value at risk and Sharpe ratio. By the way, the returns used in the package is the test set. We can see that this strategy has a backtest Sharpe ratio of 1.247878.

Back to the original idea of constructing pairs, we try to see its correlation with CAPM. Here, 000001.SS represents the SSE Composite Index, the return of which can in some degree indicate the market return.

The pair we construct has the spread_return and the ß is 0.065.

While each separate stock in the pair has ß which equals to 0.83 and 0.66, much higher than the portfolio.

The result corresponds to our goal of achieving small ß to remove the market risk exposure.

In this slide, I also provide the plots of the returns of other three possible pairs. The procedure is similar and is omitted here.

Another popular approach is based on distance. In this part, I define a function SSD to calculate the distance of the normalized prices.

By calculating the distances, I chose the pair with the least distance, which is coincidentally the pair "600015.SS”"601169.SS". Also, I design the signals based on the spread, as is shown in this slide. Then, in the part of evaluation, we can see that the backtest Sharpe ratio of 1.545734 and the value at risk is -0.0052.

As to the conclusion, By choosing pairs from 13 stocks in the bank industry in China and using the data in 2018 and 2019, we successfully discovered stock pairs and  implemented the strategies of pair trading. The strategies from both the co-integration approach and the minimum distance approach are shown to be profitable.

In practical application, the situation might be more complex, considering the transaction costs and limitations on trading. Therefore, we need more trials to determine some parameters in our strategy like the choice of n when determining the thresholds.

There are also some new research in the construction of pair trading strategy. For example, some researchers pay attention to the Kalman filter. Some researchers try to combine the GARCH model. And also, the application of machine learning methods in market neutral strategies has been more and more popular.
