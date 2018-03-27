R Notebook
================

In this File, we are dealing with the *Singleton* Time points and the
Reporting of Matching and Non-Matching Subject IDs from the *BMI* and
*Media* data sets.

#### R Libraries

This code block has all the needed R libraries

``` r
#For the dta raw files
library(foreign)
#For importing different types of data set without specification
library(rio)
#For processing long form data
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
#GTools library for ordering numeric variables
library(gtools)
```

#### Uploading Raw Singleton data

In this code chunk, we are uploading the excluded singleton data from
BMI and Media to see if there are any matches between them.

``` r
#The first two files are JUST singleton subject IDs
bmi_singleton <- import("../../data/intermediate/bmisubjects_withonedatap.csv")
media_singleton <- import("../../data/intermediate/mediasubjects_withonedatap.csv")
#The second two files include single subject ID and corresponding columns
bmi_singleton_data <- import("../../data/processing/bmi_subject_singleton.csv")
media_singleton_data <- import("../../data/processing/media_subject_singleton.csv")
```

#### Matching the Singletons

``` r
#Checking how many command IDs are between Singleton BMI and Singleton Media

common_ID <- intersect(bmi_singleton$ID_, media_singleton$ID_)
print(common_ID)
```

    ## [1] 626

``` r
#A single singleton subject ID of 626 matches!
```

*NOTE* The subjectID 626 has 0 months in BMI and ~36 months in MEDIA.
Otherwise, all other subjectID has only 1 single value and has no
corresponding matches across both files *NOTE*

#### Exploring the Singleton Data Files

``` r
head(bmi_singleton_data)
```

    ## [1] V1     V1     ID_    AgeMos zBMI  
    ## <0 rows> (or 0-length row.names)

``` r
#View(media_singleton_data)
```

#### Uploading Raw Data to explore Subject ID matches and mismatches

``` r
#processing bmi data
p_bmi <- import("../../data/processing/bmi.csv")
p_media <- import("../../data/processing/media.csv")
```

#### Total number of matches (and match IDs) including Singletons

Figuring out the sharedIDs between two data tables and saving them

``` r
total_matches <- as.data.frame(intersect(p_bmi$ID_, p_media$ID_))
colnames(total_matches) <- c("matchIDs")
write.csv(total_matches, "../../data/final/totalmatches.csv")
```

Printing out summary
numbers

``` r
print(length(total_matches$matchIDs)) #537 matches
```

    ## [1] 537

#### All the subjectIDs in BMI that does NOT match with media

``` r
#Using the dplyr set diff function to find the IDs in the first parameter that does NOT exist in the second parameter.
bmi_diff <- as.data.frame(setdiff(p_bmi$ID_, p_media$ID_))
colnames(bmi_diff) <- c("bmi_nonmatches")
write.csv(bmi_diff,"../../data/final/bmi_nommatches.csv")
```

Printing out summary
numbers

``` r
print(length(bmi_diff$bmi_nonmatches)) #130 nonmatches
```

    ## [1] 130

#### All the subjectIDs in MEDIA that does NOT match with BMI

``` r
#Using the dplyr set diff function to find the IDs in the first parameter that does NOT exist in the second parameter.
media_diff <- as.data.frame(setdiff(p_media$ID_, p_bmi$ID_))
colnames(media_diff) <- c("media_nonmatches")
write.csv(media_diff,"../../data/final/media_nommatches.csv")
```

Printing out summary numbers

``` r
print(length(media_diff$bmi_nonmatches)) #0 nonmatches
```

    ## [1] 0

In conclusion, everything in *Media* matches everything in BMI until
Singletons have been removed. To doubly check: