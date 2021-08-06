---
title: 'Supplement Solutions for New Questions in Chapter 1.2 to 1.4 in Understanding Analysis Second Edition'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The solutions for New Questions in Chapter 1 in Understanding Analysis Second Edition"

date: "2021-08-05T01:00:00Z"

categories:
  - Course Notes
tags:
  - Mathematics
  - Real Analysis
featured: yes
projects: []
---

One may notice that most questions in the second edition are the same as those in the first edition. However, there are still some new or modified questions in the latest edition that remain unanswered. 

Therefore, in the following posts, I am going to present a collection of solutions to these new questions found on the internet and worked out by myself. To be more concise and clear, I also rewrote some of my solutions according to the internet sources (links are attached at the end of each question). The solution to the first edition can be found here: https://github.com/mikinty/Understanding-Analysis-Abbott-Solutions

**Exercise 1.2.2.** Show that there is no rational number r satisfying $2^r=3$.

Suppose $r=\frac{a}{b}$ with positive integers $a,b$.

Then, we get $$2^{\frac{a}{b}}=3$$

which can be expressed as

$$2^a=3^b$$

This is clearly a contradiction because the left side is even and the right side is odd.

Source: https://math.stackexchange.com/questions/1427219/prove-there-is-no-rational-r-satisfying-2r-3

**Exercise 1.2.4.** Expressing $\Bbb N$ as an infinite union of disjoint infinite subsets.

Let $A_{i}$ consist of all the numbers of the form $2^im$ where $2\nmid m$. That is, $A_i$ consists of all the numbers that have exactly a factor of $2^i$ in them. So
$$\begin{align}
A_1 = \{1,3,5,7,9,11, \dots\}\\
A_2 = \{2, 6 =2^1\cdot 3, 10 = 2^1\cdot 5, 14 = 2^1\cdot 7, \dots\}\\
A_3 = \{4 = 2^2, 12=2^2\cdot 3, 20=2^2\cdot 5, \dots\}\\
A_4 = \{8 = 2^3, 24=2^3\cdot 3, 40=2^3\cdot 5, \dots\}\\
\vdots
\end{align}
$$

Source: https://math.stackexchange.com/questions/847465/expressing-bbb-n-as-an-infinite-union-of-disjoint-infinite-subsets

As pointed out in the link above, any prime numbers can work here.
$
A_1 = \{x|x$ is not a multiple of 3, $x \in \Bbb N\}$
$
A_2 = \{3a,a\in A_1\}\\
A_3 = \{3^2a,a\in A_1\}\\
A_4 = \{3^3a,a\in A_1\}\\
\vdots
$

**Exercise 1.2.8.**

Give an example of each or state that the request is impossible:

(a) $f : \Bbb N \rightarrow \Bbb N$ that is 1–1 but not onto.
$f(x) = x^2+2$ because $1 \in \Bbb N$, but $f(x)>1$ $\forall x \in \Bbb N$

(b) $f : \Bbb N \rightarrow \Bbb N$ that is onto but not 1–1.
$f(x) = (x-2)^2$ because $f(1)=f(3)$ while $1 \neq 3$

(c) $f : \Bbb N \rightarrow \Bbb Z$ that is 1–1 and onto.
$f(x) = x^2$

**Exercise 1.2.10.** Decide which of the following are true statements. Provide a short justification for those that are valid and a counterexample for those that are not:

(a) Two real numbers satisfy a < b if and only if a < b + $\epsilon$ for every $\epsilon$ > 0.
The converse is FALSE if we take a=b=5.

(b) Two real numbers satisfy a < b if a < b + $\epsilon$ for every $\epsilon$ > 0.
The statement is FALSE if we take a=b=5.

(c) Two real numbers satisfy a ≤ b if and only if a < b + $\epsilon$ for every $\epsilon$ > 0.
Forward (trivial): 
$a \le b \lt b + \epsilon$.
Reverse: 
Suppose $a \lt b + \epsilon$, $\forall \epsilon \gt 0$.
Let $\delta = a - b$, then $b + \delta = b + a -b = a$ so that $a \not \lt b + \delta$. $\delta \not \gt 0$, so $\delta = a - b \le 0$. Hence $a \le b$.

