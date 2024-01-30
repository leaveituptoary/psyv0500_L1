Lab 1
================

## PSY V0500 Statistical Methods in Psychology

## Kevin R Foster, Colin Powell School, the City College of New York, CUNY

We want to consider some correlates of ADDEPEV3, whether a person has
been diagnosed with a depressive disorder. We will look at simple
statistics and graphs.

Here is a couple simple crosstabs with sex at birth

``` r
xtabs(~ brfss22$ADDEPEV3 + brfss22$SEXVAR)
```

    ##                                        brfss22$SEXVAR
    ## brfss22$ADDEPEV3                          Male Female
    ##   Yes ever told had depressive disorder  31096  60314
    ##   No                                    176718 174192

``` r
prop.table(table(brfss22$ADDEPEV3, brfss22$SEXVAR))
```

    ##                                        
    ##                                               Male     Female
    ##   Yes ever told had depressive disorder 0.07030204 0.13635829
    ##   No                                    0.39952523 0.39381443

We want to make some graphs to show this.

First issue: BRFSS is large! It’s too big to easily work with so we’ll
randomly use just 10% of the observations. Here’s some code to do that

``` r
NN <- length(brfss22$ADDEPEV3)
set.seed(12345)
restrict_1 <- (runif(NN) < 0.1) # use just 10% 
summary(restrict_1)
```

    ##    Mode   FALSE    TRUE 
    ## logical  400624   44508

``` r
brfss_small <- subset(brfss22, restrict_1)
```

Let me walk through each line. The first just creates a new variable
that is the number of observations in the data set, NN.

The next is a bit odd, setting a seed value. The line after, with
`runif`, creates a series of random numbers. These numbers are not
actually random but pseudo-random. Truly random numbers would be
uncorrelated with anything but pseudo-random are the result of a fixed
rule that look random (or random enough). If we set a seed value for the
random number generator, that guarantees we’ll always get the same
results each time. Which is very useful here! You should get the same
set of `random` numbers as me.

The `runif` command generates psuedo-random numbers from a uniform
distribution in the (0,1) interval. Since they’re uniform, we’d expect
10% to be below 0.1 – which is what the rest of the code does. It
creates a variable that is the same size as BRFSS that has only values
of True or False and where at random 10% are true. We use that to select
a smaller group of observations, which we’ll use for graphing. Depending
on your setup you might need to use even a smaller subset, maybe 5% – so
change to `< 0.05` or whatever.

For now we will use the small version for both graphs and calculating
statistics. It would be better to get stats from the whole big version –
you can go back to that for homework. But for now during the lab we’ll
just use the small version.

Let’s go through a few examples.

``` r
ggplot(brfss_small, aes(x = SEXVAR, fill = ADDEPEV3)) + geom_bar()
```

