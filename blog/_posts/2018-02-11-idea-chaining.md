---
layout: post
title:  "Idea chaining"
date:   2018-02-11 20:40:00 -0700
categories: jekyll update
---
In this post, we're going to talk about a concept that is useful in many areas...
statistics, optimization, life-in-general... I call it **idea chaining**. My guess is that
most people are already aware of this notion on an intuitive level. Systems thinkers would
probably call it something like "problem decomposition", mathematicians would think of
lemmas... basically we take a problem, split it into smaller, easier-to-solve 
sub-problems, and then chain our results together to solve the original problem.

### A (convex) calculus for convexity verification.

In convex analysis and optimization, we're often interested in whether or not a given
function is convex. There are a few ways to go about showing the "vexity" of a 
particular function, such as using:

* the definition of convexity;
* the restriction-to-a-line technique;
* first-order (*i.e.*, derivative) conditions;
* second-order conditions; or
* a convex calculus.

The **convex calculus** approach is appealing because it gives us a toolbox to 
(mindlessly) construct or decompose a function, using a set of basic functions and a few 
rules that preserve convexity. In the context of convex optimization, this 
**constructive convex verification process** 
is part of an approach known as **disciplined convex 
programming** (DCP). The DCP ideas arose from the work of Michael Grant and 
Stephen Boyd in the mid-2000s. Hiriart-Urruty and Lemaréchal discuss the notion of a 
convex calculus in their books from the 1990s.

Let's put the ideas to work on an example that came up on the quiz page of the 
[DCP][dcp-site] website (which teaches the principles of disciplined convex programming).

Our function of interest is

$$

f(u,w,y,z) = \log \left( \exp \left\{ e^{\max(u, z)} \right\} + 
						 \exp \left\{ g_{\mathrm{hub}}(w) \right\} +
						 \exp \left\{ y - 42 \right\}  \right),

$$

where 

$$
g_{\mathrm{hub}}(w) = 
\begin{cases}
	2|x| - 1, & |x| \ge 1 \\
	|x|^2, 	  & |x| < 1.
\end{cases}
$$

At first sight, this looks ugly because we have a concave function with $$\log$$, a couple
convex functions with $$\max$$ and $$\exp$$, an affine function $$y - 42$$, and that Huber
function $$g_{\mathrm{hub}}$$ (which is actually convex, but may not be recognized as 
such). Moreover, we have four variables!

The DCP / convex calculus approach turns this problem into an easy verification process.
The non-obvious and key ingredient is the "log-sum-exp" function, also called the soft-max
function. For $$x \in \reals^n$$, the log-sum-exp function is

$$
h(x) = \log \left( \sum_{i = 1}^n \exp ( x_i ) \right).
$$

This function is convex---if you're suspicious, calculate its Hessian and 
convince yourself. With this single fact, we're nearly done because the functions 
appearing within the exponentials are convex and affine. Then we use that log-sum-exp is 
convex and we're done.

**Tips and tricks.** Here are the DCP rules we used:

* an increasing convex function of a convex function is convex; and
* a sum of convex functions is convex.

So, with two rules and a few basic functions we've show a rather complicated function to 
be convex---no gradients, subgradients, or Hessians required. We reduced the problem
to a few simple subproblems (*e.g.*, verifying the convexity of 
$$e^{g_{\mathrm{hub}}(w)}$$ and the like) and then strung everything together using a
couple rules. Bam.

If you're interested in seeing the "vexity" parse tree for this function, check out the 
DCP site's "[Analyzer][dcp-analyzer]" and type in 
*log_sum_exp(exp(max(u, z)), huber(w) + y - 42)*. Do it!

### Le Cam's inequality.

Our next example of idea chaining stems from a problem from Tom Ferguson's (excellent) 
mathematical statistics book called *A Course in Large Sample Theory*. Problem 5 of 
chapter 3 ("Convergence in Law") walks us through Le Cam's inequality.

Here's the set up.

* We have $$n$$ independent Bernoulli random variables each with its own probability of
  success, $$p_i = \prob(X_i = 1)$$. We'll call these $$X_1,\dots,X_n$$.
