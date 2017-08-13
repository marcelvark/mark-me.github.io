---
layout: page
title: Data transformation
comments: true
permalink: /data-transformation/
---

* TOC
{:toc}

# The trouble with currency

Since I live on the European mainland, I often get currency data delivered that doesn't comply to the English/US standard. Decimal separators are commas instead of points and big number separators are decimals. If you want to turn these currencies into the R/US/English compliant versions you can use this code.

```r
tbl_revenue %<>% mutate(amt_revenue = gsub("[.]", "", amt_revenue)) %>% # Removing thousand separators (.) from value
  mutate(amt_revenue = gsub("[,]", ".", amt_revenue)) %>% # Replacing decimal separator (,) with . from value
  mutate(amt_revenue = as.numeric(amt_revenue))
```

# Joining tables

Joining tables is most commonly done using the **dplyr** library. Joins of the dplyr library are more comprehensive than in SQL. Joins from dplyr transforms data in a way that SQL would take care of by using _IN_ or _NOT IN_ statements in the _WHERE_ clause.

## Join types

*   _inner_join(table_x, table_y)_ - Same as SQL, returns all rows from x where there are matching values in y, and all columns from x and y. If there are multiple matches between x and y, all combination of the matches are returned.
*   _left_join(table_x, table_y)_ - Same as SQL, returns all rows from x, and all columns from x and y. If there are multiple matches between x and y, all combination of the matches are returned.
*   There is a _right_join(table_x, table_y)_ function, but you are definitely loosing your mind when you want to use that. You want to use that? You're on your own.... Can't help you.
*   _full_join(table_x, table_y)_ - Same as SQL, returns all rows and all columns from both x and y. Where there are not matching values, returns NA for the one missing.
*   _semi_join(table_x, table_y)_ - Returns all rows from x where there are matching values in y, keeping just columns from x.
*   _anti_join(table_x, table_y)_ - Returns all rows from x where there are not matching values in y, keeping just columns from x.

## Key matching

When joining the tables, the key(s) on which you join is specified in the specified in the _by_ argument of the joining function (the SQL equivalent of _ON_).

*   When only matching on only **one column**, the _by_ argument can be simple a string containing the column name, for example:

```r
inner_join(table_x, table_y, by="key_column")
```

*   Matching on differing **column names** is done by passing a vector to the _by_ argument like:

```r
inner_join(table_x, table_y, by=c("key_column_x"="key_column_y"))
```

# Stacking tables

Stacking tables, the SQL equivalent is _UNION_ statement, is done by the _bind_rows(table_a, table_b, ..., table_z)_ function. If the names of the tables match, the operation is performed automatically, irrespective of the column order.

# Completing data

Sometimes we want to make sure certain combinations are always present in a data frame, but sometimes that doesn't happen in the actual data itself. Let's take an example from the tutorial on [text mining](/mining-alices-wonderland/). Here we have a set of data with characters and sentiments. It could be not all characters have sentiments in the data, but we do want them in the dataset to show they are missing. To achieve this we can use the [_complete_](http://tidyr.tidyverse.org/reference/complete.html) function. The first argument in this function specifies which group we want to complete (the characters), then we specify which unique values we want to fill put when missing by using the _sentiment_ variable within the _nesting_ function. In the _fill_ parameter we specify the values we want to give to the variables when the new sentiments are added.

```r
tbl_person_sentiments %<>%
  complete(persona, nesting(sentiment), fill = list(qty_sentiments = 0,
                                                    qty_sentiment_persona = 0,
                                                    perc_sentiments = 0))
```

# Recoding data

Sometimes labels for groups of data are almost right, but just need a little tweaking: you want to replace the old versions with new versions. This is the code to achieve this. Remember to refactor the variable after this to take effect.

```r
old_names <- c("value 1 old", "value 2 old", "value 3 old", "value 4 old")
new_names <- c("value 1 new", "value 2 new", "value 3 new", "value 4 new")
string_vector <- plyr::mapvalues(string_vector, from = old_names, to = new_names)
```

When you want to recode data in such a way that you'd wind up using a lot of _ifelse()_ functions, you'd probably prefer the _case_when()_ function. This allows you to escape an endless amount of checking if you typed enough closing parenthesis.

```r
ELSE <- TRUE # I use this ELSE variable as a placeholder for the TRUE statement. Why not write a TRUE instead? I'm a nerd....
mtcars %>% mutate(carb_new = case_when(.$carb == 1 ~ "one",
                                       .$carb == 2 ~ "two",
                                       .$carb == 4 ~ "four",
                                       ELSE ~ "other" ))
```

# Binning data

There are three ways of binning data:

1) Equal observations in bins by using the [**Hmisc**](https://www.rdocumentation.org/packages/Hmisc) package. In the example below the iris's are binnen in groups of 3 observations by Sepal.Length
```r
iris %>% mutate(Sepal.Length_bin = cut2(Sepal.Length, g=3))
```

2) Equal value intervals using the _cut_ function that also is from the **Hmisc** package:
```r
iris %>% mutate(Sepal.Length_bin = cut(Sepal.Length, rep(5:10)))
```