![](lab1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- --> The `aes`
aesthetics argument should be clear enough: the x-axis has sex and the
bars are filled depending on diagnosis.

You can swap those categories,

``` r
ggplot(brfss_small, aes(x = ADDEPEV3, fill = SEXVAR)) + geom_bar()
```

![](lab1_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

Those give the counts of people in each, but it might be more useful to
consider them as proportions of the total.

``` r
ggplot(brfss_small, aes(x = SEXVAR, fill = ADDEPEV3)) + geom_bar(position = "fill")
```

![](lab1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
ggplot(brfss_small, aes(x = ADDEPEV3, fill = SEXVAR)) + geom_bar(position = "fill")
```

![](lab1_files/figure-gfm/unnamed-chunk-6-2.png)<!-- -->

Those correspond to different statements about proportions. What
fraction of women have a diagnosis of depression? Or what fraction of
people with a diagnosis are women? The L3 videos will go deeper but you
can easily calculate those fractions.

Want to play with colors? Viridis package displays are meant to improve
readability for people with various sorts of color blindness as well as
being more readable in black-and-white if printed.

``` r
ggplot(brfss_small, aes(x = SEXVAR, fill = ADDEPEV3)) + geom_bar(position = "fill") + scale_fill_viridis_d()
```

![](lab1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
ggplot(brfss_small, aes(x = ADDEPEV3, fill = SEXVAR)) + geom_bar(position = "fill") + scale_fill_viridis_d(option="plasma")
```

![](lab1_files/figure-gfm/unnamed-chunk-7-2.png)<!-- -->

\##other options

Suppose you want a three-way comparison? For instance you wanted to also
look at exercise, with the variable `EXERANY2`. Three-dimensional graphs
are tough on a 2-d screen. So you’s probably want to subset, for
instance splitting the group into m/f then doing the 2-d graphs.
Something like this,

``` r
brfss_small_m <- subset(brfss_small,brfss_small$SEXVAR == "Male")
brfss_small_f <- subset(brfss_small,brfss_small$SEXVAR == "Female")
```

Then do the previous graphs, for instance,

``` r
ggplot(brfss_small_f, aes(x = ADDEPEV3, fill = EXERANY2)) + geom_bar(position = "fill") + scale_fill_viridis_d(option="rocket")
```

![](lab1_files/figure-gfm/unnamed-chunk-9-1.png)<!-- --> With any of
these graphs you’ll notice residual issues: is it a problem that the
“NA” group are represented as large as the others, even though there are
so many fewer? Would it be better to drop them? (It depends, try it and
see!)

Depending on what other correlates you want to mess with, let me offer
some other options.

The graph types must change depending on whether the data are categories
or numbers. For instance you might be frustrated that you’d like to
graph Age as a number but the data are binned into categories such as
“25 to 29”. Depending on circumstance, it might be ok to replace the bin
with its midpoint.

``` r
brfss22$Age_midpt <- fct_recode(brfss22$X_AGEG5YR, "21" = "Age 18 to 24",
                                "27" = "Age 25 to 29", "32" = "Age 30 to 34",
                                "37" = "Age 35 to 39", "42" = "Age 40 to 44",
                                "47" = "Age 45 to 49", "52" = "Age 50 to 54",
                                "57" = "Age 55 to 59", "62" = "Age 60 to 64",
                                "67" = "Age 65 to 69", "72" = "Age 70 to 74",
                                "77" = "Age 75 to 79", "82" = "Age 80 or older",
                                NULL = "Dont know/Refused/Missing")

summary(brfss22$Age_midpt)
```

    ##    21    27    32    37    42    47    52    57    62    67    72    77    82 
    ## 26941 21990 25807 28526 29942 28531 33644 36821 44511 47099 43472 32518 36251 
    ##  NA's 
    ##  9079

``` r
brfss22$Age_midpt <- as.numeric(levels(brfss22$Age_midpt))[brfss22$Age_midpt]
summary(brfss22$Age_midpt) # note the diff!
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
    ##   21.00   42.00   57.00   55.06   72.00   82.00    9079

If you do this and want to use this variable in a graph, note that I
created it in the original “parent” dataset so you’d need to re-run the
code to create the small dataset from it.

Some graphs don’t want NA values so you would have to pre-filter the
data to remove the NAs. For this data you have to be careful what you
filter though! If you drop every line with an NA value then you’re left
with no data at all. In other cases the NA values might have information
so dropping them might cause some skew. But you have to play around to
see.

``` r
# note this drops obs with NA value in just that variable not all the other variables
d_sleeptime <- brfss_small %>% drop_na(SLEPTIM1) 
# this drops all with either of those 2 vals missing
d_sleeptime_age <- brfss_small %>% drop_na(SLEPTIM1,Age_midpt) 
```

From there you can do lots of graphs,

``` r
ggplot(d_sleeptime, aes(x = SLEPTIM1) ) + geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](lab1_files/figure-gfm/unnamed-chunk-13-1.png)<!-- --> It gives a
grumpy message, “Pick better value” for bins.

``` r
ggplot(d_sleeptime, aes(x = SLEPTIM1) ) + geom_histogram(binwidth = 1)
```

![](lab1_files/figure-gfm/unnamed-chunk-14-1.png)<!-- --> You might want
to try different numbers for `bindwidth`.

Let me show a bit of shortcut to avoid re-typing quite so much. We can
use R to assign a variable for a part of the ggplot then it’s easier to
re-use.

``` r
sleep_gg <- ggplot(d_sleeptime, aes(x = SLEPTIM1) )
sleep_gg + geom_histogram(binwidth = 2)
```

![](lab1_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

``` r
sleep_gg + geom_histogram(binwidth = 3)
```

![](lab1_files/figure-gfm/unnamed-chunk-15-2.png)<!-- -->

``` r
sleep_gg + geom_histogram(binwidth = 4)
```

![](lab1_files/figure-gfm/unnamed-chunk-15-3.png)<!-- -->

``` r
sleep_gg + geom_histogram(bins = 10)
```

![](lab1_files/figure-gfm/unnamed-chunk-15-4.png)<!-- -->

You can use lines instead of bins,

``` r
sleep_gg + geom_freqpoly(binwidth = 2)
```

![](lab1_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

or other tricks,

``` r
sleep_gg + geom_histogram(color = "blue", fill="yellow")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](lab1_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

``` r
sleep_gg + geom_histogram(binwidth = 1) + geom_vline(aes(xintercept = mean(SLEPTIM1)), linetype="dashed", linewidth=1)
```

![](lab1_files/figure-gfm/unnamed-chunk-17-2.png)<!-- -->

Here are more,

``` r
sleep_age_gg <- ggplot(d_sleeptime_age, aes(SLEPTIM1, Age_midpt)) 
sleep_age_gg + geom_point(alpha = 0.05)
```

![](lab1_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

``` r
sleep_age_gg + geom_point(alpha = 0.05) + geom_density2d()
```

![](lab1_files/figure-gfm/unnamed-chunk-18-2.png)<!-- -->

``` r
sleep_age_gg +   stat_density_2d(aes(fill = stat(level)), geom = 'polygon') +
  scale_fill_viridis_c(name = "density") +
  geom_point(shape = '.')
```

    ## Warning: `stat(level)` was deprecated in ggplot2 3.4.0.
    ## ℹ Please use `after_stat(level)` instead.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
    ## generated.

![](lab1_files/figure-gfm/unnamed-chunk-18-3.png)<!-- -->

``` r
sleep_age_gg + stat_density_2d(aes(fill = after_stat(density)), geom = 'raster', contour = FALSE) +       
  scale_fill_viridis_c() +
  coord_cartesian(expand = FALSE) +
  geom_point(shape = '.', col = 'white')
```

![](lab1_files/figure-gfm/unnamed-chunk-18-4.png)<!-- -->
