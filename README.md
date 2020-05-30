# PML
Practical Machine Learning Assignment

---
title: "Assignment"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Practical Machine Learning
Building a model to predict how barbell lifts are conducted from data on measurements generated from activity tracking devices. 


Load necessary libraries
```{r}
library(caret)
library(ggplot2)
library(rattle)
```
Read training and testing data, with NAs replacing all invalid observations
```{r}
training=read.csv("pml-training.csv", na.strings=c("NA","#DIV/0!", ""))
testing=read.csv("pml-testing.csv", na.strings=c("NA","#DIV/0!", ""))
```
Many variables have a lot of NA values and some of them have negligible variance. We drop these. Subsequently, the first five columns that are identification variables too are dropped
```{r}
NZV <- nearZeroVar(training)
training <- training[, -NZV]
NAIndex    <- sapply(training, function(x) mean(is.na(x))) > 0.9
training <- training[, NAIndex==FALSE]
training <- training[, -(1:5)]
```

Training data split into Train and Test set. Train set to be used for model building while Test set to be used for estimating out of sample error.
```{r}
inTrain  <- createDataPartition(training$classe, p = 0.7, list = FALSE)
Train   <- training[inTrain, ]
Test    <- training[-inTrain, ]

```

Random Forest model built on the Train data 
```{r}
model=train(classe~., data=Train, method="rf")
```

Obtained model used to predict classe on the Test data, confusion matrix created to gauge accuracy and out of sample error
```{r}
prediction=predict(model, newdata=Test)
CM=confusionMatrix(prediction, Test$classe)
```



## Model Accuracy

The model shows over 99% accuracy on the Test dataset. So, out of sample error, can be expected to be less than 1%

## Predictions for the testing data
```{r}
predict(model, newdata=testing)
```

