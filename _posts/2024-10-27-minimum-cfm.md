---
title: "The minimum requirements for a `CombinatorialFreeModule`"
date: 2024-10-27
layout: post
usemathjax: true
---

One of the useful tools in algebraic combinatorics is to consider a module whose basis corresponds to a known combinatorial objet.
For example, consider the ring of symmetric functions.
The dimension of symmetric functions of degree-\\(n\\) is the number of partitions of the integer \\(n\\).
For this reason, Sage implements the ring of symmetric functions as a `CombinatorialFreeModule` with basis given by partitions `mu`. 
Sage has lots of great functionality for combinatorial objects and for `CombinatorialFreeModules`.
I was recently asked by a colleague, [Joe McDonough](https://jmcdonough98.github.io/), "what is the minimal amount of code required to properly implement a custom `CombinatorialFreeModule` with a basis that is not already an existing combinatorial object in Sage?" 
Since that question might be relevant to other folks, I decided to record it here.

# Minimum code required

There are three classes that need to be implemented to implement a combinatorial free module with a custom basis.
In order to come up with the custom indices for the basis, we need to implement an `Element` and a `Parent` for that element. 
Roughly speaking, we should think of the `Parent` like the set of all possible indices, and an `Element` like one of the actual indices.
Going back to the example of symmetric functions, the individual partition serving as a basis index is a `Partition`, which is an instance of an `Element`. The corresponding parent is `Partitions` (plural).
My implementations of these two classes are called `MyElement` and `MyParent` below.

To come up with the combinatorial free module itself, we will implement a class that inherits from `CombinatorialFreeModule`.
To do this, we will have the `__init__` method for our custom combinatorial free module be pretty simple.
It is basically calling the `__init__` method for a generic `CombinatorialFreeModule`, but since we are going through this exercise in order to have defined a custom basis, we want our custom combinatorial free module to know that its basis is `MyParent`. Thus we define

        def __init__(self, R):
            super(MyCFM, self).__init__(R, MyParent())

Passing the instance of `MyParent()` on the last line is what tells this combinatorial free module that the basis is indexed by `MyParent()`.

All together, the minimum code required to implement a `CombinatorialFreeModule` in Sage with a custom parent/element is

        from sage.structure.element import Element
        from sage.misc.inherit_comparison import InheritComparisonClasscallMetaclass

        class MyElement(Element, metaclass=InheritComparisonClasscallMetaclass):
            @staticmethod
            def __classcall_private__(self, n):
                # Preparse the input so that an instance of MyElement
                # knows it is an element of MyParent without any extra work
                # on the user end.
                return MyParent()(n)

            def __init__(self, parent, n):
                # Initialize MyElement
                self._n = n

                # don't forget to call the init of the superclass
                # so that MyElement knows it is indeed an instance of Element
                super(MyElement, self).__init__(parent)

            def __repr__(self):
                return f"E{self._n}"

            def __hash__(self):
                # Needed in order to store basis elements for the CombinatorialFreeModule
                return hash(self._n)

            def __eq__(self, other):
                # Needed so that addition is well defined
                # Without this, B[3] + B[3] != 2*B[3].
                return self._n == other._n

        class MyParent(Parent, UniqueRepresentation):
            def _repr_(self):
                return "MyParent"
                
            def _element_constructor_(self, n):
                return self.element_class(self, n)
                    
            Element = MyElement

        class MyCFM(CombinatorialFreeModule):
            def __init__(self, R):
                super(MyCFM, self).__init__(R, MyParent())


Some things to be aware of include dealing with the `__classcall_private__` method, which [I have written about in another post](https://trevorkarn.github.io/todayilearned/2022/08/10/sage-parents.html), and implementing `__hash__` and `__eq__` for `MyElement`. 

Implementing `MyElement.__hash__` allows us to use instances of `MyElement` as keys in a `dict`.
An element of `MyCFM` is a formal linear combination of `MyElement`s. 
Under the hood, the data of an element of `MyCFM` is stored in a dictionary whose keys are basis elements and values are coefficients.
Thus, if we ever want to create an element of `MyCFM`, the elements of `MyElement` have to be able to be used as keys in a `dict`.
In order for an element to be used as a key it must be hashable, so we need to implement a `__hash__` method.

The built-in binary operation for a `CombinatorialFreeModule` is addition. 
I mean this in both a programming sense and a mathematical sense. 
In the programming sense, I mean that `__add__` is implemented for classes that inherit from `CombinatorialFreeModule`.
In the mathematical sense, I mean that one of the axioms of being a module is that addition is defined.
So we need to make sure that two elements of `MyCFM` can be added to make sure that our code accurately reflects the mathematical object that it is representing.
Under the hood, when adding elements of a `CombinatorialFreeModule`, Sage checks to see if the keys in the aforementioned dictionary are the same.
Implementing `__eq__` allows us to check if two instances of `MyElement` are the same. 
If we don't implement `__eq__`, we get silly results.
For example if `B[3]` is an element of `MyCFM`, then mathematically speaking, we expect `B[3]  + B[3] == 2*B[3]` to be a true statement. 
(Indeed, addition gives a \\(\mathbb{Z}\\)-module structure to `MyCFM`.)
However if we don't implement `__eq__`, then `B[3]  + B[3] == 2*B[3]` will evaluate to `False` because we never told Sage how to consider to basis elements the same.

Of course this is only a taste of what you can do to build a custom `CombinatorialFreeModule` in Sage, but to me, this is the minimum amount of code required to start working with a custom `CombinatorialFreeModule`. 
I hope this helps you in your research and code writing!