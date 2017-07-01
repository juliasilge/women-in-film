---
title: "Women in Film"
author: "Julia Silge"
date: '2017-06-30'
output: html_document
---



How are women portrayed in film? It's a complicated question, and that question might make you think of a male protagonist's romantic interest, or someone in trouble who needs to be rescued, or maybe Wonder Woman.

One way to quantitatively measure how women are portrayed in film is to look at set directions in film scripts. Set directions, if you're not familiar with them, are the part of a script that are not dialogue. They describe actions and reactions, and communicate to the actors, producers, and director what story the script is working to tell. 



```
## Downloading: 1.4 kB     Downloading: 1.4 kB     Downloading: 2.8 kB     Downloading: 2.8 kB     Downloading: 4.1 kB     Downloading: 4.1 kB     Downloading: 5.5 kB     Downloading: 5.5 kB     Downloading: 6.9 kB     Downloading: 6.9 kB     Downloading: 8.2 kB     Downloading: 8.2 kB     Downloading: 8.6 kB     Downloading: 8.6 kB     Downloading: 9.9 kB     Downloading: 9.9 kB     Downloading: 11 kB     Downloading: 11 kB     Downloading: 13 kB     Downloading: 13 kB     Downloading: 13 kB     Downloading: 13 kB     Downloading: 13 kB     Downloading: 13 kB     Downloading: 14 kB     Downloading: 14 kB     Downloading: 15 kB     Downloading: 15 kB     Downloading: 17 kB     Downloading: 17 kB     Downloading: 17 kB     Downloading: 17 kB     Downloading: 18 kB     Downloading: 18 kB     Downloading: 20 kB     Downloading: 20 kB     Downloading: 20 kB     Downloading: 20 kB     Downloading: 21 kB     Downloading: 21 kB     Downloading: 21 kB     Downloading: 21 kB     Downloading: 21 kB     Downloading: 21 kB     Downloading: 22 kB     Downloading: 22 kB     Downloading: 24 kB     Downloading: 24 kB     Downloading: 25 kB     Downloading: 25 kB     Downloading: 25 kB     Downloading: 25 kB     Downloading: 25 kB     Downloading: 25 kB     Downloading: 27 kB     Downloading: 27 kB     Downloading: 28 kB     Downloading: 28 kB     Downloading: 30 kB     Downloading: 30 kB     Downloading: 31 kB     Downloading: 31 kB     Downloading: 32 kB     Downloading: 32 kB     Downloading: 33 kB     Downloading: 33 kB     Downloading: 35 kB     Downloading: 35 kB     Downloading: 35 kB     Downloading: 35 kB     Downloading: 36 kB     Downloading: 36 kB     Downloading: 37 kB     Downloading: 37 kB     Downloading: 38 kB     Downloading: 38 kB     Downloading: 40 kB     Downloading: 40 kB     Downloading: 40 kB     Downloading: 40 kB     Downloading: 41 kB     Downloading: 41 kB     Downloading: 42 kB     Downloading: 42 kB     Downloading: 43 kB     Downloading: 43 kB     Downloading: 45 kB     Downloading: 45 kB     Downloading: 45 kB     Downloading: 45 kB     Downloading: 46 kB     Downloading: 46 kB     Downloading: 48 kB     Downloading: 48 kB     Downloading: 48 kB     Downloading: 48 kB     Downloading: 49 kB     Downloading: 49 kB     Downloading: 51 kB     Downloading: 51 kB     Downloading: 52 kB     Downloading: 52 kB     Downloading: 52 kB     Downloading: 52 kB     Downloading: 54 kB     Downloading: 54 kB     Downloading: 54 kB     Downloading: 54 kB     Downloading: 55 kB     Downloading: 55 kB     Downloading: 56 kB     Downloading: 56 kB     Downloading: 57 kB     Downloading: 57 kB     Downloading: 58 kB     Downloading: 58 kB     Downloading: 60 kB     Downloading: 60 kB     Downloading: 60 kB     Downloading: 60 kB     Downloading: 61 kB     Downloading: 61 kB     Downloading: 62 kB     Downloading: 62 kB     Downloading: 62 kB     Downloading: 62 kB     Downloading: 62 kB     Downloading: 62 kB     Downloading: 62 kB     Downloading: 62 kB
```

```
## # A tibble: 421,890 x 4
##    scriptID  line word1   word2
##       <int> <chr> <chr>   <chr>
##  1     1502   152    he    goes
##  2     1502   162    he   tries
##  3     1502   165    he   never
##  4     1502   664    he   steps
##  5     1502  1656   she   asked
##  6     1502  1657   she   asked
##  7     1502  1815    he   turns
##  8     1502  1947    he    sits
##  9     1502  2434    he watches
## 10     1502  2434   she  starts
## # ... with 421,880 more rows
```

*Really rough mock-up of general idea for main viz:*

![plot of chunk pronoun_ratio](figure/pronoun_ratio-1.png)

*Label all the outlier points, choose interesting other points to label. For all labeled points, have example text for interactive hover. For non-labeled points, show word on hover.*



|text                                                        |
|:-----------------------------------------------------------|
|She squeals.  Business is over.  He carries her across      |
|He jumps on her. She squeals. He pretends to give her CPR.  |
|which is running the news with the sound muted. She squeals |
|She squeals, pained to leave her collection behind.         |
|up the techno music.  She squeals and bounces away.  Dodge  |
|He dumps cold champagne on her back.  She squeals and jumps |






*Rough mockup for viz of writer/director viz*

![plot of chunk gender_ratio](figure/gender_ratio-1.png)

*Hover for word for all points*

