---
title: "Webinar: Propensity Score Analysis in Healthcare Data (Part 1: Analytic data)"
author: "Ehsan Karim, ehsan.karim@ubc.ca"
date: "14 May 2020"
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

Data from the effectiveness of Right Heart Catheterization in the initial care of critically ill patients study.

## Download

```r
rhc.original <- read.csv("http://biostat.mc.vanderbilt.edu/wiki/pub/Main/DataSets/rhc.csv")
```


```r
require(kableExtra)
kable(head(rhc.original)) %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", "responsive")) %>%
  scroll_box(width = "100%", height = "200px")
```

<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:200px; overflow-x: scroll; width:100%; "><table class="table table-striped table-hover table-condensed table-responsive" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> X </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> cat1 </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> cat2 </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> ca </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> sadmdte </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> dschdte </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> dthdte </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> lstctdte </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> death </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> cardiohx </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> chfhx </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> dementhx </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> psychhx </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> chrpulhx </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> renalhx </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> liverhx </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> gibledhx </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> malighx </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> immunhx </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> transhx </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> amihx </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> age </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> sex </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> edu </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> surv2md1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> das2d3pc </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> t3d30 </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> dth30 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> aps1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> scoma1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> meanbp1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> wblc1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> hrt1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> resp1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> temp1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> pafi1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> alb1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> hema1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> bili1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> crea1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> sod1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> pot1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> paco21 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> ph1 </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> swang1 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> wtkilo1 </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> dnr1 </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> ninsclas </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> resp </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> card </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> neuro </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> gastr </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> renal </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> meta </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> hema </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> seps </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> trauma </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> ortho </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> adld3p </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> urin1 </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> race </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> income </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> ptid </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> COPD </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:right;"> 11142 </td>
   <td style="text-align:right;"> 11151 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 11382 </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 70.25098 </td>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:right;"> 12.000000 </td>
   <td style="text-align:right;"> 0.6409912 </td>
   <td style="text-align:right;"> 23.50000 </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 46 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 41 </td>
   <td style="text-align:right;"> 22.0976562 </td>
   <td style="text-align:right;"> 124 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 38.69531 </td>
   <td style="text-align:right;"> 68.0000 </td>
   <td style="text-align:right;"> 3.500000 </td>
   <td style="text-align:right;"> 58.00000 </td>
   <td style="text-align:right;"> 1.0097656 </td>
   <td style="text-align:right;"> 1.1999512 </td>
   <td style="text-align:right;"> 145 </td>
   <td style="text-align:right;"> 4.000000 </td>
   <td style="text-align:right;"> 40 </td>
   <td style="text-align:right;"> 7.359375 </td>
   <td style="text-align:left;"> No RHC </td>
   <td style="text-align:right;"> 64.69995 </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> Medicare </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> Under $11k </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> MOSF w/Sepsis </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 11799 </td>
   <td style="text-align:right;"> 11844 </td>
   <td style="text-align:right;"> 11844 </td>
   <td style="text-align:right;"> 11844 </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 78.17896 </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:right;"> 12.000000 </td>
   <td style="text-align:right;"> 0.7549996 </td>
   <td style="text-align:right;"> 14.75195 </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 50 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 63 </td>
   <td style="text-align:right;"> 28.8984375 </td>
   <td style="text-align:right;"> 137 </td>
   <td style="text-align:right;"> 38 </td>
   <td style="text-align:right;"> 38.89844 </td>
   <td style="text-align:right;"> 218.3125 </td>
   <td style="text-align:right;"> 2.599609 </td>
   <td style="text-align:right;"> 32.50000 </td>
   <td style="text-align:right;"> 0.6999512 </td>
   <td style="text-align:right;"> 0.5999756 </td>
   <td style="text-align:right;"> 137 </td>
   <td style="text-align:right;"> 3.299805 </td>
   <td style="text-align:right;"> 34 </td>
   <td style="text-align:right;"> 7.329102 </td>
   <td style="text-align:left;"> RHC </td>
   <td style="text-align:right;"> 45.69998 </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> Private &amp; Medicare </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 1437 </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> Under $11k </td>
   <td style="text-align:right;"> 7 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> MOSF w/Malignancy </td>
   <td style="text-align:left;"> MOSF w/Sepsis </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:right;"> 12083 </td>
   <td style="text-align:right;"> 12143 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 12400 </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 46.09198 </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:right;"> 14.069916 </td>
   <td style="text-align:right;"> 0.3169999 </td>
   <td style="text-align:right;"> 18.13672 </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 82 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 57 </td>
   <td style="text-align:right;"> 0.0499954 </td>
   <td style="text-align:right;"> 130 </td>
   <td style="text-align:right;"> 40 </td>
   <td style="text-align:right;"> 36.39844 </td>
   <td style="text-align:right;"> 275.5000 </td>
   <td style="text-align:right;"> 3.500000 </td>
   <td style="text-align:right;"> 21.09766 </td>
   <td style="text-align:right;"> 1.0097656 </td>
   <td style="text-align:right;"> 2.5996094 </td>
   <td style="text-align:right;"> 146 </td>
   <td style="text-align:right;"> 2.899902 </td>
   <td style="text-align:right;"> 16 </td>
   <td style="text-align:right;"> 7.359375 </td>
   <td style="text-align:left;"> RHC </td>
   <td style="text-align:right;"> 0.00000 </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> Private </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 599 </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> $25-$50k </td>
   <td style="text-align:right;"> 9 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> ARF </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 11146 </td>
   <td style="text-align:right;"> 11183 </td>
   <td style="text-align:right;"> 11183 </td>
   <td style="text-align:right;"> 11182 </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 75.33197 </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:right;"> 9.000000 </td>
   <td style="text-align:right;"> 0.4409790 </td>
   <td style="text-align:right;"> 22.92969 </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 48 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 55 </td>
   <td style="text-align:right;"> 23.2968750 </td>
   <td style="text-align:right;"> 58 </td>
   <td style="text-align:right;"> 26 </td>
   <td style="text-align:right;"> 35.79688 </td>
   <td style="text-align:right;"> 156.6562 </td>
   <td style="text-align:right;"> 3.500000 </td>
   <td style="text-align:right;"> 26.29688 </td>
   <td style="text-align:right;"> 0.3999634 </td>
   <td style="text-align:right;"> 1.6999512 </td>
   <td style="text-align:right;"> 117 </td>
   <td style="text-align:right;"> 5.799805 </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:right;"> 7.459961 </td>
   <td style="text-align:left;"> No RHC </td>
   <td style="text-align:right;"> 54.59998 </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> Private &amp; Medicare </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> $11-$25k </td>
   <td style="text-align:right;"> 10 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> MOSF w/Sepsis </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 12035 </td>
   <td style="text-align:right;"> 12037 </td>
   <td style="text-align:right;"> 12037 </td>
   <td style="text-align:right;"> 12036 </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 67.90997 </td>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:right;"> 9.945259 </td>
   <td style="text-align:right;"> 0.4369998 </td>
   <td style="text-align:right;"> 21.05078 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:right;"> 72 </td>
   <td style="text-align:right;"> 41 </td>
   <td style="text-align:right;"> 65 </td>
   <td style="text-align:right;"> 29.6992188 </td>
   <td style="text-align:right;"> 125 </td>
   <td style="text-align:right;"> 27 </td>
   <td style="text-align:right;"> 34.79688 </td>
   <td style="text-align:right;"> 478.0000 </td>
   <td style="text-align:right;"> 3.500000 </td>
   <td style="text-align:right;"> 24.00000 </td>
   <td style="text-align:right;"> 1.0097656 </td>
   <td style="text-align:right;"> 3.5996094 </td>
   <td style="text-align:right;"> 126 </td>
   <td style="text-align:right;"> 5.799805 </td>
   <td style="text-align:right;"> 17 </td>
   <td style="text-align:right;"> 7.229492 </td>
   <td style="text-align:left;"> RHC </td>
   <td style="text-align:right;"> 78.39996 </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> Medicare </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 64 </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> Under $11k </td>
   <td style="text-align:right;"> 11 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> COPD </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 12389 </td>
   <td style="text-align:right;"> 12396 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 12590 </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 86.07794 </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:right;"> 8.000000 </td>
   <td style="text-align:right;"> 0.6650000 </td>
   <td style="text-align:right;"> 17.50000 </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 38 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 115 </td>
   <td style="text-align:right;"> 18.0000000 </td>
   <td style="text-align:right;"> 134 </td>
   <td style="text-align:right;"> 36 </td>
   <td style="text-align:right;"> 39.19531 </td>
   <td style="text-align:right;"> 184.1875 </td>
   <td style="text-align:right;"> 3.099609 </td>
   <td style="text-align:right;"> 30.50000 </td>
   <td style="text-align:right;"> 1.0097656 </td>
   <td style="text-align:right;"> 1.3999023 </td>
   <td style="text-align:right;"> 138 </td>
   <td style="text-align:right;"> 5.399414 </td>
   <td style="text-align:right;"> 68 </td>
   <td style="text-align:right;"> 7.299805 </td>
   <td style="text-align:left;"> No RHC </td>
   <td style="text-align:right;"> 54.89999 </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> Medicare </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 242 </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> Under $11k </td>
   <td style="text-align:right;"> 12 </td>
  </tr>
