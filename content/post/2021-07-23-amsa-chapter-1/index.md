---
title: 'Mathematics Theorems and Proofs in Applied Multivariate Statistical Analysis (CH.1)'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The notes in Chapter 1 for Applied Multivariate Statistical Analysis"

date: "2021-07-21T01:00:00Z"

categories:
  - Course Notes
tags:
  - Mathematics
  - Applied Statistics
featured: yes
projects: []
---

## Details in Chapter 1 (Johnson & Wichern, 2002)

P78 (2-48)

**Cauchy-Schwarz Inequality**. Let $\mathbf{b}$ and $\mathbf{d}$ be any two $p\times 1$ vectors. Then
$$
\left(\mathbf{b}^{\prime} \mathbf{d}\right)^{2}\leq(\mathbf{b}^{\prime} \mathbf{b}){(\mathbf{d}^{\prime} \mathbf{d})}
$$
with equality if and only if $\mathbf{b}=c\mathbf{d}$ (or $c\mathbf{d}=\mathbf{b}$) for some constant c.

Proof. The inequality is obvious if either $\mathbf{b}=\mathbf{0}$ or $\mathbf{d}=\mathbf{0}$. Excluding this possibility, consider the vector $\mathbf{b}-x \mathbf{d}$, where $x$ is an arbitrary scalar. Since the length of $\mathbf{b}-x \mathbf{d}$ is positive for $\mathbf{b}-x \mathbf{d} \neq \mathbf{0}$, in this case
$$
\begin{aligned}
0<(\mathbf{b}-x \mathbf{d})^{\prime}(\mathbf{b}-x \mathbf{d}) &=\mathbf{b}^{\prime} \mathbf{b}-x \mathbf{d}^{\prime} \mathbf{b}-\mathbf{b}^{\prime}(x \mathbf{d})+x^{2} \mathbf{d}^{\prime} \mathbf{d} \\
&=\mathbf{b}^{\prime} \mathbf{b}-2 \boldsymbol{x}\left(\mathbf{b}^{\prime} \mathbf{d}\right)+x^{2}\left(\mathbf{d}^{\prime} \mathbf{d}\right)
\end{aligned}
$$
The last expression is quadratic in $x .$ If we complete the square by adding and subtracting the scalar $\left(\mathbf{b}^{\prime} \mathbf{d}\right)^{2} / \mathbf{d}^{\prime} \mathbf{d}$, we get
$$
\begin{gathered}
0<\mathbf{b}^{\prime} \mathbf{b}-\frac{\left(\mathbf{b}^{\prime} \mathbf{d}\right)^{2}}{\mathbf{d}^{\prime} \mathbf{d}}+\frac{\left(\mathbf{b}^{\prime} \mathbf{d}\right)^{2}}{\mathbf{d}^{\prime} \mathbf{d}}-2 x\left(\mathbf{b}^{\prime} \mathbf{d}\right)+x^{2}\left(\mathbf{d}^{\prime} \mathbf{d}\right) \\
=\mathbf{b}^{\prime} \mathbf{b}-\frac{\left(\mathbf{b}^{\prime} \mathbf{d}\right)^{2}}{\mathbf{d}^{\prime} \mathbf{d}}+\left(\mathbf{d}^{\prime} \mathbf{d}\right)\left(x-\frac{\mathbf{b}^{\prime} \mathbf{d}}{\mathbf{d}^{\prime} \mathbf{d}}\right)^{2}
\end{gathered}
$$
The term in brackets is zero if we choose $x=\mathbf{b}^{\prime} \mathbf{d} / \mathbf{d}^{\prime} \mathbf{d}$, so we conclude that
$$
0<\mathbf{b}^{\prime} \mathbf{b}-\frac{\left(\mathbf{b}^{\prime} \mathbf{d}\right)^{2}}{\mathbf{d}^{\prime} \mathbf{d}}
$$
or $\left(\mathbf{b}^{\prime} \mathbf{d}\right)^{2}<\left(\mathbf{b}^{\prime} \mathbf{b}\right)\left(\mathbf{d}^{\prime} \mathbf{d}\right)$ if $\mathbf{b} \neq x \mathbf{d}$ for some $x$
Note that if $\mathbf{b}=c \mathbf{d}, 0=(\mathbf{b}-c \mathbf{d})^{\prime}(\mathbf{b}-c \mathbf{d})$, and the same argument produces
$\left(\mathbf{b}^{\prime} \mathbf{d}\right)^{2}=\left(\mathbf{b}^{\prime} \mathbf{b}\right)\left(\mathbf{d}^{\prime} \mathbf{d}\right)$

