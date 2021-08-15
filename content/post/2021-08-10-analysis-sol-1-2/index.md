---
title: 'Supplement Solutions for New Questions in Chapter 1.4 to 1.6 in Understanding Analysis Second Edition'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The remaining solutions for New Questions in Chapter 1 in Understanding Analysis Second Edition"

date: "2021-08-10T01:00:00Z"
links:
- icon: images
  icon_pack: fas
  name: download
  url: files/uasol12.pdf
categories:
  - Course Notes
tags:
  - Mathematics
  - Real Analysis
featured: yes
projects: []
---

**Note: There may be LaTex display issues due to blogdown rendering limitations. A complete well-formatted solution can be found by clicking the download icon above.** 

One may notice that most questions in the second edition are the same as those in the first edition. However, there are still some new or modified questions in the latest edition that remain unanswered. 

Therefore, in the following posts, I am going to present a collection of solutions to these new questions found on the internet and worked out by myself. To be more concise and clear, I also rewrote some of my solutions according to the internet sources (links are attached at the end of each question). The solution to the first edition can be found here: https://github.com/mikinty/Understanding-Analysis-Abbott-Solutions

**Exercise 1.5.2.** Review the proof of Theorem 1.5.6, part (ii) showing that $\Bbb R$ is uncountable, and then find the flaw in the following erroneous proof that $\Bbb Q$ is uncountable:
Assume, for contradiction, that $\Bbb Q$ is countable. Thus we can write $\Bbb Q = \{r1, r2, r3, \dots\}$ and, as before, construct a nested sequence of closed intervals with $r_n \not \in I_n$. Our construction implies $\cap^\infty_{n=1} I_n = \empty$ while NIP implies $\cap^\infty_{n=1} I_n \neq \empty$. This contradiction implies Q must therefore be uncountable.

(1) It may contain only one irrational number.
(2) NIP is for real intervals not rational.

Source: https://math.stackexchange.com/questions/1914901/false-proofs-claiming-that-mathbbq-is-uncountable

**Exercise 1.5.4.** (a) Show $(a, b) \sim R$ for any interval $(a, b)$.
We know from the **Example 1.4.9.** that the function $f(x) = x/(x^2 − 1)$ takes the interval $(−1, 1)$ onto $\Bbb R$ in a 1–1 fashion. Then we map $(a,b)$ onto $(-1,1)$ by another bijective linear function $g(x)=2x/(b-a)-(b+a)/(b-a)$.

(b) Show that an unbounded interval like $(a,\infty) = \{x : x > a\}$ has the same cardinality as $\Bbb R$ as well.
We know from the **Example 1.4.9.** that the function $f(x) = x/(x^2 − 1)$ takes the interval $(−1, 1)$ onto $\Bbb R$ in a 1–1 fashion. Then we map $(a,\infty)$ onto $(-1,1)$ by another bijective linear function $g(x)=2x/(1-x)$.

(c) Using open intervals makes it more convenient to produce the required 1–1, onto functions, but it is not really necessary. Show that $[0, 1) \sim (0, 1)$ by exhibiting a 1–1 onto function between the two sets.
$f:[0,1) \rightarrow (0,1)$ by $f(0)=1/2$, $f(1/n)=1/(n+1)$ for integer $n \geq 2$, and $f(x)=x$ otherwise.

Source: https://math.stackexchange.com/questions/1425492/explicit-bijection-between-0-1-and-0-1

**Exercise 1.5.5.** (a) Why is $A \sim A$ for every set $A$?
Trivial. By definition $f(x)=x$ will do the job.

(b) Given sets $A$ and $B$, explain why $A \sim B$ is equivalent to asserting $B \sim A$.
Bijection, so consider inverse mapping.

(c) For three sets $A,B,$ and $C$, show that $A \sim B$ and $B \sim C$ implies $A \sim C$. These three properties are what is meant by saying that $\sim$ is an equivalence relation.
Assume $f$ maps $A$ to $B$ and $g$ maps $B$ to $C$, $g(f(x))$ will work.

**Exercise 1.5.6.** (a) Give an example of a countable collection of disjoint open intervals.
$A_n = (n, n+1)$, $n\in \Bbb N$

(b) Give an example of an uncountable collection of disjoint open intervals, or argue that no such collection exists.
DNE. Every collection of disjoint open intervals in $\Bbb R$ is countable because you can choose a rational number (by density theorem) in each of them and rationals are countable.

