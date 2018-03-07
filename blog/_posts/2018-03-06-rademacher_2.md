---
layout: post
title:  "Rademachers are rad - part II"
date:   2018-03-06 20:00:00 -0700
categories: jekyll update
---
In this post, we're going to work through another Rademacher-related problem.
Like the previous post, the problem highlights the usefulness of the 
Efron--Stein inequality.

### Conditional Rademacher averages

In this section, we're going to continue with a close cousin of the Rademacher
average called the **conditional Rademacher average**, which are used in high 
dimensional statistics to measure the complexity of model classes. Its defined
as

$$
Z = \E \left[ \max_{j=1,\dots,d} \sum_{i=1}^n Y_i X_{i,j} \mid
	X_1,\dots,X_n \right]
$$

where $$Y_i$$ are Rademacher random variables and the $$X_{i,j}$$ are 
independent random variables that live in $$[-1,1]$$. The random variable $$Z$$
is a function of the $$X_{i} \in [-1,1]^d$$.

Without lifting a finger, we can call our bound differences inequality friend
and bound the variance of $$Z$$ as

$$
\Var(Z) \le n/4,
$$

but that's less-than-satisfying at this point in time. Instead, we can improve
on this bound by showing that $$Z$$ satisfies the **self-bounding property**
via the Efron--Stein inequality.

First, let's define

$$
Z_k = \E \left[ \max_{j=1,\dots,d} 
	\sum_{\substack{i=1 \\ i \neq k}}^n Y_i X_{i,j} \mid X^{(k)}\right]
$$

and observe that $$0 \le Z - Z_k \le 1$$ because the maximum value contributed
by $$X_k$$ to the sum is 1. By summing the inequality and using a convexity
argument (see below), we get that

$$
\sum_{k=1}^n Z - Z_k \le Z.
$$

Now, we can use the Efron--Stein inequality to say

$$
\begin{align*}
\Var(Z) &\le \E \left[ \sum_{k=1}^n (Z - Z_k)^2 \right] \\
	&\le \E \left[ \sum_{k=1}^n (Z - Z_k) \right] 
	\qquad \textsf{(because $(Z - Z_k)^2 \in [0,1]$)}\\
	&\le \E Z,
\end{align*}
$$

which shows that $$Z$$ is a self-bounding function.

### References

This post is based on material from Chapter 3 of:

* *Concentration Inequalities: A Nonasymptotic Theory of Independence* by 
  S. Boucheron, G. Lugosi, and P. Masart.

#### Self-bounding property

Loosely speaking, the self-bounding property states that the difference 
between a function and a *similar* function that omits one variable is bounded
between 0 and 1.

Formally, a nonnegative function $$f: \mathcal{S}^n \to [0,\infty)$$ satisfies
the self-bounding property when functions $$f_i: \mathcal{S}^{n-1} \to \reals$$
exist and the following two conditions hold:

$$
0 \le f(x_1,\dots,x_n) - f_i(x_1,\dots,x_{i-1},x_{i+1},\dots,x_n) \le 1
$$

and

$$
\sum_{i=1}^n f(x_1,\dots,x_n) - f_i(x_1,\dots,x_{i-1},x_{i+1},\dots,x_n) 
	\le f(x_1,\dots,x_n)
$$

for $$i = 1,\dots,n$$ and $$x_1,\dots,x_n$$.

If we combine the first and second conditions, we can say that

$$
\sum_{i=1}^n
\big( f(x_1,\dots,x_n) - f_i(x_1,\dots,x_{i-1},x_{i+1},\dots,x_n) \big)^2 
	\le f(x_1,\dots,x_n),
$$

which means we can use the Efron--Stein inequality to bound the variance
by the expected value.

#### Convexity argument

The convexity argument used in the conditional Rademacher averages example
goes as follows:

$$
\begin{align*}
\sum_{k=1}^n Z - Z_k &= \sum_{k=1}^n 
\E \left[ \max_{j=1,\dots,d} \sum_{i=1}^n Y_i X_{i,j} 
	\, \middle| \,
	X_1,\dots,X_n \right] - \E \left[ \max_{j=1,\dots,d} 
	\sum_{i \neq k}^n Y_i X_{i,j} 
	\middle| 
	X^{(k)}\right] \\

&= \sum_{k=1}^n \E \left[ \max_{j=1,\dots,d} \sum_{i=1}^n Y_i X_{i,j} -  			 	
	\max_{j=1,\dots,d} \sum_{i \neq k}^n Y_i X_{i,j} 
	\, \middle| \,
	X_1,\dots,X_n \right] \\

&\le \sum_{k=1}^n \E \left[ 
	 Y_i X_{i,j^\star}
	\, \middle| \,
	X_1,\dots,X_n \right] \\

&\le \E \left[ \max_{j=1,\dots,d} \sum_{i=1}^n Y_i X_{i,j} 
	\, \middle| \,
	X_1,\dots,X_n \right] \\

&= Z,
\end{align*}
$$

where 

$$ 
k^\star = \argmax_{k=1,\dots,d} \sum_{i = 1}^n Y_i X_{i,k}
$$

and

$$
0 \le \max_{j=1,\dots,d} \sum_{i=1}^n Y_i X_{i,j} -  			 	
	\max_{j=1,\dots,d} \sum_{i \neq k}^n Y_i X_{i,j}
	= Y_i X_{i,j^\star} - Y_k X_{k,j^\star} \le 1.
$$

#### R simulation

If you want to play around with conditional Rademacher averages, here's some
R code to do it.

```r
n_trials <- 1000
n <- 5
d <- 20
Z <- rep(0, n_trials/10)
EZ <- rep(0, n_trials)

# simulate X variables
X <- matrix(runif(n*d, -1, 1), nrow = n)

for(j in 1:n_trials){
      for(k in 1:n_trials/10){
            y <- rbinom(n, 1, 0.5)
            y[y == 0] <- -1
            yX <- y %*% X
            Z[k] <- max(colSums(yX))
      }
      EZ[j] <- mean(Z)
}

hist(EZ)
mean(EZ)
var(EZ)
```