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
significant using a regression analysis. We then removed certain
variables such as Percent living in multi unit housing and percent
living in same house for multiple years. Our reasoning is that if a
house has a high median value, the occupants will probably have separate
rooms. Also any person may choose to live in a house for several years
because it might be closer to work, family etc. We don’t think that it
suggests anything about the voter.

percent_change_in_pop, , percent_change_in_pop

``` r
> library(plyr)
> 
> train1 <- train %>% plyr::rename(c("RTN130207"="Retail_Sales_07","INC910213"="Income_per_capita","HSG096213"="Percent_multi_unit_housing","POP715213"="Percent_living_in_same_house_multiple_years","HSG495213"="Median_house_value","POP815213"="Spoken_non_english_lang","EDU635213"="Percent_Highschool_grad","EDU685213"="Percent_Undergrad","AGE295214"="percent_under_18","AGE775214"="percent_over_65","PST120214"="percent_change_in_pop"))
> 
> test1 <- test %>% plyr::rename(c("RTN130207"="Retail_Sales_07","INC910213"="Income_per_capita","HSG096213"="Percent_multi_unit_housing","HSG495213"="Median_house_value","POP815213"="Spoken_non_english_lang","EDU635213"="Percent_Highschool_grad","POP715213"="Percent_living_in_same_house_multiple_years","EDU685213"="Percent_Undergrad","AGE295214"="percent_under_18","AGE775214"="percent_over_65","PST120214"="percent_change_in_pop")) 
> 
> Train1 <- train1 %>% select(Retail_Sales_07, Income_per_capita, Median_house_value,Percent_multi_unit_housing, Spoken_non_english_lang, Percent_Undergrad,  Percent_living_in_same_house_multiple_years, percent_over_65, winner)
> 
> Test1 <- test1 %>% select(Retail_Sales_07, Income_per_capita, Median_house_value, Spoken_non_english_lang, Percent_multi_unit_housing,Percent_living_in_same_house_multiple_years, Percent_Undergrad, percent_over_65)
> 
> xvars <- str_c(names(Train1)[1:8], collapse="+")
> myform <- as.formula(str_c("winner ~ ", xvars))
> 
> winner.glm <- glm(myform, data = Train1, family = binomial)
> 
> summary(winner.glm)

Call:
glm(formula = myform, family = binomial, data = Train1)

Deviance Residuals: 
    Min       1Q   Median       3Q      Max  
-3.1047   0.1350   0.2449   0.3853   3.3755  

Coefficients:
                                              Estimate Std. Error z value
(Intercept)                                  1.399e+01  1.907e+00   7.335
Retail_Sales_07                             -1.057e-07  2.928e-08  -3.611
Income_per_capita                            3.677e-04  3.074e-05  11.960
Median_house_value                          -1.309e-05  1.735e-06  -7.549
Percent_multi_unit_housing                  -1.132e-01  1.292e-02  -8.760
Spoken_non_english_lang                     -3.838e-02  6.343e-03  -6.051
Percent_Undergrad                           -1.608e-01  1.665e-02  -9.655
Percent_living_in_same_house_multiple_years -1.610e-01  2.208e-02  -7.291
percent_over_65                              3.415e-02  2.085e-02   1.638
                                            Pr(>|z|)    
(Intercept)                                 2.22e-13 ***
Retail_Sales_07                             0.000305 ***
Income_per_capita                            < 2e-16 ***
Median_house_value                          4.40e-14 ***
Percent_multi_unit_housing                   < 2e-16 ***
Spoken_non_english_lang                     1.44e-09 ***
Percent_Undergrad                            < 2e-16 ***
Percent_living_in_same_house_multiple_years 3.08e-13 ***
percent_over_65                             0.101423    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 2120.3  on 2488  degrees of freedom
Residual deviance: 1229.9  on 2480  degrees of freedom
AIC: 1247.9

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
1 0.870229 0.8685078 0.9981052
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
> train_error <- cv.glm(data = Train1, winner.glm,cost,5)
> train_error$delta[1]
[1] 0.129771
```

The error is 0.13

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
1 0.9063881 0.9275956 0.9649455 0.09361189
```

The error is 0.0936 and the accuracy is 0.907

Here, we used the k nearest neighbours model with k = 10. We then
compute the accuracy, precision, recall and error for the model K-nn

``` r
> set.seed(7)
> n <- nrow(train)
> 
> xvars <- str_c(names(Train1)[1:9], collapse=",")
> 
> train_index <- sample(1:n, size=round(.8*n))
> 
> trainX <- Train1 %>% slice(train_index) %>% select( -winner)
> 
> testX <- Test1 %>% slice(-train_index) 
> 
> dim(trainX)
[1] 1991    8
> dim(testX)
[1] 123   8
> winner_knn <- knn(trainX,testX,cl = train$winner[train_index],k=10)
> 
> winner_knn.cv <- knn.cv(Train1[,c("Retail_Sales_07", "Income_per_capita", "Median_house_value","Percent_multi_unit_housing", "Spoken_non_english_lang", "Percent_Undergrad",  "Percent_living_in_same_house_multiple_years", "percent_over_65")],cl = Train1$winner,k=10)
> 
> train_knn <- data_frame(y = Train1$winner, prediction = winner_knn.cv) 
> 
> train_knn %>% summarize(accuracy = mean(y == prediction), precision = sum(y == "Republican" & prediction == "Republican")/sum(prediction == "Republican"),recall = sum(y == "Republican" & prediction == "Republican")/sum(y == "Republican"), error = 1 - accuracy)
   accuracy precision    recall     error
1 0.8682202  0.882126 0.9748934 0.1317798
```

Error is 0.131 and accuracy is 0.868
