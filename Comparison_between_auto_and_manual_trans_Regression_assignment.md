Introduction: what is the goal of the project

    knitr::opts_chunk$set(echo = TRUE)

Explornary data of the mtcars of mpg ~ am
-----------------------------------------

According to the plot (Appendix 1), the mpg between these two am mode
(auto and manual) is quite different without considering the other
variables. The more quantitative modeling will be given below.

Linear modeling
---------------

    simfit <- lm(mpg~factor(am), data = mtcars)
    simCoef <- coef(summary(simfit))
    simsqu <- summary(simfit)$r.squared

According to the simple lm modeling, the R-squared value which is 0.36
is quite low, although the difference between auto and manual is quite
significant (p-value &lt; 0.05). We need multivariable linear fit.

Multivariable linear fit
------------------------

In order to select the variables that optimize the linear fit, we apply
the step() function to implement this purpose.

    mtcars$am <- as.factor(mtcars$am)
    mtcars$cyl <- as.factor(mtcars$cyl)
    mtcars$vs <- as.factor(mtcars$vs)
    mtcars$gear <- as.factor(mtcars$gear)
    mtcars$carb <- as.factor(mtcars$carb)

Here is the methods we used to optimize: please check out the Appendex 3
for the result.

    inifit <- lm(mpg~., data = mtcars)
    fit <- step(inifit,direction = "both")

According to the multivariated modeling, the mpg estimate of auto is
33.71 compared to the of manual 35.52. The p-value of difference is 0.21
which is larger than 0.05. Based on this modelling, we would say that
there is not much significant difference on the mpg between the auto and
manual transmission by eliminating the other variables (such as
cylindar, displacement, drat, weight and so on). The R-squared value is
0.87 which is better than before. More diagnostics, as follow, need to
be done to confirm the certainty of this modeling outcome.

According to the diagnostic plot (Appendix 2), we can see that the
points in residual vs fitted value plot distributed well, indicating
that the model fits the data well.

    anova(simfit,fit)

    ## Analysis of Variance Table
    ## 
    ## Model 1: mpg ~ factor(am)
    ## Model 2: mpg ~ cyl + hp + wt + am
    ##   Res.Df    RSS Df Sum of Sq      F    Pr(>F)    
    ## 1     30 720.90                                  
    ## 2     26 151.03  4    569.87 24.527 1.688e-08 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

According to the comparison between the simple variable and
multivariable linear fit, we can see that differences between are quite
significant by checking out the p-value 1.68e-08.

Appendix 1
----------

    g <- ggplot(data = mtcars, aes(x= factor(am),y= mpg, fill=factor(am)))
    g <- g + geom_violin(colour="black", size =2)
    g <- g + xlab("Type of transimission") + ylab("Miles per gallon")
    g <- g + scale_fill_discrete(name="Type of transmissions",
                               breaks = c(0,1),
                               labels=c("automatic","manual"))
    g <- g + ggtitle("Explornary of mpg versus am in 'mtcars' dataset")
    g

![](Comparison_between_auto_and_manual_trans_Regression_assignment_files/figure-markdown_strict/unnamed-chunk-5-1.png)

Appendix 2
----------

    par(mfrow=c(2,2))
    plot(fit)

![](Comparison_between_auto_and_manual_trans_Regression_assignment_files/figure-markdown_strict/unnamed-chunk-6-1.png)

