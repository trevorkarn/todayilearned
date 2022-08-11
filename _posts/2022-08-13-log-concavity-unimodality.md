---
title: "Log concavity implies unimodality"
date: 2022-08-13
layout: post
usemathjax: true
---

I have been to many talks where the speaker says something like "its not hard to see that log
concavity (with no internal zeroes) implies unimodality," but I have never seen the proof of this statement written down.
So here it is, inspired by [a recent talk by Petter Brändén](https://www.youtube.com/watch?v=wuQN0xaTkxE).

# Definitions

A sequence finite sequence $$a_1, a_2, \ldots, a_n$$ is said to be "unimodal" if
\\[ a_1 \leq a_2 \leq \cdots a_m \geq a_{m+1} \geq \cdots \geq a_n.\\]

A finite sequence is said to be "log(arithmically) concave" if
\\[ a_k^2 \geq a_{k-1}a_{k+1} \\]
for all $$k$$.

# Proof

Suppose $$\{a_i\}_{i=1}^n$$
is a sequence of positive numbers
with no internal zeroes which is log concave.
Now suppose for the sake of contradiction that $$\{a_i\}$$
is not unimodal. Then there exists an index $$k$$ such that
$$ a_{k-1} \geq a_k \leq a_{k+1}$$. In particular this means
\\[ \begin{cases} a_k \leq a_{k+1} \\ a_k \leq a_{k-1} \end{cases}.\\]
Then since $$ a_k > 0$$ (note we are using both positivity and the fact there
are no internal zeros to establish this) we have 
\\[ a_k^2 \leq a_{k+1}a_k \leq a_{k+1}a_{k-1}. \\]
This contradicts the assumption that $$\{a_i\}$$ was log concave. QED.


I hope that you would agree it is not too bad of a proof, but in case you
(like me) have always been too embarassed to ask to see it, here it is.