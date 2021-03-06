---
title: "A semester reflection"
date: 2021-12-16
layout: post
usemathjax: true
---

This semester I have been TA'ing for Calculus II (MATH 1272) at the University of Minnesota.
Today is the day of their final exam, so I want to take a moment to reflect on the semester.
I certainly have learned a lot about teaching, but right now I'm reminded of a blog post by
Terry Tao, enouraging us to ["learn and relearn your field"](https://terrytao.wordpress.com/career-advice/learn-and-relearn-your-field/). 

## My perspective as an undergraduate

> "When a measure becomes a target, it ceases to be a good measure" - [Goodhart's Law](https://en.wikipedia.org/wiki/Goodhart%27s_law)

As an undergraduate, I think I often found myself trying to do just enough to get a good grade and
while I *certainly* would have claimed that I valued learning-for-learning's-sake, I am not sure that I really 
practiced that. 
My alma mater was very focused on achiving objective measures of success, and so I think what I ended up doing
was the bare minimum to get an "A" in a class, and then moving on to the next thing. In hindsight I wish I had
taken fewer courses, and tried to engage more with them, rather than be a part of the "cult of more."
As I realize this I am trying to pare back what I am doing and instead focus on doing what I am doing *well*
rather than just trying to do more. So far it has met with varying levels of success...

One person who has impressed upon me what learning-for-learning's-sake should look like is Ethan Partida,
an undergraduate at the University of Minnesota. He is applying to grad schools right now and I'm super
excited to see where he goes from here.

## What I (re)learned this semester

The number one thing I think I walked away from this semester gaining a greater appreciation for the
value of deeply understanding computations before trying to do proofs. I'm probably starting to seem like too big of
a Tao fan, but I think the idea would be summed up as the transition from [pre-rigorous to rigorous mathematics](https://terrytao.wordpress.com/career-advice/theres-more-to-mathematics-than-rigour-and-proofs/).

As a part of how I have been focused on the measure (grades) rather than learning, I don't think I had
a good appreciation for the relationship between different series converging and how information about one
can give information about another. I certainly felt good about the comparision theorem, and it is intuitive
to me that if $$\sum_{n=1}^\infty a_n$$ and $$\sum_{n=1}^\infty b_n$$ are series with positive terms and 
$$a_n < b_n$$ for all values of $$n$$, then convergence of the series with bigger terms implies the convergence
of the series with smaller terms.

### A nice homework question

A homework question assigned in the course which made me think and which taught me a lot was question number
29 of section 11.8 of Stewart's Calculus.

> Suppose that $$\sum_{n=0}^\infty c_n 4^n$$ is convergent. Can we conclude that either of the following series
> are convergent?
> 1. $$\sum_{n=0}^\infty c_n(-2)^n$$
> 2. $$\sum_{n=0}^\infty c_n(-4)^n$$

### The answer to the nice homework question

The answers are respectively yes and no. To see why the answer for number (1) is "yes," I want to apply the ratio test. (Thanks to Meagan Kenney for help in figuring this out.)
Note that $$\lim_{n\rightarrow \infty} \frac{|c_{n+1}4^{n+1}|}{|c_n4^n|} 
= 4 \lim_{n\rightarrow \infty} \frac{|c_{n+1}|}{|c_n|}\leq 1$$. The reason is that convergence is the
same as not-divergence, which occurs when the limit is strictly bigger than $$1$$.
Looking at the new series $$\sum_{n=0}^\infty c_n (-2)^n$$, I see that the ratio of consecutive terms is
$$2 \lim_{n\rightarrow \infty} \frac{|c_{n+1}|}{|c_n|}$$ this is exactly half of the ratio above, so we get the
inequality that $$2 \lim_{n\rightarrow \infty} \frac{|c_{n+1}|}{|c_n|}\leq 1/2.$$ In particular, this is less than one,
and so the new series must convergence (absolutely).

The answer for number (2) can be found by constructing a counterexample - a particular value of $$c_n$$ for which
$$\sum_{n=0}^\infty c_n 4^n$$ is convergent but $$\sum_{n=0}^\infty c_n(-4)^n$$ is divergent.
One thing I really admire about my mathematical sister Sasha Pevsner is that she always seems to have just the
right example at hand. To emulate her, I've been trying to work on coming up with constructive examples,
and so I want to talk through how I constructed the following counter example (rather than just giving the counter example).
When I think of a convergent series, my favorite one to think of is the $$p$$-series with $$p=2$$, namely 
$$\sum_{n=0}^\infty \frac{1}{n^2}$$. I originally tried to construct a counter example using $$c_n = \frac{1}{4^nn^2}$$ which would make our original series into the nice $$p$$-series, but it didn't work out how I was hoping.
So I think I needed to try something different. 

The test for divergence tells me that $$\lim_{n\rightarrow \infty} c_n$$
must be $$0$$, and so if the terms $$c_n$$ were positive, then the alternating series test would tell me that 
$$\sum_{n=0}^\infty c_n(-4)^n$$ must converge. But we are trying to show that (2) doesn't converge. In particular,
this means that some of the terms must be negative. What if we flipped the problem upside down, so that the *original*
series $$\sum_{n=0}^\infty c_n 4^n$$ was convergent by the alternating series test. If I am careful with the negative signs,
then I might be able to get them to cancel with the negative signs in the series (2).

Lets try $$c_n = \frac{(-1)^n}{4^n n}$$. Then our original series becomes $$\sum_{n=0}^\infty \frac{(-1)^n}{n}$$, which
is convergent by the alternating series test. On the other hand, the series (2) becomes $$\sum_{n=0}^\infty \frac{(-1)^n (-1)^n}{n}$$. But the factors of $$(-1)^n$$ multiply to be $$(-1)^{2n} = 1$$ for all $$n$$$ and so the series (2)
becomes a divergent $$p$$-series: $$\sum_{n=0}^\infty \frac{1}{n}$$.

### Takeaways

The two main takeaways I want to share with a weary calculus student who might stumble across this question are the following:

1. The first thing I tried didn't work.
2. I used some theory to try to find the counter example.

First, I just tried to use some math that I was familiar with. I know the $$p$$-series converges when $$p=2$$, so I
tried to write my our original series as that $$p$$-series, to see if it was something I knew how to manipulate.
I did know how to manipulate it, but it didn't help me. It might even be misleading, if I thought that this one 
example was the general trend!

Second, I thought about what could possibly be going on with the second one. Usually I want to think of a series
with a factor of $$(-1)^n$$ as an alternating series. But it is pretty easy to make an alternating series converge.
The *only* thing we need to do is to force the terms to go to zero. But I realized that nothing in the hypotheses 
of the question said that $$c_n$$ had to be positive. Since it is easy to make the alternating series converge,
I figured I'd try making the original series alternating, just so that I had some example of a convergent series
to put my hands on. Then I thought some more and had some inspiration - maybe if I got lucky, the factors of $$(-1)^n$$
would multiply and give a posivite number! And that's exactly what ended up happening! 
The point is that I had some
theorem, the alternating series test, and I used what that test could tell me about the series 
$$\sum_{n=0}^\infty c_n(-4)^n$$ in order to reverse-engineer a series $$\sum_{n=0}^\infty c_n 4^n$$ 
which might have a chance of converging.

While I hope these lessons are absorbed by a calc student, I also am writing them down because they are good lessons for me too! I think it is very easy to get discouraged if the first thing I try doesn't work out. Or, if I am doing a problem which doesn't have an answer on page A113 of the textbook, to think that the first think I try must show the pattern.
As for the second point, I think it is good to remember that I shouldn't just grasp for straws and hope something is
a counterexample. Thinking about what I do know and trying to construct a counter example intelligently can also reveal more about the problem

### Relearning calculus

Maybe I should have been comfortable with the above question when I took calc two. Or maybe I should have been 
comfortable with it the *second* time I took calc two. Maybe you forgive me and say, ok wait until real analysis.
Or graduate real analysis. Or studying for the real analysis prelim. *OR* the first time I taught calc two. Or when
studying for my complex analysis prelim. But this is the first time where I was not focused on the metric of having
a grade or exam to study for (or scrambling to shift my teaching to adapt to online teaching in a pandemic).
A student asked me the question above and I was able to frame it as "I am interested in solving this problem, for its
own sake." With that framing, and without the stress of the metric, I finally feel good about being able to learn
the material. I find a lot of pressure around being a graduate student and fear about the job market, and so it
is tempting to want to fall into those metrics, but I think really what really can help me be the most successful
is to focus my efforts on learning math that I find interesting and going from there.





