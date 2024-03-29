---
title: "Group actions on polynomial rings using SageMath"
date: 2023-12-04
layout: post
usemathjax: true
---

Lately I have been working a lot with actions of groups on polynomial rings and quotients of them.
For me, the primary way this arises is via some action that permutes variables by permuting their indices.
The classic example of this flavor is the symmetric group
\\(\mathfrak{S}_n\\)
action on a polynomial ring with \\(n\\) many generators.
SageMath is a really useful tool for studying these sorts of questions, so here are some useful techniques. This is the first in a series of posts, so we'll focus on a very classical example, the coinvariant ring. 

If you are just interested in the code, scroll right to the bottom. Before I list out all the code, I try to explain what is happening and some of the Sage quirks we encounter.

# Defining the ring - mathematically

Let's make sure we are on the same page about the group action and ring we want to work with.
First, consider the action of 
\\(\mathfrak{S}\_n\\)
permuting \\(n\\) letters on a polynomial ring with \\(n\\) generators. 
The action is on the subscripts. 
So if a permutation \\(\sigma\\) takes \\(i \mapsto \sigma(i)\\), then the action of \\(\sigma\\) on the generators is 
\\(\sigma \cdot x_i = x_{\sigma(i)}\\).
Let \\(I_n\\) denote the ideal generated by all polynomials that pointwise fixed under this action. 

For example, taking \\(n=3\\), we would see that \\(e_2 = e_2(x_1, x_2, x_3) = x_1 x_2 + x_1 x_3 + x_2 x_3\\) is fixed under this action.
On the other hand, \\(x_1 x_2\\) is *not* fixed under this action because if \\(\sigma(1)=1,\;\sigma(2)=3\\) then \\(\sigma \cdot x_1x_2 = x_1x_3\\).

If an ideal \\(I\\) in a ring \\(R\\) is stable under the action of a group \\(G\\) which acts on \\(R\\), then the quotient ring \\(R/I\\) has a well-defined action of \\(G\\).
Note that we don't need the elements of \\(I\\) to be pointwise fixed.
Rather we need it to be fixed as a set.
We want this sort of stability because we don't want the action of a group element on a representative of \\(0\\) to be nonzero. 
That would be scary.

For example, the polynomial \\(f = x_1^2 x_2 + x_1^2 x_3 + x_1 x_2 x_3\\) is in \\(I_3\\), but is not fixed by every element of \\(\mathfrak{S}\_3\\).
View \\(f\\) as \\(x_1 e_2(x_1,x_2,x_3)\\), so that we can see it is in the ideal \\(I_3\\).
Then any \\(\sigma\\) acting on it will act like \\(\sigma x_1  e_2(x_1,x_2,x_3) = x_{\sigma(1)}  e_2(x_1,x_2,x_3)\\).
So the action results in a genuinely different polynomial, but one that is still in the ideal.
This is what we mean by a "stable" ideal.

Now we can define the coinvariant ring \\(R_n\\) as the quotient of the ambient polynomial ring by the ideal \\(I_n\\)
\\[R_n = \mathbb{C}[x_1, x_2, \ldots, x_n]/I_n.\\]

# Defining the ring - in Sage

In Sage, here is how we could construct the ring when `n=3`:

	sage: n = 3
	sage: S = PolynomialRing(QQ,'x',n)
	sage: x = S.gens()
	sage: L = SymmetricFunctions(QQ)
	sage: e = L.e()
	sage: I = S.ideal([e[i].expand(n) for i in range(1,n+1)])
	sage: R = S.quo(I, names=['z0','z1','z2'])
	sage: z = R.gens()

So `R` is the coinvariant ring! 
We give it the variables `z` so that we can quickly tell which ring Sage thinks a given polynomial is in. 
If the variables are `x`'s, it is in `S`, and if the variables are `z`'s, then the element is in `R`. 
The importance of working in `S` vs. in `R` will make more sense later.
We are defining `x=S.gens()` and `z=R.gens()` now so that we can use it later when we want to incorporate the group action.
The elementary symmetric polynomials in `n`-many variables are known to be a nice generating set for the ideal \\(I_n\\).
The elementary symmetric functions are given by `e[i]`, and the `.expand(n)` method turns them into a polynomial in `n`-many variables.

One of the most useful pieces of the puzzle when studying group representations carried by an algebra is to have a vector space basis for the algebra.
Even though it is not strictly necessary, it helps us to get our hands dirty with examples. 
To me, Sage is an extremely helpful tool to get our hands dirty.
A vector space basis in terms of monomials can be found in Sage using the `.normal_basis()` method (as opposed to a Gröbner basis method).
For some reason, even though we want the vector space basis for `R`, we call the method on the defining ideal, rather than the quotient ring itself.

	sage: B = I.normal_basis(); B
	[x1*x2^2, x2^2, x1*x2, x2, x1, 1]

It is a famous fact that the coinvariant ring is isomorphic as a representation to the regular representation.
In this case that means that \\(R_3\\) will be 6 dimensional over \\(\mathbb{C}\\).
And indeed we see that Sage gave us a vector space basis has 6 elements. In fact, we see that Sage gives us back the Artin basis of substaircase monomials!

