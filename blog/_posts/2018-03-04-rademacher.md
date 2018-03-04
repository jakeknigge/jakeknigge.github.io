---
layout: post
title:  "Rademachers are rad - part I"
date:   2018-03-04 20:00:00 -0700
categories: jekyll update
---
In this post, we're going to play with Rademacher random variables, which are
quite useful in many problems that arise in "modern" statistics and machine 
learning problems. They also pop up in empirical process theory and geometry.
So, it's safe to say that we should at least be acquainted with them.

In case Rademacher random variables are new to you, please meet the Rademacher 
random variable $$Y$$, which takes the values $$+1$$ and $$-1$$ with probability
$$1/2$$. For those of you who prefer a more formal introduction, please meet the
probability mass function of $$Y$$

$$
f_Y(y) = 
\begin{cases}
+1, & \textsf{with probability } 0.5 \\
-1, & \textsf{with probability } 0.5.
\end{cases}
$$

Sometimes Rademacher random variables introduce themselves as *random sign 
variables*. Now, that we've been properly introduced, let's move on to the fun 
stuff.

### Rademacher averages

To build a Rademacher average, we'll need a collection of real numbers that
are indexed by the number of variables we have $$n$$ and an index set 
$$\mathcal{T}$$. We'll also need some Rademacher variables $$Y_1,\dots,Y_n$$.
The **Rademacher average** is

$$
Z = \sup_{t \in \mathcal{T}} \sum_{i=1}^n a_{i,t} Y_i.
$$

The random variable $$Z$$ depends on the $$Y_i$$ in a somewhat complicated way
but we can see that $$Z$$ cannot change by much if we changed the value of a
single $$Y_i$$. In particular, when we flip the value of $$Y_i$$, $$Z$$ can 
only change by $$2\sup_{t \in \mathcal{T}} |a_{i,t}|$$. (The 2 comes from the
Rademacher variable and the supremum-absolute value combo reflects the 
"maximum" change.)

We can use this **bounded differences property** and the Efron--Stein inequality
to bound the $$\Var(Z)$$ even though we can't say much about the behavior of 
$$\E Z$$, which is pretty rad.

In particular, we can say that 

$$
\Var(Z) \le \sum_{i=1}^n \frac{1}{4} \left( 
		 	2\sup_{t \in \mathcal{T}} |a_{i,t}| \right)^2 =
		 	\sum_{i=1}^n \sup_{t \in \mathcal{T}} a_{i,t}^2.
$$

The inequality results from the bounded differences inequality (which we can
prove using the Efron--Stein inequality).

But... we can do better.

#### Sneaking the supremum past the sum

Now, we define $$X_1,\dots,X_n$$ as an independent copy of $$Y_1,\dots,Y_n$$.
We'll also set $$t^\star$$ to be the (possibly random) index that achieves
the supremum, *i.e.*,

$$
\sup_{t \in \mathcal{T}} \sum_{i=1}^n a_{i,t} Y_i 
	= \sum_{i=1}^n a_{i,t^\star} Y_i.
$$

So, we can bound the difference between $$Z$$ and $$Z$$ with the $$i$$th
coordinate changed from $$Y_i$$ to $$X_i$$. That is,

$$
Z - Z_i \le (Y_i - X_i) a_{i,t^\star}
\implies
(Z - Z_i)_+^2 \le (Y_i - X_i)^2 a_{i,t^\star}^2.
$$

where in the implied inequality we square only the positive portion of
$$Z - Z_i$$.

Now, we'll use an expectation trick and the independence of $$X_i$$ and $$Y$$
to say 

$$

\E \left[ (Z - Z_i)_+^2 \right] \le 
	\E \left[ \E \left( 
		(Y_i - X_i)^2 a_{i,t^\star}^2 \mid Y_1,\dots,Y_n)
	\right) \right] =
	2 \E \left[ a_{i,t^\star}^2 \right].
$$

We do the expectation-conditioning combo on $$Y_1,\dots,Y_n$$ to get rid of all
randomness expect for that of $$t^\star$$.

Finally, we use the Efron--Stein inequality to say

$$
\Var(Z) \le 2 \E \left( \sum_{i=1}^n a_{i,t^\star}^2 \right) \le 
	2 \sup_{t \in \mathcal{T}} \sum_{i=1}^n a_{i,t}^2.
$$

So, through some Efron--Stein inspired analysis, we moved the supremum outside
the sum at the expense of a factor of 2.

### References

This post is based on material from Chapter 3 of:

* *Concentration Inequalities: A Nonasymptotic Theory of Independence* by 
  S. Boucheron, G. Lugosi, and P. Masart.

#### Bounded differences property

The bounded differences property states that the value of the function changes
by a bounded amount if the value of the $$i$$th variable is changed. A function
$$f: \mathcal{S}^n \to \reals$$ satisfies the bounded differences property if

$$
\sup_{\substack{x_1,\dots,x_n \\ x_i' \in \mathcal{S}}}
	| f(x_1,\dots,x_n) - f_i(x_1,\dots,x_{i-1},x_i',x_{i+1},\dots,x_n) |
	\le c_i,
$$

for $$i = 1,\dots,n$$ and constants $$c_1,\dots,c_n$$.