Source: https://math.stackexchange.com/questions/1633992/if-true-prove-that-2-real-numbers-satisfy-ab-iff-ab-epsilon-forall-e/1633997

**Exercise 1.2.12.** Let $y_1 = 6$, and for each $n\in \Bbb N$ define $y_{n+1} = (2y_n − 6)/3$.

(a) Use induction to prove that the sequence satisfies $y_n > −6$ for all $n \in \Bbb N$.
* Base Case: $y_1 = 6 > -6$
* Inductive case. Assume $y_k>-6$.
* $y_{k+1}=\frac{2y_k}{3}-2>\frac{2\times(-6)}{3}-2=-4-2=-6$
* By induction our original claim is proved.

(b) Use another induction argument to show the sequence $(y_1, y_2, y_3, \dots)$ is decreasing.
* Base Case: $y_2 = 2 < 6 = y_1$
* Inductive case. Assume $y_{k+1}<y_k$.
* $y_{k+2}=\frac{2y_{k+1}}{3}-2
=\frac{2y_{k+1}}{3}+\frac{-6}{3}
<\frac{2y_{k+1}}{3}+\frac{y_{k+1}}{3}
=y_{k+1}$
* By induction our original claim is proved.

**Exercise 1.3.2.** Give an example of each of the following, or state that the request is impossible.

(a) A set B with inf B $\geq$ sup B.
$B=\{1\}$

(b) A finite set that contains its infimum but not its supremum.
Except for $\emptyset$, by Axiom of Completeness, we cannot find such set.

(c) A bounded subset of Q that contains its supremum but not its infimum.
$C=\{1/x|x\in\Bbb N\}$ contains its supremum 1 but not its infimum 0.

**Exercise 1.3.4.** Let $A_1,A_2,A_3,\dots$ be a collection of nonempty sets, each of which is bounded above.

(a)Find a formula for $sup(A_1 \cup A_2)$. Extend this to $sup(\cup^n_{k=1}A_k)$.
$sup \{sup A_1, sup A_2\}$
$sup \{sup A_1, sup A_2 \dots sup A_n\}$

(b) Consider $sup(\cup^{\infty}_{k=1}A_k)$. Does the formula in (a) extend to the infinite case?

No. Consider $A_i = {i}$, we have $sup(\cup^n_{k=1}A_k)=i$, but $sup(\cup^{\infty}_{k=1}A_k)$ does not exist.

**Exercise 1.3.6.** Given sets A and B, define $A+B = \{a+b : a \in A$ and $b \in B\}$.
Follow these steps to prove that if A and B are nonempty and bounded above then sup(A + B) = supA + supB.

(a) Let s = sup A and t = sup B. Show s + t is an upper bound for A + B.
Take $a \in A$ and $b \in B$, by definition, $a\leq s$ and $b \leq t$ and $a+b \in A+B$. So $a+b \leq s+t$.

(b) Now let $u$ be an arbitrary upper bound for A + B, and temporarily fix $a \in A$. Show $t \leq u − a$.
By definition of $A + B$ and $\sup(A + B)$, for all $a \in A$ and $b \in B$,
$${a + b} {\leq \sup (A + B)} {\leq u}.$$ 

If we fix $a \in A$, then ${\sup (A + B)} - a$  is an upper bound for $${A + B} - A = B.$$

Subtract $a$ from both sides:

$$b = {a + b} - a \leq \sup (A + B) \leq u - a.$$

And so by definition of $\sup B$, for every $a \in A$, $$\sup B =t \leq \sup (A+ B) − a\leq u - a.$$

(c) Finally, show sup(A + B) = s + t.
Rearrange the previous inequality in (b): ${a} \leq \sup(A +B) − \sup B$ for all $a \in A$.

Hence, $\sup(A +B) − \sup B$ is an upper bound for any a.

By the definition of supremum, the previous inequality means: ${\sup  A} \leq \sup(A + B) − \sup B \iff 
\sup A + \sup B \leq \sup(A + B).$ i.e.

