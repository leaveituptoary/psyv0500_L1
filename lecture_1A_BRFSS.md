R Basics for Lecture 1 A
================

## PSY V0500 Statistical Methods in Psychology

## Kevin R Foster, Colin Powell School, the City College of New York, CUNY

## Variable Coding

Some of the BRFSS variables here have a natural interpretation, for
instance `SLEPTIM1` is measured in hours. Actually even this has a bit
of a twist, look at the histogram.

``` r
hist(SLEPTIM1[(SLEPTIM1 >5) & (SLEPTIM1 < 9)])
```

![](lecture_1A_BRFSS_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

These are clearly rounded to the nearest hour. So although the
underlying variable might be a real number, it’s coded as an integer.

We also have to be careful about how to interpret the NA responses.
Those could mean any of a variety of things: perhaps the person asking
the questions skipped a part (accidentally or intentionally), perhaps
the responder gave a long rambling answer that couldn’t fit a coding
(for example, asked about whether they got a Covid shot, the response is
a diatribe about the government telling people what to do), or perhaps
they just refused to answer. In many of these cases the person just
wasn’t asked.

For instance, compare these

``` r
summary(SEXVAR)
```

    ##   Male Female 
    ## 209239 235893

``` r
summary(BIRTHSEX)
```

    ##   male sex at birth female sex at birth                NA's 
    ##               37441               41456              366235

If you didn’t check the number of NA values in BIRTHSEX, you would have
a very odd view. Most people aren’t asked a separate question about sex
assigned at birth; this is a complicated topic. The data presented here
is an imperfect description of reality, sometimes the result of a
tension between the people creating the survey and the people answering.
Reality is more complicated than simple answers, even in a well-designed
survey.

Some variables are a Likert scale, such as

``` r
summary(GENHLTH)
```

    ##            Excellent            Very good                 Good 
    ##                71878               148444               143598 
    ##                 Fair                 Poor Dont know - Not Sure 
    ##                60273                19741                  810 
    ##              Refused                 NA's 
    ##                  385                    3

This is basically asking people to rate their health on a scale from 1
to 5. Sometimes it’s useful to treat those as numbers that we can do
math with; sometimes not. Note that here the middle value is labeled as
“Good”. Other Likert scales might ask people to rate from 1 to 10 or
from 1 to 7.

Many of the BRFSS variables are factors and the R language provides lots
of help with them. Basically a factor lumps together a bunch of 0/1
answers. Mapping logical operations into math is a great help.

The factor, ‘X_HISPANC’, is a single y/n or 0/1 answer: is the
respondent Hispanic. But then EDUCA is a whole bunch of y/n answers: is
the person’s highest completed educational level only kindergarten or
never had school; is the person’s highest completed educational level
grade 1 to 9; did they get through some high school; did they end after
getting high school diploma; etc. When you ask for a summary of EDUCA,

``` r
summary(EDUCA)
```

    ##                   Never attended school or only kindergarten 
    ##                                                          676 
    ##                              Grades 1 through 8 (Elementary) 
    ##                                                         8381 
    ##                       Grades 9 through 11 (Some high school) 
    ##                                                        16954 
    ##                       Grade 12 or GED (High school graduate) 
    ##                                                       108990 
    ## College 1 year to 3 years (Some college or technical school) 
    ##                                                       120252 
    ##                   College 4 years or more (College graduate) 
    ##                                                       187496 
    ##                                                      Refused 
    ##                                                         2378 
    ##                                                         NA's 
    ##                                                            5

R helpfully provides labels for each of those all together. For various
cases we might want to combine some of those together.

It’s important to remember that there are some cases where the factor
values are well ordered (as with highest educational qualification)
versus others such as X_STATE (the state the person lives in) where
there is not necessarily an ordering.

Factors are really useful, enough that different people have developed
packages specifically to manipulate factors.

### Packages

R depends crucially on “packages” - that’s the whole reason that the
open-source works so well. Some statistician invents a cool new
technique, then writes up the code in R and makes it available. If you
used a commercial program you’d have to wait a decade for them to update
it; in R it’s here now. Also if somebody hacks a nicer or easier way to
do stuff, they write it up. Packages are extensions for specific tasks
and you can tell R to install specific ones. Many people who use R don’t
need to create detailed maps but if you want that, there’s a package. If
you want to analyze genetic sequences, there’s a package.

Hadley Wickham wrote ‘forcats’ for categorical data (ie factors). It’s
part of the ‘tidyverse’ package.

So enter this into the Console,

    install.packages("tidyverse")
    install.packages("plyr")

then

``` r
library(plyr)
library(tidyverse)
```

Alt, from R-Studio, click “Tools” then “Install Packages…” and tell it
to install the package, “tidyverse”. That is nice if you want to see
some of the packages or if you don’t quite remember the name. Then the
next piece of code, library, tells the program that you want to use
commands from this package. You only need to install once, then just put
library() into your code and run that part.

### Factors

R will do the summary differently when it knows the variable is a
factor,

``` r
summary(X_AGEG5YR)
```

    ##              Age 18 to 24              Age 25 to 29              Age 30 to 34 
    ##                     26941                     21990                     25807 
    ##              Age 35 to 39              Age 40 to 44              Age 45 to 49 
    ##                     28526                     29942                     28531 
    ##              Age 50 to 54              Age 55 to 59              Age 60 to 64 
    ##                     33644                     36821                     44511 
    ##              Age 65 to 69              Age 70 to 74              Age 75 to 79 
    ##                     47099                     43472                     32518 
    ##           Age 80 or older Dont know/Refused/Missing 
    ##                     36251                      9079

``` r
summary(INCOME3)
```

    ##                  Household income less than $10,000 
    ##                                               10341 
    ##    Less than $15,000 ($10,000 to less than $15,000) 
    ##                                               11031 
    ##   Less than $20,000 ($15,000 to less than $20,000)  
    ##                                               14300 
    ##   Less than $25,000 ($20,000 to less than $25,000)  
    ##                                               20343 
    ##   Less than $35,000 ($25,000 to less than $35,000)  
    ##                                               42294 
    ##   Less than $50,000 ($35,000 to less than $50,000)  
    ##                                               46831 
    ##    Less than $75,000 ($50,000 to less than $75,000) 
    ##                                               59148 
    ##  Less than $100,000 ($75,000 to less than $100,000) 
    ##                                               48436 
    ## Less than $150,000 ($100,000 to less than $150,000) 
    ##                                               50330 
    ## Less than $200,000 ($150,000 to less than $200,000) 
    ##                                               22553 
    ##                                    $200,000 or more 
    ##                                               23478 
    ##                                  Dont know/Not sure 
    ##                                               36114 
    ##                                             Refused 
    ##                                               47001 
    ##                                                NA's 
    ##                                               12932

I know, we’d like if age or income were a regular number not a factor,
but that’s what survey provides, as a way of helping keep
confidentiality.

To find mean and standard deviation of sleeptime by income, you could
use something like this,

``` r
ddply(brfss22, .(INCOME3), summarize, mean = round(mean(SLEPTIM1, na.rm = TRUE), 2), sd = round(sd(SLEPTIM1, na.rm = TRUE), 2), n_obsv = length(is.na(SLEPTIM1) == FALSE) )
```

    ##                                                INCOME3 mean   sd n_obsv
    ## 1                   Household income less than $10,000 6.90 2.26  10341
    ## 2     Less than $15,000 ($10,000 to less than $15,000) 6.94 2.04  11031
    ## 3    Less than $20,000 ($15,000 to less than $20,000)  6.99 1.96  14300
    ## 4    Less than $25,000 ($20,000 to less than $25,000)  7.02 1.83  20343
    ## 5    Less than $35,000 ($25,000 to less than $35,000)  7.02 1.66  42294
    ## 6    Less than $50,000 ($35,000 to less than $50,000)  7.03 1.50  46831
    ## 7     Less than $75,000 ($50,000 to less than $75,000) 7.00 1.36  59148
    ## 8   Less than $100,000 ($75,000 to less than $100,000) 7.01 1.27  48436
    ## 9  Less than $150,000 ($100,000 to less than $150,000) 6.99 1.19  50330
    ## 10 Less than $200,000 ($150,000 to less than $200,000) 6.98 1.16  22553
    ## 11                                    $200,000 or more 7.03 1.17  23478
    ## 12                                  Dont know/Not sure 7.12 1.76  36114
    ## 13                                             Refused 7.11 1.42  47001
    ## 14                                                <NA> 6.99 1.63  12932

Although tapply would also work fine.

Here’s the 90th and 10th percentiles of sleeptime by income,

``` r
ddply(brfss22, .(INCOME3), summarize, sleep90th = quantile(SLEPTIM1,probs = 0.9, na.rm = TRUE), sleep10th = quantile(SLEPTIM1,probs = 0.1, na.rm = TRUE), n_obs = length(is.na(SLEPTIM1) == FALSE) )
```

    ##                                                INCOME3 sleep90th sleep10th
    ## 1                   Household income less than $10,000         9         4
    ## 2     Less than $15,000 ($10,000 to less than $15,000)         9         4
    ## 3    Less than $20,000 ($15,000 to less than $20,000)          9         5
    ## 4    Less than $25,000 ($20,000 to less than $25,000)          9         5
    ## 5    Less than $35,000 ($25,000 to less than $35,000)          9         5
    ## 6    Less than $50,000 ($35,000 to less than $50,000)          8         5
    ## 7     Less than $75,000 ($50,000 to less than $75,000)         8         6
    ## 8   Less than $100,000 ($75,000 to less than $100,000)         8         6
    ## 9  Less than $150,000 ($100,000 to less than $150,000)         8         6
    ## 10 Less than $200,000 ($150,000 to less than $200,000)         8         6
    ## 11                                    $200,000 or more         8         6
    ## 12                                  Dont know/Not sure         9         5
    ## 13                                             Refused         8         6
    ## 14                                                <NA>         8         5
    ##    n_obs
    ## 1  10341
    ## 2  11031
    ## 3  14300
    ## 4  20343
    ## 5  42294
    ## 6  46831
    ## 7  59148
    ## 8  48436
    ## 9  50330
    ## 10 22553
    ## 11 23478
    ## 12 36114
    ## 13 47001
    ## 14 12932

You could also use table (or crosstabs) for factors with fewer items,

``` r
table(GENHLTH,SEXVAR)
```

    ##                       SEXVAR
    ## GENHLTH                 Male Female
    ##   Excellent            36008  35870
    ##   Very good            69033  79411
    ##   Good                 67482  76116
    ##   Fair                 26974  33299
    ##   Poor                  9156  10585
    ##   Dont know - Not Sure   385    425
    ##   Refused                201    184

``` r
xtabs(~GENHLTH + SEXVAR)
```

    ##                       SEXVAR
    ## GENHLTH                 Male Female
    ##   Excellent            36008  35870
    ##   Very good            69033  79411
    ##   Good                 67482  76116
    ##   Fair                 26974  33299
    ##   Poor                  9156  10585
    ##   Dont know - Not Sure   385    425
    ##   Refused                201    184

Want proportions instead of counts?

``` r
prop.table(table(GENHLTH,SEXVAR))
```

    ##                       SEXVAR
    ## GENHLTH                        Male       Female
    ##   Excellent            0.0808934039 0.0805833814
    ##   Very good            0.1550853797 0.1783999694
    ##   Good                 0.1516009966 0.1709976209
    ##   Fair                 0.0605981637 0.0748075277
    ##   Poor                 0.0205693181 0.0237796234
    ##   Dont know - Not Sure 0.0008649178 0.0009547794
    ##   Refused              0.0004515545 0.0004133633

*Remember prop.table later when we do marginals.*

### Alt versions

In general, R is very flexible so there are often many different ways to
get the same answer. There are some people who love to debate which is
best. (Often, tradeoff between speed and intelligibility.) For now just
worry about learning at least one way. Later on you can go back and
refine your techniques.

Sometimes attaching a dataset makes things easier. But as you get more
advanced you might find it better to include the dataset name inside the
function. There are advantages and disadvantages each way and some of
the intro texts suggest one or the other.

If you do a lot of analysis on a particular subgroup, it might be
worthwhile to create a subset of that group, so that you don’t have to
always add on logical conditions. These two sets of expressions get the
same results:

``` r
mean(SLEPTIM1[(EDUCA == "College 4 years or more (College graduate)")], na.rm = TRUE)
```

    ## [1] 7.070843

``` r
# alternatively
restrict1 <- as.logical(EDUCA == "College 4 years or more (College graduate)")
dat_subset1 <- subset(brfss22, restrict1)

detach()
attach(dat_subset1)

mean(SLEPTIM1, na.rm = TRUE)
```

    ## [1] 7.070843

``` r
detach()
```

So you detach the original data frame and instead attach the restricted
version. Then any subsequent analysis would be just done on that subset.
Just remember that you’ve done this (again, this is a good reason to
save the commands in a program so you can look back) otherwise you’ll
wonder why you suddenly don’t have so many people in the sample!

Obviously for a single restriction that might not be worthwhile but
later you might have more complicated propositions.

Even better, though, is to avoid the `attach` and `detach` since those
can easily cause confusion. Instead, explicitly tell it which variable
in which dataset you’re thinking about, so `datasetname$variable`

``` r
mean(brfss22$SLEPTIM1, na.rm = TRUE)
```

    ## [1] 7.022983

``` r
mean(dat_subset1$SLEPTIM1, na.rm = TRUE)
```

    ## [1] 7.070843

## Why All These Details?

You might be tired and bored by these details, but note that there are
actually important choices to be made here, even in simply defining
variables. Take the fraught American category of “race”. This data has a
variable, X_PRACE2, showing how people chose to classify themselves, as
‘White,’ ‘Black,’ ‘Asian,’ or other.

In this case the Survey has chosen particular values while alternate
responses go into the category of “Other”.

There’s no “right” way to do it because there’s no science in this
peculiar-but-popular concept of “race”. People’s conceptions of
themselves are fuzzy and complicated; these measures are approximations.

The US government asks questions about people’s race and ethnicity.
These categories are social constructs, which is a fancy way of pointing
out that they are based on people’s own views of themselves (influenced
by how we think that other people think of us…). Currently the standard
classification asks people separately about their “race” and “ethnicity”
where people can pick labels from each category in any combination.

The “race” categories include: “White,” “Black,” “Asian,” and others.
These represent a recent history of popular attitudes in the US. Only in
the 2000 Census did they start to classify people in multiple races. If
you were to go back to historical US Censuses from more than a century
ago, you would find that the category “race” included separate entries
for races such as Irish and French. Ta-Nehisi Coates notes, “racism
invented race in America.” Throughout history, statistics have been used
to try to prove peoples’ prejudices.

Note that “Hispanic” is not “race” but rather ethnicity (includes
various other labels such as Spanish, Latino, etc.). So a respondent
could choose “Hispanic” and any race category – some choose “White,”
some choose “Black” or “Asian” or “Other”.

If you wanted to create a variable for those who report themselves as
Black and Hispanic, you’d use the expression (X_PRACE2 == “Black or
African American”) & (X_HISPANC == “yes Hispanic”); sometimes government
stats report for non-Hispanic whites so (X_PRACE2 == “White”) &
(X_HISPANC != “Hispanic”). You can create your own classifications
depending on what questions you’re investigating.

### Re-Coding complicated variables from initial data

If we want more combinations of variables then we create those. Usually
a statistical analysis spends a lot of time doing this sort of
housekeeping - dull but necessary. It has a variety of names: data
carpentry, data munging…

This dataset lumps together those who have a 4-year college degree with
those who have an advanced degree, while it differentiates those with
various steps less than high school. Other datasets might provide
different detail.

That’s the whole point of learning to do the data work for yourself: you
can see all of the little decisions that go into creating a conclusion.
Some conclusions might be fragile so a tiny decision about coding could
change everything; other conclusions are robust to deviations. You must
find out.

# De-bugging

Without a doubt, programming is tough. In R or with any other program,
it is frustrating and complicated and difficult to do it the first few
times. Some days it feels like a continuous battle just to do the
simplest thing! Keep going despite that, keep working on it.

Your study group will be very helpful of course.

Often a google search of the error message helps. If you’ve isolated the
error and read the help documentation on that command, then you’re on
your way to solving the problem on your own.

If you have troubles that you can’t solve, email me for help. But try to
narrow down your question: if you run 20 lines of code that produce an
error, is there a way to reproduce the error in just 5 lines? What if
you did the same command on much simpler data, would it still cause an
error? Sending emails like “I have a problem with errors” might be
cathartic but is not actually useful to anyone. If you email me with the
minimal code that recreates the error, along with the text of the error
and/or a screenshot, then that will help more.

## Do it

In the first lab we will start working on these questions. Begin by
running the code that I give here, just to see if you can replicate my
results. Then start asking more questions about the data - there is so
much info there! Have some fun.
