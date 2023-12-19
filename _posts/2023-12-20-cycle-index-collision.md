---
title: "A collision between cycle index polynomials"
date: 2023-12-20
layout: post
usemathjax: true
---

Today's post is a short, to describe an example of when the cycle index polynomial of a permutation group is not a total invariant of the group up to conjugacy.

# Cycle index polynomials

Let \\(G\\) be a permutation group. 
In other words, \\(G\\) is a finte group, together with a specified embedding into some symmetric group \\(\mathfrak{S}\_n\\). 

For example, consider the following two embeddings of \\(C\_2 \times C\_2 \leq \mathfrak{S}\_6\\):

\\[G\_1 = \langle (1,2)(3,4), (1,2)(5,6)\rangle \\]

\\[G\_2 = \langle (1,2)(3,4), (1,3)(2,4) \rangle \\]

These are two distinct permutation groups which are not conjugate inside of \\(\mathfrak{S}\_6\\).

The cycle index polynomial of \\(G \leq \mathfrak{S}\_n\\) is defined to be
\\[ \frac{1}{|G|} \sum_{g \in G} p_{\operatorname{type}(g)},\\]
where \\(p_{\operatorname{type}(g)}\\) is the power-sum symmetric function (of degree \\(n\\)) indexed by the cycle type of \\(g\\) regarded as an element of \\(\mathfrak{S}\_n\\).

# The collision

It is reasonable to ask if the cycle index polynomial is an invariant of a permutation group \\(G\\) up to conjugacy (i.e. up to relabeling the set that \\(G\\) acts on).
It is not. The two examples of \\(G\_1\\) and \\(G\_2\\) above provide a counter example. Since \\(G\_1\\) acts on \\(\{1,2,3,4,5,6\}\\) and \\(G\_2\\) acts on \\(\{1,2,3,4\} \subseteq \{1,2,3,4,5,6\}\\), they are not conjugate subgroups of \\(\mathfrak{S}\_6\\). However, both have cycle index polynomial 
\\[\frac{1}{4} p\_{1^6} + \frac{3}{4} p\_{2^21^2}.\\]

# Code to generate more collisions

The examples of \\(G\_1\\) and \\(G\_2\\) above give the smallest example of a collision between two permutation groups. If you ever need to find more, you can find the collisions using the following SageMath code (and suitably modifying `N`). The `dict` called `multiples` has keys given by cycle index polynomials with multiple corresponding groups. the corresponding groups are the values.

    sage: N = 6
    ....: SN = SymmetricGroup(N)
    ....: CIs = {G: G.cycle_index() for G in SN.conjugacy_classes_subgroups()}
    ....: Gs = dict()
    ....: for G, f in CIs.items():
    ....:     l = Gs.get(f,list())
    ....:     l.append(G)
    ....:     Gs[f] = l
    ....: 
    ....: multiples = {f:Glist for f, Glist in Gs.items() if len(Glist) > 1}

For example, with `N=7`, there are `3` distict cycle index polynomials with collisions.