</tbody>
</table></div>


## Formatting

```r
rhc <- rhc.original
# Change the Age variable into categories  below 50, [50,60), [60,70), [70,80), above 80
rhc$age <- cut(rhc$age,breaks=c(-Inf, 50, 60, 70, 80, Inf),right=FALSE)
# Re-order the levels of race to white, black and other
rhc$race <- factor(rhc$race, levels=c("white","black","other"))
# merging disease categories
rhc$cat1 <- as.character(rhc$cat1)
rhc$cat1[rhc$cat1 == "Lung Cancer"] <- "Other"
rhc$cat1[rhc$cat1 == "COPD"] <- "Other"
rhc$cat1[rhc$cat1 == "Coma"] <- "Other"
rhc$cat1[rhc$cat1 == "Cirrhosis"] <- "Other"
rhc$cat1[rhc$cat1 == "Colon Cancer"] <- "Other"
rhc$cat1[rhc$cat1 == "MOSF w/Malignancy"] <- "MOSF"
rhc$cat1[rhc$cat1 == "MOSF w/Sepsis"] <- "MOSF"
rhc$cat1 <- as.factor(rhc$cat1)
# Change the baseline for gender to Male
rhc$sex <- relevel(rhc$sex, ref = "Male")
# Regroup the levels for disease categories to "ARF","CHF","MOSF","Other".
levels(rhc$ca) <- c("Metastatic","None","Localized (Yes)")
# Rename the levels of "ca" (Cancer) to "Metastatic","None" and "Localized (Yes)", 
rhc$ca <- factor(rhc$ca, levels=c("None","Localized (Yes)","Metastatic"))
# re-order the levels to "None","Localized (Yes)" and "Metastatic"
rhc$ca <- factor(rhc$ca, levels=c("None","Localized (Yes)","Metastatic"))
# create a new variable called "numcom" to count number of comorbidities illness for each person  
# (12 categories)
rhc$numcom <- rhc$cardiohx + rhc$chfhx + rhc$dementhx + rhc$psychhx + 
  rhc$chrpulhx + rhc$renalhx + rhc$liverhx + rhc$gibledhx + rhc$malighx + 
  rhc$immunhx + rhc$transhx +rhc$amihx
# Create a dataset that has only the following variables: 
# "age","sex", "race","cat1", "ca", "dnr1", "aps1","surv2md1",
# "numcom","adld3p","das2d3pc","temp1","hrt1","meanbp1","resp1",
# "wblc1","pafi1","paco21","ph1","crea1","alb1","scoma1","swang1", 
# and name it rhc2.
rhc2 <- rhc[c("age","sex", "race","cat1", "ca", "dnr1", "aps1",
              "surv2md1","numcom","adld3p","das2d3pc","temp1",
              "hrt1","meanbp1","resp1","wblc1","pafi1","paco21",
              "ph1","crea1","alb1","scoma1","swang1", "death")]
names(rhc2) <- c("age","sex", "race","Disease.category", "Cancer", 
                 "DNR.status", "APACHE.III.score", "Pr.2mo.survival",
                 "No.of.comorbidity","ADLs.2wk.prior","DASI.2wk.prior",
                 "Temperature","Heart.rate","Blood.pressure",
                 "Respiratory.rate","WBC.count","PaO2.by.FIO2","PaCO2",
                 "pH","Creatinine","Albumin","GComa.Score","RHC", "Death")
# simplifying analysis (usually not a good idea!)
analytic.data <- as.data.frame(na.omit(rhc2)) 
rm(rhc2)
```


