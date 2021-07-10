# Analysis of imputation methods in high resolution smart metering in buildings
The analysis were performed as a master's thesis in Smart Cities and Communities. 

## Table of contents
* [General info](#general-info)
* [Correlation analysis and its scenarios](#correlation-analysis-and-its-scenarios)
* [Time-gap scenarios](#time-gap-scenarios)
* [Univariate Imputation Methods](#univariate-imputation-methods) 
* [Multiple Imputation Methods using Energy and Weather variables](#multiple-imputation-methods-using-energy-and-weather-variables) 
* [Weather variables based Multiple Imputation Methods](#weather-variables-based-multiple-imputation-methods)

## General info

The aim of the project is to investigate the accuracy of single and multiple filling the gap techniques in accordance with the energy data and the impact of weather variables on the prediction of missing points. This will allow us to analyse on which basis the missing data should be treated and more importantly, with what type of methods. One of the reasons to carry out this research was due to the lack of imputation methods on indurstrial perspective.

- *Case study with the residential buildings located in Tartu, Estonia*
- *4 data sets with the aggregation of energy data measured by smart energy metering and weather data by the weather station*
- *8410 observations and 26 variables*

The original data sets do not contain any missing values. Therefore, they were introduced artificially by creating certain scenarios and after, their accuracy were checked by applying Root Mean Square Error (RMSE). At the end, Global Score (GS) value was proposed with a normalised RMSE for all of the variables in every data set. The sum of all of the accuracies of each applied method for every variables would give us GS. According to the GS value, we can state which method performed the best.

All the analyses were done in R software.

## Time-gap scenarios

6 different time-gap scenarios were created to observe if there will be an increase of the accuracy by the change of consecutiveness of missing points (in total 30 hours):
 
- 15 gaps of 2 hours
- 10 gaps of 3 hours
- 6 gaps of 5 hours
- 5 gaps of 6 hours
- 3 gaps of 10 hours
- 2 gaps of 15 hours

The missing points were introduced to a variable at a time. This implies the rest of the variables were complete and contributed to the prediction of the gaps when the correlation between variables were taken into account.

## Correlation analysis and its scenarios

The relationship within energy data variables and with weather variables may come handy when there is a gap. In order to perform it, Pearson correlation based analysis were applied using the following two commands:
```
B_11044_corr<-cor(Building_11044_Blind_1) # After we can obtain the mathematical calculation of the coefficients in the form of matrix
chart.Correlation(Building_11044_Blind_1, histogram=TRUE, pch=19) # Another way of vizualisation with the Pearson correlation coefficients between variables and scatter plots
```
##### Association strength and their corresponding correlation coefficients for Pearson correlation formula:

|Association strenght|Correlation coefficient (r)|
| :------------------: | :-------------------------: |
|Strong| r > 0.5 (positive); r < -0.5 (negative)|
|Medium| r > 0.3 (positive); r < -0.3 (negative)|
|Weak| r > 0|

Aferwards, three scenarios were proposed based on the correlation coefficients. It should be noted they are applicable solely for multivariate analysis:

- Highly correlated variables only (r > 0.5 for positive correlations, r < -0.5 for negative correlations) used to fill the gaps
- Highly and medium correlated correlations (r > 0.3 for positive correlations, r < -0.3 for negative correlations)
- All correlations with a coefficient of more than 0


## Univariate Imputation Methods

Single imputation techniques will be applied using the time series imputa-tion called [“Impute TS”](https://cran.r-project.org/web/packages/imputeTS/imputeTS.pdf) (R package) provided with a mixed variety of interpolation meth-ods to replace the missing values with artificially introduced missing data. Besides, it is one of the few methods which will impute the missing values in a univariate form while they are not enough available packages at the moment. Univariate means only one attrib-ute is measured over time, hence, only one variable will be imputed to observe the change and be able to compare the outcome of each parameter

First, time-series imputation techniques based on R package "ImputeTS" was applied with the following methods:
- Interpolation: Linear, Spline and Stineman
- Last Observation Carried Forward (LOCF) and Next Observation Carried Backward (NOCB) 
- Weighted Moving Average (WMA): Simple, Linear and Exponential
- Mean and Median

These methods rely only on the variable where the missing points occured. Consequently, the gaps were filled using the aforementioned gap filling techniques as it fitted how the data set was structured. The energy metering data records the variables every hour and hence eac observation stands for an hourly measurement. In total, the data set contains 8410 observations of 26 variables. Despite being an annual data, before the analysis carried out, it was preprocessed by cleansing the weather data. Whereas the same weather data is aggregated to all of the data sets. That is why there are 8410 observations instead of 8760.

## Multiple Imputation Methods using Energy and Weather variables 

For multiple imputations MICE, Amelia, and missForest packages were selected. First and foremost, MICE and missForest are some of the most studied existing imputation methods for different research fields. While Amelia is one of the least investigated meth-ods and hence, it was decided to check its accuracy against the other two methods and identify if it is appropriate to use to fill the gaps.

Initially, two weeks of data missing values were used to fill the gaps with the following multiple imputation methods:
- MICE
- Amelia
- missForest

All multiple filling the gap techniques follow the same missing point consecutiveness on 6 time-gap scenarios applied for univariate analysis. Apart from that, as it is a data frame, it was decided to include weather variables as well. Because depending on the correlation between variables which was performed previously, their accuracy varies considerably.

### *Multiple Imputation Methods of two weeks*

In the beginning, for all types of scenarios performed, two weeks without outliers were selected to hinder further obstacles.


### *Multiple Imputation Methods of eight weeks*

Next, now for the sensitivity analysis, those weeks are to be extended into eight weeks to observe how the multiple imputations accuracy will be altered with the expansion of the data set from which they can learn. This implies that instead of 336 observations from 11 smart energy meter data weather variables, it will now rely on 1344 observations with the same number of variables.

## Weather variables based Multiple Imputation Methods

One issue that could be encountered during the process of provided multiple imputation methods might be the inability of having full data with just losses of measurements in one variable only. In other words, as the predictions are made for the smart energy meter variables where missing values were introduced to only one of the variables at a time, while in real life if there is to be a gap for example in Heating Power then there are gaps for that period for all the variables in the smart meter. This happens because some fail in the sensor recordings leads to the loss of all the observations for that precise time. Hence, if it is a real-life scenario, then the only way we could predict those missing values would be relying on the weather data. Because they would be the only remaining data for the case study, assuming the rest of the smart energy meter observations were lost as well.

As a result, for this scenario, only the observations measured from the weather station are considered. Moreover, it was shown multiple times that the only variable having a strong correlation with the energy data was the Ambient Temperature which was predominantly high-negatively correlated for all cases. Despite the weak connection between other weather variables with the energy meter data, it was decided to include all of them for the analysis without the need of dividing them into correlation groups unlike was carried out for the previous three scenarios.