$$s+t \leq sup(A+B)$$

Also, by inequality $a+b \leq s+t$ in (a) and the definition of supremum:
$$sup(A+B)\leq s+t$$

We conclude that
$$sup(A+B)= s+t.$$

(d) Construct another proof of this same fact using Lemma 1.3.8.

Let $\epsilon \gt 0.$ Then there exists $a \in A$ and $b \in B$ such that $a \gt \sup A − \frac{\epsilon}{2}$ and $b \gt \sup B − \frac{\epsilon}{2}.$
Then $a + b \in A + B$. We have
$${\sup(A + B)} \geq a + b {\gt \sup A + \sup B - \epsilon} \implies { \sup(A + B)  \gt \sup A + \sup B - \epsilon }.$$ Since $\epsilon$ is arbitrary, $\sup(A + B) \geq \sup A + \sup B=s+t$

Take $a \in A$ and $b \in B$, by definition, $a\leq s$ and $b \leq t$ and $a+b \in A+B$. So $a+b \leq s+t$. Also, by inequality $a+b \leq s+t$ in (a) and the definition of supremum:
$$sup(A+B)\leq s+t = sup(A+B)$$

We conclude that
$$sup(A+B)= s+t.$$

Source: https://math.stackexchange.com/questions/4551/how-can-i-prove-supab-sup-a-sup-b-if-ab-ab-mid-a-in-a-b-in-b

**Exercise 1.3.8.** Compute, without proofs, the suprema and infima (if they exist) of the following sets:

(a) $\{m/n : m, n \in N$ with $m < n\}$.
sup: $1$ inf: $0$

(b) $\{(−1)^m/n : m, n \in N\}$.
sup: $1$ inf: $-1$

(c) $\{n/(3n+ 1) : n \in N\}$.
sup: $\frac{1}{3}$ inf: $\frac{1}{4}$

(d) $\{m/(m+ n) : m, n \in N\}$.
sup: 1 inf: 0

**Exercise 1.3.9.** 

(a) If supA < supB, show that there exists an element $b \in B$ that is an upper bound for A.
Take $\epsilon=supB-supA$ and take $b \in B$ where $b>supB-\epsilon /2$ as desired.

(b) Give an example to show that this is not always the case if we only assume supA ≤ supB.
Take $A=\{0\}$ and $B=\{-1/n,n \in \Bbb N\}$

**Exercise 1.3.10 (Cut Property).**

(a) Use the Axiom of Completeness to prove the Cut Property. 
Suppose we have the axiom of completeness and assume you have $A$ and $B$ as in the statement of the cut property. Then, as $B$ is nonempty, $A$ has an upper bound. Let $c$ be the least upper bound for $A$.

For $a\in A$, $a\le c$, because $c$ is an upper bound for $A$;
For $b\in B$, $c\le b$, because $b$ is an upper bound for $A$ and $c$ is the least upper bound.

Source: https://math.stackexchange.com/questions/1616583/use-the-axiom-of-completeness-to-prove-the-cut-property

(b)Show that the implication goes the other way.
Suppose we know the Cut Property. Consider a nonempty set $E$ with an upper bound. Then let

$B=\{x\in\mathbb{R}: x\geq e \forall e\in E\}$
i.e. $B$ is the set of all upper bounds of $E$

and let $A$ be the complement of $B$. 
$A=\{x\in\mathbb{R}: x\lt e$ for some $e\in E\}$

Since $E$ is non-empty and bounded above, $B$ is nonempty as well as $A$. The union of $A$ and $B$ is $\mathbb{R}$ by construction. Suppose $a\in A$ and $b\in B$. If $b\le a$, we have $e\leq a \forall e\in E$, so $A\in B$: a contradiction.

Since $b>a$ for all $a \in A$ and $b \in B$, we know there exists $d$ such that $a \leq d$ and $d \leq b$ by Cut Property. We want to show that $d$ is the supremum for E.

