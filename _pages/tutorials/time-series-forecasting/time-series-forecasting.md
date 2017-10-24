---
layout: page
title: Time series forecasting
comments: true
permalink: /time-series-forecasting/
---

Time series forecasting is the use of a model to predict future values based on previously observed values.

Differ



# Time series characteristics

Time series can have an upward or downward trend over time.

We call a time series stationary when it has stationary behavior around a mean and the variability (correlation structure) remains constant over time. 

A time series that is nonstationary can have different variability (aka heteroskedasticity) at different time intervals.

Autocorrelation is the correlation constant between two points in time having the same time distance.

random walk because at time tt the process is where it was at time t−1 plus a completely random movement. For a random walk with drift, a constant is added to the model and will cause the random walk to drift in the direction (positve or negative) of the drift.

# Diffent time series models

MA or Moving Average models

ARMA models: stationary time series can be seen as a linear combination of white noise

# Removing trends

By differencing can remove the trend in a time series. Differencing looks at the difference between the value of a time series at a certain point in time and its preceding value. That is, Xt−Xt−1 is computed. You can 

# Removing nonstationary

We can transform a nonstationary time series to a stationary time series

# Simulating models

ARMA

```r
arma.sim(model

p AR
d
q MA
```

White noise
```r
arima.sim(model = list(order = c(0, 0, 0) ), n = 100)
```

# Identifying time series models

ACF Auto Correlation Functions
PACF Partial Auto Correlation Functions

|      | AR(p)     | MA(q)          | ARMA(p, q) |
| ---- |    ----   |      ----      |    ----    |
| ACF  | Tails off | Cuts off lag q | Tails off  |
| PACF | Cuts off lag p | Tails off | Tails off  |

The p and q indicate what is called the order of the models. The order of the models determine how many parameters you add to the model. For the AR and MA models it is clear what order model to use by the lag cut offs. With ARMA models however, the choice is not clear-cut. The only way to estimate these is by varying the orders, keeping in mind to start with lower p and q, since simpeler models are preferred over more complex models. 



# Estimating model parameters

Library **astsa**
```r
sarima
```

