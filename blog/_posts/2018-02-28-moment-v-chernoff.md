---
layout: post
title:  "Better bounds: moment vs. Chernoff"
date:   2018-02-28 20:00:00 -0700
categories: jekyll update
---
In this post, we're going to work through a problem from Chapter 2 of
*Concentration Inequalities: A Nonasymptotic Theory of Independence*. The exercise 
shows that moment bounds can be tighter than Cramér--Chernoff-type bounds.

### The problem

Our task to show that moment bounds are **always** better than Cramér--Chernoff bounds.
Assume we have a nonnegative random variable $$Y$$ and a positive number $$t$$. The best
moment bound is $$\prob(Y \ge t) = \min_{q \in \integers} \E(Y^q)t^{-q}$$. On the other
hand, the best Cramér--Chernoff bound is $$\inf_{\lambda > 0} \E e^{\lambda(Y-t)}$$. So,
our job is to show that

$$
\min_{q \in \integers} \E(Y^q)t^{-q} \le \inf_{\lambda > 0} \E e^{\lambda(Y-t)}.
$$

### The proof

We're going to write the $$q$$-th moment of $$Y$$ as $$m^q$$. Now, we'll write the 
moment generating function of $$Y$$ as a series

$$
\E e^{\lambda Y} = \sum_{k=1}^\infty \frac{\lambda^k m^k}{k!},
$$

which allows us to write the shifted moment generating function as

$$
\E e^{\lambda(Y-t)} = \left. \sum_{k=1}^\infty \frac{\lambda^k m^k}{k!} 
						 \middle/
						\sum_{k=1}^\infty \frac{\lambda^k t^k}{k!}  \right.
$$

By assumption, $$q$$ is the integer that minimizes $$\E(Y^q)t^{-q}$$, which means
that 

$$
\E(Y^q)t^{-q} = \frac{m^q}{t^q}
	\le \left. \frac{\lambda^k m^k}{k!} \middle/ \frac{\lambda^k t^k}{k!} \right.
	= \frac{m^k}{t^k!} \qquad \textsf{for } t \in \reals_{++} \text{ and } k \in \integers_+.
$$

At this point, we've got the result because this holds for all $$k$$, in particular, 
when $$k = 0$$. So, when we sum up the right-hand-side of the inequality 
(from $$k=0$$ to $$\infty$$) and then take the infimum over $$\lambda$$, we're still 
larger than the (optimal) moment bound.

**Tips and tricks.**

For the analysts out there, there's a nice result on ratios of series that provides
(precise) justification for the result. Assume that $$\sum_{k=0}^\infty x_k$$ and 
$$\sum_{k=0}^\infty z_k$$ are finite and that $$c \le x_k / z_k$$ for all $$k$$. Then
it can be shown that

$$
c \le \frac{\sum_{k=0}^\infty x_k}{\sum_{k=0}^\infty z_k}
	\quad \text{and} \quad
c < \frac{\sum_{k=0}^\infty x_k}{\sum_{k=0}^\infty z_k}
$$

when $$x_i / z_i \neq x_j / z_j$$ for at least one $$i$$ and $$j$$ pair.

We can use this result by setting 

$$c = \min_{q \in \integers} \E(Y^q)t^{-q}, \qquad
	x_k = \frac{\lambda^k m^k}{k!}, \qquad 
	z_k = \frac{\lambda^k t^k}{k!},$$

and noting that the ratio $$x_k/z_k$$ changes for different values of $$k$$.

### Final thoughts

Although moment bounds can be much tighter than Cramér--Chernoff bounds, working with 
the latter is often (much, much) easier. For example, Cramér--Chernoff bounds play 
nicely with sums of random variables.

Interestingly, Markov's inequality and Chebyshev's inequality, *i.e.*,

$$
\prob(Y \ge t) \le \frac{\E Y}{t} \quad \text{and} \quad
\prob(|Y - \E Y| \ge t) \le \frac{\Var(Y)}{t^2}
$$

are often not as sharp as Cramér--Chernoff bounds. 

For example, let $$Y \sim \text{Poisson}(\beta)$$. The mean of $$Y$$ is 
$$\beta$$ and the variance is $$\beta$$. The moment generating function of the centered
version of $$Y$$, *i.e.*, $$Z = Y - \beta$$ is

$$
\E e^{\lambda Y} = e^{-\lambda \beta - \beta} e^{\beta e^\lambda}.
$$

The optimal value of $$\lambda$$ which minimizes this expression is 
$$\lambda = \log(1 + t/\beta)$$. So, we have that

$$
\inf_{\lambda > 0} \E e^{\lambda(Y-t)} = e^t(1+t/\beta)^{-(\beta+t)}.
$$

Say $$\beta = 100$$ and we set $$t = 20$$.

* Markov: 100/20 = 5, which is useless;
* Chebyshev: 100/400 = 0.25;
* Cramér--Chernoff: see formula above = 0.153;
* actual: 0.023.

Now turn up $$t$$ to 50.

* Markov: 100/50 = 2, which is still useless;
* Chebyshev: 100/2500 = 0.004;
* Cramér--Chernoff: $$2\times10^{-5}$$;
* actual: $$1.23\times10^{-6}$$.

### References

This post is based on material from:

* *Concentration Inequalities: A Nonasymptotic Theory of Independence* by 
  S. Boucheron, G. Lugosi, and P. Masart.