---
title: "Two realizations of a matroid"
date: 2023-01-27
layout: post
usemathjax: true
---

A recent perspective on the study of [matroids](https://en.wikipedia.org/wiki/Matroid)
has been to understand them geometrically. Historically,
a matroid has been considered to be realizable (or representable)
if it can be written down as a finite set of vectors.
The more modern approach views a realization as a vector subspace,
thus opening the door for the use of tools from algebraic geometry,
which are then extended to matroids which are not realizable.

# A set of vectors

The columns of a fixed matrix are a particular choice of a set of vectors.
Lets consider the matrix
\\[ M_1 = \begin{bmatrix} 1 & 0 & 1 \\\\ 0 & 1 & 1 \end{bmatrix}. \\]
Let \\(a,b,c\\) denote the columns respectively.
Then we can consider
\\[ M_2 = \begin{bmatrix} 
 1 &  -1 & 0 \\\\
-1 &  0  & -1 \\\\
 0 &  1  &  1
 \end{bmatrix}, \\]
with columns \(a^\prime, b^\prime,c^\prime\).
We see that both
\\[ a + b = c\\]
and
\\[ a^\prime + b^\prime = c^\prime \\]
The punchline is that the two of these matrices
define the same matroid, isomorphic to \\(U_{2,3}\\),
the uniform matroid of rank \\(2\\) on \\(3\\) elements.
The matroids have groundsets \\(E = \\{a,b,c\\}\\) and 
\\(E^\prime = \\{a^\prime, b^\prime, c^\prime\\}\\).

# Realizations

A realization of a matroid \\(M\\) on a groundset \\(E\\) over \\(\mathbb{C}\\)
is a subspace \\(L \subseteq \mathbb{C}^E \\) which is the
span of the rows of the matrix whose columns are the groundset of 
the matroid.
So for example, the two different realizations of \\(U_{2,3}\\)
described by \\(M_1\\) and \\(M_2\\) are
\\[ L = \operatorname{span}_{\mathbb{C}}\{\langle 1, 0, 1 \rangle, \langle 0, 1, 1 \rangle \} \\]
and
\\[ L^\prime = \operatorname{span}_{\mathbb{C}}\{
\langle 1, -1, 0 \rangle,
\langle -1, 0, -1 \rangle,
\langle 0, 1, 1 \rangle \}.\\]
It is clear that \\(L\\) is two-dimensional, but it is also true that \(L^\prime\)
is two dimensional, since \\(\langle 1, -1, 0 \rangle
+ \langle -1, 0, -1 \rangle,
= -\langle 0, 1, 1 \rangle \\).
So we can take \\(\langle 1, -1, 0 \rangle, \langle -1, 0, -1 \rangle,\\) to be a basis for \\(L^\prime\\).

# Independent sets

The core of matroid theory is the notion of independence, so we should say how
the independent sets are found by this construction. The independent sets are
the sets $I \subseteq E$ where 
\\[ L \hookrightarrow \mathbb{C}^E \twoheadrightarrow \mathbb{C}^I \\]
composes to a surjection. The projection \\( \mathbb{C}^E \twoheadrightarrow \mathbb{C}^I \\)
is the one which picks the coordinates indexed by \\(I\\).

We can see from the vectors that any proper subsets of \\(E\\) and
\\(E^\prime\\) should be independent. Lets check this for \\(a,b\\) and \\(a^\prime, c^\prime\\).
Just to be sure, we'll show \\(a,b,c\\) isn't independent too.
We'll view elements of \\(L\\)
as vectors \\(\langle s, t, s+t\rangle\\).
and elements of \\(L^\prime\\) as vectors \\(\langle s-t, -s, -t \rangle\\).

Then the matrix representing the projection from \\(L\\) to \\(\mathbb{C}^{\{a,b\}}\\)
is
\\[ \begin{bmatrix} 1 & 0 & 0 \\\\ 0 & 1 & 0 \end{bmatrix} \\]
so the image of \(L\) is
\\[ \begin{bmatrix} 1 & 0 & 0 \\\\ 0 & 1 & 0 \end{bmatrix} \begin{bmatrix}s\\\\ t\\\\ s+t\end{bmatrix}
= \begin{bmatrix} s \\\\ s + t \end{bmatrix}
\\]
which is a two dimension subspace of the two dimensional \\(\mathbb{C}^{\{a,b\}}\\).
A two-dimensional subspace of a two-dimensional space is just the whole space,
so the projection from \\(L\\) to \\(\mathbb{C}^{\{a,b\}}\\)
is a surjection.

Similarly, the matrix representing the projection from \\(L^\prime\\) to \\(\mathbb{C}^{\{a^\prime, c^\prime\}}\\)
is
\\[ \begin{bmatrix} 1 & 0 & 0 \\\\ 0 & 0 & 1 \end{bmatrix} \\]
so the image of \(L\) is
\\[ \begin{bmatrix} 1 & 0 & 0 \\\\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix}s-t\\\\ -s\\\\ -t\end{bmatrix}
= \begin{bmatrix} s-t \\\\ - t \end{bmatrix}
\\]
which is a surjection onto \\(\mathbb{C}^{\{a^\prime,c^\prime\}}\\).

There is also no way that \\(L\\) or \\(L^\prime\\), two-dimensional spaces,
could surject onto the three dimensional \\(\mathbb{C}^{\{a,b,c\}}\\)
and \\(\mathbb{C}^{\{a^\prime,b^\prime,c^\prime\}}\\), so \\(E\\) and \\(E^\prime\\)
are *not* independent.

I hope that these examples show exactly how a realizable matroid corresponds
to a vector subspace inside of the vector space \\(\mathbb{C}^E\\) with basis
given by the whole groundset.
