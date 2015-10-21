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

-   [Categories](https://en.wikipedia.org/wiki/Category_(mathematics))
-   [Initial and Final objects](https://en.wikipedia.org/wiki/Initial_and_terminal_objects)
-   [Functors](https://en.wikipedia.org/wiki/Functor)
-   [Naturality](https://en.wikipedia.org/wiki/Natural_transformation)

Universal Properties
--------------------

-   A *universal property* defines what it means for a construction to be the "most efficient" solution to a problem.

-   Comes in two flavours:
    -   Initial Properties
    -   Terminal properties
-   They are dual, so usually only discussion of one is necessary,
just reverse the arrows for the other.

Initial Property
----------------

`C, D` categories, `U: D -> C` , let `X ∈ Obj(C)`

An *initial morphism* is a pair `(A, φ)` where `A ∈ Obj(D)` and `φ: X -> U(A)` then `∀Y ∈ Obj(D)` and `f: X -> U(Y)`, there exists a **unique** `g: A -> Y` such that the following diagram commutes:

------------

![An initial property](./assets/universal-prop.png)

Examples
--------

Coproducts
----------

![The initial property for products](./assets/coproduct-initial-property.png)

Products
--------

![The terminal property for products](./assets/product-terminal-property.png)

Efficiency
----------

What makes `(A, φ)` so efficient?

-   The fact that `g` exists at all implies that `(A, φ)` is general enough for all `(Y, f)`.
-   The fact that `g` is unique implies that `(A, φ)` is not too general

The existence and uniqueness mean that `f` **uniquely factorises** like so: `f = U(g).φ`, so rather than analyse `f: X -> U(Y)`, we can look at `g: A -> Y` which could be a lot simpler.

Adjunctions
-----------

-   An efficient solution is great, whats even **better** is if it is *formulaic*, i.e for *every* `X` in the diagram, I can construct an `(A = F(X), φ)`.

-   Then my solution is not only *efficient*, I can also spray it indiscriminately over my problem domain.

-----------

For `F: D -> C` and `G: C -> D` functors between locally small categories `C` and `D`

`F` and `G` are said to be *adjoint* with `F` *left adjoint* to `G` and `G` *right adjoint* to `F` iff there is a bijection `ϕ`, natural in `X` and `Y`, any `X ∈ Obj(C)`, any `Y ∈ Obj(D)`:

![](./assets/adjunction-bijection.png)

-----------

![The naturality of ϕ](./assets/adjunction-naturality.png)

-----------

The naturality of ϕ

``` Haskell
k: X -> X'
h: Y -> Y'

ϕ(k.f)  = Gk.ϕ(f)
ϕ(f.Fh) = ϕ(f).h
```
Monads
=======

-------

The adjunction holds `∀X ∈ Obj(C)` and `FY ∈ Obj(C)`, so what do we get if we substitute `FY` for `X`?

``` Haskell
C(FY, FY) ≅ D(Y, G(FY))
```

--------

The only morphism we have that exists in all `C(FY, FY)` across all categories `C` would be the identity, so this is begging to be applied to the identity. Let's denote that `η = ϕ(id) :: Y -> G(FY)`.

Similarly since `ϕ` is a bijection, lets apply it to `id ∈ D(GX, GX)` and keep that for later: `ε = ϕ^(-1)(id) :: F(G(X)) -> X`

---------

Hmm, `F, G` are Functors... so:

``` Haskell
μ = G(ε) :: G(F(G(F(Y)))) -> G(F(Y)) -- I've substituted FY for X
δ = F(η) :: F(G(X)) -> F(G(F(G(X)))) -- I've substituted GX for Y
```
Recap
-----

``` Haskell
η = ϕ(id) :: Y -> G(FY)
ε = ϕ^(-1)(id) :: F(G(X)) -> X
μ = G(ε) :: G(F(G(F(Y)))) -> G(F(Y))
δ = F(η) :: F(G(X)) -> F(G(F(G(X))))
```

Recap
-----

Save myself some writing, denote `M = G . F`, `W = F . G`

``` Haskell
η = ϕ(id) :: Y -> MY        -- unit
ε = ϕ^(-1)(id) :: WX -> X   -- counit
μ = G(ε) :: M(M(Y)) -> MY   -- join
δ = F(η) :: WX -> W(W(X))   -- duplicate
```

Left Identity
-------------

``` Haskell
μ . η = G(ε).ϕ(id)
μ . η = G(ϕ^(-1)(id)).ϕ(id)
μ . η = ϕ(ϕ^(-1)(id) . id)  -- naturality
μ . η = ϕ(ϕ^(-1)(id))
μ . η = id
```

Right Identity
--------------

``` Haskell
μ . GF(η) = G(ε).GF(η)
μ . GF(η) = G(ε.F(η))
μ . GF(η) = G(ϕ^(-1)(ϕ(ε.F(η))))
μ . GF(η) = G(ϕ^(-1)(ϕ(ε).η))
μ . GF(η) = G(ϕ^(-1)(ϕ(ϕ^(-1)(id)).η))
μ . GF(η) = G(ϕ^(-1)(id.η))
μ . GF(η) = G(ϕ^(-1)(η))
μ . GF(η) = G(id)
μ . GF(η) = id
```

Associativity
-------------

``` Haskell
μ . GF(μ) = G(ε).GF(μ)
μ . GF(μ) = G(ε.F(μ))
μ . GF(μ) = G(ϕ^(-1)(ϕ(ε.F(μ))))
μ . GF(μ) = G(ϕ^(-1)(ϕ(ε).μ))
μ . GF(μ) = G(ϕ^(-1)(id.μ))
μ . GF(μ) = G(ϕ^(-1)(μ))
μ . GF(μ) = G(ϕ^(-1)(G(ε)))
μ . GF(μ) = G(ϕ^(-1)(G(ε).id))
μ . GF(μ) = G(ε.ϕ^(-1)(id))
μ . GF(μ) = G(ε.ε)
μ . GF(μ) = G(ε).G(ε)
μ . GF(μ) = μ . μ
```

So M is a monad
---------------

W is a comonad
--------------

Err... an "Exercise"?

Converse?
---------

Can all (co)monads be broken up into adjoint functor pairs?

Yes, there are at least two such pairs that exist for all monads (and comonads).  They are kind of trivial constructions though, and involve "exotic" categories, so they are less useful to the Scala and Haskell programmers (Where you are stuck in `Hask` or whatever the Scala equivalent is):

-   [The Kleisli Category](https://en.wikipedia.org/wiki/Kleisli_category)
-   [Eilenberg-Moore algebras](https://en.wikipedia.org/wiki/Eilenberg%E2%80%93Moore_algebra)

Universal Property
------------------

``` Haskell
h :: Y -> GZ
h = ϕ(k)        -- k = ϕ^(-1)(h) :: FY -> Z
h = ϕ(k . id)
h = Gk . ϕ(id)  -- naturality
h = Gk . η
```

Uniqueness
----------

So all `h :: Y -> GZ` can be factorised. But is `k` unique?

assume it isn't,

``` Haskell
k :: FY -> Z
k' :: FY -> Z
-- assume k, k' "distinct"
(1) h = Gk . η and (2) h = Gk' . η
(1) -> h = ϕ(k), and (2) -> h = ϕ(k') -- reverse the existence proof
-- But ϕ is a bijection, so k = k', which contradicts the assumption
```

Universal Property
------------------

![general unit initial property](./assets/unit-universal-prop.png)

------------------

![slightly specialised unit initial property](./assets/monad-universal-prop.png)

Context?
--------

-   The functor `F` represents the construction upon `Y` that represents this "context" thing that everyone talks about.
-   As all operations of the form `Y -> MY'` can be reduced to functions of type `FY -> FY'`.
-   The functor `G` represents the reverse direction, it represents the "problem" that you "want something in `FY` but lack the components to make the construction"

Deferred Problems
-----------------

Monads lets us defer the construction problem. We can effectively pretend the problem doesn't exist.

-   We can use `η` to get us into `G(FY)`
-   Then `fmap` so that we can work inside `D(FY, FY')`, helping us pretend the problem doesn't exist.
-   `μ` can let us "eliminate" the problem inside this fantasy land where it doesn't exist
-   we can ignore the problem of `GX -> X` until such a time as we have what we need.

This is why monads are so useful...

Deferred Solutions
------------------

Comonads are the opposite:

-   We have objects of `F(GX)`, we have a constructed solution around a value waiting for the stuff in `F`, just waiting to be unrolled with `ε`. At any time.

Applications for this exist, but they seem harder to imagine...

-------------

Monads let us abstract away problems, comonads let us hold solutions for ransom.

Monad Transformers
------------------

So now can we structurally decompose monads to embed others?

For `F: D -> C` and `G: C -> D` functors between locally small categories `C` and `D`, if we have a monad `M: C -> C` is `G . M . F` a monad?

Yes it is (Exercise)

Examples
========

State Monad
-----------

This is the famous one, its pretty much the only one that can be broken up into two functors `Hask -> Hask`

``` Haskell
type F s a = (a, s)
type G s a = s -> a
ϕ = curry
```

-----------

```
curry :: ((a, b) -> c) -> a -> b -> c
curry id :: a -> b -> (a, b)            -- unit
uncurry id :: (b -> c, b) -> c          -- counit
fmap (uncurry id) :: Functor f => f (b1 -> b, b1) -> f b    -- join
```

Continuation Monad
------------------

This is a unique example where the Functor leaves `Hask` and passes through `Hask^(op)` before coming back, it is adjoint to itself.

``` Haskell
type C r a = a -> r
type Cont a = forall r. (a -> r) -> r ~ forall r. (C r (C r a))
```

-----------

For `ϕ` we want a function:

``` Haskell
--  Hask ^(op)      |   Hask
ϕ :: (C r a ~> b) -> (a -> C r b)
ϕ :: (C r a <- b) -> (a -> C r b)
ϕ :: (b -> C r a) -> (a -> C r b)
ϕ :: (b -> (a -> r)) -> (a -> C r b)
ϕ :: (b -> a -> r) -> (a -> (b -> r))
ϕ :: (b -> a -> r) -> (a -> b -> r)
ϕ = flip
```

Free Constructions
------------------

```
F ~ Free Construction
U ~ Forgetful functor
ϕ :: (FY -> X) <-> (Y -> GZ)
```

References
==========

TODO....
