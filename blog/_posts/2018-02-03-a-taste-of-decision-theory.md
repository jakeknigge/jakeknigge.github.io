---
layout: post
title:  "A taste of decision theory"
date:   2018-02-03 13:00:00 -0700
categories: jekyll update
---
In this post, we're going to talk about statistical decision theory, which is a topic
with its origins in the 1950s. Decision theory is cool because it gives us a way to
compare statistical procedures. Moreover, it connects frequentist and Bayesian inference.

This post sets us on the path towards minimax rules and estimators. 
*Caveat:* In no way is this an attempt to fully cover this topic---it's huge and 
philosophically deep.

### A bit of background.

Let's say we have a parameter $$\theta$$ that lives in some parameter space 
$$\Theta$$. We're going to estimate our parameter of interest $$\theta$$ with 
$$\hat{\theta}$$. Since we're in the business of comparing estimators, we need a way
to assess their quality. We'll do this using **loss functions**.

A loss function measures the discrepancy between $$\theta$$ and $$\hat{\theta}$$. 
Formally,

$$
L: \Theta \times \Theta \to \reals.
$$
 
Some common loss functions are:

* squared-error loss: $$ L(\theta, \hat{\theta}) = \|\theta - \hat{\theta}\|^2_2 $$.
* absolute-error: $$ L(\theta, \hat{\theta}) = \| \theta - \hat{\theta} \|_1 $$.
* Kullback--Leibler: $$ L(\theta, \hat{\theta}) = \int \log 
					   \frac{f(x;\theta)}{f(x;\hat{\theta})} f(x;\theta) \, dx $$.
					   
Now we have some tools to assess the quality of our estimators, but we still have some 
work to do because our estimators (most likely) depend on random variables. 
To remove the randomness, we'll take an average which will give us the **risk**
of our estimator. The risk of $$\hat{\theta}$$ is given

$$
R(\theta, \hat{\theta}) = \E_\theta \big( L(\theta, \hat{\theta}) \big) 
	= \int L(\theta, \hat{\theta}) f(x; \theta) \, dx
$$

where the expectation is over the data. (The $$\theta$$ subscript on the 
expectation is just a way for us to index our distribution---in other words, we can read
it as "the expectation when the parameter is $$\theta$$.")
The risk quantifies how good or bad our estimator is, where "low risk"
is good and "high risk" is bad.

If we took an additional expectation across the parameter (*your Bayesian neurons should
be lighting up*), then we have the **Bayes risk** of the estimator:

$$
R_\mathrm{Bayes}(g,\hat{\theta}) = \int R(\theta, \hat{\theta}) g(\theta) \, d\theta
	= \int \left( \int L(\theta, \hat{\theta}) f(x; \theta) \, dx \right) g(\theta) \, d\theta
$$

where $$g(\theta)$$ is a prior distribution for $$\theta$$. But don't get too attached to
the name because this is an expectation over the data (frequentist) and over the 
parameter (Bayesian).

A true Bayesian statistician would be more interested in the **posterior risk**

$$
R_\mathrm{post}(\hat{\theta} \mid x) = \int L(\theta, \hat{\theta}) g(\theta \mid x) \, dx
$$

where $$g(\theta \mid x)$$ is the posterior density of $$\theta$$.

So, now we have three notions of risk at our disposal:

* the (regular) **risk**, which has a frequentist feel;
* the **posterior risk**, which has a Bayesian feel; and
* the **Bayes risk**, which has an independent-of-statistical-philosophy feel.

**Neural connection!**
When we use the squared-error loss (also known as the *$$\ell_2$$-loss* or
the *mean squared error*), we can write the risk using bias and variance of our estimator
$$\hat{\theta}$$. We'll use the "add-and-subtract" trick to show this relationship.
Let $$\mu = \E_\theta (\hat{\theta})$$, then

$$
\begin{align*}
\E_\theta (\theta - \hat{\theta})^2 
	&= \E_\theta (\theta - \mu + \mu - \hat{\theta})^2 \\
	&= \E_\theta(\mu - \hat{\theta})^2 + (\theta - \mu)^2 +
		2 \E_\theta(\mu - \hat{\theta})(\theta - \mu) \\
	&= \Var_\theta (\hat{\theta}) + \big( \E_\theta (\hat{\theta} - \mu) \big)^2.
\end{align*}
$$

### A warm-up calculation.

Let's assume we have $$n$$ data points, $$X_1$$,...,$$X_n$$ sampled from Bernoulli 
distribution with an unknown probability of success $$p$$. We'll estimate $$p$$ with 
two different estimators

* $$\hat{p}_1 = \bar{X}$$
* $$\hat{p}_2 = 0.5$$

and compare their risk functions, using the squared-error loss function.

The estimator $$\hat{p}_1 = \bar{X}$$ is an unbiased estimator of the true parameter 
$$p$$. So, the risk of $$\hat{p}_1$$ is

$$
R(p, \hat{p}_1) = \E_p (p - \bar{X})^2 
	= \Var_p (\bar{X}) 
	= \frac{p(1-p)}{n}.
$$

The estimator $$\hat{p}_2 = 0.5$$ has a bias but no variance term. Its risk is given by

$$
R(p, \hat{p}_2) = \E_p (p - 0.5)^2 
	= (E(0.5) - p)^2 
	= (0.5 - p)^2.
$$

The risk of the first estimator is a concave quadratic and the risk of the second 
estimator is a convex quadratic.
So, neither one of these risk functions is strictly better (*i.e.*, uniformly dominates)
the other one as $$p$$ ranges from 0 to 1.

### Maximum risk.

In the spirit of (working towards) minimax, we could ask: what value of $$p$$ maximizes
the risk of our estimator?

For $$\hat{p}_1$$, we can expand the quadratic $$(p - p^2)/n$$, take derivatives
$$(1 - 2p)/n$$, set it equal to 0, and find that $$p = 1/2$$ maximizes the risk. (Note
that this results holds independent of $$n$$; however, the value of the risk function
decreases at a rate of $$1/n$$.)

For $$\hat{p}_2$$, the story is a bit different because we're dealing with a convex 
quadratic---so, think endpoints. Because we chose $$\hat{p}_2 = 0.5$$, both 0 and 1
maximize the risk of this expression.

### Setting up the minimax game.

The central object of this post is the minimax risk, which is a (hopefully) minimized, 
worst-case risk, defined as

$$
\inf_{\hat{\theta}} \sup_{\theta} R(\theta, \hat{\theta}).
$$

We can interpret the minimax risk as a type of game: it's us (*the statisticians*) 
against nature. 

* Nature gets to choose $$\theta$$.
* We get to choose $$\hat{\theta}$$.
* Nature chooses $$\sup_{\theta} R(\theta, \hat{\theta})$$ 
  adversarially to maximize our risk.
* We do our best to minimize our risk given nature's choice of $$\theta$$. 
	+ That is, we pick $$\inf_{\hat{\theta}} \sup_{\theta} R(\theta, \hat{\theta})$$.
	
### References

This post draws on material from 

* *Theoretical Statistics* by Robert Keener;
* *All of Statistics* by Larry Wasserman;
* lectures by Michael Jordan.