```r
kable(head(analytic.data)) %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", "responsive")) %>%
  scroll_box(width = "100%", height = "200px")
```

<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:200px; overflow-x: scroll; width:100%; "><table class="table table-striped table-hover table-condensed table-responsive" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;">   </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> age </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> sex </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> race </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> Disease.category </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> Cancer </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> DNR.status </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> APACHE.III.score </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> Pr.2mo.survival </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> No.of.comorbidity </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> ADLs.2wk.prior </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> DASI.2wk.prior </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> Temperature </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> Heart.rate </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> Blood.pressure </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> Respiratory.rate </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> WBC.count </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> PaO2.by.FIO2 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> PaCO2 </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> pH </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> Creatinine </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> Albumin </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> GComa.Score </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> RHC </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> Death </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> [70,80) </td>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> Other </td>
   <td style="text-align:left;"> Localized (Yes) </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 46 </td>
   <td style="text-align:right;"> 0.6409912 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 23.5 </td>
   <td style="text-align:right;"> 38.69531 </td>
   <td style="text-align:right;"> 124 </td>
   <td style="text-align:right;"> 41 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 22.097656 </td>
   <td style="text-align:right;"> 68.0000 </td>
   <td style="text-align:right;"> 40 </td>
   <td style="text-align:right;"> 7.359375 </td>
   <td style="text-align:right;"> 1.199951 </td>
   <td style="text-align:right;"> 3.500000 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:left;"> No RHC </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 6 </td>
   <td style="text-align:left;"> [80, Inf) </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> Other </td>
   <td style="text-align:left;"> None </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 38 </td>
   <td style="text-align:right;"> 0.6650000 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 17.5 </td>
   <td style="text-align:right;"> 39.19531 </td>
   <td style="text-align:right;"> 134 </td>
   <td style="text-align:right;"> 115 </td>
   <td style="text-align:right;"> 36 </td>
   <td style="text-align:right;"> 18.000000 </td>
   <td style="text-align:right;"> 184.1875 </td>
   <td style="text-align:right;"> 68 </td>
   <td style="text-align:right;"> 7.299805 </td>
   <td style="text-align:right;"> 1.399902 </td>
   <td style="text-align:right;"> 3.099609 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:left;"> No RHC </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 14 </td>
   <td style="text-align:left;"> [-Inf,50) </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> MOSF </td>
   <td style="text-align:left;"> None </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 55 </td>
   <td style="text-align:right;"> 0.6719971 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 13.5 </td>
   <td style="text-align:right;"> 35.69531 </td>
   <td style="text-align:right;"> 110 </td>
   <td style="text-align:right;"> 77 </td>
   <td style="text-align:right;"> 40 </td>
   <td style="text-align:right;"> 7.299805 </td>
   <td style="text-align:right;"> 171.4062 </td>
   <td style="text-align:right;"> 25 </td>
   <td style="text-align:right;"> 7.469727 </td>
   <td style="text-align:right;"> 1.899902 </td>
   <td style="text-align:right;"> 3.500000 </td>
   <td style="text-align:right;"> 37 </td>
   <td style="text-align:left;"> No RHC </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 19 </td>
   <td style="text-align:left;"> [-Inf,50) </td>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:left;"> black </td>
   <td style="text-align:left;"> MOSF </td>
   <td style="text-align:left;"> None </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 60 </td>
   <td style="text-align:right;"> 0.7769775 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 21.0 </td>
   <td style="text-align:right;"> 39.00000 </td>
   <td style="text-align:right;"> 130 </td>
   <td style="text-align:right;"> 53 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 5.399414 </td>
   <td style="text-align:right;"> 390.4375 </td>
   <td style="text-align:right;"> 31 </td>
   <td style="text-align:right;"> 7.319336 </td>
   <td style="text-align:right;"> 15.000000 </td>
   <td style="text-align:right;"> 3.399902 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:left;"> No RHC </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 23 </td>
   <td style="text-align:left;"> [-Inf,50) </td>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:left;"> other </td>
   <td style="text-align:left;"> CHF </td>
   <td style="text-align:left;"> None </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:right;"> 0.8889160 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 22.0 </td>
   <td style="text-align:right;"> 36.89844 </td>
   <td style="text-align:right;"> 122 </td>
   <td style="text-align:right;"> 47 </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:right;"> 10.099609 </td>
   <td style="text-align:right;"> 333.3125 </td>
   <td style="text-align:right;"> 40 </td>
   <td style="text-align:right;"> 7.399998 </td>
   <td style="text-align:right;"> 1.299805 </td>
   <td style="text-align:right;"> 4.500000 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:left;"> RHC </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> [60,70) </td>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> CHF </td>
   <td style="text-align:left;"> None </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:right;"> 26 </td>
   <td style="text-align:right;"> 0.8629150 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 13.0 </td>
   <td style="text-align:right;"> 35.59375 </td>
   <td style="text-align:right;"> 115 </td>
   <td style="text-align:right;"> 63 </td>
   <td style="text-align:right;"> 22 </td>
   <td style="text-align:right;"> 5.500000 </td>
   <td style="text-align:right;"> 333.3125 </td>
   <td style="text-align:right;"> 40 </td>
   <td style="text-align:right;"> 7.399998 </td>
   <td style="text-align:right;"> 1.299805 </td>
   <td style="text-align:right;"> 3.899902 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:left;"> No RHC </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
