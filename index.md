---
title       : Familywise Error Rate App
subtitle    : A Simple Web App for Calculating FWER
author      : 
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : [mathjax]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## What is Familywise Error Rate?

When you conduct multiple comparisons on the same data set - either by subsetting it differently or testing for different things - your type I error rate will increase. This is called the multiple comparisons problem, and it can create significant errors if it's not corrected for.

This app provides an easy interface that helps marketing professionals who do a lot of A/B testing avoid errors caused by their FWER. It is inspired by other similar tools, such as [KissMetrics' A/B test tool](http://getdatadriven.com/). 

---

## How to Use

* STEP 1: Put the results of your A/B tests into the boxes under step 1. Click "Calculate", and the app will return the p-value of the test. Repeat for all your tests.
* STEP 2: Copy and paste the p-values from the box on the right side of the step 1 area and paste them in the p-values box in step 2.
* STEP 3: Make any confidence level adjustments and/or choose an error correction method (more details in the later slides) in the settings on the left rail. Then press CALCULATE in the step 3 box!

--- 

## Bonferroni Correction

The Boneferroni Correction scales alpha according to the total number of hypotheses being tested. It then only considers p values significant if they satisfy $p_i \leq \alpha/n$, Where n is the total number of hypotheses.

As an example, given a set of p-values and the standard alpha of .05:


```r
  pValues <- c(0.045, 0.093, 0.09, 0.014, 0.007, 0.076)
  alpha <- 0.05
  bonferroni_alpha <- alpha / length(pValues)
  which(pValues <= bonferroni_alpha)
```

```
## [1] 5
```

Only the 5th p-value (0.007) is considered significant post-correction; without the correction, p-values 1, 4 and 5 would all be considered significant.

---

## Benjamani-Hochberg Procedure

The Benjamani-Hochberg Procedure scales alpha more leniently than the Bonferroni Correction, generating more false positives but fewer false negatives. The procedure does the following:

* Sorts the test p-values from smallest to largest
* For every $i$ th p-value, checks if it satisfies $p_i \leq i/n * \alpha$
* If so, that p-value is considered significant.

---

## BH Procedure Example

Using the p-values from the Bonferroni example:


```r
  results <- numeric(0)
  pValues <- sort(pValues)
  for (i in 1:length(pValues)) {
    if ( pValues[i] <= (i/length(pValues) * 0.05) ) {
      results <- append(results, pValues[i])
    }
  }
  results
```

```
## [1] 0.007 0.014
```

## Results

As you can see, the BH Procedure found two significant results, while the Bonferroni Correction found only one.
