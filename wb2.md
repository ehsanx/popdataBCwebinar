---
title: "Webinar: Propensity Score Analysis in Healthcare Data (Part 2: PS matching)"
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

![](wb2_files/figure-html/ps-1.png)<!-- -->

# PS Matching 

We follow 4 steps as described in Austin 2011.

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


```r
require(MatchIt)
set.seed(123)
# This function fits propensity score model (using logistic 
# regression as above) when specified distance = 'logit'
# performs nearest-neighbor (NN) matching, 
# without replacement 
# with caliper = .2*SD of propensity score  
logitPS <-  -log(1/analytic.data$PS - 1) # calculate the logit of the propensity score
hist(logitPS)
```

![](wb2_files/figure-html/ps2-1.png)<!-- -->

```r
# within which to draw control units 
# with 1:1 ratio (pair-matching)
match.obj <- matchit(ps.formula, data = analytic.data,
                     distance = 'logit', 
                     method = "nearest", 
                     replace=FALSE,
                     caliper = .2*sd(logitPS), 
                     ratio = 1)
# see matchit function options here
# https://www.rdocumentation.org/packages/MatchIt/versions/1.0-1/topics/matchit
analytic.data$PS <- match.obj$distance
summary(match.obj$distance)
```

```
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
## 0.003351 0.097465 0.233698 0.271022 0.417233 0.941860
```

```r
plot(match.obj, type = "jitter")
```

![](wb2_files/figure-html/ps2-2.png)<!-- -->

```
## [1] "To identify the units, use first mouse button; to stop, use second."
```

```
## integer(0)
```

```r
plot(match.obj, type = "hist")
```

![](wb2_files/figure-html/ps2-3.png)<!-- -->


```r
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
# check how many matched
match.obj
```

```
## 
## Call: 
## matchit(formula = ps.formula, data = analytic.data, method = "nearest", 
##     distance = "logit", replace = FALSE, caliper = 0.2 * sd(logitPS), 
##     ratio = 1)
## 
## Sample sizes:
##           Control Treated
## All          1049     390
## Matched       353     353
## Unmatched     696      37
## Discarded       0       0
```

```r
summary(match.obj, standardize = TRUE)
```

