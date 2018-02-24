---
title: "Craft Beer New Business Opportunities"
author: "YuMei & Nitin"
date: "February 16, 2018"
output:
  html_document:
    keep_md: true
---
# Introduction
Machine Churning consulting is a new startup, our mission is to provide business intelligence to convert data into information and knowledge.  Integrating large volumes of data with advanced analytics techniques, modern computer technology, and domain expertise within specific business sectors, our data analytic service looking inside the crystal ball of Big Data to predict the future of their enterprise.

Cobell Entertainment is one of the largest brewery and restaurant corporation in the US. They have been entertaining customers for over 50 years. Recent market down turn created difficult business environment for them, management is looking for innovative ideas on all front to create new revenue stream. They have contracted Machine Churning consulting on a beer and brewery data analytic project. 

There are two historical data files are provided as the analysis input. Please see Readme for details.

This document provides explanation of analysis process and result, along with recommendations.

# Import Data
We've decide to utilize R for this project. This segment simply takes raw data csv files into R studio, and making sure both files are imported properly. 

```r
knitr::opts_chunk$set(echo = TRUE)

dfbeers <- read.csv("E:\\University\\SMU\\Doing the Data Science\\Project1\\Beers.csv",header = TRUE)
head(dfbeers)
```

```
##                  Name Beer_ID   ABV IBU Brewery_id
## 1            Pub Beer    1436 0.050  NA        409
## 2         Devil's Cup    2265 0.066  NA        178
## 3 Rise of the Phoenix    2264 0.071  NA        178
## 4            Sinister    2263 0.090  NA        178
## 5       Sex and Candy    2262 0.075  NA        178
## 6        Black Exodus    2261 0.077  NA        178
##                            Style Ounces
## 1            American Pale Lager     12
## 2        American Pale Ale (APA)     12
## 3                   American IPA     12
## 4 American Double / Imperial IPA     12
## 5                   American IPA     12
## 6                  Oatmeal Stout     12
```

```r
dim(dfbeers)
```

```
## [1] 2410    7
```

```r
dfbreweries <- read.csv("E:\\University\\SMU\\Doing the Data Science\\Project1\\Breweries.csv",header = TRUE)
head(dfbreweries)
```

```
##   Brew_ID                      Name          City State
## 1       1        NorthGate Brewing    Minneapolis    MN
## 2       2 Against the Grain Brewery    Louisville    KY
## 3       3  Jack's Abby Craft Lagers    Framingham    MA
## 4       4 Mike Hess Brewing Company     San Diego    CA
## 5       5   Fort Point Beer Company San Francisco    CA
## 6       6     COAST Brewing Company    Charleston    SC
```

```r
dim(dfbreweries)
```

```
## [1] 558   4
```
Beers.csv has 2410 data samples and 7 variables. Breweries.csv has 558 data samples and 4 variables.

# Find out how many breweries each state 
This segment answers the question of how many breweries in each state?


```r
knitr::opts_chunk$set(echo = TRUE)

Breweriesperstate <- aggregate(dfbreweries, 
                               by = list(dfbreweries$State), 
                               FUN = length)
Breweriesperstate
```

```
##    Group.1 Brew_ID Name City State
## 1       AK       7    7    7     7
## 2       AL       3    3    3     3
## 3       AR       2    2    2     2
## 4       AZ      11   11   11    11
## 5       CA      39   39   39    39
## 6       CO      47   47   47    47
## 7       CT       8    8    8     8
## 8       DC       1    1    1     1
## 9       DE       2    2    2     2
## 10      FL      15   15   15    15
## 11      GA       7    7    7     7
## 12      HI       4    4    4     4
## 13      IA       5    5    5     5
## 14      ID       5    5    5     5
## 15      IL      18   18   18    18
## 16      IN      22   22   22    22
## 17      KS       3    3    3     3
## 18      KY       4    4    4     4
## 19      LA       5    5    5     5
## 20      MA      23   23   23    23
## 21      MD       7    7    7     7
## 22      ME       9    9    9     9
## 23      MI      32   32   32    32
## 24      MN      12   12   12    12
## 25      MO       9    9    9     9
## 26      MS       2    2    2     2
## 27      MT       9    9    9     9
## 28      NC      19   19   19    19
## 29      ND       1    1    1     1
## 30      NE       5    5    5     5
## 31      NH       3    3    3     3
## 32      NJ       3    3    3     3
## 33      NM       4    4    4     4
## 34      NV       2    2    2     2
## 35      NY      16   16   16    16
## 36      OH      15   15   15    15
## 37      OK       6    6    6     6
## 38      OR      29   29   29    29
## 39      PA      25   25   25    25
## 40      RI       5    5    5     5
## 41      SC       4    4    4     4
## 42      SD       1    1    1     1
## 43      TN       3    3    3     3
## 44      TX      28   28   28    28
## 45      UT       4    4    4     4
## 46      VA      16   16   16    16
## 47      VT      10   10   10    10
## 48      WA      23   23   23    23
## 49      WI      20   20   20    20
## 50      WV       1    1    1     1
## 51      WY       4    4    4     4
```

