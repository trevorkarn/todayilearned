---
title: "Why is the hat-check problem n!/e?"
date: 2022-01-10
layout: post
usemathjax: true
---

The hat-check problem asks: what proportion of
all permutations of $$n$$-elements have no fixed points?

The reason this is called the hat-check problem is because it 
can be phrased as the question: "if $$n$$-many people check their
hats, how many ways are there to  give the $$n$$-many hats back
to them, such that no person recieves back their own hat?"

The answer is that it is *the nearest integer to $$n!/e$$.*
Let $$f_n$$ denote the number of permutations of $$\{1,2,\ldots, n\}$$
with no fixed points.
The exact solution is
\\[ f_n = n! \sum_{i=0}^n \frac{(-1)^i}{i!}.\\]
Today I am less concerned with how to find $$f_n$$ exactly (it is given
in Stanley's EC1), and instead I want to show the
statement that it is the nearest integer to $$n!/e$$. 

The idea is that we can compute the power series
representation $$e^x = \sum_{i=1}^\infty \frac{x^i}{i!}$$.
So then $$1/e = \sum_{i=1}^\infty \frac{(-1)^i}{i!}$$.
Thus, from the product given above
$$f_n/n!$$ is the Taylor approximation of $$1/e$$ to order $$n$$.

We want to show that 
\\[ \left | f_n - n!/e \right | < \frac{1}{2} \\]
because then $$f_n$$ (which we can see is an integer from the formula)
is the closest integer to $$n!/e.$$

[Taylor's theorem](https://en.wikipedia.org/wiki/Taylor%27s_theorem#Explicit_formulas_for_the_remainder) applied to the interval 
$$[-2,0]$$ containing $$1$$
tells us that since the approximation to $$1/e$$ given by $$f_n/n!$$
was centered at $$0$$ the quantity 
\\[ R_n = \frac{1}{e} - \sum_{i=0}^n \frac{(-1)^i}{i!}\\]
satisfies
\\[ R_n < \frac{1}{(n+1)!} \\]
so as long as $$n$$ is at least one, $$n! R_n < 1/2$$ and so $$f_n$$
is the nearest integer to $$n!/e$$.