```
## 
## Call:
## matchit(formula = ps.formula, data = analytic.data, method = "nearest", 
##     distance = "logit", replace = FALSE, caliper = 0.2 * sd(logitPS), 
##     ratio = 1)
## 
## Summary of balance for all data:
##                       Means Treated Means Control SD Control Std. Mean Diff.
## distance                     0.4238        0.2142     0.1707          1.0478
## age[-Inf,50)                 0.2897        0.2517     0.4342          0.0838
## age[50,60)                   0.2179        0.1525     0.3597          0.1583
## age[60,70)                   0.2538        0.2488     0.4325          0.0116
## age[70,80)                   0.1795        0.2269     0.4190         -0.1233
## age[80, Inf)                 0.0590        0.1201     0.3253         -0.2592
## sexFemale                    0.3513        0.4576     0.4984         -0.2224
## raceblack                    0.1718        0.1678     0.3738          0.0106
## raceother                    0.0667        0.0572     0.2323          0.0379
## Disease.categoryCHF          0.3308        0.1659     0.3721          0.3500
## Disease.categoryMOSF         0.2821        0.1716     0.3772          0.2452
## Disease.categoryOther        0.0615        0.2536     0.4353         -0.7981
## CancerLocalized (Yes)        0.1179        0.1630     0.3696         -0.1395
## CancerMetastatic             0.0513        0.0772     0.2671         -0.1174
## DNR.statusYes                0.0282        0.0829     0.2759         -0.3302
## APACHE.III.score            49.3821       48.3565    16.3353          0.0520
## Pr.2mo.survival              0.6870        0.6998     0.1495         -0.0743
## No.of.comorbidity            1.7641        1.7407     1.2152          0.0191
## ADLs.2wk.prior               1.0154        1.2440     1.8605         -0.1350
## DASI.2wk.prior              20.3577       20.3590     7.2795         -0.0002
## Temperature                 37.2431       37.3466     1.6635         -0.0642
## Heart.rate                 108.6564      112.2326    38.2003         -0.0912
## Blood.pressure              70.9128       87.3546    37.9652         -0.4926
## Respiratory.rate            25.2538       30.4337    11.6512         -0.4069
## WBC.count                   14.7524       14.4546    11.1580          0.0227
## PaO2.by.FIO2               238.8988      250.8964   112.5266         -0.1152
## PaCO2                       37.1561       41.7678    14.8597         -0.5380
## pH                           7.4005        7.3852     0.0987          0.1800
## Creatinine                   2.2150        2.0333     2.2680          0.0889
## Albumin                      3.1863        3.2556     0.6465         -0.1089
## GComa.Score                  6.5410        5.2459    15.8311          0.0753
##                       eCDF Med eCDF Mean eCDF Max
## distance                0.3243    0.2874   0.4402
## age[-Inf,50)            0.0190    0.0190   0.0381
## age[50,60)              0.0327    0.0327   0.0654
## age[60,70)              0.0025    0.0025   0.0050
## age[70,80)              0.0237    0.0237   0.0474
## age[80, Inf)            0.0306    0.0306   0.0611
## sexFemale               0.0531    0.0531   0.1063
## raceblack               0.0020    0.0020   0.0040
## raceother               0.0047    0.0047   0.0095
## Disease.categoryCHF     0.0824    0.0824   0.1649
## Disease.categoryMOSF    0.0552    0.0552   0.1105
## Disease.categoryOther   0.0960    0.0960   0.1920
## CancerLocalized (Yes)   0.0225    0.0225   0.0451
## CancerMetastatic        0.0130    0.0130   0.0259
## DNR.statusYes           0.0274    0.0274   0.0547
## APACHE.III.score        0.0282    0.0307   0.0709
## Pr.2mo.survival         0.0298    0.0265   0.0525
## No.of.comorbidity       0.0109    0.0099   0.0258
## ADLs.2wk.prior          0.0278    0.0286   0.0673
## DASI.2wk.prior          0.0178    0.0176   0.0449
## Temperature             0.0115    0.0134   0.0338
## Heart.rate              0.0138    0.0186   0.0737
## Blood.pressure          0.0850    0.1044   0.2301
## Respiratory.rate        0.0710    0.0847   0.2303
## WBC.count               0.0105    0.0134   0.0471
## PaO2.by.FIO2            0.0292    0.0287   0.0712
## PaCO2                   0.0465    0.0571   0.1680
## pH                      0.0243    0.0248   0.0942
## Creatinine              0.0065    0.0233   0.1478
## Albumin                 0.0161    0.0250   0.1271
## GComa.Score             0.0016    0.0089   0.0453
## 
## 
## Summary of balance for matched data:
##                       Means Treated Means Control SD Control Std. Mean Diff.
## distance                     0.3978        0.3703     0.1709          0.1376
## age[-Inf,50)                 0.3059        0.2635     0.4411          0.0936
## age[50,60)                   0.1926        0.2125     0.4096         -0.0480
## age[60,70)                   0.2521        0.2578     0.4380         -0.0130
## age[70,80)                   0.1898        0.1870     0.3904          0.0074
## age[80, Inf)                 0.0595        0.0793     0.2706         -0.0841
## sexFemale                    0.3654        0.3994     0.4905         -0.0711
## raceblack                    0.1728        0.1841     0.3881         -0.0300
## raceother                    0.0680        0.0510     0.2203          0.0681
## Disease.categoryCHF          0.3229        0.3229     0.4683          0.0000
## Disease.categoryMOSF         0.2776        0.2748     0.4470          0.0063
## Disease.categoryOther        0.0680        0.0850     0.2793         -0.0706
## CancerLocalized (Yes)        0.1190        0.1303     0.3371         -0.0351
## CancerMetastatic             0.0567        0.0453     0.2083          0.0513
## DNR.statusYes                0.0312        0.0510     0.2203         -0.1196
## APACHE.III.score            49.2153       50.0963    18.8074         -0.0447
## Pr.2mo.survival              0.6933        0.6934     0.1643         -0.0003
## No.of.comorbidity            1.7394        1.8527     1.2950         -0.0923
## ADLs.2wk.prior               1.0142        1.1105     1.7424         -0.0569
## DASI.2wk.prior              20.5481       20.2860     7.0288          0.0377
## Temperature                 37.2303       37.2890     1.7653         -0.0364
## Heart.rate                 109.3513      112.3569    41.3422         -0.0766
## Blood.pressure              73.1246       75.1360    33.9128         -0.0603
## Respiratory.rate            26.3003       27.3314    12.0489         -0.0810
## WBC.count                   14.8069       15.4096    13.4508         -0.0460
## PaO2.by.FIO2               240.8011      239.4625   100.7218          0.0129
## PaCO2                       37.0761       37.8574    10.0809         -0.0911
## pH                           7.4007        7.3980     0.0880          0.0312
## Creatinine                   2.2450        2.2849     2.5122         -0.0195
## Albumin                      3.1835        3.1905     0.6889         -0.0111
## GComa.Score                  6.2890        6.2040    18.0914          0.0049
##                       eCDF Med eCDF Mean eCDF Max
## distance                0.0397    0.0451   0.1105
## age[-Inf,50)            0.0212    0.0212   0.0425
## age[50,60)              0.0099    0.0099   0.0198
## age[60,70)              0.0028    0.0028   0.0057
## age[70,80)              0.0014    0.0014   0.0028
## age[80, Inf)            0.0099    0.0099   0.0198
## sexFemale               0.0170    0.0170   0.0340
## raceblack               0.0057    0.0057   0.0113
## raceother               0.0085    0.0085   0.0170
## Disease.categoryCHF     0.0000    0.0000   0.0000
## Disease.categoryMOSF    0.0014    0.0014   0.0028
## Disease.categoryOther   0.0085    0.0085   0.0170
## CancerLocalized (Yes)   0.0057    0.0057   0.0113
## CancerMetastatic        0.0057    0.0057   0.0113
## DNR.statusYes           0.0099    0.0099   0.0198
## APACHE.III.score        0.0170    0.0201   0.0595
## Pr.2mo.survival         0.0113    0.0128   0.0453
## No.of.comorbidity       0.0085    0.0186   0.0567
## ADLs.2wk.prior          0.0042    0.0120   0.0538
## DASI.2wk.prior          0.0113    0.0146   0.0425
## Temperature             0.0085    0.0166   0.0595
## Heart.rate              0.0170    0.0244   0.0878
## Blood.pressure          0.0085    0.0184   0.0595
## Respiratory.rate        0.0198    0.0261   0.0822
## WBC.count               0.0142    0.0136   0.0368
## PaO2.by.FIO2            0.0198    0.0206   0.0567
## PaCO2                   0.0085    0.0131   0.0652
## pH                      0.0085    0.0109   0.0482
## Creatinine              0.0113    0.0158   0.0907
## Albumin                 0.0113    0.0240   0.0963
## GComa.Score             0.0113    0.0121   0.0312
## 
## Percent Balance Improvement:
##                       Std. Mean Diff.  eCDF Med eCDF Mean  eCDF Max
## distance                      86.8714   87.7723   84.3177   74.9026
## age[-Inf,50)                 -11.6022  -11.6022  -11.6022  -11.6022
## age[50,60)                    69.6893   69.6893   69.6893   69.6893
## age[60,70)                   -12.4650  -12.4650  -12.4650  -12.4650
## age[70,80)                    94.0229   94.0229   94.0229   94.0229
## age[80, Inf)                  67.5662   67.5662   67.5662   67.5662
## sexFemale                     68.0194   68.0194   68.0194   68.0194
## raceblack                   -182.1550 -182.1550 -182.1550 -182.1550
## raceother                    -79.4969  -79.4969  -79.4969  -79.4969
## Disease.categoryCHF          100.0000  100.0000  100.0000  100.0000
## Disease.categoryMOSF          97.4354   97.4354   97.4354   97.4354
## Disease.categoryOther         91.1490   91.1490   91.1490   91.1490
## CancerLocalized (Yes)         74.8546   74.8546   74.8546   74.8546
## CancerMetastatic              56.3072   56.3072   56.3072   56.3072
## DNR.statusYes                 63.7682   63.7682   63.7682   63.7682
## APACHE.III.score              14.0905   39.6903   34.6347   16.1077
## Pr.2mo.survival               99.5620   61.9220   51.5208   13.6761
## No.of.comorbidity           -384.3092   22.3049  -87.5987 -119.6857
## ADLs.2wk.prior                57.8770   84.7003   57.8770   20.0723
## DASI.2wk.prior            -19286.8363   36.2030   16.9699    5.2834
## Temperature                   43.3085   26.0873  -23.6082  -75.8779
## Heart.rate                    15.9535  -23.0854  -31.6334  -19.1309
## Blood.pressure                87.7670   90.0021   82.4055   74.1421
## Respiratory.rate              80.0930   72.0734   69.2287   64.3206
## WBC.count                   -102.4292  -34.3868   -0.9238   21.7738
## PaO2.by.FIO2                  88.8427   32.0661   28.0936   20.4727
## PaCO2                         83.0594   81.7243   77.1249   61.2250
## pH                            82.6907   65.0778   56.0904   48.8760
## Creatinine                    78.0506  -75.3994   32.2193   38.6463
## Albumin                       89.8474   29.4559    3.8316   24.2078
## GComa.Score                   93.4378 -618.7298  -36.4053   31.2640
## 
## Sample sizes:
##           Control Treated
## All          1049     390
## Matched       353     353
## Unmatched     696      37
## Discarded       0       0
```

