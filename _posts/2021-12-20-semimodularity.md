---
title: "Semimodularity and matroids"
date: 2021-12-20
layout: post
usemathjax: true
---

In preparation for my oral exam, I asked my advisor to quiz me on some aspects of
partially ordered sets. We had a nice discussion about intervals of Young's lattice,
and distributive lattices. He then asked me about semimodular lattices, and I blanked.
He then asked me what how to characterize the lattice of flats of a matroid.

### What is a matroid

A correct, but bad, answer to my advisor's question is: "a matroid is a geometric lattice." 
My first answer is a bad one
is because nice matroids are sometimes called "combinatorial geometries" and are the reason for the "geometric" part
of the geometric lattice. A better definition might be:

> **Def.** A lattice $$L$$ is geometric if it is atomic and upper-semimodular

When I think of a matroid, I like to think of it as the either the data of a hyperplane arrangement or a graph,
at least for the sake of intuition. 
The intution of a hyperplane arrangement is a useful one for me. Let's talk about hyperplanes really quick
before trying to extend that intuition to a matroid.

If I have a collection of $$n-1$$-dimensional linear subspaces in an $$n$$-dimensional vector space, then I can
call that collection a hyperplane arrangment, which is usually denoted by the scripty-"A": $$\mathcal{A}$$.
The intersection of two (genuinely different) $$n-1$$-dimensional linear subspaces should result in a $$n-2$$-dimensional
linear subspace. 

Lets have two examples which we can visualze to help with our intutition.
A good first example is the arrangement of coordinate hyperplanes.
These are the three hyperplanes in $$\mathbb{R}^3$$, for example, where $$x = 0$$, or $$y = 0$$ or $$z = 0$$.
(They are $$2$$ dimensional subspaces because a generic point on the hyperplane $$x = 0$$ looks like $$(0, y, z)$$
where $$y, z$$ are free.) Lets call the coordinate hyperplane arrangement in $$\mathbb{R}^3$$ by the name $$\mathcal{A}$$.

For another example, which is my favorite example, we can consider the *braid arrangement* in $$\mathbb{R}^3$$. This is
defined by the hyperplanes $$x=y$$ and $$x=z$$ and $$y=z$$. 
(These are $$2$$-dimensional subspaces because a generic point on the hyperplane $$x=y$$ looks like $$(x, x, z) = (y,y,z)$$,
which has two degrees of freedom, one in $$x$$ and one in $$y$$.)
Lets call the braid arrangement $$\mathcal{B}$$.

In $$\mathcal{A}$$, suppose we want to intersect the plane $$x = 0$$ with the plane $$y= 0$$.
Then we are looking at the collection of points $$(0, 0, z)$$ where $$z$$ is free. In other words,
we are looking at the line which is exactly the $$z$$-axis.

Similarly, suppose we want to intersect the two planes $$x = y$$ and $$y = z$$. Then we force all of our coordinates
to be the same, and so we are in the span of the vector $$(1,1,1)$$.
The point I'm trying to drive home is that intersecting two different hyperplanes drops the dimension by one.

### The intersection poset

All the data of the hypeplanes and their intesections is
a lot to keep track of, so we can organize it into the following two pictures:

![Coordinate arrangement](/images/2021-12-21/coordinate-arrangement.jpeg)
![Braid arrangement](/images/2021-12-21/braid-arrangement.jpeg)


The pictures are the Hasse diagrams of the intersection poset ordered by containment (created in Sage).
They neatly contain all of the information about the intersections of any two hyperplanes,
and importantly the intersections of intersections of the hyperplanes.

So the fact of the matter is that when a matroid comes from a hyperplane arrangment, 
it comes equipped with this intersection poset, which is called the lattice of flats.
The whole point of talking about the intersection poset is to try to draw an analogy 
with the lattice of flats of a matroid which might *not* come from a hyperplane arrangement
and to try to have some intuition for why the two conditions of being atomic and semimodular
are the right conditions for the correspondence between a matroid and a lattice.

### What is an atomic lattice?

Lets break down the definition of a geometric lattice a bit, starting with the first part.
> **Def.** A lattice $$L$$ is *atomic* if every element $$x \in L$$ can be written of the join
> of atoms. An element of a lattice is an *atom* if it covers the minimal element of the lattice.
The condition that a geometric lattice should be atomic to me is pretty natural.

The atoms in the intersection poset are exactly the hyperplanes themselves. (The minimal element 
is the entire ambient vector space.)
The join of any two elements in the intersection poset is exactly the subspace where
those two subspaces intersect. Taking the case of the join of some number of hyperplanes,
means that every element of the intersection poset is exactly the intersection of hyperplanes.
This might be a sort of tautological condition, but it certianly gives good intuition of why 
our lattice of flats of a matroid should be considered to be atomic.


### What is a semimodular lattice?
The condition which I have more trouble understanding intuitively (and the one motivating this post)
is the condition of semimodularity.
A semimodular lattice is a ranked lattice, with rank function $$\rho$$ satisfying the following inequality
\\[\rho(x) + \rho(y) \geq \rho(x \vee y) + \rho(x \wedge y). \\]
It can be defined equivalently by the condition that if $$x, y \in \mathcal{L}$$ (and $$x \neq y$$) where $$x, y$$ both cover $$x \wedge y$$,
then it must be that $$x \vee y$$ covers both $$x, y$$.

The first criterion I find to be less than satsifying. The rank of an intersection is its codimension in the ambient space.
For example, the entire ambient vector space can be thought of as the "empty intersection." Which would have codimension $$0$$.
Each hyperplane by definition has codimension $$1$$, which means that every hyperplane should have rank $$1$$. Similarly,
in our examples of $$\mathcal{A}, \mathcal{B}$$ above, we saw that the intersection of two hyperplanes was a line. In $$\mathbb{R}^3$$,
a line has dimension $$1 = 3-2$$, so the rank (in the intersection lattice) of a line is two.

In terms of the inequality given above, this says that the dimension of the intersection of two subspaces, plus the dimension of the
smallest subspace containing both intersections should be bigger than the sum of the dimensions of the subspaces. Maybe I believe it, but blergh
is it a mouthful.

The second criterion feels more natural to me, and have an easier time believing that it is the right criterion. 
The join of two subspaces $$ x \vee y$$ is the intersection of the two subspaces, and the meet $$x \wedge y$$ 
is the smallest subspace containing both $$x$ and $$y$$.

Since the meet is itself a subspace, which contains both $$x$$ and $$y$$, then we can focus our attention on the subspace itself.
If $$x, y$$ both cover their meet, then that means that they both "look like hyperplanes" inside of the meet,
if we were to consider the meet as the ambient subspace. 
Above, we saw how any two distinct hyperplanes intersected in an $$n-2$$-dimensional subspace, as in the case of the lines in $$\mathbb{R}^3$$.
This means that, inside of the meet, $$ x \vee y$$ should look like an $$m-2$$-dimensional subspace, if $$m$$ is the dimension of the meet.

The condition that a matroid's lattice of flats should be semimodular serves as a generalization that, after applying some
dimension reduction, two hyperplanes should intersect in a codimension two subspace.







