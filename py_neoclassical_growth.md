Calibrating the Neoclassical Growth Model to Paraguay
================
Paul
2017-08-07

Introduction
------------

The purpose of this document is to show how to calibrate the neoclassical growth model. The example shown here is for Paraguay, but the same method could be applied to any country where data is available. I begin by introducing the model. Then, I perform the calibration, forecast GDP to the year 2025, and perform some counterfactuals.

The Neoclassical Growth Model
-----------------------------

In its simplest form the neoclassical growth model is defined by the following set of equations:

*Y*<sub>*t*</sub> = *K*<sub>*t*</sub><sup>*α*</sup>(*A*<sub>*t*</sub>*L*<sub>*t*</sub>)<sup>(1 − *α*)</sup>
*K*<sub>*t* + 1</sub> = *I*<sub>*t*</sub> + (1 − *δ*)*K*<sub>*t*</sub>
*K*<sub>*t* + 1</sub> = *I*<sub>*t*</sub> + (1 − *δ*)*K*<sub>*t*</sub>
*L*<sub>*t* + 1</sub> = (1 + *g*<sub>*L*</sub>)*L*<sub>*t*</sub>
*A*<sub>*t* + 1</sub> = (1 + *g*<sub>*A*</sub>)*A*<sub>*t*</sub>,
 along with initial conditions for *K*<sub>0</sub>, *L*<sub>0</sub>, and *A*<sub>0</sub>. *Y*<sub>*t*</sub> is total output measured by real GDP in constant dollars. Total ouput is produced by a Cobb-Douglas production function with capital *K*<sub>*t*</sub>, labor input *L*<sub>*t*</sub>, and productivity *A*<sub>*t*</sub>. The parameter *α*, with 0 &lt; *α* &lt; 1, is referred to as the "physical capital share."

Labor *L*<sub>*t*</sub> and productivity *A*<sub>*t*</sub> grow at exogenous growth rates of *g*<sub>*L*</sub> and *g*<sub>*A*</sub>, respectively. But capital *K*<sub>*t*</sub> is determined by physical capital accumulation, i.e. the "law of motion capital", where future capital *K*<sub>*t* + 1</sub> is determined by investment *I*<sub>*t*</sub>, with the savings rate *s*<sub>*t*</sub> = *I*<sub>*t*</sub>/*Y*<sub>*t*</sub>, and the rate of depreciation *δ*.

Data
----

To use the code in this section you will first need to set your working directory. This can be done with

``` r
setwd("C:/.../your_working_directory")
```

Additionaly observations and variables are selected and created using the functions `select`, `filter`, and `mutate` from the tidyverse package. To install and activate the tidyverse use

``` r
install.packages("tidyverse")
library(tidyverse)
```

Data used to calibrate the neoclassical growth model is available in the Penn World Table. The data can be downloaded directly in R using the code

``` r
fileUrl <- "http://www.rug.nl/ggdc/docs/pwt90.xlsx"
download.file(fileUrl, destfile="pwt90.xlsx", method="curl")
```

Once you have downloaded the file pwt90.xlsx, open the file in Excel. Sheet two of pwt90.xlsx provides variable definitions. The third sheet contains the data used to calibrate the neoclassical growth mode. Save sheet three as pwt90.csv in your working directory.

After saving sheet three of pwt90.xlsx as a csv file, the data can be accessed in R with

``` r
GrowthData <- read.table("pwt90.csv", sep=",", header=TRUE)
```

and you can investigate the data with `head(GrowthData)`, `tail(GrowthData)`, and `summary(GrowthData)` or any other method you chose.

Now select out Paraguay, for which there are observations after the year 1970 with

``` r
GrowthData <- GrowthData %>% 
  filter(country == "Paraguay", year>=1970)
```

and select the variables used in the calibration with

``` r
GrowthData <- GrowthData %>% 
  select(country, year, pop, emp, avh, rgdpna, rkna, rtfpna, labsh, delta)
```

Calibration
-----------

Calibration of the neoclassical growth model presented above, requires the following two assumptions:

1.  Each variable grows at a constant rate ("Balanced Growth Path" assumption).
2.  The savings rate is constant.

To begin, I specify a cutoff year. I specify the year 1999, that is data before 1999 will be used to calibrate the model, so that I can compare the model with the observed data after 1999.

``` r
cutoffyear <- 1999
```

Next, I obtain the average depreciation rate and calculate the labor and capital shares from the data

``` r
avgdelta <- mean(GrowthData$delta[GrowthData$year<cutoffyear])
avglabshare <- mean(GrowthData$labsh[GrowthData$year<cutoffyear])
avgcapshare <- (1-avglabshare)
```

Similarly, I calculate the average growth rates for output *Y*<sub>*t*</sub> and *L*<sub>*t*</sub>, between 1970 and the cutoff year as

``` r
gy <- (log(GrowthData$rgdpna[GrowthData$year==(cutoffyear-1)])-log(GrowthData$rgdpna[GrowthData$year==1970]))/((cutoffyear-1)-1970)
gl <- (log(GrowthData$emp[GrowthData$year==(cutoffyear-1)])-log(GrowthData$emp[GrowthData$year==1970]))/((cutoffyear-1)-1970)
```

ga &lt;- gy-gl

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

``` r
summary(cars)
```

    ##      speed           dist       
    ##  Min.   : 4.0   Min.   :  2.00  
    ##  1st Qu.:12.0   1st Qu.: 26.00  
    ##  Median :15.0   Median : 36.00  
    ##  Mean   :15.4   Mean   : 42.98  
    ##  3rd Qu.:19.0   3rd Qu.: 56.00  
    ##  Max.   :25.0   Max.   :120.00

Including Plots
---------------

You can also embed plots, for example:

![](py_neoclassical_growth_files/figure-markdown_github-ascii_identifiers/pressure-1.png)

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