**Extended Cauchy-Schwarz Inequality**. Let $\mathbf{b}$ and $\mathbf{d}$ be any two $p\times 1$ vectors, and let $\mathbf{B}$ be a positive definite matrix. Then
$$
\left(\mathbf{b}^{\prime} \mathbf{d}\right)^{2}\leq(\mathbf{b}^{\prime} \mathbf{B} \mathbf{b}){(\mathbf{d}^{\prime} \mathbf{B}^{-1} \mathbf{d})}
$$

with equality if and only if $\mathbf{b}=c\mathbf{B}^{-1}\mathbf{d}$  or $\mathbf{d}=c\mathbf{B}\mathbf{b}$ for some constant c.

Proof. The inequality is obvious when $\mathbf{b}=\mathbf{0}$ or $\mathbf{d}=\mathbf{0}$. For cases other than these, consider the square-root matrix $\mathbf{B}^{1 / 2}$ defined in terms of its eigenvalues $\lambda_{i}$ and the normalized eigenvectors $\mathbf{e}_{i}$ as $\mathbf{B}^{1 / 2}=\sum_{i=1}^{p} \sqrt{\lambda_{i}} \mathbf{e}_{i} \mathbf{e}_{i}^{\prime} .$ If we set 
$$
\mathbf{B}^{-1 / 2}=\sum_{i=1}^{p} \frac{1}{\sqrt{\lambda_{i}}} \mathbf{e}_{i} \mathbf{e}_{i}^{\prime}
$$
it follows that
$$
\mathbf{b}^{\prime} \mathbf{d}=\mathbf{b}^{\prime} \mathbf{I} \mathbf{d}=\mathbf{b}^{\prime} \mathbf{B}^{1 / 2} \mathbf{B}^{-1 / 2} \mathbf{d}=\left(\mathbf{B}^{1 / 2} \mathbf{b}\right)^{\prime}\left(\mathbf{B}^{-1 / 2} \mathbf{d}\right)
$$
and the proof is completed by applying the Cauchy-Schwarz inequality to the vectors $\left(\mathbf{B}^{1 / 2} \mathbf{b}\right)$ and $\left(\mathbf{B}^{-1 / 2} \mathbf{d}\right)$

Let $\mathbf{u}=\mathbf{B}^{1 / 2} \mathbf{b}$ and $\mathbf{v}=\mathbf{B}^{-1 / 2} \mathbf{d}$, we have 
$$
\mathbf{b}^{\prime} \mathbf{d}=\left(\mathbf{B}^{1 / 2} \mathbf{b}\right)^{\prime}\left(\mathbf{B}^{-1 / 2} \mathbf{d}\right)\leq(\mathbf{b}^{\prime} \mathbf{b}){(\mathbf{d}^{\prime} \mathbf{d})}=(\mathbf{B}^{1 / 2} \mathbf{b})^{\prime}(\mathbf{B}^{1 / 2} \mathbf{b})(\mathbf{B}^{-1 / 2} \mathbf{d})(\mathbf{B}^{-1 / 2} \mathbf{d})^{\prime}=(\mathbf{b}^{\prime} \mathbf{B} \mathbf{b}){(\mathbf{d}^{\prime} \mathbf{B}^{-1} \mathbf{d})}
$$

The extended Cauchy-Schwarz inequality gives rise to the following maximization result.

