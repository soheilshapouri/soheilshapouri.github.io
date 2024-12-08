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
- First: mtry, number of variables at each split is set to p / 3 for regression problems and sqrt(p) for classification problems, where p is the number of predictors.
- Second: set respect.unordered.factors as "order" when categorical variables have a lot of levels (high cardinality) or you are concerned about overfitting, do not have enough computational power, but set respect.unordered.factors as "ignore" if categorical variables have only a few levels or you want to aim for high predictive accuracy.
- Third, Unlike many other ML procedure, set seed inside the training code.

```r
library(ranger)
n_features <- (length((setdiff(names(ecodata), "GCI"))))
eco_rf1 <- ranger(
  GCI ~ ., 
  data = eco_train,
  mtry = floor(n_features/3),
  respect.unordered.factors = "order",
  seed = 123
)
```
# Model Evaluation
```r
(rmse <- sqrt(eco_rf1$prediction.error))
```
# Feature Importance 
Impurity-based feature importance in Random Forest (via ranger) measures how much each feature reduces impurity (e.g., Gini or variance) across all splits in all trees, but it can be biased toward high-cardinality features. Permutation-based feature importance, on the other hand, evaluates importance by shuffling feature values and measuring the decrease in model performance (e.g., RMSE) on out-of-bag samples, providing an unbiased assessment.
ranger uses impurity by defualt.
```r
eco_rf2 <- ranger(
  GCI ~ .,
  data = eco_train, 
  mtry = floor(n_features/3),
  respect.unordered.factors = 'order',
  seed = 123, 
  importance = "permutation"
)
sort(eco_rf2$variable.importance, decreasing = TRUE)
```
GDP 0.34   Region 0.28   No_Epidemics 0.07 No_Disasters 0.03  
Varibale importance can also be visualized:
```r
vip::vip(eco_rf2)
```
Tune hyperparameters of random forests with caret cross-validation
```r
eco_rf3 <- train(
  GCI ~ .,
  data = eco_train,
  method = "ranger",
  trControl = trainControl(method = "cv", number = 10),
  tuneLength = 3
)
summary(eco_rf3$resample)
```
tuneLength will set the number of values being checked for mtry. Also, the model above shows below metrics:
RMSE Mean: 0.4754     Rsquared Mean: 0.6065  

let's add Region to see how much it improves the model:
```r
ecodata <- read.csv("https://raw.githubusercontent.com/soheilshapouri/epidemics_collectivism/main/Data%20S2.csv")
ecodata <- ecodata[c("No_Epidemics", "No_Disasters", "GDP", "Region", "GCI")]
eco_split <- initial_split(ecodata, prop = 0.7, strata = "GCI")
eco_train <- training(eco_split)
eco_test <- testing(eco_split)
eco_rf4 <- train(
  GCI ~ .,
  data = eco_train,
  method = "ranger",
  trControl = trainControl(method = "cv", number = 10),
  tuneLength = 4
)
summary(eco_rf4$resample)
```
RMSE Mean   :0.3667     Rsquared Mean   :0.8005
So, adding region drastically imporve the model. What is more intersting is that I compare these random forests with mixed-effect regression. Random forests were much better than mixed-effect regression with Region as random intercept. 

Sources: Hands-On Machine Learning with R, ChatGPT


   