3) Cutting values at specific values:
```r
iris %>% mutate(Sepal.Length_bin = cut(Sepal.Length, c(-Inf, 6, 7, Inf)))
```

# Aggregates on non-aggregates

Sometimes you want to have the values of aggregates on the non-aggregated level. Let's take an example from a data-set _iris_. This data-set contains measurements of petals and sepals (the large 'under'-flowers). Below you see a sample of this data.

<table class="gmisc_table" style="border-collapse: collapse; margin-top: 1em; margin-bottom: 1em;">

<thead>

<tr>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Sepal.Length</th>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Sepal.Width</th>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Petal.Length</th>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Petal.Width</th>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Species</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align: left;">1</td>

<td style="text-align: center;">5.1</td>

<td style="text-align: center;">3.5</td>

<td style="text-align: center;">1.4</td>

<td style="text-align: center;">0.2</td>

<td style="text-align: center;">setosa</td>

</tr>

<tr>

<td style="text-align: left;">2</td>

<td style="text-align: center;">4.9</td>

<td style="text-align: center;">3</td>

<td style="text-align: center;">1.4</td>

<td style="text-align: center;">0.2</td>

<td style="text-align: center;">setosa</td>

</tr>

<tr>

<td style="text-align: left;">3</td>

<td style="text-align: center;">4.7</td>

<td style="text-align: center;">3.2</td>

<td style="text-align: center;">1.3</td>

<td style="text-align: center;">0.2</td>

<td style="text-align: center;">setosa</td>

</tr>

<tr>

<td style="text-align: left;">4</td>

<td style="text-align: center;">4.6</td>

<td style="text-align: center;">3.1</td>

<td style="text-align: center;">1.5</td>

<td style="text-align: center;">0.2</td>

<td style="text-align: center;">setosa</td>

</tr>

<tr>

<td style="text-align: left;">5</td>

<td style="text-align: center;">5</td>

<td style="text-align: center;">3.6</td>

<td style="text-align: center;">1.4</td>

<td style="text-align: center;">0.2</td>

<td style="text-align: center;">setosa</td>

</tr>

<tr>

<td style="border-bottom: 2px solid grey; text-align: left;">6</td>

<td style="border-bottom: 2px solid grey; text-align: center;">5.4</td>

<td style="border-bottom: 2px solid grey; text-align: center;">3.9</td>

<td style="border-bottom: 2px solid grey; text-align: center;">1.7</td>

<td style="border-bottom: 2px solid grey; text-align: center;">0.4</td>

<td style="border-bottom: 2px solid grey; text-align: center;">setosa</td>

</tr>

</tbody>

</table>

The table contains 50 measurements of each of the iris specis (setosa, versicolor and virginica). If we want this same table to contain the average length and width of the sepals and petals per species we could do this by creating an aggregated the table (_group_by())_ and with summarized data (_summarise()_), and then join the original iris data-set with the temporary table. But we can also do this in one step, by using the _group_by_ function and then the _mutate_ function instead of the _summarise_ function, like so:

