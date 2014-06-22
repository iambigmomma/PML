Practical Maching Learning Couse Project
========================================================

### Loading the basic library

```r
library(caret)
```

```
## Loading required package: lattice
## Loading required package: ggplot2
```

```
## Warning: couldn't connect to display ":0"
```

```r
library(randomForest)
```

```
## randomForest 4.6-7
## Type rfNews() to see new features/changes/bug fixes.
```


### Loading the orignal data form csv file

```r
trainRawData <- read.csv("pml-training.csv", na.strings = c("NA", ""))
testRawData <- read.csv("pml-testing.csv", na.strings = c("NA", ""))
```


### Cleanup the NA data in the original data

```r
NAs <- apply(trainRawData, 2, function(x) {
    sum(is.na(x))
})
validData <- trainRawData[, which(NAs == 0)]
```


### Partition the "clean" data into training set and test set for cross validation

```r
trainIndex <- createDataPartition(y = validData$classe, p = 0.2, list = FALSE)  # 3927 rows
trainData <- validData[trainIndex, ]
testData <- validData[-trainIndex, ]

removeIndex <- grep("timestamp|X|user_name|new_window", names(trainData))
trainData <- trainData[, -removeIndex]
```


### Train the model with the clean and selected training set by random forest

```r
modFit <- train(trainData$classe ~ ., data = trainData, method = "rf")
modFit
```

```
## Random Forest 
## 
## 3927 samples
##   53 predictors
##    5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Bootstrapped (25 reps) 
## 
## Summary of sample sizes: 3927, 3927, 3927, 3927, 3927, 3927, ... 
## 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
##   2     1         1      0.004        0.005   
##   30    1         1      0.005        0.006   
##   50    1         1      0.006        0.008   
## 
## Accuracy was used to select the optimal model using  the largest value.
## The final value used for the model was mtry = 27.
```


### Using test set to calculate the sample error rate

```r
predictions = predict(modFit, testData)
accuracy_true <- sum(predictions == testData$classe)/length(predictions)
sample_error = 1 - accuracy_true
cat("Sample error rate: ", sample_error * 100, "%")
```

```
## Sample error rate:  1.733 %
```


### Try to evaluate the sample error and the prediction result

```r
pred <- predict(modFit, testRawData)
print(pred)
```

```
##  [1] B A A A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```