</tbody>
</table></div>


# Summarize


```r
dim(analytic.data)
```

```
## [1] 1439   24
```

```r
require(Hmisc)
describe(analytic.data)
```

```
## analytic.data 
## 
##  24  Variables      1439  Observations
## --------------------------------------------------------------------------------
## age 
##        n  missing distinct 
##     1439        0        5 
## 
## lowest : [-Inf,50) [50,60)   [60,70)   [70,80)   [80, Inf)
## highest: [-Inf,50) [50,60)   [60,70)   [70,80)   [80, Inf)
##                                                             
## Value      [-Inf,50)   [50,60)   [60,70)   [70,80) [80, Inf)
## Frequency        377       245       360       308       149
## Proportion     0.262     0.170     0.250     0.214     0.104
## --------------------------------------------------------------------------------
## sex 
##        n  missing distinct 
##     1439        0        2 
##                         
## Value        Male Female
## Frequency     822    617
## Proportion  0.571  0.429
## --------------------------------------------------------------------------------
## race 
##        n  missing distinct 
##     1439        0        3 
##                             
## Value      white black other
## Frequency   1110   243    86
## Proportion 0.771 0.169 0.060
## --------------------------------------------------------------------------------
## Disease.category 
##        n  missing distinct 
##     1439        0        4 
##                                   
## Value        ARF   CHF  MOSF Other
## Frequency    556   303   290   290
## Proportion 0.386 0.211 0.202 0.202
## --------------------------------------------------------------------------------
## Cancer 
##        n  missing distinct 
##     1439        0        3 
##                                                           
## Value                 None Localized (Yes)      Metastatic
## Frequency             1121             217             101
## Proportion           0.779           0.151           0.070
## --------------------------------------------------------------------------------
## DNR.status 
##        n  missing distinct 
##     1439        0        2 
##                       
## Value         No   Yes
## Frequency   1341    98
## Proportion 0.932 0.068
## --------------------------------------------------------------------------------
## APACHE.III.score 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0       95        1    48.63    19.47       22       27 
##      .25      .50      .75      .90      .95 
##       36       47       60       71       77 
## 
## lowest :   9  11  12  13  14, highest: 106 109 112 113 114
## --------------------------------------------------------------------------------
## Pr.2mo.survival 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0      776        1   0.6963   0.1676   0.3819   0.4678 
##      .25      .50      .75      .90      .95 
##   0.6230   0.7279   0.8140   0.8580   0.8789 
## 
## lowest : 0.01399994 0.03699493 0.05899811 0.06698608 0.07199097
## highest: 0.93299961 0.93688965 0.93896484 0.93899965 0.93999958
## --------------------------------------------------------------------------------
## No.of.comorbidity 
##        n  missing distinct     Info     Mean      Gmd 
##     1439        0        7    0.938    1.747    1.332 
## 
## lowest : 0 1 2 3 4, highest: 2 3 4 5 6
##                                                     
## Value          0     1     2     3     4     5     6
## Frequency    221   437   419   246    84    27     5
## Proportion 0.154 0.304 0.291 0.171 0.058 0.019 0.003
## --------------------------------------------------------------------------------
## ADLs.2wk.prior 
##        n  missing distinct     Info     Mean      Gmd 
##     1439        0        8    0.832    1.182    1.699 
## 
## lowest : 0 1 2 3 4, highest: 3 4 5 6 7
##                                                           
## Value          0     1     2     3     4     5     6     7
## Frequency    778   296   130    43    49    64    53    26
## Proportion 0.541 0.206 0.090 0.030 0.034 0.044 0.037 0.018
## --------------------------------------------------------------------------------
## DASI.2wk.prior 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0       66    0.996    20.36    8.109       11       12 
##      .25      .50      .75      .90      .95 
##       14       19       25       33       33 
## 
## lowest : 11.0 12.0 12.5 13.0 13.5, highest: 30.5 31.0 31.5 32.0 33.0
## --------------------------------------------------------------------------------
## Temperature 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0       86    0.999    37.32     1.87     35.0     35.5 
##      .25      .50      .75      .90      .95 
##     36.0     36.9     38.8     39.4     39.8 
## 
## lowest : 30.19922 32.00000 32.29688 32.69531 33.09375
## highest: 41.00000 41.09375 41.39844 41.69531 43.00000
## --------------------------------------------------------------------------------
## Heart.rate 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0      162        1    111.3    42.77     50.0     58.8 
##      .25      .50      .75      .90      .95 
##     75.5    118.0    136.0    154.0    167.0 
## 
## lowest :   0  10  20  28  30, highest: 208 210 214 240 250
## --------------------------------------------------------------------------------
## Blood.pressure 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0      145        1     82.9    41.41     40.9     44.0 
##      .25      .50      .75      .90      .95 
##     53.0     67.0    117.0    135.0    146.1 
## 
## lowest :   0  17  24  29  30, highest: 185 203 210 222 259
## --------------------------------------------------------------------------------
## Respiratory.rate 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0       57    0.996    29.03    13.61        8       10 
##      .25      .50      .75      .90      .95 
##       24       30       38       43       48 
## 
## lowest :  0  3  4  5  6, highest: 62 65 66 69 70
## --------------------------------------------------------------------------------
## WBC.count 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0      358        1    14.54    10.47    2.590    4.899 
##      .25      .50      .75      .90      .95 
##    7.699   12.600   18.199   25.098   31.598 
## 
## lowest :   0.00000000   0.04999542   0.09999084   0.10998535   0.11999512
## highest:  89.59375000  90.68750000  93.18750000 100.00000000 171.00000000
## --------------------------------------------------------------------------------
## PaO2.by.FIO2 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0      607    0.994    247.6    122.5     90.0    113.9 
##      .25      .50      .75      .90      .95 
##    156.4    238.1    333.3    364.5    428.1 
## 
## lowest :  19.04688  37.59375  39.00000  39.29688  40.00000
## highest: 695.62500 714.25000 736.62500 823.75000 828.50000
## --------------------------------------------------------------------------------
## PaCO2 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0      142    0.993    40.52    13.59     25.0     27.0 
##      .25      .50      .75      .90      .95 
##     32.0     40.0     43.5     57.2     68.0 
## 
## lowest :   8.0000  10.0000  14.0000  15.0000  16.0000
## highest: 106.5938 111.0000 112.0000 113.0000 119.0000
## --------------------------------------------------------------------------------
## pH 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0       67    0.995    7.389  0.09904    7.209    7.279 
##      .25      .50      .75      .90      .95 
##    7.350    7.400    7.449    7.489    7.520 
## 
## lowest : 6.819336 6.919922 6.959961 6.969727 6.989258
## highest: 7.589844 7.599609 7.609375 7.619141 7.709961
## --------------------------------------------------------------------------------
## Creatinine 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0      116    0.998    2.083     1.75    0.600    0.700 
##      .25      .50      .75      .90      .95 
##    1.000    1.400    2.100    3.800    6.199 
## 
## lowest :  0.09999084  0.19998169  0.29998779  0.39996338  0.50000000
## highest: 15.69921880 15.79882810 16.69921880 17.09765620 20.29687500
## --------------------------------------------------------------------------------
## Albumin 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0       44    0.929    3.237    0.671     2.00     2.30 
##      .25      .50      .75      .90      .95 
##     2.90     3.50     3.50     3.90     4.11 
## 
## lowest : 0.2999878 0.5000000 1.0000000 1.0998535 1.1999512
## highest: 4.6992188 4.7998047 4.8994141 5.0000000 5.1992188
## --------------------------------------------------------------------------------
## GComa.Score 
##        n  missing distinct     Info     Mean      Gmd      .05      .10 
##     1439        0       11    0.467    5.597    10.01        0        0 
##      .25      .50      .75      .90      .95 
##        0        0        0       26       41 
## 
## lowest :   0   9  26  37  41, highest:  55  61  89  94 100
##                                                                             
## Value          0     9    26    37    41    44    55    61    89    94   100
## Frequency   1166   125    48    20    23    19     5    10     1     2    20
## Proportion 0.810 0.087 0.033 0.014 0.016 0.013 0.003 0.007 0.001 0.001 0.014
## --------------------------------------------------------------------------------
## RHC 
##        n  missing distinct 
##     1439        0        2 
##                         
## Value      No RHC    RHC
## Frequency    1049    390
## Proportion  0.729  0.271
## --------------------------------------------------------------------------------
## Death 
##        n  missing distinct 
##     1439        0        2 
##                       
## Value         No   Yes
## Frequency    733   706
## Proportion 0.509 0.491
## --------------------------------------------------------------------------------
```