```r
iris %>% group_by(Species) %>%
  mutate(Sepal.Length.Avg = mean(Sepal.Length)) %>%
  mutate(Sepal.Width.Avg = mean(Sepal.Width)) %>%
  mutate(Petal.Length.Avg = mean(Petal.Length)) %>%
  mutate(Petal.Width.Avg = mean(Petal.Width))
```

Which output would look like this:

<table class="gmisc_table" style="border-collapse: collapse; margin-top: 1em; margin-bottom: 1em;">

<thead>

<tr>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Sepal.Length</th>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Sepal.Width</th>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Petal.Length</th>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Petal.Width</th>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Species</th>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Sepal.Length.Avg</th>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Sepal.Width.Avg</th>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Petal.Length.Avg</th>

<th style="border-bottom: 1px solid grey; border-top: 2px solid grey; text-align: center;">Petal.Width.Avg</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align: left;">1</td>

<td style="text-align: center;">5.1</td>

<td style="text-align: center;">3.5</td>

<td style="text-align: center;">1.4</td>

<td style="text-align: center;">0.2</td>

<td style="text-align: center;">setosa</td>

<td style="text-align: center;">5.006</td>

<td style="text-align: center;">3.428</td>

<td style="text-align: center;">1.462</td>

<td style="text-align: center;">0.246</td>

</tr>

<tr>

<td style="text-align: left;">2</td>

<td style="text-align: center;">4.9</td>

<td style="text-align: center;">3</td>

<td style="text-align: center;">1.4</td>

<td style="text-align: center;">0.2</td>

<td style="text-align: center;">setosa</td>

<td style="text-align: center;">5.006</td>

<td style="text-align: center;">3.428</td>

<td style="text-align: center;">1.462</td>

<td style="text-align: center;">0.246</td>

</tr>

<tr>

<td style="text-align: left;">3</td>

<td style="text-align: center;">4.7</td>

<td style="text-align: center;">3.2</td>

<td style="text-align: center;">1.3</td>

<td style="text-align: center;">0.2</td>

<td style="text-align: center;">setosa</td>

<td style="text-align: center;">5.006</td>

<td style="text-align: center;">3.428</td>

<td style="text-align: center;">1.462</td>

<td style="text-align: center;">0.246</td>

</tr>

<tr>

<td style="text-align: left;">4</td>

<td style="text-align: center;">4.6</td>

<td style="text-align: center;">3.1</td>

<td style="text-align: center;">1.5</td>

<td style="text-align: center;">0.2</td>

<td style="text-align: center;">setosa</td>

<td style="text-align: center;">5.006</td>

<td style="text-align: center;">3.428</td>

<td style="text-align: center;">1.462</td>

<td style="text-align: center;">0.246</td>

</tr>

<tr>

<td style="text-align: left;">5</td>

<td style="text-align: center;">5</td>

<td style="text-align: center;">3.6</td>

<td style="text-align: center;">1.4</td>

<td style="text-align: center;">0.2</td>

<td style="text-align: center;">setosa</td>

<td style="text-align: center;">5.006</td>

<td style="text-align: center;">3.428</td>

<td style="text-align: center;">1.462</td>

<td style="text-align: center;">0.246</td>

</tr>

<tr>

<td style="border-bottom: 2px solid grey; text-align: left;">6</td>

<td style="border-bottom: 2px solid grey; text-align: center;">5.4</td>

<td style="border-bottom: 2px solid grey; text-align: center;">3.9</td>

<td style="border-bottom: 2px solid grey; text-align: center;">1.7</td>

<td style="border-bottom: 2px solid grey; text-align: center;">0.4</td>

<td style="border-bottom: 2px solid grey; text-align: center;">setosa</td>

<td style="border-bottom: 2px solid grey; text-align: center;">5.006</td>

<td style="border-bottom: 2px solid grey; text-align: center;">3.428</td>

<td style="border-bottom: 2px solid grey; text-align: center;">1.462</td>

<td style="border-bottom: 2px solid grey; text-align: center;">0.246</td>

</tr>

</tbody>

</table>