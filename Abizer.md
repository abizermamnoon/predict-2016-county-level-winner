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
```

``` r
> train <- train %>% mutate(winner = recode_factor(winner16, Dem = "Democratic", Rep = "Republican"))
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
> summary(winner.glm)

Call:
glm(formula = myform, family = binomial, data = train)

Deviance Residuals: 
    Min       1Q   Median       3Q      Max  
-3.2052   0.0346   0.0788   0.2088   3.2891  

Coefficients:
              Estimate Std. Error z value Pr(>|z|)    
(Intercept)  1.513e+01  1.383e+02   0.109 0.912861    
PST045214   -2.543e-05  2.295e-05  -1.108 0.267880    
PST040210   -1.031e-04  8.880e-04  -0.116 0.907591    
PST120214    1.148e-01  4.323e-02   2.656 0.007898 ** 
POP010210    1.294e-04  8.864e-04   0.146 0.883942    
AGE135214   -1.809e-01  2.273e-01  -0.796 0.426018    
AGE295214    2.964e-01  9.522e-02   3.113 0.001854 ** 
AGE775214    2.371e-01  5.748e-02   4.124 3.72e-05 ***
SEX255214   -8.858e-02  6.091e-02  -1.454 0.145877    
RHI125214   -1.436e-01  1.388e+00  -0.103 0.917587    
RHI225214   -1.176e-01  1.382e+00  -0.085 0.932149    
RHI325214   -1.241e-01  1.381e+00  -0.090 0.928403    
RHI425214   -1.486e-01  1.380e+00  -0.108 0.914288    
RHI525214   -2.802e-01  1.476e+00  -0.190 0.849402    
RHI625214   -6.328e-02  1.392e+00  -0.045 0.963727    
RHI725214    7.897e-02  1.505e-01   0.525 0.599840    
RHI825214    1.544e-01  1.650e-01   0.936 0.349357    
POP715213   -1.193e-01  3.585e-02  -3.328 0.000874 ***
POP645213    3.114e-02  4.757e-02   0.655 0.512715    
POP815213   -9.393e-02  3.287e-02  -2.858 0.004269 ** 
EDU635213   -1.246e-01  3.887e-02  -3.206 0.001348 ** 
EDU685213   -1.071e-01  3.098e-02  -3.458 0.000545 ***
VET605213    5.160e-05  3.486e-05   1.480 0.138767    
LFE305213   -2.738e-02  3.154e-02  -0.868 0.385260    
HSG010214    2.601e-05  1.887e-05   1.378 0.168065    
HSG445213   -2.419e-02  2.905e-02  -0.833 0.404934    
HSG096213   -5.629e-02  2.436e-02  -2.310 0.020870 *  
HSG495213   -1.834e-05  3.266e-06  -5.617 1.95e-08 ***
HSD410213   -5.104e-05  3.210e-05  -1.590 0.111860    
HSD310213    3.255e+00  1.046e+00   3.111 0.001863 ** 
INC910213    1.454e-04  7.198e-05   2.020 0.043344 *  
INC110213    5.421e-05  3.439e-05   1.576 0.114962    
PVY020213    1.909e-03  3.660e-02   0.052 0.958416    
BZA010213    4.938e-05  2.444e-04   0.202 0.839907    
BZA110213    6.572e-07  8.910e-06   0.074 0.941203    
BZA115213   -6.175e-03  1.915e-02  -0.322 0.747132    
NES010213    1.633e-04  9.637e-05   1.694 0.090196 .  
SBO001207   -1.535e-04  1.170e-04  -1.312 0.189477    
SBO315207   -1.695e-02  1.501e-02  -1.129 0.258870    
SBO115207   -1.597e-02  3.592e-02  -0.445 0.656655    
SBO215207   -5.215e-02  9.353e-02  -0.558 0.577119    
SBO515207   -2.185e+00  2.905e+00  -0.752 0.451924    
SBO415207   -3.656e-02  1.944e-02  -1.881 0.060036 .  
SBO015207   -9.850e-03  1.068e-02  -0.922 0.356492    
MAN450207   -1.690e-08  3.531e-08  -0.479 0.632118    
WTN220207   -2.509e-08  4.180e-08  -0.600 0.548381    
RTN130207    3.493e-07  1.740e-07   2.007 0.044782 *  
RTN131207   -2.952e-05  2.390e-05  -1.235 0.216778    
AFN120207   -1.367e-06  7.475e-07  -1.829 0.067364 .  
BPS030214    4.376e-04  2.388e-04   1.833 0.066844 .  
LND110210    2.073e-04  8.507e-05   2.437 0.014811 *  
POP060210    3.339e-04  2.632e-04   1.268 0.204640    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 2120.31  on 2488  degrees of freedom
Residual deviance:  742.44  on 2437  degrees of freedom
AIC: 846.44

Number of Fisher Scoring iterations: 8
```

Significant variables = LND110210 0.014811 *, AFN120207 0.067364 .  
BPS030214 0.066844 .  
RTN130207 0.044782 * SBO415207 0.060036 . NES010213 0.090196 . HSD310213
0.001863 \*\* INC910213 0.043344 \*  
HSG096213 0.020870 \*  
HSG495213 1.95e-08 *** POP815213 0.004269 ** EDU635213 0.001348 **
EDU685213 0.000545 *** POP715213 0.000874 *** AGE295214 0.001854 **
AGE775214 3.72e-05 *** PST120214 0.007898 **
