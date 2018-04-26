---
layout: post
title:  "Rademachers are rad - part III"
date:   2018-04-25 16:00:00 -0700
categories: jekyll update
---
After some ambivalence, I decided that we'll pay homage to our Rademacher friends
once again. We're going to prove a minimax lower bound for an expert prediction
problem. As we'll see, Rademacher random variables will come to our rescue 
(twice, in fact).

### Prediction with experts

Let's say that we disagree with statistical learning theory on philosophical grounds,
*i.e.*, we don't believe that our data are generated via a stochastic model that
assumes independence (or exchangeability). Can we predict in the absence of a
statistical assumptions? If so, how would we do it?

* *Can we predict in the absence of a statistical assumptions?* **Yes!**
* *If so, how could we do it?* **Using the advice of experts!**


The **prediction with expert advice** framework has the following set up:

* a decision space $$\mathcal{D}$$;
* an outcome space $$\mathcal{Y}$$;
* a loss function $$l$$; and
* a set of expert indices $$\mathcal{E}$$;

At each time $$t = 1, 2, \dots$$
1. the environment chooses the next outcome $$y_t$$ and the experts choose
	$$\left\{ f_{E,t} \in \mathcal{D} \mid E \in \mathcal{E} \right\}$$;
2. the expert advice is provided to the forecaster;
3. the forecast chooses a prediction $$\widehat{p}_t \in \mathcal{D}$$;
4. the environment reveals the next outcome $$y_t \in \mathcal{Y}$$;
5. the forecaster incurs a loss $$l(\widehat{p}_t, y_t)$$ and each expert $$E$$ incurs
	a loss $$l(f_{E,t}, y_t)$$.

### Minimax lower bound

We want to show that if $$l$$ is the absolute loss, then 

$$
\sup_{n,N} \frac{\mathfrak{M}(n,N,l)}{\sqrt{(n/2) \ln N}} \ge 1
$$

where the minimax regret $$\mathfrak{M}$$ for $$n$$ rounds is defined as

$$
\mathfrak{M}(n,N,l) = \inf_P \sup_{ \{ \mathcal{F}: |\mathcal{F}| = N \} } 
	\sup_{y^n \in \mathcal{Y}^n} \max_{i=1,\dots,N}
	\left\{ \sum_{i=1}^n l(\hat{p}_t, y_t) - l \left( f_{i,t}, y_t \right) \right\},
$$

with $$\mathcal{F}$$ is a set of $$N$$ static experts, $$P$$ is a forecasting strategy, 
$$l$$ is a $$[0,1]$$-valued convex loss function, and $$n$$ is the number of rounds.
(A static expert is one with constant-valued predictions, *i.e.*, they only depend on the 
current round.)

### Proof

We're going to lower bound a lower bound on $$\mathfrak{M}(n,N,l)$$, which may seem
odd, but is a fairly common technique. (So keep it in your back pocket the next time
you have to bound something.) In particular, we're going to use the fact that a fixed
class of static experts lower bounds the minimax regret as

$$
\mathfrak{M}(n,N,l) \ge 
	\sup_{ \{ \mathcal{F}: |\mathcal{F}| = N \} } \mathfrak{M}(n, \mathcal{F}, l)
$$

where

$$
\mathfrak{M}(n, \mathcal{F}, l) = \inf_P \sup_{y^n \in \{0,1\}} \sup_{f \in \mathcal{F}}
	\left\{ \sum_{t=1}^n |\hat{p}_t - y_t| -  |f_t - y_t| \right\}.
$$

Then we'll lower bound $$\mathfrak{M}(n, \mathcal{F}, l)$$, which implies the minimax
regret bound.

First, we use the *average-is-less-than-the-max* trick to get

$$
\mathfrak{M}(n, \mathcal{F}, l) \ge \inf_P \E \left[ \sup_{f \in \mathcal{F}}
	\left\{ \sum_{t=1}^n |\hat{p}_t - Y_t| -  |f_t - Y_t| \right\} \right],
$$

