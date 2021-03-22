---
title: 'Learning Stats at UofT #5: A Dialogic Way to Introduce MLE'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The fifth post in the series of Learning Stats at UofT"

date: "2021-03-06T09:00:00Z"

categories:
  - education
tags:
  - education
  - uoft
featured: yes
projects: []
---

This is the fifth post of the series Learning Stats at UofT. In case you did not read my last post, here is the introudction.

The fundamental statistics courses at UofT are normally unchanged, at least from my experience in the past three years. Still, I think it is worth devoting some blogs to this topic. Before starting the introduction to courses, I would like to spend some time on the programs offered by DoSS.

I am a student in Applied Statistics Specialist, or Method and Application, at UofT. Though there were some changes in the requirements, the main focus of the two programs is the same. In particular, you will go through some fundamentals in R and (Frequentist) statistics in your first two years, and take upper year courses in some advanced topics. Compared to the Theory one, you do not need to take so many courses in theory, but you need to choose a focus depending on your interest. The focus seemed less important, but I gave a lot of thoughts about it in my past years. So I would like to share some of them with you. Note that all of these can be found on the official website of Arts & Science, and I hope this paragraph serves well as an introduction.

# A Dialogic Way to Introduce MLE

Sure, I can explain the concept of likelihood while we wait in line. In statistics, we often need to estimate the parameter of a model. But how? Well, Maximum Likelihood estimation (MLE) can help. First of all, we need to know likelihood means the probability of a value being the true value of a parameter θ in a model given a set of data. MLE provides a way to find a value θ ̂ with the maximum probability to be θ.

Let’s use an example to illustrate this. Suppose you are interested in a model that describes the waiting time for a customer in this restaurant. Now you can first collect the data of individual waiting time randomly. Then you may assume that the true population of waiting time follows some classical distributions so that we only need to estimate the parameter θ of a known distribution. Then we may be able to use MLE here. Does that make sense so far? 

Alright, now, here comes the tricky part. In order to use MLE, we need the joint distribution of all data X, which gives the probability that each of X falls within a specific range for a variable. But wait, we do not actually know it since we only know the marginal distribution, i.e. the density function of individual X with an unknown θ. Hence we can consider making an assumption that all data are independent, meaning knowing one waiting time does not tell us about the next waiting time. It may not be true in reality, but it is sufficient for our purpose. Under this assumption, we can multiply all marginal density to get our joint density. To find the maximum likelihood estimate, we can set the first derivative with respect to θ equal 0 and find a value. We can calculate the second derivative as well to ensure that θ ̂ is a maxima. This θ ̂ is our Maximum Likelihood estimate for the parameter. Did that explanation help?   

To sum up, Maximum Likelihood estimation gives an estimate for a parameter in a model given a set of data. In particular, with a known joint probability density function of all data and the parameter, we can use derivatives to find an estimate of the parameter with the maximum probability to be the true value for the model. Let’s pause for a moment! It is my turn to get the drink!




