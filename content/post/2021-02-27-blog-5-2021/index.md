---
title: 'Learning Stats at UofT #4: Model Selection'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The fourth post in the series of Learning Stats at UofT"

date: "2021-02-27T09:00:00Z"

categories:
  - education
tags:
  - education
  - uoft
featured: yes
projects: []
---

This is the fourth post of the series Learning Stats at UofT. In case you did not read my last post, here is the introudction.

The fundamental statistics courses at UofT are normally unchanged, at least from my experience in the past three years. Still, I think it is worth devoting some blogs to this topic. Before starting the introduction to courses, I would like to spend some time on the programs offered by DoSS.

I am a student in Applied Statistics Specialist, or Method and Application, at UofT. Though there were some changes in the requirements, the main focus of the two programs is the same. In particular, you will go through some fundamentals in R and (Frequentist) statistics in your first two years, and take upper year courses in some advanced topics. Compared to the Theory one, you do not need to take so many courses in theory, but you need to choose a focus depending on your interest. The focus seemed less important, but I gave a lot of thoughts about it in my past years. So I would like to share some of them with you. Note that all of these can be found on the official website of Arts & Science, and I hope this paragraph serves well as an introduction.

# Model Selection

In the third year course STA302, you will learn about simple linear regression and multiple linear regression models. In fact, you will learn more about assumptions behind models and possible remedies for improvements. Nonetheless, what you will not learn is whether it is appropriate to apply this model in a particular field. 

In reality, you will find that many models fit the data pretty well, but those models are incorrect. So here comes the question: to what extent you can apply a complex model on your data? The disciplinary knowledge is crucial in this context. It not only provides a justification for model, but also a way to interpret the model. 

# Does Disciplinary Knowledge Play a Role in Model Selection?

In NFS284, you learn about some thresholds to determine whether the consumption of nutrients is adequate. These thresholds, however, depend on the normality assumption. In fact, if you look at the hypothesis testing in the academic papers, you will notice that almost always P=0.05 is selected as significant level and a normal assumption is approximated. But this requires some reasons. You cannot select a P value that just serves your convenience. 

I believe that the researchers have more knowledge in nutrition science than me, and they may have very good reasons for their application of statistics. However, it is not very common to see the justifications in the well-written papers. In fact, you cannot tell whether disciplinary knowledge plays a role in Model Selection.

One possible reason is that this requires researchers to devote some paragraphs on it. The page limit of an academic journal, nevertheless, may not allow them to do so. To take a step back, even if there is some restrictions on the length of the article, this justification should not be given up just because of it.    




