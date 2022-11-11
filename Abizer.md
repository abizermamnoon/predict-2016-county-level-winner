Team Project 2
================
Abizer and Luis
Day 17

``` r
> key <- read.csv("https://raw.githubusercontent.com/mgelman/data/master/county_facts_dictionary.csv")
> key
   column_name
1    PST045214
2    PST040210
3    PST120214
4    POP010210
5    AGE135214
6    AGE295214
7    AGE775214
8    SEX255214
9    RHI125214
10   RHI225214
11   RHI325214
12   RHI425214
13   RHI525214
14   RHI625214
15   RHI725214
16   RHI825214
17   POP715213
18   POP645213
19   POP815213
20   EDU635213
21   EDU685213
22   VET605213
23   LFE305213
24   HSG010214
25   HSG445213
26   HSG096213
27   HSG495213
28   HSD410213
29   HSD310213
30   INC910213
31   INC110213
32   PVY020213
33   BZA010213
34   BZA110213
35   BZA115213
36   NES010213
37   SBO001207
38   SBO315207
39   SBO115207
40   SBO215207
41   SBO515207
42   SBO415207
43   SBO015207
44   MAN450207
45   WTN220207
46   RTN130207
47   RTN131207
48   AFN120207
49   BPS030214
50   LND110210
51   POP060210
                                                              description
1                                               Population, 2014 estimate
2                               Population, 2010 (April 1) estimates base
3              Population, percent change - April 1, 2010 to July 1, 2014
4                                                        Population, 2010
5                                    Persons under 5 years, percent, 2014
6                                   Persons under 18 years, percent, 2014
7                                Persons 65 years and over, percent, 2014
8                                           Female persons, percent, 2014
9                                              White alone, percent, 2014
10                         Black or African American alone, percent, 2014
11                 American Indian and Alaska Native alone, percent, 2014
12                                             Asian alone, percent, 2014
13        Native Hawaiian and Other Pacific Islander alone, percent, 2014
14                                       Two or More Races, percent, 2014
15                                      Hispanic or Latino, percent, 2014
16                     White alone, not Hispanic or Latino, percent, 2014
17                 Living in same house 1 year & over, percent, 2009-2013
18                               Foreign born persons, percent, 2009-2013
19      Language other than English spoken at home, pct age 5+, 2009-2013
20  High school graduate or higher, percent of persons age 25+, 2009-2013
21     Bachelor's degree or higher, percent of persons age 25+, 2009-2013
22                                                    Veterans, 2009-2013
23         Mean travel time to work (minutes), workers age 16+, 2009-2013
24                                                    Housing units, 2014
25                                          Homeownership rate, 2009-2013
26             Housing units in multi-unit structures, percent, 2009-2013
27                Median value of owner-occupied housing units, 2009-2013
28                                                  Households, 2009-2013
29                                       Persons per household, 2009-2013
30    Per capita money income in past 12 months (2013 dollars), 2009-2013
31                                     Median household income, 2009-2013
32                        Persons below poverty level, percent, 2009-2013
33                                   Private nonfarm establishments, 2013
34                                      Private nonfarm employment,  2013
35                  Private nonfarm employment, percent change, 2012-2013
36                                       Nonemployer establishments, 2013
37                                            Total number of firms, 2007
38                                       Black-owned firms, percent, 2007
39          American Indian- and Alaska Native-owned firms, percent, 2007
40                                       Asian-owned firms, percent, 2007
41 Native Hawaiian- and Other Pacific Islander-owned firms, percent, 2007
42                                    Hispanic-owned firms, percent, 2007
43                                       Women-owned firms, percent, 2007
44                                 Manufacturers shipments, 2007 ($1,000)
45                               Merchant wholesaler sales, 2007 ($1,000)
46                                            Retail sales, 2007 ($1,000)
47                                          Retail sales per capita, 2007
48                   Accommodation and food services sales, 2007 ($1,000)
49                                                 Building permits, 2014
50                                        Land area in square miles, 2010
51                                       Population per square mile, 2010
```

``` r
> train <- read_csv("https://raw.githubusercontent.com/mgelman/data/master/train.csv")
> test <- read_csv("https://raw.githubusercontent.com/mgelman/data/master/test_No_Y.csv")
```

``` r
> train <- train %>% mutate(winner = recode_factor(winner16, Dem = "Democrat", Rep = "Republican"))
```

``` r
> xvars <- str_c(names(train)[1:51], collapse="+")
> myform <- as.formula(str_c("winner ~ ", xvars))
> myform
winner ~ PST045214 + PST040210 + PST120214 + POP010210 + AGE135214 + 
    AGE295214 + AGE775214 + SEX255214 + RHI125214 + RHI225214 + 
    RHI325214 + RHI425214 + RHI525214 + RHI625214 + RHI725214 + 
    RHI825214 + POP715213 + POP645213 + POP815213 + EDU635213 + 
    EDU685213 + VET605213 + LFE305213 + HSG010214 + HSG445213 + 
    HSG096213 + HSG495213 + HSD410213 + HSD310213 + INC910213 + 
    INC110213 + PVY020213 + BZA010213 + BZA110213 + BZA115213 + 
    NES010213 + SBO001207 + SBO315207 + SBO115207 + SBO215207 + 
    SBO515207 + SBO415207 + SBO015207 + MAN450207 + WTN220207 + 
    RTN130207 + RTN131207 + AFN120207 + BPS030214 + LND110210 + 
    POP060210
```

``` r
> winner.glm <- glm(myform, data = train, family = binomial)
Warning: glm.fit: fitted probabilities numerically 0 or 1 occurred
> 
> train <- train %>% mutate(probs = predict(winner.glm, type = "response"),prediction = ifelse(probs >= 0.5,"Republican","Democrat"))
> 
> ggplot(train,aes(x = probs, color = winner)) + geom_density(size = 1.5) + ggtitle("Forecasted Winner Probabilities ")
```

![](Abizer_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

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
```

Significant variables = LND110210 0.014811 *, AFN120207 0.067364 .  
BPS030214 0.066844 .  
RTN130207 0.044782 * SBO415207 0.060036 . NES010213 0.090196 . HSD310213
0.001863 \*\* INC910213 0.043344 \*  
HSG096213 0.020870 \*  
HSG495213 1.95e-08 *** POP815213 0.004269 ** EDU635213 0.001348 **
EDU685213 0.000545 *** POP715213 0.000874 *** AGE295214 0.001854 **
AGE775214 3.72e-05 *** PST120214 0.007898 **
