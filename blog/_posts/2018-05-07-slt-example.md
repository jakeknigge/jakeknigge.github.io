---
layout: post
title:  "Statistical learning theory - complexity example"
date:   2018-05-07 21:00:00 -0700
categories: jekyll update
---
This post illustrates how we use Rademacher complexities in statistical learning theory.
To that end, we'll assume that we're working with our toolbox developed in
[last post][lt1-post].

### A quick recap

Last post, our main result was that

$$
\E_S \left[\sup_{f \in \mathcal{F}} \big\{ R_{\mathcal{D}}(f) - R_n(f) \big\}\right] \le
	2 \E_S \E_\sigma \left[ \sup_{f \in \mathcal{F}} \frac{1}{n}
					\sum_{i=1}^n \sigma_i l_f(z_i) \right],
$$

which allowed us to bound the statistical risk as

$$
R_{\mathcal{D}}(f_n^\star) - R_{\mathcal{D}}(f^\star) \le
	2 \E_S \E_\sigma \left[ \sup_{f \in \mathcal{F}} \frac{1}{n}
					\sum_{i=1}^n \sigma_i l_f(z_i) \right] +
	\mathcal{O} \left( \sqrt{\frac{1}{n} \ln \frac{1}{\delta}} \right),
$$

with at least probability $$1-\delta$$. In other words, Rademacher complexity helps
us measure the tendency of a model class to overfit a sample.

### Warm up

Let's Rademacherize our minds with a straightforward application of last post's bound.
We'll assume that our loss functions are $$L$$-Lipschitz. With this and this alone, we
can show that

$$
\E_\sigma \left[ \sup_{f \in \mathcal{F}} \frac{1}{n}
	\sum_{i=1}^n \sigma_i l_f(z_i) \right] \le
	L \E_\sigma \left[ \sup_{f \in \mathcal{F}} \frac{1}{n}
		\sum_{i=1}^n \sigma_i f(x_i) \right].
$$

This is cool for at least two reasons. The first is that the righthand side doesn't
depend on $$y$$ anymore---only $$x$$. The second is that the expectation no longer
depends on $$l$$, but only on $$f$$.

### Linear regression with squared loss

To make these ideas concrete, we'll look at one of the most commone models arising in
statistics / machine learning: ordinary linear regression. Here's our set up.

* Our class of functions is $$\mathcal{F} = \{\theta \mid x \mapsto \theta^T x\}$$ where
  $$\|\theta\| \le B_\theta$$ and $$\|x\| \le B_x$$.
* By assumption on $$\theta$$ and $$x$$, we have $$\|y\| \le B_\theta B_x$$.
* Our loss function $$l(\theta^T x, y) = (\theta^T x - y)^2$$, with $$L = 4B_\theta B_x$$.

We're interested in the Rademacher complexity of our function class on $$n$$ samples
$$S = \{(x_1,y_1),\dots,(x_n,y_n)\}$$, *i.e.*,

$$
\begin{align*}
\mathcal{R}(\mathcal{F}, S) &= \E_\sigma \left[ \sup_{\theta: \theta \le B_\theta}
 	\frac{1}{n} \sum_{i=1}^n \sigma_i \theta^T x_i \right] \\
	&= \frac{1}{n} \E_\sigma \left[ \sup_\theta \theta^T \left(
		\sum_{i=1}^n \sigma_i x_i \right) \right]
		&& {\small \textsf{(linearity)}} \\
	&= \frac{1}{n} \E_\sigma \left[ B_\theta \left\|
		\sum_{i=1}^n \sigma_i x_i \right\| \right]
		&& {\small \textsf{(bound on $\theta$)}} \\
	&= \frac{B_\theta}{n} \E_\sigma \sqrt{ \left\|
		\sum_{i=1}^n \sigma_i x_i \right\|^2 }
		&& {\small \textsf{(square-square root trick)}} \\
	&\le \frac{B_\theta}{n} \sqrt{\E_\sigma \left\|
		\sum_{i=1}^n \sigma_i x_i \right\|^2}
		&& {\small \textsf{(Jensen for concave functions)}} \\
	&\le \frac{B_\theta}{n} \sqrt{n B_x^2}
		&& {\small \textsf{(independence of $\sigma_i$ and $\|\sigma_i\|=1$)}}\\
	&= \frac{B_\theta B_x}{\sqrt{n}} \\
	&= \frac{\textsf{size of model class}}{\textsf{convergence rate}}.
\end{align*}
$$

The trickiest step is the one that exploits the independence of the Rademacher
random variables and the bound on their norm. It's easy to see if we write out
the double sum

$$
\sum_k \sum_j \sigma_k \sigma_j x_k^T x_j = \sum_k \sigma_k^2 x_k^T x_k +
	\sum_{j \neq k} \sigma_j \sigma_k x_j^T x_k = \sum_k x_k^T x_k,
$$

because the $$\sigma_j$$ and $$\sigma_k$$ are independent.

### Putting it all together

From last post and the above calculation, we conclude that with probability at least
$$1 - \delta$$

$$
\begin{align*}
R_{\mathcal{D}}(f_n^\star) - R_{\mathcal{D}}(f^\star) &\le
	\frac{8(B_\theta B_x)^2}{\sqrt{n}} +
	\mathcal{O}\left( \sqrt{\frac{1}{m} \ln \frac{1}{\delta}} \right) \\
	&=\mathcal{R}(\mathcal{F}, S) L +
	\mathcal{O}\left( \sqrt{\frac{1}{m} \ln \frac{1}{\delta}} \right),
\end{align*}
$$

for empirical risk minimization. Hot diggity!

### Next up

... the online learning perspective!

### References

This post is related to material from Nicolò Cesa-Bianchi's lectures at the Bordeaux
Machine Learning Summer School in 2011. Watch them here: [MLSS 2011 lectures][ncb-lecs].

[ncb-lecs]: http://videolectures.net/mlss2011_cesa_bianchi_learningtheory/
[rad1-post]: /jekyll/update/2018/03/04/rademacher.html
[rad2-post]: /jekyll/update/2018/03/06/rademacher_2.html
[lt1-post]: /jekyll/update/2018/05/06/learning-theory-1.html
