---
title: "Women in Film"
author: "Julia Silge"
date: '2017-06-30'
output: html_document
---



How are women portrayed in film? It's a complicated question, and that question might make you think of a male protagonist's romantic interest, or someone in trouble who needs to be rescued, or maybe Wonder Woman.

One way to quantitatively measure how women are portrayed in film is to look at set directions in film scripts. Set directions, if you're not familiar with them, are the part of a script that are not dialogue. They describe actions and reactions, and communicate to the actors, producers, and director what story the script is working to tell. 



```
## 
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
