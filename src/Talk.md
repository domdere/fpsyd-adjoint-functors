% Adjoint Functors
% Dom De Re
% 21 October, 2015

Motivation
==========

Isomorphisms
------------

Awesome.

-   Learn about `X`
-   Learn about `Y`
-   Feel a little bit of deja vu.
-   Learn you wasted time learning the same thing twice.
-   Mind blown though.

Not quite Isomorphic
--------------------

-   A lot of things are "similar", two things may not be directly isomorphic but still feel like they have some notion of similarity
-   Mathematicians try to find some isomorphism somewhere to put a *concrete* finger on the nose of the vague notion of similarity

(Co)monads, how do they work?
-----------------------------

-   Explanations and tutorials often involve vague handwavy words like "context" and "burrito", but dont adequately describe what these things mean.
-   What are the internal mechanics of `return`, `join`, `duplicate`, and are they related?
-   The monad laws are enough to *use* and *write* monads, but sometimes its nice to know the science behind the tools and learn the deeper truths

Comonads, where the hell are they?
----------------------------------

-   Once heard someone say "monads are not a snowflake" and that they could also have been the dominant mechanism in FP.
-   Yet the monad path was more popular, and generally there are a lot more monads around, why?

How do you compose monads?
--------------------------

>   Monads generally do not compose.

This is a statement beginners here a lot, and its tricky to really make sense of, after all what does it mean to compose monads?

Option 1
--------

If `m` is a monad and `n` is a monad, then is `newtype F a = F (m (n a)) a` monad?

Option 2
--------

If `m` is a monad and `n` is a monad, is there a way I can structurally decompose `m` and embed `n` in a composable manner?

Compose like traversals?
------------------------

>   Traversals compose, so does that mean traversable monads compose?

``` Haskell
type Traversal s t a b = forall f s t a b. (Applicative f) => (a -> f b) -> s -> f t
```

-   Traversals in this form of compose functionally (option 1)
-   Functionally composing traversals (option 1) are equivalent to structurally embedding one traversal in another (option 2)
-   So the statement "traversals compose" is easy to make sense of because the two cases collapse into the same thing.

Do monads compose?
------------------

-   Option 1: generally no.
-   Option 2: (generally?) yes.

Problems and Solutions
----------------------

The problem of finding or constructing representations with certain properties is something that preoccupies programmers and mathematicians alike.

-   How do they present the problem?
-   How do they determine what qualifies
    -   as an "efficient" solution?
    -   as a "formulaic" solution?

Definitions
===========

Quickly
-------

-   Categories
-   Initial and Final objects
-   Functors
-   Naturality

Universal Properties
--------------------

