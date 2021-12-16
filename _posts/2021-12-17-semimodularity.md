---
title: "Semimodularity and matroids"
date: 2021-12-17
layout: post
---

In preparation for my oral exam, I had asked my advisor to quiz me on some aspects of
partially ordered sets. We had a nice discussion about intervals of Young's lattice,
and distributive lattices. He then asked me about semimodular lattices, and I blanked.
From time to time I can recall that a matroid can be defined by its lattice of flats,
and that that lattice should be geometric. So what is a geometric lattice?

> **Def.** A lattice $L$ is geometric if it is atomic and upper-semimodular

When I think of a matroid, I like to think of it as the either the data of a hyperplane arrangement or a graph,
at least for the sake of intuition. The lattice of flats is then the lattice of flats (also called
the intersection lattice) of the arrangement. 

### What is an atomic lattice?

Lets break down the definition of a geometric lattice a bit, starting with the first part.
> **Def.** A lattice $L$ is *atomic* if every element $x \in L$ can be written of the join
> of atoms. An element of a lattice with $\hat{0}$ is an *atom* if it is bigger 
The condition that a lattice is atomic to me is