```r
# extract matched data
matched.data <- match.data(match.obj)
```

## Step 3

Compare the similarity of baseline characteristics between treated and untreated subjects in a the propensity score-matched sample. In this case, we will compare SMD < 0.1 or not


```r
tab1m <- CreateTableOne(vars = baselinevars,
               data = matched.data, strata = "RHC", includeNA = TRUE, 
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
   <td style="text-align:left;"> 353 </td>
   <td style="text-align:left;"> 353 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> age (%) </td>
   <td style="text-align:left;"> [-Inf,50) </td>
   <td style="text-align:left;"> 93 (26.3) </td>
   <td style="text-align:left;"> 108 (30.6) </td>
   <td style="text-align:left;"> 0.119 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [50,60) </td>
   <td style="text-align:left;"> 75 (21.2) </td>
   <td style="text-align:left;"> 68 (19.3) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [60,70) </td>
   <td style="text-align:left;"> 91 (25.8) </td>
   <td style="text-align:left;"> 89 (25.2) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [70,80) </td>
   <td style="text-align:left;"> 66 (18.7) </td>
   <td style="text-align:left;"> 67 (19.0) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [80, Inf) </td>
   <td style="text-align:left;"> 28 ( 7.9) </td>
   <td style="text-align:left;"> 21 ( 5.9) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sex (%) </td>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:left;"> 212 (60.1) </td>
   <td style="text-align:left;"> 224 (63.5) </td>
   <td style="text-align:left;"> 0.070 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:left;"> 141 (39.9) </td>
   <td style="text-align:left;"> 129 (36.5) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> race (%) </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> 270 (76.5) </td>
   <td style="text-align:left;"> 268 (75.9) </td>
   <td style="text-align:left;"> 0.075 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> black </td>
   <td style="text-align:left;"> 65 (18.4) </td>
   <td style="text-align:left;"> 61 (17.3) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> other </td>
   <td style="text-align:left;"> 18 ( 5.1) </td>
   <td style="text-align:left;"> 24 ( 6.8) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Disease.category (%) </td>
   <td style="text-align:left;"> ARF </td>
   <td style="text-align:left;"> 112 (31.7) </td>
   <td style="text-align:left;"> 117 (33.1) </td>
   <td style="text-align:left;"> 0.067 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> CHF </td>
   <td style="text-align:left;"> 114 (32.3) </td>
   <td style="text-align:left;"> 114 (32.3) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> MOSF </td>
   <td style="text-align:left;"> 97 (27.5) </td>
   <td style="text-align:left;"> 98 (27.8) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Other </td>
   <td style="text-align:left;"> 30 ( 8.5) </td>
   <td style="text-align:left;"> 24 ( 6.8) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cancer (%) </td>
   <td style="text-align:left;"> None </td>
   <td style="text-align:left;"> 291 (82.4) </td>
   <td style="text-align:left;"> 291 (82.4) </td>
   <td style="text-align:left;"> 0.060 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Localized (Yes) </td>
   <td style="text-align:left;"> 46 (13.0) </td>
   <td style="text-align:left;"> 42 (11.9) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Metastatic </td>
   <td style="text-align:left;"> 16 ( 4.5) </td>
   <td style="text-align:left;"> 20 ( 5.7) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> DNR.status (%) </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> 335 (94.9) </td>
   <td style="text-align:left;"> 342 (96.9) </td>
   <td style="text-align:left;"> 0.100 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> 18 ( 5.1) </td>
   <td style="text-align:left;"> 11 ( 3.1) </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> APACHE.III.score (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 50.10 (18.81) </td>
   <td style="text-align:left;"> 49.22 (19.52) </td>
   <td style="text-align:left;"> 0.046 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Pr.2mo.survival (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 0.69 (0.16) </td>
   <td style="text-align:left;"> 0.69 (0.16) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> No.of.comorbidity (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 1.85 (1.30) </td>
   <td style="text-align:left;"> 1.74 (1.25) </td>
   <td style="text-align:left;"> 0.089 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ADLs.2wk.prior (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 1.11 (1.74) </td>
   <td style="text-align:left;"> 1.01 (1.71) </td>
   <td style="text-align:left;"> 0.056 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> DASI.2wk.prior (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 20.29 (7.03) </td>
   <td style="text-align:left;"> 20.55 (7.00) </td>
   <td style="text-align:left;"> 0.037 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Temperature (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 37.29 (1.77) </td>
   <td style="text-align:left;"> 37.23 (1.59) </td>
   <td style="text-align:left;"> 0.035 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Heart.rate (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 112.36 (41.34) </td>
   <td style="text-align:left;"> 109.35 (37.98) </td>
   <td style="text-align:left;"> 0.076 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Blood.pressure (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 75.14 (33.91) </td>
   <td style="text-align:left;"> 73.12 (33.48) </td>
   <td style="text-align:left;"> 0.060 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Respiratory.rate (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 27.33 (12.05) </td>
   <td style="text-align:left;"> 26.30 (12.50) </td>
   <td style="text-align:left;"> 0.084 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WBC.count (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 15.41 (13.45) </td>
   <td style="text-align:left;"> 14.81 (13.46) </td>
   <td style="text-align:left;"> 0.045 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> PaO2.by.FIO2 (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 239.46 (100.72) </td>
   <td style="text-align:left;"> 240.80 (104.74) </td>
   <td style="text-align:left;"> 0.013 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> PaCO2 (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 37.86 (10.08) </td>
   <td style="text-align:left;"> 37.08 (8.61) </td>
   <td style="text-align:left;"> 0.083 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pH (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 7.40 (0.09) </td>
   <td style="text-align:left;"> 7.40 (0.08) </td>
   <td style="text-align:left;"> 0.032 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Creatinine (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 2.28 (2.51) </td>
   <td style="text-align:left;"> 2.24 (2.10) </td>
   <td style="text-align:left;"> 0.017 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albumin (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 3.19 (0.69) </td>
   <td style="text-align:left;"> 3.18 (0.64) </td>
   <td style="text-align:left;"> 0.011 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> GComa.Score (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 6.20 (18.09) </td>
   <td style="text-align:left;"> 6.29 (16.44) </td>
   <td style="text-align:left;"> 0.005 </td>
  </tr>
</tbody>
</table>


## Step 4

Estimate the effect of treatment on outcomes using propensity score-matched sample


```r
fit3 <- glm(I(Death=="Yes")~RHC,
            family=binomial, data = matched.data)