To show that $d$ is an upper bound of $E$, suppose some $s$ in $E$ exceeds $d$. Since $(s + d)/2$ exceeds $d$, it
belongs to $B$, so by the definition of $B$ it must be an upper bound of $E$, which is impossible since $s > (s + d)/2$. To show that $d$ is a least upper bound of $E$, suppose that some $a < d$ is an upper bound of $E$. But $a$ (being less than $d$) is in $A$, so it can’t be an upper bound of $E$.

Source: https://arxiv.org/abs/1204.4483

(c) give a concrete example showing that the Cut Property is not a valid statement when $\Bbb R$ is replaced by $\Bbb Q$.
Hint: Find the break point in $\Bbb Q:$ $\sqrt{5},\sqrt{3},\dots$
Consider $A = (-\infty, 0) \cup \{x \ge 0 : x^2 \le 3\}$ and $B = \{x \ge 0 : x^2 \gt 3\}$
If such a number $c$ existed, we would have $c^2 = 3$.  But there is no rational number for which this is true.

**Exercise 1.3.11.** Without worrying about formal proofs for the moment, decide if the following statements about suprema and infima are true or false. For any that are false, supply an example where the claim in question does not appear to hold.

(a) TRUE. Since $A \subset B$, $\sup B$ is an upper bound for $A$. Since $\sup A$ is the least upper bound for $A$ by definition, it must be less than or equal $\sup B$.

(b) TRUE. Take $c=(sup A + inf B)/2$ will work for nonempty sets $A$ and $B$.

(c) FALSE. Consider $A = (-\infty, 3)$ and $B = (3, \infty)$, where $a<3<b$ and $sup A = inf B$.

**Exercise 1.4.2.** Let $A \subseteq \Bbb R$ be nonempty and bounded above, and let $s \in \Bbb R$ have the property that for all $n \in \Bbb N$, s + 1/n is an upper bound for A and s − 1/n is not an upper bound for A. Show s = supA.

Suppose s is not an upper bound for A. Then $\exist a \in A$ such that $s \lt a$. Take $\delta = a - s$ and $n_0 \in \Bbb N$ to be large enough so that $1/\delta < n_0$ i.e. $1/n_0 < \delta$. By definition, $s+1/n_0$ is an upper bound for $A$, but $s+1/n_0<s+\delta=a\in A$: a contradiction.

Let $\epsilon>0$. Take $n_1 \in \Bbb N$ to be large enough so that $1/\epsilon < n_1$ i.e. $1/n_1 < \epsilon$. By definition, $\exist a \in A$ such that $ s-\epsilon \lt s-1/n_1 \lt a$. Hence s = sup A.

**Exercise 1.4.4.** Let $a \lt b$ be real numbers and consider the set $T=\mathbb{Q}\cap[a,b]$. Show $\sup T=b$

If $x\in T$, then $x\in [a,b]$, and if $x\in [a,b]$, then $x\leq b$ i.e. $b$ is an upper bound for T. 

To show that $b$ is a least upper bound of $T$, suppose that some $c \lt b$ is an upper bound of $T$. Since the rationals are dense in $\Bbb R$ there exists a rational $t$ such that $a\lt c \lt t \lt b$. This means $t \in [a,b]$ and $t \lt c$ by definition of upper bound, which is a contradiction.

**Exercise 1.4.6.** Which of the following sets are dense in $\Bbb R$? Take $p \in \Bbb Z$ and $q \in \Bbb N$ in every case.

(a) The set of all rational numbers $p/q$ with $q \leq 10$.
Not dense in $\Bbb R$.
For any distinct $\frac pq$ and $\frac{p'}{q'}$ with $q,q'\le 10$ the difference $$ \frac pq-\frac{p'}{q'}=\frac{pq'-p'q}{qq'}$$ is a fraction with non-zero numerator and denominator$\le 10^2$, hence is $\ge \frac{1}{10^2}$ in absolute value. For example, no element in this set can be found between $1/500$ and $2/500$.

Source: https://math.stackexchange.com/questions/1638526/how-do-you-show-a-set-is-dense-for-example-is-the-set-of-all-rational-numbers

