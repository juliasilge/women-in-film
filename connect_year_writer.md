---
title: "Connecting scene direction to year and writer"
author: "Julia Silge"
date: '2017-06-17'
output: html_document
---



## Reading in the data


```r
library(tidyverse)
library(tidytext)
library(stringr)

all_tsvs <- paste0("setDirections/", list.files(path = "setDirections/", pattern = ".tsv$"))

pronoun_bigrams <- all_tsvs %>%
    map_df(~data_frame(lines = read_lines(.x)) %>%
               filter(str_detect(lines, "^[0-9]")) %>%
               separate(lines, c("line", "text"), sep = "\t") %>%
               mutate(scriptID = as.integer(str_extract(.x, "[\\d]+"))) %>%
               unnest_tokens(bigram, text, token = "ngrams", 
                             n = 2, collapse = FALSE) %>%
               separate(bigram, c("word1", "word2"), sep = " ") %>%
               filter(word1 %in% c("he", "she"))) %>%
    select(scriptID, line, word1, word2)


pronoun_bigrams
```

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



```r
mapping <- read_csv("full_mapping.csv") %>%
    rename(imdb = imdb_id)

gender <- read_csv("gender.csv") %>%
    filter(important == "1",
           gender != "NULL")

genre <- read_tsv("imdb-genre.tsv") %>%
    rename(imdb = imdb_id)

metadata <- read_tsv("imdb-meta-data-title.tsv") %>%
    select(imdb, year) ## could also get title here if we want later

pronoun_imdb <- pronoun_bigrams %>%
    left_join(mapping, by = c("scriptID" = "id")) 
```

What kind of joining can I do? Year, genre, gender of writers/etc


```r
pronoun_imdb %>%
    inner_join(genre, by = "imdb") ## to get genre
```

```
## # A tibble: 2,394,282 x 6
##    scriptID  line word1 word2      imdb     Genre
##       <int> <chr> <chr> <chr>     <chr>     <chr>
##  1     1502   152    he  goes tt0453562 Biography
##  2     1502   152    he  goes tt0453562     Drama
##  3     1502   152    he  goes tt0453562     Sport
##  4     1502   162    he tries tt0453562 Biography
##  5     1502   162    he tries tt0453562     Drama
##  6     1502   162    he tries tt0453562     Sport
##  7     1502   165    he never tt0453562 Biography
##  8     1502   165    he never tt0453562     Drama
##  9     1502   165    he never tt0453562     Sport
## 10     1502   664    he steps tt0453562 Biography
## # ... with 2,394,272 more rows
```

```r
pronoun_imdb %>%
    inner_join(gender, by = "imdb") ## to get gender of writers, etc
```

```
## # A tibble: 5,563,413 x 10
##    scriptID  line word1  word2      imdb     role       person_name gender    role_two important
##       <int> <chr> <chr>  <chr>     <chr>    <chr>             <chr>  <chr>       <chr>     <chr>
##  1     1498    69    he  comes tt0472033 director       Shane Acker   male         n/a         1
##  2     1498    69    he  comes tt0472033   writer    Pamela Pettler female  screenplay         1
##  3     1498    69    he  comes tt0472033   writer       Shane Acker   male       story         1
##  4     1498    69    he  comes tt0472033 producer Timur Bekmambetov   male    producer         1
##  5     1498    69    he  comes tt0472033 producer        Tim Burton   male    producer         1
##  6     1498    69    he  comes tt0472033 producer     Dana Ginsburg female    producer         1
##  7     1498    69    he  comes tt0472033 producer        Jim Lemley   male    producer         1
##  8     1498    69    he  comes tt0472033 producer      Marci Levine female co-producer         1
##  9     1498   103    he gently tt0472033 director       Shane Acker   male         n/a         1
## 10     1498   103    he gently tt0472033   writer    Pamela Pettler female  screenplay         1
## # ... with 5,563,403 more rows
```


## Change by year

We can use logistic regression modeling to find the words paired with "she" that have changed the fastest with time, either in a positive or negative direction.


```r
words_by_year <- pronoun_imdb %>%
    inner_join(metadata, by = "imdb") %>% ## to get year
    filter(word1 == "she",
           str_detect(word2, "[a-z]+"),
           word2 != "look",
           nchar(word2) > 2,
           !is.na(year),
           year > 1980) %>%
    mutate(year = 5 * year %/% 5) %>%
    add_count(year) %>%
    rename(year_total = n) %>%
    add_count(word2) %>%
    rename(word_total = n) %>%
    filter(word_total > 50) %>%
    count(word2, year, year_total)

slopes <- words_by_year %>%
    nest(-word2) %>%
    mutate(models = map(data, ~ glm(cbind(n, year_total) ~ year, ., 
                                    family = "binomial"))) %>%
    unnest(map(models, tidy)) %>%
    filter(term == "year") %>%
    arrange(estimate)
```



```r
words_by_year %>%
    inner_join(slopes %>%
                   top_n(10, estimate), 
               by = "word2") %>%
    ggplot(aes(year, n / year_total, color = word2)) +
    geom_line(alpha = 0.8, size = 1.3) +
    labs(x = NULL, y = "Word frequency",
         title = "Words paired with 'she' in script set directions",
         subtitle = "Many of the words increasing in frequency with time are negative contractions")
```

![plot of chunk contractions](figure/contractions-1.png)



```r
words_by_year %>%
    inner_join(slopes %>%
                   filter(str_detect(word2, "^[^’]*$")) %>%
                   top_n(10, estimate), 
               by = "word2") %>%
    ggplot(aes(year, n / year_total, color = word2)) +
    geom_line(alpha = 0.8, size = 1.3) +
    labs(x = NULL, y = "Word frequency",
         title = "Words paired with 'she' in script set directions",
         subtitle = "More recently, women are more likely to inspect, drift, steal, exhale,  breathe")
```

![plot of chunk increasing](figure/increasing-1.png)



```r
words_by_year %>%
    inner_join(slopes %>%
                   top_n(-10, estimate), 
               by = "word2") %>%
    ggplot(aes(year, n / year_total, color = word2)) +
    geom_line(alpha = 0.8, size = 1.3) +
    labs(x = NULL, y = "Word frequency",
         title = "Words paired with 'she' in script set directions",
         subtitle = "In the past, women were more likely to jab, snuggle, fling, whirl, shriek")
```

![plot of chunk decreasing](figure/decreasing-1.png)

I'm picturing an interactive version of this chart where the user can switch from increasing to decreasing, hover over the lines to see how they are changing. Maybe decide how many to see?

Let's check the words that change for "he", to compare. (This probably won't go into the final essay.)


```r
words_by_year <- pronoun_imdb %>%
    inner_join(metadata, by = "imdb") %>% ## to get year
    filter(word1 == "he",
           str_detect(word2, "[a-z]+"),
           word2 != "look",
           nchar(word2) > 2,
           !is.na(year),
           year > 1980) %>%
    mutate(year = 5 * year %/% 5) %>%
    add_count(year) %>%
    rename(year_total = n) %>%
    add_count(word2) %>%
    rename(word_total = n) %>%
    filter(word_total > 100) %>%
    count(word2, year, year_total)

slopes <- words_by_year %>%
    nest(-word2) %>%
    mutate(models = map(data, ~ glm(cbind(n, year_total) ~ year, ., 
                                    family = "binomial"))) %>%
    unnest(map(models, tidy)) %>%
    filter(term == "year") %>%
    arrange(estimate)

# increasing for "he"
slopes %>% 
    top_n(10, estimate)
```

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

```r
# decreasing for "he"
slopes %>% 
    top_n(-10, estimate)
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