## Crude Table 1 (stratified by Outcome)


```r
require(tableone)
tab1yc <- CreateTableOne(vars = "RHC",
               data = analytic.data, strata = "Death")
Tab1yc <- kable(print(tab1yc, showAllLevels = TRUE, smd = FALSE)) %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", "responsive"), 
                full_width = FALSE)
```


```r
Tab1yc
```

<table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;"> level </th>
   <th style="text-align:left;"> No </th>
   <th style="text-align:left;"> Yes </th>
   <th style="text-align:left;"> p </th>
   <th style="text-align:left;"> test </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> n </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 733 </td>
   <td style="text-align:left;"> 706 </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RHC (%) </td>
   <td style="text-align:left;"> No RHC </td>
   <td style="text-align:left;"> 537 (73.3) </td>
   <td style="text-align:left;"> 512 (72.5) </td>
   <td style="text-align:left;"> 0.798 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> RHC </td>
   <td style="text-align:left;"> 196 (26.7) </td>
   <td style="text-align:left;"> 194 (27.5) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
</tbody>
</table>

## Table 1 (stratified by Outcome)


```r
baselinevars <- c("age","sex", "race","Disease.category", 
                  "Cancer", "DNR.status", "APACHE.III.score",
                  "Pr.2mo.survival","No.of.comorbidity",
                  "ADLs.2wk.prior","DASI.2wk.prior","Temperature",
                  "Heart.rate", "Blood.pressure","Respiratory.rate",
                  "WBC.count","PaO2.by.FIO2","PaCO2","pH",
                  "Creatinine","Albumin","GComa.Score")
tab1y <- CreateTableOne(vars = baselinevars,
               data = analytic.data, strata = "Death", includeNA = TRUE, 
               test = TRUE, smd = FALSE)
Tab1y <- kable(print(tab1y, showAllLevels = TRUE, smd = FALSE)) %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", "responsive"), 
                full_width = FALSE)
```


