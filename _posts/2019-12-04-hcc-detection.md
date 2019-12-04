﻿---
layout: post
title:  "HCC Detection"
---

# Our Machine Learning Model consists of some steps:
1. Preparing the dataset  
2. Pre-processing Stage:  
    * Missing Values
    * Factorize the dependent variable
    * Scaling The Numeric Data 
3. Applying the classifiers:
    * K-NN Classifier
    * Logistic Regression Classifier
    * Naive Bayes Classifier
    
4. Comparing the results

## Preparing the Data Set
```markdown
dataset <- read.csv("hcc-data.txt", header=TRUE, na.strings = "?")
```

### Statistics on the Data Set
* With `head` function we can look at the first few records of the data set.
* Using `str` function shows us the type of the data set and number of observations and variables.
* And `summary` function gives information about the min, max and mean of each variable in the data set.
    ```
    head(dataset)
    str(dataset)
    summary(dataset)
    ```
* Finally we have to check if there's a missing values in our set, so we can handle these values if existed.
    ```
    sapply(dataset, function(x) sum(is.na(x)))
    ```
    
    > In our data set there are missing values so this was the result of the above function:
    ![Missing_Values](../assets/images/MissingValues.png)


## Handling Missing Values
We have to impute these missing values as it affects on the equations used in building the model. In our case we used a packaged called `mice`.

To impute the missing values, mice package use an algorithm in a such a way that use information from other variables in the dataset to predict and impute the missing values. 

```
install.packages("mice")
library(mice)
set.seed(789)

imputed = mice(dataset)
imputed <- complete(imputed)
```

## Factorizing the dependent variable
Factor is a data structure used for fields that takes only predefined, finite number of values (categorical data). And in our case the final result is either the patient have the disease or no, which means it's a classification type. That's why we had to factorize the Class attribute to "0" and "1" levels.

```
imputed$Class = factor(imputed$Class, levels = c(0, 1))
```

## Scaling The Numeric Data 
There can be instances found in data frame where values for one feature could range between 1-100 and values for other feature could range from 1-10000000. This will **impact prediction accuracy**. **The objective is to improve predictive accuracy** and not allow a particular feature impact the prediction due to large numeric value range.

Thus, we may need to normalize or scale values under different features such that they fall under common range. In our data set, the numeric features are from column 24 to 29:

```
temp1 <- imputed[, 1:23]
temp_scale = scale(imputed[, 24:49])
Class <- imputed[, 50]
Final_data <- data.frame(temp1, temp_scale, Class)
```