```r
write.csv(Breweriesperstate, "Breweriesperstate.csv")
```
Please see table above, it is a alphabetical list of states with last column show number of breweries in that state. Colorado is the winner with 47 breweries, followed by California with 39 brewies.

# Merge Beers and Breweries table
For convinence of rest of anlaysis, we merge the two csv files together. Notice the common variable breweries ID, but the label in both files is different. We have to align both name as "Brew_ID" before we can merge the files. The merged file or data frame is merged.


```r
knitr::opts_chunk$set(echo = TRUE)

colnames(dfbeers)[colnames(dfbeers)=="Brewery_id"] <- "Brew_ID"
colnames(dfbeers)[colnames(dfbeers)=="Name"] <- "Beer_Name"
colnames(dfbreweries)[colnames(dfbreweries)=="Name"] <- "Brew_Name"

merged<-merge(dfbeers,dfbreweries,by=c("Brew_ID"),all=FALSE)
head(merged)
```

```
##   Brew_ID     Beer_Name Beer_ID   ABV IBU
## 1       1  Get Together    2692 0.045  50
## 2       1 Maggie's Leap    2691 0.049  26
## 3       1    Wall's End    2690 0.048  19
## 4       1       Pumpion    2689 0.060  38
## 5       1    Stronghold    2688 0.060  25
## 6       1   Parapet ESB    2687 0.056  47
##                                 Style Ounces          Brew_Name
## 1                        American IPA     16 NorthGate Brewing 
## 2                  Milk / Sweet Stout     16 NorthGate Brewing 
## 3                   English Brown Ale     16 NorthGate Brewing 
## 4                         Pumpkin Ale     16 NorthGate Brewing 
## 5                     American Porter     16 NorthGate Brewing 
## 6 Extra Special / Strong Bitter (ESB)     16 NorthGate Brewing 
##          City State
## 1 Minneapolis    MN
## 2 Minneapolis    MN
## 3 Minneapolis    MN
## 4 Minneapolis    MN
## 5 Minneapolis    MN
## 6 Minneapolis    MN
```

```r
tail(merged)
```

```
##      Brew_ID                 Beer_Name Beer_ID   ABV IBU
## 2405     556             Pilsner Ukiah      98 0.055  NA
## 2406     557  Heinnieweisse Weissebier      52 0.049  NA
## 2407     557           Snapperhead IPA      51 0.068  NA
## 2408     557         Moo Thunder Stout      50 0.049  NA
## 2409     557         Porkslap Pale Ale      49 0.043  NA
## 2410     558 Urban Wilderness Pale Ale      30 0.049  NA
##                        Style Ounces                     Brew_Name
## 2405         German Pilsener     12         Ukiah Brewing Company
## 2406              Hefeweizen     12       Butternuts Beer and Ale
## 2407            American IPA     12       Butternuts Beer and Ale
## 2408      Milk / Sweet Stout     12       Butternuts Beer and Ale
## 2409 American Pale Ale (APA)     12       Butternuts Beer and Ale
## 2410        English Pale Ale     12 Sleeping Lady Brewing Company
##               City State
## 2405         Ukiah    CA
## 2406 Garrattsville    NY
## 2407 Garrattsville    NY
## 2408 Garrattsville    NY
## 2409 Garrattsville    NY
## 2410     Anchorage    AK
```

```r
dim(merged)
```

```
## [1] 2410   10
```
Please see above display for first 6 and last 6 row of merged data frame, along with merged file dimension check. This checking ensures we have merged file successfully.

