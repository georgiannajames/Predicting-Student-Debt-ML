Predicting Student Debt
================
Georgianna James
2022-03-02

## Required Packages

``` r
library(rcfss)
library(tidyverse)
library(tidymodels)
library(rsample)
library(knitr)

theme_set(theme_minimal())
set.seed(100)
```

## Load the data

``` r
rcfss::scorecard
```

    ## # A tibble: 1,732 × 14
    ##    unitid name        state type  admrate satavg  cost netcost avgfacsal pctpell
    ##     <dbl> <chr>       <chr> <fct>   <dbl>  <dbl> <dbl>   <dbl>     <dbl>   <dbl>
    ##  1 100654 Alabama A … AL    Publ…   0.918    939 23053   14990     69381   0.702
    ##  2 100663 University… AL    Publ…   0.737   1234 24495   16953     99441   0.351
    ##  3 100706 University… AL    Publ…   0.826   1319 23917   15860     87192   0.254
    ##  4 100724 Alabama St… AL    Publ…   0.969    946 21866   13650     64989   0.763
    ##  5 100751 The Univer… AL    Publ…   0.827   1261 29872   22597     92619   0.177
    ##  6 100830 Auburn Uni… AL    Publ…   0.904   1082 19849   13987     71343   0.464
    ##  7 100858 Auburn Uni… AL    Publ…   0.807   1300 31590   24104     96642   0.146
    ##  8 100937 Birmingham… AL    Priv…   0.538   1230 32095   22107     56646   0.236
    ##  9 101189 Faulkner U… AL    Priv…   0.783   1066 34317   20715     54009   0.488
    ## 10 101365 Herzing Un… AL    Priv…   0.783     NA 30119   26680     54684   0.706
    ## # … with 1,722 more rows, and 4 more variables: comprate <dbl>, firstgen <dbl>,
    ## #   debt <dbl>, locale <fct>

### Creating traning and testing sets

``` r
# split data into train and test

scorecard_split <- initial_split(scorecard, prop = 3 / 4)
scorecard_train <- training(scorecard_split)
scorecard_test <- testing(scorecard_split)
```

# Linear Regression Model

``` r
# build linear regression model

lm_mod <- linear_reg() %>%
  set_engine(engine = "lm") %>%
  set_mode(mode = "regression")


lm_mod
```

    ## Linear Regression Model Specification (regression)
    ## 
    ## Computational engine: lm

``` r
# fit the model

lm_fit <- lm_mod %>%
  fit(debt ~ type + admrate + satavg + cost + netcost + avgfacsal + pctpell + comprate + firstgen + locale, data = scorecard) %>%
  predict(new_data = scorecard_test) %>%
  mutate(true_scorecard = scorecard_test$debt)
```

## Linear Regression Metrics Table

| Metric | Estimator | Estimate |
|:-------|:----------|---------:|
| rmse   | standard  | 3062.022 |

Using a linear model to predict student debt, we get a root mean square
error of 3062.022.

# Linear Regression Model with 10-fold Cross Validation

``` r
# creating folds

scorecard_fold <- vfold_cv(data = scorecard_train, v = 10)

scorecard_fold
```

    ## #  10-fold cross-validation 
    ## # A tibble: 10 × 2
    ##    splits             id    
    ##    <list>             <chr> 
    ##  1 <split [1169/130]> Fold01
    ##  2 <split [1169/130]> Fold02
    ##  3 <split [1169/130]> Fold03
    ##  4 <split [1169/130]> Fold04
    ##  5 <split [1169/130]> Fold05
    ##  6 <split [1169/130]> Fold06
    ##  7 <split [1169/130]> Fold07
    ##  8 <split [1169/130]> Fold08
    ##  9 <split [1169/130]> Fold09
    ## 10 <split [1170/129]> Fold10

``` r
# refitting model to samples and collecting metrics

scorecard_fold_pred <- lm_mod %>%
  fit_resamples(debt ~ type + admrate + satavg + cost + netcost + avgfacsal + pctpell + comprate + firstgen + locale, resamples = scorecard_fold) %>%
  collect_metrics()
```

## Linear Regression Model with 10-fold Cross Validation Metrics Table

``` r
kable(scorecard_fold_pred, col.names = c("Metric", "Estimator", "Mean", "n", "Standard Error", "Config"))
```

| Metric | Estimator |         Mean |   n | Standard Error | Config               |
|:-------|:----------|-------------:|----:|---------------:|:---------------------|
| rmse   | standard  | 3114.5411686 |  10 |     99.0221012 | Preprocessor1_Model1 |
| rsq    | standard  |    0.3680719 |  10 |      0.0305399 | Preprocessor1_Model1 |

Predicting student debt using a 10-fold cross validation, you get an
rmse of 3110.07.

# Decicion Tree Model

``` r
tree_mod <- decision_tree() %>%
  set_engine("rpart") %>%
  set_mode("regression")
```

## Decision Tree Metrics Table

``` r
tree_pred <- tree_mod %>%
  fit_resamples(debt ~ type + admrate + satavg + cost + netcost + avgfacsal + pctpell + comprate + firstgen + locale, resamples = scorecard_fold) %>%
  collect_metrics()



kable(tree_pred, col.names = c("Metric", "Estimator", "Mean", "n", "Standard Error", "Config"))
```

| Metric | Estimator |         Mean |   n | Standard Error | Config               |
|:-------|:----------|-------------:|----:|---------------:|:---------------------|
| rmse   | standard  | 3466.5848890 |  10 |    113.2591329 | Preprocessor1_Model1 |
| rsq    | standard  |    0.3605036 |  10 |      0.0203144 | Preprocessor1_Model1 |

Predicting student debt using a decision tree model, you get an rmse of
3476.96

# Conclusion

In this file, I have predicted student debt using three different
machine learning models, reporting my results in root mean standard
error, which is a measure of how concentrated the predictions are around
the line of best fit. The models resulted in the following results:

-   Linear regression model: `rmse` = 3062.022
-   Linear regression model with 10-fold cross validation: `rmse` =
    3110.07
-   Decision tree model: `rmse` = 3476.96

The basic linear regression model resulted in the lowest `rmse` value of
3062.02
