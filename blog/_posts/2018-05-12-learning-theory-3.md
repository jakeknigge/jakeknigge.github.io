---
layout: post
title:  "Learning theory - reconciliations and connections"
date:   2018-05-12 21:00:00 -0700
categories: jekyll update
---
Howdy learning theorists! Today we're going to connect statistical learning and online
learning. We'll do via model averaging and exploiting online learning's local perspective.
To that end, this post could have easily been called "the power of local optimization",
which has a nice ring to it.

Our goal is address a few questions that pop up when the online learning approach is
unfamiliar.

1. What does the online learning regret bound mean?
2. How can that bound be used?
3. How well does the loss rate on the model sequence generalize?

### The approach

Because online learning generates a sequence of models rather than a single model, we
have to pick a model to compare to the empirical risk minimizer. In particular, we'll
choose the average model. Then we can use the online analysis machinery to provide a
risk bound on that model.

Assume we have

* an $$n$$-dimensional sample $$S = \{(x_1,y_1),\dots,(x_n,y_n)\}$$ drawn
  i.i.d. from a training set $$\mathcal{D}$$;
* a loss function $$l_\mathcal{D}(w)$$ for linear models $$w$$;
* a "best" model $$u^\star = \argmin_{u: \|u\| \le U} l_\mathcal{D}(u)$$.

We want to control the differences

$$
l_\mathcal{D}(\hat{w}) - l_\mathcal{D}(u^\star),
$$

where $$\hat{w}$$ is the average model. Here's how we get $$\hat{w}$$.

* run online gradient descent on our sample set $$S$$;
* obtain a sequence of models $$w_1,\dots,w_n$$;
* compute the average model

$$
\hat{w} = \frac{1}{n} \sum_{i=1}^n w_i.
$$

**Aside on averaging.** Online analysis gives insight on the behavior of the model
sequence but not on any one particular model. So, we need to consolidate our sequence
into something we can analyze. Moreover, averaging is a good way (think of Leo Breiman
and **bagging**) to reduce variance without impacting an estimator's bias.

### Expectation bounds

We start by showing that the expected value of the loss of the average model is less
than or equal to the average loss on the model sequence via Jensen's inequality:

$$
\begin{align*}
l_\mathcal{D}\big( \hat{w} \big)
	&= \E \left[ l\left( \frac{1}{n} \sum_{t=1}^n w_t, (X,Y) \right) \right] \\
	&\le \frac{1}{n} \sum_{t=1}^n \E \, l\big(w_t, (X,Y) \big) \\
	&= \frac{1}{n} \sum_{t=1}^n l_\mathcal{D}(w_t).
\end{align*}
$$

Cool, now we have something to compare to the best model in the class! But something feels
funny because we have something i.i.d. and something sequential---martingales to the
rescue!

### Martingale differences

Martingales are perfectly equipped for taming our sequential process. In particular,
we'll define the **conditional expectation** up to time $$t-1$$ as

$$
\E_{t-1} [\, \cdot \,] = \E[\, \cdot \mid z_1,\dots,z_{t-1}].
$$

For now, let's focus on time $$t$$ and take the expectation

$$
\E_{t-1} \big[ \color{red}{l_\mathcal{D}(w_t)}
	- \color{royalblue}{l\left(w_t, (x_t, y_t) \right)} \big] = 0
$$

where $$\color{red}{l_\mathcal{D}(w_t)}$$ is the $$t$$th model and
$$\color{royalblue}{l\left(w_t, (x_t, y_t) \right)}$$ is the expected loss on the next
example. The expectation is zero because

* the first $$t-1$$ examples are held fixed, meaning $$l_\mathcal{D}(w_t)$$ is a constant;
  and
* the expected loss, *i.e.*, risk, at time $$t$$ is
  $$\E \, \color{royalblue}{l\left(w_t, (x_t, y_t) \right)} = l_\mathcal{D}(w_t)$$ because
  our data is i.i.d. from $$\mathcal{D}$$.

Now, we average across our $$n$$ examples

$$
\frac{1}{n} \sum_{t=1}^n
	\E_{t-1} \big[ l_\mathcal{D}(w_t) - l\left(w_t, (x_t, y_t) \right) \big]
	\stackrel{\textsf{notation}}{=}
	\frac{1}{n} \sum_{t=1}^n \E_{t-1} Z_t = 0
$$

because $$\E_{t-1} Z_t = 0$$ for each $$t$$. The random variables $$Z_1,\dots,Z_n$$ form
a **martingale difference sequence**, which means we can martingale concentration results
to bound the average. In particular, with probability at least $$1 - \delta$$,

$$
\frac{1}{n} \sum_{t=1}^n Z_t \le \E \frac{1}{n} \sum_{t=1}^n Z_t +
	\mathcal{O} \left( \sqrt{\frac{1}{n} \ln \frac{1}{\delta}} \right).