summ(fit3, exp = TRUE)
```

<table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;">
<tbody>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Observations </td>
   <td style="text-align:right;"> 706 </td>
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
   <td style="text-align:right;"> 0.36 </td>
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
   <td style="text-align:right;"> 981.90 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> BIC </td>
   <td style="text-align:right;"> 991.02 </td>
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
   <td style="text-align:right;"> 0.91 </td>
   <td style="text-align:right;"> 0.74 </td>
   <td style="text-align:right;"> 1.12 </td>
   <td style="text-align:right;"> -0.90 </td>
   <td style="text-align:right;"> 0.37 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> RHCRHC </td>
   <td style="text-align:right;"> 1.09 </td>
   <td style="text-align:right;"> 0.82 </td>
   <td style="text-align:right;"> 1.47 </td>
   <td style="text-align:right;"> 0.60 </td>
   <td style="text-align:right;"> 0.55 </td>
  </tr>
</tbody>
<tfoot><tr><td style="padding: 0; border: 0;" colspan="100%">
<sup></sup> Standard errors: MLE</td></tr></tfoot>
</table>

```r
publish(fit3)
```

```
##  Variable  Units OddsRatio       CI.95 p-value 
##       RHC No RHC       Ref                     
##              RHC      1.09 [0.82;1.47]   0.547
```

General idea is that if any SMD >= 0.1, go back to the modelling stage, and change the PS model specification. If all else fails, adjust for the variables that are indicating some residual imbalance. 


```r
fit4 <- glm(I(Death=="Yes")~RHC+age+DNR.status,
            family=binomial, data = matched.data)
