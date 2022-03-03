---
title: "GWAS Quality Control"
author: " "
date: "updated on 2022-03-03"
site: bookdown::bookdown_site
output: bookdown::gitbook
documentclass: book
bibliography: [book.bib, packages.bib, paper.bib]
biblio-style:
csl: apa.csl
link-citations: yes
github-repo: rstudio/bookdown-demo
description: " "
---

# General Info

## Topic

We propose a new method to detect the inflation of GWAS test statistics caused by population stratification (& cryptic relatedness). Here we document all analyses and results.

**Background**:

1. Existing methods: use summary statistics to detect and correct inflation in GWAS test statistics.

  * genomic inflation factor:  
    * $S_{median}$/$\chi^2 (1)_{median}$ > 1 ? 
    * does not account for inflation due to polygenicity
    
  * LD score regression [@bulik2015ld] (LDSC):
    * $S_{j} = 1 + n_{j}a +  n_{j}\sum_{i}^{k}(r_{ji}^2 \ q_{i}/Q) h^2_{snp} + \epsilon_{j}$
      * under GCTA: $q_{i}=1, Q=\sum_{i}^{m}q_i=m$ 
      * under LDAK-thin: $q_{i}=I_{i}[f_{i}(1-f_{i})]^{0.75}$
    * It separates the inflation due to confounding (intercept) and the inflation due to polygenicity (slope).
    * The inflation due to confounding may not be constant but SNP specific instead [@holmes]. Hence, summary statistics may not be good after all to detect the inflation due to confounding. 

2. Proposed method: use individual level data to detect inflation and maybe extended to summary statistics (?).
    
  * $T = (\hat{h^2}_{right} + \hat{h^2}_{left}) - \hat{h^2}_{whole} > 0$ ?
  * Confounding causes cross-chromosome correlations, such that each part of the genome tags each other. Hence, for confounded GWASs, $T = (\hat{h^2}_{right} + \hat{h^2}_{left}) - \hat{h^2}_{whole} > 0$.
  * when $h_{right}^2, h_{left}^2, h_{whole}^2$ are estimated using Haseman Elston regression, this test amounts to testing $tr(K_{left}K_{right}) = 0$ or average SNP correlation^2 (between left and right) = 0.
  * Challenge: We do not know the distribution of $T$ under the null (i.e., no confounding). Consequently, the test is an approximate test. We hope to get an exact test. Also we want to simplify the test by directly testing if $tr(K_{left}K_{right}) = 0$ or average SNP correlation^2 (between left and right) = 0. Still we need to work out the distribution of average SNP correlation^2 (between left and right) under the null (i.e., no association). 

**Design**:

  * good GWASs: 100k unrelated white British
  * bad or confounded GWASs: 93k unrelated white British + 7k blacks & Asians 
  * control GWASs: 93k unrelated white British + 7k unrelated white British (who are not included in the good GWASs)
  * We also checked if confouding is evident for the UKBB recommended white British (a randomly selection of  100k out of 337k), on which most UKBB GWASs are based. 
  
**Results**:

So far the proposed and existing methods perform equally well.

  * good GWAS: no sig. inflation due to confounding
  * bad GWAS: sig. inflation due to confounding
  * control GWAS: no sig. inflation due to confounding
  * UKBB recommended: no evidence of inflation by any of the methods.

The proposed method does not seem to be more advantageous than existing methods. However, this does not mean that the existing methods are without problems. The inflation may not be constant as assumed by existing methods.

## Frequently used commands

`ssh -l zhoux login.genome.au.dk`

`sftp zhoux@login.genome.au.dk`

`lcd /home/zhoux/Dropbox/github/quality-control/main-files`

`cd /home/zhoux/dsmwpred/xuan/quality-control/qc-10oct`

`srun --mem=15g -c 2 -t 5:0:0 --constraint "s04|s05" -A snpher --pty /bin/bash`




<!--chapter:end:index.Rmd-->


# Summaries

To compare the proposed method against existing ones, we performed good and bad GWASs for 14 traits using N = 100k. While both sets of GWASs were based on quality-controlled genotype data, bad GWASs used individuals of different ethnicity and hence were confounded by population stratification, which is known to cause inflation in test statistics. As expected, for the good GWASs, no inflation was detected by the methods, though with some exceptions ( Tables \@ref(tab:ldsc-sumher-good) ). In contrast, for the bad GWASs significant inflation was evident based on all methods ( see Tables \@ref(tab:ldsc-sumher-bad) & \@ref(tab:he-bad) ). The detected inflation for the GWASs is not due to random errors because no inflation was observed for the control GWASs ( see Tables \@ref(tab:ldsc-sumher-control) & \@ref(tab:he-control) )

## Good GWAS

Code for the good GWAS can be found in section \@ref(good). Basically, we used 100k unrelated white British with quality-controlled genotype data for the GWASs. We tested the GWAS test statistics for inflation using ldsc, sumher and the proposed method (based on individual level data using Haseman Elston regression)

### ldsc & sumher

<div style="border: 0px;overflow-x: scroll; width:100%; border-bottom: 0;">
<table class=" lightable-minimal" style='font-family: "Trebuchet MS", verdana, sans-serif; margin-left: auto; margin-right: auto;'>
<caption>(\#tab:ldsc-sumher-good)Good GWAS: LDSC regression line intercept estimates (SE) using ldsc and sumher</caption>
 <thead>
<tr>
<th style="empty-cells: hide;" colspan="1"></th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3"><div style="border-bottom: 2px solid #00000050; ">ldsc</div></th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3"><div style="border-bottom: 2px solid #00000050; ">sumher-gcta</div></th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3"><div style="border-bottom: 2px solid #00000050; ">sumher-ldak-thin</div></th>
</tr>
  <tr>
   <th style="text-align:left;"> trait </th>
   <th style="text-align:right;"> est </th>
   <th style="text-align:right;"> se </th>
   <th style="text-align:left;"> wald_p </th>
   <th style="text-align:right;"> est </th>
   <th style="text-align:right;"> se </th>
   <th style="text-align:left;"> wald_p </th>
   <th style="text-align:right;"> est </th>
   <th style="text-align:right;"> se </th>
   <th style="text-align:left;"> wald_p </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> awake </td>
   <td style="text-align:right;"> 1.002 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">7.8e-01</span> </td>
   <td style="text-align:right;"> 0.997 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.8e-01</span> </td>
   <td style="text-align:right;"> 0.995 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.8e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bmi </td>
   <td style="text-align:right;"> 1.017 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.2e-02</span> </td>
   <td style="text-align:right;"> 0.994 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.3e-01</span> </td>
   <td style="text-align:right;"> 0.997 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">7.3e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> chron </td>
   <td style="text-align:right;"> 1.012 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.2e-01</span> </td>
   <td style="text-align:right;"> 1.011 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.0e-01</span> </td>
   <td style="text-align:right;"> 1.013 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.4e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ever </td>
   <td style="text-align:right;"> 1.000 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">9.4e-01</span> </td>
   <td style="text-align:right;"> 0.996 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.1e-01</span> </td>
   <td style="text-align:right;"> 0.995 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.6e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fvc </td>
   <td style="text-align:right;"> 1.032 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">1.4e-03</span> </td>
   <td style="text-align:right;"> 1.014 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.4e-01</span> </td>
   <td style="text-align:right;"> 1.007 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.6e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> height </td>
   <td style="text-align:right;"> 1.094 </td>
   <td style="text-align:right;"> 0.017 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">1.9e-08</span> </td>
   <td style="text-align:right;"> 1.018 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.4e-01</span> </td>
   <td style="text-align:right;"> 1.031 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">7.9e-03</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hyper </td>
   <td style="text-align:right;"> 1.018 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.6e-02</span> </td>
   <td style="text-align:right;"> 1.013 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.5e-01</span> </td>
   <td style="text-align:right;"> 1.002 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.6e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> imp </td>
   <td style="text-align:right;"> 1.016 </td>
   <td style="text-align:right;"> 0.011 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.3e-01</span> </td>
   <td style="text-align:right;"> 0.987 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.8e-01</span> </td>
   <td style="text-align:right;"> 0.991 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.4e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> neur </td>
   <td style="text-align:right;"> 1.004 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.8e-01</span> </td>
   <td style="text-align:right;"> 0.997 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">7.1e-01</span> </td>
   <td style="text-align:right;"> 0.996 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.5e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pulse </td>
   <td style="text-align:right;"> 1.029 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">2.3e-03</span> </td>
   <td style="text-align:right;"> 1.016 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.5e-02</span> </td>
   <td style="text-align:right;"> 1.019 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.2e-02</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> quals </td>
   <td style="text-align:right;"> 1.020 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.5e-02</span> </td>
   <td style="text-align:right;"> 1.014 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.3e-01</span> </td>
   <td style="text-align:right;"> 1.012 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.1e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> reaction </td>
   <td style="text-align:right;"> 1.010 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.6e-01</span> </td>
   <td style="text-align:right;"> 1.007 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.0e-01</span> </td>
   <td style="text-align:right;"> 1.003 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">7.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sbp </td>
   <td style="text-align:right;"> 1.007 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.9e-01</span> </td>
   <td style="text-align:right;"> 0.999 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.8e-01</span> </td>
   <td style="text-align:right;"> 0.997 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">7.2e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> snoring </td>
   <td style="text-align:right;"> 1.008 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.6e-01</span> </td>
   <td style="text-align:right;"> 1.004 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.3e-01</span> </td>
   <td style="text-align:right;"> 1.004 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.4e-01</span> </td>
  </tr>
</tbody>
</table>
<tfoot><tr><td style="padding: 0; " colspan="100%">
<span style="font-style: italic;">Note: </span> <sup></sup> p-values are based on the wald test that compares the ldsc regression line intercept against 1. p-values &lt;= Bonferroni corrected alpha (i.e., 0.05/14) are highlighted</td></tr></tfoot>
</div>

### Haseman Elston Regression

<div style="border: 0px;overflow-x: scroll; width:100%; border-bottom: 0;">
<table class=" lightable-minimal" style='font-family: "Trebuchet MS", verdana, sans-serif; margin-left: auto; margin-right: auto;'>
<caption>(\#tab:he-good)Good GWAS: Heritability estimates (SE) using Haseman Elston regression under assumed heritability models</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> trait </th>
   <th style="text-align:right;"> right_est </th>
   <th style="text-align:right;"> right_sd </th>
   <th style="text-align:right;"> left_est </th>
   <th style="text-align:right;"> left_sd </th>
   <th style="text-align:right;"> all_est </th>
   <th style="text-align:right;"> all_sd </th>
   <th style="text-align:left;"> p_inflation </th>
  </tr>
 </thead>
<tbody>
  <tr grouplength="14"><td colspan="8" style="background-color: #666; color: #fff;"><strong>under gcta</strong></td></tr>
<tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> awake </td>
   <td style="text-align:right;"> 0.040 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.034 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.073 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> bmi </td>
   <td style="text-align:right;"> 0.137 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.126 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.263 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> chron </td>
   <td style="text-align:right;"> 0.060 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.048 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.108 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.8e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> ever </td>
   <td style="text-align:right;"> 0.041 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.043 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.085 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.1e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> fvc </td>
   <td style="text-align:right;"> 0.110 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.100 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.210 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> height </td>
   <td style="text-align:right;"> 0.294 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:right;"> 0.264 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.558 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.8e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> imp </td>
   <td style="text-align:right;"> 0.154 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.141 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.295 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> neur </td>
   <td style="text-align:right;"> 0.064 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.057 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.121 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> pulse </td>
   <td style="text-align:right;"> 0.081 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.076 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.156 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> quals </td>
   <td style="text-align:right;"> 0.088 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.089 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.177 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.1e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> reaction </td>
   <td style="text-align:right;"> 0.034 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.038 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.072 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.1e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> sbp </td>
   <td style="text-align:right;"> 0.086 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.076 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.162 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> snoring </td>
   <td style="text-align:right;"> 0.034 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.033 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.068 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> hyper </td>
   <td style="text-align:right;"> 0.065 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.049 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.114 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.8e-01</span> </td>
  </tr>
  <tr grouplength="14"><td colspan="8" style="background-color: #666; color: #fff;"><strong>under ldak-thin</strong></td></tr>
<tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> awake </td>
   <td style="text-align:right;"> 0.037 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.033 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.070 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> bmi </td>
   <td style="text-align:right;"> 0.134 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.122 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.256 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> chron </td>
   <td style="text-align:right;"> 0.056 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.048 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.104 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> ever </td>
   <td style="text-align:right;"> 0.039 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.041 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.080 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> fvc </td>
   <td style="text-align:right;"> 0.105 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.100 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.205 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> height </td>
   <td style="text-align:right;"> 0.269 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.253 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.522 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> imp </td>
   <td style="text-align:right;"> 0.149 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.135 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.284 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> neur </td>
   <td style="text-align:right;"> 0.064 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.056 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.120 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> pulse </td>
   <td style="text-align:right;"> 0.077 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.073 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.150 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> quals </td>
   <td style="text-align:right;"> 0.085 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.089 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.173 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> reaction </td>
   <td style="text-align:right;"> 0.034 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.038 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.072 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> sbp </td>
   <td style="text-align:right;"> 0.088 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.076 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.163 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> snoring </td>
   <td style="text-align:right;"> 0.033 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.033 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.065 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> hyper </td>
   <td style="text-align:right;"> 0.068 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.049 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.117 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
</tbody>
</table>
<tfoot><tr><td style="padding: 0; " colspan="100%">
<span style="font-style: italic;">Note: </span> <sup></sup> p-values are for testing whether the sum of 'left' and 'right' heritability estimates are greater than the heritability estimate based on the whole genome. p-values &lt;= Bonferroni corrected alpha (i.e., 0.05/14) are highlighted</td></tr></tfoot>
</div>


## Bad GWAS

Code for the bad GWAS can be found in section \@ref(bad). Basically, we used 100k unrelated individuals that consist of 93,528 whites and 6,472 blacks and Asians (i.e., mixed populations). Hence, the GWASs were confounded by population stratification. We performed the confounded GWASs and tested the test statistics for inflation using ldsc, sumher and the proposed method (based on individual level data using Haseman Elston regression)

### ldsc & sumher

<div style="border: 0px;overflow-x: scroll; width:100%; border-bottom: 0;">
<table class=" lightable-minimal" style='font-family: "Trebuchet MS", verdana, sans-serif; margin-left: auto; margin-right: auto;'>
<caption>(\#tab:ldsc-sumher-bad)Bad GWAS: LDSC regression line intercept estimates (SE) using ldsc and sumher</caption>
 <thead>
<tr>
<th style="empty-cells: hide;" colspan="1"></th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3"><div style="border-bottom: 2px solid #00000050; ">ldsc</div></th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3"><div style="border-bottom: 2px solid #00000050; ">sumher-gcta</div></th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3"><div style="border-bottom: 2px solid #00000050; ">sumher-ldak-thin</div></th>
</tr>
  <tr>
   <th style="text-align:left;"> trait </th>
   <th style="text-align:right;"> est </th>
   <th style="text-align:right;"> se </th>
   <th style="text-align:left;"> wald_p </th>
   <th style="text-align:right;"> est </th>
   <th style="text-align:right;"> se </th>
   <th style="text-align:left;"> wald_p </th>
   <th style="text-align:right;"> est </th>
   <th style="text-align:right;"> se </th>
   <th style="text-align:left;"> wald_p </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> awake </td>
   <td style="text-align:right;"> 3.441 </td>
   <td style="text-align:right;"> 0.027 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 3.513 </td>
   <td style="text-align:right;"> 0.026 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 3.758 </td>
   <td style="text-align:right;"> 0.018 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bmi </td>
   <td style="text-align:right;"> 5.040 </td>
   <td style="text-align:right;"> 0.030 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 8.893 </td>
   <td style="text-align:right;"> 0.066 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 9.536 </td>
   <td style="text-align:right;"> 0.045 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> chron </td>
   <td style="text-align:right;"> 1.453 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">1.9e-311</span> </td>
   <td style="text-align:right;"> 1.402 </td>
   <td style="text-align:right;"> 0.011 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">2.4e-283</span> </td>
   <td style="text-align:right;"> 1.465 </td>
   <td style="text-align:right;"> 0.011 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ever </td>
   <td style="text-align:right;"> 6.838 </td>
   <td style="text-align:right;"> 0.028 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 15.830 </td>
   <td style="text-align:right;"> 0.114 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 16.885 </td>
   <td style="text-align:right;"> 0.080 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fvc </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> <span style="     color: black !important;">      NA</span> </td>
   <td style="text-align:right;"> 78.325 </td>
   <td style="text-align:right;"> 0.564 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 83.464 </td>
   <td style="text-align:right;"> 0.393 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> height </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> <span style="     color: black !important;">      NA</span> </td>
   <td style="text-align:right;"> 14.197 </td>
   <td style="text-align:right;"> 0.107 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 15.475 </td>
   <td style="text-align:right;"> 0.103 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hyper </td>
   <td style="text-align:right;"> 4.673 </td>
   <td style="text-align:right;"> 0.029 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 6.891 </td>
   <td style="text-align:right;"> 0.050 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 7.320 </td>
   <td style="text-align:right;"> 0.034 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> imp </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> <span style="     color: black !important;">      NA</span> </td>
   <td style="text-align:right;"> 8.930 </td>
   <td style="text-align:right;"> 0.064 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 9.467 </td>
   <td style="text-align:right;"> 0.045 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> neur </td>
   <td style="text-align:right;"> 4.179 </td>
   <td style="text-align:right;"> 0.026 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 5.349 </td>
   <td style="text-align:right;"> 0.038 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 5.682 </td>
   <td style="text-align:right;"> 0.027 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pulse </td>
   <td style="text-align:right;"> 1.444 </td>
   <td style="text-align:right;"> 0.013 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">7.8e-260</span> </td>
   <td style="text-align:right;"> 1.406 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">1.5e-269</span> </td>
   <td style="text-align:right;"> 1.452 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">2.5e-323</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> quals </td>
   <td style="text-align:right;"> 2.082 </td>
   <td style="text-align:right;"> 0.018 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 2.023 </td>
   <td style="text-align:right;"> 0.016 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 2.147 </td>
   <td style="text-align:right;"> 0.017 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> reaction </td>
   <td style="text-align:right;"> 8.123 </td>
   <td style="text-align:right;"> 0.031 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 33.822 </td>
   <td style="text-align:right;"> 0.241 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 35.822 </td>
   <td style="text-align:right;"> 0.169 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sbp </td>
   <td style="text-align:right;"> 3.028 </td>
   <td style="text-align:right;"> 0.026 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 3.050 </td>
   <td style="text-align:right;"> 0.023 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
   <td style="text-align:right;"> 3.313 </td>
   <td style="text-align:right;"> 0.016 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> snoring </td>
   <td style="text-align:right;"> 1.050 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 3.1e-11</span> </td>
   <td style="text-align:right;"> 1.052 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 5.2e-10</span> </td>
   <td style="text-align:right;"> 1.047 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: red !important;"> 6.3e-08</span> </td>
  </tr>
</tbody>
</table>
<tfoot><tr><td style="padding: 0; " colspan="100%">
<span style="font-style: italic;">Note: </span> <sup></sup> p-values are based on the wald test that compares the ldsc regression line intercept against 1. p-values &lt;= Bonferroni corrected alpha (i.e., 0.05/14) are highlighted</td></tr></tfoot>
</div>

### Haseman Elston Regression

<div style="border: 0px;overflow-x: scroll; width:100%; border-bottom: 0;">
<table class=" lightable-minimal" style='font-family: "Trebuchet MS", verdana, sans-serif; margin-left: auto; margin-right: auto;'>
<caption>(\#tab:he-bad)Bad GWAS: Heritability estimates (SE) using Haseman Elston regression under assumed heritability models</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> trait </th>
   <th style="text-align:right;"> right_est </th>
   <th style="text-align:right;"> right_sd </th>
   <th style="text-align:right;"> left_est </th>
   <th style="text-align:right;"> left_sd </th>
   <th style="text-align:right;"> all_est </th>
   <th style="text-align:right;"> all_sd </th>
   <th style="text-align:left;"> p_inflation </th>
  </tr>
 </thead>
<tbody>
  <tr grouplength="14"><td colspan="8" style="background-color: #666; color: #fff;"><strong>under gcta</strong></td></tr>
<tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> awake </td>
   <td style="text-align:right;"> 0.075 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:right;"> 0.075 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:right;"> 0.080 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">1.8e-04</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> bmi </td>
   <td style="text-align:right;"> 0.237 </td>
   <td style="text-align:right;"> 0.023 </td>
   <td style="text-align:right;"> 0.235 </td>
   <td style="text-align:right;"> 0.023 </td>
   <td style="text-align:right;"> 0.249 </td>
   <td style="text-align:right;"> 0.025 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> chron </td>
   <td style="text-align:right;"> 0.016 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.016 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.017 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.0e-02</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> ever </td>
   <td style="text-align:right;"> 0.439 </td>
   <td style="text-align:right;"> 0.028 </td>
   <td style="text-align:right;"> 0.434 </td>
   <td style="text-align:right;"> 0.027 </td>
   <td style="text-align:right;"> 0.461 </td>
   <td style="text-align:right;"> 0.029 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> fvc </td>
   <td style="text-align:right;"> 2.280 </td>
   <td style="text-align:right;"> 0.063 </td>
   <td style="text-align:right;"> 2.237 </td>
   <td style="text-align:right;"> 0.062 </td>
   <td style="text-align:right;"> 2.384 </td>
   <td style="text-align:right;"> 0.067 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> height </td>
   <td style="text-align:right;"> 0.406 </td>
   <td style="text-align:right;"> 0.023 </td>
   <td style="text-align:right;"> 0.402 </td>
   <td style="text-align:right;"> 0.023 </td>
   <td style="text-align:right;"> 0.426 </td>
   <td style="text-align:right;"> 0.024 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> imp </td>
   <td style="text-align:right;"> 0.228 </td>
   <td style="text-align:right;"> 0.015 </td>
   <td style="text-align:right;"> 0.227 </td>
   <td style="text-align:right;"> 0.015 </td>
   <td style="text-align:right;"> 0.240 </td>
   <td style="text-align:right;"> 0.016 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> neur </td>
   <td style="text-align:right;"> 0.128 </td>
   <td style="text-align:right;"> 0.015 </td>
   <td style="text-align:right;"> 0.129 </td>
   <td style="text-align:right;"> 0.015 </td>
   <td style="text-align:right;"> 0.136 </td>
   <td style="text-align:right;"> 0.016 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">1.0e-05</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> pulse </td>
   <td style="text-align:right;"> 0.018 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.019 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.020 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">2.6e-03</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> quals </td>
   <td style="text-align:right;"> 0.037 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.038 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.040 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">2.9e-04</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> reaction </td>
   <td style="text-align:right;"> 0.959 </td>
   <td style="text-align:right;"> 0.059 </td>
   <td style="text-align:right;"> 0.950 </td>
   <td style="text-align:right;"> 0.059 </td>
   <td style="text-align:right;"> 1.008 </td>
   <td style="text-align:right;"> 0.063 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> sbp </td>
   <td style="text-align:right;"> 0.064 </td>
   <td style="text-align:right;"> 0.011 </td>
   <td style="text-align:right;"> 0.064 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:right;"> 0.068 </td>
   <td style="text-align:right;"> 0.011 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">4.6e-04</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> snoring </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.001 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.001 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.001 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">3.1e-03</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> hyper </td>
   <td style="text-align:right;"> 0.175 </td>
   <td style="text-align:right;"> 0.020 </td>
   <td style="text-align:right;"> 0.172 </td>
   <td style="text-align:right;"> 0.020 </td>
   <td style="text-align:right;"> 0.183 </td>
   <td style="text-align:right;"> 0.021 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr grouplength="14"><td colspan="8" style="background-color: #666; color: #fff;"><strong>under ldak-thin</strong></td></tr>
<tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> awake </td>
   <td style="text-align:right;"> 0.090 </td>
   <td style="text-align:right;"> 0.014 </td>
   <td style="text-align:right;"> 0.090 </td>
   <td style="text-align:right;"> 0.014 </td>
   <td style="text-align:right;"> 0.080 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">2.0e-05</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> bmi </td>
   <td style="text-align:right;"> 0.278 </td>
   <td style="text-align:right;"> 0.027 </td>
   <td style="text-align:right;"> 0.273 </td>
   <td style="text-align:right;"> 0.027 </td>
   <td style="text-align:right;"> 0.249 </td>
   <td style="text-align:right;"> 0.025 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> chron </td>
   <td style="text-align:right;"> 0.021 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.021 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.017 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">1.8e-03</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> ever </td>
   <td style="text-align:right;"> 0.533 </td>
   <td style="text-align:right;"> 0.033 </td>
   <td style="text-align:right;"> 0.525 </td>
   <td style="text-align:right;"> 0.032 </td>
   <td style="text-align:right;"> 0.461 </td>
   <td style="text-align:right;"> 0.029 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> fvc </td>
   <td style="text-align:right;"> 2.782 </td>
   <td style="text-align:right;"> 0.076 </td>
   <td style="text-align:right;"> 2.734 </td>
   <td style="text-align:right;"> 0.074 </td>
   <td style="text-align:right;"> 2.384 </td>
   <td style="text-align:right;"> 0.067 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> height </td>
   <td style="text-align:right;"> 0.527 </td>
   <td style="text-align:right;"> 0.029 </td>
   <td style="text-align:right;"> 0.526 </td>
   <td style="text-align:right;"> 0.029 </td>
   <td style="text-align:right;"> 0.426 </td>
   <td style="text-align:right;"> 0.024 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> imp </td>
   <td style="text-align:right;"> 0.283 </td>
   <td style="text-align:right;"> 0.018 </td>
   <td style="text-align:right;"> 0.281 </td>
   <td style="text-align:right;"> 0.017 </td>
   <td style="text-align:right;"> 0.240 </td>
   <td style="text-align:right;"> 0.016 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> neur </td>
   <td style="text-align:right;"> 0.150 </td>
   <td style="text-align:right;"> 0.018 </td>
   <td style="text-align:right;"> 0.150 </td>
   <td style="text-align:right;"> 0.018 </td>
   <td style="text-align:right;"> 0.136 </td>
   <td style="text-align:right;"> 0.016 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> pulse </td>
   <td style="text-align:right;"> 0.024 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.025 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.020 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">1.0e-05</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> quals </td>
   <td style="text-align:right;"> 0.049 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:right;"> 0.050 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:right;"> 0.040 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> reaction </td>
   <td style="text-align:right;"> 1.146 </td>
   <td style="text-align:right;"> 0.070 </td>
   <td style="text-align:right;"> 1.135 </td>
   <td style="text-align:right;"> 0.069 </td>
   <td style="text-align:right;"> 1.008 </td>
   <td style="text-align:right;"> 0.063 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> sbp </td>
   <td style="text-align:right;"> 0.077 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:right;"> 0.075 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:right;"> 0.068 </td>
   <td style="text-align:right;"> 0.011 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">1.0e-05</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> snoring </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.001 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:right;"> 0.001 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.001 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">5.0e-05</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> hyper </td>
   <td style="text-align:right;"> 0.206 </td>
   <td style="text-align:right;"> 0.024 </td>
   <td style="text-align:right;"> 0.202 </td>
   <td style="text-align:right;"> 0.023 </td>
   <td style="text-align:right;"> 0.183 </td>
   <td style="text-align:right;"> 0.021 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">0.0e+00</span> </td>
  </tr>
</tbody>
</table>
<tfoot><tr><td style="padding: 0; " colspan="100%">
<span style="font-style: italic;">Note: </span> <sup></sup> p-values are for testing whether the sum of 'left' and 'right' heritability estimates are greater than the heritability estimate based on the whole genome. p-values &lt;= Bonferroni corrected alpha (i.e., 0.05/14) are highlighted</td></tr></tfoot>
</div>

## UKBB recommended

Code for this GWAS can be found in section \@ref(ukbb). Basically, we randomly selected 100k white British from a total of 337k individuals that are recommended by the UKBB (i.e., QCed by the UKBB). We performed the GWASs and tested the test statistics for inflation using ldsc, sumher and the proposed method (based on individual level data using Haseman Elston regression).

### ldsc & sumher

<div style="border: 0px;overflow-x: scroll; width:100%; border-bottom: 0;">
<table class=" lightable-minimal" style='font-family: "Trebuchet MS", verdana, sans-serif; margin-left: auto; margin-right: auto;'>
<caption>(\#tab:ldsc-sumher-ukbb)UKBB recommended: LDSC regression line intercept estimates (SE) using ldsc and sumher</caption>
 <thead>
<tr>
<th style="empty-cells: hide;" colspan="1"></th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3"><div style="border-bottom: 2px solid #00000050; ">ldsc</div></th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3"><div style="border-bottom: 2px solid #00000050; ">sumher-gcta</div></th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3"><div style="border-bottom: 2px solid #00000050; ">sumher-ldak-thin</div></th>
</tr>
  <tr>
   <th style="text-align:left;"> trait </th>
   <th style="text-align:right;"> est </th>
   <th style="text-align:right;"> se </th>
   <th style="text-align:left;"> wald_p </th>
   <th style="text-align:right;"> est </th>
   <th style="text-align:right;"> se </th>
   <th style="text-align:left;"> wald_p </th>
   <th style="text-align:right;"> est </th>
   <th style="text-align:right;"> se </th>
   <th style="text-align:left;"> wald_p </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> awake </td>
   <td style="text-align:right;"> 1.017 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">9.8e-03</span> </td>
   <td style="text-align:right;"> 1.015 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">7.0e-02</span> </td>
   <td style="text-align:right;"> 1.012 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bmi </td>
   <td style="text-align:right;"> 1.016 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.4e-02</span> </td>
   <td style="text-align:right;"> 0.995 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.1e-01</span> </td>
   <td style="text-align:right;"> 0.996 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.6e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> chron </td>
   <td style="text-align:right;"> 1.014 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.2e-02</span> </td>
   <td style="text-align:right;"> 1.008 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.4e-01</span> </td>
   <td style="text-align:right;"> 1.010 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.4e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ever </td>
   <td style="text-align:right;"> 0.990 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.3e-01</span> </td>
   <td style="text-align:right;"> 0.986 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.1e-01</span> </td>
   <td style="text-align:right;"> 0.986 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.1e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fvc </td>
   <td style="text-align:right;"> 1.036 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">2.8e-05</span> </td>
   <td style="text-align:right;"> 1.021 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.4e-02</span> </td>
   <td style="text-align:right;"> 1.018 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.4e-02</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> height </td>
   <td style="text-align:right;"> 1.102 </td>
   <td style="text-align:right;"> 0.015 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">2.9e-11</span> </td>
   <td style="text-align:right;"> 1.030 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.1e-02</span> </td>
   <td style="text-align:right;"> 1.043 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">2.4e-04</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hyper </td>
   <td style="text-align:right;"> 1.010 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.7e-01</span> </td>
   <td style="text-align:right;"> 1.003 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.9e-01</span> </td>
   <td style="text-align:right;"> 1.003 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">7.3e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> imp </td>
   <td style="text-align:right;"> 1.029 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">1.2e-03</span> </td>
   <td style="text-align:right;"> 1.004 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.9e-01</span> </td>
   <td style="text-align:right;"> 1.005 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.8e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> neur </td>
   <td style="text-align:right;"> 1.010 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.9e-01</span> </td>
   <td style="text-align:right;"> 1.007 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.1e-01</span> </td>
   <td style="text-align:right;"> 1.001 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pulse </td>
   <td style="text-align:right;"> 1.007 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.2e-01</span> </td>
   <td style="text-align:right;"> 0.992 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.0e-01</span> </td>
   <td style="text-align:right;"> 0.995 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.6e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> quals </td>
   <td style="text-align:right;"> 1.015 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">7.4e-02</span> </td>
   <td style="text-align:right;"> 1.004 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.9e-01</span> </td>
   <td style="text-align:right;"> 1.002 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.5e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> reaction </td>
   <td style="text-align:right;"> 1.016 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.1e-02</span> </td>
   <td style="text-align:right;"> 1.015 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.1e-02</span> </td>
   <td style="text-align:right;"> 1.016 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">7.1e-02</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sbp </td>
   <td style="text-align:right;"> 1.022 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.9e-03</span> </td>
   <td style="text-align:right;"> 1.016 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.0e-02</span> </td>
   <td style="text-align:right;"> 1.011 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.1e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> snoring </td>
   <td style="text-align:right;"> 1.005 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.6e-01</span> </td>
   <td style="text-align:right;"> 1.002 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.5e-01</span> </td>
   <td style="text-align:right;"> 1.001 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.9e-01</span> </td>
  </tr>
</tbody>
</table>
<tfoot><tr><td style="padding: 0; " colspan="100%">
<span style="font-style: italic;">Note: </span> <sup></sup> p-values are based on the wald test that compares the ldsc regression line intercept against 1. p-values &lt;= Bonferroni corrected alpha (i.e., 0.05/14) are highlighted</td></tr></tfoot>
</div>

### Haseman Elston Regression

<div style="border: 0px;overflow-x: scroll; width:100%; border-bottom: 0;">
<table class=" lightable-minimal" style='font-family: "Trebuchet MS", verdana, sans-serif; margin-left: auto; margin-right: auto;'>
<caption>(\#tab:he-ukbb) UKBB recommended: Heritability estimates (SE) using Haseman Elston regression under assumed heritability models</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> trait </th>
   <th style="text-align:right;"> right_est </th>
   <th style="text-align:right;"> right_sd </th>
   <th style="text-align:right;"> left_est </th>
   <th style="text-align:right;"> left_sd </th>
   <th style="text-align:right;"> all_est </th>
   <th style="text-align:right;"> all_sd </th>
   <th style="text-align:left;"> p_inflation </th>
  </tr>
 </thead>
<tbody>
  <tr grouplength="14"><td colspan="8" style="background-color: #666; color: #fff;"><strong>under gcta</strong></td></tr>
<tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> awake </td>
   <td style="text-align:right;"> 0.034 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.030 </td>
   <td style="text-align:right;"> 0.003 </td>
   <td style="text-align:right;"> 0.062 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> bmi </td>
   <td style="text-align:right;"> 0.135 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.124 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.251 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> chron </td>
   <td style="text-align:right;"> 0.061 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.048 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.104 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> ever </td>
   <td style="text-align:right;"> 0.034 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.040 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.071 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> fvc </td>
   <td style="text-align:right;"> 0.098 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.103 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.194 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.4e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> height </td>
   <td style="text-align:right;"> 0.274 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:right;"> 0.264 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.522 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.7e-02</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> imp </td>
   <td style="text-align:right;"> 0.142 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.142 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.276 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> neur </td>
   <td style="text-align:right;"> 0.064 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.055 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.115 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.3e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> pulse </td>
   <td style="text-align:right;"> 0.084 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.077 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.155 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.5e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> quals </td>
   <td style="text-align:right;"> 0.084 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.089 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.168 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.4e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> reaction </td>
   <td style="text-align:right;"> 0.037 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.040 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.075 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> sbp </td>
   <td style="text-align:right;"> 0.086 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.073 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.154 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.8e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> snoring </td>
   <td style="text-align:right;"> 0.038 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.032 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.067 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> hyper </td>
   <td style="text-align:right;"> 0.057 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.046 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.100 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.2e-01</span> </td>
  </tr>
  <tr grouplength="14"><td colspan="8" style="background-color: #666; color: #fff;"><strong>under ldak-thin</strong></td></tr>
<tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> awake </td>
   <td style="text-align:right;"> 0.033 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.031 </td>
   <td style="text-align:right;"> 0.003 </td>
   <td style="text-align:right;"> 0.062 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.2e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> bmi </td>
   <td style="text-align:right;"> 0.134 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.122 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.251 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> chron </td>
   <td style="text-align:right;"> 0.059 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.047 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.104 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> ever </td>
   <td style="text-align:right;"> 0.032 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.039 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.071 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> fvc </td>
   <td style="text-align:right;"> 0.094 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.101 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.194 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.3e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> height </td>
   <td style="text-align:right;"> 0.257 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.249 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.522 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">9.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> imp </td>
   <td style="text-align:right;"> 0.138 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.135 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.276 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> neur </td>
   <td style="text-align:right;"> 0.069 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:right;"> 0.055 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.115 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> pulse </td>
   <td style="text-align:right;"> 0.081 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.074 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.155 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.6e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> quals </td>
   <td style="text-align:right;"> 0.083 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.088 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.168 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.8e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> reaction </td>
   <td style="text-align:right;"> 0.036 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.038 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.075 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.1e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> sbp </td>
   <td style="text-align:right;"> 0.085 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.072 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.154 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> snoring </td>
   <td style="text-align:right;"> 0.035 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.033 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.067 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> hyper </td>
   <td style="text-align:right;"> 0.055 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.045 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.100 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
</tbody>
</table>
<tfoot><tr><td style="padding: 0; " colspan="100%">
<span style="font-style: italic;">Note: </span> <sup></sup> p-values are for testing whether the sum of 'left' and 'right' heritability estimates are greater than the heritability estimate based on the whole genome. p-values &lt;= Bonferroni corrected alpha (i.e., 0.05/14) are highlighted</td></tr></tfoot>
</div>

## Control GWAS

Code for this GWAS can be found in section \@ref(control). In short, we performed GWASs that serve as the control for the good versus bad GWASs comparison. This to ascertain that the observed inflation of bad GWAS test statistics is due to population stratification not random errors. We used a total of 100k that included 93,528 unrelated white British (also included in the bad and good GWAS) and 6,472 unrelated white British that were neither included in the good GWASs nor bad GWASs.

### ldsc & sumher

<div style="border: 0px;overflow-x: scroll; width:100%; border-bottom: 0;">
<table class=" lightable-minimal" style='font-family: "Trebuchet MS", verdana, sans-serif; margin-left: auto; margin-right: auto;'>
<caption>(\#tab:ldsc-sumher-control)Control GWASs: LDSC regression line intercept estimates (SE) using ldsc and sumher</caption>
 <thead>
<tr>
<th style="empty-cells: hide;" colspan="1"></th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3"><div style="border-bottom: 2px solid #00000050; ">ldsc</div></th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3"><div style="border-bottom: 2px solid #00000050; ">sumher-gcta</div></th>
<th style="padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3"><div style="border-bottom: 2px solid #00000050; ">sumher-ldak-thin</div></th>
</tr>
  <tr>
   <th style="text-align:left;"> trait </th>
   <th style="text-align:right;"> est </th>
   <th style="text-align:right;"> se </th>
   <th style="text-align:left;"> wald_p </th>
   <th style="text-align:right;"> est </th>
   <th style="text-align:right;"> se </th>
   <th style="text-align:left;"> wald_p </th>
   <th style="text-align:right;"> est </th>
   <th style="text-align:right;"> se </th>
   <th style="text-align:left;"> wald_p </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> awake </td>
   <td style="text-align:right;"> 0.996 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.4e-01</span> </td>
   <td style="text-align:right;"> 0.992 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.6e-01</span> </td>
   <td style="text-align:right;"> 0.994 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.5e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bmi </td>
   <td style="text-align:right;"> 1.014 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.1e-01</span> </td>
   <td style="text-align:right;"> 0.991 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.9e-01</span> </td>
   <td style="text-align:right;"> 0.991 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.6e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> chron </td>
   <td style="text-align:right;"> 1.009 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.2e-01</span> </td>
   <td style="text-align:right;"> 1.008 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.4e-01</span> </td>
   <td style="text-align:right;"> 1.011 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ever </td>
   <td style="text-align:right;"> 0.999 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">9.2e-01</span> </td>
   <td style="text-align:right;"> 0.996 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.4e-01</span> </td>
   <td style="text-align:right;"> 0.993 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> fvc </td>
   <td style="text-align:right;"> 1.034 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">5.1e-04</span> </td>
   <td style="text-align:right;"> 1.015 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.3e-01</span> </td>
   <td style="text-align:right;"> 1.007 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.6e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> height </td>
   <td style="text-align:right;"> 1.097 </td>
   <td style="text-align:right;"> 0.017 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">7.0e-09</span> </td>
   <td style="text-align:right;"> 1.019 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.1e-01</span> </td>
   <td style="text-align:right;"> 1.032 </td>
   <td style="text-align:right;"> 0.012 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.5e-03</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> hyper </td>
   <td style="text-align:right;"> 1.017 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">7.9e-02</span> </td>
   <td style="text-align:right;"> 1.011 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.3e-01</span> </td>
   <td style="text-align:right;"> 1.000 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">9.6e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> imp </td>
   <td style="text-align:right;"> 1.012 </td>
   <td style="text-align:right;"> 0.011 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.8e-01</span> </td>
   <td style="text-align:right;"> 0.983 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">9.5e-02</span> </td>
   <td style="text-align:right;"> 0.988 </td>
   <td style="text-align:right;"> 0.010 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.1e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> neur </td>
   <td style="text-align:right;"> 1.001 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">9.4e-01</span> </td>
   <td style="text-align:right;"> 0.995 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.0e-01</span> </td>
   <td style="text-align:right;"> 0.997 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">6.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> pulse </td>
   <td style="text-align:right;"> 1.028 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: red !important;">2.8e-03</span> </td>
   <td style="text-align:right;"> 1.012 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.8e-01</span> </td>
   <td style="text-align:right;"> 1.015 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">9.9e-02</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> quals </td>
   <td style="text-align:right;"> 1.020 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.8e-02</span> </td>
   <td style="text-align:right;"> 1.014 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.5e-01</span> </td>
   <td style="text-align:right;"> 1.012 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.2e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> reaction </td>
   <td style="text-align:right;"> 1.013 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">7.4e-02</span> </td>
   <td style="text-align:right;"> 1.008 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.4e-01</span> </td>
   <td style="text-align:right;"> 1.006 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sbp </td>
   <td style="text-align:right;"> 1.010 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.7e-01</span> </td>
   <td style="text-align:right;"> 1.000 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">9.8e-01</span> </td>
   <td style="text-align:right;"> 0.999 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">9.2e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;"> snoring </td>
   <td style="text-align:right;"> 1.010 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.7e-01</span> </td>
   <td style="text-align:right;"> 1.007 </td>
   <td style="text-align:right;"> 0.008 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.3e-01</span> </td>
   <td style="text-align:right;"> 1.008 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.8e-01</span> </td>
  </tr>
</tbody>
</table>
<tfoot><tr><td style="padding: 0; " colspan="100%">
<span style="font-style: italic;">Note: </span> <sup></sup> p-values are based on the wald test that compares the ldsc regression line intercept against 1. p-values &lt;= Bonferroni corrected alpha (i.e., 0.05/14) are highlighted</td></tr></tfoot>
</div>

### Haseman Elston Regression

<div style="border: 0px;overflow-x: scroll; width:100%; border-bottom: 0;">
<table class=" lightable-minimal" style='font-family: "Trebuchet MS", verdana, sans-serif; margin-left: auto; margin-right: auto;'>
<caption>(\#tab:he-control) Control GWASs: Heritability estimates (SE) using Haseman Elston regression under assumed heritability models</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> trait </th>
   <th style="text-align:right;"> right_est </th>
   <th style="text-align:right;"> right_sd </th>
   <th style="text-align:right;"> left_est </th>
   <th style="text-align:right;"> left_sd </th>
   <th style="text-align:right;"> all_est </th>
   <th style="text-align:right;"> all_sd </th>
   <th style="text-align:left;"> p_inflation </th>
  </tr>
 </thead>
<tbody>
  <tr grouplength="14"><td colspan="8" style="background-color: #666; color: #fff;"><strong>under gcta</strong></td></tr>
<tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> awake </td>
   <td style="text-align:right;"> 0.034 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.030 </td>
   <td style="text-align:right;"> 0.003 </td>
   <td style="text-align:right;"> 0.062 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> bmi </td>
   <td style="text-align:right;"> 0.135 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.124 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.251 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> chron </td>
   <td style="text-align:right;"> 0.061 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.048 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.104 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> ever </td>
   <td style="text-align:right;"> 0.034 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.040 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.071 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> fvc </td>
   <td style="text-align:right;"> 0.098 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.103 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.194 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.4e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> height </td>
   <td style="text-align:right;"> 0.274 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:right;"> 0.264 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.522 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">8.7e-02</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> imp </td>
   <td style="text-align:right;"> 0.142 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.142 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.276 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> neur </td>
   <td style="text-align:right;"> 0.064 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.055 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.115 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.3e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> pulse </td>
   <td style="text-align:right;"> 0.084 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.077 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.155 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.5e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> quals </td>
   <td style="text-align:right;"> 0.084 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.089 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.168 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.4e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> reaction </td>
   <td style="text-align:right;"> 0.037 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.040 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.075 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> sbp </td>
   <td style="text-align:right;"> 0.086 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.073 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.154 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">2.8e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> snoring </td>
   <td style="text-align:right;"> 0.038 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.032 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.067 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> hyper </td>
   <td style="text-align:right;"> 0.057 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.046 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.100 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.2e-01</span> </td>
  </tr>
  <tr grouplength="14"><td colspan="8" style="background-color: #666; color: #fff;"><strong>under ldak-thin</strong></td></tr>
<tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> awake </td>
   <td style="text-align:right;"> 0.033 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.031 </td>
   <td style="text-align:right;"> 0.003 </td>
   <td style="text-align:right;"> 0.062 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.2e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> bmi </td>
   <td style="text-align:right;"> 0.134 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.122 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.251 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> chron </td>
   <td style="text-align:right;"> 0.059 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.047 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.104 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> ever </td>
   <td style="text-align:right;"> 0.032 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.039 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.071 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> fvc </td>
   <td style="text-align:right;"> 0.094 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.101 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.194 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.3e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> height </td>
   <td style="text-align:right;"> 0.257 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.249 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.522 </td>
   <td style="text-align:right;"> 0.009 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">9.0e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> imp </td>
   <td style="text-align:right;"> 0.138 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 0.135 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.276 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> neur </td>
   <td style="text-align:right;"> 0.069 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:right;"> 0.055 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.115 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">1.9e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> pulse </td>
   <td style="text-align:right;"> 0.081 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.074 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.155 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.6e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> quals </td>
   <td style="text-align:right;"> 0.083 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.088 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.168 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.8e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> reaction </td>
   <td style="text-align:right;"> 0.036 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.038 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.075 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.1e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> sbp </td>
   <td style="text-align:right;"> 0.085 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:right;"> 0.072 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.154 </td>
   <td style="text-align:right;"> 0.007 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">3.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> snoring </td>
   <td style="text-align:right;"> 0.035 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.033 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.067 </td>
   <td style="text-align:right;"> 0.005 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">4.7e-01</span> </td>
  </tr>
  <tr>
   <td style="text-align:left;padding-left: 2em;" indentlevel="1"> hyper </td>
   <td style="text-align:right;"> 0.055 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.045 </td>
   <td style="text-align:right;"> 0.004 </td>
   <td style="text-align:right;"> 0.100 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:left;"> <span style="     color: black !important;">5.0e-01</span> </td>
  </tr>
</tbody>
</table>
<tfoot><tr><td style="padding: 0; " colspan="100%">
<span style="font-style: italic;">Note: </span> <sup></sup> p-values are for testing whether the sum of 'left' and 'right' heritability estimates are greater than the heritability estimate based on the whole genome. p-values &lt;= Bonferroni corrected alpha (i.e., 0.05/14) are highlighted</td></tr></tfoot>
</div>




<!--chapter:end:01-summaries.Rmd-->


# Good VS Bad GWAS

Here we investigate the inflation in test statistics from the bad GWASs. We want to find out 1) if the inflation is constant; 2) if it can be predicted in some way.  

## test statistics ~ LD score

The inflation in GWAS test statistics due to confouding is not always constant, as assumed by LDSC. Below we plotted the chi-square test statistics from the bad (colored in red), good (orange), and control (gray) GWASs as a function of LD score ( Fig \@ref(fig:good-vs-bad-by-ldscorebin) ). The linear model assumed by LDSC is only appropriate for four traits, namely chron, pulse, quals, and snoring. For these traits, LDSC accounts for the inflation due to confounding (i.e., intercept) and the inflation due to polygenity (i.e., slope). However, LDSC is inadequate for other traits, where the inflation is non-linear. More specifically, for these traits, the test statistics for SNPs with a low LD score are much more inflated than would be expected by LDSC. Thus, although LDSC can detect the inflation in GWAS test statistics due to confounding, it cannot be used to correct the inflation for some traits.







<div class="figure" style="text-align: left">
<img src="fig-to-insert/gwas-stat-by-ldscbin.png" alt="Test statistics by LD score for good, bad and control GWASs." width="100%" />
<p class="caption">(\#fig:good-vs-bad-by-ldscorebin)Test statistics by LD score for good, bad and control GWASs.</p>
</div>

## New models?

1. * $S_{j} = 1 + n_{j}a +  n_{j}\sum_{i}^{m}(r_{ji}^2 \ q_{i}/Q) h^2_{snp} + \epsilon_{j}$
      * under GCTA: $q_{i}=1, Q=\sum_{i}^{m}q_i=m$ 
      * under LDAK-thin: $q_{i}=I_{i}[f_{i}(1-f_{i})]^{0.75}$

1. * $S_{j} = 1 + n_{j}a +  n_{j}\sum_{i}^{k}(r_{ji}^2 \ q_{i}/Q) h^2_{snp} + n_j \sum ()h + \epsilon_{j}$
      * under GCTA: $q_{i}=1, Q=\sum_{i}^{m}q_i=m$ 
      * under LDAK-thin: $q_{i}=I_{i}[f_{i}(1-f_{i})]^{0.75}$


## LD scores based on individual level data


```bash

mkdir ldsc/ldscore-individual-data
mkdir gen/snps-unrel-inds-by-chrom

# divide snp list by chrom
for i in {1..22}; do
awk -v i=$i '{split($0, a , ":")} (a[1]==i) {print $0}' gen/snps-unrel-inds.use > gen/snps-unrel-inds-by-chrom/snps-unrel-inds-$i
done

# compute LD scores

for i in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 80G
#SBATCH -c 2
#SBATCH -t 10:0:0

.././ldsc.py \
      --bfile ../../gen/geno-mix \
      --extract ../../gen/snps-unrel-inds-by-chrom/snps-unrel-inds-$i \
      --keep ../../mix-pop-gwas.id \
      --l2 \
      --ld-wind-cm 1 \
      --out ../ldscore-individual-data/ldsc-mix-$i
"> sh_script/compute-ldsc-$i.sh
done

for i in {1..22}; do
sbatch -A snpher ../sh_script/compute-ldsc-$i.sh
done > ../../job-records/compute-ldsc-mix

# check job completion
file=job-records/compute-ldsc-mix
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

```




## Test the new model

Inflation = np^2 (h2-h2local) <=np^2

1. extract heritability estimates from good GWASs
2. n*p2*h2

3. $S_{true}$ vs $\hat{S}_{ldsc}$ vs $\hat{S}_{new}$ 


```r
# extract heritability estimates
h2_gcta=read.table('summary/reml-gcta-inflation-good-gwas.txt', header=T, 
                stringsAsFactors = F)
h2_ldak=read.table('summary/reml-ldak-thin-inflation-good-gwas.txt', header=T, 
                stringsAsFactors = F)

# average r_ij^2
r2_good=read.table("inflation/summary/ave-r2-1k-snps-goodgwas", header=F)
r2_bad=read.table("inflation/summary/ave-r2-1k-snps-badgwas", header=F)
r2_good=mean(r2_good$V1)
r2_bad=mean(r2_bad$V1)

# ldsc intercept estimates
ldsc_bad=read.table("summary/ldsc-mix", header=F, stringsAsFactors = F)

# GWAS test stats & LD scores

require("vroom")
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals",
        "reaction","sbp","snoring","hyper")
# c("fcv", "height","imp")

png(paste0("fig/predcited-chisq-by-ldscbin.png"),
    width =40, height = 30, units = "cm", res=600)
par(mfrow=c(3,4))

for(i in 1:length(traits)){
  
n=100000
trait=traits[i]
ldsc_intercept=ldsc_bad$V2[ldsc_bad$V1==trait]
h2=h2_ldak$all_est[h2_ldak$code==trait]
  
dat=vroom(paste0("summary/",trait,"-gwas-test-stats-compare2.txt"), col_names=T)
dat$chisq_ldsc=dat$good_chisq+ldsc_intercept-1
dat$chisq_new=dat$good_chisq+n*r2_bad*h2

out=data.frame(ave_ldscore=tapply(dat$ldsc_ref,INDEX=dat$ldsc_ref_bin, mean),
               ave_chisq_ldsc=tapply(dat$chisq_ldsc,INDEX=dat$ldsc_ref_bin, mean),
               ave_chisq_new=tapply(dat$chisq_new,INDEX=dat$ldsc_ref_bin, mean),
               ave_good_chisq=tapply(dat$good_chisq,INDEX=dat$ldsc_ref_bin, mean),
               ave_bad_chisq=tapply(dat$bad_chisq,INDEX=dat$ldsc_ref_bin, mean))

end=round(max(c(out[,2], out[,3], out[,4]), out[,5]),0)
start=round(min(c(out[,2], out[,3], out[,4]), out[,5]),0)

plot(out$ave_ldscore, out$ave_bad_chisq,
     xlab="ldscore bin", ylab="mean chisquare", 
     ylim=c(start, end),
     main=trait, las=1,
     cex = 1.5, pch=21,  bg="grey", col="white", lwd=0.5)
points(out$ave_ldscore, out$ave_chisq_new,
       cex = 1.5, pch=21, col="white", bg="orange")
points(out$ave_ldscore, out$ave_chisq_ldsc,
       cex = 1.5, pch=21, col="white", bg="red")
#points(out$ave_ldscore, out$ave_good_chisq,
#       cex = 1.5, pch=21, col="white", bg="gray")
}
dev.off()
```

## predictors of inflation?

Potential predictors considered:
1. MAF
2. ave r^2_ij for each j cross chromsomes [10k SNPs]


```bash

# prep: convert snp labels to rs system
awk '(NR==FNR){a[$1]; b[$1]=$2; next}($1 in a){print b[$1], $2, $3, $5}' doug/ukbb.ldsc gen/geno-unrel.stats > gen/geno-unrel-rs.maf

R
require("vroom")
# files for maf & ave r2 by snp
stat=vroom("gen/geno-unrel-rs.maf", col_names=F)
r2_good=vroom("summary/ave-r2-by-snp-goodgwas-rs", col_names=F)
r2_bad=vroom("summary/ave-r2-by-snp-badgwas-rs", col_names=F)

# compute inflation in chisq (good vs. bad) by trait
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(i in 1:length(traits)){
  
trait=traits[i]
dat=vroom(paste0("summary/",trait,"-gwas-test-stats-compare2.txt"), col_names=T)
inflation=data.frame(snp=dat$SNP,
                     inflation=dat$bad_chisq-dat$good_chisq,
                     bad_chisq=dat$bad_chisq,
                     good_chisq=dat$good_chisq,
                     stringsAsFactors = F)

m1=match(inflation$snp, stat$X1)
m2=match(inflation$snp,r2_good$X1)
m3=match(inflation$snp,r2_bad$X1)
out=data.frame(inflation,
               maf=stat$X4[m1], 
               r2_good=r2_good$X2[m2],
               r2_bad=r2_bad$X2[m2], stringsAsFactors = F)

# bin maf & r2_bad according to quantiles
cutoff1=quantile(out$maf, probs = seq(0, 1, 0.005), na.rm=T)
cutoff2=quantile(out$r2_bad, probs = seq(0, 1, 0.005), na.rm=T)

out$maf_bin=cut(out$maf, breaks=cutoff1, labels=1:(length(cutoff1)-1))
out$r2_bad_bin=cut(out$r2_bad, breaks=cutoff2, labels=1:(length(cutoff2)-1))

write.table(out,paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), 
            col.names=T, row.names=F, quote=F)
}

# plot by maf
require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", 
        "reaction","sbp","snoring","hyper")
# "fcv", "height","imp",
png(paste0("fig/inflation-by-mafbin.png"),
    width =40, height = 30, units = "cm", res=600)
par(mfrow=c(3,4))
for(i in 1:length(traits)){
trait=traits[i]
dat=vroom(paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), col_names=T)
sel=dat[,c("snp","inflation","maf","maf_bin")]
sel=sel[complete.cases(sel),]
out=data.frame(inflation=tapply(sel$inflation,INDEX=sel$maf_bin, mean))
out$maf_bin=1:dim(out)[1]
out$maf_bin_val=tapply(sel$maf,INDEX=sel$maf_bin, mean)
  
plot(out$maf_bin_val, out$inflation,
     xlab="maf bin", ylab="mean inflation",
     main=trait, las=1,
     cex = 1.5, pch=21,  bg="grey", col="white", lwd=0.5)
}
dev.off()

# plot by r2_bad bin

require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", 
        "reaction","sbp","snoring","hyper")
# "fcv", "height","imp",
png(paste0("fig/inflation-by-aver2.png"),
    width =40, height = 30, units = "cm", res=600)
par(mfrow=c(3,4))
for(i in 1:length(traits)){
trait=traits[i]
dat=vroom(paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), col_names=T)
sel=dat[,c("snp","inflation","r2_bad","r2_bad_bin")]
sel=sel[complete.cases(sel),]
out=data.frame(inflation=tapply(sel$inflation,INDEX=sel$r2_bad_bin, mean))
out$r2_bad_bin=1:dim(out)[1]
out$r2_bad_bin_val=tapply(sel$r2_bad,INDEX=sel$r2_bad_bin, mean)
  
plot(out$r2_bad_bin_val, out$inflation,
     xlab="ave r2 bin", ylab="mean inflation",
     main=trait, las=1,
     cex = 1.5, pch=21,  bg="grey", col="white", lwd=0.5)
}
dev.off()

```



```r
## inflation by raw ave r2 
require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", 
        "reaction","sbp","snoring","hyper")
# "fvc", "height","imp",
png(paste0("fig/inflation-by-aver2-raw.png"),
    width =40, height = 30, units = "cm", res=600)
par(mfrow=c(3,4))
for(i in 1:length(traits)){
trait=traits[i]
dat=vroom(paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), col_names=T)
sel=dat[,c("snp","inflation","r2_bad")]
sel=sel[complete.cases(sel),]
  
plot(sel$r2_bad, sel$inflation,
     xlab="ave r2", ylab="inflation",
     main=trait, las=1,
     cex = 1.5, pch=21,  bg="grey", col="white", lwd=0.5)
}
dev.off()


###
require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", 
        "reaction","sbp","snoring","hyper")
# "fcv", "height","imp",
png(paste0("fig/inflation-by-aver2-goodgwas-raw.png"),
    width =40, height = 30, units = "cm", res=600)
par(mfrow=c(3,4))
for(i in 1:length(traits)){
trait=traits[i]
dat=vroom(paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), col_names=T)
sel=dat[,c("snp","inflation","r2_good")]
sel=sel[complete.cases(sel),]
  
plot(sel$r2_good, sel$inflation,
     xlab="ave r2", ylab="inflation",
     main=trait, las=1,
     cex = 1.5, pch=21,  bg="grey", col="white", lwd=0.5)
}
dev.off()


## histogram
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", 
        "reaction","sbp","snoring","hyper")
# "fcv", "height","imp",
png(paste0("fig/hist-aver2-goodgwas.png"),
    width =40, height = 30, units = "cm", res=600)
par(mfrow=c(3,4))
for(i in 1:length(traits)){
trait=traits[i]
dat=vroom(paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), col_names=T)
sel=dat[,c("snp","inflation","r2_good")]
sel=sel[complete.cases(sel),]
  
hist(sel$r2_good, breaks=100, main=trait)
}
dev.off()
```

## regress bad chisq ~ mean r^2_j


```r
#::::::::
# by binned ave r2
#:::::::

# bad chisq by binned ave r2 
require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")
png(paste0("fig/badgwas-chisq-by-aver2-bin.png"),
    width = 50, height = 30, units = "cm", res=600)
par(mfrow=c(3,5))
for(i in 1:length(traits)){
trait=traits[i]
dat=vroom(paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), col_names=T)
sel=dat[,c("snp","bad_chisq","r2_bad","r2_bad_bin")]
sel=sel[complete.cases(sel),]
out=data.frame(bad_chisq=tapply(sel$bad_chisq,INDEX=sel$r2_bad_bin, mean))
out$r2_bad_bin=1:dim(out)[1]
out$r2_bad_bin_val=tapply(sel$r2_bad,INDEX=sel$r2_bad_bin, mean)

plot(out$r2_bad_bin_val, out$bad_chisq,
     xlab="ave r2", ylab="chisq test stat",
     main=trait, las=1,
     cex = 1.5, pch=21,  bg="grey", col="white", lwd=0.5)
}
dev.off()

#::::::::
# by raw ave r2
#:::::::

# bad chisq by raw ave r2 
require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp",
        "reaction","sbp","snoring","hyper")

png(paste0("fig/badgwas-chisq-by-aver2.png"),
    width =50, height = 30, units = "cm", res=600)
par(mfrow=c(3,5))
for(i in 1:length(traits)){
trait=traits[i]
dat=vroom(paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), col_names=T)
sel=dat[,c("snp","bad_chisq","r2_bad")]
sel=sel[complete.cases(sel),]
  
plot(sel$r2_bad, sel$bad_chisq,
     xlab="ave r2", ylab="chisq test stat",
     main=trait, las=1,
     cex = 1.5, pch=21,  bg="grey", col="white", lwd=0.5)
}
dev.off()

# good chisq by raw ave r2 
require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp",
        "reaction","sbp","snoring","hyper")

png(paste0("fig/goodgwas-chisq-by-aver2.png"),
    width =50, height = 30, units = "cm", res=600)
par(mfrow=c(3,5))
for(i in 1:length(traits)){
trait=traits[i]
dat=vroom(paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), col_names=T)
sel=dat[,c("snp","good_chisq","r2_good")]
sel=sel[complete.cases(sel),]
  
plot(sel$r2_good, sel$good_chisq,
     xlab="ave r2", ylab="chisq test stat",
     main=trait, las=1,
     cex = 1.5, pch=21,  bg="grey", col="white", lwd=0.5)
}
dev.off()

# regression: chisq test stats ~ ave r^2_j --------------------------------------
require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp",
        "reaction","sbp","snoring","hyper")

for(i in 1:length(traits)){
trait=traits[i]
dat=vroom(paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), col_names=T)
sel=dat[,c("snp","bad_chisq","good_chisq","r2_bad", "r2_good", "maf")]
sel=sel[complete.cases(sel),]
sel$maf2=sel$maf^2

mod1=lm(sel$bad_chisq~sel$r2_bad)
#mod1.1=lm(sel$bad_chisq~sel$maf)
#mod1.2=lm(sel$bad_chisq~ sel$maf + sel$maf2)
#mod1.3=lm(sel$bad_chisq~sel$r2_bad+sel$maf)
#mod1.4=lm(sel$bad_chisq~sel$r2_bad+sel$maf+sel$maf2)
mod2=lm(sel$good_chisq~sel$r2_good)

slope0=data.frame(trait=trait,
                  slope_bad=coef(mod1)[2],
                  p_bad=summary(mod1)$coefficients[,4][2],
                  slope_good=coef(mod2)[2],
                  p_good=summary(mod2)$coefficients[,4][2],
                  stringsAsFactors = F)

if(i==1){slope=slope0} else {slope=rbind(slope,slope0)}
}

slope$good_div_n=slope$slope_good/100000
slope$bad_div_n=slope$slope_bad/100000

# heritability estimates
h2_gcta=read.table('summary/reml-gcta-inflation-good-gwas.txt', header=T, 
                stringsAsFactors = F)
h2_ldak=read.table('summary/reml-ldak-thin-inflation-good-gwas.txt', header=T, 
                stringsAsFactors = F)
h2=data.frame(trait=h2_gcta$code,gcta=h2_gcta$all_est, ldak=h2_ldak$all_est)

# together
m=match(slope$trait,h2$trait)
all=cbind(slope,h2[m,-1])
```


<!--chapter:end:02-inflation.Rmd-->


# Mathematical Details

Here we detail the mathematics behind the project.

## $tr(K_{right}K_{left})$ 


## verification

Here we want to verify the followings:

  1. $r^2_{i,j} \overset{H_0}{\sim} beta (\alpha=(k-1)/2, \beta=(n-k)/2)$, where k= # of regressors including the intercept (so for an univariate regression, k=2), and n = sample size.
  * $E[r^2_{i,j}]=\alpha/(\alpha + \beta)$
  * $var[r^2_{i,j}]=\alpha \beta/[(\alpha+\beta)^2(\alpha+\beta+1)]$
  
  2.  $T = \sum_{i,j}^{m_1,m_2} r^2_{i,j} \overset{H_0}{\sim} N(1/n, var[r^2_{i,j}])$

To do so, we computed $r^2_{i,j}=cor(x_i, x_j)^2$ for $n = 1k , m_1=m_2=1k$ in following ways.

  1. simulated data: $x \sim N(0,1)$
  
  2. real genotype data: $x$ from two chromosomes.

We expect to see:

  1. $r^2_{i,j} \overset{H_0}{\sim} beta (\alpha=0.5, \beta=499)$
  2. $T \overset{H_0}{\sim} N(1/1000, var[r^2_{i,j}])$
  

```r
#::
# verify distribution of r^2
#::

n=100000
k=2
alpha=(k-1)/2
beta=(n-k)/2
m=1000 

for(i in 1:m){
  x1=rnorm(n)
  x2=rnorm(n)
  y=cor(x1,x2)^2
  if(i==1){out=y} else {out=c(out,y)}
}

x=seq(0.00000001,0.0015,0.000001)
density=dbeta(x, alpha, beta)
options(scipen=0)
hist(out, breaks=100, freq=F, main="", 
     col="lightgray", border="lightgray",
     xlab="r^2", las=1)
lines(x, density, col="red")
#mean(out)
#1/n
exp=alpha/(alpha+beta)
var=alpha*beta/(((alpha+beta)^2)*(alpha+beta+1))
exp
var

exp*n
var*n
```

2. using real data



<!--chapter:end:03-maths.Rmd-->


# chi square ~ $\overline{r^2_j}$

New measure of inflation, $\overline{r^2_j}$ - average correlation between SNP j and distant SNPs.

1. Check if the maths in ldak function is correct. Use --make-snps to generate clean data, and check estimates of average r2j are not significant.

We did 10 runs and results are non-significant.


```bash

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 10
#SBATCH -t 5:00:0

for i in {1..10}; do
  
  dir=../inflation/random-snps
  
  # simulate random snps -------------------------------------------------------
  ./ldak5.1 --make-snps \$dir/20k-snps-100k-inds --num-samples 100000 --num-snps 20000
  
  # create snp lists -----------------------------------------------------------
  awk 'NR <= 10000 {print \$2}' \$dir/20k-snps-100k-inds.bim > \$dir/lista
  awk 'NR > 10000 {print \$2}' \$dir/20k-snps-100k-inds.bim > \$dir/listb
  
  # compute r_ij --------------------------------------------------------------
  ./ldak5.2 --max-threads 10 \
            --calc-inflation \$dir/out/20k-random-snps-100k-inds \
            --bfile \$dir/20k-snps-100k-inds \
            --lista \$dir/lista \
            --listb \$dir/listb
done
">sh_script/calc-ave-r2j

# submit the job
sbatch -A snpher ../sh_script/calc-ave-r2j >../job-records/calc-ave-r2j-random-snps

# results
grep "Average squared correlation" sh_out/calc-ave-r2j-60721312.out > inflation/random-snps/out/ave-r2j.out

```

## $\overline{r^2_j}$ & choice of SNPs 

### number of SNPs

2. Ideally, we show $\overline{r^2_j}$ is robust - e.g., it is not sensitive to choice of SNPs (e.g., randomly pick 1000, then a different 1000). It is not sensitive to MAF threshold (e.g., if you restrict to MAF>.1, MAF>.01, MAF>.001).

To test the robustness of $\overline{r^2_j}$, we randomly chose 1k SNPs from chromosomes 1-7 and computed the $\overline{r^2_j}$ for these SNPs using 1k, 5k, 10k and 20k SNPs chosen randomly from chromosomes 8-22. We resampled the SNPs and repeated the calculation for 100 times.

So we want to test if $\overline{r^2_j}$ is sensitive to 
  * the choice of SNPs? var(r^2_j) for a given j. 
  * the number of SNPs? 1k, 5k, 10k, 20k
  * the MAF threshold? MAF > .1, MAF > 0.01, MAF > 0.001
  
  * For MAF > 0.01, $\overline{r^2_j}$ is not sensitive to the choice of SNPs and slightly sensitive to the number of SNPs. Based on different set of SNPs, $\overline{r^2_j}$ align well. The alignment is better for m > 10k than for m < 10k. 


```bash

#:::
# define a R function to compute ave r_j^2 for each i
# To be used below
#:::

options(stringsAsFactors=FALSE)
ip<-commandArgs(trailingOnly=TRUE)
options(warn=1)

compute_ave_r2=function(m,nm,maf){

m=as.numeric(m)
nm=as.character(nm)
maf=as.character(maf)
dir=paste0("../inflation/sensitivity/maf",maf)

require(vroom)
names=c("good", "bad")

for(i in 1:length(names)){
gwas=names[i]
dat=vroom(paste0(dir,"/out/", nm,"-maf",maf,"-snps-", gwas,".pairwise"), col_names=F)
dat=dat[,-c(m+1)]^2
out=t(data.frame(ave_r2=apply(dat,1, mean)))
write.table(out,paste0(dir,"/ave-r2-",nm,"-",gwas), col.names=F, row.names=F, quote=F, append=T)
 }
}

compute_ave_r2(ip[1],ip[2],ip[3])

#:::
# 0. compute r^2
#:::

# lista: 1k from chrom1-7. This is list is fixed for all conditions
dir=inflation/sensitivity
maf=.01
awk '{split($1, a, ":"); if (a[1]<8) print $1 }' gen/snps-unrel-inds.use | shuf | head -n 1000 >$dir/lista-maf$maf

#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# m=1k & maf = .01 
#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# to change
nm=1k
maf=.01

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 2G
#SBATCH -c 10
#SBATCH -t 20:00:0

#:::
# 0. define vars & create snp lists
#:::

for i in {1..100}; do

# to change
use=../gen/snps-unrel-inds.use
m=1000
nm=1k
maf=.01
lista=../inflation/sensitivity/lista-maf\$maf
dir=../inflation/sensitivity/maf\$maf
listb=\$dir/listb-\$nm-maf\$maf


  #::
  # 1. select list b [to be recycled]
  #::
  
  awk '{split(\$1, a, \":\"); if (a[1]>=8) print \$1 }' \$use | shuf | head -n \$m >\$listb
  
  #::
  # 2. good gwas
  #::
  
  id=../unrelated/rand.100000
  bfile=../gen/geno-unrel
  out=\$nm-maf\$maf-snps-good
    
  ./ldak5.2 --max-threads 10 \
            --calc-inflation \$dir/out/\$out \
            --bfile \$bfile \
            --keep \$id \
            --lista \$lista \
            --listb \$listb 
  
  #::
  # 3. bad gwas
  #::
  
  id=../mix-pop-gwas.id
  bfile=../gen/geno-mix
  out=\$nm-maf\$maf-snps-bad
    
  ./ldak5.2 --max-threads 10 \
            --calc-inflation \$dir/out/\$out \
            --bfile \$bfile \
            --keep \$id \
            --lista \$lista \
            --listb \$listb 

  #::
  # 4. compute ave r2 [each column corresponds to a SNP in lista]
  #::
  
  Rscript --vanilla ../inflation/sensitivity/calc-ave-r2.r \$m \$nm \$maf
  
done

">sh_script/calc-ave-r2-$nm-maf$maf

# submit the job
nm=1k
maf=.01
sbatch -A snpher ../sh_script/calc-ave-r2-$nm-maf$maf >../job-records/calc-ave-r2-$nm-maf$maf

#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# m=5k & maf = .01
#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# to change
nm=5k
maf=.01

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 10G
#SBATCH -c 10
#SBATCH -t 20:00:0

#:::
# 0. define vars & create snp lists
#:::

for i in {1..100}; do

# to change
use=../gen/snps-unrel-inds.use
m=5000
nm=5k
maf=.01
lista=../inflation/sensitivity/lista-maf\$maf
dir=../inflation/sensitivity/maf\$maf
listb=\$dir/listb-\$nm-maf\$maf


  #::
  # 1. select list b [to be recycled]
  #::
  
  awk '{split(\$1, a, \":\"); if (a[1]>=8) print \$1 }' \$use | shuf | head -n \$m >\$listb
  
  #::
  # 2. good gwas
  #::
  
  id=../unrelated/rand.100000
  bfile=../gen/geno-unrel
  out=\$nm-maf\$maf-snps-good
    
  ./ldak5.2 --max-threads 10 \
            --calc-inflation \$dir/out/\$out \
            --bfile \$bfile \
            --keep \$id \
            --lista \$lista \
            --listb \$listb 
  
  #::
  # 3. bad gwas
  #::
  
  id=../mix-pop-gwas.id
  bfile=../gen/geno-mix
  out=\$nm-maf\$maf-snps-bad
    
  ./ldak5.2 --max-threads 10 \
            --calc-inflation \$dir/out/\$out \
            --bfile \$bfile \
            --keep \$id \
            --lista \$lista \
            --listb \$listb 

  #::
  # 4. compute ave r2 [each column corresponds to a SNP in lista]
  #::
  
  Rscript --vanilla ../inflation/sensitivity/calc-ave-r2.r \$m \$nm \$maf
  
done

">sh_script/calc-ave-r2-$nm-maf$maf

# submit the job
nm=5k
maf=.01
sbatch -A snpher ../sh_script/calc-ave-r2-$nm-maf$maf >../job-records/calc-ave-r2-$nm-maf$maf

#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# m=10k & maf = .01
#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# to change
nm=10k
maf=.01

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 10
#SBATCH -t 20:00:0

#:::
# 0. define vars & create snp lists
#:::

for i in {1..100}; do

# to change
use=../gen/snps-unrel-inds.use
m=10000
nm=10k
maf=.01
lista=../inflation/sensitivity/lista-maf\$maf
dir=../inflation/sensitivity/maf\$maf
listb=\$dir/listb-\$nm-maf\$maf


  #::
  # 1. select list b [to be recycled]
  #::
  
  awk '{split(\$1, a, \":\"); if (a[1]>=8) print \$1 }' \$use | shuf | head -n \$m >\$listb
  
  #::
  # 2. good gwas
  #::
  
  id=../unrelated/rand.100000
  bfile=../gen/geno-unrel
  out=\$nm-maf\$maf-snps-good
    
  ./ldak5.2 --max-threads 10 \
            --calc-inflation \$dir/out/\$out \
            --bfile \$bfile \
            --keep \$id \
            --lista \$lista \
            --listb \$listb 
  
  #::
  # 3. bad gwas
  #::
  
  id=../mix-pop-gwas.id
  bfile=../gen/geno-mix
  out=\$nm-maf\$maf-snps-bad
    
  ./ldak5.2 --max-threads 10 \
            --calc-inflation \$dir/out/\$out \
            --bfile \$bfile \
            --keep \$id \
            --lista \$lista \
            --listb \$listb 

  #::
  # 4. compute ave r2 [each column corresponds to a SNP in lista]
  #::
  
  Rscript --vanilla ../inflation/sensitivity/calc-ave-r2.r \$m \$nm \$maf
  
done

">sh_script/calc-ave-r2-$nm-maf$maf

# submit the job
nm=10k
maf=.01
sbatch -A snpher ../sh_script/calc-ave-r2-$nm-maf$maf >../job-records/calc-ave-r2-$nm-maf$maf

#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# m=20k & maf = .01
#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# to change
nm=20k
maf=.01

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 10
#SBATCH -t 20:00:0

#:::
# 0. define vars & create snp lists
#:::

for i in {1..100}; do

# to change
use=../gen/snps-unrel-inds.use
m=20000
nm=20k
maf=.01
lista=../inflation/sensitivity/lista-maf\$maf
dir=../inflation/sensitivity/maf\$maf
listb=\$dir/listb-\$nm-maf\$maf


  #::
  # 1. select list b [to be recycled]
  #::
  
  awk '{split(\$1, a, \":\"); if (a[1]>=8) print \$1 }' \$use | shuf | head -n \$m >\$listb
  
  #::
  # 2. good gwas
  #::
  
  id=../unrelated/rand.100000
  bfile=../gen/geno-unrel
  out=\$nm-maf\$maf-snps-good
    
  ./ldak5.2 --max-threads 10 \
            --calc-inflation \$dir/out/\$out \
            --bfile \$bfile \
            --keep \$id \
            --lista \$lista \
            --listb \$listb 
  
  #::
  # 3. bad gwas
  #::
  
  id=../mix-pop-gwas.id
  bfile=../gen/geno-mix
  out=\$nm-maf\$maf-snps-bad
    
  ./ldak5.2 --max-threads 10 \
            --calc-inflation \$dir/out/\$out \
            --bfile \$bfile \
            --keep \$id \
            --lista \$lista \
            --listb \$listb 

  #::
  # 4. compute ave r2 [each column corresponds to a SNP in lista]
  #::
  
  Rscript --vanilla ../inflation/sensitivity/calc-ave-r2.r \$m \$nm \$maf
  
done

">sh_script/calc-ave-r2-$nm-maf$maf

# submit the job
nm=20k
maf=.01
sbatch -A snpher ../sh_script/calc-ave-r2-$nm-maf$maf >../job-records/calc-ave-r2-$nm-maf$maf

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#    summary
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

require(vroom)
dir="inflation/sensitivity/maf.01/"
snps=read.table(paste0(dir,"/out/1k-maf.01-snps-bad.predictorsa"), 
                stringsAsFactors = F, header=F)
# note: the snp set for list a is constant across different m's

# extract data
m=c("1k","5k","10k", "20k")

for(i in 1:length(m)){
  
  dat=vroom(paste0(dir,"ave-r2-",m[i],"-bad"),col_names=F)
  ave0=apply(dat,2,mean)
  sd0=apply(dat,2,sd)

  if(i==1){ave=data.frame(snp=snps$V1,ave0, stringsAsFactors = F);
            names(ave)[i+1]=paste0("m_",m[i])
            sd=data.frame(snp=snps$V1,sd0, stringsAsFactors = F);
            names(sd)[i+1]=paste0("m_",m[i])
  } else {ave=cbind(ave,ave0); 
          names(ave)[i+1]=paste0("m_",m[i])
          sd=cbind(sd,sd0); 
          names(sd)[i+1]=paste0("m_",m[i])
          } 
}

# organize data
ord=order(ave$m_20k,decreasing=F)
ave=ave[ord,]
sd=sd[ord,]

cutoff=quantile(ave$m_20k, probs = seq(0, 1, 0.01))
ave$quant_bin=cut(ave$m_20k, breaks=cutoff, 
                  include.lowest=T,
                  labels=1:(length(cutoff)-1))

# check alignment of ave r^2_j for different m
require(vroom)
dir="inflation/sensitivity/maf.01/"
snps=read.table(paste0(dir,"/out/1k-maf.01-snps-bad.predictorsa"),
                stringsAsFactors = F, header=F)
# note: the snp set for list a is constant across different m's

# extract data
png("fig/ave-r2-1st-vs-2nd-run-by-m.png", res=400 , width=40, height=10, units="cm")
par(mfrow=c(1,4), pty="s")
m=c("1k","5k","10k", "20k")
for(i in 1:length(m)){
  dat=vroom(paste0(dir,"ave-r2-",m[i],"-bad"),col_names=F)
  sel=t(dat[c(5,20),])
  start=min(c(sel[,1],sel[,2]))
  end=max(c(sel[,1],sel[,2]))
  plot(sel[,1], sel[,2],
       xlim=c(start,end), ylim=c(start, end),
      xlab="1st run", ylab="2nd run", main=m[i], las=1,
     cex = 1, pch=21,  bg="darkgray", col="white", lwd=0.5)
abline(0, 1, col="darkgray", lwd=1.5, lty=1)
}
dev.off()

```

### maf 

For a given list of SNPs, We computed their correlations with distant SNPs, i.e., $\overline{r^2_j}$, using SNPs with a MAF < 0.1 and ones with a MAF > 0.1. We did it in a systematic way.

1. maf: < .1 vs. > .1.
2. level of confounding: 0k, 1k, 2k, ..., 6k.
3. number of distant SNPs: 1k, 5k, 10k, 20k.

#### 1st run


```bash
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# compute maf of mix pops
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# script file

for n in {0k,1k,2k,3k,4k,5k,6k}; do

if [ $n == 0k ]; then
  id=../rand.100000
else
  id=../mix-pop-gwas-$n-noneuro.id
fi

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 10G
#SBATCH -c 2
#SBATCH -t 5:0:0

./ldak5.1 --calc-stats ../gen/maf/geno-mix-$n-noneuro-stats \
          --bfile ../gen/geno-mix-maf.001 \
          --keep $id
"> sh_script/calc-maf-$n-noneuro.sh
done

# submit script
for n in {0k,1k,2k,3k,4k,5k,6k}; do
sbatch -A snpher ../sh_script/calc-maf-$n-noneuro.sh
done>../job-records/calc-maf

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# SNP lists
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# lista (1k): fixed for all analyses
# chosen from chrom 1-7. maf > 0.01

dir=inflation/sensitivity/maf.1
awk '{split($1, a, ":"); 
    if (a[1]<8 && $5>0.1 && $6>=0.95) 
    print $1}' gen/geno-mix-maf.001.stats | shuf | head -n 1000 > $dir/lista-1k

# listb
# this will vary depending on the mix pop
dir=inflation/sensitivity/maf.1/listb
m=(1000 5000 10000 20000)
nm=(1k 5k 10k 20k)

for n in {0k,1k,2k,3k,4k,5k,6k}; do
for i in {0..3}; do
infile=gen/maf/geno-mix-$n-noneuro-stats.stats
# maf > 0.1
awk '{split($1, a, ":"); 
    if (a[1]>=8 && $5>=0.1 && $6>=0.95) 
    print $1}' $infile | shuf | head -n ${m[$i]} > $dir/listb-maf-g.1-${nm[$i]}-snps-mix-pop-$n-noneuro 
# maf < 0.1
awk '{split($1, a, ":"); 
    if (a[1]>=8 && $5>0.01 && $5<0.1 && $6>=0.95) 
    print $1}' $infile | shuf | head -n ${m[$i]} > $dir/listb-maf-l.1-${nm[$i]}-snps-mix-pop-$n-noneuro  
done
done

# actually it is easier to create pool of the right for maf > .1 & maf<.1.
# then we can choose a completely different listb for another run. So let's do that
dir=inflation/sensitivity/maf.1/listb
for n in {0k,1k,2k,3k,4k,5k,6k}; do
infile=gen/maf/geno-mix-$n-noneuro-stats.stats
# maf > 0.1
awk '{split($1, a, ":"); 
    if (a[1]>=8 && $5>=0.1 && $6>=0.95 && NR >1 ) 
    print $1}' $infile > $dir/right-maf-g.1-mix-pop-$n-noneuro 
# maf < 0.1
awk '{split($1, a, ":"); 
    if (a[1]>=8 && $5>0.01 && $5<0.1 && $6>=0.95 && NR >1) 
    print $1}' $infile > $dir/right-maf-l.1-mix-pop-$n-noneuro  
done

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# compute ave r^2_j
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# compute r_ij using ldak-------------------------------------------------------

dir=../inflation/sensitivity/maf.1
bfile=../gen/geno-mix-maf.001

for n in {0k,1k,2k,3k,4k,5k,6k}; do
for m in {1k,5k,10k,20k}; do
for j in {g.1,l.1}; do

# define vars
lista=$dir/lista-1k
listb=$dir/listb/listb-maf-$j-$m-snps-mix-pop-$n-noneuro
out=$dir/out/maf-$j-$m-snps-mix-pop-$n-noneuro

if [ $n == 0k ]; then
  id=../rand.100000
else 
  id=../mix-pop-gwas-$n-noneuro.id
fi

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 00:10:0
./ldak5.2 --max-threads 3 \
          --calc-inflation $out \
          --bfile $bfile \
          --keep $id \
          --lista $lista \
          --listb $listb
">sh_script/calc-cor-maf-$j-$m-snps-mix-pop-$n-noneuro
done
done
done

# submit jobs
for n in {0k,1k,2k,3k,4k,5k,6k}; do
for m in {1k,5k,10k,20k}; do
for j in {g.1,l.1}; do
sbatch -A snpher ../sh_script/calc-cor-maf-$j-$m-snps-mix-pop-$n-noneuro
done
done
done>../job-records/calc-r-sensitivity

# compute ave r^2_j using R-----------------------------------------------------
R
require(vroom)
n=c("0k","1k", "2k", "3k", "4k", "5k", "6k")
m=c("1k","5k", "10k", "20k")
m_num=c(1000,5000, 10000,20000)
l=c("g.1","l.1")
dir="inflation/sensitivity/maf.1/"

for(j in 1:length(m)){
for(k in 1:length(l)){
  for(i in 1:length(n)){
    
    nm=paste0("maf-",l[k],"-",m[j],"-snps-mix-pop-",n[i],"-noneuro")
    dat=vroom(paste0(dir,"out/",nm,".pairwise"), col_names=F)
    lista=read.table(paste0(dir,"out/",nm,".predictorsa"), stringsAsFactors = F)
    listb=read.table(paste0(dir,"out/",nm,".predictorsb"), stringsAsFactors = F)
    
    dat=dat[,-c(m_num[j]+1)]^2
    out=data.frame(predictor=lista$V1, ave_r2=apply(dat,1, mean))
    write.table(out,paste0(dir,"summary/ave-r2-",nm), col.names=F, row.names=F, quote=F)
  }
}
}

```

#### 2st run


```bash

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# SNP lists
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# listb
# we want to generate a completely different listb

dir1=inflation/sensitivity/maf.1/listb
dir2=inflation/sensitivity/maf.1/listb-run2
m=(1000 5000 10000 20000)
nm=(1k 5k 10k 20k)

for n in {0k,1k,2k,3k,4k,5k,6k}; do
for i in {0..3}; do

# right snp list based on dif. mix pop. i.e., our pools to draw snps
pool1=$dir1/right-maf-g.1-mix-pop-$n-noneuro
pool2=$dir1/right-maf-l.1-mix-pop-$n-noneuro

# list b for run #1
old1=$dir1/listb-maf-g.1-${nm[$i]}-snps-mix-pop-$n-noneuro
old2=$dir1/listb-maf-l.1-${nm[$i]}-snps-mix-pop-$n-noneuro 

# new list b for run #2
new1=$dir2/listb-maf-g.1-${nm[$i]}-snps-mix-pop-$n-noneuro
new2=$dir2/listb-maf-l.1-${nm[$i]}-snps-mix-pop-$n-noneuro 

# maf > 0.1
awk 'NR==FNR {a[$1]; next} 
     !($1 in a) {print $1}' $old1 $pool1 | shuf | head -n ${m[$i]} > $new1
    
# maf < 0.1
awk 'NR==FNR {a[$1]; next} 
     !($1 in a) {print $1}' $old2 $pool2 | shuf | head -n ${m[$i]} > $new2 
done
done

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# compute ave r^2_j
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# compute r_ij using ldak-------------------------------------------------------

dir=../inflation/sensitivity/maf.1
bfile=../gen/geno-mix-maf.001

for n in {0k,1k,2k,3k,4k,5k,6k}; do
for m in {1k,5k,10k,20k}; do
for j in {g.1,l.1}; do

# define vars
lista=$dir/lista-1k
listb=$dir/listb-run2/listb-maf-$j-$m-snps-mix-pop-$n-noneuro
out=$dir/out-run2/maf-$j-$m-snps-mix-pop-$n-noneuro

if [ $n == 0k ]; then
  id=../rand.100000
else 
  id=../mix-pop-gwas-$n-noneuro.id
fi

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 00:10:0
./ldak5.2 --max-threads 3 \
          --calc-inflation $out \
          --bfile $bfile \
          --keep $id \
          --lista $lista \
          --listb $listb
">sh_script/calc-cor-maf-$j-$m-snps-mix-pop-$n-noneuro
done
done
done

# submit jobs
for n in {0k,1k,2k,3k,4k,5k,6k}; do
for m in {1k,5k,10k,20k}; do
for j in {g.1,l.1}; do
sbatch -A snpher ../sh_script/calc-cor-maf-$j-$m-snps-mix-pop-$n-noneuro
done
done
done>../job-records/calc-r-sensitivity-run2

# compute ave r^2_j using R-----------------------------------------------------
R
require(vroom)
n=c("0k","1k", "2k", "3k", "4k", "5k", "6k")
m=c("1k","5k", "10k", "20k")
m_num=c(1000,5000, 10000,20000)
l=c("g.1","l.1")
dir="inflation/sensitivity/maf.1/"

for(j in 1:length(m)){
for(k in 1:length(l)){
  for(i in 1:length(n)){
    
    nm=paste0("maf-",l[k],"-",m[j],"-snps-mix-pop-",n[i],"-noneuro")
    dat=vroom(paste0(dir,"out-run2/",nm,".pairwise"), col_names=F)
    lista=read.table(paste0(dir,"out-run2/",nm,".predictorsa"), stringsAsFactors = F)
    listb=read.table(paste0(dir,"out-run2/",nm,".predictorsb"), stringsAsFactors = F)
    
    dat=dat[,-c(m_num[j]+1)]^2
    out=data.frame(predictor=lista$V1, ave_r2=apply(dat,1, mean))
    write.table(out,paste0(dir,"summary-run2/ave-r2-",nm), col.names=F, row.names=F, quote=F)
  }
}
}

```

### summary

Here we check the alignment between 1st and 2nd run under different settings.


```r
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# 1st run vs. 2nd run
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

n=c("0k","1k", "2k", "3k", "4k", "5k", "6k")
m=c("1k","5k", "10k", "20k")
dir="inflation/sensitivity/maf.1/"

png("fig/ave-r2-1st-vs-2nd-run.png", res=600, width=40, height=70, units="cm")
par(mfrow=c(7,4), pty="s")

for(i in 1:length(n)){
  for(j in 1:length(m)){
    
    nm1=paste0("maf-g.1-",m[j],"-snps-mix-pop-",n[i],"-noneuro") # maf > .1
    nm2=paste0("maf-l.1-",m[j],"-snps-mix-pop-",n[i],"-noneuro") # maf < .1
    
    dat1.1=read.table(paste0(dir,"summary/ave-r2-",nm1),stringsAsFactors = F)
    dat2.1=read.table(paste0(dir,"summary-run2/ave-r2-",nm1),stringsAsFactors = F)
    dat1.2=read.table(paste0(dir,"summary/ave-r2-",nm2),stringsAsFactors = F)
    dat2.2=read.table(paste0(dir,"summary-run2/ave-r2-",nm2),stringsAsFactors = F)
    
    start=min(c(dat1.1$V2,dat1.2$V2,dat2.1$V2,dat2.2$V2))
    end=max(c(dat1.1$V2,dat1.2$V2,dat2.1$V2,dat2.2$V2))
    
      plot(dat1.1$V2, dat2.1$V2,
           xlim=c(start,end), ylim=c(start, end),
           xlab="1st run", ylab="2nd run", 
           main=paste0(m[j]," snps"," ",n[i]," non-european"), las=1,
           cex = 1.5, pch=21,  bg="darkgray", col="white", lwd=0.5)
      points(dat1.2$V2, dat2.2$V2,
           cex = 1.5, pch=21, col="white", bg="orange", lwd=0.5)
      abline(0, 1, col="darkgray", lwd=1.5, lty=1)
      
      if(i==1 & j==1) {legend("topleft", pch=19, legend=c("maf > .1","maf < .1"), 
             col=c("darkgray", "orange"), cex=1.5, box.lty=0)}
  }
}
dev.off()

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# maf > .1 vs. maf < .1
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

n=c("0k","1k", "2k", "3k", "4k", "5k", "6k")
m=c("1k","5k", "10k", "20k")
dir="inflation/sensitivity/maf.1/"

png("fig/ave-r2-maf-g.1-vs-l.1.png", res=600, width=40, height=70, units="cm")
par(mfrow=c(7,4), pty="s")

for(i in 1:length(n)){
  for(j in 1:length(m)){
    
    nm1=paste0("maf-g.1-",m[j],"-snps-mix-pop-",n[i],"-noneuro") # maf > .1
    nm2=paste0("maf-l.1-",m[j],"-snps-mix-pop-",n[i],"-noneuro") # maf < .1
    
    dat1.1=read.table(paste0(dir,"summary/ave-r2-",nm1),stringsAsFactors = F)
    dat1.2=read.table(paste0(dir,"summary/ave-r2-",nm2),stringsAsFactors = F)
    dat2.1=read.table(paste0(dir,"summary-run2/ave-r2-",nm1),stringsAsFactors = F)
    dat2.2=read.table(paste0(dir,"summary-run2/ave-r2-",nm2),stringsAsFactors = F)
    
    start=min(c(dat1.1$V2,dat1.2$V2,dat2.1$V2,dat2.2$V2))
    end=max(c(dat1.1$V2,dat1.2$V2,dat2.1$V2,dat2.2$V2))
    
      plot(dat1.1$V2, dat1.2$V2,
           xlim=c(start,end), ylim=c(start, end),
           xlab="maf > .1", ylab="maf < .1", 
           main=paste0(m[j]," snps"," ",n[i]," non-european"), las=1,
           cex = 1.5, pch=21,  bg="lightgray", col="white", lwd=0.5)
      points(dat2.1$V2, dat2.2$V2,
           cex = 1.5, pch=21, col="white", bg="orange", lwd=0.5)
      abline(0, 1, col="darkgray", lwd=1.5, lty=1)
      
      if(i==1 & j==1) {legend("topleft", pch=19, legend=c("1st run","2nd run"), 
             col=c("lightgray", "orange"), cex=1.5, box.lty=0)}
  }
}
dev.off()
```


<div class="figure" style="text-align: left">
<img src="fig-to-insert/ave-r2-1st-vs-2nd-run.png" alt="robustness of ave r2_j: 1st run vs. 2nd run" width="100%" />
<p class="caption">(\#fig:aver2-alignment-by-run)robustness of ave r2_j: 1st run vs. 2nd run</p>
</div>


<div class="figure" style="text-align: left">
<img src="fig-to-insert/ave-r2-maf-g.1-vs-l.1.png" alt="robustness of ave r2_j: maf &gt; .1 vs. maf &lt; .1" width="100%" />
<p class="caption">(\#fig:aver2-alignment-by-maf)robustness of ave r2_j: maf > .1 vs. maf < .1</p>
</div>



```r
n=c("0k","1k", "2k", "3k", "4k", "5k", "6k")
m=c("1k","5k", "10k", "20k")
dir="inflation/sensitivity/maf.1/"

png("fig/ave-r2-1st-vs-2nd-run.png", res=600, width=40, height=70, units="cm")
par(mfrow=c(7,4), pty="s")

for(i in 1:length(n)){
  for(j in 1:length(m)){
    
    nm1=paste0("maf-g.1-",m[j],"-snps-mix-pop-",n[i],"-noneuro") # maf > .1
    nm2=paste0("maf-l.1-",m[j],"-snps-mix-pop-",n[i],"-noneuro") # maf < .1
    
    dat1.1=read.table(paste0(dir,"summary/ave-r2-",nm1),stringsAsFactors = F)
    dat2.1=read.table(paste0(dir,"summary-run2/ave-r2-",nm1),stringsAsFactors = F)
    dat1.2=read.table(paste0(dir,"summary/ave-r2-",nm2),stringsAsFactors = F)
    dat2.2=read.table(paste0(dir,"summary-run2/ave-r2-",nm2),stringsAsFactors = F)
    
    test1=cor.test(dat1.1$V2, dat2.1$V2)
    test2=cor.test(dat1.2$V2, dat2.2$V2)
    
    out1.0=data.frame(maf=">.1",
                      n_noneuro=n[i],
                      m=m[j],
                      est=test1$estimate,
                      low_95=test1$conf.int[1],
                      up_95=test1$conf.int[2], stringsAsFactors = F)
    out2.0=data.frame(maf="<.1",
                      n_noneuro=n[i],
                      m=m[j],
                      est=test2$estimate,
                      low_95=test2$conf.int[1],
                      up_95=test2$conf.int[2], stringsAsFactors = F)
      
      if(i==1 & j==1){
        out1.1=out1.0; 
        out2.1=out2.0
      }else{
          out1.1=rbind(out1.1,out1.0)
          out2.1=rbind(out2.1,out2.0)
         }
  }
}
```

## varying levels of confouding

3. Show that estimated inflation correlates well with difference in $\overline{r_j^2}$ between bad and good GWAS.
I guess it will be nice to have more variety. E.g., instead of good and bad, have good, a bit bad (e.g., 1000 non-europeans), a bit more bad (2000 non-europeans), ... bad (7000 non-europeans).

Would be great to know the slope of this regression. Can you estimate it by regressing $S_j$ on $\overline{r_j^2}$ from the bad GWAS? But if we can, we just say the slope is less than n

You showed that if you regress inflation on $\overline{r_j^2}$ and MAF, that MAF is not significant (nor if you use log(MAF) or exp(MAF) or (MAFX(1-MAF))).
Can you also include info score - hopefully this is also not significant.
If Maf and Info scores are redundant, this strengthens argument for using r2j

So we ran the bad GWASs once again using different number of Europeans (n1) & Non-europeans (n2) while keep N = n1+n2=100. We did so for different MAF. For these GWASs, we regress the chi square test statistics on $\overline{r_j^2}$, MAF & info score.


```bash

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# create directories
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

for i in 0k 1k 2k 3k 4k 5k 6k; do
mkdir gwas-mix-$i-noneuro
done
# note: previously, the mixed population GWAS was based on 6,472 non-europeans 
for i in 0k 1k 2k 3k 4k 5k 6k; do
for j in .1 .01 .001; do
mkdir gwas-mix-$i-noneuro/maf$j
done
done

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# create id lists & snp lists
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# id lists---------------------------------------------------------------------
# note we used id lists from previous bad GWAS

# noneuropean id  
cat relatedness/asian-cut.05.keep relatedness/black-cut.05.keep > noneuro-unrel.id

# mixed pop id
n1=(1000 2000 3000 4000 5000 6000)
nm=(1k 2k 3k 4k 5k 6k)
n2=(99000 98000 97000 96000 95000 94000)

for i in {0..5}; do
shuf noneuro-unrel.id | head -n ${n1[$i]} > noneuro-unrel.rand.${n1[$i]}
shuf rand.100000 | head -n ${n2[$i]} > white-unrel.rand.${n2[$i]}
cat white-unrel.rand.${n2[$i]} noneuro-unrel.rand.${n1[$i]} > mix-pop-gwas-${nm[$i]}-noneuro.id
done

# snp list----------------------------------------------------------------------
# note: for the initial bad gwas, we used the snp list for good gwas
# i.e., ../gen/snps-unrel-inds.use
# gen/geno-unrel.stats is based on QCed genotype data of rand.100000 before maf...
# ... call-rate screening 

# here we want to get snp list of different maf thresholds
for maf in .1 .01 .001; do
awk < gen/geno-unrel.stats -v maf=$maf '($5>maf && $6>=0.95 && NR>1){print $1}' > gen/snps-unrel-maf$maf.use
wc -l gen/snps-unrel-maf$maf.use
done

# maf > 0.1 : 856,746 snps-unrel-maf.1.use
# maf > 0.01 : 1,103,209 snps-unrel-maf.01.use
# maf > 0.001 : 1,111,494 snps-unrel-maf.001.use

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  extract genotype data for mixed pop
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# note: previously geno-mix is based on snps-unrel-inds.use, which is a snp list...
#... after filtering for maf (maf>0.01) and call rate. So genotype data does not...
#... contain snps with maf < 0.01. So we need to extract genotype data for this...
#... set of analysis using overlap-mixed-complete-cov.id and snps-unrel-maf.001.use...
#...(see above for how the snp list is generated)

# bfiles by chr-----------------------------------------------------------------
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 1:0:0

./plink2 --pfile ../gen/geno_plink/bhr$j \
         --keep ../overlap-mixed-complete-cov.id \
         --extract ../gen/snps-unrel-maf.001.use \
         --make-bed \
         --memory 20000 \
         --out ../gen/tmp/bhr$j-mix \
         --threads 3 \
"> sh_script/chr$j-mix.sh
done

# submit script
for i in {1..22}; do
sbatch -A snpher ../sh_script/chr$i-mix.sh
done>../job-records/mkbfile-mix-pop-new

# merge bfiles------------------------------------------------------------------
rm bfile-mix-new.list
for j in {1..22}; do
echo  "../gen/tmp/bhr$j-mix" >>bfile-mix-new.list
done

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 10:0:0
./ldak5.1 --make-bed ../gen/geno-mix-maf.001 \
          --mbfile ../gen/bfile-mix-new.list \
          --max-threads 3 \
          --exclude-dups YES  
"> sh_script/merge-mbfiles-mix.sh

# submit the script
sbatch -A snpher ../sh_script/merge-mbfiles-mix.sh >../job-records/merge-mbfiles-mix-pop-maf.001

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  get INFO score
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# here we repeat the QC step for the good gwas to compute the INFO score
# note: since the MAF is based on snp list after QC for rand.100000, here we compute...
#...INFO based on the same list of participants.

# make QCed pgen files---------------------------------------------------------- 
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 1:0:0

./plink2 --pfile ../gen/geno_plink/bhr$j \
         --keep ../unrelated/rand.100000 \
         --extract ../doug/ukbb.ldsc \
         --hwe 0.0001 \
         --hard-call-threshold .05 \
         --mach-r2-filter 0.8 2 \
         --make-pgen \
         --memory 20000 \
         --out ../gen/tmp/bhr$j-unrel \
         --threads 3
"> sh_script/chr$j.sh
done

# submit jobs
for j in {1..22}; do
sbatch -A snpher ../sh_script/chr$j.sh
done > ../job-records/qc-unrel-pgen

# compute info score------------------------------------------------------------
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 1:0:0

./plink2 --pfile ../gen/tmp/bhr$j-unrel \
         --keep ../unrelated/rand.100000 \
         --threads 3 \
         --memory 20000 \
         --freq cols=chrom,ref,alt,altfreq,machr2 \
         --out ../gen/info/chr$j 
        
"> sh_script/info-chr$j.sh
done

# submit file
for j in {1..22}; do
sbatch -A snpher ../sh_script/info-chr$j.sh
done > ../job-records/compute-info-score

# combine files
dir=gen/info
rm $dir/rand.100000-qced-afreq 
for i in {1..22}; do
if [ $i == 1 ]; then
  awk '{print $0}' $dir/chr$i.afreq > $dir/rand.100000-qced.afreq
else 
  awk 'NR>1 {print $0}' $dir/chr$i.afreq >> $dir/rand.100000-qced.afreq
fi
done

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# GWASs
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

## NOTE: for maf >0.001, we will have to redo the GWAS, because geno-mix only ...
#...contains snps with maf > 0.01. See below

# for 0k, i.e., the good gwas without PC as covariates--------------------------------------
n=0k
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for maf in {.1,.01,.001}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 7G
#SBATCH -c 2
#SBATCH -t 2:00:0

./ldak5.1 --linear ../gwas-mix-$n-noneuro/maf$maf/$i-linear \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile ../gen/geno-mix \
          --covar ../phen/basic-covariates.use \
          --keep ../rand.100000 \
          --extract ../gen/snps-unrel-maf$maf.use \
          --max-threads 2
"> sh_script/$i-linear-$n-noneuro-maf-$maf.sh
done
done

# submit files------------------------------------------------------------------
n=0k
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for maf in {.1,.01,.001}; do
sbatch -A snpher ../sh_script/$i-linear-$n-noneuro-maf-$maf.sh
done
done>../job-records/bad-gwas-0k-noneuro

# for 1k to 6k------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for n in {1k,2k,3k,4k,5k,6k}; do
for maf in {.1,.01,.001}; do
#for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 7G
#SBATCH -c 2
#SBATCH -t 8:00:0

./ldak5.1 --linear ../gwas-mix-$n-noneuro/maf$maf/$i-linear \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile ../gen/geno-mix \
          --covar ../phen/basic-covariates.use \
          --keep ../mix-pop-gwas-$n-noneuro.id \
          --extract ../gen/snps-unrel-maf$maf.use \
          --max-threads 2
"> sh_script/$i-linear-$n-noneuro-maf-$maf.sh
done
done
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for n in {1k,2k,3k,4k,5k,6k}; do
for maf in {.1,.01,.001}; do
#for j in {1..22}; do
sbatch -A snpher ../sh_script/$i-linear-$n-noneuro-maf-$maf.sh
done
done
done>../job-records/bad-gwas

# check job completion
file=job-records/bad-gwas
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# combine results for some--------------------------------------------------- 
# only for .summaries & .pvalues
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,quals,pulse,reaction,sbp,snoring,hyper}; do 
for j in {1..22}; do
if [ $j == 1 ]; then
  awk '{print $0}' $i-linear-chr-$j.summaries > $i-linear.summaries
  awk '{print $0}' $i-linear-chr-$j.pvalues > $i-linear.pvalues
else 
  awk 'NR>1 {print $0}' $i-linear-chr-$j.summaries >> $i-linear.summaries
  awk 'NR>1 {print $0}' $i-linear-chr-$j.pvalues >> $i-linear.pvalues
fi
done
done

# repeat gwas for maf>.001 -----------------------------------------------------
maf=.001
bfile=../gen/geno-mix-maf.001
n=(0k 1k 2k 3k 4k 5k 6k)

for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for j in {0..6}; do

out=../gwas-mix-${n[$j]}-noneuro/maf$maf/$i-linear

if [ $j == 1 ]; then
  id=../rand.100000
else
  id=../mix-pop-gwas-${n[$j]}-noneuro.id
fi

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 7G
#SBATCH -c 2
#SBATCH -t 01:30:0

./ldak5.1 --linear $out \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile $bfile \
          --covar ../phen/basic-covariates.use \
          --keep $id \
          --extract ../gen/snps-unrel-maf$maf.use \
          --max-threads 2
"> sh_script/$i-linear-${n[$j]}-noneuro-maf-$maf.sh
done
done

# submit files------------------------------------------------------------------
maf=.001
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for n in {0k,1k,2k,3k,4k,5k,6k}; do
sbatch -A snpher ../sh_script/$i-linear-$n-noneuro-maf-$maf.sh
done
done>../job-records/bad-gwas


#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  compute ave r^2_j
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# create snp lists--------------------------------------------------------------

# note: we do this for three snp lists
# maf > 0.1 : 856,746 gen/snps-unrel-maf.1.use
# maf > 0.01 : 1,103,209 gen/snps-unrel-maf.01.use
# maf > 0.001 : 1,111,494 gen/snps-unrel-maf.001.use

m=10000
nm=10k

for i in {.1,.01,.001}; do
# define vars
infile=gen/snps-unrel-maf$i.use
left=inflation/left-snps-unrel-maf$i-$nm
right=inflation/right-snps-unrel-maf$i-$nm

awk '{split($1, a, ":");
    if (a[1]<8) print $1 }' $infile | shuf | head -n $m >$left  
awk '{split($1, a, ":");
    if (a[1]>=8) print $1 }' $infile | shuf | head -n $m >$right
done

# compute r_ij using ldak -------------------------------------------------------

n=(0k 1k 2k 3k 4k 5k 6k)
indir=../inflation
outdir=../inflation/out
bfile=../gen/geno-mix-maf.001

for j in {0..6}; do
for i in {.1,.01,.001}; do

# define vars
lista=$indir/right-snps-unrel-maf$i-10k
listb=$indir/left-snps-unrel-maf$i-10k
out=$outdir/10k-snps-mix-pop-gwas-${n[$j]}-noneuro-maf$i

if [ $j == 0 ]; then
  id=../rand.100000
else 
  id=../mix-pop-gwas-${n[$j]}-noneuro.id
fi

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 10
#SBATCH -t 2:00:0
./ldak5.2 --max-threads 10 \
          --calc-inflation $out \
          --bfile $bfile \
          --keep $id \
          --lista $lista \
          --listb $listb
">sh_script/calc-r-10k-snps-mix-pop-gwas-${n[$j]}-noneuro-maf$i
done
done

# submit the job
n=(0k 1k 2k 3k 4k 5k 6k)
for j in {0..6}; do
for i in {.1,.01,.001}; do
sbatch -A snpher ../sh_script/calc-r-10k-snps-mix-pop-gwas-${n[$j]}-noneuro-maf$i
done
done>../job-records/calc-r

#  compute ave r_j^2 for each i-------------------------------------------------
R
require(vroom)
n=c("0k","1k", "2k", "3k", "4k", "5k", "6k")
maf=c(".1",".01",".001")

for(j in 1:length(maf)){

  for(i in 1:length(n)){
    nm=paste0("10k-snps-mix-pop-gwas-",n[i],"-noneuro-maf",maf[j])
    dat=vroom(paste0("inflation/out/",nm,".pairwise"), col_names=F)
    lista=read.table(paste0("inflation/out/",nm,".predictorsa"), stringsAsFactors = F)
    listb=read.table(paste0("inflation/out/",nm,".predictorsb"), stringsAsFactors = F)
    
    dat=dat[,-c(10001)]^2
    outb=data.frame(predictor=listb$V1, ave_r2=apply(dat,2, mean))
    outa=data.frame(predictor=lista$V1, ave_r2=apply(dat,1, mean))
    out=rbind(outa,outb)
    
    write.table(out,paste0("summary/ave-r2-",nm), col.names=F, row.names=F, quote=F)  
  }
}

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# extract LD score
#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# convert snp list to rs system ------------------------------------------------
infile=gen/snps-unrel-maf.001.use
outfile=gen/snps-unrel-maf.001.use-rs
awk '(NR==FNR){a[$1]; b[$1]=$2; next}
     ($1 in a){print b[$1], $2}' doug/ukbb.ldsc $infile > $outfile

#  extract ld scores -----------------------------------------------------------
dir=ldsc/eur_w_ld_chr
for chrom in {1..22}; do
zcat $dir/$chrom.l2.ldscore.gz | awk 'NR>1 {print $2, $6}' > ldscore
awk '(NR==FNR){a[$1];next}($1 in a){print $0}' gen/snps-unrel-maf.001.use-rs ldscore > temp
if [ $chrom -eq 1 ]
then 
 mv temp snps-unrel-maf.001.ldscore
else
 cat snps-unrel-maf.001.ldscore temp > temp2
 mv temp2 snps-unrel-maf.001.ldscore
fi
echo $chrom
done

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  put info together
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# INFO: gen/info rand.100000-qced.afreq 
# maf: gen/geno-mix-maf.001.stats
# chi square : trait-linear.summaries
# ave r^2_j: e.g, summary/ave-r2-10k-snps-mix-pop-gwas-$i-noneuro-maf$j
# ld score from ldsc ref panel: snps-unrel-maf.001.ldscore; in rs

# required files
maf=gen/geno-mix-maf.001.stats # use as the snp list to integrate all info
info=gen/info/rand.100000-qced.afreq
rs=doug/ukbb.ldsc
ldsc=snps-unrel-maf.001.ldscore

# temporary files
awk 'NR > 1 {print $1, $5 }' $maf > tmp/maf.tmp
awk 'NR==FNR {a[$2]; b[$2]=$6; next} 
              {if ($1 in a) print b[$1] ;
               else print "NA"}' $info tmp/maf.tmp  > tmp/info.tmp
awk 'NR==FNR {a[$2]; b[$2]=$1; next} ($1 in a) {print b[$1], $0}' $rs $ldsc > tmp/ldsc.tmp1
# here we make sure the order of the rows are the same as maf.tmp
awk 'NR==FNR {a[$1]; b[$1]=$2; c[$1]=$3 ; next} 
     {if ($1 in a) print b[$1], c[$1];
      else print "NA"}' tmp/ldsc.tmp1 tmp/maf.tmp > tmp/ldsc.tmp

for trait in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for n in {0k,1k,2k,3k,4k,5k,6k}; do
for j in {.1,.01,.001}; do

# define files
gwas=gwas-mix-$n-noneuro/maf$j/$trait-linear.summaries
aver2=summary/ave-r2-10k-snps-mix-pop-gwas-$n-noneuro-maf$j
out=gwas-mix-all-out/$trait-mix-pop-gwas-$n-noneuro-maf$j.out

# create temporary files
awk 'NR==FNR {a[$1]; b[$1]=$2; next} 
     {if ($1 in a) print b[$1]; 
     else print "NA" }' $aver2 tmp/maf.tmp > tmp/aver2.tmp
     
awk 'NR==FNR {a[$1]; b[$1]=$5; next} 
     {if ($1 in a) print b[$1]; 
     else print "NA" }' $gwas tmp/maf.tmp > tmp/gwas.tmp     

# put info together
paste tmp/maf.tmp \
      tmp/info.tmp \
      tmp/gwas.tmp \
      tmp/aver2.tmp \
      tmp/ldsc.tmp \
      | awk 'BEGIN{OFS=";";print "snp;maf;info;chisq;aver2;rs;ldsc"}
              {$1=$1}1' > $out
done
done
done

```

## results summary

```r
#::::::::::::::::::::::::::::::::::::::::::::::
# plot chisq ~ aver2_j
#::::::::::::::::::::::::::::::::::::::::::::::

# function to plot a single trait---------------------------------------------------

make_plot=function(trait, maf, n_noneuro){
  
  # define variables
  n_noneuro=n_noneuro
  trait=trait
  maf=maf
  
  # define color
  library(RColorBrewer)
  qual_col_pals = brewer.pal.info[brewer.pal.info$category == 'qual',]
  col_vector = unlist(mapply(brewer.pal, qual_col_pals$maxcolors, rownames(qual_col_pals)))
  set.seed(14)
  mycol=sample(col_vector,7)
  
  # plot
  for(i in length(n_noneuro):1){
    n=n_noneuro[i]
    file=paste0("gwas-mix-all-out/",trait,"-mix-pop-gwas-",n,
                "-noneuro-maf",maf,".out")
    
    dat=vroom(file, col_names=T, delim=";")
    # bin a variable by quantile
    cutoff=quantile(dat$aver2, probs = seq(0, 1, 0.005), na.rm=T)
    dat$bin=cut(dat$aver2, breaks=cutoff, labels=1:(length(cutoff)-1))
    # average chisq by bin values
    out=data.frame(chisq_ave=tapply(dat$chisq,INDEX=dat$bin, mean))
    out$bin_val=tapply(dat$aver2,INDEX=dat$bin, mean)
    
    if(i==length(n_noneuro)){
    plot(out$bin_val, out$chisq_ave,
         xlab="ave r2", ylab="ave chisq",
         main=trait, las=1,
         cex = 1.5, pch=21,  bg=mycol[i], col="white", lwd=0.5)
    } else {
      points(out$bin_val, out$chisq_ave,
           cex = 1.5, pch=21, col="white", bg=mycol[i], lwd=0.5)
    } # end of conditional statement
  } # end of loop over n
   # add a legend
  
   if(trait=="awake"){
     legend("topleft", pch=19, legend=n_noneuro, col=mycol, cex=1.5, box.lty=0)}
   
} # end of function

# make a plot ------------------------------------------------------------------

# maf = .01----------------------------
require(vroom)
n_noneuro=c("0k","1k", "2k", "3k", "4k", "5k", "6k")
maf_threshold=c(".1",".01",".001")
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

maf=maf_threshold[2]

png(paste0("fig/mix-pop-gwas-chisq-by-aver2-bin.png"),
    width = 50, height = 30, units = "cm", res=600)
par(mfrow=c(3,5))
    for(i in 1:length(traits)){
      trait=traits[i]
      make_plot(trait, maf, n_noneuro)
    }
dev.off()

# maf = .001----------------------------
require(vroom)
n_noneuro=c("0k","1k", "2k", "3k", "4k", "5k", "6k")
maf_threshold=c(".1",".01",".001")
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

maf=maf_threshold[3]

png(paste0("fig/mix-pop-gwas-chisq-by-aver2-bin-maf.001.png"),
    width = 50, height = 30, units = "cm", res=600)
par(mfrow=c(3,5))
    for(i in 1:length(traits)){
      trait=traits[i]
      make_plot(trait, maf, n_noneuro)
    }
dev.off()

#::::::::::::::::::::::::::::::::::::::::::::::
# estimate slope: chisq ~ aver2_j
#::::::::::::::::::::::::::::::::::::::::::::::

require(vroom)
n_noneuro=c("0k","1k", "2k", "3k", "4k", "5k", "6k")
maf_threshold=c(".1",".01",".001")
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(i in 1:length(maf_threshold)){
  for(j in 1:length(n_noneuro)){
    for(k in 1:length(traits)){

    maf=maf_threshold[i]
    n=n_noneuro[j]
    trait=traits[k]
    
    file=paste0("gwas-mix-all-out/",trait,"-mix-pop-gwas-",n,
                    "-noneuro-maf",maf,".out")
    
    dat=vroom(file, col_names=T, delim=";")
    mod1=lm(chisq ~ aver2,data=dat)
    mod2=lm(chisq ~ ldsc,data=dat)
    
    slope0=data.frame(trait=trait,
                      maf_threshold=maf,
                      n_noneuro=n,
                      slope_aver2=coef(mod1)[2],
                      p_aver2=summary(mod1)$coefficients[,4][2],
                      slope_ldsc=coef(mod2)[2],
                      p_ldsc=summary(mod2)$coefficients[,4][2],
                      stringsAsFactors = F)
    
    if(i==1 & j==1 & k==1){slope=slope0} else {slope=rbind(slope,slope0)}
    
    }
  }
}

out=slope[order(slope$trait, slope$maf_threshold, slope$n_noneuro),]
write.table(out,"summary/mix-pop-gwas-slope.txt", col.names=T, row.names=F, quote=F)
```


```r
#::::::::::::::::::::::::::::::::::::::::::::::
# plot chisq ~ aver2_j by maf | n_noneuro
#::::::::::::::::::::::::::::::::::::::::::::::

# function to plot a single trait---------------------------------------------------

make_plot=function(trait, maf_threshold, n){
  
  # define variables
  n=n
  trait=trait
  maf_threshold=maf_threshold
  
  # define color
  library(RColorBrewer)
  qual_col_pals = brewer.pal.info[brewer.pal.info$category == 'qual',]
  col_vector = unlist(mapply(brewer.pal, qual_col_pals$maxcolors, rownames(qual_col_pals)))
  set.seed(14)
  mycol=sample(col_vector,length(maf_threshold))
  
  # plot
  for(i in 1:length(maf_threshold)){
    maf=maf_threshold[i]
    file=paste0("gwas-mix-all-out/",trait,"-mix-pop-gwas-",n,
                "-noneuro-maf",maf,".out")
    
    dat=vroom(file, col_names=T, delim=";")
    # bin a variable by quantile
    cutoff=quantile(dat$aver2, probs = seq(0, 1, 0.005), na.rm=T)
    dat$bin=cut(dat$aver2, breaks=cutoff, labels=1:(length(cutoff)-1))
    # average chisq by bin values
    out=data.frame(chisq_ave=tapply(dat$chisq,INDEX=dat$bin, mean))
    out$bin_val=tapply(dat$aver2,INDEX=dat$bin, mean)
    
    if(i==1){
    plot(out$bin_val, out$chisq_ave,
         xlab="ave r2", ylab="ave chisq",
         main=paste0(trait," ", n," non-european"), las=1,
         cex = 1.5, pch=21,  bg=mycol[i], col="white", lwd=0.5)
    } else {
      points(out$bin_val, out$chisq_ave,
           cex = 1.5, pch=21, col="white", bg=mycol[i], lwd=0.5)
    } # end of conditional statement
  } # end of loop over maf
   # add a legend
   if(trait=="awake"){
     legend("topleft", pch=19, legend=maf_threshold, col=mycol, cex=1.5, box.lty=0)}
   
} # end of function

# make a plot ------------------------------------------------------------------

require(vroom)
n_noneuro=c("0k","1k", "2k", "3k", "4k", "5k", "6k")
maf_threshold=c(".1",".01",".001")
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(j in 1:length(n_noneuro)){
  
  n=n_noneuro[j]
  # plot give an n for non-european
  png(paste0("fig/mix-pop-gwas-chisq-by-aver2-bin-",n,"-noneuro.png"),
      width = 50, height = 30, units = "cm", res=600)
  par(mfrow=c(3,5))
      for(i in 1:length(traits)){
        trait=traits[i]
        make_plot(trait, maf_threshold, n)
      }
  dev.off()
}
```




We can now report average inflation of test statistics (mean(r2j) x n x h2, which is upper bounded by mean(r2j) x n), AND we can report estimated per-SNP inflation of test statistics (r2j x n x j2)

Eg., we can say that if you use UKBB recommended samples, with MAF>.01, the average inflation will be less than 0.5 units, while the maximum inflation will be less than 1 unit.

However, maybe we find that if you analyse rare SNPs (e.g. 0.0001 < MAF < .01), the maximum inflation is much higher (so people must be careful).

What happens if you have a meta-analysis - how does inflation accumulate over cohorts?


Possible way to write up this paper:

Perform a good and bad GWAS of height

Figure 1a - show that GIF performs poorly (because test statistics of good GWAS do not follow a chisq(1) distribution)

Figure 1b - use test statistics of good GWAS to show that LDSC performs badly, because it assumes causal variation is constant [Do you mean the choice of heritability model affects inflation estimates? But we found for good GWASs, ldsc under gcta is OK for most traits.]

Figure 1c - use difference between test statistics to show that LDSC performs badly because it assumes inflation is constant.

(in supplement, can show same results for other traits).

Figure 2 - show that r2j is independent of true signal (test statistics from good gwas), but predicts well inflation (difference)

<!--chapter:end:04_ave_r2_ij.Rmd-->


# Good GWAS {#good}

Here we do good GWASs. We will use 1.2M hapmap3 SNPs.
But sill need to do some QC to these SNPs.

## QC of hp3 SNPs {#goodid}

```bash

# select 100k unrelated individuals with no missing covariates & 14 phenotypes--

# individual with complete covariates
R
options(scipen = 100)
library(vroom)
dat=vroom("phen/covariates.use", col_names=F)
out=dat[complete.cases(dat),c(1,2)]
write.table(out,"covariates-complete-cases.id", col.names=F, row.names=F, quote=F)

# overlaping individuals across 14 traits
cp icd10/unrelated.inds overlap.ind # unrelated white British

dir=phen/continuous-traits/
for tt in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
mv overlap.ind temp
awk '(NR==FNR){a[$1];next}($1 in a){print $1, $2}' temp $dir/$tt.raw.pheno > overlap.ind
wc -l overlap.ind
echo $tt
done
rm temp

# overlapping & complete covariates
awk 'NR==FNR{a[$1]; next} ($1 in a) {print $1, $1}' overlap.ind covariates-complete-cases.id > overlap-complete-cases.id

#randomly pick 100k of these
shuf overlap-complete-cases.id | head -n 100000 > rand.100000

# QC SNPs-----------------------------------------------------------------------
# stating number of SNPs= 1,184,423 
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 1:0:0

./plink2 --pfile ../gen/geno_plink/bhr$j \
         --keep ../unrelated/rand.100000 \
         --extract ../doug/ukbb.ldsc \
         --hwe 0.0001 \
         --hard-call-threshold .05 \
         --mach-r2-filter 0.8 2 \
         --make-bed \
         --memory 20000 \
         --out ../gen/tmp/bhr$j-unrel \
         --threads 3
"> sh_script/chr$j.sh
done

# submit jobs
for j in {1..22}; do
sbatch -A snpher ../sh_script/chr$j.sh
done > ../job-records/qc-unrel

# merge files
rm bfile-unrel.list
for j in {1..22}; do
echo  "../gen/tmp/bhr$j-unrel" >>bfile-unrel.list
done

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 40:0:0
./ldak5.1 --make-bed ../gen/geno-unrel \
          --mbfile ../gen/bfile-unrel.list \
          --max-threads 3 \
          --exclude-dups YES  
"> sh_script/mbfile-unrel.sh

# submit the script
sbatch -A snpher ../sh_script/mbfile-unrel.sh >../job-records/mbfiles-unrel

# MAF & call-rate 
awk < geno-unrel.stats '($5>.01 && $6>=0.95 && NR>1){print $1}' > snps-unrel-inds.use
# m = 1,103,209 SNPs
 
```

## extract covariates


```r
head=read.table("phen/ukb45861.header", sep=",", header=F, stringsAsFactors = F)
# function to get the variables
get=function(nm){
  colnum=grep(nm,head,fixed=TRUE)
  out=data.frame(t(rbind(colnum, head[,colnum])))
  names(out)=c("column", "field")
  return (out)
}
# get the variables
out=rbind(get('21022-0'), # age at recruitment: 21022
          get('54-0'), # assessment centre: 54
          get('22000-0'), # genotyping batch: 22000
          get('22001-0'), # genetic sex: 22001
          get('189-0'), # townsend
          get('22009-0'), # genotype PC: 22009
          get('21000-0')) # ethnic background 
row.names(out)=1:dim(out)[1]
# remove unwanted
out=out[-c(95:100),]
write.table(out,"phen/covariates.colnum", col.names=F, row.names=F, sep="\t", quote=F)

# extract dat from the full data set
awk -F '","' '(NR==FNR){a[$1];next}{printf "%s\"", $1;for(i in a){printf " \"%s\"", $i};printf "\n"}' phen/covariates.colnum phen/ukb45861.csv > phen/covariates.dat

#--------
# organize covariates
#--------

options(scipen = 100)
# all covariates
var=read.table("phen/covariates.dat", header=T, stringsAsFactors = F)
nm=names(var)

# extract covariates
var1=data.frame(eid=var$eid,
                age=var[,grep('21022', nm, fixed=T)], # age at recruitment
                sex_gen=var[,grep('22001', nm, fixed=T)], # genetic sex: 0 =F; 1 = M
                centre=var[,grep('X54', nm, fixed=T)], # assessment centre
                geno_batch=var[,grep('22000', nm, fixed=T)], # genotype batch
                townsend=var[,grep('189', nm, fixed=T)], # townsen
                var[,grep('22009', nm, fixed=T)], # genotype PC
                ethnicity=var[,grep('21000', nm, fixed=T)], # self-reported ethnicity
                stringsAsFactors = F)
pcnm=strsplit(nm[grep('22009', nm, fixed=T)], "[.]")
pcnm=paste0("pc",unlist(lapply(pcnm,function(X) X[3])))
names(var1)[7:46]=pcnm

# file that contains all covariates
write.table(var1,"phen/covariates.phen", col.names=T, row.names=F, quote=F)

# create a file to use: no col.names & continuous covariates only
use=var1[,c(1,1:3,6:46)]
write.table(names(use),"phen/covariates.use-names", 
            col.names=F, row.names=F, quote=F)
write.table(use,"phen/covariates.use", col.names=F, row.names=F, quote=F)
```


## GWAS


```bash

# linear regression-------------------------------------------------------------
mkdir unrelated/gwas-good
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 10G
#SBATCH -c 2
#SBATCH -t 10:0:0

./ldak5.1 --linear ../unrelated/gwas-good/$i-linear \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile ../gen/geno-unrel \
          --keep ../unrelated/rand.100000 \
          --extract ../gen/snps-unrel-inds.use \
          --covar ../phen/covariates.use \
          --max-threads 2 \
"> sh_script/$i-linear.sh
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-linear.sh
done>../job-records/gwas-good

# check job completion----------------------------------------------------------
file=job-records/gwas-good
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# resubmit failed/incomplete jobs-----------------------------------------------

for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 8G
#SBATCH -c 2
#SBATCH -t 5:0:0

./ldak5.1 --linear ../unrelated/gwas-good/$i-linear-chr-$j \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile ../gen/geno-unrel \
          --keep ../unrelated/rand.100000 \
          --extract ../gen/snps-unrel-inds.use \
          --covar ../phen/covariates.use \
          --max-threads 2 \
          --chr $j
"> sh_script/$i-linear-chr-$j.sh
done
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for j in {1..22}; do
sbatch -A snpher ../sh_script/$i-linear-chr-$j.sh
done
done>../job-records/gwas-good-resubmission

# check job completion----------------------------------------------------------
file=job-records/gwas-good-resubmission
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# combine results
# only need .summaries & .pvalues
#for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,reaction,sbp,snoring,hyper}; do 
i=quals 
for j in {1..22}; do
if [ $j == 1 ]; then
  awk '{print $0}' $i-linear-chr-$j.summaries > $i-linear.summaries
  awk '{print $0}' $i-linear-chr-$j.pvalues > $i-linear.pvalues
else 
  awk 'NR>1 {print $0}' $i-linear-chr-$j.summaries >> $i-linear.summaries
  awk 'NR>1 {print $0}' $i-linear-chr-$j.pvalues >> $i-linear.pvalues
fi
done
#done

```

## ldsc intercept

### under gcta


```bash
#:::::::::::::
# using original ldsc
#:::::::::::::

# format stats for ldsc--------------------------------------------------------- 
library(vroom)
options(scipen = 100)
rs=vroom("doug/ukbb.ldsc", col_names=F)
phen=c("awake","bmi","chron","ever","fvc",
       "height","imp","neur","pulse","quals",
       "reaction","sbp","snoring","hyper", "quals")

for(i in phen){
  stat=vroom(paste0("unrelated/gwas-good/",i,"-linear.summaries"), col_names=T)
  p=vroom(paste0("unrelated/gwas-good/",i,"-linear.pvalues"), col_names=T)
  m1=match(rs$X1, stat$Predictor)
  m2=match(rs$X1, p$Predictor)
  out=data.frame(SNP=rs$X2,
                 N=stat$n[m1],
                 Z=(sqrt(stat$Stat)*stat$Direction)[m1],
                 A1=stat$A1[m1],
                 A2=stat$A2[m1], 
                 pval=p$P[m2], stringsAsFactors=F)
  out=out[complete.cases(out),]
  write.table(out, paste0("unrelated/gwas-good/",i,"-linear-rs.summaries"), 
  col.names=T, row.names=F, quote=F)
}

# perform ldsc------------------------------------------------------------------
mkdir out-good-gwas
for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 2G
#SBATCH -c 3
#SBATCH -t 24:00:0

.././munge_sumstats.py \
--sumstats ../../unrelated/gwas-good/$i-linear-rs.summaries \
--out ../out-good-gwas/$i \
--merge-alleles ../w_hm3.snplist

.././ldsc.py \
--h2 ../out-good-gwas/$i.sumstats.gz \
--ref-ld-chr ../eur_w_ld_chr/ \
--w-ld-chr ../eur_w_ld_chr/ \
--out ../out-good-gwas/$i-ldsc
">sh_script/ldsc-$i-good-gwas.sh
done

# submit jobs-------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,reaction,sbp,snoring,hyper,quals}; do
sbatch -A snpher ../sh_script/ldsc-$i-good-gwas.sh
done>../../job-records/ldsc-good-gwas

#:::::::::::::
# using sumher
#:::::::::::::

# insert genetic distance into bim file-----------------------------------------
head geno-unrel.fam > small-unrel

for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --mem 8G
#SBATCH -c 1
#SBATCH -t 3:00:0
#SBATCH --constraint \"s04|s05\"
./plink1.9 --bfile ../gen/geno-unrel \
          --chr $j \
          --cm-map /home/zhoux/snpher/faststorage/genetic_maps/genetic_map_chr@_combined_b37.txt \
          --make-bed \
          --out new$j \
          --keep ../gen/small-unrel
" > sh_script/map$j
done

for j in {1..22}; do
sbatch -A snpher ../sh_script/map$j
done > genetic-distance-hapmap3

cd /home/zhoux/dsmwpred/xuan/quality-control/qc-10oct/gen
cat new{1..22}.bim | awk '{print $2, $3}' > maps-hapmap3.txt
rm new{1..22}.{bim,bed,fam,log}

awk '(NR==FNR){arr[$1]=$2;next}{print $1, $2, arr[$2], $4, $5, $6}' > geno-unrel.bim2 maps-hapmap3.txt geno-unrel.bim
mv geno-unrel.bim geno-unrel.bim0
mv geno-unrel.bim2 geno-unrel.bim

# compute tagging under gcta----------------------------------------------------
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 1
#SBATCH -t 5:0:0

./ldak5.1 --calc-tagging ../tagging/gcta-hapmap3-chr-$j \
          --bfile ../gen/geno-unrel \
          --ignore-weights YES \
          --power -1 \
          --window-cm 1 \
          --chr $j
"> sh_script/tagging-gcta-hapmap3-chr$j
done

for j in {1..22}; do
sbatch -A snpher ../sh_script/tagging-gcta-hapmap3-chr$j 
done > ../job-records/tagging-gcta-hapmap3

# check job completion----------------------------------------------------------
file=job-records/tagging-gcta-hapmap3
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# merge tagging files-----------------------------------------------------------
rm list.txt
for j in {1..22}; do 
echo "tagging/gcta-hapmap3-chr-$j.tagging" >> list.txt
done
./ldak5.1 --join-tagging tagging/gcta-hapmap3 --taglist list.txt

# ldsc intercept----------------------------------------------------------------
mkdir sumher-good
for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 1
#SBATCH -t 5:0:0

./ldak5.1 --sum-hers ../sumher-good/$i-sumher-gcta \
          --tagfile ../tagging/gcta-hapmap3.tagging \
          --summary ../unrelated/gwas-good/$i-linear.summaries \
          --check-sums NO \
          --intercept YES
"> sh_script/$i-sumher-gcta-unrelated
done

for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-sumher-gcta-unrelated
done>../job-records/sumher-gcta-unrelated

```

### under ldak-thin


```bash

# get weights-------------------------------------------------------------------
awk < ldak-thin/ldak-thin-hapmap3.in '{print $1, 1}' > ldak-thin/weights.ldak-thin-hapmap3

# calculate tagging under ldak-thin---------------------------------------------
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 40G
#SBATCH -c 5
#SBATCH -t 10:0:0
#SBATCH --constraint \"s04|s05\"
./ldak5.1 --calc-tagging ../tagging/ldak-thin-hapmap3-chr-$j \
          --bfile ../gen/geno-unrel \
          --weights ../ldak-thin/weights.ldak-thin-hapmap3 \
          --power -.25 \
          --window-cm 1 \
          --chr $j \
          --save-matrix YES \
          --max-threads 5
" > sh_script/tagging-ldak-thin-hapmap3-chr$j.sh
done

# submit scripts
for j in {1..22}; do
sbatch -A snpher ../sh_script/tagging-ldak-thin-hapmap3-chr$j.sh
done > ../job-records/tagging-ldak-thin-hapmap3

# check job completion----------------------------------------------------------
file=job-records/tagging-ldak-thin-hapmap3
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# merge tagging files-----------------------------------------------------------
rm list.txt
rm matlist.txt
for j in {1..22}; do 
echo "tagging/ldak-thin-hapmap3-chr-$j.tagging" >> list.txt
echo "tagging/ldak-thin-hapmap3-chr-$j.matrix" >> matlist.txt
done
./ldak5.1 --join-tagging tagging/ldak-thin-hapmap3 --taglist list.txt --matlist matlist.txt

# ldsc intercept----------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 1
#SBATCH -t 00:30:0

./ldak5.1 --sum-hers ../sumher-good/$i-sumher-ldak-thin \
          --tagfile ../tagging/ldak-thin-hapmap3.tagging \
          --summary ../unrelated/gwas-good/$i-linear.summaries \
          --check-sums NO \
          --intercept YES
"> sh_script/$i-sumher-ldak-thin-unrelated
done

for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-sumher-ldak-thin-unrelated
done>../job-records/sumher-ldak-thin-unrelated

```

### summary


```bash

# original ldsc
grep Intercept *ldsc.log | awk '{split($1, a, /[-]/); split($3, b, /[()]/); print a[1], $2, b[2]}' > ../../summary/ldsc-good

# sumher under gcta
grep Intercept_Estimate *gcta.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-gcta-good-est
grep Intercept_SD *gcta.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-gcta-good-sd
cd ../summary/
paste sumher-gcta-good-est sumher-gcta-good-sd | awk '{print $1, $2, $4}' > sumber-gcta-good
      
# submer under ldak-thin
grep Intercept_Estimate *ldak-thin.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-ldak-thin-good-est
grep Intercept_SD *ldak-thin.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-ldak-thin-good-sd
cd ../summary/
paste sumher-ldak-thin-good-est sumher-ldak-thin-good-sd | awk '{print $1, $2, $4}' > sumher-ldak-thin-good

```

## REML

### making grms

```bash

# making grm -------------------------------------------------------------------

#:::
# under gcta
#:::

# all snps
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 60G
#SBATCH -c 10
#SBATCH -t 20:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/gcta-all-unrel \
          --bfile ../gen/geno-unrel \
          --power -1 \
          --ignore-weights YES \
          --single YES
" > sh_script/grm-all-snps-unrel

sbatch -A snpher ../sh_script/grm-all-snps-unrel > ../job-records/grm-all-snps-unrel

# grm by snp blocks: right vs. left
awk '$1<8 {print $2}' geno-unrel.bim > left-hapmap3.snps 
awk '$1>=8 {print $2}' geno-unrel.bim > right-hapmap3.snps

for i in left right; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 60G
#SBATCH -c 10
#SBATCH -t 20:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/gcta-$i-unrel \
          --bfile ../gen/geno-unrel \
          --extract ../gen/$i-hapmap3.snps \
          --power -1 \
          --ignore-weights YES \
          --single YES
" > sh_script/grm-gcta-$i-unrel
done

for i in left right; do
sbatch -A snpher ../sh_script/grm-gcta-$i-unrel 
done > ../job-records/grm-gcta-by-snps-unrel 

#:::
# under ldak-thin
#:::

#-----------
# 1. thin snps
#-----------

for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 10G
#SBATCH -c 4
#SBATCH -t 4:0:0
#SBATCH --constraint \"s04|s05\"
./ldak5.1 --max-threads 4 \
          --window-prune 0.98 \
          --window-kb 100 \
          --extract ../gen/snps-unrel-inds.use \
          --bfile ../gen/geno-unrel \
          --thin ../ldak-thin/chr$j-hapmap3 \
          --chr $j
" > sh_script/ldak-thin$j-hapmap3
done

# submit script
for j in {1..22}; do
sbatch -A snpher ../sh_script/ldak-thin$j-hapmap3
done > ../job-records/ldak-thin-hapmap3

# check job completion---
file=job-records/ldak-thin-hapmap3
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# combine snp list
cat ldak-thin/chr{1..22}-hapmap3.in > ldak-thin/ldak-thin-hapmap3.in

#----------------------
# 2. kinship matrix under ldak-thin
#------------------------

echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 60G
#SBATCH -c 10
#SBATCH -t 20:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/ldak-thin-all-unrel \
          --bfile ../gen/geno-unrel \
          --extract ../ldak-thin/ldak-thin-hapmap3.in \
          --power -0.25 \
          --ignore-weights YES \
          --single YES
" > sh_script/ldak-thin-grm-all-snps-unrel

sbatch -A snpher ../sh_script/ldak-thin-grm-all-snps-unrel > ../job-records/ldak-thin-grm-all-snps-unrel

# grm by snp blocks: right vs. left

awk '{split($1, a, /[:]/); if (a[1]<8) print $1}' \
 ldak-thin/ldak-thin-hapmap3.in > gen/left-ldak-thin-hapmap3.snps 
awk '{split($1, a, /[:]/); if (a[1]>=8) print $1}' \
 ldak-thin/ldak-thin-hapmap3.in > gen/right-ldak-thin-hapmap3.snps 

for i in left right; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 60G
#SBATCH -c 10
#SBATCH -t 20:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/ldak-thin-$i-unrel \
          --bfile ../gen/geno-unrel \
          --extract ../gen/$i-ldak-thin-hapmap3.snps \
          --power -0.25 \
          --ignore-weights YES \
          --single YES
" > sh_script/ldak-thin-grm-$i-unrel
done

for i in left right; do
sbatch -A snpher ../sh_script/ldak-thin-grm-$i-unrel 
done > ../job-records/grm-ldak-thin-by-snps-unrel 

```

### fast-reml

```bash

#:::
# under gcta
#:::

# make script files-------------------------------------------------------------
mkdir reml-good
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 100G
#SBATCH -c 2
#SBATCH -t 5:0:0

./ldak5.1 --fast-reml ../reml-good/$i-gcta-$k \
          --repetitions 20 \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --keep ../unrelated/rand.100000 \
          --covar ../phen/covariates.use \
          --grm ../kinship/gcta-$k-unrel \
          --max-threads 2 \
          --single YES
"> sh_script/$i-reml-good-gcta-$k-snps
done
done

# submit script files-----------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
sbatch -A snpher ../sh_script/$i-reml-good-gcta-$k-snps
done
done>../job-records/reml-good-gcta

# check job completion----------------------------------------------------------
file=job-records/reml-good-gcta
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

#:::
# under ldak-thin
#:::

# make script files-------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 100G
#SBATCH -c 2
#SBATCH -t 5:0:0

./ldak5.1 --fast-reml ../reml-good/$i-ldak-thin-$k \
          --repetitions 20 \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --keep ../unrelated/rand.100000 \
          --covar ../phen/covariates.use \
          --grm ../kinship/ldak-thin-$k-unrel \
          --max-threads 2 \
          --single YES
"> sh_script/$i-reml-good-ldak-thin-$k-snps
done
done

# submit script files-----------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
sbatch -A snpher ../sh_script/$i-reml-good-ldak-thin-$k-snps
done
done >../job-records/reml-good-ldak-thin

# check job completion----------------------------------------------------------
file=job-records/reml-good-ldak-thin
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

```

### inflation test


```bash
#::::::::::::
# under gcta
#::::::::::::

# extract h2 estimates ---------------------------------------------------------
# right
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=reml-good/$i-gcta-right.reml
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/est.tmp
awk '$1=="Converged" {print$2}' $outfile >> summary/converge.tmp
done
paste  summary/est.tmp \
       summary/converge.tmp \
       | awk 'BEGIN{print "code h2 se converge"}{print i, $0}' \
       > summary/reml-gcta-good.right
rm summary/est.tmp summary/converge.tmp

# left
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=reml-good/$i-gcta-left.reml
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/est.tmp
awk '$1=="Converged" {print$2}' $outfile >> summary/converge.tmp
done
paste  summary/est.tmp \
       summary/converge.tmp \
       | awk 'BEGIN{print "code h2 se converge"}{print i, $0}' \
       > summary/reml-gcta-good.left
rm summary/est.tmp summary/converge.tmp

# all
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=reml-good/$i-gcta-all.reml
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/est.tmp
awk '$1=="Converged" {print$2}' $outfile >> summary/converge.tmp
done
paste  summary/est.tmp \
       summary/converge.tmp \
       | awk 'BEGIN{print "code h2 se converge"}{print i, $0}' \
       > summary/reml-gcta-good.all
rm summary/est.tmp summary/converge.tmp

# inflation test----------------------------------------------------------------
R

full=read.table(paste0("summary/reml-gcta-good.all"), header=T)
left=read.table(paste0("summary/reml-gcta-good.left"), header=T)
right=read.table(paste0("summary/reml-gcta-good.right"), header=T)
# some analyses did not complete --> match dataframes
dim(full);dim(left);dim(right)
common=intersect(full$code, left$code)
common=intersect(common, right$code)
m1=match(common, right$code)
m2=match(common, full$code)
m3=match(common, left$code)
right=right[m1,]
full=full[m2,]
left=left[m3,]

for(i in 1:dim(full)[1]){
    
    est1=left$h2[i]
    sd1=left$se[i]
    est2=right$h2[i]
    sd2=right$se[i]
    est=full$h2[i]
    sd=full$se[i]
    N=100000
    d1=rnorm(N,est1,sd1)
    d2=rnorm(N,est2,sd2)
    d=rnorm(N,est,sd)
    p=1-mean(d1+d2-d>=0)
    
    out0=data.frame(code=full$code[i],
                    right_est=est2,
                    right_sd=sd2,
                    left_est=est1,
                    left_sd=sd1,
                    all_est=est,
                    all_sd=sd,
                    p_inflation=p)
   if(i==1){out=out0}else{out=rbind(out,out0)}
}
write.table(out, paste0("summary/reml-gcta-inflation-good-gwas.txt"),
            col.names=T, row.names=F, quote=F)

#::::::::::::
# under ldak-thin
#::::::::::::

# extract h2 estimates ---------------------------------------------------------
# right
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=reml-good/$i-ldak-thin-right.reml
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/est.tmp
awk '$1=="Converged" {print$2}' $outfile >> summary/converge.tmp
done
paste  summary/est.tmp \
       summary/converge.tmp \
       | awk 'BEGIN{print "code h2 se converge"}{print i, $0}' \
       > summary/reml-ldak-thin-good.right
rm summary/est.tmp summary/converge.tmp

# left
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=reml-good/$i-ldak-thin-left.reml
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/est.tmp
awk '$1=="Converged" {print$2}' $outfile >> summary/converge.tmp
done
paste  summary/est.tmp \
       summary/converge.tmp \
       | awk 'BEGIN{print "code h2 se converge"}{print i, $0}' \
       > summary/reml-ldak-thin-good.left
rm summary/est.tmp summary/converge.tmp

# all
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=reml-good/$i-ldak-thin-all.reml
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/est.tmp
awk '$1=="Converged" {print$2}' $outfile >> summary/converge.tmp
done
paste  summary/est.tmp \
       summary/converge.tmp \
       | awk 'BEGIN{print "code h2 se converge"}{print i, $0}' \
       > summary/reml-ldak-thin-good.all
rm summary/est.tmp summary/converge.tmp

# inflation test----------------------------------------------------------------
R
full=read.table(paste0("summary/reml-ldak-thin-good.all"), header=T)
left=read.table(paste0("summary/reml-ldak-thin-good.left"), header=T)
right=read.table(paste0("summary/reml-ldak-thin-good.right"), header=T)
# some analyses did not complete --> match dataframes
dim(full);dim(left);dim(right)
common=intersect(full$code, left$code)
common=intersect(common, right$code)
m1=match(common, right$code)
m2=match(common, full$code)
m3=match(common, left$code)
right=right[m1,]
full=full[m2,]
left=left[m3,]

for(i in 1:dim(full)[1]){
    
    est1=left$h2[i]
    sd1=left$se[i]
    est2=right$h2[i]
    sd2=right$se[i]
    est=full$h2[i]
    sd=full$se[i]
    N=100000
    d1=rnorm(N,est1,sd1)
    d2=rnorm(N,est2,sd2)
    d=rnorm(N,est,sd)
    p=1-mean(d1+d2-d>=0)
    
    out0=data.frame(code=full$code[i],
                    right_est=est2,
                    right_sd=sd2,
                    left_est=est1,
                    left_sd=sd1,
                    all_est=est,
                    all_sd=sd,
                    p_inflation=p)
   if(i==1){out=out0}else{out=rbind(out,out0)}
}

write.table(out, paste0("summary/reml-ldak-thin-inflation.txt"),
            col.names=T, row.names=F, quote=F)

```

## HE

### estimation

```bash
# regress grm on covariates-----------------------------------------------------
for grm in gcta-all-unrel gcta-left-unrel gcta-right-unrel ldak-thin-all-unrel ldak-thin-left-unrel ldak-thin-right-unrel; do  
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 50G
#SBATCH -c 5
#SBATCH -t 10:0:0
./ldak5.1 --adjust-grm ../kinship/$grm.covar \
          --grm ../kinship/$grm \
          --covar ../phen/covariates.use \
          --max-threads 5
"> sh_script/$grm-adjust-unrel.sh
done

# submit jobs
for grm in gcta-all-unrel gcta-left-unrel gcta-right-unrel ldak-thin-all-unrel ldak-thin-left-unrel ldak-thin-right-unrel; do
sbatch -A snpher ../sh_script/$grm-adjust-unrel.sh
done > ../job-records/grm-unrel-adjust-for-HE

# check job completion----------------------------------------------------------
file=job-records/grm-unrel-adjust-for-HE
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# HE under gcta-----------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 2G
#SBATCH -c 1
#SBATCH -t 00:30:0

./ldak5.1 --he ../he-good/$i-he-gcta-$k \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --grm ../kinship/gcta-$k-unrel.covar \
          --kinship-details NO \
          --check-root NO \
          --covar ../phen/covariates.use \
          --max-threads 1 \
          --memory-save YES
"> sh_script/$i-he-good-gcta-$k-snps.sh
done
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
sbatch -A snpher ../sh_script/$i-he-good-gcta-$k-snps.sh
done
done > ../job-records/he-good-gcta

# HE under ldak-thin------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 2G
#SBATCH -c 1
#SBATCH -t 0:30:0

./ldak5.1 --he ../he-good/$i-he-ldak-thin-$k \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --grm ../kinship/ldak-thin-$k-unrel.covar \
          --kinship-details NO \
          --check-root NO \
          --covar ../phen/covariates.use \
          --max-threads 1 \
          --memory-save YES
"> sh_script/$i-he-good-ldak-thin-$k-snps.sh
done
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
sbatch -A snpher ../sh_script/$i-he-good-ldak-thin-$k-snps.sh
done
done > ../job-records/he-good-ldak-thin

```

### summary


```bash

#::::::::::::
# under gcta
#::::::::::::

# extract h2 estimates ---------------------------------------------------------
# right
rm summary/he-gcta-unrel.right 
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-good/$i-he-gcta-right.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-gcta-unrel.right
done

# left
rm summary/he-gcta-unrel.left
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-good/$i-he-gcta-left.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-gcta-unrel.left
done

# all
rm summary/he-gcta-unrel.all
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-good/$i-he-gcta-all.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-gcta-unrel.all
done

# inflation test----------------------------------------------------------------
R

full=read.table(paste0("summary/he-gcta-unrel.all"), header=F)
left=read.table(paste0("summary/he-gcta-unrel.left"), header=F)
right=read.table(paste0("summary/he-gcta-unrel.right"), header=F)
names(full)=names(left)=names(right)=c("code","h2","se")
# some analyses did not complete --> match dataframes
dim(full);dim(left);dim(right)
common=intersect(full$code, left$code)
common=intersect(common, right$code)
m1=match(common, right$code)
m2=match(common, full$code)
m3=match(common, left$code)
right=right[m1,]
full=full[m2,]
left=left[m3,]

for(i in 1:dim(full)[1]){
    
    est1=left$h2[i]
    sd1=left$se[i]
    est2=right$h2[i]
    sd2=right$se[i]
    est=full$h2[i]
    sd=full$se[i]
    N=100000
    d1=rnorm(N,est1,sd1)
    d2=rnorm(N,est2,sd2)
    d=rnorm(N,est,sd)
    p=1-mean(d1+d2-d>=0)
    
    out0=data.frame(code=full$code[i],
                    right_est=est2,
                    right_sd=sd2,
                    left_est=est1,
                    left_sd=sd1,
                    all_est=est,
                    all_sd=sd,
                    p_inflation=p)
   if(i==1){out=out0}else{out=rbind(out,out0)}
}
write.table(out, paste0("summary/he-gcta-inflation-unrel-gwas.txt"),
            col.names=T, row.names=F, quote=F)

#::::::::::::
# under ldak-thin
#::::::::::::

# extract h2 estimates ---------------------------------------------------------
# right
rm summary/he-ldak-thin-unrel.right 
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-good/$i-he-ldak-thin-right.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-ldak-thin-unrel.right
done

# left
rm summary/he-ldak-thin-unrel.left
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-good/$i-he-ldak-thin-left.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-ldak-thin-unrel.left
done

# all
rm summary/he-ldak-thin-unrel.all
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-good/$i-he-ldak-thin-all.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-ldak-thin-unrel.all
done

# inflation test----------------------------------------------------------------
R

full=read.table(paste0("summary/he-ldak-thin-unrel.all"), header=F)
left=read.table(paste0("summary/he-ldak-thin-unrel.left"), header=F)
right=read.table(paste0("summary/he-ldak-thin-unrel.right"), header=F)
names(full)=names(left)=names(right)=c("code","h2","se")
# some analyses did not complete --> match dataframes
dim(full);dim(left);dim(right)
common=intersect(full$code, left$code)
common=intersect(common, right$code)
m1=match(common, right$code)
m2=match(common, full$code)
m3=match(common, left$code)
right=right[m1,]
full=full[m2,]
left=left[m3,]

for(i in 1:dim(full)[1]){
    
    est1=left$h2[i]
    sd1=left$se[i]
    est2=right$h2[i]
    sd2=right$se[i]
    est=full$h2[i]
    sd=full$se[i]
    N=100000
    d1=rnorm(N,est1,sd1)
    d2=rnorm(N,est2,sd2)
    d=rnorm(N,est,sd)
    p=1-mean(d1+d2-d>=0)
    
    out0=data.frame(code=full$code[i],
                    right_est=est2,
                    right_sd=sd2,
                    left_est=est1,
                    left_sd=sd1,
                    all_est=est,
                    all_sd=sd,
                    p_inflation=p)
   if(i==1){out=out0}else{out=rbind(out,out0)}
}
write.table(out, paste0("summary/he-ldak-thin-inflation-unrel-gwas.txt"),
            col.names=T, row.names=F, quote=F)

```


<!--chapter:end:04.1-good-gwas.Rmd-->


# Bad GWAS {#bad}

## ID list {#badid}

### initial selection

Randomly select participants with complete data and use the SNP list as for unrelated.


```bash

# extract white, asian & black from ukbb----------------------------------------
R
dat=read.table("phen/covariates.phen", header=T, stringsAsFactors = F)
# White: British(1001)  
# Asian or Asian British: Indian(3001)+Pakistani(3002)+Bangladeshi(3003)+other Asian backgroud(3004)
# Black: Caribbean(4001)+African(4002)+other Black Background(4003)
dat=dat[complete.cases(dat),]
white=1001
asian=3001:3004
black=4001:4003
out1=dat[dat$ethnicity%in%white,]
out2=dat[dat$ethnicity%in%asian,]
out3=dat[dat$ethnicity%in%black,]
write.table(out1[,"eid"], "white-complete-cov.id", col.names=F, row.names=F, quote=F)
write.table(out2[,"eid"], "asian-complete-cov.id", col.names=F, row.names=F, quote=F)
write.table(out3[,"eid"], "black-complete-cov.id", col.names=F, row.names=F, quote=F)
# overlapping UNRELATED WHITE across 14 traits----------------------------------
# overlapping = no missing data for the 14 traits

cp icd10/unrelated.inds overlap.ind # unrelated white British

dir=phen/continuous-traits/
for tt in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
mv overlap.ind temp
awk '(NR==FNR){a[$1];next}($1 in a){print $1, $2}' temp $dir/$tt.raw.pheno > overlap.ind
wc -l overlap.ind
echo $tt
done
rm temp

# overlapping + complete data for covariates + unrelated
awk 'NR==FNR{a[$1]; next} ($1 in a) {print $1, $1}' unrelated/overlap.ind white-complete-cov.id > overlap-white-complete-cov.id # N = 147,008

# overlapping UNRELATED ASIAN across 14 traits----------------------------------
cp asian-complete-cov.id overlap-asian-complete-cov.id
dir=phen/continuous-traits/
for tt in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
mv overlap-asian-complete-cov.id temp
awk '(NR==FNR){a[$1];next}($1 in a){print $1, $2}' temp $dir/$tt.raw.pheno > overlap-asian-complete-cov.id
wc -l overlap-asian-complete-cov.id # N = 4,052
echo $tt
done
rm temp

# relatedness filtering
# see below

# overlapping UNRELATED BLACK across 14 traits----------------------------------
cp black-complete-cov.id overlap-black-complete-cov.id
dir=phen/continuous-traits/
for tt in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
mv overlap-black-complete-cov.id temp
awk '(NR==FNR){a[$1];next}($1 in a){print $1, $2}' temp $dir/$tt.raw.pheno > overlap-black-complete-cov.id
wc -l overlap-black-complete-cov.id # N = 3,583
echo $tt
done
rm temp

# relatedness filtering
# see below

```

### relatedness filtering

For black and Asian people only.


```bash
#-------------
# 0. make bfiles
#------------

# id lists
overlap-white-complete-cov.id # N=147,008
overlap-black-complete-cov.id # N=3,583
overlap-asian-complete-cov.id # N=4,052
awk '{print $0}' overlap-white-complete-cov.id overlap-black-complete-cov.id overlap-asian-complete-cov.id > overlap-mixed-complete-cov.id # 154,643    

# bfiles by chr
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 1:0:0

./plink2 --pfile ../gen/geno_plink/bhr$j \
         --keep ../overlap-mixed-complete-cov.id \
         --extract ../gen/snps-unrel-inds.use \
         --make-bed \
         --memory 20000 \
         --out ../gen/tmp/bhr$j-mix \
         --threads 3 \
"> sh_script/chr$j-mix.sh
done

# submit script
for i in {1..22}; do
sbatch -A snpher ../sh_script/chr$i-mix.sh
done>../job-records/mkbfile-mix-pop

# merge bfiles
rm bfile-mix.list
for j in {1..22}; do
echo  "../gen/tmp/bhr$j-mix" >>bfile-mix.list
done

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 10:0:0
./ldak5.1 --make-bed ../gen/geno-mix \
          --mbfile ../gen/bfile-mix.list \
          --max-threads 3 \
          --exclude-dups YES  
"> sh_script/merge-mbfiles-mix-pop.sh

# submit the script
sbatch -A snpher ../sh_script/merge-mbfiles-mix-pop.sh >../job-records/merge-mbfiles-mix

#-------
# 1. prune SNPs
#-------
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 10G
#SBATCH -c 4
#SBATCH -t 5:0:0
#SBATCH --constraint \"s04|s05\"
./ldak5.1 --max-threads 4 \
          --window-prune 0.05 \
          --window-kb 1000 \
          --bfile ../gen/geno-mix \
          --chr $j \
          --thin ../thin/thin-chr$j
"  > sh_script/thin$j
done

for j in {1..22}; do
sbatch -A snpher ../sh_script/thin$j
done > ../job-records/thin-snps

#-------------
# 2. kinship matrix under GCTA
#-------------

for pop in {black,asian}; do
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 1:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 3 \
          --calc-kins-direct ../kinship/$pop-gcta-thin$j \
          --bfile ../gen/geno-mix \
          --keep ../overlap-$pop-complete-cov.id\
          --extract ../thin/thin-chr$j.in \
          --chr $j \
          --power -1 \
          --ignore-weights YES \
          --single YES
" > sh_script/$pop-grm$j
done
done

# submit files
for pop in {black,asian}; do 
for j in {1..22}; do
sbatch -A snpher ../sh_script/$pop-grm$j
done
done > ../job-records/grm-pops

# merge grms
for pop in {black,asian}; do
rm $pop-grm.list
for j in {1..22} 
do 
echo "../kinship/$pop-gcta-thin$j" >> $pop-grm.list
done
done

for pop in {black,asian}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 80G
#SBATCH -c 10
#SBATCH -t 12:0:0
./ldak5.1 --add-grm ../kinship/$pop-gcta-thin --mgrm ../$pop-grm.list
"> sh_script/$pop-grm.sh
done

for pop in {black,asian}; do
sbatch -A snpher ../sh_script/$pop-grm.sh 
done > ../job-records/grm-merge

#can now delete per-chr files
#rm *gcta-thin{1..22}.*

#-----------------------
# 3. Relatedness filtering 
#------------------------

# relatedness filtering
for pop in {asian,black};do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 2:0:0

./ldak5.1 --filter ../relatedness/$pop-cut.05 \
          --grm ../kinship/$pop-gcta-thin \
          --max-rel 0.05 \
          --max-threads 3
"> sh_script/$pop-rel-cut.05.sh
done

# submit script
for pop in {asian,black};do
sbatch -A snpher ../sh_script/$pop-rel-cut.05.sh
done > ../job-records/relatedness-filtering

# remaining individuals
# asian-cut.05.keep N = 3,448
# black-cut.05.keep N = 3,024
# sum = 6,472
```

### final list

To match the good GWAS, we will have N = 100k in total and replace 6,472 whites with Asians and Blacks.


```bash
# select  
shuf rand.100000 | head -n 93528 > white.rand.93528
cat white.rand.93528 relatedness/asian-cut.05.keep relatedness/black-cut.05.keep > mix-pop-gwas.id 

```

## GWAS-lm

### basic cov

We use basic covariates only: age, sex and townsend.


```bash

# covariates--------------------------------------------------------------------
awk '{print $1, $2, $3, $4, $5}' covariates.use > basic-covariates.use
awk 'NR<=5{print $0}' covariates.use-names > basic-covariates.use

# gwas
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 8G
#SBATCH -c 2
#SBATCH -t 5:0:0

./ldak5.1 --linear ../gwas-mix/$i-linear-chr-$j \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile ../gen/geno-mix \
          --covar ../phen/basic-covariates.use \
          --keep ../mix-pop-gwas.id \
          --extract ../gen/snps-unrel-inds.use \
          --max-threads 2 \
          --chr $j
"> sh_script/$i-linear-chr-$j.sh
done
done

# --covar ../phen/covariates.use

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for j in {1..22}; do
sbatch -A snpher ../sh_script/$i-linear-chr-$j.sh
done
done>../job-records/gwas-mix-pop

# check job completion----------------------------------------------------------
file=job-records/gwas-mix-pop
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# combine results
# only need .summaries & .pvalues
for i in {awake,bmi,quals,chron,ever,fvc,height,imp,neur,pulse,reaction,sbp,snoring,hyper}; do 
for j in {1..22}; do
if [ $j == 1 ]; then
  awk '{print $0}' $i-linear-chr-$j.summaries > $i-linear.summaries
  awk '{print $0}' $i-linear-chr-$j.pvalues > $i-linear.pvalues
else 
  awk 'NR>1 {print $0}' $i-linear-chr-$j.summaries >> $i-linear.summaries
  awk 'NR>1 {print $0}' $i-linear-chr-$j.pvalues >> $i-linear.pvalues
fi
done
done

```

### basic cov + PC
Here we add PC one at a time until the tenth as covariates and conduct GWAS


```bash
# covariates--------------------------------------------------------------------
R
options(scipen = 999)
require(vroom)
cov=vroom("phen/covariates.phen", col_names=T)
nm=c("eid","age","sex_gen", "townsend", paste0("pc",1:40))
cov=cov[,nm]

for(i in 1:10){
  sel=c("eid","eid","age","sex_gen", "townsend", paste0("pc", 1:i))
  out=cov[,sel]
  file1=paste0("phen/covariates-basic-",i,"PC.use")
  file2=paste0("phen/covariates-basic-",i,"PC.use-names")
  write.table(out,file1, col.names=F, row.names=F,quote=F)
  write.table(names(out),file2, col.names=F, row.names=F,quote=F)
}

# gwas--------------------------------------------------------------------------
for j in {1..10}; do
mkdir gwas-mix-$j'PC'
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
out=../gwas-mix-$j'PC'/$i-linear
cov=../phen/covariates-basic-$j'PC'.use
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 6G
#SBATCH -c 2
#SBATCH -t 6:0:0

./ldak5.1 --linear $out \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile ../gen/geno-mix \
          --covar $cov \
          --keep ../mix-pop-gwas.id \
          --extract ../gen/snps-unrel-inds.use \
          --max-threads 2 \
"> sh_script/$i-linear-$j'PC'.sh
done
done

# submit files------------------------------------------------------------------
for j in {1..10}; do
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-linear-$j'PC'.sh
done
done>../job-records/gwas-mix-pc

# check job completion----------------------------------------------------------
file=job-records/gwas-mix-pc
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

```

## GWAS-bolt-lmm


```bash

# make filter files-------------------------------------------------------------
fam=gen/geno-mix.fam
bim=gen/geno-mix.bim
id=mix-pop-gwas.id
snp=gen/snps-unrel-inds.use
id_remove=BOLT-LMM_v2.3.5/mix-pop-gwas-id.exclude
snp_exclude=BOLT-LMM_v2.3.5/snps-unrel-inds.remove

awk 'NR==FNR {a[$1];next} 
     !($2 in a) {print $2}' $snp $bim > $snp_exclude
# note the list is empty because $snp & $bim contain the same list
     
awk 'NR==FNR {a[$1];next} 
     !($1 in a) {print $0}' $id $fam > $id_remove

# organize phenotype files------------------------------------------------------
R
options(scipen = 999)
require(vroom)

# covariate file
cov=vroom("phen/covariates.phen", col_names=T)
nm=c("age","sex_gen", "townsend" ,paste0("pc",1:40))

traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(i in 1:length(traits)){
    trait=traits[i]
    dat=vroom(paste0("phen/continuous-traits/",trait,".raw.pheno"), col_names=F)
    m=match(dat$X1,cov$eid)
    
    out=data.frame(FID=dat$X1,
                   IID=dat$X2,
                   PHENO=dat$X3,
                   cov[m,nm])
    old=names(out)
    new=c(names(out)[1:3], paste0("COV",1:43))
    names(out)=new
    write.table(out,paste0("BOLT-LMM_v2.3.5/dat/",trait,".dat"), col.names=T,
                row.names=F, quote=F)
    if(i==1){
            out.nm=data.frame(name=new,true_name=old, stringsAsFactors=F)
            write.table(out.nm,"BOLT-LMM_v2.3.5/dat/cov-names", 
            col.names=T, row.names=F, quote=F)
            }
}

# model SNPs--------------------------------------------------------------------
# subset snps used in GRM for BOLT-LMM
# we previously thinned SNPs: see 'relatedness filtering' section of bad GWAS
# Now we just merge them across chromsomes
out=thin/geno-mix-thin-snps
rm $out 
for i in {1..22}; do
awk '{print $0}' thin/thin-chr$i.in >> $out
done

# run BOLT-LMM------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
dir=../gwas-bad
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 10
#SBATCH -t 5:0:0

../bolt --bfile=../gen/geno-mix \
         --phenoFile=../dat/$i.dat \
         --phenoCol=PHENO \
         --remove=../mix-pop-gwas-id.exclude\
         --lmm \
         --LDscoresFile=../tables/LDSCORE.1000G_EUR.tab.gz \
         --LDscoresMatchBp \
         --covarFile=../dat/$i.dat \
         --qCovarCol=COV{1:3}\
         --modelSnps=../geno-mix-thin-snps \
         --maxMissingPerSnp=1 \
         --maxMissingPerIndiv=1 \
         --statsFile=$dir/$i.out \
         --numThreads=10 \
       2>&1 | tee $dir/$i.log
"> sh_script/$i-bolt-lmm.sh
done

# submit scripts
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-bolt-lmm.sh
done>../job-records/bolt-lmm-bad

# check job completion----------------------------------------------------------
file=job-records/bolt-lmm-bad
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# organize results -------------------------------------------------------------
mkdir gwas-norm-337k-out

for trait in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do

gwas=gwas-norm-337k/$trait-linear.summaries
aver2=summary/ave-r2-10k-snps-ukbb-norm
out=gwas-norm-337k-out/$trait.out

awk 'NR>1 {print $1, $5}' $gwas > tmp/gwas.tmp    

awk 'NR==FNR {a[$1]; b[$1]=$2; next} 
     {if ($1 in a) print b[$1]; 
     else print "NA" }' $aver2 tmp/gwas.tmp > tmp/aver2.tmp

# put info together
paste tmp/gwas.tmp \
      tmp/aver2.tmp \
      | awk 'BEGIN{OFS=";" ; 
                  print "snp;chisq;aver2"}
             {$1=$1}1' > $out
done

```

## aver2_j

### calc aver2_j

Note we previously computed aver2_j based using `geno-mix` data. see \@ref(calc_aver2_j).

### bolt-lmm


```bash

mkdir gwas-norm-337k-out

for trait in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do

gwas=gwas-norm-337k/$trait-linear.summaries
aver2=summary/ave-r2-10k-snps-ukbb-norm
out=gwas-norm-337k-out/$trait.out

awk 'NR>1 {print $1, $5}' $gwas > tmp/gwas.tmp    

awk 'NR==FNR {a[$1]; b[$1]=$2; next} 
     {if ($1 in a) print b[$1]; 
     else print "NA" }' $aver2 tmp/gwas.tmp > tmp/aver2.tmp

# put info together
paste tmp/gwas.tmp \
      tmp/aver2.tmp \
      | awk 'BEGIN{OFS=";" ; 
                  print "snp;chisq;aver2"}
             {$1=$1}1' > $out
done

```

### basic cov + PC


```bash

mkdir gwas-norm-337k-out

for trait in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do

gwas=gwas-norm-337k/$trait-linear.summaries
aver2=summary/ave-r2-10k-snps-ukbb-norm
out=gwas-norm-337k-out/$trait.out

awk 'NR>1 {print $1, $5}' $gwas > tmp/gwas.tmp    

awk 'NR==FNR {a[$1]; b[$1]=$2; next} 
     {if ($1 in a) print b[$1]; 
     else print "NA" }' $aver2 tmp/gwas.tmp > tmp/aver2.tmp

# put info together
paste tmp/gwas.tmp \
      tmp/aver2.tmp \
      | awk 'BEGIN{OFS=";" ; 
                  print "snp;chisq;aver2"}
             {$1=$1}1' > $out
done

```


## ldsc intercept

### under gcta

```bash
#:::::::::::::
# using original ldsc
#:::::::::::::

# format stats for ldsc--------------------------------------------------------- 
library(vroom)
options(scipen = 100)
rs=vroom("doug/ukbb.ldsc", col_names=F)
phen=c("awake","bmi","chron","ever","fvc",
       "height","imp","neur","pulse","quals",
       "reaction","sbp","snoring","hyper", "quals")

for(i in phen){
  stat=vroom(paste0("gwas-mix/",i,"-linear.summaries"), col_names=T)
  p=vroom(paste0("gwas-mix/",i,"-linear.pvalues"), col_names=T)
  m1=match(rs$X1, stat$Predictor)
  m2=match(rs$X1, p$Predictor)
  out=data.frame(SNP=rs$X2,
                 N=stat$n[m1],
                 Z=(sqrt(stat$Stat)*stat$Direction)[m1],
                 A1=stat$A1[m1],
                 A2=stat$A2[m1], 
                 pval=p$P[m2], stringsAsFactors=F)
  out=out[complete.cases(out),]
  write.table(out, paste0("gwas-mix/",i,"-linear-rs.summaries"), 
  col.names=T, row.names=F, quote=F)
}

# perform ldsc------------------------------------------------------------------
mkdir out-mix-pop
for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 2G
#SBATCH -c 3
#SBATCH -t 08:00:0

.././munge_sumstats.py \
--sumstats ../../gwas-mix/$i-linear-rs.summaries \
--out ../out-mix-pop/$i \
--merge-alleles ../w_hm3.snplist

.././ldsc.py \
--h2 ../out-mix-pop/$i.sumstats.gz \
--ref-ld-chr ../eur_w_ld_chr/ \
--w-ld-chr ../eur_w_ld_chr/ \
--out ../out-mix-pop/$i-ldsc
">sh_script/ldsc-$i-mix-pop.sh
done

# submit jobs-------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,reaction,sbp,snoring,hyper,quals}; do
sbatch -A snpher ../sh_script/ldsc-$i-mix-pop.sh
done>../../job-records/ldsc-mix-pop

# check job completion----------------------------------------------------------
file=job-records/ldsc-mix-pop
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

#:::::::::::::
# using sumher
#:::::::::::::

# insert genetic distance into bim file-----------------------------------------
# here we want to use the same genetic distance as for unrelated individuals, i.e., good GWAS
R
dat=read.table("gen/geno-mix.bim", header=F, stringsAsFactors=F)
ref=read.table("gen/geno-unrel.bim", header=F, stringsAsFactors=F)
m=match(dat$V2,ref$V2)
out=data.frame(dat$V1, dat$V2, ref$V3[m], dat$V4, dat$V5, dat$V6, stringsAsFactors=F)
write.table(out,"gen/geno-mix.bim2", col.names=F, row.names=F, quote=F)

mv geno-mix.bim geno-mix.bim0
mv geno-mix.bim2 geno-mix.bim

# compute tagging under gcta----------------------------------------------------
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 1
#SBATCH -t 5:0:0

./ldak5.1 --calc-tagging ../tagging-mix-pop/gcta-hapmap3-chr-$j \
          --bfile ../gen/geno-mix \
          --ignore-weights YES \
          --power -1 \
          --window-cm 1 \
          --chr $j
"> sh_script/tagging-gcta-hapmap3-chr$j
done

for j in {1..22}; do
sbatch -A snpher ../sh_script/tagging-gcta-hapmap3-chr$j 
done > ../job-records/tagging-gcta-hadmap3-mix-pop

# check job completion----------------------------------------------------------
file=job-records/tagging-gcta-hadmap3-mix-pop
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# merge tagging files-----------------------------------------------------------
rm list.txt
for j in {1..22}; do 
echo "tagging-mix-pop/gcta-hapmap3-chr-$j.tagging" >> list.txt
done
./ldak5.1 --join-tagging tagging-mix-pop/gcta-hapmap3 --taglist list.txt

# ldsc intercept----------------------------------------------------------------
mkdir sumher-mix
for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 1
#SBATCH -t 5:0:0

./ldak5.1 --sum-hers ../sumher-mix/$i-sumher-gcta \
          --tagfile ../tagging-mix-pop/gcta-hapmap3.tagging \
          --summary ../gwas-mix/$i-linear.summaries \
          --check-sums NO \
          --intercept YES
"> sh_script/$i-sumher-gcta-mix-pop
done

for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-sumher-gcta-mix-pop
done>../job-records/sumher-gcta-mix-pop

# check job completion----------------------------------------------------------
file=job-records/sumher-gcta-mix-pop
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

```

### under ldak-thin


```bash
# get weights-------------------------------------------------------------------
awk < ldak-thin/ldak-thin-hapmap3.in '{print $1, 1}' > ldak-thin/weights.ldak-thin-hapmap3

# calculate tagging under ldak-thin---------------------------------------------
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 40G
#SBATCH -c 5
#SBATCH -t 10:0:0
#SBATCH --constraint \"s04|s05\"
./ldak5.1 --calc-tagging ../tagging-mix-pop/ldak-thin-hapmap3-chr-$j \
          --bfile ../gen/geno-mix \
          --weights ../ldak-thin/weights.ldak-thin-hapmap3 \
          --power -.25 \
          --window-cm 1 \
          --chr $j \
          --save-matrix YES \
          --max-threads 5
" > sh_script/tagging-ldak-thin-hapmap3-chr$j.sh
done

# submit scripts
for j in {1..22}; do
sbatch -A snpher ../sh_script/tagging-ldak-thin-hapmap3-chr$j.sh
done > ../job-records/tagging-ldak-thin-hapmap3

# check job completion----------------------------------------------------------
file=job-records/tagging-ldak-thin-hapmap3
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# merge tagging files-----------------------------------------------------------
rm list.txt
rm matlist.txt
for j in {1..22}; do 
echo "tagging-mix-pop/ldak-thin-hapmap3-chr-$j.tagging" >> list.txt
echo "tagging-mix-pop/ldak-thin-hapmap3-chr-$j.matrix" >> matlist.txt
done
./ldak5.1 --join-tagging tagging-mix-pop/ldak-thin-hapmap3 --taglist list.txt --matlist matlist.txt

# ldsc intercept----------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 1
#SBATCH -t 00:30:0

./ldak5.1 --sum-hers ../sumher-mix/$i-sumher-ldak-thin \
          --tagfile ../tagging-mix-pop/ldak-thin-hapmap3.tagging \
          --summary ../gwas-mix/$i-linear.summaries \
          --check-sums NO \
          --intercept YES
"> sh_script/$i-sumher-ldak-thin-mix
done

for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-sumher-ldak-thin-mix
done>../job-records/sumher-ldak-thin-mix

# check job-completion
file=job-records/sumher-ldak-thin-mix
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

```

### summary


```bash

# without 40 PCs----------------------------------------------------------------------
# original ldsc
grep Intercept *ldsc.log | awk '{split($1, a, /[-]/); split($3, b, /[()]/); print a[1], $2, b[2]}' > ../../summary/ldsc-mix

# sumher under gcta
grep Intercept_Estimate *gcta.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-gcta-mix-est
grep Intercept_SD *gcta.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-gcta-mix-sd
cd ../summary/
paste sumher-gcta-mix-est sumher-gcta-mix-sd | awk '{print $1, $2, $4}' > sumher-gcta-mix
rm sumher-gcta-mix-sd sumher-gcta-mix-est
      
# suhmer under ldak-thin
grep Intercept_Estimate *ldak-thin.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-ldak-thin-mix-est
grep Intercept_SD *ldak-thin.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-ldak-thin-mix-sd
cd ../summary/
paste sumher-ldak-thin-mix-est sumher-ldak-thin-mix-sd | awk '{print $1, $2, $4}' > sumher-ldak-thin-mix
rm sumher-ldak-thin-mix-sd sumher-ldak-thin-mix-est

# with covariates (i.e., including all 40 PCs)-------------------------------------------

# original ldsc
grep Intercept *ldsc.log | awk '{split($1, a, /[-]/); split($3, b, /[()]/); print a[1], $2, b[2]}' > ../../summary/ldsc-mix-with-cov

# sumher under gcta
grep Intercept_Estimate *gcta.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-gcta-mix-est
grep Intercept_SD *gcta.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-gcta-mix-sd
cd ../summary/
paste sumher-gcta-mix-est sumher-gcta-mix-sd | awk '{print $1, $2, $4}' > sumher-gcta-mix-with-cov
rm sumher-gcta-mix-sd sumher-gcta-mix-est
      
# suhmer under ldak-thin
grep Intercept_Estimate *ldak-thin.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-ldak-thin-mix-est
grep Intercept_SD *ldak-thin.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-ldak-thin-mix-sd
cd ../summary/
paste sumher-ldak-thin-mix-est sumher-ldak-thin-mix-sd | awk '{print $1, $2, $4}' > sumher-ldak-thin-mix-with-cov
rm sumher-ldak-thin-mix-sd sumher-ldak-thin-mix-est

```

## REML
### making grms

```bash
# making grm -------------------------------------------------------------------

#:::
# under gcta
#:::

# all snps
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 120G
#SBATCH -c 10
#SBATCH -t 20:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/gcta-all-mix \
          --bfile ../gen/geno-mix \
          --keep ../mix-pop-gwas.id \
          --power -1 \
          --ignore-weights YES \
          --single YES
" > sh_script/grm-all-snps-mix

sbatch -A snpher ../sh_script/grm-all-snps-mix > ../job-records/grm-all-snps-mix

# grm by snp blocks: right vs. left
awk '$1<8 {print $2}' geno-mix.bim > left-mix-pop.snps 
awk '$1>=8 {print $2}' geno-mix.bim > right-mix-pop.snps

for i in left right; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 100G
#SBATCH -c 10
#SBATCH -t 20:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/gcta-$i-mix \
          --bfile ../gen/geno-mix \
          --keep ../mix-pop-gwas.id \
          --extract ../gen/$i-mix-pop.snps \
          --power -1 \
          --ignore-weights YES \
          --single YES
" > sh_script/grm-gcta-$i-mix
done

for i in left right; do
sbatch -A snpher ../sh_script/grm-gcta-$i-mix 
done > ../job-records/grm-gcta-by-snps-mix

# check job completion----------------------------------------------------------
file=job-records/grm-gcta-by-snps-mix
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

#:::
# under ldak-thin
#:::

#-----------
# 1. thin snps
#-----------
# we omit this step and use ldak-thin/ldak-thin-hapmap3.in, which was created previously
# using geno-unrel bfiles. See above.

#----------------------
# 2. kinship matrix under ldak-thin
#------------------------

echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 100G
#SBATCH -c 10
#SBATCH -t 20:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/ldak-thin-all-mix \
          --bfile ../gen/geno-mix \
          --keep ../mix-pop-gwas.id \
          --extract ../ldak-thin/ldak-thin-hapmap3.in \
          --power -0.25 \
          --ignore-weights YES \
          --single YES
" > sh_script/ldak-thin-grm-all-snps-mix

sbatch -A snpher ../sh_script/ldak-thin-grm-all-snps-mix> ../job-records/ldak-thin-grm-all-snps-mix

# grm by snp blocks: right vs. left

awk '{split($1, a, /[:]/); if (a[1]<8) print $1}' \
 ldak-thin/ldak-thin-hapmap3.in > gen/left-ldak-thin-hapmap3.snps 
awk '{split($1, a, /[:]/); if (a[1]>=8) print $1}' \
 ldak-thin/ldak-thin-hapmap3.in > gen/right-ldak-thin-hapmap3.snps 

for i in left right; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 100G
#SBATCH -c 10
#SBATCH -t 20:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/ldak-thin-$i-mix \
          --bfile ../gen/geno-mix \
          --keep ../mix-pop-gwas.id \
          --extract ../gen/$i-ldak-thin-hapmap3.snps \
          --power -0.25 \
          --ignore-weights YES \
          --single YES
" > sh_script/ldak-thin-grm-$i-mix
done

for i in left right; do
sbatch -A snpher ../sh_script/ldak-thin-grm-$i-mix 
done > ../job-records/grm-ldak-thin-by-snps-mix

# check job completion----------------------------------------------------------
file=job-records/grm-ldak-thin-by-snps-mix
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

```

### fast-reml

```bash

#:::
# under gcta
#:::

# make script files-------------------------------------------------------------
mkdir reml-mix
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 100G
#SBATCH -c 2
#SBATCH -t 5:0:0

./ldak5.1 --fast-reml ../reml-mix/$i-gcta-$k \
          --repetitions 20 \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --covar ../phen/basic-covariates.use \
          --grm ../kinship/gcta-$k-mix \
          --max-threads 2 \
          --single YES
"> sh_script/$i-reml-mix-gcta-$k-snps
done
done

# submit script files-----------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
sbatch -A snpher ../sh_script/$i-reml-mix-gcta-$k-snps
done
done>../job-records/reml-mix-gcta

# check job completion----------------------------------------------------------
file=job-records/reml-mix-gcta
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# cancel jobs 
for i in {1..14}; do
job=`awk -v i=$i 'NR==i{print $0}' kill-jobs`
scancel $job
done

#:::
# under ldak-thin
#:::

# make script files-------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 100G
#SBATCH -c 2
#SBATCH -t 5:0:0

./ldak5.1 --fast-reml ../reml-mix/$i-ldak-thin-$k \
          --repetitions 20 \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --covar ../phen/basic-covariates.use \
          --grm ../kinship/ldak-thin-$k-mix \
          --max-threads 2 \
          --single YES
"> sh_script/$i-reml-mix-ldak-thin-$k-snps
done
done

# submit script files-----------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
sbatch -A snpher ../sh_script/$i-reml-mix-ldak-thin-$k-snps
done
done >../job-records/reml-mix-ldak-thin

# check job completion----------------------------------------------------------
file=job-records/reml-mix-ldak-thin
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

```

### inflation test


```bash
#::::::::::::
# under gcta
#::::::::::::

# extract h2 estimates ---------------------------------------------------------
# right
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=reml-mix/$i-gcta-right.reml
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/est.tmp
awk '$1=="Converged" {print$2}' $outfile >> summary/converge.tmp
done
paste  summary/est.tmp \
       summary/converge.tmp \
       | awk 'BEGIN{print "code h2 se converge"}{print i, $0}' \
       > summary/reml-gcta-mix.right
rm summary/est.tmp summary/converge.tmp

# left
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=reml-mix/$i-gcta-left.reml
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/est.tmp
awk '$1=="Converged" {print$2}' $outfile >> summary/converge.tmp
done
paste  summary/est.tmp \
       summary/converge.tmp \
       | awk 'BEGIN{print "code h2 se converge"}{print i, $0}' \
       > summary/reml-gcta-mix.left
rm summary/est.tmp summary/converge.tmp

# all
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=reml-mix/$i-gcta-all.reml
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/est.tmp
awk '$1=="Converged" {print$2}' $outfile >> summary/converge.tmp
done
paste  summary/est.tmp \
       summary/converge.tmp \
       | awk 'BEGIN{print "code h2 se converge"}{print i, $0}' \
       > summary/reml-gcta-mix.all
rm summary/est.tmp summary/converge.tmp

# inflation test----------------------------------------------------------------
R

full=read.table(paste0("summary/reml-gcta-mix.all"), header=T)
left=read.table(paste0("summary/reml-gcta-mix.left"), header=T)
right=read.table(paste0("summary/reml-gcta-mix.right"), header=T)
# some analyses did not complete --> match dataframes
dim(full);dim(left);dim(right)
common=intersect(full$code, left$code)
common=intersect(common, right$code)
m1=match(common, right$code)
m2=match(common, full$code)
m3=match(common, left$code)
right=right[m1,]
full=full[m2,]
left=left[m3,]

for(i in 1:dim(full)[1]){
    
    est1=left$h2[i]
    sd1=left$se[i]
    est2=right$h2[i]
    sd2=right$se[i]
    est=full$h2[i]
    sd=full$se[i]
    N=100000
    d1=rnorm(N,est1,sd1)
    d2=rnorm(N,est2,sd2)
    d=rnorm(N,est,sd)
    p=1-mean(d1+d2-d>=0)
    
    out0=data.frame(code=full$code[i],
                    right_est=est2,
                    right_sd=sd2,
                    left_est=est1,
                    left_sd=sd1,
                    all_est=est,
                    all_sd=sd,
                    p_inflation=p)
   if(i==1){out=out0}else{out=rbind(out,out0)}
}
write.table(out, paste0("summary/reml-gcta-inflation-mix-gwas.txt"),
            col.names=T, row.names=F, quote=F)

#::::::::::::
# under ldak-thin
#::::::::::::

# extract h2 estimates ---------------------------------------------------------
# right
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=reml-mix/$i-ldak-thin-right.reml
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/est.tmp
awk '$1=="Converged" {print$2}' $outfile >> summary/converge.tmp
done
paste  summary/est.tmp \
       summary/converge.tmp \
       | awk 'BEGIN{print "code h2 se converge"}{print i, $0}' \
       > summary/reml-ldak-thin-mix.right
rm summary/est.tmp summary/converge.tmp

# left
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=reml-mix/$i-ldak-thin-left.reml
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/est.tmp
awk '$1=="Converged" {print$2}' $outfile >> summary/converge.tmp
done
paste  summary/est.tmp \
       summary/converge.tmp \
       | awk 'BEGIN{print "code h2 se converge"}{print i, $0}' \
       > summary/reml-ldak-thin-mix.left
rm summary/est.tmp summary/converge.tmp

# all
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=reml-mix/$i-ldak-thin-all.reml
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/est.tmp
awk '$1=="Converged" {print$2}' $outfile >> summary/converge.tmp
done
paste  summary/est.tmp \
       summary/converge.tmp \
       | awk 'BEGIN{print "code h2 se converge"}{print i, $0}' \
       > summary/reml-ldak-thin-mix.all
rm summary/est.tmp summary/converge.tmp

# inflation test----------------------------------------------------------------
R
full=read.table(paste0("summary/reml-ldak-thin-mix.all"), header=T)
left=read.table(paste0("summary/reml-ldak-thin-mix.left"), header=T)
right=read.table(paste0("summary/reml-ldak-thin-mix.right"), header=T)
# some analyses did not complete --> match dataframes
dim(full);dim(left);dim(right)
common=intersect(full$code, left$code)
common=intersect(common, right$code)
m1=match(common, right$code)
m2=match(common, full$code)
m3=match(common, left$code)
right=right[m1,]
full=full[m2,]
left=left[m3,]

for(i in 1:dim(full)[1]){
    
    est1=left$h2[i]
    sd1=left$se[i]
    est2=right$h2[i]
    sd2=right$se[i]
    est=full$h2[i]
    sd=full$se[i]
    N=100000
    d1=rnorm(N,est1,sd1)
    d2=rnorm(N,est2,sd2)
    d=rnorm(N,est,sd)
    p=1-mean(d1+d2-d>=0)
    
    out0=data.frame(code=full$code[i],
                    right_est=est2,
                    right_sd=sd2,
                    left_est=est1,
                    left_sd=sd1,
                    all_est=est,
                    all_sd=sd,
                    p_inflation=p)
   if(i==1){out=out0}else{out=rbind(out,out0)}
}

write.table(out, paste0("summary/reml-ldak-thin-inflation-mix-gwas.txt"),
            col.names=T, row.names=F, quote=F)

```


## HE

### estimation 


```bash
# regress grm on covariates-----------------------------------------------------

for grm in gcta-all-mix gcta-left-mix gcta-right-mix ldak-thin-all-mix ldak-thin-left-mix ldak-thin-right-mix; do  
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 50G
#SBATCH -c 5
#SBATCH -t 10:0:0
./ldak5.1 --adjust-grm ../kinship/$grm.covar \
          --grm ../kinship/$grm \
          --covar ../phen/basic-covariates.use \
          --max-threads 5
"> sh_script/$grm-adjust.sh
done

# submit jobs
for grm in gcta-all-mix gcta-left-mix gcta-right-mix ldak-thin-all-mix ldak-thin-left-mix ldak-thin-right-mix; do
sbatch -A snpher ../sh_script/$grm-adjust.sh
done > ../job-records/grm-adjust-for-HE

# check job completion----------------------------------------------------------
file=job-records/grm-adjust-for-HE
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# HE under gcta-----------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 2G
#SBATCH -c 1
#SBATCH -t 00:30:0

./ldak5.1 --he ../he-mix/$i-he-gcta-$k \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --grm ../kinship/gcta-$k-mix.covar \
          --kinship-details NO \
          --check-root NO \
          --covar ../phen/basic-covariates.use \
          --max-threads 1 \
          --memory-save YES
"> sh_script/$i-he-gcta-$k-snps.sh
done
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
sbatch -A snpher ../sh_script/$i-he-gcta-$k-snps.sh
done
done > ../job-records/he-gcta

# HE under ldak-thin------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 2G
#SBATCH -c 1
#SBATCH -t 0:30:0

./ldak5.1 --he ../he-mix/$i-he-ldak-thin-$k \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --grm ../kinship/ldak-thin-$k-mix.covar \
          --kinship-details NO \
          --check-root NO \
          --covar ../phen/basic-covariates.use \
          --max-threads 1 \
          --memory-save YES
"> sh_script/$i-he-ldak-thin-$k-snps.sh
done
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
sbatch -A snpher ../sh_script/$i-he-ldak-thin-$k-snps.sh
done
done > ../job-records/he-ldak-thin

```

### summary


```bash

#::::::::::::
# under gcta
#::::::::::::

# extract h2 estimates ---------------------------------------------------------
# right
rm summary/he-gcta-mix.right 
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-mix/$i-he-gcta-right.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-gcta-mix.right
done

# left
rm summary/he-gcta-mix.left
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-mix/$i-he-gcta-left.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-gcta-mix.left
done

# all
rm summary/he-gcta-mix.all
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-mix/$i-he-gcta-all.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-gcta-mix.all
done

# inflation test----------------------------------------------------------------
R

full=read.table(paste0("summary/he-gcta-mix.all"), header=F)
left=read.table(paste0("summary/he-gcta-mix.left"), header=F)
right=read.table(paste0("summary/he-gcta-mix.right"), header=F)
names(full)=names(left)=names(right)=c("code","h2","se")
# some analyses did not complete --> match dataframes
dim(full);dim(left);dim(right)
common=intersect(full$code, left$code)
common=intersect(common, right$code)
m1=match(common, right$code)
m2=match(common, full$code)
m3=match(common, left$code)
right=right[m1,]
full=full[m2,]
left=left[m3,]

for(i in 1:dim(full)[1]){
    
    est1=left$h2[i]
    sd1=left$se[i]
    est2=right$h2[i]
    sd2=right$se[i]
    est=full$h2[i]
    sd=full$se[i]
    N=100000
    d1=rnorm(N,est1,sd1)
    d2=rnorm(N,est2,sd2)
    d=rnorm(N,est,sd)
    p=1-mean(d1+d2-d>=0)
    
    out0=data.frame(code=full$code[i],
                    right_est=est2,
                    right_sd=sd2,
                    left_est=est1,
                    left_sd=sd1,
                    all_est=est,
                    all_sd=sd,
                    p_inflation=p)
   if(i==1){out=out0}else{out=rbind(out,out0)}
}
write.table(out, paste0("summary/he-gcta-inflation-mix-gwas.txt"),
            col.names=T, row.names=F, quote=F)


#::::::::::::
# under ldak-thin
#::::::::::::

# extract h2 estimates ---------------------------------------------------------
# right
rm summary/he-ldak-thin-mix.right 
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-mix/$i-he-ldak-thin-right.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-ldak-thin-mix.right
done

# left
rm summary/he-ldak-thin-mix.left
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-mix/$i-he-ldak-thin-left.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-ldak-thin-mix.left
done

# all
rm summary/he-ldak-thin-mix.all
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-mix/$i-he-gcta-all.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-ldak-thin-mix.all
done

# inflation test----------------------------------------------------------------
R

full=read.table(paste0("summary/he-ldak-thin-mix.all"), header=F)
left=read.table(paste0("summary/he-ldak-thin-mix.left"), header=F)
right=read.table(paste0("summary/he-ldak-thin-mix.right"), header=F)
names(full)=names(left)=names(right)=c("code","h2","se")
# some analyses did not complete --> match dataframes
dim(full);dim(left);dim(right)
common=intersect(full$code, left$code)
common=intersect(common, right$code)
m1=match(common, right$code)
m2=match(common, full$code)
m3=match(common, left$code)
right=right[m1,]
full=full[m2,]
left=left[m3,]

for(i in 1:dim(full)[1]){
    
    est1=left$h2[i]
    sd1=left$se[i]
    est2=right$h2[i]
    sd2=right$se[i]
    est=full$h2[i]
    sd=full$se[i]
    N=100000
    d1=rnorm(N,est1,sd1)
    d2=rnorm(N,est2,sd2)
    d=rnorm(N,est,sd)
    p=1-mean(d1+d2-d>=0)
    
    out0=data.frame(code=full$code[i],
                    right_est=est2,
                    right_sd=sd2,
                    left_est=est1,
                    left_sd=sd1,
                    all_est=est,
                    all_sd=sd,
                    p_inflation=p)
   if(i==1){out=out0}else{out=rbind(out,out0)}
}
write.table(out, paste0("summary/he-ldak-thin-inflation-mix-gwas.txt"),
            col.names=T, row.names=F, quote=F)

```



<!--chapter:end:04.2-bad-gwas.Rmd-->


# UKBB recommended {#ukbb}

## total N = 337k

Here we want to identify the unrelated white individuals recommended by the UKBB.

Email from Florian:
"We restrict individuals to the ones used for computing the principal components (PCs) in the UK Biobank (Field 22020). These individuals are unrelated and have passed some quality control including removing samples with a missing rate on autosomes larger than 0.02, having a mismatch between inferred sex and self-reported sex, and outliers based on heterozygosity (more details can be found in section S3 of Bycroft et al. (2018))."
And the White British are from this: https://biobank.ctsu.ox.ac.uk/crystal/field.cgi?id=22006

So required data fields:
PC: 22020
White british: 22006  


```bash

options(scipen = 100)
# extract data
head=read.table("phen/ukb45861.header", sep=",", header=F, stringsAsFactors = F)
# function to get the variables
get=function(nm){
  colnum=grep(nm,head,fixed=TRUE)
  out=data.frame(t(rbind(colnum, head[,colnum])))
  names(out)=c("column", "field")
  return (out)
}

# get the variables
out=rbind(get('22020-0'), # PC
      get('22006-0')) # white british

write.table(out,"phen/vars.colnum", col.names=F, row.names=F, sep="\t", quote=F)

# extract dat
awk -F '","' '(NR==FNR){a[$1];next}{printf "%s\"", $1;for(i in a){printf " \"%s\"", $i};printf "\n"}' phen/vars.colnum phen/ukb45861.csv > phen/ukbb-recommended.dat

# get the id list of the intersect of the two data fields
dat=read.table("phen/ukbb-recommended.dat", header=T, stringsAsFactors=F)
id1=dat$eid[dat$X22020.0.0==1 & !is.na(dat$X22020.0.0)]
id2=dat$eid[dat$X22006.0.0==1 & !is.na(dat$X22006.0.0)]
out=intersect(id1,id2) # N = 337,462
write.table(out, "ukbb-recommend.id", col.names=F, row.names=F, quote=F)

```

## QC of hp3 SNPs

Here we do QC to 1.2M hapmap3 SNPs for the UKBB recommended individuals.


```bash

# QC SNPs-----------------------------------------------------------------------
# stating number of SNPs= 1,184,423
# note: at this stage, we use all UKBB recommended IDs 
# we will do a random selection of IDs later.
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 1:0:0

./plink2 --pfile ../gen/geno_plink/bhr$j \
         --keep ../ukbb-recommend.id \
         --extract ../doug/ukbb.ldsc \
         --hwe 0.0001 \
         --hard-call-threshold .05 \
         --mach-r2-filter 0.8 2 \
         --make-bed \
         --memory 20000 \
         --out ../gen/tmp/bhr$j-norm \
         --threads 3
"> sh_script/chr$j.sh
done

# submit jobs
for j in {1..22}; do
sbatch -A snpher ../sh_script/chr$j.sh
done > ../job-records/qc-norm

# merge files
rm bfile-norm.list
for j in {1..22}; do
echo  "../gen/tmp/bhr$j-norm" >>bfile-norm.list
done

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 20:0:0
./ldak5.1 --make-bed ../gen/geno-norm \
          --mbfile ../gen/bfile-norm.list \
          --max-threads 3 \
          --exclude-dups YES  
"> sh_script/mbfile-norm.sh

# submit the script
sbatch -A snpher ../sh_script/mbfile-norm.sh >../job-records/mbfiles-norm

# randomly select 100k individuals 
shuf ukbb-recommend.id | head -n 100000 > ukbb-recommned-rand.100000

# make bfile for these individuals
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 20:0:0
./ldak5.1 --make-bed ../gen/geno-norm-100k \
          --bfile ../gen/geno-norm \
          --keep ../ukbb-recommned-rand.100000 \
          --max-threads 3 \
          --exclude-dups YES  
"> sh_script/mbfile-norm-100k.sh

# submit the script
sbatch -A snpher ../sh_script/mbfile-norm-100k.sh >../job-records/mbfiles-norm-100k


# MAF & call-rate 
awk < geno-norm-100k.stats '($5>.01 && $6>=0.95 && NR>1){print $1}' > snps-norm-100k.use
# m = 1,100,799 SNPs
 
```

## GWAS-100k


```bash

# linear regression-------------------------------------------------------------
mkdir gwas-norm
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 10G
#SBATCH -c 2
#SBATCH -t 10:0:0

./ldak5.1 --linear ../gwas-norm/$i-linear \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile ../gen/geno-norm-100k \
          --keep ../ukbb-recommned-rand.100000 \
          --extract ../gen/snps-norm-100k.use \
          --covar ../phen/covariates.use \
          --max-threads 2 \
"> sh_script/$i-linear.sh
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-linear.sh
done>../job-records/gwas-norm

# check job completion----------------------------------------------------------
file=job-records/gwas-norm
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

```

## GWAS-337k

Here we do gwas for all recommended individuals. Previously we made a bfile for all these individuals.

  * **bfile** (after QC) gen/geno-norm
  * **id list** ukbb-recommend.id


```bash

# snp list----------------------------------------------------------------------
# MAF & call rate filtering
awk < gen/geno-norm.stats '($5>.01 && $6>=0.95 && NR>1){print $1}' > gen/snps-norm.use #1,100,715 snps

# GWAS -------------------------------------------------------------------------
mkdir gwas-norm-337k
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 10:0:0

./ldak5.1 --linear ../gwas-norm-337k/$i-linear \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile ../gen/geno-norm \
          --keep ../ukbb-recommend.id \
          --extract ../gen/snps-norm.use \
          --covar ../phen/covariates.use \
          --max-threads 3 \
"> sh_script/$i-linear.sh
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-linear.sh
done>../job-records/gwas-norm-337k

# check job completion----------------------------------------------------------
file=job-records/gwas-norm-337k
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

```

## ldsc-100k

### under gcta


```bash

#:::::::::::::
# using original ldsc
#:::::::::::::

# format stats for ldsc--------------------------------------------------------- 
library(vroom)
options(scipen = 100)
rs=vroom("doug/ukbb.ldsc", col_names=F)
phen=c("awake","bmi","chron","ever","fvc",
       "height","imp","neur","pulse","quals",
       "reaction","sbp","snoring","hyper", "quals")

for(i in phen){
  stat=vroom(paste0("gwas-norm/",i,"-linear.summaries"), col_names=T)
  p=vroom(paste0("gwas-norm/",i,"-linear.pvalues"), col_names=T)
  m1=match(rs$X1, stat$Predictor)
  m2=match(rs$X1, p$Predictor)
  out=data.frame(SNP=rs$X2,
                 N=stat$n[m1],
                 Z=(sqrt(stat$Stat)*stat$Direction)[m1],
                 A1=stat$A1[m1],
                 A2=stat$A2[m1], 
                 pval=p$P[m2], stringsAsFactors=F)
  out=out[complete.cases(out),]
  write.table(out, paste0("gwas-norm/",i,"-linear-rs.summaries"), 
  col.names=T, row.names=F, quote=F)
}

# perform ldsc------------------------------------------------------------------
mkdir out-norm-100k
for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 2G
#SBATCH -c 3
#SBATCH -t 24:00:0

.././munge_sumstats.py \
--sumstats ../../gwas-norm/$i-linear-rs.summaries \
--out ../out-norm-100k/$i \
--merge-alleles ../w_hm3.snplist

.././ldsc.py \
--h2 ../out-norm-100k/$i.sumstats.gz \
--ref-ld-chr ../eur_w_ld_chr/ \
--w-ld-chr ../eur_w_ld_chr/ \
--out ../out-norm-100k/$i-ldsc
">sh_script/ldsc-$i-norm-100k.sh
done

# submit jobs-------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,reaction,sbp,snoring,hyper,quals}; do
sbatch -A snpher ../sh_script/ldsc-$i-norm-100k.sh
done>../../job-records/ldsc-norm-100k

#:::::::::::::
# using sumher
#:::::::::::::

# insert genetic distance into bim file-----------------------------------------
head geno-norm-100k.fam > small-norm

for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --mem 8G
#SBATCH -c 1
#SBATCH -t 3:00:0
#SBATCH --constraint \"s04|s05\"
./plink1.9 --bfile ../gen/geno-norm-100k \
          --chr $j \
          --cm-map /home/zhoux/snpher/faststorage/genetic_maps/genetic_map_chr@_combined_b37.txt \
          --make-bed \
          --out new$j \
          --keep ../gen/small-norm
" > sh_script/map$j
done

for j in {1..22}; do
sbatch -A snpher ../sh_script/map$j
done > genetic-distance-hapmap3

cd /home/zhoux/dsmwpred/xuan/quality-control/qc-10oct/gen
cat new{1..22}.bim | awk '{print $2, $3}' > maps-hapmap3-norm-100k.txt
rm new{1..22}.{bim,bed,fam,log}

awk '(NR==FNR){arr[$1]=$2;next}{print $1, $2, arr[$2], $4, $5, $6}' > geno-norm-100k.bim2 maps-hapmap3-norm-100k.txt geno-norm-100k.bim
mv geno-norm-100k.bim geno-norm-100k.bim0
mv geno-norm-100k.bim2 geno-norm-100k.bim

# compute tagging under gcta----------------------------------------------------
mkdir tagging-norm-100k
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 1
#SBATCH -t 5:0:0

./ldak5.1 --calc-tagging ../tagging-norm-100k/gcta-hapmap3-chr-$j \
          --bfile ../gen/geno-norm-100k \
          --ignore-weights YES \
          --power -1 \
          --window-cm 1 \
          --chr $j
"> sh_script/tagging-gcta-hapmap3-chr$j
done

for j in {1..22}; do
sbatch -A snpher ../sh_script/tagging-gcta-hapmap3-chr$j 
done > ../job-records/tagging-gcta-hapmap3-norm-100k

# check job completion----------------------------------------------------------
file=job-records/tagging-gcta-hapmap3-norm-100k
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# merge tagging files-----------------------------------------------------------
rm list.txt
for j in {1..22}; do 
echo "tagging-norm-100k/gcta-hapmap3-chr-$j.tagging" >> list.txt
done
./ldak5.1 --join-tagging tagging-norm-100k/gcta-hapmap3 --taglist list.txt

# ldsc intercept----------------------------------------------------------------
mkdir sumher-norm-100k
dirin1=tagging-norm-100k
dirin2=gwas-norm
dirout=sumher-norm-100k
for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 1
#SBATCH -t 5:0:0

./ldak5.1 --sum-hers ../$dirout/$i-sumher-gcta \
          --tagfile ../$dirin1/gcta-hapmap3.tagging \
          --summary ../$dirin2/$i-linear.summaries \
          --check-sums NO \
          --intercept YES
"> sh_script/$i-sumher-gcta
done

for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-sumher-gcta
done>../job-records/sumher-gcta-norm-100k

```

### under ldak-thin


```bash

# get weights-------------------------------------------------------------------
awk < ldak-thin/ldak-thin-norm.in '{print $1, 1}' > ldak-thin/weights.ldak-thin-norm

# calculate tagging under ldak-thin---------------------------------------------
dirout=tagging-norm-100k
filein1=geno-norm-100k
filein2=weights.ldak-thin-norm
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 40G
#SBATCH -c 5
#SBATCH -t 10:0:0
#SBATCH --constraint \"s04|s05\"
./ldak5.1 --calc-tagging ../$dirout/ldak-thin-hapmap3-chr-$j \
          --bfile ../gen/$filein1 \
          --weights ../ldak-thin/$filein2 \
          --power -.25 \
          --window-cm 1 \
          --chr $j \
          --save-matrix YES \
          --max-threads 5
" > sh_script/tagging-ldak-thin-hapmap3-chr$j.sh
done

# submit scripts
for j in {1..22}; do
sbatch -A snpher ../sh_script/tagging-ldak-thin-hapmap3-chr$j.sh
done > ../job-records/tagging-ldak-thin-norm-100k

# check job completion----------------------------------------------------------
file=job-records/tagging-ldak-thin-norm-100k
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# merge tagging files-----------------------------------------------------------
rm list.txt
rm matlist.txt
for j in {1..22}; do
echo "tagging-norm-100k/ldak-thin-hapmap3-chr-$j.tagging" >> list.txt
echo "tagging-norm-100k/ldak-thin-hapmap3-chr-$j.matrix" >> matlist.txt
done
dirout=tagging-norm-100k
./ldak5.1 --join-tagging $dirout/ldak-thin-hapmap3 --taglist list.txt --matlist matlist.txt

# ldsc intercept----------------------------------------------------------------
dirin1=tagging-norm-100k
dirin2=gwas-norm
dirout=sumher-norm-100k
for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 1
#SBATCH -t 00:30:0

./ldak5.1 --sum-hers ../$dirout/$i-sumher-ldak-thin \
          --tagfile ../$dirin1/ldak-thin-hapmap3.tagging \
          --summary ../$dirin2/$i-linear.summaries \
          --check-sums NO \
          --intercept YES
"> sh_script/$i-sumher-ldak-thin
done

for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-sumher-ldak-thin
done>../job-records/sumher-ldak-thin-norm-100k

```

### summary


```bash

# original ldsc
grep Intercept *ldsc.log | awk '{split($1, a, /[-]/); split($3, b, /[()]/); print a[1], $2, b[2]}' > ../../summary/ldsc-norm-100k

# sumher under gcta
grep Intercept_Estimate *gcta.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-gcta-norm-100k-est
grep Intercept_SD *gcta.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-gcta-norm-100k-sd
cd ../summary/
paste sumher-gcta-norm-100k-est sumher-gcta-norm-100k-sd | awk '{print $1, $2, $4}' > sumher-gcta-norm-100k
      
# suhmer under ldak-thin
grep Intercept_Estimate *ldak-thin.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-ldak-thin-norm-100k-est
grep Intercept_SD *ldak-thin.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-ldak-thin-norm-100k-sd
cd ../summary/
paste sumher-ldak-thin-norm-100k-est sumher-ldak-thin-norm-100k-sd | awk '{print $1, $2, $4}' > sumher-ldak-thin-norm-100k

```

## ldsc-337k


```bash
 #:::::::::::::
# using original ldsc
#:::::::::::::

# format stats for ldsc--------------------------------------------------------- 
library(vroom)
options(scipen = 100)
rs=vroom("doug/ukbb.ldsc", col_names=F)
phen=c("awake","bmi","chron","ever","fvc",
       "height","imp","neur","pulse","quals",
       "reaction","sbp","snoring","hyper", "quals")

for(i in phen){
  stat=vroom(paste0("gwas-norm-337k/",i,"-linear.summaries"), col_names=T)
  p=vroom(paste0("gwas-norm-337k/",i,"-linear.pvalues"), col_names=T)
  m1=match(rs$X1, stat$Predictor)
  m2=match(rs$X1, p$Predictor)
  out=data.frame(SNP=rs$X2,
                 N=stat$n[m1],
                 Z=(sqrt(stat$Stat)*stat$Direction)[m1],
                 A1=stat$A1[m1],
                 A2=stat$A2[m1], 
                 pval=p$P[m2], stringsAsFactors=F)
  out=out[complete.cases(out),]
  write.table(out, paste0("gwas-norm-337k/",i,"-linear-rs.summaries"), 
  col.names=T, row.names=F, quote=F)
}

# perform ldsc------------------------------------------------------------------
mkdir out-norm-337k

for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 8G
#SBATCH -c 3
#SBATCH -t 24:00:0

.././munge_sumstats.py \
--sumstats ../../gwas-norm-337k/$i-linear-rs.summaries \
--out ../out-norm-337k/$i \
--merge-alleles ../w_hm3.snplist

.././ldsc.py \
--h2 ../out-norm-337k/$i.sumstats.gz \
--ref-ld-chr ../eur_w_ld_chr/ \
--w-ld-chr ../eur_w_ld_chr/ \
--out ../out-norm-337k/$i-ldsc
">sh_script/ldsc-$i-norm-337k.sh
done

# submit jobs-------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,reaction,sbp,snoring,hyper,quals}; do
sbatch -A snpher ../sh_script/ldsc-$i-norm-337k.sh
done>../../job-records/ldsc-norm-337k

# summary ----------------------------------------------------------------------

grep Intercept *ldsc.log | awk '{split($1, a, /[-]/); split($3, b, /[()]/); print a[1], $2, b[2]}' > ../../summary/ldsc-norm-337k

R
ldsc=read.table("summary/ldsc-norm-337k", header=F, stringsAsFactors = F)

# get p-values for Wald tests
# H0: intercept = 1
alpha=0.05/14 # Bonferroni corrected alpha
ldsc=data.frame(trait=ldsc$V1, est=ldsc$V2, se=ldsc$V3,
                wald_p=pchisq(((ldsc$V2-1)/ldsc$V3)^2, df=1, lower.tail=F),
                sig=pchisq(((ldsc$V2-1)/ldsc$V3)^2, df=1, lower.tail=F) < alpha,
                stringsAsFactors = F)
```

## aver2_j-337k

### calc


```bash

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  snp lists
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# new directory
mkdir inflation/norm
mkdir inflation/norm/out

# lista & listb
m=10000
infile=gen/snps-norm.use
left=inflation/norm/left-snps
right=inflation/norm/right-snps

awk '{split($1, a, ":");
    if (a[1]<8) print $1 }' $infile | shuf | head -n $m >$left  
awk '{split($1, a, ":");
    if (a[1]>=8) print $1 }' $infile | shuf | head -n $m >$right

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  compute ave r^2_j
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

lista=../inflation/norm/right-snps
listb=../inflation/norm/left-snps
bfile=../gen/geno-norm
out=../inflation/norm/out/10k-snps

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 60G
#SBATCH -c 7
#SBATCH -t 00:10:0
./ldak5.2 --max-threads 7 \
          --calc-inflation $out \
          --bfile $bfile \
          --lista $lista \
          --listb $listb
">sh_script/calc-r-10k-snps

# submit the job
sbatch -A snpher ../sh_script/calc-r-10k-snps >../job-records/calc-r-norm

#  compute ave r_j^2 for each i-------------------------------------------------
R
require(vroom)

nm="10k-snps"
dat=vroom(paste0("inflation/norm/out/",nm,".pairwise"), col_names=F)
lista=read.table(paste0("inflation/norm/out/",nm,".predictorsa"), 
                stringsAsFactors = F)
listb=read.table(paste0("inflation/norm/out/",nm,".predictorsb"), 
                stringsAsFactors = F)

dat=dat[,-c(10001)]^2
outb=data.frame(predictor=listb$V1, ave_r2=apply(dat,2, mean))
outa=data.frame(predictor=lista$V1, ave_r2=apply(dat,1, mean))
out=rbind(outa,outb)

write.table(out,paste0("summary/ave-r2-",nm, "-ukbb-norm"), col.names=F, row.names=F, quote=F)  

```

### organize data


```bash

mkdir gwas-norm-337k-out

for trait in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do

gwas=gwas-norm-337k/$trait-linear.summaries
aver2=summary/ave-r2-10k-snps-ukbb-norm
out=gwas-norm-337k-out/$trait.out

awk 'NR>1 {print $1, $5}' $gwas > tmp/gwas.tmp    

awk 'NR==FNR {a[$1]; b[$1]=$2; next} 
     {if ($1 in a) print b[$1]; 
     else print "NA" }' $aver2 tmp/gwas.tmp > tmp/aver2.tmp

# put info together
paste tmp/gwas.tmp \
      tmp/aver2.tmp \
      | awk 'BEGIN{OFS=";" ; 
                  print "snp;chisq;aver2"}
             {$1=$1}1' > $out
done

```

### chisq ~ aver2_j


```r
#::::::::::::::::::::::::::::::::::::::::::::::
# estimate slope: chisq ~ aver2_j
#::::::::::::::::::::::::::::::::::::::::::::::

require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(i in 1:length(traits)){

trait=traits[i]

file=paste0("gwas-norm-337k-out/",trait,".out")

dat=vroom(file, col_names=T, delim=";")
dat=dat[complete.cases(dat),]
mod=lm(chisq ~ aver2,data=dat)

slope0=data.frame(trait=trait,
                  slope_aver2=coef(mod)[2],
                  p_aver2=summary(mod)$coefficients[,4][2],
                  stringsAsFactors = F)

if(i==1){slope=slope0} else {slope=rbind(slope,slope0)}

}

#::::::::::::::::::::::::::::::::::::::::::::::
# plot chisq ~ aver2_j  
#::::::::::::::::::::::::::::::::::::::::::::::
# make a plot ------------------------------------------------------------------
require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")
        
png(paste0("fig/chisq-by-aver2-bin-ukbb-recommend-337k.png"),
      width = 50, height = 30, units = "cm", res=600)
  par(mfrow=c(3,5))

for (i in 1:length(traits)){
    trait=traits[i]
    file=paste0("gwas-norm-337k-out/",trait,".out")
    dat=vroom(file, col_names=T, delim=";")
    # bin a variable by quantile
    cutoff=quantile(dat$aver2, probs = seq(0, 1, 0.005), na.rm=T)
    dat$bin=cut(dat$aver2, breaks=cutoff, labels=1:(length(cutoff)-1))
    # average chisq by bin values
    out=data.frame(chisq_ave=tapply(dat$chisq,INDEX=dat$bin, mean))
    out$bin_val=tapply(dat$aver2,INDEX=dat$bin, mean)

plot(out$bin_val, out$chisq_ave,
         xlab="ave r2", ylab="ave chisq",
         main=trait, las=1,
         cex = 1.5, pch=21,  bg="orange", col="white", lwd=0.5)
 }
dev.off()
```


## HE

### making grms


```bash

# making grm -------------------------------------------------------------------

#:::
# under gcta
#:::

# all snps
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 60G
#SBATCH -c 10
#SBATCH -t 08:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/gcta-all-norm \
          --bfile ../gen/geno-norm-100k \
          --extract ../gen/snps-norm-100k.use \
          --power -1 \
          --ignore-weights YES \
          --single YES
" > sh_script/grm-all-snps-norm

sbatch -A snpher ../sh_script/grm-all-snps-norm > ../job-records/grm-all-snps-norm

# grm by snp blocks: right vs. left
awk '$1<8 {print $2}' geno-norm-100k.bim > left-snps-norm-100k.use 
awk '$1>=8 {print $2}' geno-norm-100k.bim > right-snps-norm-100k.use

for i in left right; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 60G
#SBATCH -c 10
#SBATCH -t 20:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/gcta-$i-norm \
          --bfile ../gen/geno-norm-100k \
          --extract ../gen/$i-snps-norm-100k.use \
          --power -1 \
          --ignore-weights YES \
          --single YES
" > sh_script/grm-gcta-$i-norm
done

for i in left right; do
sbatch -A snpher ../sh_script/grm-gcta-$i-norm 
done > ../job-records/grm-gcta-by-snps-norm 

#:::
# under ldak-thin
#:::

#-----------
# 1. thin snps
#-----------

for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 10G
#SBATCH -c 4
#SBATCH -t 4:0:0
#SBATCH --constraint \"s04|s05\"
./ldak5.1 --max-threads 4 \
          --window-prune 0.98 \
          --window-kb 100 \
          --extract ../gen/snps-norm-100k.use \
          --bfile ../gen/geno-norm-100k \
          --thin ../ldak-thin/chr$j-norm \
          --chr $j
" > sh_script/ldak-thin$j-norm
done

# submit script
for j in {1..22}; do
sbatch -A snpher ../sh_script/ldak-thin$j-norm
done > ../job-records/ldak-thin-norm

# check job completion---
file=job-records/ldak-thin-norm
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# combine snp list
cat ldak-thin/chr{1..22}-norm.in > ldak-thin/ldak-thin-norm.in

#----------------------
# 2. kinship matrix under ldak-thin
#------------------------

echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 60G
#SBATCH -c 10
#SBATCH -t 08:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/ldak-thin-all-norm \
          --bfile ../gen/geno-norm-100k \
          --extract ../ldak-thin/ldak-thin-norm.in \
          --power -0.25 \
          --ignore-weights YES \
          --single YES
" > sh_script/ldak-thin-grm-all-snps-norm

sbatch -A snpher ../sh_script/ldak-thin-grm-all-snps-norm > ../job-records/ldak-thin-grm-all-snps-norm

# grm by snp blocks: right vs. left

awk '{split($1, a, /[:]/); if (a[1]<8) print $1}' \
 ldak-thin/ldak-thin-norm.in > gen/left-ldak-thin-norm.snps 
awk '{split($1, a, /[:]/); if (a[1]>=8) print $1}' \
 ldak-thin/ldak-thin-norm.in > gen/right-ldak-thin-norm.snps 

for i in left right; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 60G
#SBATCH -c 10
#SBATCH -t 08:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/ldak-thin-$i-norm \
          --bfile ../gen/geno-norm-100k \
          --extract ../gen/$i-ldak-thin-norm.snps \
          --power -0.25 \
          --ignore-weights YES \
          --single YES
" > sh_script/ldak-thin-grm-$i-norm
done

for i in left right; do
sbatch -A snpher ../sh_script/ldak-thin-grm-$i-norm 
done > ../job-records/grm-ldak-thin-by-snps-norm 

```

### estimation

NEED To check what covariates to adjust for HE
basic.covariates.use or covariates.use?


```bash
# regress grm on covariates-----------------------------------------------------

for grm in gcta-all-norm gcta-left-norm gcta-right-norm ldak-thin-all-norm ldak-thin-left-norm ldak-thin-right-norm; do  
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 50G
#SBATCH -c 5
#SBATCH -t 10:0:0
./ldak5.1 --adjust-grm ../kinship/$grm.covar \
          --grm ../kinship/$grm \
          --covar ../phen/covariates.use \
          --max-threads 5
"> sh_script/$grm-adjust.sh
done

# submit jobs
for grm in gcta-all-norm gcta-left-norm gcta-right-norm ldak-thin-all-norm ldak-thin-left-norm ldak-thin-right-norm; do
sbatch -A snpher ../sh_script/$grm-adjust.sh
done > ../job-records/grm-adjust-for-HE

# check job completion----------------------------------------------------------
file=job-records/grm-adjust-for-HE
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# HE under gcta-----------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 2G
#SBATCH -c 1
#SBATCH -t 00:30:0

./ldak5.1 --he ../he-norm/$i-he-gcta-$k \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --grm ../kinship/gcta-$k-norm.covar \
          --kinship-details NO \
          --check-root NO \
          --covar ../phen/covariates.use \
          --max-threads 1 \
          --memory-save YES
"> sh_script/$i-he-gcta-$k-snps-norm.sh
done
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
sbatch -A snpher ../sh_script/$i-he-gcta-$k-snps-norm.sh
done
done > ../job-records/he-gcta

# HE under ldak-thin------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 2G
#SBATCH -c 1
#SBATCH -t 0:30:0

./ldak5.1 --he ../he-norm/$i-he-ldak-thin-$k \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --grm ../kinship/ldak-thin-$k-norm.covar \
          --kinship-details NO \
          --check-root NO \
          --covar ../phen/covariates.use \
          --max-threads 1 \
          --memory-save YES
"> sh_script/$i-he-ldak-thin-$k-norm.sh
done
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
sbatch -A snpher ../sh_script/$i-he-ldak-thin-$k-norm.sh
done
done > ../job-records/he-ldak-thin

```

### summary


```bash

#::::::::::::
# under gcta
#::::::::::::

# extract h2 estimates ---------------------------------------------------------
# right
rm summary/he-gcta-norm.right 
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-norm/$i-he-gcta-right.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-gcta-norm.right
done

# left
rm summary/he-gcta-norm.left
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-norm/$i-he-gcta-left.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-gcta-norm.left
done

# all
rm summary/he-gcta-norm.all
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-norm/$i-he-gcta-all.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-gcta-norm.all
done

# inflation test----------------------------------------------------------------
R

full=read.table(paste0("summary/he-gcta-norm.all"), header=F)
left=read.table(paste0("summary/he-gcta-norm.left"), header=F)
right=read.table(paste0("summary/he-gcta-norm.right"), header=F)
names(full)=names(left)=names(right)=c("code","h2","se")
# some analyses did not complete --> match dataframes
dim(full);dim(left);dim(right)
common=intersect(full$code, left$code)
common=intersect(common, right$code)
m1=match(common, right$code)
m2=match(common, full$code)
m3=match(common, left$code)
right=right[m1,]
full=full[m2,]
left=left[m3,]

for(i in 1:dim(full)[1]){
    
    est1=left$h2[i]
    sd1=left$se[i]
    est2=right$h2[i]
    sd2=right$se[i]
    est=full$h2[i]
    sd=full$se[i]
    N=100000
    d1=rnorm(N,est1,sd1)
    d2=rnorm(N,est2,sd2)
    d=rnorm(N,est,sd)
    p=1-mean(d1+d2-d>=0)
    
    out0=data.frame(code=full$code[i],
                    right_est=est2,
                    right_sd=sd2,
                    left_est=est1,
                    left_sd=sd1,
                    all_est=est,
                    all_sd=sd,
                    p_inflation=p)
   if(i==1){out=out0}else{out=rbind(out,out0)}
}
write.table(out, paste0("summary/he-gcta-inflation-norm.txt"),
            col.names=T, row.names=F, quote=F)


#::::::::::::
# under ldak-thin
#::::::::::::

# extract h2 estimates ---------------------------------------------------------
# right
rm summary/he-ldak-thin-norm.right 
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-norm/$i-he-ldak-thin-right.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-ldak-thin-norm.right
done

# left
rm summary/he-ldak-thin-norm.left
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-norm/$i-he-ldak-thin-left.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-ldak-thin-norm.left
done

# all
rm summary/he-ldak-thin-norm.all
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-norm/$i-he-gcta-all.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-ldak-thin-norm.all
done

# inflation test----------------------------------------------------------------
R

full=read.table(paste0("summary/he-ldak-thin-norm.all"), header=F)
left=read.table(paste0("summary/he-ldak-thin-norm.left"), header=F)
right=read.table(paste0("summary/he-ldak-thin-norm.right"), header=F)
names(full)=names(left)=names(right)=c("code","h2","se")
# some analyses did not complete --> match dataframes
dim(full);dim(left);dim(right)
common=intersect(full$code, left$code)
common=intersect(common, right$code)
m1=match(common, right$code)
m2=match(common, full$code)
m3=match(common, left$code)
right=right[m1,]
full=full[m2,]
left=left[m3,]

for(i in 1:dim(full)[1]){
    
    est1=left$h2[i]
    sd1=left$se[i]
    est2=right$h2[i]
    sd2=right$se[i]
    est=full$h2[i]
    sd=full$se[i]
    N=100000
    d1=rnorm(N,est1,sd1)
    d2=rnorm(N,est2,sd2)
    d=rnorm(N,est,sd)
    p=1-mean(d1+d2-d>=0)
    
    out0=data.frame(code=full$code[i],
                    right_est=est2,
                    right_sd=sd2,
                    left_est=est1,
                    left_sd=sd1,
                    all_est=est,
                    all_sd=sd,
                    p_inflation=p)
   if(i==1){out=out0}else{out=rbind(out,out0)}
}
write.table(out, paste0("summary/he-ldak-thin-inflation-norm.txt"),
            col.names=T, row.names=F, quote=F)

```




<!--chapter:end:04.3-ukbb-recommended.Rmd-->


# control GWAS {#control}

Here we perform GWASs that serve as the control group for the good versus bad GWASs comparison. For the bad GWASs (section \@ref(bad)), we have a total of 100k individuals, which consist of 93,528 unrelated white British (from the good GWASs) and 6,472 blacks and Asians. We observed inflation of the test statistics. Although unlikely, it is still possible that some of the inflation is due to random errors. To rule out this possibility [or to ascertain that the observed inflation is due to population stratification not random errors], we used the same 93,528 unrelated white British but replaced the 6,472 blacks and Asians with unrelated white British that were not included in the good GWASs.

## ID list

We used previously obtained id lists (see section \@ref(goodid) for the good GWAS id list and section \@ref(badid) for the bad GWAS id list ) to derive the id list for the control GWASs. These are:

  * `overlap-complete-cases.id`: N = 147,008. Unrelated white British who have no missing data for all 14 traits and covariates.
  * `rand.100000`: ID list for the good gwas. Randomly selected from `overlap-complete-cases.id`  
  * `white.rand.93528`: unrelated whites as a part of the bad gwas ID list. Randomly selected from `rand.100000`
  
Here are the steps:

1. identify IDs from `overlap-complete-cases.id` not included in `rand.100000`.
2. randomly select 6,472 from the identified IDs.
3. combine `white.rand.93528` with the randomly selected 6,472 IDs.  


```r
options(scipen = 100)
id1=read.table("unrelated/overlap-complete-cases.id", header=F)
id2=read.table("unrelated/rand.100000", header=F)
id3=read.table("white.rand.93528", header=F)

# pool for selection
common=intersect(id1$V1, id2$V1)
pool=id1[!id1$V1%in%common,]

# randomly select 6,472 from the pool
sel=pool[sample(dim(pool)[1], 6472, replace=F),]

#  combine with 'white.rand.93528'
write.table(rbind(sel,id3), 'control-gwas.id', quote=F, row.names=F, col.names=F)
```

## QC of hp3 SNPs


```bash

# QC SNPs-----------------------------------------------------------------------
# stating number of SNPs= 1,184,423 
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 1:0:0

./plink2 --pfile ../gen/geno_plink/bhr$j \
         --keep ../control-gwas.id \
         --extract ../doug/ukbb.ldsc \
         --hwe 0.0001 \
         --hard-call-threshold .05 \
         --mach-r2-filter 0.8 2 \
         --make-bed \
         --memory 20000 \
         --out ../gen/tmp/bhr$j \
         --threads 3
"> sh_script/chr$j.sh
done

# submit jobs
for j in {1..22}; do
sbatch -A snpher ../sh_script/chr$j.sh
done > ../job-records/qc-control-gwas

file=job-records/qc-control-gwas
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# merge files
rm bfile-control-gwas.list
for j in {1..22}; do
echo  "../gen/tmp/bhr$j" >>bfile-control-gwas.list
done

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 40:0:0
./ldak5.1 --make-bed ../gen/geno-control \
          --mbfile ../gen/bfile-control-gwas.list \
          --max-threads 3 \
          --exclude-dups YES  
"> sh_script/mbfile-control.sh

# submit the script
sbatch -A snpher ../sh_script/mbfile-control.sh >../job-records/mbfiles-control-gwas

# MAF & call-rate 
awk < geno-control.stats '($5>.01 && $6>=0.95 && NR>1){print $1}' > snps-control-gwas.use
# m = 1,103,182 SNPs

```

## GWAS


```bash

# linear regression-------------------------------------------------------------
mkdir gwas-control

dirout=gwas-control
filein=geno-control
id=control-gwas.id
snp=snps-control-gwas.use
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 10G
#SBATCH -c 2
#SBATCH -t 10:0:0

./ldak5.1 --linear ../$dirout/$i-linear \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile ../gen/$filein \
          --keep ../$id \
          --extract ../gen/$snp \
          --covar ../phen/covariates.use \
          --max-threads 2 \
"> sh_script/$i-linear.sh
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-linear.sh
done>../job-records/gwas-control

# check job completion----------------------------------------------------------
file=job-records/gwas-control
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

```

## ldsc intercept

### under gcta


```bash
# UP TO HERE

#:::::::::::::
# using original ldsc
#:::::::::::::

# format stats for ldsc--------------------------------------------------------- 
library(vroom)
options(scipen = 100)
rs=vroom("doug/ukbb.ldsc", col_names=F)
phen=c("awake","bmi","chron","ever","fvc",
       "height","imp","neur","pulse","quals",
       "reaction","sbp","snoring","hyper", "quals")

for(i in phen){
  stat=vroom(paste0("gwas-control/",i,"-linear.summaries"), col_names=T)
  p=vroom(paste0("gwas-control/",i,"-linear.pvalues"), col_names=T)
  m1=match(rs$X1, stat$Predictor)
  m2=match(rs$X1, p$Predictor)
  out=data.frame(SNP=rs$X2,
                 N=stat$n[m1],
                 Z=(sqrt(stat$Stat)*stat$Direction)[m1],
                 A1=stat$A1[m1],
                 A2=stat$A2[m1], 
                 pval=p$P[m2], stringsAsFactors=F)
  out=out[complete.cases(out),]
  write.table(out, paste0("gwas-control/",i,"-linear-rs.summaries"), 
  col.names=T, row.names=F, quote=F)
}

# perform ldsc------------------------------------------------------------------
mkdir out-control
for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 2G
#SBATCH -c 3
#SBATCH -t 24:00:0

.././munge_sumstats.py \
--sumstats ../../gwas-control/$i-linear-rs.summaries \
--out ../out-control/$i \
--merge-alleles ../w_hm3.snplist

.././ldsc.py \
--h2 ../out-control/$i.sumstats.gz \
--ref-ld-chr ../eur_w_ld_chr/ \
--w-ld-chr ../eur_w_ld_chr/ \
--out ../out-control/$i-ldsc
">sh_script/ldsc-$i-control.sh
done

# submit jobs-------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,reaction,sbp,snoring,hyper,quals}; do
sbatch -A snpher ../sh_script/ldsc-$i-control.sh
done>../../job-records/ldsc-control

#:::::::::::::
# using sumher
#:::::::::::::

# insert genetic distance into bim file-----------------------------------------
head geno-control.fam > small-control

for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --mem 8G
#SBATCH -c 1
#SBATCH -t 3:00:0
#SBATCH --constraint \"s04|s05\"
./plink1.9 --bfile ../gen/geno-control \
          --chr $j \
          --cm-map /home/zhoux/snpher/faststorage/genetic_maps/genetic_map_chr@_combined_b37.txt \
          --make-bed \
          --out new$j \
          --keep ../gen/small-control
" > sh_script/map$j
done

for j in {1..22}; do
sbatch -A snpher ../sh_script/map$j
done > genetic-distance-hapmap3

cd /home/zhoux/dsmwpred/xuan/quality-control/qc-10oct/gen
cat new{1..22}.bim | awk '{print $2, $3}' > maps-hapmap3-control.txt
rm new{1..22}.{bim,bed,fam,log}

awk '(NR==FNR){arr[$1]=$2;next}{print $1, $2, arr[$2], $4, $5, $6}' > geno-control.bim2 maps-hapmap3-control.txt geno-control.bim
mv geno-control.bim geno-control.bim0
mv geno-control.bim2 geno-control.bim

# compute tagging under gcta----------------------------------------------------
mkdir tagging-control
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 1
#SBATCH -t 5:0:0

./ldak5.1 --calc-tagging ../tagging-control/gcta-hapmap3-chr-$j \
          --bfile ../gen/geno-control \
          --ignore-weights YES \
          --power -1 \
          --window-cm 1 \
          --chr $j
"> sh_script/tagging-gcta-hapmap3-chr$j
done

for j in {1..22}; do
sbatch -A snpher ../sh_script/tagging-gcta-hapmap3-chr$j 
done > ../job-records/tagging-gcta-hapmap3-control

# check job completion----------------------------------------------------------
file=job-records/tagging-gcta-hapmap3-control
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# merge tagging files-----------------------------------------------------------
rm list.txt
for j in {1..22}; do 
echo "tagging-control/gcta-hapmap3-chr-$j.tagging" >> list.txt
done
./ldak5.1 --join-tagging tagging-control/gcta-hapmap3 --taglist list.txt

# ldsc intercept----------------------------------------------------------------
mkdir sumher-control
dirin1=tagging-control
dirin2=gwas-control
dirout=sumher-control
for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 1
#SBATCH -t 5:0:0

./ldak5.1 --sum-hers ../$dirout/$i-sumher-gcta \
          --tagfile ../$dirin1/gcta-hapmap3.tagging \
          --summary ../$dirin2/$i-linear.summaries \
          --check-sums NO \
          --intercept YES
"> sh_script/$i-sumher-gcta
done

for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-sumher-gcta
done>../job-records/sumher-gcta-control

```

### under ldak-thin


```bash

# get weights-------------------------------------------------------------------
awk < ldak-thin/ldak-thin-control.in '{print $1, 1}' > ldak-thin/weights.ldak-thin-control

# calculate tagging under ldak-thin---------------------------------------------
dirout=tagging-control
filein1=geno-control
filein2=weights.ldak-thin-control
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 40G
#SBATCH -c 5
#SBATCH -t 10:0:0
#SBATCH --constraint \"s04|s05\"
./ldak5.1 --calc-tagging ../$dirout/ldak-thin-hapmap3-chr-$j \
          --bfile ../gen/$filein1 \
          --weights ../ldak-thin/$filein2 \
          --power -.25 \
          --window-cm 1 \
          --chr $j \
          --save-matrix YES \
          --max-threads 5
" > sh_script/tagging-ldak-thin-hapmap3-chr$j.sh
done

# submit scripts
for j in {1..22}; do
sbatch -A snpher ../sh_script/tagging-ldak-thin-hapmap3-chr$j.sh
done > ../job-records/tagging-ldak-thin-control

# check job completion----------------------------------------------------------
file=job-records/tagging-ldak-thin-control
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# merge tagging files-----------------------------------------------------------
rm list.txt
rm matlist.txt
for j in {1..22}; do
echo "tagging-control/ldak-thin-hapmap3-chr-$j.tagging" >> list.txt
echo "tagging-control/ldak-thin-hapmap3-chr-$j.matrix" >> matlist.txt
done
dirout=tagging-control
./ldak5.1 --join-tagging $dirout/ldak-thin-hapmap3 --taglist list.txt --matlist matlist.txt

# ldsc intercept----------------------------------------------------------------
dirin1=tagging-control
dirin2=gwas-control
dirout=sumher-control
for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 1
#SBATCH -t 00:30:0

./ldak5.1 --sum-hers ../$dirout/$i-sumher-ldak-thin \
          --tagfile ../$dirin1/ldak-thin-hapmap3.tagging \
          --summary ../$dirin2/$i-linear.summaries \
          --check-sums NO \
          --intercept YES
"> sh_script/$i-sumher-ldak-thin
done

for i in {awake,bmi,chron,ever,fvc,height,quals,imp,neur,pulse,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-sumher-ldak-thin
done>../job-records/sumher-ldak-thin-control

```

### summary


```bash

nm=control

# original ldsc
cd ldsc/out-control
grep Intercept *ldsc.log | awk '{split($1, a, /[-]/); split($3, b, /[()]/); print a[1], $2, b[2]}' > ../../summary/ldsc-$nm

# sumher under gcta
cd sumher-control
grep Intercept_Estimate *gcta.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-gcta-$nm-est
grep Intercept_SD *gcta.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-gcta-$nm-sd
cd ../summary/
paste sumher-gcta-$nm-est sumher-gcta-$nm-sd | awk '{print $1, $2, $4}' > sumher-gcta-$nm
      
# suhmer under ldak-thin
grep Intercept_Estimate *ldak-thin.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-ldak-thin-$nm-est
grep Intercept_SD *ldak-thin.extra | awk '{ split($1, a, /[-]/); print a[1], $2}' >../summary/sumher-ldak-thin-$nm-sd
cd ../summary/
paste sumher-ldak-thin-$nm-est sumher-ldak-thin-$nm-sd | awk '{print $1, $2, $4}' > sumher-ldak-thin-$nm

```

## HE

### making grms


```bash

# making grm -------------------------------------------------------------------

#:::
# under gcta
#:::
fileout=gcta-all-control
filein=geno-control
snp=snps-control-gwas.use

# all snps
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 60G
#SBATCH -c 10
#SBATCH -t 08:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/$fileout \
          --bfile ../gen/$filein \
          --extract ../gen/$snp \
          --power -1 \
          --ignore-weights YES \
          --single YES
" > sh_script/grm-all-snps

sbatch -A snpher ../sh_script/grm-all-snps > ../job-records/grm-all-snps-control

# grm by snp blocks: right vs. left
awk '$1<8 {print $2}' geno-control.bim > left-snps-control.use 
awk '$1>=8 {print $2}' geno-control.bim > right-snps-control.use

filein=geno-control
for i in left right; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 60G
#SBATCH -c 10
#SBATCH -t 20:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/gcta-$i-control \
          --bfile ../gen/$filein \
          --extract ../gen/$i-snps-control.use \
          --power -1 \
          --ignore-weights YES \
          --single YES
" > sh_script/grm-gcta-$i
done

for i in left right; do
sbatch -A snpher ../sh_script/grm-gcta-$i 
done > ../job-records/grm-gcta-by-snps-control

#:::
# under ldak-thin
#:::

#-----------
# 1. thin snps
#-----------
snp=snps-control-gwas.use
filein=geno-control
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 10G
#SBATCH -c 4
#SBATCH -t 4:0:0
#SBATCH --constraint \"s04|s05\"
./ldak5.1 --max-threads 4 \
          --window-prune 0.98 \
          --window-kb 100 \
          --extract ../gen/$snp \
          --bfile ../gen/$filein \
          --thin ../ldak-thin/chr$j-control \
          --chr $j
" > sh_script/ldak-thin$j
done

# submit script
for j in {1..22}; do
sbatch -A snpher ../sh_script/ldak-thin$j
done > ../job-records/ldak-thin-control

# check job completion---
file=job-records/ldak-thin-control
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# combine snp list
cat ldak-thin/chr{1..22}-control.in > ldak-thin/ldak-thin-control.in

#----------------------
# 2. kinship matrix under ldak-thin
#-----------------------

fileout=ldak-thin-all-control
filein=geno-control
snp=ldak-thin-control.in

echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 60G
#SBATCH -c 10
#SBATCH -t 08:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/$fileout \
          --bfile ../gen/$filein \
          --extract ../ldak-thin/$snp \
          --power -0.25 \
          --ignore-weights YES \
          --single YES
" > sh_script/ldak-thin-grm-all-snps

sbatch -A snpher ../sh_script/ldak-thin-grm-all-snps > ../job-records/ldak-thin-grm-all-snps-control

# grm by snp blocks: right vs. left

awk '{split($1, a, /[:]/); if (a[1]<8) print $1}' \
 ldak-thin/ldak-thin-control.in > gen/left-ldak-thin-control.snps 
awk '{split($1, a, /[:]/); if (a[1]>=8) print $1}' \
 ldak-thin/ldak-thin-control.in > gen/right-ldak-thin-control.snps 

for i in left right; do
echo "#"'!'"/bin/bash
#SBATCH --partition normal
#SBATCH --mem 60G
#SBATCH -c 10
#SBATCH -t 08:0:0
#SBATCH --constraint \"s04|s05\"

./ldak5.1 --max-threads 10 \
          --calc-kins-direct ../kinship/ldak-thin-$i-control \
          --bfile ../gen/geno-control \
          --extract ../gen/$i-ldak-thin-control.snps \
          --power -0.25 \
          --ignore-weights YES \
          --single YES
" > sh_script/ldak-thin-grm-$i
done

for i in left right; do
sbatch -A snpher ../sh_script/ldak-thin-grm-$i 
done > ../job-records/grm-ldak-thin-by-snps-control

```

### estimation

NEED To check what covariates to adjust for HE
basic.covariates.use or covariates.use?


```bash
# regress grm on covariates-----------------------------------------------------

for grm in gcta-all-control gcta-left-control gcta-right-control ldak-thin-all-control ldak-thin-left-control ldak-thin-right-control; do  
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 50G
#SBATCH -c 5
#SBATCH -t 10:0:0
./ldak5.1 --adjust-grm ../kinship/$grm.covar \
          --grm ../kinship/$grm \
          --covar ../phen/covariates.use \
          --max-threads 5
"> sh_script/$grm-adjust.sh
done

# submit jobs
for grm in gcta-all-control gcta-left-control gcta-right-control ldak-thin-all-control ldak-thin-left-control ldak-thin-right-control; do
sbatch -A snpher ../sh_script/$grm-adjust.sh
done > ../job-records/grm-adjust-for-HE-control

# check job completion----------------------------------------------------------
file=job-records/grm-adjust-for-HE-control
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# HE under gcta-----------------------------------------------------------------

mkdir he-control
dirout=he-control
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 2G
#SBATCH -c 1
#SBATCH -t 00:30:0

./ldak5.1 --he ../$dirout/$i-he-gcta-$k \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --grm ../kinship/gcta-$k-control.covar \
          --kinship-details NO \
          --check-root NO \
          --covar ../phen/covariates.use \
          --max-threads 1 \
          --memory-save YES
"> sh_script/$i-he-gcta-$k-snps-control.sh
done
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
sbatch -A snpher ../sh_script/$i-he-gcta-$k-snps-control.sh
done
done > ../job-records/he-gcta-control

# HE under ldak-thin------------------------------------------------------------
dirout=he-control
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 2G
#SBATCH -c 1
#SBATCH -t 0:30:0

./ldak5.1 --he ../$dirout/$i-he-ldak-thin-$k \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --grm ../kinship/ldak-thin-$k-control.covar \
          --kinship-details NO \
          --check-root NO \
          --covar ../phen/covariates.use \
          --max-threads 1 \
          --memory-save YES
"> sh_script/$i-he-ldak-thin-$k-control.sh
done
done

# submit files------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for k in all left right; do
sbatch -A snpher ../sh_script/$i-he-ldak-thin-$k-control.sh
done
done > ../job-records/he-ldak-thin-control

```

### summary


```bash

#::::::::::::
# under gcta
#::::::::::::

nm=control
# extract h2 estimates ---------------------------------------------------------
# right
rm summary/he-gcta-$nm.right 
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-$nm/$i-he-gcta-right.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-gcta-$nm.right
done

# left
rm summary/he-gcta-$nm.left
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-$nm/$i-he-gcta-left.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-gcta-$nm.left
done

# all
rm summary/he-gcta-$nm.all
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-$nm/$i-he-gcta-all.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-gcta-$nm.all
done

# inflation test----------------------------------------------------------------
R

full=read.table(paste0("summary/he-gcta-control.all"), header=F)
left=read.table(paste0("summary/he-gcta-control.left"), header=F)
right=read.table(paste0("summary/he-gcta-control.right"), header=F)
names(full)=names(left)=names(right)=c("code","h2","se")
# some analyses did not complete --> match dataframes
dim(full);dim(left);dim(right)
common=intersect(full$code, left$code)
common=intersect(common, right$code)
m1=match(common, right$code)
m2=match(common, full$code)
m3=match(common, left$code)
right=right[m1,]
full=full[m2,]
left=left[m3,]

for(i in 1:dim(full)[1]){
    
    est1=left$h2[i]
    sd1=left$se[i]
    est2=right$h2[i]
    sd2=right$se[i]
    est=full$h2[i]
    sd=full$se[i]
    N=100000
    d1=rnorm(N,est1,sd1)
    d2=rnorm(N,est2,sd2)
    d=rnorm(N,est,sd)
    p=1-mean(d1+d2-d>=0)
    
    out0=data.frame(code=full$code[i],
                    right_est=est2,
                    right_sd=sd2,
                    left_est=est1,
                    left_sd=sd1,
                    all_est=est,
                    all_sd=sd,
                    p_inflation=p)
   if(i==1){out=out0}else{out=rbind(out,out0)}
}
write.table(out, paste0("summary/he-gcta-inflation-control.txt"),
            col.names=T, row.names=F, quote=F)


#::::::::::::
# under ldak-thin
#::::::::::::

nm=control
# extract h2 estimates ---------------------------------------------------------
# right
rm summary/he-ldak-thin-$nm.right 
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-$nm/$i-he-ldak-thin-right.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-ldak-thin-$nm.right
done

# left
rm summary/he-ldak-thin-$nm.left
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-$nm/$i-he-ldak-thin-left.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-ldak-thin-$nm.left
done

# all
rm summary/he-ldak-thin-$nm.all
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
outfile=he-$nm/$i-he-gcta-all.he
awk -v i=$i '$1=="Her_All" {print i, $2, $3}' $outfile >> summary/he-ldak-thin-$nm.all
done

# inflation test----------------------------------------------------------------
R

full=read.table(paste0("summary/he-ldak-thin-control.all"), header=F)
left=read.table(paste0("summary/he-ldak-thin-control.left"), header=F)
right=read.table(paste0("summary/he-ldak-thin-control.right"), header=F)
names(full)=names(left)=names(right)=c("code","h2","se")
# some analyses did not complete --> match dataframes
dim(full);dim(left);dim(right)
common=intersect(full$code, left$code)
common=intersect(common, right$code)
m1=match(common, right$code)
m2=match(common, full$code)
m3=match(common, left$code)
right=right[m1,]
full=full[m2,]
left=left[m3,]

for(i in 1:dim(full)[1]){
    
    est1=left$h2[i]
    sd1=left$se[i]
    est2=right$h2[i]
    sd2=right$se[i]
    est=full$h2[i]
    sd=full$se[i]
    N=100000
    d1=rnorm(N,est1,sd1)
    d2=rnorm(N,est2,sd2)
    d=rnorm(N,est,sd)
    p=1-mean(d1+d2-d>=0)
    
    out0=data.frame(code=full$code[i],
                    right_est=est2,
                    right_sd=sd2,
                    left_est=est1,
                    left_sd=sd1,
                    all_est=est,
                    all_sd=sd,
                    p_inflation=p)
   if(i==1){out=out0}else{out=rbind(out,out0)}
}
write.table(out, paste0("summary/he-ldak-thin-inflation-control.txt"),
            col.names=T, row.names=F, quote=F)

```





<!--chapter:end:04.4-control-gwas.Rmd-->


# inclusive GWAS

Here we conduct a GWAS that mimic the QC steps taken by some of the most inclusive GWAS studies. For example, the GWAS conducted by Codd et al. (2021) on telomere length has the following QC step, which resulted in n = 472,174 and m = 19.4 million SNPs

> We used imputed genotypes available in the UKB2 for the GWAS. To ensure quality, we restricted the analysis to variants with a MAF of ≥0.1% (where imputation accuracy is greatest) and an INFO score of ≥0.3. We tested 19.4 million variants using the BOLT-LMM package, adjusting for age, sex, array and the first ten principal components (PCs). The analysis was run separately for chromosome 23, where males were coded as 0/2.

Here we want to test if the test statistics from GWAS conducted in such a way are inflated using the proposed method. We do not have access to telomere length, but we can use the 14 traits as examples instead.

## QC


```bash

#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# extract covariates
#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# This has been done under 'Good GWAS' section. Here we just want to extract from the file
R
options(scipen=999)
require(vroom)
dat=vroom("phen/covariates.phen", col_names=T)
pc=paste0("pc",1:10)
cov=c("eid","eid","age","geno_batch",pc)
out=dat[,cov]

write.table(out,"phen/covariates-inclusive-gwas.use", col.names=F,
            row.names=F, quote=F)
write.table(cov,"phen/covariates-inclusive-gwas.use-names", col.names=F,
            row.names=F, quote=F)

sel=out[complete.cases(out),] # n = 488,244
write.table(sel[,1:2], "complete-cases-covariates-inclusive-gwas.id", col.names=F,
            row.names=F, quote=F)

#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# id list of complete cases: covariates & phenotypes
#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# note: to remain as much people as possible, we did not select all 14 traits. but 9.
# for these traits, n = 467, 104 have complete data
cp complete-cases-covariates-inclusive-gwas.id inclusive-gwas.id
dir=phen/continuous-traits/
# {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}
for tt in {awake,bmi,ever,height,imp,quals,reaction,imp,hyper}; do
mv inclusive-gwas.id temp
awk '(NR==FNR){a[$1];next}($1 in a){print $1, $2}' temp $dir/$tt.raw.pheno > inclusive-gwas.id
wc -l inclusive-gwas.id
echo $tt
done
rm temp

#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# extract genotypes
#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# QC SNPs-----------------------------------------------------------------------
# stating number of SNPs=
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 10:0:0

./plink2 --pfile ../gen/geno_plink/bhr$j \
         --keep ../inclusive-gwas.id \
         --hwe 0.0001 \
         --maf 0.001 \
         --hard-call-threshold .05 \
         --mach-r2-filter 0.3 2 \
         --make-bed \
         --memory 20000 \
         --out ../gen/tmp/bhr$j \
         --threads 3
"> sh_script/chr$j.sh
done

# submit jobs-------------------------------------------------------------------
for j in {1..22}; do
sbatch -A snpher ../sh_script/chr$j.sh
done > ../job-records/qc-inclusive-gwas

# check job completion----------------------------------------------------------
file=job-records/qc-inclusive-gwas
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# merge files-------------------------------------------------------------------
rm gen/bfile-inclusive-gwas.list
for j in {1..22}; do
echo  "../gen/tmp/bhr$j" >> gen/bfile-inclusive-gwas.list
done

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 80G
#SBATCH -c 10
#SBATCH -t 10:0:0
./ldak5.1 --make-bed ../gen/geno-inclusive-gwas \
          --mbfile ../gen/bfile-inclusive-gwas.list \
          --max-threads 10 \
          --exclude-dups YES
"> sh_script/mbfile-inclusive-gwas.sh

# submit the script-------------------------------------------------------------
sbatch -A snpher ../sh_script/mbfile-inclusive-gwas.sh >../job-records/mbfile-inclusive-gwas

# compute info score------------------------------------------------------------
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 5:0:0

./plink2 --pfile ../gen/geno_plink/bhr$j \
         --threads 3 \
         --keep ../inclusive-gwas.id \
         --memory 20000 \
         --freq cols=chrom,ref,alt,altfreq,machr2 \
         --out ../gen/info/chr$j
"> sh_script/info-chr$j.sh
done

# submit file-------------------------------------------------------------------
for j in {1..22}; do
sbatch -A snpher ../sh_script/info-chr$j.sh
done > ../job-records/compute-info-score-inclusive-gwas

# combine files ----------------------------------------------------------------
dir=gen/info
rm $dir/inclusive-gwas-afreq
for i in {1..22}; do
if [ $i == 1 ]; then
  awk '{print $0}' $dir/chr$i.afreq > $dir/inclusive-gwas-afreq
else
  awk 'NR>1 {print $0}' $dir/chr$i.afreq >> $dir/inclusive-gwas-afreq
fi
done

#::::::::::::::::::
# SNP list
#::::::::::::::::::

# MAF & call-rate
awk < gen/bfile-inclusive-gwas.stat '($5>.001 && $6>=0.95 && NR>1){print $1}' > snps-inclusive-gwas.use
# m = SNPs
```

## gwas-telomere length

### aver2_j
Here we want to check if there is evidence of inflation in GWAS statistics due to confouding.
Here are the steps:

  1. download the GWAS stats (https://figshare.com/s/caa99dc0f76d62990195)
  2. randomly chose 10k SNPs from each side of the genome
  3. compute aver2_j
  4. check if chi square test stats vary with respect to aver2_j
  

```bash
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# select SNPs & organize data
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# download gwas stats-----------------------------------------------------------
mkdir gwas-telomere
wget https://figshare.com/ndownloader/files/28414941?private_link=caa99dc0f76d62990195
# m = 20,134,422 SNPs

# organize data: snp name & chisq ----------------------------------------------
infile=gwas-telomere/UKB_telomere_gwas_summarystats.tsv.gz
outfile=gwas-telomere/telemere-gwas-organized
zcat $infile | awk -F '\t' 'BEGIN {print "chr base_pair_location chr_base rs chisq"} 
                            NR >1 {print $3, $4,$3":"$4, $1, ($8/$9)^2}' > $outfile
                            
# hapmap3 SNPs------------------------------------------------------------------
infile1=doug/ukbb.ldsc
infile2=gwas-telomere/telemere-gwas-organized
outfile=gwas-telomere/telemere-gwas-organized-hm3
awk 'NR==FNR{a[$3];next} 
     ($3 in a || FNR==1 ){print $0}' $infile1 $infile2 > $outfile

# choose snp lists--------------------------------------------------------------
# choose 40k for each side as the pool for selection
m=40000
infile=gwas-telomere/telemere-gwas-organized-hm3
left=inflation/gwas-telomere/left-snps-pool
right=inflation/gwas-telomere/right-snps-pool
awk '$1<8 && NR > 1 {print $1, $2, $3}' $infile | shuf | head -n $m >$left
awk '$1>=8 && NR > 1 {print $1, $2, $3}' $infile | shuf | head -n $m >$right

# entire snp list of ukbb
out=inflation/gwas-telomere/ukbb-bhr-all-snps
rm $out
for i in {1..22};do
gen=gen/geno_plink/bhr$i.pvar
awk '{print $1, $2, $1":"$2, $3}' $gen >> $out
done
# wc -l gen/geno_plink/bhr{1..22}.pvar

# put the chosen 40k snps in the right format
snp=inflation/gwas-telomere/ukbb-bhr-all-snps
leftin=inflation/gwas-telomere/left-snps-pool
rightin=inflation/gwas-telomere/right-snps-pool
leftout=inflation/gwas-telomere/left-snps-tmp
rightout=inflation/gwas-telomere/right-snps-tmp

awk '(NR==FNR){a[$3];next}
     ($3 in a){print $4}' $leftin $snp > $leftout

awk '(NR==FNR){a[$3];next}
     ($3 in a){print $4}' $rightin $snp > $rightout
# note:  > 40k are in the list of each side because some snps have the same basepair...    

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  extract genotypes of the bfiles
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

left=inflation/gwas-telomere/left-snps-tmp
right=inflation/gwas-telomere/right-snps-tmp
out=inflation/gwas-telomere/snp-all
awk '{print $0}' $left $right > $out

# extract data -----------------------------------------------------------------
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 4G
#SBATCH -c 3
#SBATCH -t 00:30:0

./plink2 --pfile ../gen/geno_plink/bhr$j \
         --extract ../inflation/gwas-telomere/snp-all \
         --make-bed \
         --out ../gen/tmp/bhr$j \
         --memory 20000 \
         --threads 3
"> sh_script/chr$j.sh
done

# submit jobs-------------------------------------------------------------------
for j in {1..22}; do
sbatch -A snpher ../sh_script/chr$j.sh
done > ../job-records/bfile-telomere

# check job completion----------------------------------------------------------
file=job-records/bfile-telomere
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# merge files-------------------------------------------------------------------
rm gen/bfile.list
for j in {1..22}; do
echo  "../gen/tmp/bhr$j" >> gen/bfile.list
done

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 01:00:0
./ldak5.1 --make-bed ../gen/geno-telomere \
          --mbfile ../gen/bfile.list \
          --max-threads 3 \
          --exclude-dups YES
"> sh_script/mbfile-telomere.sh

# submit the script-------------------------------------------------------------
sbatch -A snpher ../sh_script/mbfile-telomere.sh >../job-records/mbfile-telomere

# compute info score------------------------------------------------------------
for j in {1..22}; do
snp=../inflation/gwas-telomere/snp-all
out=../gen/info/chr$j-telomere
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 5G
#SBATCH -c 3
#SBATCH -t 00:20:0

./plink2 --pfile ../gen/geno_plink/bhr$j \
         --threads 3 \
         --extract $snp \
         --memory 20000 \
         --freq cols=chrom,ref,alt,altfreq,machr2 \
         --out $out
"> sh_script/info-chr$j.sh
done

# submit file-------------------------------------------------------------------
for j in {1..22}; do
sbatch -A snpher ../sh_script/info-chr$j.sh
done > ../job-records/compute-info-telomere

# combine files ----------------------------------------------------------------
dir=gen/info
rm $dir/telomere-afreq
for i in {1..22}; do
if [ $i == 1 ]; then
  awk '{print $0}' $dir/chr$i-telomere.afreq > $dir/telomere-afreq
else
  awk 'NR>1 {print $0}' $dir/chr$i-telomere.afreq >> $dir/telomere-afreq
fi
done

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  Finalize the 10k SNP list of each side
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

m=10000
snp=gen/geno-telomere.bim
leftin=inflation/gwas-telomere/left-snps-tmp
rightin=inflation/gwas-telomere/right-snps-tmp
leftout=inflation/gwas-telomere/left-snps
rightout=inflation/gwas-telomere/right-snps

awk '(NR==FNR){a[$1];next}
     ($2 in a){print $2}' $leftin $snp | shuf | head -$m  > $leftout

awk '(NR==FNR){a[$1];next}
     ($2 in a){print $2}' $rightin $snp | shuf | head -$m  > $rightout

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  compute ave r^2_j
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

lista=../inflation/gwas-telomere/right-snps
listb=../inflation/gwas-telomere/left-snps
bfile=../gen/geno-telomere
out=../inflation/gwas-telomere/out/10k-snps

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 80G
#SBATCH -c 7
#SBATCH -t 00:30:0
./ldak5.2 --max-threads 7 \
          --calc-inflation $out \
          --bfile $bfile \
          --lista $lista \
          --listb $listb
">sh_script/calc-r-10k-snps

# submit the job
sbatch -A snpher ../sh_script/calc-r-10k-snps >../job-records/calc-r-telomere

#  compute ave r_j^2 for each i-------------------------------------------------
R
require(vroom)

nm="10k-snps"
dat=vroom(paste0("inflation/gwas-telomere/out/",nm,".pairwise"), col_names=F)
lista=read.table(paste0("inflation/gwas-telomere/out/",nm,".predictorsa"), 
                stringsAsFactors = F)
listb=read.table(paste0("inflation/gwas-telomere/out/",nm,".predictorsb"), 
                stringsAsFactors = F)

dat=dat[,-c(10001)]^2
outb=data.frame(predictor=listb$V1, ave_r2=apply(dat,2, mean))
outa=data.frame(predictor=lista$V1, ave_r2=apply(dat,1, mean))
out=rbind(outa,outb)

write.table(out,paste0("summary/ave-r2-",nm, "-ukbb-telomere"), col.names=F, row.names=F, quote=F)  

```

### organize data


```bash
# NEED TO CHECK THE FOLLOWING CODE
aver2=summary/ave-r2-10k-snps-ukbb-telomere
rs=doug/ukbb.ldsc
gwas=gwas-telomere/telomere-gwas-organized-hm3
out=gwas-telomere/telomere.out

awk 'NR==FNR {a[$1]; b[$1]=$2; next}
      ($1 in a) {print $1, b[$1], $2}' $rs $aver2 > tmp/aver2.tmp
      
awk 'BEGIN{print "snp rs chisq aver2"} 
     NR==FNR {a[$2]; b[$2]=$1; c[$2]=$3; next}
     ($4 in a) {print b[$4], $4, $5, c[$4] }' tmp/aver2.tmp $gwas  > $out  

```

### chisq ~ aver2_j

```r
require(vroom)
dat=vroom("gwas-telomere/telomere.out", col_names=T)

# estimate slope: chisq ~ aver2_j-----------------------------------------------
mod=lm(chisq ~ aver2,data=dat)
summary(mod)

# make the plot---------------------------------------------------------------
# bin a variable by quantile
cutoff=quantile(dat$aver2, probs = seq(0, 1, 0.005), na.rm=T)
dat$bin=cut(dat$aver2, breaks=cutoff, labels=1:(length(cutoff)-1))
# average chisq by bin values
out=data.frame(chisq_ave=tapply(dat$chisq,INDEX=dat$bin, mean))
out$bin_val=tapply(dat$aver2,INDEX=dat$bin, mean)

png("fig/chisq-by-aver2-bin-telomere.png",
    width = 10, height = 10, units = "cm", res=600)
start=0
end=max(out$chisq_ave)
plot(out$bin_val, out$chisq_ave,
         xlab="ave r2", ylab="ave chisq",
         ylim=c(start,end),
         main="", las=1,
         cex = 0.8, pch=21,  bg="orange", col="white", lwd=0.5)
dev.off()

# make a plot ------------------------------------------------------------------

require(vroom)
#cuts=c("cut.125","cut.25","cut.5")
cuts="cut.125"
related=c("rel","unrel")
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(j in 1:length(cuts)){
  
  cut=cuts[j]
  # plot chisq ~ aver2 by rel for a given cutoff for relatedness
  png(paste0("fig/chisq-by-aver2-bin-related-gwas-",cut,".png"),
      width = 50, height = 30, units = "cm", res=600)
  par(mfrow=c(3,5))
      for(i in 1:length(traits)){
        trait=traits[i]
        make_plot(trait, cut)
      }
  dev.off()
}
```


## notes/concerns

 * Telomere length GWAS:
    * The QC seems too rough so that some SNPs have multiple alleles.  


<!--chapter:end:04.5-inclusive-gwas.Rmd-->


# related individuals

## genotyped SNPs

### aver2_j

Previously we performed analyses on icd10 traits using related individuals. Here are relevant files:

  * **bfile**: gen/geno-rel: created using icd10/related.inds
  * **id lists**:
    * 56757 icd10/related.inds
    * 56754 relatedness/cut.05.related: relatedness >= 0.05.
    * 56753 relatedness/cut.125.related
    * 48197 relatedness/cut.25.related
    * 18294 relatedness/cut.5.related
  * **snp list** after QC: gen/snps-rel-inds.use : 627,320 SNPs 



```bash

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# create id lists & snp lists
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# unrelated ids----------------------------------------------------------------
# serve as control groups
n=(56753 48197 18294)
nm=(cut.125 cut.25 cut.5)

for i in {0..2}; do
shuf rand.100000 | head -n ${n[$i]} > white-unrel-rand-control-for-${nm[$i]}
done

# snp list----------------------------------------------------------------------
# common snps of related & unrelated
unrel=gen/snps-unrel-inds.use # 1,103,209
rel=gen/snps-rel-inds.use # 627,320 SNPs
awk 'NR==FNR{a[$1];next} 
    ($1 in a) {print $1}' $unrel $rel > gen/common-unrel-rel-snps # 153,313

# lista & listb
# this will be common for all id lists
m=10000
infile=gen/common-unrel-rel-snps
left=inflation/related/left-snps
right=inflation/related/right-snps

awk '{split($1, a, ":");
    if (a[1]<8) print $1 }' $infile | shuf | head -n $m >$left  
awk '{split($1, a, ":");
    if (a[1]>=8) print $1 }' $infile | shuf | head -n $m >$right

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  compute ave r^2_j
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

for nm in {cut.125,cut.25,cut.5}; do

# common snp lists
lista=../inflation/related/right-snps
listb=../inflation/related/left-snps

# related individuals
id=../relatedness/$nm.related
bfile=../gen/geno-rel
out=../inflation/related/out/10k-snps-$nm-rel

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 2
#SBATCH -t 00:10:0
./ldak5.2 --max-threads 2 \
          --calc-inflation $out \
          --bfile $bfile \
          --keep $id \
          --lista $lista \
          --listb $listb
">sh_script/calc-r-10k-snps-$nm-rel

# unrelated controls
id=../white-unrel-rand-control-for-$nm
bfile=../gen/geno-unrel
out=../inflation/related/out/10k-snps-$nm-unrel

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 2
#SBATCH -t 00:10:0
./ldak5.2 --max-threads 2 \
          --calc-inflation $out \
          --bfile $bfile \
          --keep $id \
          --lista $lista \
          --listb $listb
">sh_script/calc-r-10k-snps-$nm-unrel
done

# submit the job
for nm in {cut.125,cut.25,cut.5}; do
sbatch -A snpher ../sh_script/calc-r-10k-snps-$nm-rel
sbatch -A snpher ../sh_script/calc-r-10k-snps-$nm-unrel
done>../job-records/calc-r-related

#  compute ave r_j^2 for each i-------------------------------------------------
R
require(vroom)
cut=c("cut.125","cut.25","cut.5")
rel=c("rel","unrel")

for(j in 1:length(rel)){
for(i in 1:length(cut)){
    nm=paste0("10k-snps-",cut[i],"-",rel[j])
    dat=vroom(paste0("inflation/related/out/",nm,".pairwise"), col_names=F)
    lista=read.table(paste0("inflation/related/out/",nm,".predictorsa"), stringsAsFactors = F)
    listb=read.table(paste0("inflation/related/out/",nm,".predictorsb"), stringsAsFactors = F)
    
    dat=dat[,-c(10001)]^2
    outb=data.frame(predictor=listb$V1, ave_r2=apply(dat,2, mean))
    outa=data.frame(predictor=lista$V1, ave_r2=apply(dat,1, mean))
    out=rbind(outa,outb)
    
    write.table(out,paste0("summary/ave-r2-",nm), col.names=F, row.names=F, quote=F)  
  }
}

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# summary
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# check if mean r^2_j differs for rel vs. unrel

cut=c("cut.125","cut.25","cut.5")

for(i in 1:length(cut)){
    nm_rel=paste0("ave-r2-10k-snps-",cut[i],"-rel")
    nm_unrel=paste0("ave-r2-10k-snps-",cut[i],"-unrel")
    
    dat_rel=read.table(paste0("summary/",nm_rel),stringsAsFactors = F)
    dat_unrel=read.table(paste0("summary/",nm_unrel),stringsAsFactors = F)
    
    out0=data.frame(rel=cut[i],
                    rel_ave=mean(dat_rel$V2),
                    rel_sd=sd(dat_rel$V2),
                    unrel_ave=mean(dat_unrel$V2),
                    unrel_sd=sd(dat_unrel$V2),
                    stringsAsFactors = F)
      if(i==1){out1=out0}else{out1=rbind(out1,out0)}
  }

```

### gwas


```bash

# create directories------------------------------------------------------------
for j in {cut.125,cut.25,cut.5}; do
for i in {rel,unrel};do
mkdir gwas-$j-$i
done
done

# gwas -------------------------------------------------------------------------
for j in {cut.125,cut.25,cut.5}; do
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do

# related individuals----------------------
out=../gwas-$j-rel/$i-linear
id=../relatedness/$j.related
snp=../gen/snps-rel-inds.use
bfile=../gen/geno-rel
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 8G
#SBATCH -c 1
#SBATCH -t 8:0:0

./ldak5.1 --linear $out \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile $bfile \
          --keep $id \
          --extract $snp \
          --covar ../phen/covariates.use \
          --max-threads 2 \
"> sh_script/$i-linear-$j-rel.sh

# unrelated individuals----------------------
out=../gwas-$j-unrel/$i-linear
id=../white-unrel-rand-control-for-$j
snp=../gen/snps-unrel-inds.use
bfile=../gen/geno-unrel
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 8G
#SBATCH -c 1
#SBATCH -t 8:0:0

./ldak5.1 --linear $out \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile $bfile \
          --keep $id \
          --extract $snp \
          --covar ../phen/covariates.use \
          --max-threads 2 \
"> sh_script/$i-linear-$j-unrel.sh
done
done

# submit jobs
for j in {cut.125,cut.25,cut.5}; do
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
sbatch -A snpher ../sh_script/$i-linear-$j-rel.sh
sbatch -A snpher ../sh_script/$i-linear-$j-unrel.sh
done
done>../job-records/gwas-related

# check job completion
file=job-records/gwas-related
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

```

### organize data


```bash

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# compute MAF
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

for j in {cut.125,cut.25,cut.5}; do
for i in {rel,unrel};do

out=../gen/maf/geno-$j-$i

if [ $i == rel ]; then
  bfile=../gen/geno-rel
  id=../relatedness/$j.related
else
  bfile=../gen/geno-unrel
  id=../white-unrel-rand-control-for-$j
fi

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 10G
#SBATCH -c 2
#SBATCH -t 5:0:0

./ldak5.1 --calc-stats $out \
          --bfile $bfile \
          --keep $id
    
"> sh_script/calc-maf-$j-$i.sh
done
done

# submit script
for j in {cut.125,cut.25,cut.5}; do
for i in {rel,unrel};do
sbatch -A snpher ../sh_script/calc-maf-$j-$i.sh
done
done>../job-records/calc-maf-rel

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# extract LD scores
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# we do this for related individuals.
# ld scores of snp list for unrelated id: snps-unrel-maf.001.ldscore 

# convert snp list to rs system ------------------------------------------------
infile=gen/snps-rel-inds.use
outfile=gen/snps-rel-rs
awk '(NR==FNR){a[$1]; b[$1]=$2; next}
     ($1 in a){print b[$1], $2}' doug/ukbb.ldsc $infile > $outfile

#  extract ld scores -----------------------------------------------------------
dir=ldsc/eur_w_ld_chr
for chrom in {1..22}; do
zcat $dir/$chrom.l2.ldscore.gz | awk 'NR>1 {print $2, $6}' > ldscore
awk '(NR==FNR){a[$1];next}($1 in a){print $0}' gen/snps-rel-rs ldscore > temp
if [ $chrom -eq 1 ]
then 
 mv temp snps-rel.ldscore
else
 cat snps-rel.ldscore temp > temp2
 mv temp2 snps-rel.ldscore
fi
echo $chrom
done

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  put info together
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# maf: gen/maf/geno-[cut.125]-[rel]
# chi square : gwas-[cut.125]-[rel]/[trait]-linear.summaries
# ave r^2_j: summary/ave-r2-10k-snps-[cut.25]-[unrel]
# ld score from ldsc ref panel: snps-unrel-maf.001.ldscore; snps-rel.ldscore

# fixed file
rs=doug/ukbb.ldsc

for j in {cut.125,cut.25,cut.5}; do
for i in {rel,unrel};do

# files vary depending on j &/or  i
maf=gen/maf/geno-$j-$i.stats # use as the snp list to integrate all info
aver2=summary/ave-r2-10k-snps-$j-$i

if [ $i == rel ]; then
  ldsc=snps-rel.ldscore
else
  ldsc=snps-unrel-maf.001.ldscore
fi

# create temporary files
awk 'NR > 1 {print $1, $5 }' $maf > tmp/maf.tmp
awk 'NR==FNR {a[$2]; b[$2]=$1; next} ($1 in a) {print b[$1], $0}' $rs $ldsc > tmp/ldsc.tmp1
# here we make sure the order of the rows are the same as maf.tmp
awk 'NR==FNR {a[$1]; b[$1]=$2; c[$1]=$3 ; next} 
     {if ($1 in a) print b[$1], c[$1];
      else print "NA"}' tmp/ldsc.tmp1 tmp/maf.tmp > tmp/ldsc.tmp
awk 'NR==FNR {a[$1]; b[$1]=$2; next} 
     {if ($1 in a) print b[$1]; 
     else print "NA" }' $aver2 tmp/maf.tmp > tmp/aver2.tmp

for trait in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do

# files vary with j, i and trait
gwas=gwas-$j-$i/$trait-linear.summaries
out=gwas-related-all-out/$trait-$j-$i.out

awk 'NR==FNR {a[$1]; b[$1]=$5; next} 
     {if ($1 in a) print b[$1]; 
     else print "NA" }' $gwas tmp/maf.tmp > tmp/gwas.tmp     

# put info together
paste tmp/maf.tmp \
      tmp/gwas.tmp \
      tmp/aver2.tmp \
      tmp/ldsc.tmp \
      | awk 'BEGIN{OFS=";" ; 
                  print "snp;maf;chisq;aver2;rs;ldsc"}
             {$1=$1}1' > $out
done
done
done

```

### summary


```r
#::::::::::::::::::::::::::::::::::::::::::::::
# estimate slope: chisq ~ aver2_j
#::::::::::::::::::::::::::::::::::::::::::::::

require(vroom)

#cuts=c("cut.125","cut.25","cut.5")
cuts="cut.125"
related=c("rel","unrel")

traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(i in 1:length(related)){
  for(j in 1:length(cuts)){
    for(k in 1:length(traits)){

    rel=related[i]
    cut=cuts[j]
    trait=traits[k]
    
    file=paste0("gwas-related-all-out/",trait,"-",cut,
                    "-",rel,".out")
    
    dat=vroom(file, col_names=T, delim=";")
    mod=lm(chisq ~ aver2,data=dat)
  
    slope0=data.frame(trait=trait,
                      cutoff=cut,
                      rel=rel,
                      slope_aver2=coef(mod)[2],
                      p_aver2=summary(mod)$coefficients[,4][2],
                      stringsAsFactors = F)
    
    if(i==1 & j==1 & k==1){slope=slope0} else {slope=rbind(slope,slope0)}
    
    }
  }
}

out=slope[order(slope$trait, slope$cutoff, slope$rel),]
#write.table(out,"summary/chisq-aver2-slope-gwas-related.txt", 
#            col.names=T, row.names=F, quote=F)


#::::::::::::::::::::::::::::::::::::::::::::::
# plot chisq ~ aver2_j  by rel | cut
#::::::::::::::::::::::::::::::::::::::::::::::

# function to plot a single trait---------------------------------------------------

make_plot=function(trait, cut){
  
  # define variables
  cut=cut
  trait=trait
  
  # define color
  library(RColorBrewer)
  qual_col_pals = brewer.pal.info[brewer.pal.info$category == 'qual',]
  col_vector = unlist(mapply(brewer.pal, qual_col_pals$maxcolors, rownames(qual_col_pals)))
  set.seed(14)
  mycol=sample(col_vector,2)
  
  # related 
    file1=paste0("gwas-related-all-out/",trait,"-",cut,"-rel.out")
    dat1=vroom(file1, col_names=T, delim=";")
    # bin a variable by quantile
    cutoff=quantile(dat1$aver2, probs = seq(0, 1, 0.005), na.rm=T)
    dat1$bin=cut(dat1$aver2, breaks=cutoff, labels=1:(length(cutoff)-1))
    # average chisq by bin values
    out1=data.frame(chisq_ave=tapply(dat1$chisq,INDEX=dat1$bin, mean))
    out1$bin_val=tapply(dat1$aver2,INDEX=dat1$bin, mean)
  
   # unrelated 
    file2=paste0("gwas-related-all-out/",trait,"-",cut,"-unrel.out")
    dat2=vroom(file2, col_names=T, delim=";")
    # bin a variable by quantile
    cutoff=quantile(dat2$aver2, probs = seq(0, 1, 0.005), na.rm=T)
    dat2$bin=cut(dat2$aver2, breaks=cutoff, labels=1:(length(cutoff)-1))
    # average chisq by bin values
    out2=data.frame(chisq_ave=tapply(dat2$chisq,INDEX=dat2$bin, mean))
    out2$bin_val=tapply(dat2$aver2,INDEX=dat2$bin, mean)  
    
    xstart=min(out1$bin_val,out2$bin_val)
    xend=max(out1$bin_val,out2$bin_val)
    ystart=min(out1$chisq_ave,out2$chisq_ave)
    yend=max(out1$chisq_ave,out2$chisq_ave)
    
    plot(out1$bin_val, out1$chisq_ave,
         xlab="ave r2", ylab="ave chisq",
         xlim=c(xstart,xend), ylim=c(ystart,yend),
         main=paste0(trait," ", cut), las=1,
         cex = 1.5, pch=21,  bg=mycol[1], col="white", lwd=0.5)
      points(out2$bin_val, out2$chisq_ave,
           cex = 1.5, pch=21, col="white", bg=mycol[2], lwd=0.5)
   if(trait=="awake"){
     legend("topleft", pch=19, legend=c("rel","unrel"), 
            col=mycol, cex=1.5, box.lty=0)}
   
} # end of function

# make a plot ------------------------------------------------------------------

require(vroom)
cuts=c("cut.125","cut.25","cut.5")
related=c("rel","unrel")
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(j in 1:length(cuts)){
  
  cut=cuts[j]
  # plot chisq ~ aver2 by rel for a given cutoff for relatedness
  png(paste0("fig/chisq-by-aver2-bin-related-gwas-",cut,".png"),
      width = 50, height = 30, units = "cm", res=600)
  par(mfrow=c(3,5))
      for(i in 1:length(traits)){
        trait=traits[i]
        make_plot(trait, cut)
      }
  dev.off()
}
```

## hapmap3 SNPs

### genotype data


```bash

#:::
# make bfiles by chromosome
#::::
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 1:0:0

./plink2 --pfile ../gen/geno_plink/bhr$j \
         --keep ../icd10/related.inds \
         --extract ../doug/ukbb.ldsc \
         --hwe 0.0001 \
         --hard-call-threshold .05 \
         --mach-r2-filter 0.8 2 \
         --make-bed \
         --memory 20000 \
         --out ../gen/tmp/bhr$j \
         --threads 3 \
"> sh_script/chr$j.sh
done

# submit script
for i in {1..22}; do
sbatch -A snpher ../sh_script/chr$i.sh
done>../job-records/mkbfile-related

# check job completion
file=job-records/mkbfile-related
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

#:::::
# merge bfiles 
#::::

rm bfile.list
for j in {1..22}; do
echo  "../gen/tmp/bhr$j" >>bfile.list
done

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 40:0:0
./ldak5.1 --make-bed ../gen/geno-rel-hmp3 \
          --mbfile ../gen/bfile.list \
          --max-threads 3 \
          --exclude-dups YES  
"> sh_script/mbfile.sh

# submit the script
sbatch -A snpher ../sh_script/mbfile.sh >../job-records/mbfiles-related

# snp list --------------------------------------------------------------------

# MAF & call-rate 
awk < gen/geno-rel-hmp3.stats '($5>.01 && $6>=0.95 && NR>1){print $1}' > gen/snps-rel-hmp3.use
# m = 1,105,446 SNPs

```

### gwas


```bash
# script ----------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
#for j in {cut.125,cut.25,cut.5}; do
j=cut.125

for chr in {1..22}; do
id=../relatedness/$j.related
snp=../gen/snps-rel-hmp3.use
bfile=../gen/geno-rel-hmp3
out=../gwas-$j-rel/$i-linear-$chr
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 8G
#SBATCH -c 1
#SBATCH -t 8:0:0

./ldak5.1 --linear $out \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile $bfile \
          --keep $id \
          --extract $snp \
          --covar ../phen/basic-covariates.use \
          --max-threads 2 \
          --chr $chr
"> sh_script/$i-linear-$j-rel-chr-$chr.sh
done
done
#done

# submit jobs-------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
j=cut.125
for chr in {1..22}; do
#for j in {cut.125,cut.25,cut.5}; do
sbatch -A snpher ../sh_script/$i-linear-$j-rel-chr-$chr.sh
#sbatch -A snpher ../sh_script/$i-linear-$j-unrel.sh
done
done>../job-records/gwas-related

# check job completion----------------------------------------------------------
file=job-records/gwas-related
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

# merge files-------------------------------------------------------------------

# only do .summaries & .pvalues
for i in {awake,bmi,quals,chron,ever,fvc,height,imp,neur,pulse,reaction,sbp,snoring,hyper}; do 
for j in {1..22}; do
if [ $j == 1 ]; then
  awk '{print $0}' $i-linear-$j.summaries > $i-linear.summaries
  awk '{print $0}' $i-linear-$j.pvalues > $i-linear.pvalues
else 
  awk 'NR>1 {print $0}' $i-linear-$j.summaries >> $i-linear.summaries
  awk 'NR>1 {print $0}' $i-linear-$j.pvalues >> $i-linear.pvalues
fi
done
done

```

### aver2_j


```bash

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  snp lists
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# snp list----------------------------------------------------------------------
# common snps of related & unrelated
unrel=gen/snps-unrel-inds.use # 1,103,209
rel=gen/snps-rel-hmp3.use # 1,105,446 SNPs
awk 'NR==FNR{a[$1];next} 
    ($1 in a) {print $1}' $unrel $rel > gen/common-unrel-rel-snps # 1,101,844

# lista & listb
# this will be common for all id lists
m=10000
infile=gen/common-unrel-rel-snps
left=inflation/related/left-snps
right=inflation/related/right-snps

awk '{split($1, a, ":");
    if (a[1]<8) print $1 }' $infile | shuf | head -n $m >$left  
awk '{split($1, a, ":");
    if (a[1]>=8) print $1 }' $infile | shuf | head -n $m >$right

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  compute ave r^2_j
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

#for nm in {cut.125,cut.25,cut.5}; do
nm=cut.125
# common snp lists
lista=../inflation/related/right-snps
listb=../inflation/related/left-snps

# related individuals
id=../relatedness/$nm.related
bfile=../gen/geno-rel-hmp3
out=../inflation/related/out/10k-snps-$nm-rel

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 2
#SBATCH -t 00:10:0
./ldak5.2 --max-threads 2 \
          --calc-inflation $out \
          --bfile $bfile \
          --keep $id \
          --lista $lista \
          --listb $listb
">sh_script/calc-r-10k-snps-$nm-rel

# unrelated controls
id=../white-unrel-rand-control-for-$nm
bfile=../gen/geno-unrel
out=../inflation/related/out/10k-snps-$nm-unrel

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 2
#SBATCH -t 00:10:0
./ldak5.2 --max-threads 2 \
          --calc-inflation $out \
          --bfile $bfile \
          --keep $id \
          --lista $lista \
          --listb $listb
">sh_script/calc-r-10k-snps-$nm-unrel
#done

# submit the job
#for nm in {cut.125,cut.25,cut.5}; do
for nm in cut.125; do
sbatch -A snpher ../sh_script/calc-r-10k-snps-$nm-rel
sbatch -A snpher ../sh_script/calc-r-10k-snps-$nm-unrel
done>../job-records/calc-r-related

#  compute ave r_j^2 for each i-------------------------------------------------
R
require(vroom)
#cut=c("cut.125","cut.25","cut.5")
cut=c("cut.125")
rel=c("rel","unrel")

for(j in 1:length(rel)){
for(i in 1:length(cut)){
    nm=paste0("10k-snps-",cut[i],"-",rel[j])
    dat=vroom(paste0("inflation/related/out/",nm,".pairwise"), col_names=F)
    lista=read.table(paste0("inflation/related/out/",nm,".predictorsa"), stringsAsFactors = F)
    listb=read.table(paste0("inflation/related/out/",nm,".predictorsb"), stringsAsFactors = F)
    
    dat=dat[,-c(10001)]^2
    outb=data.frame(predictor=listb$V1, ave_r2=apply(dat,2, mean))
    outa=data.frame(predictor=lista$V1, ave_r2=apply(dat,1, mean))
    out=rbind(outa,outb)
    
    write.table(out,paste0("summary/ave-r2-",nm), col.names=F, row.names=F, quote=F)  
  }
}

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# summary
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# check if mean r^2_j differs for rel vs. unrel

#cut=c("cut.125","cut.25","cut.5")
cut=c("cut.125")
for(i in 1:length(cut)){
    nm_rel=paste0("ave-r2-10k-snps-",cut[i],"-rel")
    nm_unrel=paste0("ave-r2-10k-snps-",cut[i],"-unrel")
    
    dat_rel=read.table(paste0("summary/",nm_rel),stringsAsFactors = F)
    dat_unrel=read.table(paste0("summary/",nm_unrel),stringsAsFactors = F)
    
    out0=data.frame(rel=cut[i],
                    rel_ave=mean(dat_rel$V2),
                    rel_sd=sd(dat_rel$V2),
                    unrel_ave=mean(dat_unrel$V2),
                    unrel_sd=sd(dat_unrel$V2),
                    stringsAsFactors = F)
      if(i==1){out1=out0}else{out1=rbind(out1,out0)}
  }

```

### organize data


```bash

#for j in {cut.125,cut.25,cut.5}; do
for j in cut.125; do
for i in {rel,unrel};do
for trait in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
gwas=gwas-$j-$i/$trait-linear.summaries
aver2=summary/ave-r2-10k-snps-$j-$i
out=gwas-related-all-out/$trait-$j-$i.out

awk 'NR>1 {print $1, $5}' $gwas > tmp/gwas.tmp
awk 'NR==FNR {a[$1]; b[$1]=$2; next} 
     {if ($1 in a) print b[$1]; 
     else print "NA" }' $aver2 tmp/gwas.tmp > tmp/aver2.tmp

# put info together
paste tmp/gwas.tmp \
      tmp/aver2.tmp \
      | awk 'BEGIN{OFS=";" ; 
                  print "snp;chisq;aver2"}
             {$1=$1}1' > $out
done
done
done

```

### chisq ~ aver2_j


```r
#::::::::::::::::::::::::::::::::::::::::::::::
# estimate slope: chisq ~ aver2_j
#::::::::::::::::::::::::::::::::::::::::::::::

require(vroom)

#cuts=c("cut.125","cut.25","cut.5")
cuts="cut.125"
related=c("rel","unrel")

traits=c("awake","bmi","chron","ever",
       "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(i in 1:length(related)){
  for(j in 1:length(cuts)){
    for(k in 1:length(traits)){

    rel=related[i]
    cut=cuts[j]
    trait=traits[k]
    
    file=paste0("gwas-related-all-out/",trait,"-",cut,
                    "-",rel,".out")
    
    dat=vroom(file, col_names=T, delim=";")
    mod=lm(chisq ~ aver2,data=dat)
  
    slope0=data.frame(trait=trait,
                      cutoff=cut,
                      rel=rel,
                      slope_aver2=coef(mod)[2],
                      p_aver2=summary(mod)$coefficients[,4][2],
                      stringsAsFactors = F)
    
    if(i==1 & j==1 & k==1){slope=slope0} else {slope=rbind(slope,slope0)}
    
    }
  }
}

out=slope[order(slope$trait, slope$cutoff, slope$rel),]
#write.table(out,"summary/chisq-aver2-slope-gwas-related.txt", 
#            col.names=T, row.names=F, quote=F)


#::::::::::::::::::::::::::::::::::::::::::::::
# plot chisq ~ aver2_j  by rel | cut
#::::::::::::::::::::::::::::::::::::::::::::::

# function to plot a single trait---------------------------------------------------

make_plot=function(trait, cut){
  
  # define variables
  cut=cut
  trait=trait
  
  # define color
  library(RColorBrewer)
  qual_col_pals = brewer.pal.info[brewer.pal.info$category == 'qual',]
  col_vector = unlist(mapply(brewer.pal, qual_col_pals$maxcolors, rownames(qual_col_pals)))
  set.seed(14)
  mycol=sample(col_vector,2)
  
  # related 
    file1=paste0("gwas-related-all-out/",trait,"-",cut,"-rel.out")
    dat1=vroom(file1, col_names=T, delim=";")
    # bin a variable by quantile
    cutoff=quantile(dat1$aver2, probs = seq(0, 1, 0.005), na.rm=T)
    dat1$bin=cut(dat1$aver2, breaks=cutoff, labels=1:(length(cutoff)-1))
    # average chisq by bin values
    out1=data.frame(chisq_ave=tapply(dat1$chisq,INDEX=dat1$bin, mean))
    out1$bin_val=tapply(dat1$aver2,INDEX=dat1$bin, mean)
  
   # unrelated 
    file2=paste0("gwas-related-all-out/",trait,"-",cut,"-unrel.out")
    dat2=vroom(file2, col_names=T, delim=";")
    # bin a variable by quantile
    cutoff=quantile(dat2$aver2, probs = seq(0, 1, 0.005), na.rm=T)
    dat2$bin=cut(dat2$aver2, breaks=cutoff, labels=1:(length(cutoff)-1))
    # average chisq by bin values
    out2=data.frame(chisq_ave=tapply(dat2$chisq,INDEX=dat2$bin, mean))
    out2$bin_val=tapply(dat2$aver2,INDEX=dat2$bin, mean)  
    
    xstart=min(out1$bin_val,out2$bin_val)
    xend=max(out1$bin_val,out2$bin_val)
    ystart=min(out1$chisq_ave,out2$chisq_ave)
    yend=max(out1$chisq_ave,out2$chisq_ave)
    
    plot(out1$bin_val, out1$chisq_ave,
         xlab="ave r2", ylab="ave chisq",
         xlim=c(xstart,xend), ylim=c(ystart,yend),
         main=paste0(trait," ", cut), las=1,
         cex = 1.5, pch=21,  bg=mycol[1], col="white", lwd=0.5)
      points(out2$bin_val, out2$chisq_ave,
           cex = 1.5, pch=21, col="white", bg=mycol[2], lwd=0.5)
   if(trait=="awake"){
     legend("topleft", pch=19, legend=c("rel","unrel"), 
            col=mycol, cex=1.5, box.lty=0)}
   
} # end of function

# make a plot ------------------------------------------------------------------

require(vroom)
#cuts=c("cut.125","cut.25","cut.5")
cuts="cut.125"
related=c("rel","unrel")
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(j in 1:length(cuts)){
  
  cut=cuts[j]
  # plot chisq ~ aver2 by rel for a given cutoff for relatedness
  png(paste0("fig/chisq-by-aver2-bin-related-gwas-",cut,".png"),
      width = 50, height = 30, units = "cm", res=600)
  par(mfrow=c(3,5))
      for(i in 1:length(traits)){
        trait=traits[i]
        make_plot(trait, cut)
      }
  dev.off()
}
```

### inflation ~ aver2_j


```r
#::::::::::::::::::::::::::::::::::::::::::::::
# estimate slope: inflation ~ aver2_j
#::::::::::::::::::::::::::::::::::::::::::::::

require(vroom)

cuts="cut.125"

traits=c("awake","bmi","chron","ever",
       "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(j in 1:length(cuts)){
    for(k in 1:length(traits)){

    cut=cuts[j]
    trait=traits[k]
    
    f1=paste0("gwas-related-all-out/",trait,"-",cut,"-rel.out")
    f2=paste0("gwas-related-all-out/",trait,"-",cut,"-unrel.out")
    
    d1=vroom(f1, col_names=T, delim=";")
    d2=vroom(f2, col_names=T, delim=";")
    common=intersect(d1$snp, d2$snp)
    m1=match(common, d1$snp)
    m2=match(common, d2$snp)
    dat=data.frame(snp=common,
                   inflation=d1$chisq[m1]-d2$chisq[m2],
                   aver2=d1$aver2[m1])
    
    mod=lm(inflation ~ aver2,data=dat)
  
    slope0=data.frame(trait=trait,
                      cutoff=cut,
                      slope_aver2=coef(mod)[2],
                      p_aver2=summary(mod)$coefficients[,4][2],
                      stringsAsFactors = F)
    
    if(j==1 & k==1){slope=slope0} else {slope=rbind(slope,slope0)}
    
    }
}

out=slope[order(slope$trait, slope$cutoff),]
#write.table(out,"summary/chisq-aver2-slope-gwas-related.txt", 
#            col.names=T, row.names=F, quote=F)


#::::::::::::::::::::::::::::::::::::::::::::::
# plot inflation ~ aver2_j  
#::::::::::::::::::::::::::::::::::::::::::::::
# make a plot ------------------------------------------------------------------
require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")
cut="cut.125"       
png(paste0("fig/inflation-by-aver2-bin-related-gwas.png"),
      width = 50, height = 30, units = "cm", res=600)
  par(mfrow=c(3,5))
for (i in 1:length(traits)){

    trait=traits[i]
    f1=paste0("gwas-related-all-out/",trait,"-",cut,"-rel.out")
    f2=paste0("gwas-related-all-out/",trait,"-",cut,"-unrel.out")
    
    d1=vroom(f1, col_names=T, delim=";")
    d2=vroom(f2, col_names=T, delim=";")
    common=intersect(d1$snp, d2$snp)
    m1=match(common, d1$snp)
    m2=match(common, d2$snp)
    dat=data.frame(snp=common,
                   inflation=d1$chisq[m1]-d2$chisq[m2],
                   aver2=d1$aver2[m1])    
    
    # bin a variable by quantile
    cutoff=quantile(dat$aver2, probs = seq(0, 1, 0.005), na.rm=T)
    dat$bin=cut(dat$aver2, breaks=cutoff, labels=1:(length(cutoff)-1))
    # average inflation by bin values
    out=data.frame(inflation_ave=tapply(dat$inflation,INDEX=dat$bin, mean))
    out$bin_val=tapply(dat$aver2,INDEX=dat$bin, mean)

plot(out$bin_val, out$inflation_ave,
         xlab="ave r2", ylab="ave inflation",
         main=trait, las=1,
         cex = 1.5, pch=21,  bg="orange", col="white", lwd=0.5)
 }
dev.off()
```

## mix unrel & rel

### genotype


```bash

# id list
awk '{print $1, $2}' rand.100000 icd10/related.inds > rel-unrel-combo.id 
# note: there are some overlap between the two id lists.

#:::
# make bfiles by chromosome
#::::
for j in {1..22}; do
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 1:0:0

./plink2 --pfile ../gen/geno_plink/bhr$j \
         --keep ../rel-unrel-combo.id \
         --extract ../doug/ukbb.ldsc \
         --hwe 0.0001 \
         --hard-call-threshold .05 \
         --mach-r2-filter 0.8 2 \
         --make-bed \
         --memory 20000 \
         --out ../gen/tmp/bhr$j-combo \
         --threads 3 \
"> sh_script/chr$j.sh
done

# submit script
for i in {1..22}; do
sbatch -A snpher ../sh_script/chr$i.sh
done>../job-records/mkbfile-related-unrel-combo

# check job completion
file=job-records/mkbfile-related-unrel-combo
jobs=`awk '{print $4}' $file`
mkdir $file-tmp
for i in $jobs; do
jobinfo $i | awk -F ":" -v i=$i '$1~/Name/ {print i, $2}' >> $file-tmp/name.tmp 
jobinfo $i | awk -F ":" '$1~/State/ {print$2}' >> $file-tmp/state.tmp
jobinfo $i | awk -F ":" '$1~/Cores/ {print$2}' >> $file-tmp/cores.tmp
jobinfo $i | awk -F ":" '$1~/Used walltime/ {print $2 ":" $3 ":" $4}' >> $file-tmp/time.tmp
jobinfo $i | awk -F ":" '$1~/Max Mem/ {split($2,a,/[(]/ ); print a[1]}' >> $file-tmp/mem.tmp
done
paste $file-tmp/name.tmp \
      $file-tmp/state.tmp \
      $file-tmp/cores.tmp \
      $file-tmp/time.tmp \
      $file-tmp/mem.tmp \
      | awk 'BEGIN{print "ID name state cores time mem"}{print $0}' > $file.out
rm -r $file-tmp

#:::::
# merge bfiles 
#::::

rm bfile.list
for j in {1..22}; do
echo  "../gen/tmp/bhr$j-combo" >>bfile.list
done

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 3
#SBATCH -t 40:0:0
./ldak5.1 --make-bed ../gen/geno-rel-unrel-combo \
          --mbfile ../gen/bfile.list \
          --max-threads 3 \
          --exclude-dups YES  
"> sh_script/mbfile.sh


# snp list --------------------------------------------------------------------

# MAF & call-rate 
awk < gen/geno-rel-unrel-combo.stats '($5>.01 && $6>=0.95 && NR>1){print $1}' > gen/snps-rel-unrel-combo.use
# m = 1,102,906 SNPs

```

### gwas


```bash

mkdir gwas-rel-unrel-combo

# script ----------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for chr in {1..22}; do
snp=../gen/snps-rel-unrel-combo.use
bfile=../gen/geno-rel-unrel-combo
out=../gwas-rel-unrel-combo/$i-linear-$chr

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 8G
#SBATCH -c 1
#SBATCH -t 8:0:0

./ldak5.1 --linear $out \
          --pheno ../phen/continuous-traits/$i.raw.pheno \
          --bfile $bfile \
          --extract $snp \
          --covar ../phen/basic-covariates.use \
          --max-threads 2 \
          --chr $chr
"> sh_script/$i-linear-chr-$chr.sh
done
done
#done

# submit jobs-------------------------------------------------------------------
for i in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do
for chr in {1..22}; do
sbatch -A snpher ../sh_script/$i-linear-chr-$chr.sh
done
done>../job-records/gwas-combo

# merge files-------------------------------------------------------------------

# only do .summaries & .pvalues
for i in {awake,bmi,quals,chron,ever,fvc,height,imp,neur,pulse,reaction,sbp,snoring,hyper}; do 
for j in {1..22}; do
if [ $j == 1 ]; then
  awk '{print $0}' $i-linear-$j.summaries > $i-linear.summaries
  awk '{print $0}' $i-linear-$j.pvalues > $i-linear.pvalues
else 
  awk 'NR>1 {print $0}' $i-linear-$j.summaries >> $i-linear.summaries
  awk 'NR>1 {print $0}' $i-linear-$j.pvalues >> $i-linear.pvalues
fi
done
done

```

### aver2_j


```bash

#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  snp lists
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# common snps of related & unrelated
unrel=gen/snps-unrel-inds.use
rel=gen/snps-rel-unrel-combo.use
awk 'NR==FNR{a[$1];next} 
    ($1 in a) {print $1}' $unrel $rel > gen/common-unrel-rel-combo-snps # 1,101,883

# lista & listb
m=10000
infile=gen/common-unrel-rel-combo-snps
left=inflation/combo/left-snps
right=inflation/combo/right-snps

awk '{split($1, a, ":");
    if (a[1]<8) print $1 }' $infile | shuf | head -n $m >$left  
awk '{split($1, a, ":");
    if (a[1]>=8) print $1 }' $infile | shuf | head -n $m >$right
  
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#  compute ave r^2_j
#:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

# related
lista=../inflation/combo/right-snps
listb=../inflation/combo/left-snps
bfile=../gen/geno-rel-unrel-combo
out=../inflation/combo/out/10k-snps

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 30G
#SBATCH -c 2
#SBATCH -t 00:30:0
./ldak5.2 --max-threads 2 \
          --calc-inflation $out \
          --bfile $bfile \
          --lista $lista \
          --listb $listb
">sh_script/calc-r-10k-snps

# submit the job
sbatch -A snpher ../sh_script/calc-r-10k-snps >../job-records/calc-r-combo

# unrelated
lista=../inflation/combo/right-snps
listb=../inflation/combo/left-snps
bfile=../gen/geno-unrel
id=../unrelated/rand.100000
out=../inflation/combo/out/10k-snps-unrel

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 30G
#SBATCH -c 2
#SBATCH -t 00:10:0
./ldak5.2 --max-threads 2 \
          --calc-inflation $out \
          --keep $id \
          --bfile $bfile \
          --lista $lista \
          --listb $listb
">sh_script/calc-r-10k-snps-unrel

# submit the job
sbatch -A snpher ../sh_script/calc-r-10k-snps-unrel >../job-records/calc-r-combo-unrel

#  compute ave r_j^2 for each i-------------------------------------------------
R
require(vroom)

nm="10k-snps"
dat=vroom(paste0("inflation/combo/out/",nm,".pairwise"), col_names=F)
lista=read.table(paste0("inflation/combo/out/",nm,".predictorsa"), 
                stringsAsFactors = F)
listb=read.table(paste0("inflation/combo/out/",nm,".predictorsb"), 
                stringsAsFactors = F)

dat=dat[,-c(10001)]^2
outb=data.frame(predictor=listb$V1, ave_r2=apply(dat,2, mean))
outa=data.frame(predictor=lista$V1, ave_r2=apply(dat,1, mean))
out=rbind(outa,outb)

write.table(out,paste0("summary/ave-r2-",nm, "-rel-unrel-combo"), col.names=F, row.names=F, quote=F) 

# for unrelated control rand.100000
R
require(vroom)
nm="10k-snps-unrel"
dat=vroom(paste0("inflation/combo/out/",nm,".pairwise"), col_names=F)
lista=read.table(paste0("inflation/combo/out/",nm,".predictorsa"), 
                stringsAsFactors = F)
listb=read.table(paste0("inflation/combo/out/",nm,".predictorsb"), 
                stringsAsFactors = F)

dat=dat[,-c(10001)]^2
outb=data.frame(predictor=listb$V1, ave_r2=apply(dat,2, mean))
outa=data.frame(predictor=lista$V1, ave_r2=apply(dat,1, mean))
out=rbind(outa,outb)

write.table(out,"summary/ave-r2-10k-snps-rel-unrel-combo-ref", 
            col.names=F, row.names=F, quote=F) 

```

### organize data


```bash

# related------------------------------------------------------------------------
for trait in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do

gwas=gwas-rel-unrel-combo/$trait-linear.summaries
aver2=summary/ave-r2-10k-snps-rel-unrel-combo
out=gwas-rel-unrel-combo-out/$trait.out

awk 'NR>1 {print $1, $5}' $gwas > tmp/gwas.tmp    

awk 'NR==FNR {a[$1]; b[$1]=$2; next} 
     {if ($1 in a) print b[$1]; 
     else print "NA" }' $aver2 tmp/gwas.tmp > tmp/aver2.tmp

# put info together
paste tmp/gwas.tmp \
      tmp/aver2.tmp \
      | awk 'BEGIN{OFS=";" ; 
                  print "snp;chisq;aver2"}
             {$1=$1}1' > $out
done

# unrelated control-------------------------------------------------------------
for trait in {awake,bmi,chron,ever,fvc,height,imp,neur,pulse,quals,reaction,sbp,snoring,hyper}; do

gwas=unrelated/gwas-good/$trait-linear.summaries
aver2=summary/ave-r2-10k-snps-rel-unrel-combo-ref
out=gwas-rel-unrel-combo-out/$trait-ref.out

awk 'NR>1 {print $1, $5}' $gwas > tmp/gwas.tmp    

awk 'NR==FNR {a[$1]; b[$1]=$2; next} 
     {if ($1 in a) print b[$1]; 
     else print "NA" }' $aver2 tmp/gwas.tmp > tmp/aver2.tmp

# put info together
paste tmp/gwas.tmp \
      tmp/aver2.tmp \
      | awk 'BEGIN{OFS=";" ; 
                  print "snp;chisq;aver2"}
             {$1=$1}1' > $out
done

```

### chisq ~ aver2_j


```bash

#::::::::::::::::::::::::::::::::::::::::::::::
# estimate slope: chisq ~ aver2_j
#::::::::::::::::::::::::::::::::::::::::::::::

require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(i in 1:length(traits)){

trait=traits[i]

file=paste0("gwas-rel-unrel-combo-out/",trait,".out")

dat=vroom(file, col_names=T, delim=";")
dat=dat[complete.cases(dat),]
mod=lm(chisq ~ aver2,data=dat)

slope0=data.frame(trait=trait,
                  slope_aver2=coef(mod)[2],
                  p_aver2=summary(mod)$coefficients[,4][2],
                  stringsAsFactors = F)

if(i==1){slope=slope0} else {slope=rbind(slope,slope0)}

}

#::::::::::::::::::::::::::::::::::::::::::::::
# plot chisq ~ aver2_j  
#::::::::::::::::::::::::::::::::::::::::::::::
# make a plot ------------------------------------------------------------------
require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")
        
png(paste0("fig/chisq-by-aver2-bin-unrel-rel-combo-gwas.png"),
      width = 50, height = 30, units = "cm", res=600)
  par(mfrow=c(3,5))

for (i in 1:length(traits)){
    trait=traits[i]
    file=paste0("gwas-rel-unrel-combo-out/",trait,".out")
    dat=vroom(file, col_names=T, delim=";")
    # bin a variable by quantile
    cutoff=quantile(dat$aver2, probs = seq(0, 1, 0.005), na.rm=T)
    dat$bin=cut(dat$aver2, breaks=cutoff, labels=1:(length(cutoff)-1))
    # average chisq by bin values
    out=data.frame(chisq_ave=tapply(dat$chisq,INDEX=dat$bin, mean))
    out$bin_val=tapply(dat$aver2,INDEX=dat$bin, mean)

plot(out$bin_val, out$chisq_ave,
         xlab="ave r2", ylab="ave chisq",
         main=trait, las=1,
         cex = 1.5, pch=21,  bg="orange", col="white", lwd=0.5)
 }
dev.off()
```

### inflation ~ aver2_j


```r
#::::::::::::::::::::::::::::::::::::::::::::::
# estimate slope: inflation ~ aver2_j
#::::::::::::::::::::::::::::::::::::::::::::::

require(vroom)

traits=c("awake","bmi","chron","ever",
       "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(i in 1:length(traits)){

    trait=traits[i]
    f1=paste0("gwas-rel-unrel-combo-out/",trait,".out")
    f2=paste0("gwas-rel-unrel-combo-out/",trait,"-ref.out")
    d1=vroom(f1, col_names=T, delim=";")
    d2=vroom(f2, col_names=T, delim=";")
    common=intersect(d1$snp, d2$snp)
    m1=match(common, d1$snp)
    m2=match(common, d2$snp)
    dat=data.frame(snp=common,
                   inflation=d1$chisq[m1]-d2$chisq[m2],
                   aver2=d1$aver2[m1])
    
    mod=lm(inflation ~ aver2,data=dat)
  
    slope0=data.frame(trait=trait,
                      slope_aver2=coef(mod)[2],
                      p_aver2=summary(mod)$coefficients[,4][2],
                      stringsAsFactors = F)
    
    if(i==1){slope=slope0} else {slope=rbind(slope,slope0)}
    
}


out=slope[order(slope$trait),]

#::::::::::::::::::::::::::::::::::::::::::::::
# plot inflation ~ aver2_j  
#::::::::::::::::::::::::::::::::::::::::::::::
# make a plot ------------------------------------------------------------------
require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")
     
png(paste0("fig/inflation-by-aver2-bin-rel-unrel-combo.png"),
      width = 50, height = 30, units = "cm", res=600)
  par(mfrow=c(3,5))
for (i in 1:length(traits)){

    trait=traits[i]
    f1=paste0("gwas-rel-unrel-combo-out/",trait,".out")
    f2=paste0("gwas-rel-unrel-combo-out/",trait,"-ref.out")
    d1=vroom(f1, col_names=T, delim=";")
    d2=vroom(f2, col_names=T, delim=";")
    common=intersect(d1$snp, d2$snp)
    m1=match(common, d1$snp)
    m2=match(common, d2$snp)
    dat=data.frame(snp=common,
                   inflation=d1$chisq[m1]-d2$chisq[m2],
                   aver2=d1$aver2[m1])    
    
    # bin a variable by quantile
    cutoff=quantile(dat$aver2, probs = seq(0, 1, 0.005), na.rm=T)
    dat$bin=cut(dat$aver2, breaks=cutoff, labels=1:(length(cutoff)-1))
    # average inflation by bin values
    out=data.frame(inflation_ave=tapply(dat$inflation,INDEX=dat$bin, mean))
    out$bin_val=tapply(dat$aver2,INDEX=dat$bin, mean)

plot(out$bin_val, out$inflation_ave,
         xlab="ave r2", ylab="ave inflation",
         main=trait, las=1,
         cex = 1.5, pch=21,  bg="orange", col="white", lwd=0.5)
 }
dev.off()
```




<!--chapter:end:05.1-related-individuals.rmd-->


# compute $\sum_{i,j}^{m_1, m_2}r^2_{i,j}$ {#calc_aver2_j}

## good GWAS-all qced snps


```bash

#::::
# here we define a R function to compute sum(r^2)
#:::

echo "# here we define a R function to compute sum(r^2)
options(stringsAsFactors=FALSE)
ip<-commandArgs(trailingOnly=TRUE)
options(warn=1)

require(vroom)

compute_sumr2=function(filein, fileout){

  dir=\"/home/zhoux/dsmwpred/xuan/quality-control/qc-10oct/inflation/\"
  dat=vroom(paste0(dir,\"out/\", filein), col_names=F)
  r=dat[lower.tri(dat, diag = T)]
  sumr2=sum(r^2)
  write.table(cbind(sumr2, length(r)), paste0(dir,\"summary/\", fileout),
  col.names=F, row.names=F, quote=F, append=T)
}

compute_sumr2(ip[1],ip[2])
">inflation/calc-sum-r2.r

```



```bash

# script
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 10
#SBATCH -t 4:00:0

#:::
# 0. define vars & create snp lists
#:::

  m=1000
  nm=1k
  lista=left-snps-goodgwas-$nm
  listb=right-snps-goodgwas-$nm
  out=$nm-snps-goodgwas
  filein=$nm-snps-goodgwas.pairwise
  fileout=sum-r2-$nm-snps-goodgwas

# loop and recycle files to avoid storage problems

for i in {1..500}; do

    #::
    # 1. create snp lists & use ldak to compute r_i,j
    #::
    
      shuf ../gen/left-hapmap3.snps | head -n $m > ../inflation/$lista 
      shuf ../gen/right-hapmap3.snps | head -n $m > ../inflation/$listb
      
      ./ldak5.2 --max-threads 10 \
                --calc-inflation ../inflation/out/$out \
                --bfile ../gen/geno-unrel \
                --lista ../inflation/$lista \
                --listb ../inflation/$listb 
    
    #::
    # 2. compute sum(r^2): see for details
    #::
    
      Rscript --vanilla ../inflation/calc-sum-r2.r $filein $fileout

done

">sh_script/calc-sum-r2

# submit the job
m=1000
nm=1k

sbatch -A snpher ../sh_script/calc-sum-r2 >../job-records/calc-sum-r2-$nm-snps-goodgwas

```

## good GWASs- ldak-thin snps


```bash

# script
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 10
#SBATCH -t 4:00:0

#:::
# 0. define vars & create snp lists
#:::

# to be changed

  m=1000
  nm=1k
  snpa=../gen/left-mix-pop.snps
  snpb=../gen/right-mix-pop.snps
  id=../mix-pop-gwas.id
  bfile=../gen/geno-mix
  lista=left-snps-badgwas-$nm
  listb=right-snps-badgwas-$nm
  out=$nm-snps-badgwas
  filein=$nm-snps-badgwas.pairwise
  fileout=sum-r2-$nm-snps-badgwas

# loop and recycle files to avoid storage problems

for i in {1..500}; do

    #::
    # 1. create snp lists & use ldak to compute r_i,j
    #::
    
      shuf $snpa | head -n $m > ../inflation/$lista 
      shuf $snpb | head -n $m > ../inflation/$listb
      
      ./ldak5.2 --max-threads 10 \
                --calc-inflation ../inflation/out/$out \
                --bfile $bfile \
                --keep $id \
                --lista ../inflation/$lista \
                --listb ../inflation/$listb 
    
    #::
    # 2. compute sum(r^2): see for details
    #::
    
      Rscript --vanilla ../inflation/calc-sum-r2.r $filein $fileout

done

">sh_script/calc-sum-r2

# submit the job
m=1000
nm=1k

sbatch -A snpher ../sh_script/calc-sum-r2 >../job-records/calc-sum-r2-$nm-snps-badgwas

```

## bad GWASs-all qced snps


```bash

# script
echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 10
#SBATCH -t 4:00:0

#:::
# 0. define vars & create snp lists
#:::

# to be changed

  m=1000
  nm=1k
  snpa=../gen/left-mix-pop.snps
  snpb=../gen/right-mix-pop.snps
  id=../mix-pop-gwas.id
  bfile=../gen/geno-mix
  lista=left-snps-badgwas-$nm
  listb=right-snps-badgwas-$nm
  out=$nm-snps-badgwas
  filein=$nm-snps-badgwas.pairwise
  fileout=sum-r2-$nm-snps-badgwas

# loop and recycle files to avoid storage problems

for i in {1..500}; do

    #::
    # 1. create snp lists & use ldak to compute r_i,j
    #::
    
      shuf $snpa | head -n $m > ../inflation/$lista 
      shuf $snpb | head -n $m > ../inflation/$listb
      
      ./ldak5.2 --max-threads 10 \
                --calc-inflation ../inflation/out/$out \
                --bfile $bfile \
                --keep $id \
                --lista ../inflation/$lista \
                --listb ../inflation/$listb 
    
    #::
    # 2. compute sum(r^2): see for details
    #::
    
      Rscript --vanilla ../inflation/calc-sum-r2.r $filein $fileout

done

">sh_script/calc-sum-r2

# submit the job
m=1000
nm=1k

sbatch -A snpher ../sh_script/calc-sum-r2 >../job-records/calc-sum-r2-$nm-snps-badgwas

```

## summary


```bash

#::
# ave r_ij^2
#::

# good gwas
grep 'Average squared correlation' sh_out/calc-sum-r2-60083794.out | awk '{split($0, a, ";") split(a[1], b, " "); print b[5]}' > inflation/summary/ave-r2-1k-snps-goodgwas

# bad gwas
grep 'Average squared correlation' sh_out/calc-sum-r2-60086056.out | awk '{split($0, a, ";") split(a[1], b, " "); print b[5]}' > inflation/summary/ave-r2-1k-snps-badgwas

R
good=read.table("inflation/summary/ave-r2-1k-snps-goodgwas", header=F)
bad=read.table("inflation/summary/ave-r2-1k-snps-badgwas", header=F)

png("fig/ave-r2-his-1k-snps.png",
    width =40, height = 20, units = "cm", res=600)
par(mfrow=c(1,2))
hist(good$V1, main="ave r2 good GWAS", breaks=50, freq=F, 
    col="lightgray", border="lightgray")
hist(bad$V1, main="ave r2 bad GWAS", breaks=50, freq=F, 
    col="lightgray", border="lightgray")
dev.off()
# mean var
# good 1.004277e-05 2.172758e-16
# bad 0.0003566028 9.250966e-10

#::
# r_ij^2
#::

require(vroom)
good=vroom("inflation/out/1k-snps-goodgwas.pairwise", col_names=F)
bad=vroom("inflation/out/1k-snps-badgwas.pairwise", col_names=F)
r2_good=c(as.matrix(good)^2)
r2_bad=c(as.matrix(bad)^2)

png("fig/r2-his-1k-snps.png",
    width =40, height = 20, units = "cm", res=600)
par(mfrow=c(1,2))
hist(r2_good, main="r2 good GWAS", breaks=100, freq=F, 
    col="lightgray", border="lightgray")
hist(r2_bad, main="r2 bad GWAS", breaks=100, freq=F, 
    col="lightgray", border="lightgray")
dev.off()
# mean var
# good 1.002858e-05 2.020087e-10
# bad 0.0003632657 2.666479e-06

```



```r
# good gwas
dat=read.table("inflation/summary/sum-r2-1k-snps-goodgwas", header=F)
png(paste0("fig/sumr2-good-gwas-1k-snps.png"),
    width =20, height = 20, units = "cm", res=600)
hist(dat$V1, main="good GWAS")
dev.off()

# bad gwas
dat=read.table("inflation/summary/sum-r2-1k-snps-badgwas", header=F)
png(paste0("fig/sumr2-bad-gwas-1k-snps.png"),
    width =20, height = 20, units = "cm", res=600)
hist(dat$V1, main="bad GWAS")
dev.off()
```

## compute $\overline{r^2_j}$

1. compute average r2 for each i.

```r
#:::
# 1. compute r^2
#:::

echo "#"'!'"/bin/bash
#SBATCH --constraint \"s04|s05\"
#SBATCH --partition normal
#SBATCH --mem 20G
#SBATCH -c 10
#SBATCH -t 4:00:0

#:::
# 0. define vars & create snp lists
#:::

# to be changed
m=10000
nm=10k
lista=left-snps-unrel-inds-\$nm
listb=right-snps-unrel-inds-\$nm

# NOTE: both good and bad gwas share the same snp list: snps-unrel-inds.use
awk '{split(\$1, a, \":\"); if (a[1]<8) print \$1 }' ../gen/snps-unrel-inds.use | shuf | head -n \$m >../inflation/\$lista  
awk '{split(\$1, a, \":\"); if (a[1]>=8) print \$1 }' ../gen/snps-unrel-inds.use | shuf | head -n \$m >../inflation/\$listb

#::
# 1. good gwas
#::

id=../unrelated/rand.100000
bfile=../gen/geno-unrel
out=\$nm-snps-goodgwas
  
./ldak5.2 --max-threads 10 \
          --calc-inflation ../inflation/out/\$out \
          --bfile \$bfile \
          --keep \$id \
          --lista ../inflation/\$lista \
          --listb ../inflation/\$listb 

#::
# 2. bad gwas
#::

id=../mix-pop-gwas.id
bfile=../gen/geno-mix
out=\$nm-snps-badgwas
  
./ldak5.2 --max-threads 10 \
          --calc-inflation ../inflation/out/\$out \
          --bfile \$bfile \
          --keep \$id \
          --lista ../inflation/\$lista \
          --listb ../inflation/\$listb 
    
">sh_script/calc-sum-r2

# submit the job
m=10000
nm=10k

sbatch -A snpher ../sh_script/calc-sum-r2 >../job-records/calc-sum-r2-$nm-snps

#:::
# 2. compute ave r_j^2 for each i
#:::

R
require(vroom)
names=c("goodgwas", "badgwas")

for(i in 1:length(names)){
nm=names[i]
dat=vroom(paste0("inflation/out/10k-snps-",nm,".pairwise"), col_names=F)
lista=read.table(paste0("inflation/out/10k-snps-",nm,".predictorsa"), stringsAsFactors = F)
listb=read.table(paste0("inflation/out/10k-snps-",nm,".predictorsb"), stringsAsFactors = F)

dat=dat[,-c(10001)]^2
outb=data.frame(predictor=listb$V1, ave_r2=apply(dat,2, mean))
outa=data.frame(predictor=lista$V1, ave_r2=apply(dat,1, mean))
out=rbind(outa,outb)

write.table(out,paste0("summary/ave-r2-by-snp-",nm), col.names=F, row.names=F, quote=F)  
}

#:::
# 3. convert to rs system
#:::

awk '(NR==FNR){a[$1]; b[$1]=$2; next}($1 in a){print b[$1], $2}' doug/ukbb.ldsc summary/ave-r2-by-snp-goodgwas > summary/ave-r2-by-snp-goodgwas-rs

awk '(NR==FNR){a[$1]; b[$1]=$2; next}($1 in a){print b[$1], $2}' doug/ukbb.ldsc summary/ave-r2-by-snp-badgwas > summary/ave-r2-by-snp-badgwas-rs
```

## mixed pop GWAS with PC as covariates

Previously we conducted mixed pop GWAS with the first 40 PC as covariates. Since we used the same genotype data (i.e., the same individuals) as for the bad GWASs, $\overline{r^2_j}$ is the same. The difference is the chi-square test statistics. Here we want to check if the relationship between $\overline{r^2_j}$ and chi square still exists for the bad gwas.


```r
# align GWAS test statistics with ld score-------------------------------------- 
dir1="gwas-mix-with-cov/"
dir2="unrelated/gwas-good/"
dir3="gwas-mix/"

require("vroom")
snps=vroom("overlap-control-good-bad.ldscore", col_names=F)
names(snps)=c("snp","ldsc_ref")

traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp",
        "reaction","sbp","snoring","hyper")

for(i in 1:length(traits)){
  
  trait=traits[i]
  dat1=vroom(paste0(dir1,trait,"-linear-rs.summaries"), col_names=T)
  dat2=vroom(paste0(dir2,trait,"-linear-rs.summaries"), col_names=T)
  dat3=vroom(paste0(dir3,trait,"-linear-rs.summaries"), col_names=T)
  
  m1=match(snps$snp, dat1$SNP)
  m2=match(snps$snp, dat2$SNP)
  m3=match(snps$snp, dat3$SNP)
  out=data.frame(SNP=snps$snp,
                 ldsc_ref=snps$ldsc_ref,
                  bad_cov_chisq=dat1$Z[m1]^2,
                  good_chisq=dat2$Z[m2]^2,
                  bad_chisq=dat3$Z[m3]^2, stringsAsFactors = F)
  out=out[complete.cases(out),]
  # bin LD scores according to quantiles
  cutoff1=quantile(out$ldsc_ref, probs = seq(0, 1, 0.005))
  out$ldsc_ref_bin=cut(out$ldsc_ref, breaks=cutoff1, labels=1:(length(cutoff1)-1))
  write.table(out,paste0("summary/",trait,"-gwas-test-stats-compare3.txt"), 
              col.names=T, row.names=F, quote=F)
}

# align chisq with ave r^2_j----------------------------------------------------

stat=vroom("gen/geno-unrel-rs.maf", col_names=F)
r2_good=vroom("summary/ave-r2-by-snp-goodgwas-rs", col_names=F)
r2_bad=vroom("summary/ave-r2-by-snp-badgwas-rs", col_names=F)

traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")

for(i in 1:length(traits)){
  
trait=traits[i]
dat=vroom(paste0("summary/",trait,"-gwas-test-stats-compare3.txt"), col_names=T)
inflation=data.frame(snp=dat$SNP,
                     inflation=dat$bad_chisq-dat$good_chisq,
                     bad_chisq=dat$bad_chisq,
                     bad_cov_chisq=dat$bad_cov_chisq,
                     good_chisq=dat$good_chisq,
                     stringsAsFactors = F)

m1=match(inflation$snp, stat$X1)
m2=match(inflation$snp,r2_good$X1)
m3=match(inflation$snp,r2_bad$X1)
out=data.frame(inflation,
               maf=stat$X4[m1], 
               r2_good=r2_good$X2[m2],
               r2_bad=r2_bad$X2[m2], stringsAsFactors = F)

# bin maf & r2_bad according to quantiles
cutoff1=quantile(out$maf, probs = seq(0, 1, 0.005), na.rm=T)
cutoff2=quantile(out$r2_bad, probs = seq(0, 1, 0.005), na.rm=T)
cutoff3=quantile(out$r2_good, probs = seq(0, 1, 0.005), na.rm=T)

out$maf_bin=cut(out$maf, breaks=cutoff1, labels=1:(length(cutoff1)-1))
out$r2_bad_bin=cut(out$r2_bad, breaks=cutoff2, labels=1:(length(cutoff2)-1))
out$r2_good_bin=cut(out$r2_good, breaks=cutoff3, labels=1:(length(cutoff3)-1))

write.table(out,paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), 
            col.names=T, row.names=F, quote=F)
}

# plot bad chisq by binned ave r2 --------------------------------------------------
require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")
png(paste0("fig/badgwas-with-cov-chisq-by-aver2-bin.png"),
    width = 50, height = 30, units = "cm", res=600)
par(mfrow=c(3,5))
for(i in 1:length(traits)){
trait=traits[i]
dat=vroom(paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), col_names=T)
sel=dat[,c("snp","bad_cov_chisq","r2_bad","r2_bad_bin")]
sel=sel[complete.cases(sel),]
out=data.frame(bad_cov_chisq=tapply(sel$bad_cov_chisq,INDEX=sel$r2_bad_bin, mean))
out$r2_bad_bin=1:dim(out)[1]
out$r2_bad_bin_val=tapply(sel$r2_bad,INDEX=sel$r2_bad_bin, mean)

plot(out$r2_bad_bin_val, out$bad_cov_chisq,
     xlab="ave r2", ylab="chisq test stat",
     main=trait, las=1,
     cex = 1.5, pch=21,  bg="grey", col="white", lwd=0.5)
}
dev.off()

# plot good chisq by binned ave r2 --------------------------------------------------
require(vroom)
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp", 
        "reaction","sbp","snoring","hyper")
png(paste0("fig/goodgwas-chisq-by-aver2-bin.png"),
    width = 50, height = 30, units = "cm", res=600)
par(mfrow=c(3,5))
for(i in 1:length(traits)){
trait=traits[i]
dat=vroom(paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), col_names=T)
sel=dat[,c("snp","good_chisq","r2_good","r2_good_bin")]
sel=sel[complete.cases(sel),]
out=data.frame(good_chisq=tapply(sel$good_chisq,INDEX=sel$r2_good_bin, mean))
out$r2_good_bin=1:dim(out)[1]
out$r2_good_bin_val=tapply(sel$r2_good,INDEX=sel$r2_good_bin, mean)

plot(out$r2_good_bin_val, out$good_chisq,
     xlab="ave r2", ylab="chisq test stat",
     main=trait, las=1,
     cex = 1.5, pch=21,  bg="grey", col="white", lwd=0.5)
}
dev.off()

# plot chisq by ldsc----------------------------------------------------------- 

require("vroom")
traits=c("awake","bmi","chron","ever",
        "neur","pulse","quals", "fvc", "height","imp",
        "reaction","sbp","snoring","hyper")

png(paste0("fig/badgwas-with-cov-by-ldscbin.png"),
    width =50, height = 30, units = "cm", res=600)
par(mfrow=c(3,5))

for(i in 1:length(traits)){
trait=traits[i]
trait
dat=vroom(paste0("summary/",trait,"-gwas-test-stats-compare3.txt"), col_names=T)
out1=data.frame(ave_ldscore=tapply(dat$ldsc_ref,INDEX=dat$ldsc_ref_bin, mean),
               ave_good_chisq=tapply(dat$good_chisq,INDEX=dat$ldsc_ref_bin, mean),
               ave_bad_cov_chisq=tapply(dat$bad_cov_chisq,INDEX=dat$ldsc_ref_bin, mean))
               
#out2=data.frame(ave_ldscore=tapply(dat$ldsc_mix,INDEX=dat$ldsc_mix_bin, mean),
#               ave_control_chisq=tapply(dat$control_chisq,INDEX=dat$ldsc_mix_bin, mean),
#               ave_good_chisq=tapply(dat$good_chisq,INDEX=dat$ldsc_mix_bin, mean),
#              ave_bad_chisq=tapply(dat$bad_chisq,INDEX=dat$ldsc_mix_bin, mean))

end=round(max(c(out1[,2], out1[,3])),0)
#end2=round(max(c(out2[,2], out2[,3], out2[,4])),0)
#end=max(end1,end2)
start=0

plot(out1$ave_ldscore, out1$ave_good_chisq,
     xlab="ldscore", ylab="mean chisquare", 
     ylim=c(start, end),
     main=trait, las=1,
     cex = 1.5, pch=21,  bg="gray", col="white", lwd=0.5)
points(out1$ave_ldscore, out1$ave_bad_cov_chisq,
       cex = 1.5, pch=21, col="white", bg="orange")
}
dev.off()

# regress chi square from bad gwas with cov on ldsc

require(vroom)
traits=c("awake","bmi","chron","ever",
         "neur","pulse","quals", "fvc", "height","imp",
         "reaction","sbp","snoring","hyper")

for(i in 1:length(traits)){

trait=traits[i]
trait
dat=vroom(paste0("summary/",trait,"-gwas-test-stats-compare3.txt"), col_names=T)
dat2=vroom(paste0("inflation/summary/",trait,"-inflation-by-maf-r2"), col_names=T)
m=match(dat$SNP,dat2$snp)
out=data.frame(dat2[m,-c(9:11)],dat[,-c(1,3:6)])
out=out[complete.cases(out),]

#ldsc_ref2=(out$ldsc_ref)^2
#r2_bad2=(out$r2_bad)^2
mod0=lm(bad_cov_chisq ~ 1,data=out)
mod1=lm(bad_cov_chisq ~ ldsc_ref,data=out)
mod1.1=lm(bad_chisq ~ ldsc_ref + ldsc_ref2 ,data=out)
mod2=lm(bad_chisq ~ r2_bad, data=out)
mod2.1=lm(bad_chisq ~ r2_bad + r2_bad2, data=out)
mod3=lm(bad_chisq ~ r2_bad + ldsc_ref ,data=out)
mod3.1=lm(bad_chisq ~ r2_bad + ldsc_ref + ldsc_ref2 ,data=out)
mod3.2=lm(bad_chisq ~ r2_bad + ldsc_ref + ldsc_ref2 + r2_bad*ldsc_ref ,data=out)
mod4=lm(bad_chisq ~ r2_bad + ldsc_ref + r2_bad*ldsc_ref ,data=out)

p0=data.frame(trait=trait,
              p_r2=anova(mod2, mod0)$P[2],
              p_r2_2=anova(mod2.1, mod2)$P[2],
              p_ldsc=anova(mod3,mod2)$P[2],
              p_ldsc2=anova(mod3.1,mod3)$P[2],
              p_ldsc_r2=anova(mod4,mod3)$P[2],
              stringsAsFactors = F)

if(i==1){p=p0} else{p=rbind(p,p0)}
}
p_bad=p
```





<!--chapter:end:06_working_space.Rmd-->