```r
Tab1y
```

<table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;"> level </th>
   <th style="text-align:left;"> No </th>
   <th style="text-align:left;"> Yes </th>
   <th style="text-align:left;"> p </th>
   <th style="text-align:left;"> test </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> n </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 733 </td>
   <td style="text-align:left;"> 706 </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> age (%) </td>
   <td style="text-align:left;"> [-Inf,50) </td>
   <td style="text-align:left;"> 253 (34.5) </td>
   <td style="text-align:left;"> 124 (17.6) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [50,60) </td>
   <td style="text-align:left;"> 115 (15.7) </td>
   <td style="text-align:left;"> 130 (18.4) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [60,70) </td>
   <td style="text-align:left;"> 158 (21.6) </td>
   <td style="text-align:left;"> 202 (28.6) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [70,80) </td>
   <td style="text-align:left;"> 144 (19.6) </td>
   <td style="text-align:left;"> 164 (23.2) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> [80, Inf) </td>
   <td style="text-align:left;"> 63 ( 8.6) </td>
   <td style="text-align:left;"> 86 (12.2) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sex (%) </td>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:left;"> 397 (54.2) </td>
   <td style="text-align:left;"> 425 (60.2) </td>
   <td style="text-align:left;"> 0.024 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:left;"> 336 (45.8) </td>
   <td style="text-align:left;"> 281 (39.8) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> race (%) </td>
   <td style="text-align:left;"> white </td>
   <td style="text-align:left;"> 564 (76.9) </td>
   <td style="text-align:left;"> 546 (77.3) </td>
   <td style="text-align:left;"> 0.568 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> black </td>
   <td style="text-align:left;"> 129 (17.6) </td>
   <td style="text-align:left;"> 114 (16.1) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> other </td>
   <td style="text-align:left;"> 40 ( 5.5) </td>
   <td style="text-align:left;"> 46 ( 6.5) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Disease.category (%) </td>
   <td style="text-align:left;"> ARF </td>
   <td style="text-align:left;"> 331 (45.2) </td>
   <td style="text-align:left;"> 225 (31.9) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> CHF </td>
   <td style="text-align:left;"> 134 (18.3) </td>
   <td style="text-align:left;"> 169 (23.9) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> MOSF </td>
   <td style="text-align:left;"> 136 (18.6) </td>
   <td style="text-align:left;"> 154 (21.8) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Other </td>
   <td style="text-align:left;"> 132 (18.0) </td>
   <td style="text-align:left;"> 158 (22.4) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cancer (%) </td>
   <td style="text-align:left;"> None </td>
   <td style="text-align:left;"> 629 (85.8) </td>
   <td style="text-align:left;"> 492 (69.7) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Localized (Yes) </td>
   <td style="text-align:left;"> 88 (12.0) </td>
   <td style="text-align:left;"> 129 (18.3) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Metastatic </td>
   <td style="text-align:left;"> 16 ( 2.2) </td>
   <td style="text-align:left;"> 85 (12.0) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> DNR.status (%) </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> 707 (96.5) </td>
   <td style="text-align:left;"> 634 (89.8) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> 26 ( 3.5) </td>
   <td style="text-align:left;"> 72 (10.2) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> APACHE.III.score (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 47.51 (17.24) </td>
   <td style="text-align:left;"> 49.80 (17.32) </td>
   <td style="text-align:left;"> 0.012 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Pr.2mo.survival (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 0.73 (0.13) </td>
   <td style="text-align:left;"> 0.66 (0.17) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> No.of.comorbidity (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 1.51 (1.20) </td>
   <td style="text-align:left;"> 2.00 (1.19) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ADLs.2wk.prior (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 0.96 (1.68) </td>
   <td style="text-align:left;"> 1.41 (1.93) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> DASI.2wk.prior (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 21.75 (7.63) </td>
   <td style="text-align:left;"> 18.91 (6.39) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Temperature (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 37.52 (1.67) </td>
   <td style="text-align:left;"> 37.11 (1.60) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Heart.rate (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 112.14 (38.18) </td>
   <td style="text-align:left;"> 110.36 (38.84) </td>
   <td style="text-align:left;"> 0.381 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Blood.pressure (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 86.51 (39.21) </td>
   <td style="text-align:left;"> 79.15 (35.25) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Respiratory.rate (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 29.08 (12.34) </td>
   <td style="text-align:left;"> 28.98 (11.99) </td>
   <td style="text-align:left;"> 0.869 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WBC.count (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 14.36 (8.45) </td>
   <td style="text-align:left;"> 14.72 (14.33) </td>
   <td style="text-align:left;"> 0.554 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> PaO2.by.FIO2 (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 237.35 (109.56) </td>
   <td style="text-align:left;"> 258.33 (110.34) </td>
   <td style="text-align:left;"> &lt;0.001 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> PaCO2 (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 40.72 (14.47) </td>
   <td style="text-align:left;"> 40.31 (12.64) </td>
   <td style="text-align:left;"> 0.565 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pH (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 7.39 (0.10) </td>
   <td style="text-align:left;"> 7.39 (0.09) </td>
   <td style="text-align:left;"> 0.104 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Creatinine (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 1.96 (2.08) </td>
   <td style="text-align:left;"> 2.21 (2.34) </td>
   <td style="text-align:left;"> 0.037 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albumin (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 3.22 (0.66) </td>
   <td style="text-align:left;"> 3.25 (0.62) </td>
   <td style="text-align:left;"> 0.302 </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> GComa.Score (mean (SD)) </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 6.30 (17.77) </td>
   <td style="text-align:left;"> 4.87 (14.41) </td>
   <td style="text-align:left;"> 0.093 </td>
   <td style="text-align:left;">  </td>
  </tr>
</tbody>
</table>

## Unadjusted regression


```r
fit1 <- glm(I(Death=="Yes")~RHC,
            family=binomial, data = analytic.data)
require(jtools)
summ(fit1, conf = TRUE, digits = 2, exp = TRUE)
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
   <td style="text-align:right;"> 0.10 </td>
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
   <td style="text-align:right;"> 1998.27 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> BIC </td>
   <td style="text-align:right;"> 2008.81 </td>
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
   <td style="text-align:right;"> 0.95 </td>
   <td style="text-align:right;"> 0.84 </td>
   <td style="text-align:right;"> 1.08 </td>
   <td style="text-align:right;"> -0.77 </td>
   <td style="text-align:right;"> 0.44 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> RHCRHC </td>
   <td style="text-align:right;"> 1.04 </td>
   <td style="text-align:right;"> 0.82 </td>
   <td style="text-align:right;"> 1.31 </td>
   <td style="text-align:right;"> 0.32 </td>
   <td style="text-align:right;"> 0.75 </td>
  </tr>
</tbody>
<tfoot><tr><td style="padding: 0; border: 0;" colspan="100%">
<sup></sup> Standard errors: MLE</td></tr></tfoot>
</table>

```r
require(Publish)
publish(fit1)
```

```
##  Variable  Units OddsRatio       CI.95  p-value 
##       RHC No RHC       Ref                      
##              RHC      1.04 [0.82;1.31]   0.7524
```


```r
saveRDS(analytic.data, "data/RHC.Rds")
```