**Exercise 1.5.7.** Consider the open interval $(0,1)$, and let $S$ be the set of points in the open unit square; that is, $S = \{(x, y) : 0 < x,y < 1\}$.

(a) Find a 1–1 function that maps $(0, 1)$ into, but not necessarily onto, $S$. (This is easy.)
$f(x) = (x,x),x \in (0,1)$

(b) Use the fact that every real number has a decimal expansion to produce a 1–1 function that maps $S$ into $(0, 1)$. Discuss whether the formulated function is onto. (Keep in mind that any terminating decimal expansion such as $.235$ represents the same real number as $.234999 \dots$)

For any point with two coordinates $(0.d_1d_2\dots,0.e_1e_2\dots)$, we map it to the real number $(0.d_1e_1d_2e_2\dots)$. We restrict the choice of point in its simplest form so that $(0.2,0.5)$ will be chosen for $0.25$ instead of $(0.2999\dots,0.4999\dots)$, which is equal to $(0.3,0.5)$, corresponding to $0.35$.

This function (mapping), however, is not onto. Consider $1/11=0.090909\dots$, which by definition can be produced by a point $(0,0.999\dots)$, but this point can no be selected since it is equal to $(0,1)$ and $(0,1)$ yields $0.01$. Therefore not point in the unit square can be used to map to $1/11$.

**Exercise 1.5.8.** Let $B$ be a set of positive real numbers with the property that adding together any finite subset of elements from $B$ always gives a sum of $2$ or less. Show $B$ must be finite or countable.

For each $n\in \Bbb N$, let$$B_n=\left\{b\in B\,\middle|\,b\geqslant\frac2n\right\}\subset B.$$

Of course, $B_n$ can have no more than $n-1$ distinct elements; otherwise, the sum of $n$ distinct elements of $B_n$ would be grater than $2$.

But$$B=\bigcup_{n\in\Bbb N}B_n.$$Since $\Bbb N$ is countable and each $B_n$ is finite, $B$ is countable.

Source: https://math.stackexchange.com/questions/2446630/showing-a-set-is-finite-or-countable

**Exercise 1.5.10.** (a) Let $C \subseteq [0,1]$ be uncountable, show there exists $a \in (0,1)$ such that $C \cap [a,1] $ is uncountable.

Suppose that $C\cap [\tfrac{1}{n}, 1]$ is countable for all $n$. Then $$C\cap [0,1] = C\cap\big(\{0\}\cup \bigcup_{n=1}^\infty [\tfrac{1}{n},1]\big) = (C\cap \{0\}) \cup \bigcup_{n=1}^\infty (C\cap [\tfrac{1}{n}, 1])$$ would be countable too.

Source: https://math.stackexchange.com/questions/1452550/let-c-subseteq-0-1-be-uncountable-show-there-exists-a-in-0-1-such-tha

(b) Now let A be the set of all $a \in (0, 1)$ such that $C \subseteq [a,1]$ is uncountable, and set $\alpha = supA$. Is $C \subseteq [0,1]$ an uncountable set?

WTS: Suppose $C\subseteq [0,1]$ is uncountable. Let $A = \{a\in (0,1)\mid C\cap[a,1]$ is uncountable $\}$, and $\alpha = \sup A$. Then $C\cap [\alpha,1]$ is countable.

First, $A$ is nonempty: for $n\in\Bbb N$ let $C_n = C\cap [\frac 1 n, 1]$. Some $C_n$ must be uncountable, otherwise $C= \bigcup_n C_n$ is a countable union of countable sets and therefore countable. So for some $n$, $1/n \in A$.

Clearly $0 \lt \alpha \le 1$.

If $\alpha =1 $ then of course the claim is true.
If $\alpha \lt 1$. Let $(b_n)$ be a decreasing sequence in $(\alpha, 1)$ with $\alpha = \inf_n b_n$. By definition of $A$ and $\alpha$, for every $n$, $C\cap[b_n,1]$ is countable, for otherwise $b_n\in A$ and $b_n \le \alpha$. Thus 
$$\begin{align}
C\cap [\alpha,1] &= C\cap \bigcup_n [b_n, 1] \\
&= \bigcup_n (C\cap [b_n, 1])
\end{align}$$
is a countable union of countable sets, so it's countable.

