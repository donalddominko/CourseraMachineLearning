# Implementing machine learning algorithm with Cross-Validation
Author  : Donald Dominko - Copyright 2015 Donald Dominko
About   : Course Project asignement in class "http://class.coursera.org/predmachlearn-011"


## Introduction
This is a "rough cut" procedure for creating a "machine learning" based prediction algorithm with using cross validation which will determin the most suitable variables for predicting the outcome.
This algorithm was not used for an actual prediction you should not treat it as a complete work in that regard i.e. **correctly** predicting 20 validation cases at the end. In reality I used "randomForest()" function for predicting and in this example I will use "train()" function from "caret" package with "rpart" method

## Loding data

Data is provided as part of an assignment and after loading it and some basic pre processing it will be used for making two data partitions that will be used for making a confusion matrix that will be used for comparison of error.

```
## Loading required package: caret
## Loading required package: lattice
## Loading required package: ggplot2
## Loading required package: randomForest
## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
## Loading required package: e1071
## Loading required package: rpart
```


  
  
### Spliting into training and test sets
This part of R chunk will split the testing and training. Variable "user_name" would be a perfect predictor so it will be used for a split. After that it will be removed.

```r
        # Spliting training set into training and test set
        inTrain.01 = createDataPartition(pmlTrainingClean$user_name, p=0.60, list=FALSE)
        
        # First dataset batch for training and testing
        training.01 = pmlTrainingClean[inTrain.01,]
        testing.01 = pmlTrainingClean[-inTrain.01,]

        # Second split 
        inTrain.02 = createDataPartition(pmlTrainingClean$user_name, p=0.75, list=FALSE)

        # Second batch of training and testing dataset
        training.02 = pmlTrainingClean[inTrain.02,]
        testing.02 = pmlTrainingClean[-inTrain.02,]

        # For housekeeping purposes and unbiased prediction we remove "user_name" variable
        training.01 = training.01[-2] 
        training.02 = training.02[-2]
        testing.01 = testing.01[-2]
        testing.02 = testing.02[-2]
```

### Building the models
This part will build two models. We'll let "train()" function deal with cross-validation by passing "trControl" parameter value "trainControl(method="cv")" and "cv" part instructs that it should train model based on cross-validation.
Also some preprocessing takes place so that data will as good as possible for a "raw cut".

```r
        # First model will use data from first data slicing partition
        rpartModelFit.01 = train(classe ~ ., 
                                   data=training.01, 
                                   method="rpart", 
                                   preProcess = c("center", "scale", "pca"),
                                   trControl=trainControl(method="cv"))

        # And second uses from second data partition ("training.02" subset)
        rpartModelFit.02 = train(classe ~ ., 
                                   data=training.02, 
                                   method="rpart", 
                                   preProcess = c("center", "scale", "pca"),
                                   trControl=trainControl(method="cv"))
```
We could further anaylse best variables based on the model(s) that we get.
  
### Plotting best.model from tune.rpart function
Folowing part will calculate best tuning values for rpart function given the data and based on those we can we get summary and we can use "best.model" of the returned object to print model and use that to pick best variables for building an algorithm.

  
**Following two plots ilustrate use of Cross-validation via use of function plotcp() and errors related to result of tune.rpart() function for both scenarios of data partitioning.**

```
## 
## Parameter tuning of 'rpart.wrapper':
## 
## - sampling method: 10-fold cross validation 
## 
## - best parameters:
##  minsplit
##         5
## 
## - best performance: 0.2600189 
## 
## - Detailed performance results:
##   minsplit     error dispersion
## 1        5 0.2600189 0.01121234
## 2       10 0.2600189 0.01121234
## 3       15 0.2600189 0.01121234
```

![plot of chunk unnamed-chunk-6](img/unnamed-chunk-6-1.png) 

```
## 
## Parameter tuning of 'rpart.wrapper':
## 
## - sampling method: 10-fold cross validation 
## 
## - best parameters:
##  minsplit
##         5
## 
## - best performance: 0.2007088 
## 
## - Detailed performance results:
##   minsplit     error dispersion
## 1        5 0.2007088  0.0259827
## 2       10 0.2007088  0.0259827
## 3       15 0.2007088  0.0259827
```

![plot of chunk unnamed-chunk-6](img/unnamed-chunk-6-2.png) 
  
## Summary
Based on the precedure above and resulting plots we can choose just a few variables that we estimate are best based on the plotted trees from "best.model" value.
There are other methods we can use to pick best variable based on anaysis of our model we got back from "train()" fuction.



