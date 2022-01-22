---
title: "Stirling numbers of the first kind"
date: 2022-01-21
layout: post
usemathjax: true
---

The Stirling numbers of the first kind, which I'll write as $$c(n,k)$$, are the number of
of permutations of $$n$$ with exactly $$k$$ cycles.
The number $$c(n,k)$$ is also the coefficient of $$x^k$$ in the product
$$x(x + 1)(x+2) \cdots (x+n-1)$$.

Suppose we wanted to compute the Stirling number
$$c(100,10)$$.
There are two ways to compute this in Sage. The first is 

	sage: stirling_number1(100,10)
	1125272380578944825172147216455781795628518533063359467753257922095126858974036298524724548786755944321693935796805099192994799803568595147738316800000000000

The second is a little more involved, but is a great example of how to extract coefficients from a generating function.
We'll compute the product above, and then take a derivative. Then, we'll extract the coefficient by evaluating that derivative at $$x=0$$.

	sage: R.<x> = PolynomialRing(QQ)
	sage: G = R.prod((x+i) for i in range(100))
	sage: G.derivative(x, 10)(0)/factorial(10) # the 10th derivative
	1125272380578944825172147216455781795628518533063359467753257922095126858974036298524724548786755944321693935796805099192994799803568595147738316800000000000.

Both of these seemed fast to my human eye, so I wanted to look into how long they took in comparison to eachother. 
On my MacBook Air, running SageMath 9.5.beta9, here were the results:

	sage: timeit('G.derivative(x, 10)(0)/factorial(10)')
	625 loops, best of 3: 56.4 μs per loop
	sage: timeit('stirling_number1(100,10)')
	625 loops, best of 3: 171 μs per loop

I find these results rather surprising! Computing the derivative seems like it would be faster than the built-in function.
Why would we have a built in function, if it was slower? Well, it seems the answer to that would be
that it doesn't take that long to compute 10 derivatives, but if we wanted a larger value of $$k$$ then it might be
slower. For example, when $$k=90$$, it takes less than half the time.

	sage: timeit('G.derivative(x, 90)(0)/factorial(10)')
	625 loops, best of 3: 371 μs per loop
	sage: timeit('stirling_number1(100,90)')
	625 loops, best of 3: 120 μs per loop

Another reason we might want to use the built-in version over the derivative, even for small $$k$$, is that creating
the polynomial ring and the generating function introduces some overhead that adds unnecessary time:

	sage: timeit('R.<x> = PolynomialRing(QQ); G = R.prod((x+i) for i in range(100)); G.derivative(x, 10)(0)/factorial(10)')
	625 loops, best of 3: 363 μs per loop