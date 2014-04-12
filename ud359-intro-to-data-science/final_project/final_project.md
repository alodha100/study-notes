# MTA Subway Network question: how does rain affect ridership?

## Overview

The MTA (Metropolitan Transportation Authority) subway network runs from New York City through Long Island, southeastern New York State, and Connecticut.

The subway is a quintessential piece of New York culture; having been featured in countless novels, movies and TV shows.

For subway operators, predicting and understanding the connection between people's behaviour and weather conditions could be extremely useful for operational planning. So, in this short article, we're going to do some analysis on how weather affects the MTAs ridership, specifically asking the question: what happens when it rains?

## Dataset

Our sample dataset includes data from roughly 550 stations across New York. It begins on the 1st of May and ends on the 30th. 

According to our dataset, there were over 144,532,327 people entering the turnstile for the month, an average of about 4,817,744 per day and about 33,724,209 entries per week.

([basic_stats.py](https://github.com/lextoumbourou/study-notes/blob/master/ud359-intro-to-data-science/final_project/basic_stats.py)).

We begin our analysis by plotting the mean ridership by hour for the month of May across our entire dataset.

### Mean Ridership Per Hour

<img src="https://raw.githubusercontent.com/lextoumbourou/study-notes/master/ud359-intro-to-data-science/final_project/images/mean-entries-per-hour.png"></img>

[entries_per_hour.py](https://github.com/lextoumbourou/study-notes/blob/master/ud359-intro-to-data-science/final_project/entries_per_hour.py)

From simply examining the graph, we can see that the busiest hour for the station is around 20:00.

Now let's create the same line graph, but this time we'll separate wet days with non-wet days.

### Mean Ridership Per Hour (Wet vs Dry days)

<img src="https://raw.githubusercontent.com/lextoumbourou/study-notes/master/ud359-intro-to-data-science/final_project/images/mean-entries-per-hour-wet-vs-dry.png"></img>

[entries_per_hour_wet_to_dry.py](https://github.com/lextoumbourou/study-notes/blob/master/ud359-intro-to-data-science/final_project/entries_per_hour_wet_to_dry.py)

It appears, based on this chart alone, that there is a slight increase in ridership on wetter days.

However, more statistical rigour is required before coming to any correlation conclusions.

## Statistical Analysis

Firstly, let's perform some basic statistical comparisons between wet and dry days.

&nbsp; | Clear          | Rain
------ | -------------- | -----
count  | 20.000000      | 10.000000
mean   | 4788886        | 4875460
std    | 1392179.929718 | 1336030.366933
min    | 2370432.000000 | 2661525.000000
25%    | 3484968.250000 | 4064350.000000
50%    | 5666797.500000 | 5549290.000000
75%    | 5864204.500000 | 5806608.500000
max    | 5965262.000000 | 5866031.000000

[compare_datasets.py](https://github.com/lextoumbourou/study-notes/blob/master/ud359-intro-to-data-science/final_project/compare_datasets.py)

To perform a comparison between the two samples group, we first need to understand the distribution of the data. By plotting the hourly ridership for both sample groups, we end up with a non-normally distributed dataset.

### Histogram of Ridership Per Hour

<img src="https://raw.githubusercontent.com/lextoumbourou/study-notes/master/ud359-intro-to-data-science/final_project/images/histogram-entries.png"></img>

[histogram.py](https://github.com/lextoumbourou/study-notes/blob/master/ud359-intro-to-data-science/final_project/histogram.py)

Therefore, the Mann Whitney U Test may be an appropriate test to determine whether the sample difference is statistically significant.

In this example, we're setting a p-critical value of 0.05. We'll perform a two-tailed test, with the following U-value: 1924409167.0 and a two-tailed p-value: 0.0386192688276. Based on this, we reject the null hypothesis. Rain does appear to affect ridership. However, more data would be required for clarity of these results.

[mann_whitney_i.py](https://github.com/lextoumbourou/study-notes/blob/master/ud359-intro-to-data-science/final_project/mann_whitney_i.py)

## Predicting Ridership Per Station

Using Linear Regression with the Gradient Descent algorithm, we can collect a set of theta values that can help us predict ridership at a station using the following input variables: rain, precipi, Hour, meantempi and station.

In our model, we set a learning rate of 0.5 - a happy medium between learning too fast and over utilising our computational resources - and the value 50 as the number of iterations. This provides us with an r-squared value of 0.45804446474. Not exactly ideal but moves us some way toward having a model for accurately predicting ridership based on weather patterns.

[gradient_descent.py](https://github.com/lextoumbourou/study-notes/blob/master/ud359-intro-to-data-science/final_project/gradient_descent.py)

## Working with a larger dataset 

If we wanted to perform some similar calculations for a larger dataset, we could look at introduction MapReduce to perform some calculations. 

For example, we could write MapReduce code to determine top 10 stations for weekly average ridership. To do that, we'd first put the large dataset onto a HDFS. Then we might write a mapper and reducer like the following: [mapper.py](https://github.com/lextoumbourou/study-notes/blob/master/ud359-intro-to-data-science/final_project/mapper.py) and [reducer.py](https://github.com/lextoumbourou/study-notes/blob/master/ud359-intro-to-data-science/final_project/reducer.py).

Which, using the current dataset, returns the following information.

```
('R170', 2887918.0)
('R084', 1809423.0)
('R022', 1796932.0)
('R033', 1711663.0)
('R046', 1695150.0)
('R179', 1618261.0)
('R055', 1607534.0)
('R011', 1582914.0)
('R012', 1564752.0)
('R018', 1389878.0)
```

Based on this, it appears R170 is the busiest turnstile.

## Conclusion

Based on the tools available to us, we can infer that rain doesn't have a statistical correlation with ridership on the MTA subway system. With that said, one would expect with more data we could get closer to a more definitive answer. Using Gradient Descent, we can predict ridership using input variables relating to the weather, providing us with a moderately accurate prediction model. Lastly, with a larger data size, map reduce techniques could help us scale out our processing to help answer additional questions about the data.