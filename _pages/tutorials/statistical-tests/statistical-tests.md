---
layout: page
title: Statistical tests
comments: true
permalink: /statistical-tests/
---

This tutorial is one in a series of four. The goal of this tutorial series is to show you how to choose your test and how to apply and interpret them. This first part will tell you which one to choose, the other three parts are about applying and interpreting the tests for categorical, ordinal and Gaussian variables respectively.

| Goal | Categorical | Ordinal | Gaussian | 
| :--- | :-------- | :--------- | :------------------- |
| Descriptive | [Proportion](/statistical-tests-categorical/#proportion), [Mode](/statistical-tests-categorical/#mode) | [Median](/statistical-tests-ordinal/#ordinal-median), [Interquartile Range](/statistical-tests-ordinal/#ordinal-interquartile-range) | [Central tendency](/statistical-tests-gaussian/#central-tendency), [Distribution spread](/statistical-tests-gaussian/#distribution-spread) |
| 1 Sample | [Chi-square](/statistical-tests-categorical/#chi-square-goodness-of-fit-test), [Binominal test](/statistical-tests-categorical/#binominal-test) | [Wilcoxon one sample text](/statistical-tests-ordinal/#wilcoxon-one-sample-test) | [Normality](/statistical-tests-gaussian/#normality-check), [One sample t-test](/statistical-tests-gaussian/#one-sample-t-test) |
| 2 Unrelated samples | [Chi-square](/statistical-tests-categorical/#two-sample-chi-square-test) | [Mann-Whitney U test](/statistical-tests-ordinal/#mann-whitney-u-test), [Kolmogorov-Smirnov test](/statistical-tests-ordinal/#kolmogorov-smirnov-test) | [Unpaired t-test](/statistical-tests-gaussian/#unpaired-t-test) |
| 2 Related samples | [McNemar's test](/statistical-tests-categorical/#mcnemars-test) | [Wilcoxon Signed-Rank Test](/statistical-tests-ordinal/#wilcoxon-signed-rank-test) | [Paired t-test](/statistical-tests-gaussian/#paired-t-test) |
| Association 2 variables | [Contigency coefficients](/statistical-tests-categorical/#contigency-coefficients) | [Spearman rank correlation](/statistical-tests-ordinal/#spearman-rank-correlation) | [Pearson correlation](/statistical-tests-gaussian/#pearson-correlation) |

<img src="/_pages/tutorials/statistical-tests/absolutely-nothing.jpg" width="195" height="180" align="right"/>

Statistical test allow us to draw conclusions about the distribution of a population, comparisons between populations or relations between variables. Statistical testing is about testing whether the so called null hypothesis, which I sometimes refer to as the 'nothing to see here' conclusion, is true. General examples of null hypothesis statements are "there are no differences between groups", "this didn't show any effect" or "there is no relation between...". 

# Statistical tests results: p-values

<img src="/_pages/tutorials/statistical-tests/pea-value.jpeg" width="180" height="180" align="right"/>

The outcome of a statistical test can be read from the p-value. The p-value is very powerful, because it incorporates effect size, sample size, and variability of the data into a single number that objectively tells you how consistent your data are with the null hypothesis. Low p-values indicate strong evidence against the null hypothesis, so they lead you to reject the null hypothesis; generally a p-value 0.05 or less is taken as a significant deviation from the null hypothesis. 

There are many ways in which p-values are misinterpreted (see [this](http://blog.minitab.com/blog/adventures-in-statistics-2/how-to-correctly-interpret-p-values) blog for an in depth discussion about this). Do _not_ take the p value as a percentual chance you might be wrong in rejecting the null hypothesis, tempting you into thinking a p-value of 0.1 is fine too: a p-value of 0.1 is not equal to a 10% chance of being wrong! In the table below you can see just how wrong this interpretation is. You might even prefer to be more stringent in your p-value choice after seeing this... Just remember: p-values are about the likeliness that _your_ samples represent the null hypothesis (p-value > 0.05) or not (p-value < 0.05), it is not about how likely this result will hold in all other samples.

| p-value | Probability of incorrectly rejecting a true null hypothesis |
| ------: | ----------------------------------------------------------- |
| 0.05    | At least 23% (and typically close to 50%)                   |
| 0.01    | At least 7% (and typically close to 15%)                    |


# Choosing your statistical test

The exact test you use is determined by two things:

* The goal you're trying to reach a conclusion about
* The level of measurement of a variable.

## Types of conclusions

Sometimes you just want to describe one variable. Although these types of descriptions don't need statistical tests, I'll describe them here since they should be a part of interpreting the statistical test results. Statistical tests say whether they change, but descriptions on distibutions tell you in what direction they change.

One sample tests are done when you want to find out whether your measurements differ from some kind of theorethical distribution. For example: you might want to find out whether you have a dice that doesn't get the random result you'd expect from a dice. In this case you'd expect that the dice would throw 1 to 6 about 1/6th of the time.

Two sample tests come in two flavors: unrelated and related samples. Unrelated sample tests can be used for analysing marketing tests; you apply some kind of marketing voodoo to two different groups of prospects/customers and you want to know which method was best. The related samples tests are used to determine whether there are differences before and after some kind of treatment. It is also useful when seeing when verifying the predictions of machine learning algorithms.

Tests of association determine what the strength of the movement between variables is. It can be used if you want to know if there is any relation between the customer's amount spent, and the number of orders the customer already placed. 

## Levels of measurement

A variable can be categorized as one of the following levels of measurements, in order of increasing information value:

* A **categorical** variable values are just names, that indicate no ordering. An example is fruit: you've got apples and oranges, there is no order in these. A special case is a binominal is a variable that can only assume one of two values, true or false, heads or tails and the like. Churn and prospect/customer variables are more specific examples of binominal variables.
* An **ordinal** variable contains values that can be ordered like ranks and scores. You can say that one value higher than the other, but you can't say one value is 2 times more important. An example of this is army ranks: a General is higher in rank than a Major, but you can't say a General outranks a Major 2 times.
* A **interval** variable has values that are ordered, and the difference between has meaning, but there is no absolute 0 point, which makes it difficult to interpret differences in terms of absolute magnitude. Money value is an example of an interval level variable, which may sound a bit counterintuitive. Money can take negative values, which makes it impossible to say that someone with 1 million Euros on his bank account has twice as much money as someone that has 1 million Euros in debt. The [Big Mac Index](https://en.wikipedia.org/wiki/Big_Mac_Index) is another case in point.
* A **ratio** variable has values like interval variables, but here there is an absolute 0 point. 0 Kelvin is an example: there is no temperature below 0 Kelvin, which also means that the 700 degrees Kelvin is twice as hot as 350 degrees Kelvin. Quantities are the only example in my field that I know of. 

The level of measurement of the variable determines which type of test you can use. The main distinction between tests are the parametric versus nonparametric tests. Nonparametric tests are the tests that I've categorized here in the Categorical and Ordinal variables. Parametric tests can only be done from interval and ratio variables, but additionaly tests make assumptions about the defining properties of the variable's distribution of the population. Mostly they assume that the variable is normally distributed for the population (i.e. [Gaussian](https://en.wikipedia.org/wiki/Normal_distribution)). To check whether your variable is in this category you can use [this link](/statistical-tests-gaussian/#normality-check). In my experience in Marketing variables never follow a Gaussian distribution (please let me know if you have a examples in which it is the case). So for me, this rules out parametric tests, and whenever I think I have an interval or ratio level variable I still take the tests associated with ordinal variables. The downside to my approach is that the nonparametric tests are less powerful for detecting effects, so it makes my conclusions more conservative.

# Putting it all together

<img src="/_pages/tutorials/statistical-tests/pritt-stick.png" width="134" height="140" align="right"/>

The previous sections should have given you enough rope to find out what kind of test you need: by knowing what the type of conclusion is you want to reach, and found out which level measurement your variable is at, you can find out the test by making the correct crossing in the table below: 

| Goal | Categorical | Ordinal | Gaussian | 
| :--- | :-------- | :--------- | :------------------- |
| Descriptive | [Proportion](/statistical-tests-categorical/#proportion), [Mode](/statistical-tests-categorical/#mode) | [Median](/statistical-tests-ordinal/#ordinal-median), [Interquartile Range](/statistical-tests-ordinal/#ordinal-interquartile-range) | [Mean, SD](/statistical-tests-gaussian/#mean-sd) |
| 1 Sample | [Chi-square](/statistical-tests-categorical/#chi-square-goodness-of-fit-test), [Binominal test](/statistical-tests-categorical/#binominal-test) | [Wilcoxon one sample text](/statistical-tests-ordinal/#wilcoxon-one-sample-test) | [One sample t-test](/statistical-tests-gaussian/#one-sample-t-test) |
| 2 Unrelated samples | [Chi-square](/statistical-tests-categorical/#two-sample-chi-square-test) | [Mann-Whitney U test](/statistical-tests-ordinal/#mann-whitney-u-test), [Kolmogorov-Smirnov test](/statistical-tests-ordinal/#kolmogorov-smirnov-test) | [Unpaired t-test](/statistical-tests-gaussian/#unpaired-t-test) |
| 2 Related samples | [McNemar's test](/statistical-tests-categorical/#mcnemars-test) | [Wilcoxon Signed-Rank Test](/statistical-tests-ordinal/#wilcoxon-signed-rank-test) | [Paired t-test](/statistical-tests-gaussian/#paired-t-test) |
| Association 2 variables | [Contigency coefficients](/statistical-tests-categorical/#contigency-coefficients) | [Spearman correlation](/statistical-tests-ordinal/#spearman-correlation) | [Pearson correlation](/statistical-tests-gaussian/#pearson-correlation) |

All descriptive methods and statistical tests associated with the lower information value variables can be applied to the higher information variables. So you can calculate a mode, which I've here associated with categorical variables, for ordinal and Gaussian variables as well. Although you can do the same for statistical tests, you should prefer the test associated with the variable level if you can: these tests increase the chances of finding the smallest p-value, since they include most of the variable's information value.
