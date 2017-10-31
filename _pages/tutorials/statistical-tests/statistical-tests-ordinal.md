---
layout: page
title: Statistical tests for ordinal variables
comments: true
permalink: /statistical-tests-ordinal/
---

This tutorial is the third in a series of four. This third part shows you how to apply and interpret the tests for ordinal and interval variables. [This link](/statistical-tests/) will get you back to the first part of the series.

* An **ordinal** variable contains values that can be ordered like ranks and scores. You can say that one value higher than the other, but you can't say one value is 2 times more important. An example of this is army ranks: a General is higher in rank than a Major, but you can't say a General outranks a Major 2 times.
* A **interval** variable has values that are ordered, and the difference between has meaning, but there is no absolute 0 point, which makes it difficult to interpret differences in terms of absolute magnitude. Money value is an example of an interval level variable, which may sound a bit counterintuitive. Money can take negative values, which makes it impossible to say that someone with 1 million Euros on his bank account has twice as much money as someone that has 1 million Euros in debt. The [Big Mac Index](https://en.wikipedia.org/wiki/Big_Mac_Index) is another case in point.

## Descriptive

Sometimes you just want to describe one variable. Although these types of descriptions don't need statistical tests, I'll describe them here since they should be a part of interpreting the statistical test results. Statistical tests say whether they change, but descriptions on distibutions tell you in what direction they change.

### Ordinal median

The median, the value or quantity lying at the midpoint of a frequency distribution, is the appropriate  central tendency measure for ordinal variables. Ordinal variables are implemented in R as factor ordered variables. Strangely enough the standard R function _median_ doesn't support ordered factor variables, so here's a function that you can use to create this:
```r
median_ordinal <- function(x) {
  d <- table(x)
  cfd <- cumsum(d / sum(d))
  idx <- min(which(cfd >= .5))
  return(levels(x)[idx])
}
```
Which you can use on the [diamond](http://ggplot2.tidyverse.org/reference/diamonds.html) dataset from the **ggplot2** library on it's cut variable, which has the following distribution: 

| cut |n()  | prop | cum_prop |
| --- | --: | ---: | ------:  |
| Fair | 1.610 | 3% | 3% |
| Good| 4.906 | 9% | 12% |
| Very Good | 12.082 | 22% | 34% |
| Premium | 13.791 | 26% | 60% |
| Ideal | 21.551| 40% | 100% |

With this distribution I'd expect the **Premium** cut would be the median. Our function call,
```r
median_ordinal(diamonds$cut)
```
shows extactly that:
```
[1] "Premium"
```

### Ordinal interquartile range

The [InterQuartile Range](https://en.wikipedia.org/wiki/Interquartile_range) (IQR) is implemented in the _IQR_ function from base R, like it's _median_ counterpart, does not work with ordered factor variables. So again, we're out on our own again to create a function for this:
```r
IQR_ordinal <- function(x) {
  d <- table(x)
  cfd <- cumsum(d / sum(d))
  idx <- c(min(which(cfd >= .25)), min(which(cfd >= .75)))
  return(levels(x)[idx])
}
```
This returns, as expected:
```
[1] "Very Good" "Ideal" 
```

## One sample

One sample tests are done when you want to find out whether your measurements differ from some kind of theorethical distribution. For example: you might want to find out whether you have a dice that doesn't get the random result you'd expect from a dice. In this case you'd expect that the dice would throw 1 to 6 about 1/6th of the time.

### Wilcoxon one sample test

<img src="/_pages/tutorials/statistical-tests/diamonds-are-forever.jpg" width="136" height="180" align="right"/>

With the Wilcoxon one sample test, you test whether your ordinal data fits an hypothetical distribution you’d expect. In this example we'll examine the _diamonds_ data set included in the **ggplot2** library. We'll test a hypothesis that the diamond cut quality is centered around the middle value of "Very Good" (our null hypothesis). First let's see the total number of factor levels of the cut quality:
```r
library(ggplot2)
levels(diamonds$cut)
```
Output:
```
[1] "Fair"      "Good"      "Very Good" "Premium"   "Ideal"
```
The r function for the Wilcoxon one-sample test doesn't take non-numeric variables. So we have to convert the vector cut to numeric, and 'translate' our null hypothesis to numeric as well. As we can derive from the _level_ function's output the value "Very Good" corresponds to the number 3. We'll pass this to the _wilcox.test_ function like this:
```r
wilcox.test(as.numeric(diamonds$cut), 
            mu=3,
            conf.int=TRUE ) 
```	 
Output:
```
	Wilcoxon signed rank test with continuity correction

data:  as.numeric(diamonds$cut)
V = 781450000, p-value < 2.2e-16
alternative hypothesis: true location is not equal to 3
95 percent confidence interval:
 4.499983 4.499995
sample estimates:
(pseudo)median 
      4.499967
```
We can see that our null hypothesis doesn't hold. The diamond cut quality doesn't center around "Very Good". Somewhat non-sensical we also passed TRUE to the argument _conf.int_, to the function, but this also gave the pseudo median, so we are able to interpret what the cut quality does center around: "Premium" (the level counterpart of the numeric value 4.499967).

## Two unrelated samples

Two unrelated sample tests can be used for analysing marketing tests; you apply some kind of marketing voodoo to two different groups of prospects/customers and you want to know which method was best. 

### Mann-Whitney U test

<img src="/_pages/tutorials/statistical-tests/u-turn.png" width="160" height="160" align="right"/>

The [Mann–Whitney U test](https://en.wikipedia.org/wiki/Mann%E2%80%93Whitney_U_test) can be used to test whether two sets of unrelated samples are equally distributed. Let's take our trusted **mtcars** data set: we can test whether automatic and manual transmission cars differ in gas mileage. For this we use the _wilcox.test_ function:
```r
wilcox.test(mpg ~ am, data=mtcars)
```
Output:
```
        Wilcoxon rank sum test with continuity correction 
 
data:  mpg by am 
W = 42, p-value = 0.001871 
alternative hypothesis: true location shift is not equal to 0 
```
Since the p-value is well below 0.05, we can assume the cars differ in gas milage between automatic and manual transmission cars. This plot shows us the differences:
```r
mtcars %>% 
  mutate(am = ifelse(am == 1, "Automatic", "Manual")) %>% 
  ggplot() +
    geom_density(aes(x = mpg, fill = am), alpha = 0.8)
```
{:refdef: style="text-align: center;"}
<img src="/_pages/tutorials/statistical-tests/cars-mpg-am.png" alt="Image text" width="491" height="450" align="middle"/>
{: refdef}
The plot tells us, since the null hypothesis doesn't hold, it is likely the manual transmission cars have lower gas milage.

### Kolmogorov-Smirnov test

<img src="/_pages/tutorials/statistical-tests/smirnov.png" width="49" height="180" align="right"/>

The [Kolmogorov-Smirnov test](https://en.wikipedia.org/wiki/Kolmogorov%E2%80%93Smirnov_test) tests the same thing as the Mann-Whitney U test, but has a much cooler name; the only reason I included this test here. And I don't like name dropping, but drop this name in certain circles: it will result in snickers of approval for obvious reasons. The _ks.test_ function call isn't as elegant as the _wilcox.test_ function call, but still does the job:
```r
automatic <- (mtcars %>% filter(am == 1))$mpg
manual <- (mtcars %>% filter(am == 0))$mpg
ks.test(automatic, manual)
```
Output:
```
	Two-sample Kolmogorov-Smirnov test

data:  automatic and manual
D = 0.63563, p-value = 0.003911
alternative hypothesis: two-sided
```
Not surprisingly, automatic and manual transmission cars aren't equal in gas milage, but the p-value is higher than the Mann-Whitney U test told us. This tells us Kolmogorov-Smirnov is a tad more conservative than it's Mann-Whitney U counterpart. 

## Two related samples

Related samples tests are used to determine whether there are differences before and after some kind of treatment. It is also useful when seeing when verifying the predictions of machine learning algorithms.

### Wilcoxon Signed-Rank Test
 
The [Wilcoxon Signed-Rank Test](https://en.wikipedia.org/wiki/Wilcoxon_signed-rank_test) is used to see whether observations changed direction on two sets of ordinal variables. It's usefull, for example, when comparing results of questionaires with ordered scales for the same person across a period of time.

## Association between 2 variables

Tests of association determine what the strength of the movement between variables is. It can be used if you want to know if there is any relation between the customer's amount spent, and the number of orders the customer already placed. 

### Spearman Rank Correlation

<img src="/_pages/tutorials/statistical-tests/spearman.png" width="135" height="180" align="right"/>

The [Spearman Rank Correlation](https://en.wikipedia.org/wiki/Spearman%27s_rank_correlation_coefficient) is a test of association for ordinal or interval variables. In this example we'll see if vocabulary is related to education. The function _cor.test_ can be used to see if they are related:
```r
library(car)

cor.test( ~ education + vocabulary, 
          data=Vocab,
          method = "spearman",
          continuity = FALSE,
          conf.level = 0.95)
```
Output:
```
	Spearman's rank correlation rho

data:  education and vocabulary
S = 8.5074e+11, p-value < 2.2e-16
alternative hypothesis: true rho is not equal to 0
sample estimates:
      rho 
0.4961558 
```
It looks like there is a relation between education and vocubulary. By looking at the plot, you can see the relation between education and vocabulary is a quite positive one.
```r
ggplot(Vocab, aes(x = education, y = vocabulary)) +
  geom_jitter(alpha = 0.1) +
  stat_ellipse(geom = "polygon", alpha = 0.4, size = 1)
```
{:refdef: style="text-align: center;"}
<img src="/_pages/tutorials/statistical-tests/spearman-rank.png" alt="Image text" width="444" height="450" align="middle"/>
{: refdef}

Association measures can be useful more than one variable at a time. For example you might want to consider a range of variables from your data set for inclusion in your predictive model. Luckily the **[corrplot](https://cran.r-project.org/web/packages/corrplot/vignettes/corrplot-intro.html)** library contains the _corrplot_function_ to quickly visualise the relations between all in a dataset. Let's take the mtcars variables as an example:
```r
library(corrplot)
mat_corr <- cor(mtcars, 
                method = "spearman", 
                use = "pairwise.complete.obs")

p_values <- cor.mtest(mtcars, 
                      method = "spearman", 
                      use = "pairwise.complete.obs")
corrplot(mat_corr, 
         order = "AOE", 
         type = "lower", 
         cl.pos = "b", 
         p.mat = p.values$p, 
         sig.level = .05, 
         insig = "blank")
```
The matrix _mat_corr_ contains all Spearman rank correlation values, which are calculated with the _cor_ function, passing the whole data frame of _mtcars_, using the _method_ spearman and only include the pairwise observations. Then the p-values of each of the correlations are calculated using *corrplot**'s _cor.mtest_ function, with the same function arguments. Both are used in the _corrplot_ function, where the _order_, _type_ and _cl.post_ arguments specify some layout, which I won't go in further. The argument _p.mat_ is the p-value matrix, which are used by the _sig.level_ and _insig_ arguments to leave out those correlations which are considered insigificant below the 0.05 threshold. 
{:refdef: style="text-align: center;"}
<img src="/_pages/tutorials/statistical-tests/corrplot-spearman-rank.png" alt="Image text" width="451" height="450" align="middle"/>
{: refdef}
This plot shows which values are positively correlated by the blue dots, while the negative associations are indicated by red dots. The size of the dots and the intensity of the colour show how strong that association is. The cells without dot, don't have significant correlations. For the number of cylinders (_cyl_) are strongly negatively correlated with the milage per gallon, while the horsepower (_hp_) is positively associated with the number of carburetors (_carb_) albeit not as strong.

When you make a correlation martrix like this, be on the lookout for [spurious correlations](http://www.tylervigen.com/spurious-correlations). Putting in variables into your model indiscriminately, without reasoning, may lead to some unintended results...
