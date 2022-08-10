---
title: "Parents, amirite?"
date: 2022-08-10
layout: post
usemathjax: true
---

I'm fortunate to have the support of the [Google Summer of Code](https://summerofcode.withgoogle.com/) this summer to contribute to
the [SageMath](https://www.sagemath.org) (more commonly called Sage) mathematical software.
To me one of the most confusing aspects of doing development in Sage is the ["Parent/Element"
framework](https://doc.sagemath.org/html/en/reference/structure/index.html). The goal of this post is to explain some details in the Parent/Element framework via a minimal implementation
of some of the code I'm working on. 

# The big picture

One of the things Sage is useful for is working with a mathematical structure and also elements of it.
For example, we could work with a group $$G$$ and an element $$x \in G$$. As an example in Sage, 
lets consider the cyclic group of order seven:

	sage: G = CyclicPermutationGroup(7)
	sage: x = G.an_element(); x
	(1,2,3,4,5,6,7)
	sage: x(6)
	7

This element `x` is the element which cycles everything up by one, as we see by calling it on the
integer $$6$$.
The fact that $$x$$ is an element of $$G$$ is an inextricable part of the information of $$x$$.
Sage models this fundamental relationship by including `G` as a "parent" and `x` as an "element".

From [the documentation](https://doc.sagemath.org/html/en/reference/categories/sage/categories/primer.html#elements-parents-categories), a parent is

> a Python instance modelling a set of mathematical elements 
> together with its additional (algebraic) structure.

and an element is

> a Python instance modelling a mathematical element of a set.

The power of this framework comes from the fact that operations on elements `+, -, *, /`, etc. 
will create a new element of the parent (when they make mathematical sense). The "standard idiom"
in Sage is to create a parent before creating elements, and then use that parent to create the
element. This can get slightly annoying to the user sometimes, and so later we'll see a way
to use that idiom in code that is hidden to user but still happens in Sage.

# An example

This summer, part of my project is to implement combinatorial diagrams in Sage. A combinatorial diagram is just a collection of cells $$(i,j)$$ indexed by positive (or nonnegative) integers. The perhaps most familiar
example to people will be the "Ferrer's diagram", which represents a partition of an integer by including
left justified cells in rows corresponding to each part in the partition, with the number of cells in each
row corresponding to the size of the part itself. A less familiar class of diagrams is those with the
"northwest property," that: if $$(i_1, j_1),\,(i_2, j_2)$$ are cells in a diagram $$D$$, then the cell
$$(\min(i_1, i_2), \min(j_1, j_2))$$ is also in $$D$$.

In order to model diagrams in Sage, I want to have two objects, the first a class to model the diagrams
themselves, and then also a class to model the class of *all* diagrams. The former will be the element
and the latter will be the parent.

This post will build up the code one step at a time, so **WARNING** the blocks of code other than
the final example may or may not run or make mathematical sense. 

Since we are trying to have a parent/element relationship between an instance of a `Diagram` and
the parent of all `Diagrams`, we'll start by importing the `Parent` class, and defining `Diagrams` as 
as subclass.

	from sage.structure.parent import Parent
	class Diagrams(Parent):
		pass

If you think about it, there should be only one class of all diagrams. Mathematically this makes sense,
but in Python we could (in theory) create many instances of this object. Fortunately, Sage has a way
to take care of this for us, by making `Diagrams` a `UniqueRepresentation`.

	from sage.structure.parent import Parent
	from sage.structure.unique_representation import UniqueRepresentation
	class Diagrams(UniqueRepresentation, Parent):
		pass

Next, we want to initialize `Diagrams`. Every mathematical object in Sage should live in some
category (literally mathematically a category, with objects and morphisms). There is not too
much structure to the set of all diagrams, so it is enough to put it in the category of `Sets`.
(This is the default for parent so this step is not actually necesarry, but if you wanted
to do something with more structure, you might want to put in the category of `Rings` or
something.)

	from sage.categories.sets_cat import Sets
	from sage.structure.parent import Parent
	from sage.structure.unique_representation import UniqueRepresentation
	class Diagrams(UniqueRepresentation, Parent):
		def __init__(self):	
			Parent.__init__(self, category=Sets())

Now that we have established a parent, we should establish the elements. In order to tell
Sage that elements of `Diagrams` should be objects of `type` `Diagram`, we just assign
the attribute `Element`:

	from sage.categories.sets_cat import Sets
	from sage.structure.parent import Parent
	from sage.structure.unique_representation import UniqueRepresentation
	class Diagram():
		pass
	class Diagrams(UniqueRepresentation, Parent):
		def __init__(self):	
			Parent.__init__(self, category=Sets())
	    Element = Diagram


It is worth noting at this point that in the source code you'll see two different 
idioms for doing this. Sometimes, you'll see the element
defined as a class inside of the parent (as in the [Lie algebras code](
https://github.com/sagemath/sage/blob/7f7149489cd662174d19733feb97c00899f3eaec/src/sage/algebras/lie_algebras/abelian.py)),
and sometimes it will be outside, as I have done below.
My interpretation of this is that it has to do with complexity and if you will be constructing elements
directly. For example, since I want to create a `Diagram` directly and sometimes
hide creation of the `Diagrams` from the user, so I'll include it as its own class.
Don't worry about it too much. They are doing the same thing - defining the
`Element` attribute of the `Parent` subclass.

Now, if we have a `Parent`, we want Sage to know how to construct elements of that parent.
We do that by by defining the `_element_constructor_` method.

	from sage.categories.sets_cat import Sets
	from sage.structure.parent import Parent
	from sage.structure.unique_representation import UniqueRepresentation
	class Diagram():
		pass
	class Diagrams(UniqueRepresentation, Parent):
		def __init__(self):	
			Parent.__init__(self, category=Sets())
		def _element_constructor_(self):
			return self.element_class(self, cells)
	    Element = Diagram

The `_element_constructor_` method is utilized when we call
instances of a parent in order to create elements from the given data. 
The basic syntax is `Parent()(data)`. 

Calling `Parent()` creates an instance of the parent, and then we call that object with
the argument `data`.
Under the hood, here is what is happening:
1. `Parent()(data)` is calling the object `Parent()` with the argument(s) `data`.
2. Inside of the call of `Parent()`, we run into `Parent._element_constructor_`.
3. This then calls `self.element_class(self, data)` where `self` is the parent, in this case `Diagrams`. (In case the two appearances of `self` here worry you, we'll come back to that later.)
4. `self.element_class` does the same thing as `Parent.Element.__init__`

In the sample code of this example, I haven't created the `Parent.Element.__init__` method
yet, so lets do that. I want a single diagram to be an immutable collection of cells `(i,j)`,
and so I'll make it a `ClonableArray`, which is a subclass of `sage.structure.element.Element`.
I don't have great insight into why to chose this one over a different subclass, it was the one
suggested to me by my GSoC mentors. But a `ClonableArray` is an immutable array of elements,
subject to some invariant, which is enforced by the (required) `ClonableArray.check` method.
Lets ignore the check for the purpose of this example, but you could imagine the "check"
being a test that all cells are pairs, and the list of all of them has no repeated elements.

	from sage.categories.sets_cat import Sets
	from sage.structure.list_clone import ClonableArray
	from sage.structure.parent import Parent
	from sage.structure.unique_representation import UniqueRepresentation
	class Diagram(ClonableArray):
	    def __init__(self, parent, cells):
	        self._cells = {c: True for c in cells}
			ClonableArray.__init__(self, parent, cells)
		def check(self):
			pass
	class Diagrams(UniqueRepresentation, Parent):
		def __init__(self):	
			Parent.__init__(self, category=Sets())
		def _element_constructor(self):
			return self.element_class(self, cells)
	    Element = Diagram

Now, if you are a Sage user, you might be familiar with using the `Partition` class. In the framework we've
described so far, `Partition` is an element class whose parent is `Partitions`. If you have tried to create
a partition, you usually just want to type something like `mu = Partition([5,3,1])`. In particular, it is
cumbersome to create an instance of the parent class every time we create an instance of an element, especially
if we don't use the parent class anywhere in our code. But as it stands, our code above would require
being run like this:

	sage: Dgms = Diagrams()
	sage: Diagram(Dgms, [(1,1), (3,5)])

which is more cumbersome than

	sage: Partition([3,2,1])

especially because we know any instance of `Diagram` should be in the parent of `Diagrams` - why
are we manually passing it?

In order to fix this problem, we will set the metaclass of `Diagram` to be 
`InheritComparisonClasscallMetaclass`. We get a few different things out of this, but the primary
one I want to point out is that it allows us to define a `__classcall_private__` method (for
technical reasons I don't understand, it must be a `@staticmethod`) which is called
whenever the class is called (and importantly *before* the usual `__call__`). In this case, calling
`Diagram(cells)` will return `Diagrams()(cells)`. Recall that `Diagrams()(cells)` constructs the element
*known to be an element of the parent `Diagrams`*. Thus, we no longer have to manually create the parent
class and instead we can just create the `Diagram`, but we still have the power of the parent/element
framework to utilize later if we need it.

	from sage.categories.sets_cat import Sets
	from sage.structure.unique_representation import UniqueRepresentation
	from sage.structure.list_clone import ClonableArray
	from sage.structure.parent import Parent
	from sage.misc.inherit_comparison import InheritComparisonClasscallMetaclass
	class Diagram(ClonableArray, metaclass=InheritComparisonClasscallMetaclass):
	    @staticmethod
	    def __classcall_private__(cls, cells):
	        return Diagrams()(cells)
	    def __init__(self, parent, cells):
	        self._cells = {c: True for c in cells}
	        ClonableArray.__init__(self, parent, cells)
	    def check(self):
	        pass
	class Diagrams(UniqueRepresentation, Parent):
	    def __init__(self):
	        Parent.__init__(self, category=Sets())
	    def _element_constructor_(self, cells):
	        return self.element_class(self, cells)
	    Element = Diagram

The code above now would run like this:

	sage: D = Diagram([(2,1), (3,1)])
	sage: D.parent() is Diagrams
	True

In the process of calling `Diagram`, we create `Diagrams` under the hood, and know that our
`Diagram` should belong to it. Woo!

# Why `self.element_class(self, data)`?

Above, I had mentioned that it was mysterious why `self.element_class` utilized `self` twice. One reason
this is actualy awesome is because it allows us to subclass things very easily. For example, suppose I wanted
to create a class for subdiagrams. This should definitely be a subclass of `Diagram` and its parent should be
a subclass of `Diagrams`, so we could do something like this:

	class SubDiagram(Diagram):
		@static_method
		def __classcall_private__(cls, cells, D):
			return SubDiagrams()(cells, D)
		def __init__(self, parent, cells, D):
			self._super_diagram = D
			Diagram.__init__(self, parent, cells)

	class SubDiagrams(Diagrams):
		Element = SubDiagram

If the `SubDiagram.__init__` method didn't require a `parent` argument, we'd have to duplicate all
of the code we wrote for the `Diagram`, so that the parent of `SubDiagram` was not `Diagrams`.
Then, we'd have to rewrite the `_element_constructor_` method because `self` would be a diagram, not
a subdiagram. In short, the `self.element_class(self, data)` and requiring the parent as an argument
allows us to be a lot more flexible with how we create classes and avoid unnecessary duplication of code.

I hope that this example helps show you how to create parents and elements and how to subclass them. If this helps you in your Sage development, or if anything is unclear, please let me know! Thanks for reading!
