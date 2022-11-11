Team Project 2
================
Abizer and Luis
Day 17

    winner ~ RTN130207 + INC910213 + HSG096213 + HSG495213 + POP815213 + 
        EDU635213 + EDU685213 + POP715213 + AGE295214 + AGE775214 + 
        PST120214


    Call:
    glm(formula = myform, family = binomial, data = train)

    Deviance Residuals: 
        Min       1Q   Median       3Q      Max  
    -3.2543   0.1038   0.2097   0.3633   3.5090  

    Coefficients:
                  Estimate Std. Error z value Pr(>|z|)    
    (Intercept)  3.677e+00  2.400e+00   1.532 0.125438    
    RTN130207   -1.120e-07  3.028e-08  -3.699 0.000216 ***
    INC910213    3.043e-04  3.339e-05   9.114  < 2e-16 ***
    HSG096213   -9.933e-02  1.355e-02  -7.329 2.32e-13 ***
    HSG495213   -1.336e-05  1.917e-06  -6.971 3.16e-12 ***
    POP815213   -4.140e-02  7.666e-03  -5.400 6.66e-08 ***
    EDU635213    6.898e-02  1.896e-02   3.638 0.000275 ***
    EDU685213   -1.938e-01  1.920e-02 -10.091  < 2e-16 ***
    POP715213   -1.490e-01  2.348e-02  -6.347 2.20e-10 ***
    AGE295214    1.413e-01  3.317e-02   4.260 2.04e-05 ***
    AGE775214    1.709e-01  3.277e-02   5.215 1.84e-07 ***
    PST120214    1.831e-01  2.849e-02   6.426 1.31e-10 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    (Dispersion parameter for binomial family taken to be 1)

        Null deviance: 2120.3  on 2488  degrees of freedom
    Residual deviance: 1126.6  on 2477  degrees of freedom
    AIC: 1150.6

    Number of Fisher Scoring iterations: 6
    # A tibble: 1 × 3
      accuracy precision recall
         <dbl>     <dbl>  <dbl>
    1    0.878     0.876  0.998

![](Abizer_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

    Warning: `data_frame()` was deprecated in tibble 1.1.0.
    Please use `tibble()` instead.
    This warning is displayed once every 8 hours.
    Call `lifecycle::last_lifecycle_warnings()` to see where this warning was generated.

![](Abizer_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

    [1] 0.1221374

# K-nn

train_knn \<- knn(train2\[, -1\], test2, train2$winner\[train_index\],
k=10)

train4 \<- data_frame( y=train2\[-train_index\],prediction = train_knn)
%\>% summarize(accuracy = mean(prediction == y), precision =
sum(prediction == “Default” & y == “Default”)/sum(prediction ==
“Default”), recall = sum(prediction == “Default” & y == “Default”)/sum(y
== “Default”), ) \`\`\`
