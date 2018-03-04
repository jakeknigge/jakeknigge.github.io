---
layout: post
title:  "A Poincaré inequality for the Laplace distribution"
date:   2018-02-24 13:30:00 -0700
categories: jekyll update
---
Lately, I've been reading and working through Boucheron, Lugosi, and Massart's
*Concentration Inequalities: A Nonasymptotic Theory of Independence*. The book is (very)
well written, self-contained, and full of lots of useful tools (*i.e.*, theorems, lemmas,
corollaries, and the like), as well as many thought provoking examples and exercises.

In this post, we're going to work through a problem from Chapter 3, which is titled
*Bounding the Variance*, that proves a Poincaré-type inequality.

### The problem

Suppose we have a random variable $$X \sim \mathrm{Laplace}(\lambda = 1)$$, with a density 
given by

$$
g(x) = \frac{1}{2}\exp(-|x|), \qquad x \in \reals.
$$

Assume that we have a differentiable function $$f$$ that has finite variance, *i.e.*,
$$\Var(f(X)) < \infty$$. We're going to prove that

$$
\Var(f(X)) < 4 \E \left[ (f'(X))^2 \right],
$$

where $$f'$$ is the derivative of $$f$$.

### The proof

First, take a moment to appreciate what this result says: the variance of the function
of a relatively heavy-tailed distribution is controlled by its expected value; in other
words, the tails of $$X$$ **don't** lead to wild behavior of $$f$$. That's cool!

We'll use the following (*integration by parts*) fact in the proof:

$$
\E \big( h(X) \big) = h(0) + E \big( \mathrm{sgn}(X) h'(X) \big),
$$

where $$\mathrm{sgn}$$ is the function that returns -1 when its argument is negative and
+1 when its argument is positive.

$$
\begin{align*}
\Var(f(X)) &\stackrel{\textsf{(a)}}{=} \Var(f(X) - f(0)) \\
	&\stackrel{\textsf{def}}{=} \Var(h(X)) \\
	&\stackrel{\textsf{(b)}}{\le} \E \left[ \big( h(X) \big)^2 \right] \\
	&\stackrel{\textsf{(c)}}{=} 2 \E \left[ \mathrm{sgn}(X) h'(X) h(X) \right] \\
	&\stackrel{\textsf{(d)}}{\le} 2 \left(\E \left[\big(h'(X)\big)^2\right]\right)^{1/2}
								  \left(\E \left[\big(h(X)\big)^2\right]\right)^{1/2}. \\	
\end{align*}
$$

Now we compare the third and fifth lines and note that

$$
\begin{align*}
\E \left[ \big( h(X) \big)^2 \right] &\le 
	2 \left(\E \left[\big(h'(X)\big)^2\right]\right)^{1/2}
	  \left(\E \left[\big(h(X)\big)^2\right]\right)^{1/2} \\
\left(\E \left[ \big( h(X) \big)^2 \right]\right)^{1/2} &\le 
	2 \left(\E \left[\big(h'(X)\big)^2\right]\right)^{1/2} \\
\E \left[ \big( h(X) \big)^2 \right] &\le 
	4 \E \left[\big(h'(X)\big)^2\right].
\end{align*}
$$

Finally, we chain everything together and we complete the proof: 

$$
\Var(f(X)) \le 4 \E \left[\big(h'(X)\big)^2\right].
$$

**Tips and tricks.**

* In **(a)** we used that the variance of a random variable is not changed when a constant
  is added to the random variable, *i.e.*, when we translate it.
* In **(b)** we used that $$\Var(Y) + (\E Y)^2 = \E(Y^2)$$.
* In **(c)** we used the *integration by parts* fact.
* In **(d)** we used the Cauchy-Schwarz inequality.

### References

This post is based on material from:

* *Concentration Inequalities: A Nonasymptotic Theory of Independence* by 
  S. Boucheron, G. Lugosi, and P. Masart.