(b) The set of all rational numbers $p/q$ with $q$ a power of $2$.
Dense in $\Bbb R$.
Consider two arbitrary real numbers $a,b$ with $a\lt  b $,
By the Archimedean Property there exists $n \in \mathbb N$ such that $$0\lt  \frac{1}{n} \lt b-a \;\;\text{ which implies} \;\; 0\lt \frac{1}{2^{n}}\lt \frac{1}{n}\lt b-a$$
Thus we have $1\lt b2^n-a2^n$.
As the distance between $a2^n$ and $b2^n$ is greater than $1$, there exists $m \in \mathbb N$ such that $a2^{n}\lt m\lt b2^{n}$
which implies that $a \lt \frac{m}{2^{n}} \lt b$. Since $a$ and $b$ were arbitrary, the claim is proved.

Source: https://math.stackexchange.com/questions/3968925/proof-of-dyadic-rational-numbers-are-dense-in-mathbb-r

(c) The set of all rational numbers $p/q$ with $10|p| \geq q$.

Not dense in $\Bbb R$.
Rational numbers between (-1/10,1/20) are missing.
For example, no element in this set can be found between $-1/20$ and $-1/30$.

Source: https://www.reddit.com/r/HomeworkHelp/comments/7ruu7u/real_analysis_density_of_subsets_of_q_in_r/

**Exercise 1.4.8.** Give an example of each or state that the request is impossible. When a request is impossible, provide a compelling argument for why this is the case.

(a) Two sets A and B with $A \cap B = \empty$, supA = supB, $supA \not \in A $ and $supB \not \in B$.
$A=\{x|x\in I,x\in (0,1)\}$
$B=\{x|x\in Q,x\in (0,1)\}$

(b) A sequence of nested open intervals $J_1 \supseteq J_2 \supseteq J_3 \supseteq \dots $ with 	$\cap^\infty_{n=1}J_n$ nonempty but containing only a finite number of elements.
$J_n = (5-1/n,5+1/n), n \in \Bbb N, \cap^\infty_{n=1}J_n=5$

(c) A sequence of nested unbounded closed intervals  $L_1 \supseteq L_2 \supseteq L_3 \supseteq \dots $ with 	$\cap^\infty_{n=1}L_n=\empty$ (An unbounded closed interval has the form $[a,\infty) = \{x \in \Bbb R : x \geq a\}$.)
$L_n = [n,\infty), n \in \Bbb N, \cap^\infty_{n=1}J_n=\empty$

(d) A sequence of closed bounded (not necessarily nested) intervals $I_1, I_2, I_3, \dots$ with the property that $\cap^N_{n=1} I_n \neq \empty$ for all $N \in \Bbb N$, but $\cap^\infty_{n=1} I_n = \empty$.
The answer is negative, because then $\cap^N_{n=1} I_n$ for all $N \in \Bbb N$ is a decreasing sequence of non-empty closed and bounded intervals and therefore its intersection is non-empty. 

Source: https://math.stackexchange.com/questions/2619781/intersection-of-a-sequence-of-closed-intervals


## Appendix for unused sources

By definition, $d$ is an upper bound for A. So it is an upper bound for $E$, because if there exists $e \in E$ with $d<e$, then $d<\frac{d+e}{2}$. $\frac{d+e}{2}$ cannot be in $B$ (indeed, it's not an upper bound for $E$, because it's less than $e$) so it must be in $A$, but this contradicts that $d$ is an upper bound for $A$.

Source: https://math.stackexchange.com/questions/2228772/assume-mathbbr-possesses-the-cut-property-and-let-e-be-a-nonempty-that-is-b

If possible, suppose $A$ has the greatest member, say $a'$. Then, $a' \in A \Rightarrow a' \not\in B$. We know $\exist s \in E$ such that $a' \lt s$, since $a'<(a'+s)/2 \in B$, $(a'+s)/2 $ is an upper bound of $S$. This contradiction leads to the fact that $A$ has no greatest member. And so, $B$ has the least member. Hence, the set of upper bounds of a non-empty set 𝑆 bounded above has a least member, which is the completeness axiom in $\Bbb R$. Hence the theorem is proved.

Read more: https://www.emathzone.com/tutorials/real-analysis/dedekind-property.html#ixzz72M0m5FcR