---
layout: post
title:  "Entropy and its subadditivity"
date:   2018-03-17 19:00:00 -0700
categories: jekyll update
---
(Happy St. Patrick's Day!)

In this post, we're picking up from the previous post and continuing with
some information-theoretic ideas as they arise in concentration inequalities.
We'll quickly revisit the Efron--Stein inequality and then proceed towards
the subadditivity of entropy.

### Efron--Stein

To encourage the formation of new (or reinforce existing) neuronal 
connections, let's remind ourselves of the Efron--Stein inequality. 
If we have independent random variables $$X_1,\dots,X_n$$ and a 
square-integrable function $$f$$ with $$Z = f(X)$$, then

$$
\Var(Z) \le \sum_{i=1}^n \E \left[ \E^{(i)}[Z^2] - 
	\big( \E^{(i)}Z \big)^2 \right],
$$

where the operator $$\E^{(i)}$$ is the conditional expectation with respect
to the $$i$$th variable. The square-integrable condition on $$f$$ ensures 
that its variance is finite.

### Entropy

Now, let's generalize the Efron--Stein inequality by observing that we
*could* write it as 

$$
\E [Z^2] - \big( \E Z \big)^2 \le 
	\sum_{i=1}^n \E \left[ \E^{(i)}[Z^2] - \big( \E^{(i)}Z \big)^2 \right],
$$

where we used the fact that $$\Var(Y) = \E [Y^2] - \big( \E Y \big)^2$$.
If we stare at this representation of the various for a moment, 
we see that it has the form

$$
\E [g(Z)] - g( \E Z),
$$

where $$g(x) = x^2$$. When $$g(x) = x \log x$$, then quantity 
$$\E [g(Z)] - g( \E Z)$$ is often called the **entropy** and is
denoted by $$\mathrm{Ent}(Z)$$.

### Subadditivity of entropy

Using the Efron--Stein inequality as our source of inspiration, we
might guess (or hope) that

$$
\E [g(Z)] - g( \E Z) \le 
	\sum_{i=1}^n \E \left[ \E^{(i)}g(Z) - g\big( \E^{(i)}Z \big) \right].
$$

If we did guess this, then we'd be correct! So, let's prove it.

We'll assume that $$X_1,\dots,X_n$$ are independent random variables 
taking values in a finite set $$\mathcal{S}$$ and that 
$$f: \mathcal{S^n} \to \reals_+$$ with $$Z = f(X_1,\dots,X_n)$$.

**Proof.**

The proof relies on an application of Han's inequality for relative
entropies (which is an extension of what we showed in the 
[previous post][hans-ineq]).

Note that $$Z$$ is always positive, so the result holds for any
$$cZ$$ with $$c>0$$. Let's use this to simplify our lives and assume that
$$\E Z = 1$$. So, now we can write

$$
\E g(Z) - g( \E Z) = \E[Z \log Z] - 0.
$$

Now, we're going to introduce the relative entropy component by
defining

$$
q(x) = f(x)p(x) \iff \frac{q(x)}{p(x)} = f(x).
$$

The relative entropy neurons in your brain are probably going wild 
because we can now write

$$
Z = f(X) = \frac{q(X)}{p(X)}.
$$

So, let's take an expectation of $$g(Z)$$:

$$
\begin{align*}
\E g(Z) &= \E g\big( f(X) \big) \\
	&= \E_P \left[ \frac{q(X)}{p(X)} \log \frac{q(X)}{p(X)} \right] \\
	&= D_{\mathrm{kl}}(q(X), p(X)).
\end{align*}
$$

Here's where **Han's inequality** comes to the scene, because the
relative entropy form of Han's inequality says that

$$
D_{\mathrm{kl}}(q(X), p(X)) \le \sum_{i=1}^n
	\left( D_{\mathrm{kl}}(q(X), p(X)) - 
	D_{\mathrm{kl}}\left(q^{(i)}(X), p^{(i)}(X)\right) \right).
$$

In other words, we have a bound on the (joint) relative entropy based on 
the sum of marginal relative entropies. Lastly, we'll show that

$$
\sum_{i=1}^n 
	\left( D_{\mathrm{kl}}(q(X), p(X)) - 
	D_{\mathrm{kl}}\left(q^{(i)}(X), p^{(i)}(X)\right) \right) =
\sum_{i=1}^n 
	\E \left[ \E^{(i)} g(Z) - 
	g \left( \E^{(i)} Z \right) \right],
$$

which will conclude the proof. The tower property of expectation let's us
write 

$$
D_{\mathrm{kl}}\left(q(X), p(X)\right) = \E \left[ \E^{(i)} g(Z) \right]
$$

and (with a bit more work)

$$
\begin{align*}
D_{\mathrm{kl}}\left(q^{(i)}(X), p^{(i)}(X)\right) &=
\sum_{x^{(i)} \in \mathcal{S}^{n-1}} p^{(i)} \left( x^{(i)} \right)
	\left( \sum_{y \in \mathcal{S}} q^{(i)} \left( x^{(i)} \right) \right) 
	\log \frac{p^{(i)}\left( x^{(i)} \right) 
			\sum_{y \in \mathcal{S}} q^{(i)} \left( x^{(i)} \right)}
		{p^{(i)} \left( x^{(i)} \right)} \\
	&= \sum_{x^{(i)} \in \mathcal{S}^{n-1}} p^{(i)} \left( x^{(i)} \right) 
	 	\left( \E^{(i)} Z \log \E^{(i)} Z \right) \\
	&= \E \left[ g(\E^{(i)}Z) \right].
\end{align*}
$$

So, chaining everything together we have that

$$
\begin{align*}
\E [g(Z)] - g( \E Z) &= D_{\mathrm{kl}}(Q, P) \\
	&\le \sum_{i=1}^n 
	\left( D_{\mathrm{kl}}(Q, P) - 
			D_{\mathrm{kl}} \left( Q^{(i)}, P^{(i)} \right) \right) \\
	&= \sum_{i=1}^n \E \left[ \E^{(i)}g(Z) - g\big( \E^{(i)}Z \big) \right].
\end{align*}
$$

That's it, that's all folks.

### Next up

... logarithmic Sobolev inequalities! Stay tuned---it's gonna be good.

### References

This post is based on material from Chapter 4 of (my new favorite book):

* *Concentration Inequalities: A Nonasymptotic Theory of Independence* by 
  S. Boucheron, G. Lugosi, and P. Masart.
  
[hans-ineq]: https://jakeknigge.github.io/jekyll/update/2018/03/11/han.html