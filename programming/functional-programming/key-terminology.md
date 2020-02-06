# Key Terminology

Read this entire walkthrough.

{% embed url="https://dev.to/gcanti/getting-started-with-fp-ts-setoid-39f3" %}



#### Type / Category

A type is a mathematical concept referring to a category, in lay-speak. The more formal idea of a category is better defined as:

* Consistent groupings of **objects**
* Certain transformations between those groupings, known as **morphisms**

An **object** may also be a **morhphism**, this is where **Functors** come in.

#### Function

Denoted as `f(a)` Defines a mapping between types, taking the form:

`f: a -> b`

Where `a` is the input type and `b` is the output type.

#### Algebra

An algebra is a family of functions with the signature `f: a -> a` for a given category `a`.

#### Type Constructor

A [type constructor](https://en.wikipedia.org/wiki/Type_constructor) is an `n`-ary type operator taking as argument zero or more types, and returning another type. It has the type of signature:

`a -> F<a>`

Often used to model side-effects in a program. Examples include:

`List<a>` `Option<a>` `Task<a>`

#### Catamorphism

A catamorphism is a generalised morphism concept that performs an operation known as `fold` or `reduce`.

The name comes from the Greek 'κατα-' meaning "downward or according to". A useful mnemonic is to think of a catastrophe destroying something. 

{% embed url="https://wiki.haskell.org/Catamorphisms" %}

#### Functor

A type constructor is classed as a Functor if the `lift` operation can be defined for it. To avoid further confusion let it be clear that `lift`, `map` and `fmap` can be conceptually considered equivalent.

Say we have:

`f: a -> F<b>`

`g: b -> c`

How can we compose `f` and `g` together? `lift`ing is the process of taking `b -> c` and creating `F<b> -> F<c>`. 

Which in turn lets us compose `a -> F<b>` and `F<b> -> F<c>` trivially as `a -> F<b> -> F<c>` and ultimately `a -> F<c>`.

A more complete way to define a Functor is that it is made up of two aspects:

* `A type constructor F:` a **mapping between objects** that associates to each object `X` in _a_ an object in _b_
* `A function lift:` a **mapping between morphisms** that associates to each morphism in _c_ a morphism in _d_

Examples:

```fsharp
let lift (g: b -> c) (fb: List<b>) = fb |> List.map g
```

```typescript
function lift<B, C>(g: (b: B) => C): (fb: Array<B>) => Array<C> {
  return fb => fb.map(g)
}

function lift<B, C>(g: (b: B) => C): (fb: Option<B>) => Option<C> {
  return fb => (isNone(fb) ? none : some(g(fb.value)))
}

function lift<B, C>(g: (b: B) => C): (fb: Task<B>) => Task<C> {
  return fb => () => fb().then(g)
}
```

{% embed url="https://dev.to/gcanti/getting-started-with-fp-ts-functor-36ek" %}

#### Endofunctors

An endofunctor is a functor mapping to and from the same category, `F: C -> C.`

The simplest example of an endofunctor is `id` which has the signature `a -> a`. We can define the identity endofunctor as:

```fsharp
module Identity =
    // ('a -> 'b) -> Identity<'a> -> Identity<'b>
    let map f (Identity x) = Identity (f x)
```

Similarly, the `Maybe` endofunctor has the form:

```haskell
instance Functor Maybe where
  fmap f Nothing = Nothing
  fmap f (Just x) = Just (f x)
```

If unclear, the defining property that makes a functor an endofunctor above is that `fmap` always preserves the category, which is not necessarily true for a functor.

{% embed url="https://blog.ploeh.dk/2018/09/03/the-identity-functor/" %}

#### F-Algebra

Building on the base idea of an algebra, an F-Algebra is composed of three things:

* An endofunctor `F`
* An object `a` that is "inside" the endofunctor as the carried type
* A morphism of the form `F<a> -> a`, often called the evaluation function or the structure map

Once again, `Maybe` is an example of an F-Algebra.

```haskell
data MaybeF a c = NothingF | JustF a deriving (Show, Eq, Read)
 
instance Functor (MaybeF a) where
  fmap _  NothingF = NothingF
  fmap _ (JustF x) = JustF x
```

{% embed url="https://blog.ploeh.dk/2019/05/20/maybe-catamorphism/" %}

#### Semigroup

A semigroup is a pair `(A, *)` in which `A` is a non-empty set and `*` is a binary **associative** operation on `A`, i.e. a function that takes two elements of `A` as input and returns an element of `A` as output. 

Effectively, this means that the `concat` morphism can be defined.

```typescript
interface Semigroup<A> {
  concat: (x: A, y: A) => A
}

const semigroupProduct: Semigroup<number> = {
  concat: (x, y) => x * y
}

const semigroupSum: Semigroup<number> = {
  concat: (x, y) => x + y
}

const semigroupString: Semigroup<string> = {
  concat: (x, y) => x + y
}
```

#### Applicative Functors

A Functor is classed as Applicative if the `apply` morphism can be defined for it, these are also simply known as Applicatives for short. In practical terms this property allows us to handle `n-ary` Functors rather than just `unary`.

`apply` as an operation on the functor instance `F` is able to **unpack** the value `F<(c: C) => D>` to a function `(fc: F<C>) => F<D>`.

Examples

`F = Array`

```typescript
import { flatten } from 'fp-ts/lib/Array'

const applicativeArray = {
  map: <A, B>(fa: Array<A>, f: (a: A) => B): Array<B> => fa.map(f),
  of: <A>(a: A): Array<A> => [a],
  ap: <A, B>(fab: Array<(a: A) => B>, fa: Array<A>): Array<B> =>
    flatten(fab.map(f => fa.map(f))) // flatten prevents [['a']] style output
}
```

`F = Option`

```typescript
import { Option, some, none, isNone } from 'fp-ts/lib/Option'

const applicativeOption = {
  map: <A, B>(fa: Option<A>, f: (a: A) => B): Option<B> =>
    isNone(fa) ? none : some(f(fa.value)),
  of: <A>(a: A): Option<A> => some(a),
  ap: <A, B>(fab: Option<(a: A) => B>, fa: Option<A>): Option<B> =>
    isNone(fab) ? none : applicativeOption.map(fa, fab.value)
}
```

`F = Task`

```typescript
import { Task } from 'fp-ts/lib/Task'

const applicativeTask = {
  map: <A, B>(fa: Task<A>, f: (a: A) => B): Task<B> => () => fa().then(f),
  of: <A>(a: A): Task<A> => () => Promise.resolve(a),
  ap: <A, B>(fab: Task<(a: A) => B>, fa: Task<A>): Task<B> => () =>
    Promise.all([fab(), fa()]).then(([f, a]) => f(a))
}
```

{% embed url="https://dev.to/gcanti/getting-started-with-fp-ts-applicative-1kb3" %}

#### Monoid

A `Monoid` is any `Semigroup` that happens to have a special value which is "neutral" with respect to `concat`. Do note, not all Semigroups are Monoids.

```typescript
import { Semigroup } from 'fp-ts/lib/Semigroup'

interface Monoid<A> extends Semigroup<A> {
  readonly empty: A
}
```

The following laws must hold

* **Right identity**: `concat(x, empty) = x`, for all `x` in `A`
* **Left identity**: `concat(empty, x) = x`, for all `x` in `A`

```typescript
/** number `Monoid` under addition */
const monoidSum: Monoid<number> = {
  concat: (x, y) => x + y,
  empty: 0
}

/** number `Monoid` under multiplication */
const monoidProduct: Monoid<number> = {
  concat: (x, y) => x * y,
  empty: 1
}

const monoidString: Monoid<string> = {
  concat: (x, y) => x + y,
  empty: ''
}

/** boolean monoid under conjunction */
const monoidAll: Monoid<boolean> = {
  concat: (x, y) => x && y,
  empty: true
}

/** boolean monoid under disjunction */
const monoidAny: Monoid<boolean> = {
  concat: (x, y) => x || y,
  empty: false
}
```

#### **Non-Monoidal Semigroup**

A trivial example of a semigroup for which `empty` cannot be defined to satisfy the right and left identity laws.

```typescript
const semigroupSpace: Semigroup<string> = {
  concat: (x, y) => x + ' ' + y
}
```

{% embed url="https://dev.to/gcanti/getting-started-with-fp-ts-monoid-ja0" %}

#### Monad

A Monad is simply a Monoid in the category of Endofunctors 🙃.

What this practically means is that we can define the operations:

* `of: a -> M<a>`
* `map: M<a> -> M<b>`
* `get: M<a> -> a`
* `concat: M<a> -> M<a> -> M<a>`
* `flatten: M<M<a>> -> M<a>`
* `flatMap: (a -> M<b>) -> (M<a> -> M<b>)`

#### 

#### 



**Pending questions to directly address**:

* how does `filter` fit in?
* define transducers
* draw a diagram of related terms
* how does a catamorphism interact with the definitions of semigroup, monoid and monad?
* what is the **common** set of useful morphisms?
  * map
  * get
  * concat
  * bind / chain / of\(?\)
  * apply
  * fold
  * unfold

