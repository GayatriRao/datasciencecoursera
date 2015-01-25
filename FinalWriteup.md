 **Final project write up ** 

This write up describes the analysis I carried out to test the data collected from 6 participants as outlined in the study described at [link](http://groupware.les.inf.puc-rio.br/har). Measurements from accelormeters attached to the beld, forearm, arm and dumbell of these participants were recorded while they performed barbell lifts correctly and incorrectly. 

I read the paper the authors of the study wrote in which they described the feature selection and the model they used. I followed their lead and used a random forest, though feature selection was done based on the test variables. This is described below.  

Only the predictors available in the test data were retained in the training model. Variables with NA were excluded and additional feature selection was carried out using nearZeroVar in the caret package. Variables that were TRUE for 'nzv' were excluded from the analysis. The variable 'new_window' was also excluded because it is present in one dataset but not the other.
 
Cross-validation was done using bootstrap resampling within the train function. I suspect this overfit the data but changing it to boot632 and oob didn't seem to help either.

 
```{r}
# final project
train.dat = na.omit(read.csv("/Users/raog/Documents/PredictionAndMachineLearning/pml-training.csv", header = TRUE))
test.dat = read.csv("/Users/raog/Documents/PredictionAndMachineLearning/pml-testing2.csv", header = TRUE)  #NA excluded

##first only include the variables that are available in test:
testvars <- names(train.dat) %in% names(test.dat)
testvars[[length(testvars)]] = TRUE
training <- train.dat[testvars]
 
var = nearZeroVar(training, saveMetrics=TRUE)
var
# remove all variables with nzv
myvars <- names(training) %in% c("new_window")

train <- training[!myvars]
test <- test.dat[!myvars]

train_var = train[-147]

control <- trainControl(method="boot")
modelFitrf = train(train$classe ~ ., method = "rf", data = train_var,trControl=control)
modelFitrf
prf = predict(modelFitrf, newdata=test)
prf
 
```
 
 
 