where the expectation is over the randomness in $$Y_t$$. By the definitions of the 
infimum and supremum, we can rewrite this guy as

$$
\inf_P \E \left[ \sum_{t=1}^n |\hat{p}_t - Y_t| \right] -  
 \E \left[ \inf_{f \in \mathcal{F}} \sum_{t=1}^n |f_t - Y_t| \right].
$$

Okay, cool. Now we're going to use the fact that 
$$\E \sum_{t=1}^n |\hat{p}_t - Y_t| = n/2$$ for all
forecasting strategies because of the randomness of the $$Y_t$$. (To see this, split the
absolute value into two parts, enjoy some nice cancellations and sum it all up.)
We'll use this fact and then introduce some Rademacher random variables defined as 
$$W_t = 1 - 2Y_t$$ to write

$$
\begin{align*}
\mathfrak{M}(n, \mathcal{F}, l) &\ge 
\frac{n}{2} - \E \left[ \inf_{f \in \mathcal{F}} \sum_{t=1}^n |f_t - Y_t| \right] \\
	&= \E \left[ \sup_{f \in \mathcal{F}} \sum_{t=1}^n \frac{1}{2} - |f_t - Y_t| \right]\\
	&= \E \left[ \sup_{f \in \mathcal{F}} \sum_{t=1}^n 
		\left( \frac{1}{2} - f_t \right)  W_t \right].
\end{align*}
$$

Now, let's take supremums of both sides, do another Rademacher trick, and then 
exploit the symmetry of Rademachers:

$$
\begin{align*}
\sup_{ \{ \mathcal{F}: |\mathcal{F}| = N \} } \mathfrak{M}(n, \mathcal{F}, l) 
	&\ge \sup_{ \{ \mathcal{F}: |\mathcal{F}| = N \} } 
		\E \left[ \sup_{f \in \mathcal{F}} \sum_{t=1}^n \left( \frac{1}{2} - f_t \right) 
		 W_t \right] \\
	&\ge \frac{1}{2} \E \left[ \max_{i=1,...,N} \sum_{t=1}^n Z_{i,t} W_t \right] \\
	&= \frac{1}{2} \E \left[ \max_{i=1,...,N} \sum_{t=1}^n Z_{i,t} \right],
\end{align*}
$$

where we used that $$(1/2)(1 - 2f_t) = (1/2)Z_{i,t}$$ and $$Z_{i,t}W_t \sim Z_{i,t}$$.

The final step is an appeal to *ye-old* central limit theorem and a maximal inequality
for (sub-)Gaussian random variables. The CLT says that for each $$i = 1,\dots,N$$

$$
\frac{1}{\sqrt{n}} \sum_{t=1}^n Z_{i,t} \sim N(0,1) \implies
\lim_{n \to \infty} \E \left[ \frac{1}{\sqrt{n}} \max_{i=1,...,N} 
								\sum_{t=1}^n Z_{i,t} \right] =
	\E \left[ \max_{i=1,...,N} X_i \right].
$$

for standard normals $$X_1,\dots,X_n$$. The maximal inequality gives

$$
\E \left[ \max_{i=1,...,N} X_i \right] \le \sqrt{2 \ln N}.
$$

Now, we stitch everything back together and conclude the proof with

$$
\begin{align*}
\mathfrak{M}(n,N,l) &\ge 
	\sup_{ \{ \mathcal{F}: |\mathcal{F}| = N \} } \mathfrak{M}(n, \mathcal{F}, l) \\
	&\ge \frac{\E \left[ \max_{i=1,...,N} X_i \right]}{\sqrt{2 \ln N}} \\
	&\ge 1.
\end{align*}
$$

Rademacher's to the rescue once again.

### Next up

... modified logarithmic Sobolev inequalities **or** bandits!

### References

This post is related to material from Chapters 2 and 3 of:

* *Prediction, Learning, and Games* by N. Cesa-Bianchi and G. Lugosi.
  
[lsi-post]: /jekyll/update/2018/04/07/lsi.html