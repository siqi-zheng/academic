---
title: 'Test'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "Test"

date: "2021-06-15T01:00:00Z"
projects: []
output: 
  html_document:
    keep_md: true
---

We build a linear regression below.





```r
fit = lm(dist ~ speed, data = cars)
b = coef(summary(fit))
plot(fit)
```

![](test_files/figure-html/unnamed-chunk-1-1.png)

![](test_files/figure-html/unnamed-chunk-1-2.png)

![](test_files/figure-html/unnamed-chunk-1-3.png)

![](test_files/figure-html/unnamed-chunk-1-4.png)

The slope of the regression is 3.9324088.