**Maximization Lemma.** Let $\underset{(p \times p)}{\mathbf{B}}$ be positive definite and $\underset{(p \times 1)}{\mathbf{d}}$ be a given vector. Then, for an ărbitrary nonzero vector $\underset{(p \times 1)}{\mathbf{x}}$,
$$
\max _{\mathbf{x} \neq \boldsymbol{\theta}} \frac{\left(\mathbf{x}^{\prime} \mathbf{d}\right)^{2}}{\mathbf{x}^{\prime} \mathbf{B} \mathbf{x}}=\mathbf{d}^{\prime} \mathbf{B}^{-\mathbf{1}} \mathbf{d}
$$
with the maximum attained when $\underset{(p \times 1)}{\mathbf{x}}=\underset{(p \times p)\langle p \times 1)}{\mathbf{d}}$ for any constant $c \neq 0$. Proof. By the extended Cauchy-Schwarz inequality, $\left(\mathbf{x}^{\prime} \mathbf{d}\right)^{2} \leq\left(\mathbf{x}^{\prime} \mathbf{B} \mathbf{x}\right)\left(\mathbf{d}^{\prime} \mathbf{B}^{-1} \mathbf{d}\right)$.
Because $\mathbf{x} \neq \mathbf{0}$ and $\mathbf{B}$ is positive definite, $\mathbf{x}^{\prime} \mathbf{B} \mathbf{x}>0$. Dividing both sides of the
inequality by the positive scalar $\mathbf{x}^{\prime} \mathbf{B} \mathbf{x}$ yields the upper bound
$$
\frac{\left(\mathbf{x}^{\prime} \mathbf{d}\right)^{2}}{\boldsymbol{x}^{\prime} \mathbf{B} \mathbf{x}} \leq \mathbf{d}^{\prime} \mathbf{B}^{-1} \mathbf{d}
$$

Taking the maximum over $\mathbf{x}$ gives Equation $(2-50)$ because the bound is attained for $\mathbf{x}=c \mathbf{B}^{-1} \mathbf{d} .$

A final maximization result will provide us with an interpretation of eigenvalues.

**Maximization of Quadratic Forms for Points on the Unit Sphere.** Let $\mathbf{B}$ be a positive definite matrix witl eigenvalues $\lambda_{1} \geq \lambda_{2} \geq \cdots \geq \lambda_{p} \geq 0$ and associated normalized eigenvectors $\mathbf{e}_{\mathbf{1}}, \mathbf{e}_{2}, \ldots, \mathbf{e}_{p}$. Then

$$
\max _{\mathbf{x} \neq \mathbf{0}} \frac{\mathbf{x}^{\prime} \mathbf{B} \mathbf{x}}{\mathbf{x}^{\prime} \mathbf{x}}=\lambda_{1}\quad \text { (attained when } \mathbf{x}=\mathbf{e}_{1} \text {)}
$$

$$
\min _{\mathbf{x} \neq 0} \frac{\mathbf{x}^{\prime} \mathbf{B} \mathbf{x}}{\mathbf{x}^{\prime} \mathbf{x}}=\lambda_{p} \quad \text { (attained when } \mathbf{x}=\mathbf{e}_{p} \text {)}
$$

Moreover, 
$$
\max _{\mathbf{x} \perp \mathbf{e}_{\mathbf{1}},\ldots,\mathbf{e}_{\mathbf{k}}} \frac{\mathbf{x}^{\prime} \mathbf{B} \mathbf{x}}{\mathbf{x}^{\prime} \mathbf{x}}=\lambda_{k+1} \quad \text { (attained when } \mathbf{x}=\mathbf{e}_{k+1} \text {, } k=1,2,\ldots,p-1 \text {)}
$$

where the symbol $\perp$ is read "is perpendicular to.
Proof. Let $\underset{\langle p \times p\}}{\mathbf{P}}$ be the orthogonal matrix whose columns are the eigenvectors
$\mathbf{e}_{1}, \mathbf{e}_{2}, \ldots, \mathbf{e}_{p}$ and $\mathbf{A}$ be the diagonal matrix with eigenvalues $\lambda_{1}, \lambda_{2}, \ldots, \lambda_{p}$ along the
main diagonal. Let $\mathbf{B}^{1 / 2}=\mathbf{P} \Lambda^{1 / 2} \mathbf{P}^{\prime}$ and $\underset{(p \times 1)}{\mathbf{y}}=\underset{(p \times p)(p \times 1)}{\mathbf{x}}$.
Consequently, $\mathbf{x} \neq \boldsymbol{0}$ implies $\mathbf{y} \neq \mathbf{0}$. Thus,
$$
\begin{aligned}
\frac{\mathbf{x}^{\prime} \mathbf{B} \mathbf{x}}{\mathbf{x}^{\prime} \mathbf{x}} &=\frac{\mathbf{x}^{\prime} \mathbf{B}^{1 / 2} \mathbf{B}^{1 / 2} \mathbf{x}}{\mathbf{x}^{\prime} \underbrace{\mathbf{P P}^{\prime}}_{\mathbf{I} \atop(p \times p)} \mathbf{x}}=\frac{\mathbf{x}^{\prime} \mathbf{P} \mathbf{\Lambda}^{1 / 2} \mathbf{P}^{\prime} \mathbf{P} \mathbf{\Lambda}^{1 / 2} \mathbf{P}^{\prime} \mathbf{x}}{\mathbf{y}^{\prime} \mathbf{y}}=\frac{\mathbf{y}^{\prime} \mathbf{\Lambda} \mathbf{y}}{\mathbf{y}^{\prime} \mathbf{y}} \\
&=\frac{\sum_{i=1}^{p} \lambda_{i} y_{i}^{2}}{\sum_{i=1}^{p} y_{i}^{2}} \leq \lambda_{1} \frac{\sum_{i=1}^{p} y_{i}^{2}}{\sum_{i=1}^{p} y_{i}^{2}}=\lambda_{\mathrm{I}}
\end{aligned}
$$