# Report number of NAs in each merged table column
It is important to understand if the data is clean or not, for example if any NA exist. if it does, we can apply approperiate R options to avoid mistake or skewed analytic result.


```r
knitr::opts_chunk$set(echo = TRUE)

NApercolumn<-colSums(is.na(merged))
NApercolumn
```

```
##   Brew_ID Beer_Name   Beer_ID       ABV       IBU     Style    Ounces 
##         0         0         0        62      1005         0         0 
## Brew_Name      City     State 
##         0         0         0
```

```r
write.csv(NApercolumn, "NApercolumn.csv")
```
In deed, there are some NAs in our data frame. Please see table above, details each variable in merged data frame NA quantities. 

# Median alcohol content and international bitterness unit for each state
Here we take a look into median value of alcolhol content and international bitterness per state. 

```r
knitr::opts_chunk$set(echo = TRUE)

medianABVperstate1 = aggregate(data=merged, ABV ~ State, FUN=median)
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
medianABVperstate<-arrange(medianABVperstate1,desc(ABV))
medianABVperstate
```

```
##    State    ABV
## 1     DC 0.0625
## 2     KY 0.0625
## 3     MI 0.0620
## 4     NM 0.0620
## 5     WV 0.0620
## 6     CO 0.0605
## 7     AL 0.0600
## 8     CT 0.0600
## 9     NV 0.0600
## 10    OK 0.0600
## 11    SD 0.0600
## 12    CA 0.0580
## 13    IL 0.0580
## 14    IN 0.0580
## 15    MD 0.0580
## 16    MS 0.0580
## 17    OH 0.0580
## 18    FL 0.0570
## 19    NC 0.0570
## 20    PA 0.0570
## 21    TN 0.0570
## 22    ID 0.0565
## 23    VA 0.0565
## 24    AK 0.0560
## 25    MN 0.0560
## 26    NE 0.0560
## 27    OR 0.0560
## 28    IA 0.0555
## 29    WA 0.0555
## 30    AZ 0.0550
## 31    DE 0.0550
## 32    GA 0.0550
## 33    MT 0.0550
## 34    NH 0.0550
## 35    NY 0.0550
## 36    RI 0.0550
## 37    SC 0.0550
## 38    TX 0.0550
## 39    VT 0.0550
## 40    HI 0.0540
## 41    MA 0.0540
## 42    AR 0.0520
## 43    LA 0.0520
## 44    MO 0.0520
## 45    WI 0.0520
## 46    ME 0.0510
## 47    KS 0.0500
## 48    ND 0.0500
## 49    WY 0.0500
## 50    NJ 0.0460
## 51    UT 0.0400
```

```r
write.csv(medianABVperstate, "medianABVperstate.csv")

medianIBUperstate1 = aggregate(data=merged, IBU ~ State, FUN=median)
medianIBUperstate<-arrange(medianIBUperstate1,desc(IBU))
medianIBUperstate
```

```
##    State  IBU
## 1     ME 61.0
## 2     WV 57.5
## 3     FL 55.0
## 4     GA 55.0
## 5     DE 52.0
## 6     NM 51.0
## 7     NH 48.5
## 8     DC 47.5
## 9     NY 47.0
## 10    AK 46.0
## 11    MS 45.0
## 12    MN 44.5
## 13    AL 43.0
## 14    CA 42.0
## 15    VA 42.0
## 16    NV 41.0
## 17    CO 40.0
## 18    MT 40.0
## 19    OH 40.0
## 20    OR 40.0
## 21    AR 39.0
## 22    ID 39.0
## 23    WA 38.0
## 24    TN 37.0
## 25    MA 35.0
## 26    MI 35.0
## 27    NE 35.0
## 28    OK 35.0
## 29    NJ 34.5
## 30    UT 34.0
## 31    NC 33.5
## 32    IN 33.0
## 33    TX 33.0
## 34    ND 32.0
## 35    KY 31.5
## 36    LA 31.5
## 37    IL 30.0
## 38    PA 30.0
## 39    SC 30.0
## 40    VT 30.0
## 41    CT 29.0
## 42    MD 29.0
## 43    IA 26.0
## 44    MO 24.0
## 45    RI 24.0
## 46    HI 22.5
## 47    WY 21.0
## 48    AZ 20.5
## 49    KS 20.0
## 50    WI 19.0
```