* We define $$S_n = \sum_{i=1}^n X_i$$.
* We define $$Z$$ as a Poisson random variable with parameter 
  $$\lambda = \sum_{i=1}^n p_i$$.

We want to show that 

$$
| \prob(S_n \in A) - \prob(Z \in A)| \le \sum_{i=1}^n p_i^2
$$

for all sets $$A$$. In other words, we're computing a bound on the error of using a 
Poisson approximation.

We'll solve this problem by solving three simpler sub-problems.

(1) First, we'll show that 

$$
| \prob(S_n \in A) - \prob(Z \in A)| \le \prob(S_n \neq Z).
$$

(2) Then, we'll show that

$$
\prob(S_n \neq Z) \le \sum_{i=1}^n \prob(X_i \neq Y_i).
$$

(3) Finally, we'll show that

$$ 
\prob(X_i \neq Y_i) \le \sum_{i=1}^n p_i^2.
$$

To show (1), (2), and (3), we're going to couple $$S_n$$ and $$Z$$ to the same underlying 
probability space.

* The variables $$U_1,\dots,U_n$$ are uniformly distributed on $$[0,1]$$.
* We can define $$X_i = \ind{}(U_i > 1 - p_i)$$ with $$\E X_i = p_i$$.
* We introduce $$Y \sim \mathrm{Poisson}(p_i)$$ as

$$
Y_i =
\begin{cases}
0, & U_i < e^{-p_i} \\ 
k, & F(k-1) < U_i < F(k),
\end{cases}
$$

where $$F(k) = e^{-p_i} \sum_{j=0}^{k} p_i^j/j!$$ is the cumulative distribution function
for a Poisson random variable with parameter $$p_i$$.

**Proof of (1).**

Without loss of generality we can assume that $$\prob(S_n \in A) \ge \prob(Z \in A)$$. 
This allows us to conclude that $$\{S_n \in A\} \supseteq \{Z \in A\}$$. Now, we isolate
the set where $$\{S_n \neq Z\}$$ by noting that

$$
\prob(S_n \in A) - \prob(Z \in A) \le \prob(S_n \in A) - \prob(S_n \cap Z \in A) 
	= \prob(S_n \neq Z).
$$

One down, two to go.

**Proof of (2).**

First, let's rewrite $$\prob(S_n \neq Z)$$ as $$\prob \left( \sum_i X_i \neq Z \right)$$.
This implies that there's at least one $$X_i$$ not equal to $$Y_i$$. (The *at least* 
phrase should be triggering the part of your brain associated with *unions*.) 
So, we have that

$$
\prob \left( \bigcup_{i=1}^n \{X_i \neq Y_i\} \right) \le \sum_{i=1}^n \prob(X_i \neq Y_i)
$$

by the subadditivity of probability.

Two down, one to go.

**Proof of (3).**

Using our definitions of $$X_i$$ and $$Y_i$$, we can write

$$
\begin{align*}
\prob(X_i \neq Y_i) &= 1 - \prob(X_i = 0) - \prob(Y_i = 1) \\
	&= 1 - (1 - p_i) - p_i e^{-p_i} \\
	&= p_i - p_i e^{-p_i} \qquad \qquad \text{(because $(1-e^{-p_i}) \le p_i$)}\\
	&\le p_i^2.
\end{align*}
$$

Now, we sum both sides of the equation and we've got it.

**Bringing it all back home.**

Finally, chain the inequalities in (1), (2), and (3) together and we've proved Le Cam's
inequality. Heck yeah!

### References

This post draws on material from:

* Stephen Boyd's "[Convex Optimization Short Course][cvx-sc]" notes;
* *Fundamentals of Convex Analysis* by Hiriart-Urruty and Lemaréchal; and
* *A Course in Large Sample Theory* by Thomas Ferguson.

[cvx-sc]: https://web.stanford.edu/~boyd/papers/cvx_short_course.html
[dcp-site]: http://dcp.stanford.edu/
[dcp-analyzer]: http://dcp.stanford.edu/analyzer