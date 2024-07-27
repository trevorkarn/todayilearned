---
title: "Notes on using Sage for commutative algebra"
date: 2024-07-27
layout: post
usemathjax: true
---

This is a living post that will be updated with notes about using SageMath for commutative algebra. Macaulay2 does all of these computations very nicely, and often more quickly than Sage. But for folks with some Python background, writing Sage code tends to feel a bit more natural. This post is meant to be a living set of notes for common things SageMath users might want to do, without having to learn the syntax of Macaulay2. (Last updated 27 July, 2024)

# Some thoughts

I'm writing up these notes to help with speed of mathematical experimentation. In other words, I expect this post to help someone with a mathematical idea to quickly write basic code, especially if they already have some Sage code written. While Sage is faster than by-hand computations, the goal of this post is not to address speed of actual computation. If you find your Sage code is taking too long, either check out [parallel computation in SageMath](https://doc.sagemath.org/html/en/reference/parallel/index.html) or just bite the bullet and learn [Macaulay2](https://macaulay2.com/). (You will probably want to eventually anyway.)

 
# The notes


## Basic computations using ideals

To create a polynomial ring, there are a few different ways:

    sage: R.<x> = PolynomialRing(QQ) # A single variate polynomial ring with rational coefficients
    sage: R = PolynomialRing(QQ,'x') # A different syntax for the same thing
    sage: R = QQ['x'] # same as above
    sage: R.<x> = QQ[] # same as above
    sage: S.<x,y> = PolynomialRing(QQ) # A multivariate polynomial ring

To create an ideal in your polynomial ring, create a list of elements and call the `ideal` function or the `.ideal` method of the ring itself:

    sage: I = ideal([x^2 - y^2, x^3*y^3])
    sage: I = R.ideal([x^2 - y^2, x^3*y^3])

To reduce a polynomial ring element modulo the ideal, use the `.reduce()` method of the ideal:

    sage: I.reduce(x^5)
    x*y^4

(This is the same functionality as Macaulay2's `%` operator. As of Sage 10.4, the same syntax is not supported in Sage.)

To change the monomial ordering on the ring, pass it when constructing the ring using the `order` kwarg. 

    sage: S.<x,y> = PolynomialRing(QQ, order='neglex')
    sage: J = R.ideal([x^2 - y^2, x^3*y^3])
    sage: J.reduce(x^5)
    x^5
    sage: J.reduce(y^5)
    x^4*y

To check if an element is in the ideal, just use Python's builtin `in`:

    sage: x^4 - y^4 in J
    True

To express an element in terms of the generators of the ideal, use the `.lift` method on the polynomial element:

    sage: (x^4 - y^4).lift(J)
    [y^2 + x^2, 0]
    sage: x^4 - y^4 == (y^2 + x^2) * (x^2 - y^2) + 0 * (x^3*y^3)
    sage: True

This is the same as Macualay2's `//`.

To check if the provided generating set is a Gröbner basis, use the `.basis_is_groebner()` of the ideal:

    sage: J.basis_is_groebner()
    False

To get the Gröbner basis, use the `.groebner_basis()` method of the ideal:

    sage: J.groebner_basis()
    [y^7, x*y^5, x^2 - y^2]