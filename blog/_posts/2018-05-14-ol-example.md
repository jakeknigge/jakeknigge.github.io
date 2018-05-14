---
layout: post
title:  "Online learning theory - strong convexity"
date:   2018-05-12 21:00:00 -0700
categories: jekyll update
---
Today's post is a neat example of what *exploiting structure* can do. It ties back to
the post that introduced [online learning][lt2-post]. In that discussion, we had an
online gradient descent algorithm that relied on an auxiliary sequence of models.
We calculated the auxiliary sequence using gradient descent and then we projected back
into our model space, which was all fine and good; however, in this post we're going to
show that we can avoid that projection step when we have a loss function that is strongly
convex.

### Strong convexity

Before we defined strong convexity, try to guess what it could look like. If you imagined
a function that has positive (more precisely, nonnegative) curvature everywhere, then
you're spot on!

We'll assume that our loss function $$l$$ is $$\sigma$$-strongly, meaning

$$
l(w) - l(u) \le \nabla l(w)^T (w-u) - \frac{\sigma}{2} \|(w-u)\|^2.
$$

(Technically, strong convexity is defined with respect to a norm---in this case, it's
the $$\ell_2$$ norm.) So, strong convexity means that our function grows faster than
linearly in all directions.

### Online gradient descent

With strongly convex losses, the online gradient descent algorithm is a one-liner.

---
**Algorithm** *online gradient descent with strongly convex losses.*

* **given** parameters $$\alpha_1,\alpha_2,\dots$$, an initial model $$w_1 = 0$$, and a
  bound on the norm of the gradient, *i.e.*, $$\|\nabla l(w)\| \le G$$.
* **repeat** for $$t=1,2,\dots$$
	* compute a gradient step:

	$$w_{t+1} = w_t - \alpha_t \nabla l_t(w_t).$$

---

That's it! Now here's the cool part. If we know the minimum eigenvalue
$$\sigma = \lambda_\min(H)$$, associated with the Hessian of $$l$$, then we can choose
$$\alpha_t = 1/t\sigma$$ which gives

$$
\frac{1}{T} \sum_{t=1}^T \big( l_t(w_t) - l_t(u^\star_T) \big) \le \frac{G^2}{2\sigma}
      \frac{\ln T+1}{T}.
$$

We can interpret this bound as: convergence is fast when we have global information about
the sequence of loss functions. No proof this time (although it's very similar to the
online gradient descent proof), so that's it for today!

### Next up

... a foray into the mind of R.A. Fisher with Brad Efron's help!

### References

This post is related to material from Nicolò Cesa-Bianchi's lectures at the Bordeaux
Machine Learning Summer School in 2011. Watch them here: [MLSS 2011 lectures][ncb-lecs].

[ncb-lecs]: http://videolectures.net/mlss2011_cesa_bianchi_learningtheory/
[rad1-post]: /jekyll/update/2018/03/04/rademacher.html
[rad2-post]: /jekyll/update/2018/03/06/rademacher_2.html
[lt1-post]: /jekyll/update/2018/05/06/learning-theory-1.html
[lt2-post]: /jekyll/update/2018/05/10/learning-theory-2.html