summ(fit4, exp = TRUE)
```

<table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;">
<tbody>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Observations </td>
   <td style="text-align:right;"> 706 </td>
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
   <td style="text-align:left;font-weight: bold;"> χ²(6) </td>
   <td style="text-align:right;"> 24.22 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Pseudo-R² (Cragg-Uhler) </td>
   <td style="text-align:right;"> 0.04 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Pseudo-R² (McFadden) </td>
   <td style="text-align:right;"> 0.02 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> AIC </td>
   <td style="text-align:right;"> 968.04 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> BIC </td>
   <td style="text-align:right;"> 999.96 </td>
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
   <td style="text-align:right;"> 0.52 </td>
   <td style="text-align:right;"> 0.38 </td>
   <td style="text-align:right;"> 0.73 </td>
   <td style="text-align:right;"> -3.81 </td>
   <td style="text-align:right;"> 0.00 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> RHCRHC </td>
   <td style="text-align:right;"> 1.15 </td>
   <td style="text-align:right;"> 0.85 </td>
   <td style="text-align:right;"> 1.55 </td>
   <td style="text-align:right;"> 0.90 </td>
   <td style="text-align:right;"> 0.37 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> age[50,60) </td>
   <td style="text-align:right;"> 1.75 </td>
   <td style="text-align:right;"> 1.13 </td>
   <td style="text-align:right;"> 2.71 </td>
   <td style="text-align:right;"> 2.51 </td>
   <td style="text-align:right;"> 0.01 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> age[60,70) </td>
   <td style="text-align:right;"> 2.11 </td>
   <td style="text-align:right;"> 1.40 </td>
   <td style="text-align:right;"> 3.19 </td>
   <td style="text-align:right;"> 3.55 </td>
   <td style="text-align:right;"> 0.00 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> age[70,80) </td>
   <td style="text-align:right;"> 2.01 </td>
   <td style="text-align:right;"> 1.28 </td>
   <td style="text-align:right;"> 3.16 </td>
   <td style="text-align:right;"> 3.03 </td>
   <td style="text-align:right;"> 0.00 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> age[80, Inf) </td>
   <td style="text-align:right;"> 2.13 </td>
   <td style="text-align:right;"> 1.12 </td>
   <td style="text-align:right;"> 4.07 </td>
   <td style="text-align:right;"> 2.29 </td>
   <td style="text-align:right;"> 0.02 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> DNR.statusYes </td>
   <td style="text-align:right;"> 2.44 </td>
   <td style="text-align:right;"> 1.05 </td>
   <td style="text-align:right;"> 5.69 </td>
   <td style="text-align:right;"> 2.07 </td>
   <td style="text-align:right;"> 0.04 </td>
  </tr>
</tbody>
<tfoot><tr><td style="padding: 0; border: 0;" colspan="100%">
<sup></sup> Standard errors: MLE</td></tr></tfoot>
</table>

```r
publish(fit4)
```

```
##    Variable     Units OddsRatio       CI.95     p-value 
##         RHC    No RHC       Ref                         
##                   RHC      1.15 [0.85;1.55]   0.3675406 
##         age [-Inf,50)       Ref                         
##               [50,60)      1.75 [1.13;2.71]   0.0119904 
##               [60,70)      2.11 [1.40;3.19]   0.0003908 
##               [70,80)      2.01 [1.28;3.16]   0.0024402 
##             [80, Inf)      2.13 [1.12;4.07]   0.0217611 
##  DNR.status        No       Ref                         
##                   Yes      2.44 [1.05;5.69]   0.0384989
```