Setting $\mathbf{x}=\mathbf{e}_{1}$ gives
$$
\mathbf{y}=\mathbf{P}^{\prime} \mathbf{e}_{1}=\left[\begin{array}{c}
1 \\
0 \\
\vdots \\
0
\end{array}\right]
$$
since
$$
\mathbf{e}_{k}^{\prime} \mathbf{e}_{1}= \begin{cases}1, & k=1 \\ 0, & k \neq 1\end{cases}
$$
For this choice of $\mathbf{x}$, we have $\mathbf{y}^{\prime} \mathbf{\Lambda} \mathbf{y} / \mathbf{y}^{\prime} \mathbf{y}=\lambda_{1} / 1=\lambda_{1}$, or
$$
\frac{\mathbf{e}_{1}^{\prime} \mathbf{B e}_{1}}{\mathbf{e}_{1}^{\prime} \mathbf{e}_{1}}=\mathbf{e}_{1}^{\prime} \mathbf{B e}_{1}=\lambda_{1}
$$
A similar argument produces the second part of $(2-51)$. Now, $\mathbf{x}=\mathbf{P y}=y_{1} \mathbf{e}_{1}+y_{2} \mathbf{e}_{2}+\cdots+y_{p} \mathbf{e}_{p}$, so $\mathbf{x} \perp \mathbf{e}_{1}, \ldots, \mathbf{e}_{k}$ implies
$$
0=\mathbf{e}_{i}^{\prime} \mathbf{x}=y_{1} \mathbf{e}_{i}^{\prime} \mathbf{e}_{1}+y_{2} \mathbf{e}_{i}^{\prime} \mathbf{e}_{2}+\cdots+y_{p} \mathbf{e}_{i}^{\prime} \mathbf{e}_{p}=y_{i}, \quad i \leq k
$$
Therefore, for $x$ perpendicular to the first $k$ eigenvectors $\mathbf{e}_{i}$, the left-hand side of the inequality in $(2-53)$ becomes
$$
\frac{\mathbf{x}^{\prime} \mathbf{B} \mathbf{x}}{\mathbf{x}^{\prime} \mathbf{x}}=\frac{\sum_{i=k+1}^{p} \lambda_{i} y_{i}^{2}}{\sum_{i=k+1}^{p} y_{i}^{2}}
$$
Taking $y_{k+1}=1, y_{k+2}=\cdots=y_{p}=0$ gives the asserted maximum.
For a fixed $\mathbf{x}_{0} \neq \mathbf{0}, \mathbf{x}_{0}^{\prime} \mathbf{B} \mathbf{x}_{0} / \mathbf{x}_{0}^{\prime} \mathbf{x}_{0}$ has the same value as $\mathbf{x}^{\prime} \mathbf{B} \mathbf{x}$, where
$\mathbf{x}^{\prime}=\mathbf{x}_{0}^{\prime} / \sqrt{\mathbf{x}_{0}^{\prime} \mathbf{x}_{0}}$ is of unit length. Consequently, Equation (2-51) says that the largest eigenvalue, $\lambda_{1}$, is the maximum value of the quadratic form $\mathbf{x}^{\prime} \mathbf{B} \mathbf{x}$ for all points $\mathbf{x}$ whose distance from the origin is unity. Similarly, $\lambda_{p}$ is the smallest value of the quadratic form for all points $x$ one unit from the origin. The largest and smallest eigenvalues thus represent extreme values of $\mathbf{x}^{\prime} \mathbf{B} \mathbf{x}$ for points on the unit sphere. The "intermediate" eigenvalues of the $p \times p$ positive definite matrix $B$ also have an interpretation as extreme values when $\mathbf{x}$ is further restricted to be perpendicular to the earlier choices.

