---
layout: post
title:  "Learning theory - online learning"
date:   2018-05-10 21:00:00 -0700
categories: jekyll update
---
Welcome back! Today's post focuses on the **online learning** perspective of learning.
The online learning approach differs from statistical learning because it makes no
assumption on the data source. In other words, the process generating the data is
arbitrary![^1] Instead of focusing on the data source, online learning makes assumptions
on the sequence of loss functions to attain some level of tractability. Yep, you read
that correctly: we will have a sequence of loss functions---not just one to
*rule them all*.

### A protocol for online learning

Here's how our online learning world works.

* An algorithm generates an initial model $$w_1$$.
* At each time $$t = 1, 2, \dots$$, the model is tested on a data point.
* After being tested, the algorithm updates $$w_t \to w_{t+1}$$.

The second step suggests that we're going to need some notion of loss---*i.e.*, something
like risk---to help us *test* our model. For our purposes, we'll assume that
the loss functions we encounter are **convex**, **nonnegative**, and **differentiable**.

For application-minded readers, model spaces are typically linear but can be finite
dimensional (*e.g.*, regression or support vector machine parameters) or infinite
dimensional, via reproducing Kernel Hilbert spaces.[^2]

### Assessing performance

We're going to assess the performance of our (model-generating) algorithm via its
**loss rate**, defined as

$$
\frac{1}{T} \sum_{t=1}^T l_t(w_t).
$$

But this is only part of the story. A loss rate by itself is uninteresting because
losses are arbitrary. So, we'll focus on controlling our **regret**

$$
\frac{1}{T} \sum_{t=1}^T l_t(w_t) - l_t(w^\star_T),
$$

where

$$
w^\star_T = \argmin_{u: \|u\| \le U} \frac{1}{T} \sum_{t=1}^T l_t(u)
$$

is the best model in the class. When the regret goes to zero, then we are learning and
also experiencing some type of consistency.

### The algorithm of choice - online gradient descent

**Online gradient descent** (OGD) is akin to empirical risk minimization in statistical
learning theory, but it's a bit different than typical gradient descent because the losses
can vary over time. Nonetheless, the algorithm is quite simple.

---
**Algorithm** *online gradient descent.*

* **given** parameters $$\alpha_1,\alpha_2,\dots$$, a radius $$U$$, and an initial model
$$w_1 = 0$$.
* **repeat** for $$t=1,2,\dots$$
	* compute a tentative gradient step:

	$$\tilde{w}_{t+1} = w_t - \alpha_t \nabla l_t(w_t).$$

	* project back to the model space:

	$$w_{t+1} = \argmin_{w: \|w\| \le U} \|w - \tilde{w}_{t+1}\|.$$

---

**Neural connection.** Online gradient descent is similar to stochastic gradient except
there's no stochasticity in the data.

**Food for thought.**
Online gradient descent is a local optimization perspective rather than global
optimization as in empirical risk minimization. Because of this, online gradient descent
scales gracefully to large problems.

### Analyzing online gradient descent

Let's fix a time $$t$$ and analyze the instantaneous regret

$$
l_t(w_t) - l_t(u).
$$

By convexity, the first-order Taylor expansion of $$u$$ around $$w_t$$ gives,

$$
l_t(w_t) - l_t(u) \le \nabla l_t (w_t)^T (w_t - u).
$$

This is pretty much the definition of convexity.[^3] By the OGD algorithm, we
calculate

$$
\begin{align*}
l_t(w_t) - l_t(u)
&\le \nabla l_t (w_t)^T (w_t - u) \\
&= \frac{1}{\alpha_t} (\tilde{w}_{t+1} - w_t)^T (w_t - u) \\
&\stackrel{\textsf{(a)}}{=}
	\frac{1}{\alpha_t} \left( \frac{1}{2} \|w_t - u\|^2
		- \frac{1}{2} \|\tilde{w}_{t+1} - u\|^2
		+ \frac{1}{2} \|\tilde{w}_{t+1} - w_t\|^2 \right) \\
&\stackrel{\textsf{(b)}}{\le}
	\frac{1}{\alpha_t} \left( \frac{1}{2} \|w_t - u\|^2
		- \frac{1}{2} \|w_{t+1} - u\|^2
		+ \frac{1}{2} \|\tilde{w}_{t+1} - w_t\|^2 \right) \\
&\stackrel{\textsf{(c)}}{=}
	\frac{1}{2\alpha_t} \|w_t - u\|^2
		- \frac{1}{2\alpha_{t+1}} \|w_{t+1} - u\|^2
		- \frac{1}{2\alpha_t} \|w_{t+1} - u\|^2 \\
		&\qquad + \frac{1}{2\alpha_{t+1}} \|w_{t+1} - u\|^2
		+ \frac{1}{2\alpha_t} \|\tilde{w}_{t+1} - w_t\|^2.
\end{align*}
$$

In (a) we compare the current model at time $$t$$ to the best model $$u$$ plus a
penalty for changing models. In (b) we use the fact that projecting
$$\tilde{w}_{t+1} \to w_{t+1}$$ reduces the distance to $$u$$; since that term is
negative, we get the inequality. In (c) we add and subtract the same term for reasons
which will become clear shortly.

#### Summing it up

Now, we need to sum over $$t = 1, 2,\dots$$

