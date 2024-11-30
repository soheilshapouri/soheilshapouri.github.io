---
layout: post
title: "Decisio Trees"
date: 2024-12-01
category: [codes]
excerpt: "Where trees branch, decisions bloom."
---
Decision trees might not be the first choice wehn it comes to predictive accuracy, but they have some advantages over other methods that make them a good choice you should always consider:
- They do not require transformation prior to modeling
- They are resistant to outliers
- They can handle missing values

So, let's grow some trees to decide what ecological factors affect collectivism. 

```r
ecodata <- read.csv("https://raw.githubusercontent.com/soheilshapouri/epidemics_collectivism/main/Data%20S2.csv")
library(rsample)
set.seed(123)
ecodata_split <- initial_split(ecodata, prop = 0.8, strata = "GCI")
ecodata_train <- training(ecodata_split)
ecodata_test <- testing(ecodata_split)
```
Decision trees with rpart package
```r
eco_dt1 <- rpart(
  formula = GCI ~ No_Epidemics + No_Disasters + Death_Epidemics + Death_Disasters + Mortality_Epidemics + Mortality_Disasters + GDP + Region,
  data = ecodata_train,
  method = "anova"
)
# method = "anova" for regression problems, method = "class" for classification problems
```
If you are only concerned about predictive accuracy, multicolinearity is not an issue here. So, I entered all the predicotrs I had in the data. 
  
```r
eco_dt1
```
The output shows GDP is the first variable resulting in the largest reduction of SSE. But it is more intuitive to look at the visualization of decision trees. 
```r
library(rpart.plot)
rpart.plot(eco_dt1)
```
![Decision Tree](https://raw.githubusercontent.com/soheilshapouri/soheilshapouri.github.io/master/_posts/multipleLogisticVisualization.jpg)
