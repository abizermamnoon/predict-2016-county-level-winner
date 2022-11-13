Team Project 2
================
Abizer and Luis
Day 17

This code chunk ensures that there are no commands in output file

Download the packages

Read the key file

``` r
> key <- read.csv("https://raw.githubusercontent.com/mgelman/data/master/county_facts_dictionary.csv")
```

Create the train and test dataframes

``` r
> train <- read_csv("https://raw.githubusercontent.com/mgelman/data/master/train.csv")
> test <- read_csv("https://raw.githubusercontent.com/mgelman/data/master/test_No_Y.csv")
```

Define a winner variable, which is based on winner16 variable. Winner is
set as Democrat if winner16 is Dem and Republican if winner16 is Rep

``` r
> train <- train %>% mutate(winner = recode_factor(winner16, Dem = "Democrat", Rep = "Republican"))
```

We chose the x-variables by determining which variables were most
significant using a regression analysis

``` r
> library(plyr)
> train1 <- train %>% plyr::rename(c("RTN130207"="Retail_Sales_07","INC910213"="Income_per_capita","HSG096213"="Percent_multi_unit_housing","HSG495213"="Median_house_value","POP815213"="Spoken_non_english_lang","EDU635213"="Percent_Highschool_grad","EDU685213"="Percent_Undergrad","POP715213"="Percent_living_in_same_house_multiple_years","AGE295214"="percent_under_18","AGE775214"="percent_over_65","PST120214"="percent_change_in_pop"))
> 
> test1 <- test %>% plyr::rename(c("RTN130207"="Retail_Sales_07","INC910213"="Income_per_capita","HSG096213"="Percent_multi_unit_housing","HSG495213"="Median_house_value","POP815213"="Spoken_non_english_lang","EDU635213"="Percent_Highschool_grad","EDU685213"="Percent_Undergrad","POP715213"="Percent_living_in_same_house_multiple_years","AGE295214"="percent_under_18","AGE775214"="percent_over_65","PST120214"="percent_change_in_pop"))
> 
> Train1 <- train1 %>% select(Retail_Sales_07, Income_per_capita, Percent_multi_unit_housing, Median_house_value, Spoken_non_english_lang, Percent_Highschool_grad, Percent_Undergrad, Percent_living_in_same_house_multiple_years, percent_under_18, percent_over_65, percent_change_in_pop,winner)
> 
> xvars <- str_c(names(Train1)[1:11], collapse="+")
> myform <- as.formula(str_c("winner ~ ", xvars))
> 
> winner.glm <- glm(myform, data = Train1, family = binomial)
> 
> summary(winner.glm)

Call:
glm(formula = myform, family = binomial, data = Train1)

Deviance Residuals: 
    Min       1Q   Median       3Q      Max  
-3.2543   0.1038   0.2097   0.3633   3.5090  

Coefficients:
                                              Estimate Std. Error z value
(Intercept)                                  3.677e+00  2.400e+00   1.532
Retail_Sales_07                             -1.120e-07  3.028e-08  -3.699
Income_per_capita                            3.043e-04  3.339e-05   9.114
Percent_multi_unit_housing                  -9.933e-02  1.355e-02  -7.329
Median_house_value                          -1.336e-05  1.917e-06  -6.971
Spoken_non_english_lang                     -4.140e-02  7.666e-03  -5.400
Percent_Highschool_grad                      6.898e-02  1.896e-02   3.638
Percent_Undergrad                           -1.938e-01  1.920e-02 -10.091
Percent_living_in_same_house_multiple_years -1.490e-01  2.348e-02  -6.347
percent_under_18                             1.413e-01  3.317e-02   4.260
percent_over_65                              1.709e-01  3.277e-02   5.215
percent_change_in_pop                        1.831e-01  2.849e-02   6.426
                                            Pr(>|z|)    
(Intercept)                                 0.125438    
Retail_Sales_07                             0.000216 ***
Income_per_capita                            < 2e-16 ***
Percent_multi_unit_housing                  2.32e-13 ***
Median_house_value                          3.16e-12 ***
Spoken_non_english_lang                     6.66e-08 ***
Percent_Highschool_grad                     0.000275 ***
Percent_Undergrad                            < 2e-16 ***
Percent_living_in_same_house_multiple_years 2.20e-10 ***
percent_under_18                            2.04e-05 ***
percent_over_65                             1.84e-07 ***
percent_change_in_pop                       1.31e-10 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 2120.3  on 2488  degrees of freedom
Residual deviance: 1126.6  on 2477  degrees of freedom
AIC: 1150.6

Number of Fisher Scoring iterations: 6
```