# Acting on the polynomial ring

Now we understand `R` as a vector space, but we still don't understand it as a representation.
Now how can we figure out that this representation is the regular representation?
A first approach would be to compute the character values.
Let's do that using Sage!

We want to work with the symmetric group, so lets go ahead and define that.

	sage: Sn = SymmetricGroup(n)

This defines the symmetric group as a `PermutationGroup_generic` object.
Most of the rest of the way we interact with `Sn` only relies on it being a `PermutationGroup_generic`. 
Thus, we could do a lot of the same things with any permutation group appropriately defined. (More on this in a follow-up post!)
Now, recall that the character of a representation is constant on conjugacy classes.
Thus, we can make our computation significantly more efficient if we work with a single representative for each conjugacy class rather than iterating over the whole group.

	sage: CC = Sn.conjugacy_classes_representatives()

Now we want to act by permuting the variables.
This is partiularly easy for `PermutationGroup`s.
The syntax is to treat a group element `s` like a function.
The input should be a list that is the same length as the domain of the permutation group.
(We can find this by using the `.domain()`.)

	sage: Sn.domain()
	{1, 2, 3}

The output is then the list containing the same elements, but with the order permuted appropriately.
For the coinvariant ring, the list of generators, which I have named `x`, has the right size.

	sage: [s(x) for s in CC]
	[(x0, x1, x2), (x1, x0, x2), (x1, x2, x0)]

Now we want to figure out how each group element acts on the `.normal_basis()`. 
The action is really a substitution action.
Substituion in Sage can be done by again calling the polynomial ring element like a function.
For example

	sage: f = x[0] + x[1]
	sage: f(1,2,0) 
	3
	sage: f(x2,x0,x1)
	x0 + x2

We want to do this for each of the `.normal_basis()` monomials.

	sage: for s in CC:
	....:    print([m(s(x)) for m in B])
	[x1*x2^2, x2^2, x1*x2, x2, x1, 1]
	[x0*x2^2, x2^2, x0*x2, x2, x0, 1]
	[x0^2*x2, x0^2, x0*x2, x0, x2, 1]

So we have successfully acted upon the monomial basis by by an element of each conjugacy class.
I mentioned before that I named the variables in `R` as `z`'s because I want to be able to quickly tell where Sage things monomials are living.
Here, we see that the resulting monomials are not expressed in terms of our Artin basis, rather they are monomials in `S`.
In order to turn these into elements of the coinvariant ring, we again call the quotient ring `R` like a function.
The argument is a polynomial (in `S`) that we want to convert to a quotient ring element (in `R`).

	sage: for s in CC:
	....:    print([R(m(s(x))) for m in B])
	[z1*z2^2, z2^2, z1*z2, z2, z1, 1]
	[-z1*z2^2, z2^2, -z1*z2 - z2^2, z2, -z1 - z2, 1]
	[z1*z2^2, z1*z2, -z1*z2 - z2^2, -z1 - z2, z2, 1]

Now the variables are `z`'s so we know they are written in terms of our Artin basis for `R`. Thats better!

# Getting the character values

Now that we know how to act on the monomials forming a linear basis for \\(R_n\\), we want to better understand the action. 
One way to do this is to use character theory.

The way to go from a polynomial to a character value is to extract coefficients. 
More explictly, if \\(m\\) is a monomial in a basis for \\(R\\) (for example, one of the monomials returned by `.normal_basis()`), then we look at the coefficient of \\(m\\) inside of \\( w \cdot m\\).
This would give us the entries on the diagonal of the matrix if we were to explicitly write out the way \\(w\\) acts on \\(R_n\\) as a matrix. 
Thus, we want to sum the diagonal entries to get the trace of the matrix.

In the following code we act on all of the basis monomials by the permutation \\(231\\) and get the coefficient of the same monomial.
We act on the `z` variables because we want to express the resulting polynomial in normal form. 
The `.lift()` method is included because `m` is a monomial in `S`, so in order to extract coefficients, we need `m(s(z))` to be thought of as an element of `S` via the natural inclusion of a standard monomial in `R` into a monomial in `S`.

	sage: w = Sn([2,3,1])
	sage: [m(w(z)).lift().coefficient(m) for m in B]

We can't get the coefficient of `R(m)` in `m(s(z))` beause Sage `QuotientRing_generic`s don't know how to get coefficients. The following line throws an `AttributeError`:

	sage: [m(w(z)).coefficient(R(m)) for m in B]

Now the character value is the sum of these diagonally-indexed coefficients. So the following line of code computes the character value of the permutation \\(231\\):

	sage: sum([m(w(z)).lift().coefficient(m) for m in B])
	0

# The Frobenius character map

One easy way to compute the irreducible decomposition from the character using Sage is to compute the Frobenius character of the representation. For the coinvariant ring, we know that we are looking to come up with the regular representation, so we *should* find the symmetric function `s[1,1,1] + 2*s[2,1] + s[3]`.

