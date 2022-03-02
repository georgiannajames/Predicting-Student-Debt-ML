# Predicting-Student-Debt-ML

Author: Georgianna James

This repo was originally created to complete a homework assignment for the course [MACS 30500](https://cfss.uchicago.edu). See detailed instructions for this homework assignment [here](https://cfss.uchicago.edu/homework/machine-learning/#fn:View-the-documen).

## Required packages



```r
library(rcfss)
library(tidyverse)
library(tidymodels)
library(rsample)
library(knitr)
library(ranger)
library(kknn)
library(glmnet)

```

[`rcfss`](https://github.com/uc-cfss/rcfss) can be installed from GitHub using the command:

```r
if (packageVersion("devtools") < 1.6) {
  install.packages("devtools")
}

devtools::install_github("uc-cfss/rcfss")
```

##  Summary

Median student debt has increased substaintially overtime. In this repo, I create several machine learning models that predict ```debt``` as a function of all the other variables in the  ```rcfss::scorecard``` data set, which reports the median debt of students after leaving school in 2019 along with a variety of other related variables. I measured the performance of each model using root mean standard error, which is a measure of how concentrated the predictions are around the line of best fit.

* [Student Debt R Markdown file](./student_debt.Rmd)
* [Student Debt Markdown file](./student_debt.md)