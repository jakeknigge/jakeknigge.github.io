---
layout: post
title:  "The entropy method"
date:   2018-04-29 21:00:00 -0700
categories: jekyll update
---
Welcome back! This post is going to start us down the path of the **entropy method**
for obtaining concentration inequalities. The idea is pretty straightforward. We
take a (relevant) modified logarithmic Sobolev inequality and, via a Herbst-type 
argument, derive exponential concentration inequalities.

In a deviation from previous posts, we're going to take as given the concentration
behavior (*i.e.*, we're doing an example instead of a proof) and look at a neat 
application that puts it to work.

### An eigenvalue bound

The largest eigenvalue of a random symmetric matrix is a quantity that naturally
arises in many "modern" statistical problems---*e.g.*, in random correlation matrices. 
We can describe the behavior of the variance of this eigenvalue using the Efron--Stein 
inequality; however, we can obtain concentration convergence rates using the entropy
method. In particular, we'll use bounded differences-inspired condition to evoke
a fast concentration inequality.

Assume we have independent random $$X_{ij}, 1 \le i \le j \le n$$ that are bounded
by 1 in absolute value. The symmetric matrix $$A$$ has entries $$X_{ij}$$. Our 
object of interest is

$$
Z = \lambda_{\max} = \sup_{\|u\| = 1} u^T A u = v^T A v,
$$

where $$v$$ is the eigenvector associated with $$\lambda_{\max}$$. Assume that
$$\tilde{X}_{ij}$$ is an independent copy of $$X_{ij}$$ and $$\tilde{A}_{ij}$$
is the matrix $$A$$ with entropy $$(i,j)$$ replaced by the independent copy.
Similarly, let $$\tilde{Z}_{ij}$$ be the maximum eigenvalue of $$\tilde{A}_{ij}$$.
Here's where it starts to get good.

In the spirit of bounded differences, we can calculate

$$
\begin{align*}
\big(Z - \tilde{Z}_{ij}\big)_+ &\le 
	(v^TAv - v^T\tilde{A}_{ij}v) \ind{} \{Z > \tilde{Z}_{ij}\} \\
	&= \big( v^T(A-\tilde{A}_{ij})v \big) \ind{} \{Z > \tilde{Z}_{ij}\} \\
	&\le 2 \left( v_i v_j \left( X_{ij} - \tilde{X}_{ij} \right) \right)_+ \\
	&\le 4 |v_i v_j|.
\end{align*}
$$

The first line results from the assumption on $$v$$ and the use of the indicator variable.
The third line follows from a rewriting of the matrix multiplication and the observation
that all but two entries of the sum cancel each other. 

If we throw this guy into the Efron--Stein inequality, we get that

$$
\Var(Z) \le 16.
$$

Now, we can use the logarithmic Sobolev machinery to get the exponential
concentration inequality

$$
\prob(Z - \E Z > t) \le e^{-t^2 / (2c)} = e^{-t^2 / (32)},
$$

where $$c$$ is a constant that bounds $$\sum_{i=1}^n (Z - \tilde{Z}_{ij})^2$$.

### Linear algebra aside

A theorem from linear algebra tells us that the maximum eigenvalue of a symmetric
matrix is upper-bounded by the largest row sum and lower-bounded the smallest row
sum. So, the above ideas become interesting and useful when have big matrices...
precisely the kind arising in *modern* applications!

### Next up

... honestly, I'm not sure! I'm thinking we could start to unpack the machinery
that supported our blind use of the entropy method. Or it could be about bandits,
prediction with expert advice, Markov chains, something from convex optimization...


### References

This post is related to material from Chapters 3 and 6 of:

* *Concentration Inequalities: A Nonasymptotic Theory of Independence* by 
  S. Boucheron, G. Lugosi, and P. Masart.

  
[lsi-post]: /jekyll/update/2018/04/07/lsi.html