$$

With this fact, we can bound the risk of the average model!

### Bounding via online analysis

Thanks to our martingale convergence result, this is a plug-and-play step. So, with
probability at least $$1- \delta$$ with respect to the random draw of our sample $$S$$,

$$
\begin{align*}
l_\mathcal{D}(\hat{w}) &\le \frac{1}{n} \sum_{t=1}^n l_\mathcal{D}(w_t) \\
	&\le \frac{1}{n} \sum_{t=1}^n l\big(w_t, (x_t, y_t)\big) +
	\mathcal{O} \left( \sqrt{\frac{1}{n} \ln \frac{1}{\delta}} \right).
\end{align*}
$$

**Neural connection.** This is the same object as what we had in the online learning
analysis because we have a loss rate on the first $$n$$ models in the sequence---and
all we needed was a slightly modified (*i.e.*, *martingalified*) concentration result!

Our last step is to use online analysis to bound the loss rate

$$
\frac{1}{n} \sum_{t=1}^n l\big(w_t, (x_t, y_t)\big),
$$

which will give us something that we can compare to the empirical risk minimizer. To make
things concrete, we'll look at a specific example.

### Linear regression with squared loss

We're going to show that

$$
\frac{1}{n} \sum_{t=1}^n l\big(w_t, (x_t, y_t)\big) \le
	\min_{u: \|u\| \le U} \frac{1}{n} \sum_{t=1}^n l\big(u, (x_t, y_t)\big) +
		\delta (UX)^2 \sqrt{\frac{2}{T}}.
$$

We'll define $$u^\star_t$$ as the minimizer of
$$\frac{1}{n} \sum_{t=1}^n l\big(u, (x_t, y_t)\big)$$, which allows us to say that

$$
\frac{1}{n} \sum_{t=1}^n l\big(u_n^\star, (x_t, y_t)\big) \le
	\frac{1}{n} \sum_{t=1}^n l\big(u^\star, (x_t, y_t)\big),
$$

where $$u^\star$$ is the model with the smallest statistical risk over $$\mathcal{D}$$,
not just $$S$$. So, $$u^\star$$ may not have the smallest risk over $$S$$.
(**Neural connection!** This is the same trick we used in the Rademacher analysis in the
[statistical learning theory post][lt1-post]!)

Via the Chernoff bound, with probability at least $$1-\delta$$ we have that

$$
\frac{1}{n} \sum_{t=1}^n l\big(u_n^\star, (x_t, y_t)\big) \le
	l_\mathcal{D}(u^\star) +
	\mathcal{O} \left( \sqrt{\frac{1}{n} \ln \frac{1}{\delta}} \right).
$$

This last expression yields the bound on the average model:

$$
l_\mathcal{D}(\hat{w}) - l_\mathcal{D}(u^\star) \le + c\frac{UX^2}{\sqrt{n}}
	\mathcal{O} \left( \sqrt{\frac{1}{n} \ln \frac{1}{\delta}} \right),
$$

where $$c$$ is a constant. This is the same bound obtained from the Rademacher analysis!

### Final thoughts

* The average loss on a sequence of models is a good proxy for the risk of the average
  model.
* Local optimization performs well (up to constant factors), compared to empirical risk
  minimization, *i.e.*, globabl optimization.
 	+ So, local optimization isn't doing anything crazy---it's doing something quite
	  similar to global optimization.
	+ In other words, doing local optimization and then averaging is a good and viable
	  strategy, especially when we're dealing with big problems.

Now, let's revisit our questions.

1. What does the online learning regret bound mean?
	+ In some sense, it means that we're bounding an object not unlike the empirical
	  risk.
2. How can that bound be used?
	+ The online learning bound can be used to bound an *average* model, which is
	  comparable to a model found via empirical risk minimization.
3. How well does the loss rate on the model sequence generalize?
	+ Up to constant factors, it generalizes as well as well the model found through
	  empirical risk minimization.

### Next up

... a short and sweet example of how strong convexity speeds things up in the online
learning setting! Then a foray into the mind of R.A. Fisher with Brad Efron's help.

### References

This post is related to material from Nicolò Cesa-Bianchi's lectures at the Bordeaux
Machine Learning Summer School in 2011. Watch them here: [MLSS 2011 lectures][ncb-lecs].

[ncb-lecs]: http://videolectures.net/mlss2011_cesa_bianchi_learningtheory/
[rad1-post]: /jekyll/update/2018/03/04/rademacher.html
[rad2-post]: /jekyll/update/2018/03/06/rademacher_2.html
[lt1-post]: /jekyll/update/2018/05/06/learning-theory-1.html
