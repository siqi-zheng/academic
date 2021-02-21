---
title: First Blog in 2021 about Teaching Statistics
draft: false
authors: 
  - Siqi Zheng
event: '2020-2021 STA130H1F'
location: Blog
abstract: ""
summary: "Two questions from my students in STA130."

# Talk start and end times.
#   End time can optionally be hidden by prefixing the line with `#`.
date: "2021-01-30T09:00:00Z"
date_end: "2021-01-30T10:00:00Z"
all_day: false

categories:
  - education
tags:
  - test statistic
  - simulation
featured: yes
image:
  caption: '[Illustration by Chris Liverani](https://unsplash.com/photos/dBI_My696Rk)'
  focal_point: 'center'
  preview_only: no
projects: []
links:
# - icon: play-circle
#   icon_pack: fas
#   name: video
#   url: https://resources.rstudio.com/webinars/sharing-on-short-notice-how-to-get-your-materials-online-with-r-markdown
# - icon: github
#   icon_pack: fab
#   name: materials
#   url: https://github.com/rstudio-education/sharing-short-notice
---

A few days ago, a student asked me about the logic behind the simulated sampling distribution. She was curious about how we could use reshuffling labels + random sampling to obtain a sampling distribution. The logic is related to the Skeptic’s Argument and the consequence of it. Here is the theorem and its elegant proof. 

![Theorem from the Skeptic’s Argument](1.jpg)


I am particularly interested in her question because I knew the intuition behind the codes back to my first year at UofT, but I also saw some unbalanced CRDs and the same reshuffling method was used to calculate the sampling distribution. Hence I was confused by the use of the codes.

The symmetric property of an unbalanced CRD turns out to be uncertain in theory, but in many cases we can still see a simulated sampling distribution somewhat symmetric around 0. 

Another question about test statistic appears in one of my students’ writing assignments. In the writing, this student defines the test statistic (mean) as a random variable after calculating an exact number from the sample. Indeed, a test statistic __can be__ a random variable, but it is not when a number is already obtained from the sample. A great answer from an online forum is attached below.

![Answer from Stack Exchange](test.jpg)


Test statistic is about the sample, but parameter is about the population. If one is thinking about whether a parameter is a random variable or a fixed value, then one may be very interested in the controversy between Bayes and Frequentist. They are fundamentally different approaches to knowledge about data and uncertainty, but they can yield the same result in many situations mathematically. 

This also reminds me of my past experience with statistics. When I learnt probability theories in middle school, we did not really differentiate between these two approaches. We sometimes claimed that one event was more likely to happen because of higher probability, and sometimes interpreted the proportion of heads when flipping the coin for many times as the long-term frequency. 

To wrap up, I use these two examples to show that there can be complicated theories behind some seemingly simple facts. Though this is really beyond the scope of STA130, I still think students can benefit from thinking about these questions. One will get to know more about statistics when one takes a second-year statistics course.

## References

* [Statistic Course Material](http://pages.stat.wisc.edu/~wardrop/courses/371chapter3sum15.pdf) 

* [Stack Exchange Question](https://stats.stackexchange.com/questions/85426/is-test-statistic-a-value-or-a-random-variable)