Apendix 3
---------

    inifit <- lm(mpg~., data = mtcars)
    fit <- step(inifit,direction = "both")

    ## Start:  AIC=76.4
    ## mpg ~ cyl + disp + hp + drat + wt + qsec + vs + am + gear + carb
    ## 
    ##        Df Sum of Sq    RSS    AIC
    ## - carb  5   13.5989 134.00 69.828
    ## - gear  2    3.9729 124.38 73.442
    ## - am    1    1.1420 121.55 74.705
    ## - qsec  1    1.2413 121.64 74.732
    ## - drat  1    1.8208 122.22 74.884
    ## - cyl   2   10.9314 131.33 75.184
    ## - vs    1    3.6299 124.03 75.354
    ## <none>              120.40 76.403
    ## - disp  1    9.9672 130.37 76.948
    ## - wt    1   25.5541 145.96 80.562
    ## - hp    1   25.6715 146.07 80.588
    ## 
    ## Step:  AIC=69.83
    ## mpg ~ cyl + disp + hp + drat + wt + qsec + vs + am + gear
    ## 
    ##        Df Sum of Sq    RSS    AIC
    ## - gear  2    5.0215 139.02 67.005
    ## - disp  1    0.9934 135.00 68.064
    ## - drat  1    1.1854 135.19 68.110
    ## - vs    1    3.6763 137.68 68.694
    ## - cyl   2   12.5642 146.57 68.696
    ## - qsec  1    5.2634 139.26 69.061
    ## <none>              134.00 69.828
    ## - am    1   11.9255 145.93 70.556
    ## - wt    1   19.7963 153.80 72.237
    ## - hp    1   22.7935 156.79 72.855
    ## + carb  5   13.5989 120.40 76.403
    ## 
    ## Step:  AIC=67
    ## mpg ~ cyl + disp + hp + drat + wt + qsec + vs + am
    ## 
    ##        Df Sum of Sq    RSS    AIC
    ## - drat  1    0.9672 139.99 65.227
    ## - cyl   2   10.4247 149.45 65.319
    ## - disp  1    1.5483 140.57 65.359
    ## - vs    1    2.1829 141.21 65.503
    ## - qsec  1    3.6324 142.66 65.830
    ## <none>              139.02 67.005
    ## - am    1   16.5665 155.59 68.608
    ## - hp    1   18.1768 157.20 68.937
    ## + gear  2    5.0215 134.00 69.828
    ## - wt    1   31.1896 170.21 71.482
    ## + carb  5   14.6475 124.38 73.442
    ## 
    ## Step:  AIC=65.23
    ## mpg ~ cyl + disp + hp + wt + qsec + vs + am
    ## 
    ##        Df Sum of Sq    RSS    AIC
    ## - disp  1    1.2474 141.24 63.511
    ## - vs    1    2.3403 142.33 63.757
    ## - cyl   2   12.3267 152.32 63.927
    ## - qsec  1    3.1000 143.09 63.928
    ## <none>              139.99 65.227
    ## + drat  1    0.9672 139.02 67.005
    ## - hp    1   17.7382 157.73 67.044
    ## - am    1   19.4660 159.46 67.393
    ## + gear  2    4.8033 135.19 68.110
    ## - wt    1   30.7151 170.71 69.574
    ## + carb  5   13.0509 126.94 72.095
    ## 
    ## Step:  AIC=63.51
    ## mpg ~ cyl + hp + wt + qsec + vs + am
    ## 
    ##        Df Sum of Sq    RSS    AIC
    ## - qsec  1     2.442 143.68 62.059
    ## - vs    1     2.744 143.98 62.126
    ## - cyl   2    18.580 159.82 63.466
    ## <none>              141.24 63.511
    ## + disp  1     1.247 139.99 65.227
    ## + drat  1     0.666 140.57 65.359
    ## - hp    1    18.184 159.42 65.386
    ## - am    1    18.885 160.12 65.527
    ## + gear  2     4.684 136.55 66.431
    ## - wt    1    39.645 180.88 69.428
    ## + carb  5     2.331 138.91 72.978
    ## 
    ## Step:  AIC=62.06
    ## mpg ~ cyl + hp + wt + vs + am
    ## 
    ##        Df Sum of Sq    RSS    AIC
    ## - vs    1     7.346 151.03 61.655
    ## <none>              143.68 62.059
    ## - cyl   2    25.284 168.96 63.246
    ## + qsec  1     2.442 141.24 63.511
    ## - am    1    16.443 160.12 63.527
    ## + disp  1     0.589 143.09 63.928
    ## + drat  1     0.330 143.35 63.986
    ## + gear  2     3.437 140.24 65.284
    ## - hp    1    36.344 180.02 67.275
    ## - wt    1    41.088 184.77 68.108
    ## + carb  5     3.480 140.20 71.275
    ## 
    ## Step:  AIC=61.65
    ## mpg ~ cyl + hp + wt + am
    ## 
    ##        Df Sum of Sq    RSS    AIC
    ## <none>              151.03 61.655
    ## - am    1     9.752 160.78 61.657
    ## + vs    1     7.346 143.68 62.059
    ## + qsec  1     7.044 143.98 62.126
    ## - cyl   2    29.265 180.29 63.323
    ## + disp  1     0.617 150.41 63.524
    ## + drat  1     0.220 150.81 63.608
    ## + gear  2     1.361 149.66 65.365
    ## - hp    1    31.943 182.97 65.794
    ## - wt    1    46.173 197.20 68.191
    ## + carb  5     5.633 145.39 70.438