```r
write.csv(medianIBUperstate, "medianIBUperstate.csv")
```
Please see above two tables for IBU and ABV per state median.

# Making bar plot for median IBU and AVB
Creating bar chart for better visual understanding of per state IBU and ABV median. 

```r
knitr::opts_chunk$set(echo = TRUE)

barplot(medianIBUperstate$IBU, main="Median IBU per State", xlab="states",ylab="median IBU",las=2, col=rainbow(20), names.arg=medianIBUperstate$State, horiz=FALSE,cex.axis=0.5, cex.names=0.5)
```

![](DDSProject1MachineChurningv2_files/figure-html/plot1-1.png)<!-- -->

```r
barplot(medianABVperstate$ABV, main="Median ABV per State", xlab="states",ylab="median ABV", las=2, col=rainbow(20), names.arg=medianABVperstate$State, horiz=FALSE,cex.axis=0.5, cex.names=0.5)
```

![](DDSProject1MachineChurningv2_files/figure-html/plot1-2.png)<!-- -->
Please see the two bar chart above, look like the alcolhol content per state median does not vary much, this is likely due to the regulation in place. The bitterness does vary as much as double. Colorado is the winner for alcohol content median, ABV(median)=0.128.

# Find the max ABV and IBU
We sort the merged data frame by IBU and ABV to find the winner.

```r
knitr::opts_chunk$set(echo = TRUE)

library(dplyr)
MergedsortABV<-arrange(merged %>% filter(ABV>0.1), desc(ABV))
head(MergedsortABV,n=1)
```

```
##   Brew_ID                                            Beer_Name Beer_ID
## 1      52 Lee Hill Series Vol. 5 - Belgian Style Quadrupel Ale    2565
##     ABV IBU            Style Ounces               Brew_Name    City State
## 1 0.128  NA Quadrupel (Quad)   19.2 Upslope Brewing Company Boulder    CO
```

```r
write.csv(MergedsortABV, "MergedsortABV.csv")

MergedsortIBU<-arrange(merged %>% filter(IBU>120), desc(IBU))
head(MergedsortIBU,n=1)
```

```
##   Brew_ID                 Beer_Name Beer_ID   ABV IBU
## 1     375 Bitter Bitch Imperial IPA     980 0.082 138
##                            Style Ounces               Brew_Name    City
## 1 American Double / Imperial IPA     12 Astoria Brewing Company Astoria
##   State
## 1    OR
```

```r
write.csv(MergedsortIBU, "MergedsortIBU.csv")
```
Colorado is the winner for alcohol content, ABV=0.128. 
Oregon is the winner for bitterness, IBU=138!

# Statistics for ABV
We now zoom into statistics of ABV.

```r
knitr::opts_chunk$set(echo = TRUE)

summary(merged$ABV)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
## 0.00100 0.05000 0.05600 0.05977 0.06700 0.12800      62
```

```r
sd(merged$ABV,na.rm=TRUE)
```

```
## [1] 0.01354173
```
We have ABV(mean)=0.06, ABV(median)=0.056, ABV(sd)=0.014, ABV(min)=0.001, ABV(max)=0.128. 

# Plot IBU and AVB relationship
Create scatter plot to see if any relationship we can draw between bitterness and alcolhol content.

```r
knitr::opts_chunk$set(echo = TRUE)

attach(merged)
plot(IBU, ABV, main="Scatterplot IBU vs. ABV",  	xlab="International Bitterness Units of the beer", ylab="Alcohol by volume of the beer", pch=19)
abline(lm(ABV~IBU), col="red") # regression line (y~x) 
```

![](DDSProject1MachineChurningv2_files/figure-html/plot2-1.png)<!-- -->
As we can visually observe from the scatter plot and the linear regression line, there is a visible relationship between IBU and ABV. Although the regression line is not steep, but does show as bitterness increase, there is visible increase on alcohol content as well. 

Of course, from ecademic stand point, the analysis was done on observation data provided. Given we have not validated how the data is collected and its validaty, we can only say that the result applies to data input population.

From practical stand point, we assume input data are valid from client and it does represent overall market situation, it is a good random collections of data sample. Therefore, analysis result is valid. 

We will feedback to Cobell Entertainment with this document along with a slide deck to summarize our findings, and provide some recommendations. 

