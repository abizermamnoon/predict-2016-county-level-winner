Team Project 2
================
Abizer and Luis
Day 17

In this project, we used the training dataset to create a model that
predicts the county- level winner of the 2016 Presidential Election. The
winner of each county is classified as Democrat (Clinton) and Republican
(Rep)

We were concerned that there would be NA values in the “winner16”
variable. To remove the NAs, we created a new variable called “winner”
that omits the NA values. “Winner” variable is classified as Democrat if
the actual winner is “Dem” and Republican if the actual winner is “Rep”.

    [1] Republican Republican Republican Republican Democrat   Republican
    Levels: Democrat Republican

The fun part was deciding the variables we wanted to use in order to
create our model. We ran a regression analysis with the 51 predictor
variables and deciphered that 17 of them were significant. However, we
wanted to avoid overfitting hence we ran a regression analysis with
these 17 variables against the winner variable and chose only those
variables that were significant. We were also concerned that some of
these variables might be correlated to each other which might be
affecting their significance level. To examine this, we ran a individual
regression analysis for each variable against the winner variable and
chose the ones that were still significant. After doing this procedure,
we concluded that retail sales, income/capita, median house value,
percentage of population living in multi unit housing, languages other
English spoken at home, percentage of population who have completed
undergraduate degree or higher, percentage of population living in same
housing for multiple years and percentage change in population,
Persons/Household, Land Area, Non-Employer Establishments, Hispanic
Owned Firms, Accommodation/Food Sales and Building Permits are
significant.

    Warning: glm.fit: fitted probabilities numerically 0 or 1 occurred

    Call:
    glm(formula = myform, family = binomial, data = train_final)

    Deviance Residuals: 
        Min       1Q   Median       3Q      Max  
    -3.1030   0.1233   0.2278   0.3712   3.3573  

    Coefficients:
                                                  Estimate Std. Error z value
    (Intercept)                                  1.252e+01  2.134e+00   5.867
    Retail_Sales_07                              6.890e-08  1.117e-07   0.617
    Income_per_capita                            3.748e-04  3.394e-05  11.042
    Median_house_value                          -1.434e-05  1.946e-06  -7.369
    Percent_multi_unit_housing                  -1.032e-01  1.380e-02  -7.482
    Spoken_non_english_lang                     -3.329e-02  9.749e-03  -3.414
    Percent_Undergrad                           -2.020e-01  1.805e-02 -11.191
    Percent_living_in_same_house_multiple_years -1.113e-01  2.302e-02  -4.837
    percent_change_in_pop                        1.957e-01  2.870e-02   6.821
    Persons_per_Household                       -6.610e-01  4.212e-01  -1.569
    Land_Area                                    2.457e-04  6.419e-05   3.828
    Nonemployer_establishments                  -2.377e-06  2.296e-05  -0.104
    Hispanic_firms                              -5.422e-02  1.551e-02  -3.497
    Accomodation_Food_Sales                     -1.996e-06  6.223e-07  -3.207
    Building_Permits                             2.777e-04  1.219e-04   2.279
                                                Pr(>|z|)    
    (Intercept)                                 4.45e-09 ***
    Retail_Sales_07                             0.537480    
    Income_per_capita                            < 2e-16 ***
    Median_house_value                          1.72e-13 ***
    Percent_multi_unit_housing                  7.33e-14 ***
    Spoken_non_english_lang                     0.000639 ***
    Percent_Undergrad                            < 2e-16 ***
    Percent_living_in_same_house_multiple_years 1.32e-06 ***
    percent_change_in_pop                       9.06e-12 ***
    Persons_per_Household                       0.116558    
    Land_Area                                   0.000129 ***
    Nonemployer_establishments                  0.917516    
    Hispanic_firms                              0.000471 ***
    Accomodation_Food_Sales                     0.001340 ** 
    Building_Permits                            0.022670 *  
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    (Dispersion parameter for binomial family taken to be 1)

        Null deviance: 2120.3  on 2488  degrees of freedom
    Residual deviance: 1142.5  on 2474  degrees of freedom
    AIC: 1172.5

    Number of Fisher Scoring iterations: 7

We then fit the model “winner.glm” into the train and test data to
create probabilities. We then set a threshold of 0.05. For example, if
the model determines the probability to be 0.1, prediction will be
Republican or else Democrat. We then calculated the accuracy, precision
and recall for the model. Then, we created a double density curve

                
                 Democrat Republican
      Democrat         90        288
      Republican        3       2108
       accuracy precision    recall
    1 0.8830856 0.8797997 0.9985789
       accuracy precision    recall
    1 0.8830856 0.9677419 0.2380952

![](Abizer_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

The double density curve indicates that most Republicans have an
estimated probability of winning around 90%. Probability of Democrats
winning is not defined so well but range lower than a Republican

The accuracy for the logistic regression model is 0.883

Here, we created a ROC curve for the training data

    Warning: `data_frame()` was deprecated in tibble 1.1.0.
    Please use `tibble()` instead.
    This warning is displayed once every 8 hours.
    Call `lifecycle::last_lifecycle_warnings()` to see where this warning was generated.

![](Abizer_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

The ROC curve suggests that our model is very good because it is close
to a right angled triangle and has a higher sensitivity than a random
classifier.

I calculated the error of the model using a 5-fold cross validation and
a 0.05 threshold

    Warning: glm.fit: fitted probabilities numerically 0 or 1 occurred

    Warning: glm.fit: fitted probabilities numerically 0 or 1 occurred

    Warning: glm.fit: fitted probabilities numerically 0 or 1 occurred

    Warning: glm.fit: fitted probabilities numerically 0 or 1 occurred

    Warning: glm.fit: fitted probabilities numerically 0 or 1 occurred
    [1] 0.119325

The error is 0.119

Random Forest

We then created a random forest model called winner_rforest with 350
trees that randomly select out of 5 variables at each split. To choose
the number of bootstrapped trees and the number of predictors to
randomly select, we tried numbers around 350 and 5 respectively. We
noticed that the accuracy goes down if we input more/less predictors or
more/less trees hence we decided to stick with 350 trees and 5
predictors to randomly select. Then we fitted the model to the training
data, and calculated the accuracy, precision, recall and error for the
training data predictions.

                
                 Democrat Republican
      Democrat        225        153
      Republican       61       2050
       accuracy precision    recall     error
    1 0.9140217 0.9305493 0.9711037 0.0859783
       accuracy precision    recall
    1 0.9140217 0.7867133 0.5952381

Our accuracy was 0.91 and error was 0.09.

Finally, we then used the k nearest neighbours model with k = 50 and
called it winner_knn. We also checked the accuracy with higher/lower k
and we noticed that k=50 is optimal. We then computed the accuracy,
precision, recall and error for the model of train_knn.

We got an accuracy of 0.873 and an error of 0.127.

After running the three models, it was clear that the random forest
model gave us the highest accuracy of 0.91 and lowest error of 0.09 so
we went ahead with that model for our predictions.
