A Kernel approach for extending Nonparametric Multivariate Analysis of
Variance in high-dimensional settings
================

This is a document explaining and providing the code for the Kernel
approach that extends the Nonparametric Multivariate Analysis of
Variance (KMANOVA) in high-dimensional settings (Gallego and Oller
2024). To illustrate the methodology we will explore the role of the
genetic variation for explaining Alzheimer’s disease and use data from a
genome-wide association study (Reiman et al. 2007).

## Loading the data

The dataset corresponds to a case-control study with $1410$ subjects
($860$ cases and $550$ controls) and is restricted to the $680$
genotyped SNPs which lie within $30$ genes of the Reelin signaling
pathway. The same data set was used in Gallego, Calle, and Oller (2017)
to measure the importance of each gene to the joint genetic effect on
the risk of Alzheimer’s disease.

``` r
load("snpdata.rda")
dim(snpdata)
```

    [1] 1410  681

``` r
head(names(snpdata),n=10)
```

     [1] "y"             "SNP_A.1812613" "SNP_A.1805969" "SNP_A.2014547" "SNP_A.1942846" "SNP_A.1839344"
     [7] "SNP_A.2143419" "SNP_A.1849980" "SNP_A.2152054" "SNP_A.1900408"

``` r
table(snpdata$y)
```


      0   1 
    550 860 

<br> Each attribute in the data frame is labelled. The label of each SNP
provides the name of the corresponding gene in the Reelin signaling
pathway.

``` r
library(Hmisc)
label(snpdata$y)
```

    [1] "Alzheimer's disease"

``` r
table(label(snpdata[,-1]))
```


      ABL1   ABL2   AKT1 APOER2    APP   BDNF CAMK2A  CDC42   CDK5   CNR1   DAB1  EPHA1    FYN  GSK3B  ITGA3   LDLR 
        27      8      3     11     50      6     11     12      4      8    252      3     37      7     10      4 
      LRP2 PIK3R1  PSEN1  PSEN2   RAC1   RELN    RHO   RHOA   SHIP    SRC    TAU   TBR1   TP73  VLDLR 
        44     10      4      6      5     82      2      4     18      4     31      6      4      7 

## Loading the main function

The KMANOVA methodology takes advantage of the Kernel framework. The
idea is to project the data from the original data space to a Hilbert
space generated by a given Kernel function and then perform the
Nonparametric Multivariate Analysis of Variance in the reproducing
Hilbert Kernel space (RKHS). Dispersion of the embedded points can be
measured by the distance induced by the inner product in the RKHS but
also by many other distances, for instance, a Manhattan-type distance
and a distance based on an orthogonal projection of the embedded points
in the direction of the group centroids. Notice that the Nonparametric
Multivariate Analysis of Variance is essentially equivalent to the
KMANOVA method with the induced distance.

The methodology is implemented with the `KMANOVA_pv` function. This
function depends on a combination of several functions that calculate
Kernel matrices, distances in the Hilbert Space and p-values and also on
several R packages.

``` r
library("rARPACK")
library("kyotil")
library("vegan")
library("BinaryDosage")
load("Kernel_matrix.rda")
load("Distances_Hilbert.rda")
load("kmanova_pv.rda")
```

<br> The `KMANOVA_pv` function takes the following arguments:

- *data*: a data frame that contains the characterization of the
  individuals to analyze. .
- *y*: a binary vector (1 for cases, 0 for controls).
- *k_m*: the Kernel matrix to be used. By default, k_m=NULL, but if it
  is replaced by a kernel matrix, k should be “NULL”.
- *k*: the Kernel to be used, currently only “Polygenic”, “IBS”,
  “Weighted_Linear”, “Weighted_IBS” or “NULL”. If the Kernel is “NULL”,
  the kernel matrix (k_m) should be given by the user.
- *dist_hilbert*: the distance on the Hilbert Space to be used,
  currently only ‘Manhattan’, ‘PROJ’ or ‘Induced’.
- *nperm*: number of permutations.

## Studying the DAB1 gene

Now, we focus our attention on evaluating whether DAB1 gene is
associated with Alzheimer’s disease. We illustrate the methodology
considering a polygenic Kernel and comparing the results obtained from
the different distances: a significant association is found except for
the case of the induced distance.

``` r
snpdataDAB1=snpdata[,label(snpdata)=="DAB1"]
dim(snpdataDAB1)
```

    [1] 1410  252

### Induced distance

``` r
set.seed(12345)
KMANOVA_pv(snpdataDAB1,snpdata$y,k="Polygenic",dist_hilbert="Induced",nperm=999)
```

    [1] 0.542

### Manhattan distance

``` r
set.seed(12345)
KMANOVA_pv(snpdataDAB1,snpdata$y,k="Polygenic",dist_hilbert="Manhattan",nperm=999)
```

    [1] 0.044

### Orthogonal projection distance

``` r
set.seed(12345)
KMANOVA_pv(snpdataDAB1,snpdata$y,k="Polygenic",dist_hilbert="PROJ",nperm=999)
```

    [1] 0.022

<br>

# References

<div id="refs" class="references csl-bib-body hanging-indent"
entry-spacing="0">

<div id="ref-gallego2017kernel" class="csl-entry">

Gallego, Vicente, M Luz Calle, and Ramon Oller. 2017. “Kernel-Based
Measure of Variable Importance for Genetic Association Studies.” *The
International Journal of Biostatistics* 13 (2).

</div>

<div id="ref-gallegooller2024" class="csl-entry">

Gallego, Vicente, and Ramon Oller. 2024. “A Kernel Approach for
Extending Nonparametric Multivariate Analysis of Variance in
High-Dimensional Settings,” Under submission.

</div>

<div id="ref-reiman2007gab2" class="csl-entry">

Reiman, Eric M, Jennifer A Webster, Amanda J Myers, John Hardy, Travis
Dunckley, Victoria L Zismann, Keta D Joshipura, et al. 2007. “GAB2
Alleles Modify Alzheimer’s Risk in APOE $\epsilon$<!-- -->4 Carriers.”
*Neuron* 54 (5): 713–20.

</div>

</div>