## After All The Pre-processing
```

| Column0 | Gender | Symptoms | Alcohol | HBsAg | HBeAg | HBcAb | HCVAb | Cirrhosis | Endemic | Smoking | Diabetes | Obesity | Hemochro | AHT | CRI | HIV | NASH | Varices | Spleno | PHT | PVT | Metastasis | Hallmark | Age                | Grams_day          | Packs_year         | PS                 | Encephalopathy    | Ascites            | INR                | AFP                | Hemoglobin        | MCV                | Leucocytes         | Platelets          | Albumin             | Total_Bil          | ALT                | AST                | GGT                 | ALP                | TP                  | Creatinine          | Nodule             | Major_Dim          | Dir_Bil             | Iron               | Sat                | Ferritin           | Class |
|---------|--------|----------|---------|-------|-------|-------|-------|-----------|---------|---------|----------|---------|----------|-----|-----|-----|------|---------|--------|-----|-----|------------|----------|--------------------|--------------------|--------------------|--------------------|-------------------|--------------------|--------------------|--------------------|-------------------|--------------------|--------------------|--------------------|---------------------|--------------------|--------------------|--------------------|---------------------|--------------------|---------------------|---------------------|--------------------|--------------------|---------------------|--------------------|--------------------|--------------------|-------|
| 1       | 1      | 0        | 1       | 0     | 0     | 0     | 0     | 1         | 0       | 1       | 1        | 0       | 1        | 0   | 0   | 0   | 0    | 1       | 0      | 0   | 0   | 0          | 1        | 0.17336124237755   | 0.771425693312772  | -0.148430375670675 | -0.861749914072829 | -0.36902324824164 | -0.638568718126724 | 0.242635781533894  | -0.140624552634307 | 0.386341861356185 | 1.3718620413012    | -0.499258041899373 | -1.06189069924242  | -0.0570335848408941 | -0.172473335742014 | -0.565091750159498 | -0.626958799385725 | -0.322041037446935  | -0.383951790138616 | -0.151339553250634  | -0.460438758828878  | -0.965935627295664 | -0.628000771077676 | -0.317788554701684  | -0.495057703214069 | 0.728751340688424  | 0.81177163859096   | 1     |
| 2       | 0      | 0        | 0       | 0     | 0     | 0     | 1     | 1         | 0       | 1       | 1        | 0       | 0        | 1   | 0   | 0   | 0    | 1       | 0      | 0   | 0   | 0          | 1        | -0.202027274581711 | -1.04560856711603  | 0.233890288935609  | -0.861749914072829 | -0.36902324824164 | -0.638568718126724 | -0.515840453763092 | -0.137177019033542 | 0.198576302817833 | 0.552587527022239  | -0.499638787124567 | -0.576819835484262 | -1.07991853035693   | -0.430563253189739 | -0.670111617375173 | -0.557947323802906 | -0.609828106985558  | 0.356722596914621  | -0.142525212874229  | 1.60442524267276    | -0.965935627295664 | -0.956941270568429 | -0.372258103889632  | -0.11175938988608  | 0.982973067125525  | -0.807676482422879 | 1     |
| 3       | 1      | 0        | 1       | 1     | 0     | 1     | 0     | 1         | 0       | 1       | 0        | 0       | 0        | 1   | 1   | 0   | 0    | 0       | 0      | 1   | 0   | 1          | 1        | 0.999215979687925  | -0.382457377178511 | 0.638700404401087  | 0.830973131427371  | -0.36902324824164 | 0.824817927580352  | -0.958284924353    | -0.141224006819859 | -1.86684484110404 | -1.81024766633304  | -0.498046579819212 | -1.05840461164909  | -0.2031600056289    | -0.485868235499966 | -0.145012281296796 | -0.316407159263042 | -0.248149762835666  | -0.617549096824637 | -0.160153893627038  | 0.999566090717736   | 1.25537975254802   | 1.21019613784124   | -0.42672765307758   | -1.09738362415805  | -1.15975291284433  | -0.893588849585417 | 1     |
| 4       | 1      | 1        | 1       | 0     | 0     | 0     | 0     | 1         | 0       | 1       | 1        | 0       | 0        | 1   | 0   | 0   | 0    | 0       | 0      | 0   | 0   | 1          | 1        | 0.924138276296073  | -0.515087615166015 | 0.188911387217223  | -0.861749914072829 | -0.36902324824164 | -0.638568718126724 | -0.979353708666805 | -0.124865359079153 | 0.245517692452421 | 0.243875391206975  | -0.49783890060547  | -1.06020840496146  | 0.381345677523122   | -0.485868235499966 | -0.880151351806524 | -0.362414809651588 | -0.668163323783927  | -0.247211903298019 | -0.0631961494865865 | -0.0328659100330839 | -0.410606782334742 | 1.73263104879714   | -0.399492878483606  | -0.841851415272725 | -0.397087733533027 | -0.556382808472456 | 0     |
| 5       | 1      | 1        | 1       | 1     | 0     | 1     | 0     | 1         | 0       | 1       | 0        | 0       | 0        | 1   | 1   | 0   | 0    | 0       | 0      | 0   | 0   | 0          | 1        | 0.84906057290422   | 0.280693812759008  | 0.188911387217223  | -0.861749914072829 | -0.36902324824164 | -0.638568718126724 | -1.00042249298061  | -0.140933688200848 | 0.667990199163713 | 0.0064045175029266 | -0.498738843865018 | -1.06095609130855  | 0.965851360675143   | -0.430563253189739 | 1.41278241573572   | 2.42104803885541   | -0.360931181979181  | -0.617549096824637 | -0.168968234003443  | 0.686707908672033   | -0.965935627295664 | 0.436218491980643  | -0.181614681731815  | -0.531562304483401 | -0.832896407425202 | -0.880701994511037 | 1     |
| 6       | 1      | 0        | 1       | 0     | 0     | 0     | 0     | 1         | 0       | 1       | 0        | 1       | 0        | 0   | 0   | 0   | 0    | 1       | 1      | 1   | 0   | 0          | 1        | 0.773982869512368  | 0.0154333367840003 | 0.188911387217223  | -0.015388391322729 | -0.36902324824164 | 0.824817927580352  | 0.34797970310292   | -0.140523747558262 | 0.245517692452421 | -0.42104305516436  | -0.499084975887922 | -1.06202154435317  | -0.0570335848408941 | 0.0856165817057106 | 0.432596988389419  | 0.304696120982322  | -0.0925891847066809 | 1.01763204997751   | -0.283554658896704  | -0.251866637465076  | -0.965935627295664 | 0.629712903445792  | -0.0726755833559194 | -0.641076108291398 | -0.5786746809881   | -0.68954697757439  | 0     |

```
##### As we see there's no missing values and the normalization is done. :blush:

# K-NN Classifier

## What is K-NN?
...

## How it works?
..

## Used Algorithm
..


# Logistic Regression Classifier

## What is Logistic Regression?
.

## How it works?
..

## Used Algorithm
..


# Naive Bayes Classifier

## What is Naive Bayes?
.

## How it works?
..

## Used Algorithm
..


# Comparing The Results

| Classifier | K-NN | Logistic Regression | Naive Bayes |
|----------|----------|----------|----------|
| Accuracy | 71.1% | % | % |