Source: https://math.stackexchange.com/questions/1639608/intersection-of-uncountable-sets

**Exercise 1.5.11 (Schr&#246;der–Bernstein Theorem).** Assume there exists a 1–1 function function $f: X \rightarrow Y$ and another 1–1 function $g: Y \rightarrow X$. Then there exists a 1–1, onto function $h: X \rightarrow Y$ and hence $X \sim Y$.

The strategy is to partition $X$ and $Y$ into components $X = A \cup A'$ and $Y = B \cup B'$ with $A \cap A' = \emptyset$ and $B \cap B' = \emptyset$, in such a way that $f$ maps $A$ onto $B$ and $g$ maps $B'$ onto $A'$.

(a) Explain how achieving this would lead to a proof that $X \sim Y$.
$f: A \rightarrow B$ is a 1–1, onto function;
$g: B' \rightarrow A'$ is a 1–1, onto function;
Then $h(x)=f(x)$ if $x \in A$ and $h(x)=g^{-1}(x)$ if $x \in A'$ is a $X \rightarrow Y$ 1–1, onto function and hence $X \sim Y$.

(b) Set $A_1 = X \setminus g(Y)$ (what happens if $A_1 = \emptyset$?) and inductively define a sequence of sets by letting $A_{n+1} = g(f(A_n))$. Show that $\{A_n : n \in \Bbb{N}\}$ is a pairwise disjoint collection of subsets of $X$, while $\{f(A_n) : n \in \Bbb{N} \}$ is a similar collection in $Y$.

For $k \ge 2$, since $A_k = g(f(A_{k-1})) \subseteq g(Y)$, $A_k$ and $A_1$ are disjoint.

For $2 \le m \lt  n$, if there exists $a \in A_m \cap A_n$, then for some $a_{m-1} \in A_{m-1}$ and $a_{n-1} \in A_{n-1}$, $f(g(a_{m-1})) = a = f(g(a_{n-1}))$. Since both $f$ and $g$ are injective, here $a_{m-1} = a_{n-1}$. Hence $A_m \cap A_n \ne \emptyset$ implies $A_{m-1} \cap A_{n-1} \ne \emptyset$. By induction, we can conclude that $A_1 \cap A_{n-m+1} \ne \emptyset$, which is contradict with part 1. Therefore $A_m$ and $A_n$ are disjoint ($2 \le m \lt  n$).

Source: https://math.stackexchange.com/questions/1726578/understanding-a-proof-of-schr%C3%B6der-bernstein-theorem

(c) Let $A = \cup_{n=1}^\infty A_n$ and $B = \cup_{n=1}^\infty f(A_n)$. Show that $f$ maps $A$ onto $B$.
Trivial because for every $b \in B$, $b = f(a_n)$ for some $a_n \in A_n \subseteq A $.

(d) Let $A' = X\setminus A$ and $B' = Y \setminus B$. Show $g$ maps $B'$ onto $A'$.
Suppose there is an element $a' \in A'\not \in g(B')$. Since $a'$ cannot be in $A_1$ there has to be an element $b \in f(A_n)\subset B$ s.t. $g(b)=a'$. Since $b \in f(A_n)$ we can write it as $f(a)=b$ and therefore $a'=g(f(a))\in A_{n+1}$. But this is a contradiction to where $a'$ lives.

Source: https://math.stackexchange.com/questions/1726578/understanding-a-proof-of-schr%C3%B6der-bernstein-theorem

**Exercise 1.6.9.** Using the various tools and techniques developed in the last two sections (including the exercises from Section 1.5), give a compelling argument showing that $\cal P(\Bbb N) \sim \Bbb R$.

First note that that $\Bbb R$ can inject into $ \cal P(\Bbb Q)$ by mapping $r$ to $\{q\in\Bbb Q\mid q \lt r\}$. Since $\Bbb Q$ is countable there is a bijection between $\cal P(\Bbb Q)$ and $\cal P(\Bbb N)$. So $\Bbb R$ injects into $\cal P(\Bbb N)$.

Then note that we can map $x\in 2^\Bbb N$ to the continued fraction defined by the sequence $x$. Or to a point in $[0,1]$ defined by $\sum\frac{x(n)}{3^{n+1}}$, which we can show is injective in a somewhat easier proof.