## An Example of the Application of Cauchy-Schwarz Inequality (Cramér, 1946)

In statistical problems, large amounts of data are collected to study a phenomenon. With a desire to derive a mathematical model to describe it, we may find, numerically, a function $\widetilde{\phi}$ to approximate a parameter $\phi$. $\widetilde{\phi}$ is called an unbiased estimator of $\phi$ if $E(\widetilde{\phi})=\phi . \quad$ That is
$$
\int_{-\infty}^{\infty} \widetilde{\phi} f_{\theta}(x) d x=\phi(\theta)
$$
Here, $\theta$ and $x$ are independent parameters. Differentiating this with respect to $\theta$ and interchanging integration and differentiation (provided of course that this is permissible) gives:
$$
\int_{-\infty}^{\infty} \widetilde{\phi}(x) \frac{\partial f_{\theta}}{\partial \theta}(x) d x=\phi^{\prime}(\theta)
$$
The rate of change of information is the function
$$
S(x):=\frac{\partial}{\partial \theta} \log f_{\theta}(x)
$$
called the score statistic. Plainly, $S(x)=\frac{1}{f_{\theta}(x)} \frac{\partial f_{\theta}}{\partial \theta}(x)$, so that we can write
$$
\int_{-\infty}^{\infty} \widetilde{\phi}(x) S(x) f_{\theta}(x) d x=\phi^{\prime}(\theta) .
$$
Also, the expectation of $S(x)$ is
$$
E(S(x))=\int_{-\infty}^{\infty} S(x) f_{\theta}(x) d x=\int_{-\infty}^{\infty} \frac{\partial f_{\theta}}{\partial \theta}(x) d x=\frac{\partial}{\partial \theta} \int_{-\infty}^{\infty} f_{\theta}(x) d x=0
$$
since
$$
\int_{-\infty}^{\infty} f_{\theta}(x) d x=1
$$
because the total probability is $1 .$ Thus, (4.1) can be re-written as
$$
\int_{-\infty}^{\infty}(\widetilde{\phi}(x)-\phi(\theta)) S(x) f_{\theta}(x) d x=\phi^{\prime}(\theta) .
$$
Applying the Cauchy-Schwarz inequality, we obtain
$$
\phi^{\prime}(\theta)^{2} \leq\left(\int_{-\infty}^{\infty}(\widetilde{\phi}(x)-\phi(\theta))^{2} f_{\theta}(x) d x\right)\left(\int_{-\infty}^{\infty} S(x)^{2} f_{\theta}(x) d x\right)
$$

Writing
$$
I(\theta):=\int_{-\infty}^{\infty}\left(\frac{\partial \log f_{\theta}}{\partial \theta}\right)^{2} f_{\theta}(x) d x
$$
(called Fisher information in statistical parlance), we can write our inequality as:

**Theorem 5** (The Cramér-Rao inequality). For an unbiased estimator $\widetilde{\phi}$ of $\phi$, we have
$$
\int_{-\infty}^{\infty}(\widetilde{\phi}(x)-\phi(\theta))^{2} f_{\theta}(x) d x \geq \frac{\phi^{\prime}(\theta)^{2}}{I(\theta)} .
$$
Often, this is applied with $\phi(\theta)=\theta$ so that $\phi^{\prime}(\theta)=1$. The inequality then gives us a limitation on the accuracy of the unbiased estimator to the function $\theta$. Somtimes it is referred to as the information inequality. It was discovered independently by C. R. Rao [10] and H. Cramér [2] in 1945 and has played a pivotal role in statistical inference. An enlightening survey of the Cramér-Rao inequality was written by K.R. Parthasarathy [9] where the reader can find discussion of Riemannian metrics to study population models.

Regarding Theorem 5 , there is a lot of interest in estimators that actually achieve the Cramer-Rao lower bound. Such estimators are said to be asymptotically efficient. Under certain regularity conditions the maximum likelihood estimators are asymptotically efficient. In such cases the Fisher information about $\theta$ in the data is equal to the inverse of the variance of the estimator.

## Reference

1. Cramér, H. (1946). Mathematical methods of statistics. Princeton University Press,
Princeton. 
2. Johnson, R. A., Wichern, D. W. (2002). Applied multivariate statistical analysis. Upper Saddle River, NJ: Prentice Hall. ISBN: 0130925535
 