$$
\begin{align*}
\sum_{t=1}^T l_t(w_t) - l_t(u) &\le
	\frac{1}{2\alpha_1} \|w_1 - u\|^2 -
	\frac{1}{2\alpha_{T+1}} \|w_{T+1} - u\|^2
		\\ &\qquad +
	\frac{1}{2} \sum_{t=1}^T \|w_{t+1} - u\|^2 \left( \frac{1}{\alpha_{t+1}} -
		\frac{1}{\alpha_t} \right)
		\\ &\qquad +
	\frac{1}{2} \sum_{t=1}^T \frac{1}{\alpha_t} \|\tilde{w}_{t+1} - w_t\|^2,
\end{align*}
$$

where $$w_1 = 0$$, $$\|w_{t+1} - u\|^2 \le 4U^2$$, and
$$\|\tilde{w}_{t+1} - w_t\|^2 = \alpha_t^2 \|\nabla l_t(w_t)\|^2$$. Continuing from our
last line and using $$\|\nabla l_t(w_t)\| \le G$$, we calculate

$$
\begin{align*}
\sum_{t=1}^T l_t(w_t) - l_t(u) &\le
	\frac{U^2}{2\alpha_1} - \frac{1}{2\alpha_{T+1}} \|w_{T+1} - u\|^2
		\\ &\qquad
	+ 2U^2 \sum_{t=1}^{T-1}
		\left( \frac{1}{\alpha_{t+1}} - \frac{1}{\alpha_t} \right)
		\\ &\qquad
	+ \frac{1}{2\alpha_{T+1}} \|w_{T+1} - u\|^2
	- \frac{1}{2\alpha_T} \|w_T - u\|^2
		\\ &\qquad
	+ \frac{G^2}{2} \sum_{t=1}^T \alpha_t,
\end{align*}
$$

by removing the last two terms,

$$\frac{1}{2\alpha_{T+1}} \|w_{T+1} - u\|^2 \quad \textsf{and} \quad
\frac{1}{2\alpha_T} \|w_T - u\|^2,$$

from the summation term involving $$\alpha_{t+1}^{-1} - \alpha_t^{-1}$$.
The $$\|w_{T+1} - u\|^2$$ terms cancel each other and we throw about the $$\|w_T - u\|^2$$
term since it's negative, leaving us with

$$
\begin{align*}
\sum_{t=1}^T l_t(w_t) - l_t(u) &\le
	\frac{U^2}{2\alpha_1} + 2U^2 \sum_{t=1}^{T-1}
		\left( \frac{1}{\alpha_{t+1}} - \frac{1}{\alpha_t} \right)
	+ \frac{G^2}{2} \sum_{t=1}^T \alpha_t \\
&\stackrel{\textsf{(a)}}{\le}
	\frac{2U^2}{\alpha_T} + \frac{G^2}{2} \sum_{t=1}^T \alpha_t,
\end{align*}
$$

where we use telescoping and the *throw-away-a-negative-term* trick to get the
inequality (a).

#### Putting it all together

We can pick our $$\alpha_t$$ terms to equate terms in the sum. Specifically,
we choose them as

$$
\alpha_t = \frac{\alpha}{\sqrt{t}}.
$$

So, choosing $$\alpha_t$$ as above, selecting $$\alpha = U\sqrt{2}/G$$, and dividing by
$$T$$, our final bound, *i.e.*, our **rate**, is

$$
\begin{align*}
\frac{1}{T} \sum_{t=1}^T l_t(w_t) - l_t(u)
	&\le \frac{2U^2}{\alpha\sqrt{T}} + \frac{\alpha G^2}{\sqrt{T}} \\
	&= UG \sqrt{\frac{8}{T}}.
\end{align*}
$$

### Linear regression with squared loss

Let's do linear regression with squared loss using the assumptions

* $$G = 4UX^2$$,
* $$\|x_t\| \le X$$, and
* $$\|y\| \le UX$$.

Under this set up our regret is

$$8UX^2\sqrt{2/T}$$

since $$\nabla l(w) = 2(w^T x - y)x$$.
(The norm bound on $$\nabla l(w)$$ makes use of the Cauchy--Schwarz inequality.)
Now, hold on a sec... this is the same rate as the Rademacher complexity for the
statistical learning approach!

### Next up

... reconciling and connecting statistical learning and online learning!

### References

This post is related to material from Nicolò Cesa-Bianchi's lectures at the Bordeaux
Machine Learning Summer School in 2011. Watch them here: [MLSS 2011 lectures][ncb-lecs].

[ncb-lecs]: http://videolectures.net/mlss2011_cesa_bianchi_learningtheory/
[rad1-post]: /jekyll/update/2018/03/04/rademacher.html
[rad2-post]: /jekyll/update/2018/03/06/rademacher_2.html
[lt1-post]: /jekyll/update/2018/05/06/learning-theory-1.html

#### Footnotes

[^1]: If you're like me, with more of stats background, then this might be an
	"aha!" moment. If it is, take a moment to enjoy it.

[^2]: Don't worry if you've never heard of reproducing Kernel Hilbert spaces. They won't
	show up here.

[^3]: The inequality may look more familiar as
	$$l_t(w_t) - \nabla l_t (w_t)^T w_t \le l_t(u) - \nabla l_t (w_t)^T u$$.
