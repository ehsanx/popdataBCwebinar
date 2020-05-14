---
title: "Webinar: Propensity Score Analysis in Healthcare Data (Part 4: PS weighting - ATT)"
author: "Ehsan Karim, ehsan.karim@ubc.ca"
date: "13 May 2020"
always_allow_html: yes
header-includes:
- \usepackage{float}
- \usepackage{booktabs}
- \floatplacement{figure}{H}
- \usepackage{fancyhdr,textcomp,xcolor}
- \pagestyle{fancy}
- \fancyhead[LE,RO]{\texttt{\tiny{\textcolor{gray}{Do not use, share or revise without explicit written permission from the copyright owner} \\ \textcolor{gray}{Ehsan Karim} \textcolor{red}{\textcopyright} \textcolor{gray}{\the\year} \textcolor{blue}{ ehsan.karim@ubc.ca}}}}
output:
  html_document:
    fig_caption: yes
    keep_md: yes
    toc: yes
    number_sections: true
    toc_depth: 2
    toc_float: 
      collapsed: true
      smooth_scroll: true
    theme: lumen
    highlight: textmate
  pdf_document:
    fig_caption: yes
    latex_engine: xelatex
    number_sections: yes
    toc: yes
    toc_depth: 3
  word_document:
    toc: yes
  slidy_presentation:
    toc: yes  
---



# Read data


```r
analytic.data <- readRDS("data/RHC.Rds")
baselinevars <- c("age","sex", "race","Disease.category", 
                  "Cancer", "DNR.status", "APACHE.III.score",
                  "Pr.2mo.survival","No.of.comorbidity",
                  "ADLs.2wk.prior","DASI.2wk.prior","Temperature",
                  "Heart.rate", "Blood.pressure","Respiratory.rate",
                  "WBC.count","PaO2.by.FIO2","PaCO2","pH",
                  "Creatinine","Albumin","GComa.Score")
```

# Propensity score (PS) analysis

## Table 1 (stratified by exposure)


```r
require(tableone)
tab1 <- CreateTableOne(vars = baselinevars,
               data = analytic.data, strata = "RHC", includeNA = TRUE, 
               test = FALSE, smd = TRUE)
tab1k <- kable(print(tab1, showAllLevels = TRUE, smd = TRUE)) %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", "responsive"), 
                full_width = FALSE)
```


```r
tab1k
```