We then fit the model “winner.glm” into the train and test data to
create probabilities. We then set a threshold of 0.05. For example, if
the model determines the probability to be 0.1, prediction will be
Republican or else Democrat. We then calculated the accuracy, precision
and recall for the model. Then, we created a double density curve

``` r
> train <- Train1 %>% mutate(probs = predict(winner.glm, type = "response"),prediction = ifelse(probs >= 0.05,"Republican","Democrat"))
> 
> test <- test1 %>% mutate(probs = predict(winner.glm, newdata = test1, type = "response"),prediction = ifelse(probs >= 0.05,"Republican","Democrat"))
> 
> train %>% summarize(accuracy = mean(winner == prediction), precision = sum(winner == "Republican" & prediction == "Republican")/sum(prediction == "Republican"), recall = sum(winner == "Republican" & prediction == "Republican")/sum(winner == "Republican"))
   accuracy precision    recall
1 0.8782644 0.8757273 0.9981052
> 
> ggplot(train,aes(x = probs, color = winner)) + geom_density(size = 1.5) + ggtitle("Forecasted Winner Probabilities ")
```

![](Abizer_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

Here, we created a ROC curve for the training data

``` r
> preds_obj1 <- prediction(train$probs, train$winner, label.ordering=c("Democrat","Republican"))
> perf_obj1 <- performance(preds_obj1, "tpr","fpr")
> perf_df1 <- data_frame(fpr=unlist(perf_obj1@x.values),
+                        tpr= unlist(perf_obj1@y.values),
+                        threshold=unlist(perf_obj1@alpha.values), 
+                        model="train")
Warning: `data_frame()` was deprecated in tibble 1.1.0.
Please use `tibble()` instead.
This warning is displayed once every 8 hours.
Call `lifecycle::last_lifecycle_warnings()` to see where this warning was generated.
> 
> ggplot(perf_df1, aes(x=fpr, y=tpr, color=model)) +  geom_line(size=1.5) + 
+   labs(x="false positive rate", y="true positive rate", title="ROC curve for logistic regression") + 
+   geom_abline(slope=1,intercept=0, linetype=3) 
```

![](Abizer_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

I calculated the error of the model using a 5-fold cross validation and
a 0.05 threshold

``` r
> set.seed(5)
> cost <- function(y, pi) 1-mean(y==(pi>0.05))
> train_error <- cv.glm(data = train, winner.glm,cost,5)
> train_error$delta[1]
[1] 0.1221374
```

Random Forest

Created a random forest model with 300 trees that randomly select out of
5 variables at each split. Then we fitted the model to the training data
and the testing data. We then calculated the accuracy, precision, recall
and error for the training data predictions

``` r
> set.seed(5)
> 
> winner_rforest <- randomForest(myform,data = train, ntree = 300, mtry = 5)
> 
> train3 <- train %>% mutate(probs = predict(winner_rforest, type = "prob")[,2],prediction = predict(winner_rforest, type = "response"))
> 
> test3 <- test %>% mutate(prediction = predict(winner_rforest,type = "response",newdata = test))
> 
> train3 %>% summarize(accuracy = mean(winner == prediction), precision = sum(winner == "Republican" & prediction == "Republican")/sum(prediction == "Republican"), recall = sum(winner == "Republican" & prediction == "Republican")/sum(winner == "Republican"), error = 1-accuracy)
   accuracy precision    recall      error
1 0.9144235 0.9341263 0.9673141 0.08557654
```

K-nn

``` r
> set.seed(7)
> n <- nrow(train)
> 
> xvars <- str_c(names(Train1)[1:11], collapse=",")
> 
> train_index <- sample(1:n, size=round(.8*n))
> 
> trainX <- train1 %>% slice(train_index) %>% select( -winner,-winner16)
> 
> testX <- test1 %>% slice(-train_index) 
> 
> dim(trainX)
[1] 1991   51
> dim(testX)
[1] 123  51
> winner_knn <- knn(trainX,testX,cl = train$winner[train_index],k=10)
```

winner_knn.cv \<- knn.cv(Train1\[,xvars\],cl = Train1$winner,k=10)
