---
title: "Reducing symbolic expressions in Sage"
date: 2024-04-22
layout: post
usemathjax: true
---

When should a computer algebra system reduce an expression?
As humans, there are some "obvious" reductions that we might make to simplify fractions or rational functions, but of course teaching the computer to do symbolic calculations essentially means we code in a huge decision tree to Sage. 
Sometimes "obvious" reductions slip through the cracks. 
Today's post adresses an interesting case that slips through the first round of cracks, but can be caught with a bit of care.

# The problem

This semester I am teaching Calculus II (MATH 1272), which includes some time spent on working with power series.
On a recent exam, I asked by students to compute the interval of convergence of the power series
\\[ \sum_{n=1}^\infty \frac{(-1)^n}{n \cdot 3^n} (x-2)^n.\\]
To solve this problem, I want to use the ratio test, and do computations using the Sage `SymbolicRing`.

# Things start to work as planned

The ratio test says that if \\(\lim_{n\to\infty} \left | \frac{b_{n+1}}{b_n}\right | < 1\\), then a series is absolutely convergent. 
So I'll compute that limit in Sage.
I find it helpful to define the terms \\(b_n\\) of the series using a `lambda` function, so that I can call `b(n)` and `b(n+1)` instead of typing out the terms and inevitably making a typo with the \\(b_{n+1}\\) term.

    sage: x, n = var('x,n')
    sage: b = lambda n: (-1)^n * (x-2)^n/(3^n * n)
    sage: b(n+1)/b(n)
    3^n*(-1)^(n + 1)*n*(x - 2)^(n + 1)/(3^(n + 1)*(-1)^n*(n + 1)*(x - 2)^n)

Yikes. Let's do some simplification. 

    sage: simplify(b(n+1)/b(n))
    -1/3*n*(x - 2)/(n + 1)

Thats much better! Now I could chain the function calls, or copy and paste to compute the limits. Either works just fine.

    sage: limit(b(n+1)/b(n), n=oo)
    -1/3*x + 2/3

We can then use the `solve` function to compute the interior of the interval.
Again, I could either copy and paste `-1/3*x + 2/3` or chain the function calls.
In this case it is shorter to just copy and paste, but if I was developing a function to automate this procedure I'd probably use the second one. 

    sage: solve(abs((x - 2)/3) < 1, x)
    [[-1 < x, x < 5]]
    sage: solve(abs(limit(b(n+1)/b(n), n=oo))< 1, x)
    [[-1 < x, x < 5]]

So we can see that the radius of convergence is \\(3\\).
But the sticking point is always those pesky endpoints. 
And this is where we start to get into the simplification issues.
Lets start with checking the endpoint \\(5\\).
I want to look at the terms when I plug in the endpoint \\(x=5\\) to the general power series term.

    sage: b(n)(x=+5)
    (-1)^n/n

So I see that at the right endpoint, the series becomes an alternating harmonic series, which converges by the alternating series test. Easy day.

# Things start to go wrong

Now lets look at the left endpoint.

    sage: b(n)(x=-1)
    (-1)^n*(-3)^n/(3^n*n)

Ok that is not too bad of a function to start to look at. The very first thing I notice is that we have `(-1)^n * (-3)^n`. 
Well, shouldn't that simplify to just be \\(3^n\\)? 
I'll try to prod Sage to do that simplification for me.

    sage: simplify((-1)^n * (-3)^n)
    (-1)^n*(-3)^n

That didn't seem to help! Sometimes calling `expand` first helps Sage to see the simplification, especially help with sums of rational functions. Even though we aren't taking sums of rational functions, maybe we can try throwing that in. 

    sage: simplify(expand((-1)^n * (-3)^n))
    (-1)^n*(-3)^n

Again, we don't get our simpler value of just \\(3^n\\). Now wait! We could be having simplification issues because there is a mathematical problem in what we have asked the computer to do. We know from the problem that \\(n\\) is an integer, but the `SymbolicRing` doesn't know that! 
We don't have to go that far to see an example of where we might need the assumption that `n` is an integer. For instance \\( (-1)^{1/4} (-3)^{1/4} = i\sqrt[4]{3} \\)
As far as Sage is concerned, `n` is just some symbol. So lets tell Sage about the assumptions we have been making that `n` is a positive integer.

    sage: assumptions()
    []
    sage: assume(n > 0)
    sage: assume(n, 'integer')
    sage: assumptions()
    [n > 0, n is integer]

Let's check if that fixes our problem. 

    sage: b(n)(x=-1)
    (-1)^n*(-3)^n/(3^n*n)
    sage: simplify(b(n)(x=-1))
    (-1)^n*(-3)^n/(3^n*n)
    sage: simplify(expand(b(n)(x=-1)))
    (-1)^n*(-3)^n/(3^n*n)

Still nothing!
The funny thing is that it doesn't have a problem when we replace `-3` by `-1`

    sage: (-1)^n * (-1)^n
    1

It also doesn't have any trouble when we replace `-1` by `-3`

    sage: (-3)^n * (-3)^n
    3^(2*n)


# Now we are stuck

Of course, at this point, we really should just break out the pencil and paper and do the simplification ourself. But the whole point of this post is to convince Sage to simplify as much as possible, so I feel obligated to link to a [favorite XKCD comic of mine](https://xkcd.com/1319/).

I started Googling by trying the profound statement "sagemath simplifies (-1)^n * (-1)^n but not (-1)^n * (-3)^n". Ask.sagemath user eric\_g [points out](https://ask.sagemath.org/question/40347/simplifying-expression/) that there is a method `canonicalize_radical` that is not called just by `simplify`. 
The `canonicalize_radical` method picks a branch for roots.
For example, from [the documentation](https://doc.sagemath.org/html/en/reference/calculus/sage/symbolic/expression.html#sage.symbolic.expression.Expression.canonicalize_radical), we see that the method will pick the positive value of \\(\sqrt{x^2}\\).

    sage: sqrt(x^2)
    sqrt(x^2)
    sage: sqrt(x^2).canonicalize_radical()
    x

This seemed like it might be helpful, so I just tried it.

    sage: b(n)(x=-1).canonicalize_radical()
    1/n

Woohoo! Now this is what we wanted. So now we can see that the series diverges when \\(x=-1\\) because the series collapses to become the harmonic series, our favorite example of a series that diverges, despite the fact that its terms have limiting value of \\(0\\). Of course, we should be careful when using `canonicalize_radical` to make sure that the answer checks out but in this case it does.
Just as a warning, the approach that worked in this case will not always work. For example, just trying to call `canonicalize_radical` on the numerator *didn't* work. 

    sage: ((-1)^n * (-3)^n).canonicalize_radical()
    (-1)^n*(-3)^n

So there is something about the denominator `3^n` that really makes the difference.

    sage: ((-1)^n * (-3)^n/(3^n)).canonicalize_radical()
    1

Maybe I'll dig into this in a future post, but for now, I'll leave it there.

# What have we learned

It is nice to try to use the basic Sage function `simplify` when trying to do computations in the symbolic ring. 
But we saw that Sage didn't do the basic reduction that \\((-1)^n(-3)^n = 3^n\\) even after we `assume`d that `n` was a positive integer. 
In this example, we were able to prod Sage enough to get the answer we wanted by using the function `canonicalize_radical`.