<table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;"> level </th>
   <th style="text-align:left;"> No RHC </th>
   <th style="text-align:left;"> RHC </th>
   <th style="text-align:left;"> SMD </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> n </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 1049 </td>
   <td style="text-align:left;"> 390 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> age (%) </td>
   <td style="text-align:left;"> [-Inf,50) </td>
   <td style="text-align:left;"> 264 (25.2) </td>
   <td style="text-align:left;"> 113 (29.0) </td>
   <td style="text-align:left;"> 0.288 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [50,60) </td>
   <td style="text-align:left;"> 160 (15.3) </td>
   <td style="text-align:left;"> 85 (21.8) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [60,70) </td>
   <td style="text-align:left;"> 261 (24.9) </td>
   <td style="text-align:left;"> 99 (25.4) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [70,80) </td>
   <td style="text-align:left;"> 238 (22.7) </td>
   <td style="text-align:left;"> 70 (17.9) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [80, Inf) </td>
   <td style="text-align:left;"> 126 (12.0) </td>
   <td style="text-align:left;"> 23 ( 5.9) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sex (%) </td>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:left;"> 569 (54.2) </td>
   <td style="text-align:left;"> 253 (64.9) </td>
   <td style="text-align:left;"> 0.218 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:left;"> 480 (45.8) </td>
   <td style="text-align:left;"> 137 (35.1) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> race (%) </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> 813 (77.5) </td>
   <td style="text-align:left;"> 297 (76.2) </td>
   <td style="text-align:left;"> 0.042 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> black </td>
   <td style="text-align:left;"> 176 (16.8) </td>
   <td style="text-align:left;"> 67 (17.2) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> other </td>
   <td style="text-align:left;"> 60 ( 5.7) </td>
   <td style="text-align:left;"> 26 ( 6.7) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Disease.category (%) </td>
   <td style="text-align:left;"> ARF </td>
   <td style="text-align:left;"> 429 (40.9) </td>
   <td style="text-align:left;"> 127 (32.6) </td>
   <td style="text-align:left;"> 0.682 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> CHF </td>
   <td style="text-align:left;"> 174 (16.6) </td>
   <td style="text-align:left;"> 129 (33.1) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> MOSF </td>
   <td style="text-align:left;"> 180 (17.2) </td>
   <td style="text-align:left;"> 110 (28.2) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Other </td>
   <td style="text-align:left;"> 266 (25.4) </td>
   <td style="text-align:left;"> 24 ( 6.2) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cancer (%) </td>
   <td style="text-align:left;"> None </td>
   <td style="text-align:left;"> 797 (76.0) </td>
   <td style="text-align:left;"> 324 (83.1) </td>
   <td style="text-align:left;"> 0.178 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Localized (Yes) </td>
   <td style="text-align:left;"> 171 (16.3) </td>
   <td style="text-align:left;"> 46 (11.8) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Metastatic </td>
   <td style="text-align:left;"> 81 ( 7.7) </td>
   <td style="text-align:left;"> 20 ( 5.1) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> DNR.status (%) </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> 962 (91.7) </td>
   <td style="text-align:left;"> 379 (97.2) </td>
   <td style="text-align:left;"> 0.241 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> 87 ( 8.3) </td>
   <td style="text-align:left;"> 11 ( 2.8) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> APACHE.III.score (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 48.36 (16.34) </td>
   <td style="text-align:left;"> 49.38 (19.71) </td>
   <td style="text-align:left;"> 0.057 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Pr.2mo.survival (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 0.70 (0.15) </td>
   <td style="text-align:left;"> 0.69 (0.17) </td>
   <td style="text-align:left;"> 0.079 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> No.of.comorbidity (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 1.74 (1.22) </td>
   <td style="text-align:left;"> 1.76 (1.23) </td>
   <td style="text-align:left;"> 0.019 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ADLs.2wk.prior (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 1.24 (1.86) </td>
   <td style="text-align:left;"> 1.02 (1.69) </td>
   <td style="text-align:left;"> 0.129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> DASI.2wk.prior (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 20.36 (7.28) </td>
   <td style="text-align:left;"> 20.36 (6.96) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Temperature (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 37.35 (1.66) </td>
   <td style="text-align:left;"> 37.24 (1.61) </td>
   <td style="text-align:left;"> 0.063 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Heart.rate (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 112.23 (38.20) </td>
   <td style="text-align:left;"> 108.66 (39.22) </td>
   <td style="text-align:left;"> 0.092 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Blood.pressure (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 87.35 (37.97) </td>
   <td style="text-align:left;"> 70.91 (33.38) </td>
   <td style="text-align:left;"> 0.460 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Respiratory.rate (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 30.43 (11.65) </td>
   <td style="text-align:left;"> 25.25 (12.73) </td>
   <td style="text-align:left;"> 0.424 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WBC.count (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 14.45 (11.16) </td>
   <td style="text-align:left;"> 14.75 (13.09) </td>
   <td style="text-align:left;"> 0.024 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> PaO2.by.FIO2 (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 250.90 (112.53) </td>
   <td style="text-align:left;"> 238.90 (104.11) </td>
   <td style="text-align:left;"> 0.111 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> PaCO2 (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 41.77 (14.86) </td>
   <td style="text-align:left;"> 37.16 (8.57) </td>
   <td style="text-align:left;"> 0.380 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pH (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 7.39 (0.10) </td>
   <td style="text-align:left;"> 7.40 (0.09) </td>
   <td style="text-align:left;"> 0.166 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Creatinine (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 2.03 (2.27) </td>
   <td style="text-align:left;"> 2.22 (2.05) </td>
   <td style="text-align:left;"> 0.084 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albumin (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 3.26 (0.65) </td>
   <td style="text-align:left;"> 3.19 (0.64) </td>
   <td style="text-align:left;"> 0.108 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> GComa.Score (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 5.25 (15.83) </td>
   <td style="text-align:left;"> 6.54 (17.20) </td>
   <td style="text-align:left;"> 0.078 </td>
  </tr>
</tbody>
</table>

## PS modelling


```r
ps.formula <- as.formula(I(RHC == "RHC") ~ age+sex+race+Disease.category+ 
                                Cancer+DNR.status+APACHE.III.score+
                                Pr.2mo.survival+No.of.comorbidity+
                                ADLs.2wk.prior+DASI.2wk.prior+Temperature+
                                Heart.rate+Blood.pressure+Respiratory.rate+
                                WBC.count+PaO2.by.FIO2+PaCO2+pH+
                                Creatinine+Albumin+GComa.Score)
# fit logistic regression
PS.fit <- glm(ps.formula,family="binomial", data=analytic.data)
# extract propensity scores
analytic.data$PS <- predict(PS.fit, newdata = analytic.data, type="response")
# summarize propensity scores
summary(analytic.data$PS)
```

```
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
## 0.003351 0.097465 0.233698 0.271022 0.417233 0.941860
```

```r
# summarize propensity scores by exposure group
tapply(analytic.data$PS, analytic.data$RHC, summary)
```

```
## $`No RHC`
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
## 0.003351 0.066781 0.176932 0.214205 0.321125 0.845037 
## 
## $RHC
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
## 0.02451 0.26598 0.43403 0.42384 0.56963 0.94186
```

```r
# plot propensity scores by exposure group
plot(density(analytic.data$PS), col = "white", type = "n", main = "")
lines(density(analytic.data$PS[analytic.data$RHC == "RHC"]), 
     col = "red")
lines(density(analytic.data$PS[analytic.data$RHC == "No RHC"]), 
      col = "blue", lty = 2)
legend("topright", c("RHC","No RHC"), 
       col = c("red", "blue"), lty=1:2)
```

![](wb4_files/figure-html/ps-1.png)<!-- -->

# PS Weighting (ATT)

## Step 1
Specify the propensity score model to estimate propensity scores


```r
ps.formula <- as.formula(I(RHC == "RHC") ~ age+sex+race+Disease.category+ 
                                Cancer+DNR.status+APACHE.III.score+
                                Pr.2mo.survival+No.of.comorbidity+
                                ADLs.2wk.prior+DASI.2wk.prior+Temperature+
                                Heart.rate+Blood.pressure+Respiratory.rate+
                                WBC.count+PaO2.by.FIO2+PaCO2+pH+
                                Creatinine+Albumin+GComa.Score)
```

## Step 2

Convert propensity scores to IPW (ATT formula)


```r
analytic.data$IPW.ATT <- ifelse(analytic.data$RHC=="RHC", 
                            1, 
                            analytic.data$PS/(1-analytic.data$PS))
summary(analytic.data$IPW.ATT)
```

```
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
## 0.003363 0.117339 0.375757 0.536772 1.000000 5.453154
```


```r
tapply(analytic.data$IPW.ATT, 
       analytic.data$RHC, 
       summary)
```

```
## $`No RHC`
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
## 0.003363 0.071560 0.214966 0.364552 0.473026 5.453154 
## 
## $RHC
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       1       1       1       1       1       1
```

## Step 3

Compare the similarity of baseline characteristics between treated and untreated subjects in a the propensity score-weighted sample. In this case, we will compare SMD < 0.1 or not


```r
require(survey)
analytic.design <- svydesign(id=~1,weights=~IPW.ATT, 
                             data=analytic.data, nest=TRUE)
tab1m <- svyCreateTableOne(vars = baselinevars,
               data = analytic.design, strata = "RHC", includeNA = TRUE, 
               test = FALSE, smd = TRUE)
tab1mk <- kable(print(tab1m, showAllLevels = TRUE, smd = TRUE)) %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", "responsive"), 
                full_width = FALSE)
```


```r
tab1mk
```

<table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;"> level </th>
   <th style="text-align:left;"> No RHC </th>
   <th style="text-align:left;"> RHC </th>
   <th style="text-align:left;"> SMD </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> n </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 382.4 </td>
   <td style="text-align:left;"> 390.0 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> age (%) </td>
   <td style="text-align:left;"> [-Inf,50) </td>
   <td style="text-align:left;"> 110.0 (28.8) </td>
   <td style="text-align:left;"> 113.0 (29.0) </td>
   <td style="text-align:left;"> 0.060 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [50,60) </td>
   <td style="text-align:left;"> 74.8 (19.6) </td>
   <td style="text-align:left;"> 85.0 (21.8) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [60,70) </td>
   <td style="text-align:left;"> 102.1 (26.7) </td>
   <td style="text-align:left;"> 99.0 (25.4) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [70,80) </td>
   <td style="text-align:left;"> 72.1 (18.9) </td>
   <td style="text-align:left;"> 70.0 (17.9) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [80, Inf) </td>
   <td style="text-align:left;"> 23.4 ( 6.1) </td>
   <td style="text-align:left;"> 23.0 ( 5.9) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sex (%) </td>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:left;"> 239.5 (62.6) </td>
   <td style="text-align:left;"> 253.0 (64.9) </td>
   <td style="text-align:left;"> 0.047 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:left;"> 142.9 (37.4) </td>
   <td style="text-align:left;"> 137.0 (35.1) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> race (%) </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> 290.6 (76.0) </td>
   <td style="text-align:left;"> 297.0 (76.2) </td>
   <td style="text-align:left;"> 0.022 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> black </td>
   <td style="text-align:left;"> 64.3 (16.8) </td>
   <td style="text-align:left;"> 67.0 (17.2) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> other </td>
   <td style="text-align:left;"> 27.5 ( 7.2) </td>
   <td style="text-align:left;"> 26.0 ( 6.7) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Disease.category (%) </td>
   <td style="text-align:left;"> ARF </td>
   <td style="text-align:left;"> 119.7 (31.3) </td>
   <td style="text-align:left;"> 127.0 (32.6) </td>
   <td style="text-align:left;"> 0.032 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> CHF </td>
   <td style="text-align:left;"> 126.3 (33.0) </td>
   <td style="text-align:left;"> 129.0 (33.1) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> MOSF </td>
   <td style="text-align:left;"> 112.2 (29.3) </td>
   <td style="text-align:left;"> 110.0 (28.2) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Other </td>
   <td style="text-align:left;"> 24.2 ( 6.3) </td>
   <td style="text-align:left;"> 24.0 ( 6.2) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cancer (%) </td>
   <td style="text-align:left;"> None </td>
   <td style="text-align:left;"> 314.4 (82.2) </td>
   <td style="text-align:left;"> 324.0 (83.1) </td>
   <td style="text-align:left;"> 0.030 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Localized (Yes) </td>
   <td style="text-align:left;"> 48.9 (12.8) </td>
   <td style="text-align:left;"> 46.0 (11.8) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Metastatic </td>
   <td style="text-align:left;"> 19.1 ( 5.0) </td>
   <td style="text-align:left;"> 20.0 ( 5.1) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> DNR.status (%) </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> 371.2 (97.1) </td>
   <td style="text-align:left;"> 379.0 (97.2) </td>
   <td style="text-align:left;"> 0.006 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> 11.2 ( 2.9) </td>
   <td style="text-align:left;"> 11.0 ( 2.8) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> APACHE.III.score (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 49.76 (18.81) </td>
   <td style="text-align:left;"> 49.38 (19.71) </td>
   <td style="text-align:left;"> 0.019 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Pr.2mo.survival (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 0.69 (0.17) </td>
   <td style="text-align:left;"> 0.69 (0.17) </td>
   <td style="text-align:left;"> 0.006 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> No.of.comorbidity (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 1.79 (1.26) </td>
   <td style="text-align:left;"> 1.76 (1.23) </td>
   <td style="text-align:left;"> 0.019 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ADLs.2wk.prior (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 1.05 (1.71) </td>
   <td style="text-align:left;"> 1.02 (1.69) </td>
   <td style="text-align:left;"> 0.019 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> DASI.2wk.prior (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 20.35 (7.01) </td>
   <td style="text-align:left;"> 20.36 (6.96) </td>
   <td style="text-align:left;"> 0.002 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Temperature (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 37.24 (1.75) </td>
   <td style="text-align:left;"> 37.24 (1.61) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Heart.rate (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 110.71 (39.48) </td>
   <td style="text-align:left;"> 108.66 (39.22) </td>
   <td style="text-align:left;"> 0.052 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Blood.pressure (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 72.75 (31.94) </td>
   <td style="text-align:left;"> 70.91 (33.38) </td>
   <td style="text-align:left;"> 0.056 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Respiratory.rate (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 25.97 (12.14) </td>
   <td style="text-align:left;"> 25.25 (12.73) </td>
   <td style="text-align:left;"> 0.057 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WBC.count (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 14.98 (12.99) </td>
   <td style="text-align:left;"> 14.75 (13.09) </td>
   <td style="text-align:left;"> 0.017 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> PaO2.by.FIO2 (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 236.53 (103.80) </td>
   <td style="text-align:left;"> 238.90 (104.11) </td>
   <td style="text-align:left;"> 0.023 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> PaCO2 (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 36.83 (9.51) </td>
   <td style="text-align:left;"> 37.16 (8.57) </td>
   <td style="text-align:left;"> 0.036 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pH (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 7.40 (0.09) </td>
   <td style="text-align:left;"> 7.40 (0.09) </td>
   <td style="text-align:left;"> 0.027 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Creatinine (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 2.23 (2.38) </td>
   <td style="text-align:left;"> 2.22 (2.05) </td>
   <td style="text-align:left;"> 0.008 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albumin (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 3.15 (0.68) </td>
   <td style="text-align:left;"> 3.19 (0.64) </td>
   <td style="text-align:left;"> 0.053 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> GComa.Score (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 6.42 (18.42) </td>
   <td style="text-align:left;"> 6.54 (17.20) </td>
   <td style="text-align:left;"> 0.007 </td>
  </tr>
</tbody>
</table>


## Step 4

Estimate the effect of treatment on outcomes using propensity score-weighted sample


```r
fit3 <- glm(I(Death=="Yes")~RHC,
            family=binomial, data = analytic.data,
            weights = IPW.ATT)
```

```
## Warning in eval(family$initialize): non-integer #successes in a binomial glm!
```

```r
summ(fit3, exp = TRUE)
```

```
## Warning in eval(family$initialize): non-integer #successes in a binomial glm!
```

<table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;">
<tbody>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Observations </td>
   <td style="text-align:right;"> 1439 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Dependent variable </td>
   <td style="text-align:right;"> I(Death == "Yes") </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Type </td>
   <td style="text-align:right;"> Generalized linear model </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Family </td>
   <td style="text-align:right;"> binomial </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Link </td>
   <td style="text-align:right;"> logit </td>
  </tr>
</tbody>
</table> <table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;">
<tbody>
  <tr>
   <td style="text-align:left;font-weight: bold;"> χ²(1) </td>
   <td style="text-align:right;"> 0.24 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Pseudo-R² (Cragg-Uhler) </td>
   <td style="text-align:right;"> 0.00 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Pseudo-R² (McFadden) </td>
   <td style="text-align:right;"> 0.00 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> AIC </td>
   <td style="text-align:right;"> 944.54 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> BIC </td>
   <td style="text-align:right;"> 955.09 </td>
  </tr>
</tbody>
</table> <table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> exp(Est.) </th>
   <th style="text-align:right;"> 2.5% </th>
   <th style="text-align:right;"> 97.5% </th>
   <th style="text-align:right;"> z val. </th>
   <th style="text-align:right;"> p </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;font-weight: bold;"> (Intercept) </td>
   <td style="text-align:right;"> 0.92 </td>
   <td style="text-align:right;"> 0.75 </td>
   <td style="text-align:right;"> 1.13 </td>
   <td style="text-align:right;"> -0.79 </td>
   <td style="text-align:right;"> 0.43 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> RHCRHC </td>
   <td style="text-align:right;"> 1.07 </td>
   <td style="text-align:right;"> 0.81 </td>
   <td style="text-align:right;"> 1.42 </td>
   <td style="text-align:right;"> 0.49 </td>
   <td style="text-align:right;"> 0.62 </td>
  </tr>
</tbody>
<tfoot><tr><td style="padding: 0; border: 0;" colspan="100%">
<sup></sup> Standard errors: MLE</td></tr></tfoot>
</table>

```r
publish(fit3)
```

```
##  Variable  Units OddsRatio       CI.95  p-value 
##       RHC No RHC       Ref                      
##              RHC      1.07 [0.81;1.42]   0.6228
```






