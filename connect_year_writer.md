---
title: "Connecting scene direction to year and writer"
author: "Julia Silge"
date: '2017-06-17'
output: html_document
---



## Reading in the data


```
## # A tibble: 1,201,303 x 4
##    scriptID  line word1    word2
##       <int> <chr> <chr>    <chr>
##  1     1498    69    he    comes
##  2     1498   103    he   gently
##  3     1498   104    he    picks
##  4     1498   109    he    faces
##  5     1498   179    he  machine
##  6     1498   184    he dictator
##  7     1498   254    he       is
##  8     1498   257    he   labels
##  9     1498   257    he    makes
## 10     1498   258    he  brushes
## # ... with 1,201,293 more rows
```

## Joining the bigrams to other metadata about the scripts





## Differences by gender of writer

We can use the odds ratio to find words coming after "she" that are more likely to be used by female writers/directors/etc and those more likely to be used by male writers/directors/etc. Movies can have both, can have multiple writers and so forth, but the following analysis accounts for that.

![plot of chunk gender_ratio](figure/gender_ratio-1.png)

What are some words after "she" that have about the same likelihood to come from writers/directors/etc who are men and women?


```
## # A tibble: 363 x 6
##         word2 total       female         male     ratio     logratio
##         <chr> <int>        <dbl>        <dbl>     <dbl>        <dbl>
##  1      still   192 0.0013449170 0.0013439956 1.0006856  0.000988722
##  2        and   435 0.0035893754 0.0035954237 0.9983178 -0.002428952
##  3 approaches   278 0.0020833028 0.0020879692 0.9977651 -0.003227846
##  4     drives   169 0.0014621211 0.0014583496 1.0025862  0.003726210
##  5      tries   833 0.0096048757 0.0095734462 1.0032830  0.004728590
##  6    thought    59 0.0003691929 0.0003679507 1.0033759  0.004862147
##  7     brings   184 0.0013478471 0.0013527403 0.9963827 -0.005228069
##  8    listens   227 0.0019074967 0.0018982761 1.0048573  0.006990707
##  9        can   939 0.0135370731 0.0134702263 1.0049626  0.007141761
## 10     didn't   114 0.0008673103 0.0008717809 0.9948719 -0.007417391
## # ... with 353 more rows
```

Words like "still", "and", "drives", "tries", "brings", "listens"...

## Change by year

We can use logistic regression modeling to find the words paired with "she" that have changed the fastest with time, either in a positive or negative direction.




![plot of chunk contractions](figure/contractions-1.png)


![plot of chunk increasing](figure/increasing-1.png)


![plot of chunk decreasing](figure/decreasing-1.png)

I'm picturing an interactive version of this chart where the user can switch from increasing to decreasing, hover over the lines to see how they are changing. Maybe decide how many to see?

Let's check the words that change for "he", to compare. (This probably won't go into the final essay.)


```
## # A tibble: 10 x 6
##        word2  term   estimate   std.error statistic       p.value
##        <chr> <chr>      <dbl>       <dbl>     <dbl>         <dbl>
##  1   marches  year 0.04006890 0.008666637  4.623351  3.775903e-06
##  2 nervously  year 0.04011326 0.010431548  3.845380  1.203658e-04
##  3   answers  year 0.04070616 0.004913550  8.284471  1.186350e-16
##  4   inhales  year 0.05236354 0.012375951  4.231072  2.325803e-05
##  5     won’t  year 0.11525625 0.016620935  6.934403  4.079415e-12
##  6    didn’t  year 0.14564995 0.012329963 11.812684  3.356735e-32
##  7     isn’t  year 0.16544851 0.016061585 10.300883  6.981766e-25
##  8   doesn’t  year 0.16658435 0.004937153 33.740975 1.450089e-249
##  9     can’t  year 0.16802397 0.005833018 28.805663 1.821765e-182
## 10    hasn’t  year 0.16816630 0.014260869 11.792150  4.284630e-32
```

```
## # A tibble: 10 x 6
##       word2  term    estimate   std.error statistic      p.value
##       <chr> <chr>       <dbl>       <dbl>     <dbl>        <dbl>
##  1    don't  year -0.06683106 0.010610917 -6.298330 3.008693e-10
##  2   gropes  year -0.06194826 0.009751585 -6.352635 2.116578e-10
##  3    edges  year -0.05115763 0.010629973 -4.812583 1.489919e-06
##  4 clambers  year -0.04562880 0.010709564 -4.260566 2.039099e-05
##  5  mumbles  year -0.04256591 0.010374608 -4.102893 4.080160e-05
##  6   whirls  year -0.04131920 0.006288846 -6.570235 5.023578e-11
##  7      too  year -0.04106888 0.005377260 -7.637510 2.214637e-14
##  8    ain't  year -0.04085533 0.009688645 -4.216826 2.477650e-05
##  9   heaves  year -0.04080685 0.008563592 -4.765156 1.887078e-06
## 10  swivels  year -0.03745774 0.011197175 -3.345285 8.219801e-04
```

So the change in contractions looks like it is more about how set directions are changing as a whole, not a difference for how women are portrayed. For the final modeling by year, I'll plan to filter contractions out.

Also, HA -- women are exhaling and men are inhaling.
