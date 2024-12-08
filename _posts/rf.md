---
layout: post
title: "Random Forest with R"
date: 2024-12-07
category: [codes]
excerpt: "A symphony of trees, learning together"
---  
Random forests are fantastic for computational social sciences: They are robust to outliers, provide a feature importance measure that can be used for feature selection, automatically handle non-linear relationships, have better overal accuracy than individual decision trees, do not requre feature scaling, capture interactions without explicitly modeling them, do not assume homoscedasticity, and are not very sensitive to multicolinearity among explnatory variables. With proper tuning they can handle small datasets as good as large ones. 

# Get the data
```r
ecodata <- read.csv("https://raw.githubusercontent.com/soheilshapouri/epidemics_collectivism/main/Data%20S2.csv")
ecodata <- ecodata[c("No_Epidemics", "No_Disasters", "GDP", "GCI")]
```
# Train-test split
```r
library(rsample)
eco_split <- initial_split(ecodata, prop = 0.7, strata = "GCI")
eco_train <- training(eco_split)
eco_test <- testing(eco_split)
```
Three things to consider before training random forests:
- First: mtry, number of variables at each split is set to p / 3 for regression problems and 