The way that we'll come up with these symmetric functions is by applying the definition of the Frobenius characteristic map. 
The map is defined on a representation \\(V\\), as follows:
\\[\operatorname{ch}\ V = \frac{1}{n!}\sum_{w \in \mathfrak{S}\_n} \chi(w)p_{\operatorname{type}(w)} = \sum{\lambda \vdash n} \frac{\chi(\lambda)}{z_\lambda}p_\lambda,\\]
where \\(z_\lambda\\) is the size of the centralizer of an element of cycle type \\(\lambda\\).
Sage knows how to compute \\(z_\lambda\\) using the function `zee` in the symmetric function algebras package.

	sage: from sage.combinat.sf.sfa import zee

Now we want to apply the Frobenius characteristic map.
To collect this as a symmetric function, we give

	sage: p = L.p()
	sage: F = 0
	....: for w in CC:
	....:     mu = w.cycle_type()
	....:     zed = zee(mu)
	....:     char = sum([f(w(z)).lift().coefficient(f) for f in I.normal_basis()])
	....:     F += QQ(char/zed) * p[mu]
	sage: F
	p[1,1,1]

This is what we expect, because in the regular representation, the only nonzero character value is the character value of the identity.
Now to decompose the representation we should express it in the Schur basis:

	sage: s = L.s()
	sage: s(F)
	s[1, 1, 1] + 2*s[2, 1] + s[3]

So we see that each representation occurs with multiplicity equal to its dimension. This is exactly the description of decomposition for the regular representation that we want!

# The *graded* irreducible decomposition

But we can do even better. The coinvariant ring is a graded ring, and so we could ask for the graded irreducible decomposition. This has graded Frobenius characteristic given by a polynomial whose coefficients are symmetric funtctions.

	sage: P.<q> = PolynomialRing(s)
	sage: grF = 0
	sage: for w in CC:
	....:     mu = w.cycle_type()
	....:     zed = zee(mu)
	....:     char = sum([P(f(w(z)).lift().coefficient(f))/zed*p[mu]*q^f.degree() for f in I.normal_basis()])
	....:     grF += char
	sage: grF
	s[1, 1, 1]*q^3 + s[2, 1]*q^2 + s[2, 1]*q + s[3]

This is the right grading! 

So in this post we have seen how to utilize Sage's functionality to act on quotients of polynomial rings, and how to compute their graded Frobenius characteristic, which tells us the irreducible decomposition of each homogeneous component.

Stay tuned! In this post, we acted on the variables by directly permuting their indices \\(1, \ldots, n\\) by an action of \\(\mathfrak{S}_n\\). Sometimes, however, the action is a bit more subtle. For example, if the variables are indexed by subsets of \\([n]\\), the action is a little trickier. In a follow up post, I'll be discussing to act on more interesting sets of variables using the example of the Varchenko-Gel'fand ring of the braid matroid.

# Code only

Here is all of the code from this post in a single block, so that you can see the full process from start to finish.

	sage: from sage.combinat.sf.sfa import zee
	....: 
	....: # define the coinvariant ring
	....: n = 3
	....: S = PolynomialRing(QQ,'x',n)
	....: x = S.gens()
	....: L = SymmetricFunctions(QQ)
	....: e = L.e()
	....: I = S.ideal([e[i].expand(n) for i in range(1,n+1)])
	....: R = S.quo(I, names=['z0','z1','z2'])
	....: z = R.gens()
	....: 
	....: # get a vector space basis for the coinvariant ring
	....: B = I.normal_basis(); B
	....: 
	....: # get conjugacy class representatives
	....: Sn = SymmetricGroup(n)
	....: CC = Sn.conjugacy_classes_representatives()
	....: 
	....: # compute the Frobenius characteristic F
	....: p = L.p()
	....: F = 0
	....: 
	....: for w in CC:
	....:     mu = w.cycle_type()
	....:     zed = zee(mu)
	....:     char = sum([f(w(z)).lift().coefficient(f) for f in I.normal_basis()])
	....:     F += QQ(char/zed) * p[mu]
	....: F
	....: 
	....: s = L.s()
	....: s(F) # express it in the Schur basis
	....: 
	....: # compute the *graded* Frobenius characteristic
	....: P.<q> = PolynomialRing(s)
	....: grF = 0
	....: for w in CC:
	....:     mu = w.cycle_type()
	....:     zed = zee(mu)
	....:     char = sum([P(f(w(z)).lift().coefficient(f))/zed*p[mu]*q^f.degree() for f in I.normal_basis()])
	....:     grF += char
	....: grF
	[x1*x2^2, x2^2, x1*x2, x2, x1, 1]
	p[1, 1, 1]
	s[1, 1, 1] + 2*s[2, 1] + s[3]
	s[1, 1, 1]*q^3 + s[2, 1]*q^2 + s[2, 1]*q + s[3]

The first output is a vector space basis for \\(R_3\\) (the Artin basis).
The second output is the Frobenius character of \\(R_3\\)in the `p` basis.
The third output is the Frobenius character of \\(R_3\\)in the `s` basis, so that we can quickly read off the irreducible decomposition.
The fourth output is the graded Frobenius character of \\(R_3\\)in the `s` basis, telling us the irreducible decomposition of each graded component of the ring.
