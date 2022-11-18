Team Project 2
================
Abizer and Luis
Day 17

In this project, we used the training dataset to create a model that
predicts the county- level winner of the 2016 Presidential Election. The
winner of each county is classified as Democrat (Clinton) and Republican
(Rep)

This code chunk ensures that there are no commands in output file.

Download the packages

Read the key file

Create the train and test data frames

We were concerned that there would be NA values in the “winner16”
variable. To remove the NAs, we created a new variable called “winner”
that omits the NA values. “Winner” variable is classified as Democrat if
the actual winner is “Dem” and Republican if the actual winner is “Rep”.

The fun part was deciding the variables we wanted to use in order to
create our model. We ran a regression analysis with the 51 predictor
variables and deciphered that 17 of them were significant. However, we
wanted to avoid overfitting hence we ran a regression analysis with
these 17 variables against the winner variable and chose only those
variables that had a significance level less than 0.0001. We were also
concerned that some of these variables might be correlated to each other
which might be affecting their significance level. To examine this, we
ran a individual regression analysis for each variable against the
winner variable and chose the ones that were still significant. After
doing this procedure, we concluded that retail sales, income/capita,
median house value, percentage of population living in multi unit
housing, languages other English spoken at home, percentage of
population who have completed undergraduate degree or higher, percentage
of population living in same housing for multiple years and percentage
change in population are significant.


    Call:
    glm(formula = myform, family = binomial, data = Train1)

    Deviance Residuals: 
        Min       1Q   Median       3Q      Max  
    -3.0933   0.1303   0.2326   0.3772   3.5282  

    Coefficients:
                                                  Estimate Std. Error z value
    (Intercept)                                  1.222e+01  1.898e+00   6.437
    Retail_Sales_07                             -1.269e-07  3.098e-08  -4.096
    Income_per_capita                            3.667e-04  3.104e-05  11.816
    Median_house_value                          -1.412e-05  1.785e-06  -7.914
    Percent_multi_unit_housing                  -1.205e-01  1.284e-02  -9.380
    Spoken_non_english_lang                     -4.910e-02  6.285e-03  -7.813
    Percent_Undergrad                           -1.831e-01  1.736e-02 -10.543
    Percent_living_in_same_house_multiple_years -1.243e-01  2.182e-02  -5.694
    percent_change_in_pop                        1.713e-01  2.512e-02   6.818
                                                Pr(>|z|)    
    (Intercept)                                 1.22e-10 ***
    Retail_Sales_07                             4.21e-05 ***
    Income_per_capita                            < 2e-16 ***
    Median_house_value                          2.50e-15 ***
    Percent_multi_unit_housing                   < 2e-16 ***
    Spoken_non_english_lang                     5.58e-15 ***
    Percent_Undergrad                            < 2e-16 ***
    Percent_living_in_same_house_multiple_years 1.24e-08 ***
    percent_change_in_pop                       9.23e-12 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    (Dispersion parameter for binomial family taken to be 1)

        Null deviance: 2120.3  on 2488  degrees of freedom
    Residual deviance: 1183.0  on 2480  degrees of freedom
    AIC: 1201

    Number of Fisher Scoring iterations: 6

We then fit the model “winner.glm” into the train and test data to
create probabilities. We then set a threshold of 0.05. For example, if
the model determines the probability to be 0.1, prediction will be
Republican or else Democrat. We then calculated the accuracy, precision
and recall for the model. Then, we created a double density curve

       accuracy precision    recall
    1 0.8750502 0.8725166 0.9985789

![](Abizer_files/figure-gfm/unnamed-chunk-5-1.png)<!-- --> The double
density curve indicates that our model does well at differentiating
between the winner being a Democrat and a Republican because there is
little overlap between the curves. However, the double density curves
are not evenly spread out on the graph, which seems troubling.

The accuracy for the logistic regression model is 0.875

Here, we created a ROC curve for the training data

    Warning: `data_frame()` was deprecated in tibble 1.1.0.
    Please use `tibble()` instead.
    This warning is displayed once every 8 hours.
    Call `lifecycle::last_lifecycle_warnings()` to see where this warning was generated.

![](Abizer_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

The ROC curve also suggests that our model is very good because it is
close to a right angled triangle and is much better than a random
classifier.

I calculated the error of the model using a 5-fold cross validation and
a 0.05 threshold

    [1] 0.1257533

The error is 0.126

Random Forest

We then created a random forest model called winner_rforest with 400
trees that randomly select out of 4 variables at each split. To choose
the number of bootstrapped trees and the number of predictors to
randomly select, we tried numbers around 400 and 4 respectively. We
noticed that the accuracy goes down if we input more/less predictors or
more/less trees hence we decided to stick with 400 trees and 4
predictors to randomly select. Then we fitted the model to the training
data, and calculated the accuracy, precision, recall and error for the
training data predictions.

      accuracy precision    recall      error
    1 0.910004 0.9302326 0.9663667 0.08999598

Our accuracy was 0.91 and error was 0.09.

Finally, we then used the k nearest neighbours model with k = 50 and
called it winner_knn. We also checked the accuracy with higher/lower k
and we noticed that k=50 is optimal. We then computed the accuracy,
precision, recall and error for the model of train_knn.

    [1] 1991    8
    [1] 123   8
       accuracy precision    recall     error
    1 0.8734431 0.8811545 0.9834202 0.1265569

We got an accuracy of 0.873 and an error of 0.127.

After running the three models, it was clear that the random forest
model gave us the highest accuracy of 0.91 and lowest error of 0.09 so
we went ahead with that model for our predictions.
