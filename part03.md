---
title: "Part 3: Types"
---

# Predefined and primitive types

*[Sources: [1][primitivetypes]]*

Below are Haskell's primitive data types. These cannot be defined in Haskell itself and must, therefore, be provided by the implementation.

Name        | Description
:-----------|:-----------
`Char`      | An enumeration whose values represent Unicode characters
`Integer`   | Arbitrary-precision integer
`Int`       | Fixed-precision integer, typically 64-bit signed integer on 64-bit platforms
`Float`     | Single-precision floating-point real number
`Double`    | Double-precision floating-point real number

# Algebraic data types

*[Sources: [1][datadecl], [2][adts]]*

Additional user-defined types can be defined as composites of other types using Haskell's support for _algebraic data types_.

Consider the following `data` declaration:

```haskell
data Colour = Red | Green | Blue
```

`Colour` is the _type constructor_ and appears on the left-hand side of the `=` sign. This type has three _data constructors_, named `Red`, `Green` and `Blue` which appear on the right-hand side of the `=` sign. You use type constructors where you'd expect a type (e.g. in type annotations) and data constructors where you'd expect a value. `Colour` is a type while `Red`, `Green` and `Blue` contain values of type `Colour`. This is known as a [_sum type_][taggedunion].

Let's try a more interesting version of this type:

```haskell
data Colour = RGB Int Int Int
```

`Colour` is still a type. However, `RGB` is not a value. It is a function taking three `Int`s and returning a value of type `Colour`:

Input                                     | Output                                                            | Comment
:-----------------------------------------|:------------------------------------------------------------------|:-------
`λ> data Colour = RGB Int Int Int`        |                                                                   | Defines type `Colour`
`λ> :info Colour`<br>or<br>`λ> :i Colour` | `data Colour = RGB Int Int Int   -- Defined at <interactive>:2:1` | Shows information about type `Colour`
`λ> :t RGB`                               | `RGB :: Int -> Int -> Int -> Colour`                              | Shows type of `RGB`
`λ> let c = RGB 10 20 30`                 |                                                                   | Assigns name `c` to a `Colour` value
`λ> :t c`                                 | `c :: Colour`                                                     | Shows type of `c`
`λ> c` | `<interactive>:13:1:`<br><code>&nbsp;&nbsp;&nbsp;&nbsp;No instance for (Show Colour) arising from a use of &#96;print'</code><br><code>&nbsp;&nbsp;&nbsp;&nbsp;In a stmt of an interactive GHCi command: print it</code> | What?
`λ> data Colour = RGB Int Int Int deriving Show`<br>`λ> let c = RGB 10 20 30`<br>`λ> c` | `RGB 10 20 30`      | That's better!

This version of `Colour` is a [_product type_][producttype]. It is isometric to a 3-tuple, or triple, of `Int`s.

## Sum types

* Equivalent to tagged union, variant, discriminated union etc. in other languages

## Product types

*[Sources: [1][cardinalityproof]]*

* Isomorphic to strongly-typed tuples
* So-called because the _cardinality_ (i.e. size or number of inhabitants) of a type is equal to the product of the cardinality of each of its component types
* Thus equivalent to a Cartesian product of finite sets
* For the Cartesian product $S \times T$ of two finite sets $S$ and $T$, $\left|{S \times T}\right| = \left|{S}\right| \times \left|{T}\right|$ where $\left|{S}\right|$ denotes cardinality
* Equivalent to records and structures in other languages

## <a name="recordsyntax"></a> Records

There is another way of defining a product type with the added convenience of automatically-generated accessor functions. This is Haskell's _record_ syntax:

```haskell
data Colour = RGB { red :: Int, green :: Int, blue :: Int }
```

Just like the previous product type definition of `Colour`, this definition consists of a triple of `Int`s. Similarly, `RGB` is a data constructor of type `Int -> Int -> Int -> Colour`. However, this definition also names the three components and generates accessor functions for them, as you can convince yourself by defining `Colour` in GHCi and using `:t` on `red`, `green`, `blue`. Each has type `Colour -> Int`: i.e. each is a function taking a `Colour` and returning an `Int`.

# Parametric polymorphism

Similarly, we might define a "pair" type as follows:

Input                                              | Output                                  |
:--------------------------------------------------|:----------------------------------------|
`λ> data Pair a b = P { first :: a, second :: b }` |                                         |
`λ> :t P`                                          | `P :: a -> b -> Pair a b`               |
`λ> :t first`                                      | `first :: Pair a b -> a`                |
`λ> :t second`                                     | `second :: Pair a b -> b`               |
`λ> indianaPi = P "pi" (4 / 1.25)`                 |                                         |
`λ> first indianaPi`                               | `"pi"`                                  |
`λ> :t first indianaPi`                            | `first indianaPi :: [Char]`             |
`λ> second indianaPi`                              | `3.2`                                   |
`λ> :t second indianaPi`                           | `second indianaPi :: Fractional b => b` |

This is our first experience of _parametric polymorphism_. In this case, `a` and `b` are type arguments/variables and define the _polymorphic type_ `Pair a b`. `Pair` is the type constructor in two type arguments, so-called because each distinct pair of concrete types substituted for `a` and `b` _construct_ a new type. As with previous `data` definitions, `P` is a data constructor and construct specific _values_ belonging to a concrete instantiation of `Pair a b`.

The type of `first indianaPi` is is also our first experience of a _list_ in Haskell which we'll discuss in more detail soon and are ubiquitous in Haskell.

# Predefined algebraic data types

We now have enough knowledge to look into some other _predefined_ Haskell types and polymorphic types that you'll run into a lot. These types are algebraic data types and can, therefore, be expressed using `data` definitions and derived from Haskell's primitive types. The "definition" is some cases is really an instructive approximation of the real definition and elide detail that is superfluous for our immediate purposes:

Name                       | Definition                  | Description
:--------------------------|:----------------------------|:-----------------------------------
`Bool`                     | `data Bool = True | False`  | A [nullary type constructor][nullaryconstructor] with two nullary data constructors `True` and `False` representing Boolean truth values
`String`                   | `type String = [Char]`      | A list of Unicode characters
`Ratio a`                  | _n/a_                       | A rational number represented as a pair of primitive numeric values
`Complex a`                | _n/a_                       | A complex floating-point number represented as a pair of primitive numeric values
`[a]`                      | `data [a] = [] | a : [a]`   | A homogeneous singly-linked list represented as a pair of head element and tail list
`(a, b)`, `(a, b, c)` etc. | `data (a, b) = (a, b)` etc. | A heterogeneous $n$-tuple
`()` ("unit")              | `data () = ()`              | [A type with the single nullary member `()`][unittype]

We'll describe `type` definitions in more detail later, but suffice it to say that `type` defines simple type aliases for other types.

# Pattern matching

Given our previous definition of `Colour` as a product type without record syntax, how do we extract the component values? This is where "pattern matching" comes in. Pattern matching is a mechanism for _deconstructing_ Haskell values, so-called because the patterns mimic the _data constructor_ invocation used to construct the value initially. Consequently, the runtime representation of values of product types retain sufficient information to allow code to determine _how_ a value was constructed at runtime.

There are two-and-a-half distinct places where you'll see pattern matching:

* In function definitions, used to deconstruct function arguments
* In `case` expressions, used to deconstruct arbitrary values
* Similar to function definitions, lambdas can also perform pattern matching on their argument

## Pattern matching in function definitions

This is equivalent to the code that the Haskell compiler generates for record accessor functions described [previously](#recordsyntax). Here's an example using our trusty `Colour` data type:

```haskell
data Colour = RGB Int Int Int

red :: Colour -> Int
red (RGB r _ _) = r

green :: Colour -> Int
green (RGB _ g _) = g

blue :: Colour -> Int
blue (RGB _ _ b) = b
```

* `_` ("unknown") is a "throwaway" name: it matches a value but does not assign a name to it which is useful when we don't care about that specific value
* You'll see it used a lot
* It can also be used to represent a [typed hole][typedholes]
* `RGB r _ _` matches the value of type `Colour` on its `RGB` data constructor of type `Int -> Int -> Int -> Colour`, matching `r` to the first value of the triple and ignoring the second and third values
* Interestingly, "regular" function argument names are really just a degenerate case of pattern matching

## Pattern matching using `case`

An alternative implementation can make use of a `case` expression:

```haskell
data Colour =
    RGB Int Int Int |
    CMYK Float Float Float Float

colourModelV1 :: Colour -> String
colourModelV1 (RGB _ _ _) = "RGB"
colourModelV1 (CMYK _ _ _ _) = "CMYK"

colourModelV2 :: Colour -> String
colourModelV2 c =
    case c of RGB _ _ _ -> "RGB"
              CMYK _ _ _ _ -> "CMYK"

main :: IO ()
main =
    let c1 = CMYK 0.5 0.5 0.5 0.0
        cm1 = colourModelV1 c1
        c2 = RGB 50 100 150
        cm2 = colourModelV2 c2
    in putStrLn ("cm1=" ++ cm1 ++ ", cm2=" ++ cm2)
```

This will yield `cm1=CMYK, cm2=RGB`.

Patterns can be matched both in function argument position and in `case` expressions. Here's a contrived example using our `RGB`/`CMYK` definition of `Colour`:

```haskell
data Point = Point { x :: Int, y :: Int }

data Line = Line
    { start :: Point
    , end :: Point
    , thickness :: Int
    , colour :: Colour
    }

lineRedness :: Line -> Int
lineRedness (Line _ _ _ (RGB r _ _)) = r

main :: IO ()
main =
    let l = Line
                (Point 10 10)
                (Point 50 50)
                1
                (RGB 255 0 0)
    in print (lineRedness l)
```

This will output `255`.

## Exhaustiveness of pattern matches

This example illustrates another important aspect of pattern matching, namely _exhaustiveness_. Let's change our `main` function to the following to illustrate this:

```haskell
main :: IO ()
main =
    let l = Line
                (Point 10 10)
                (Point 50 50)
                1
                (CMYK 0.5 0.5 0.5 0.0)
    in print (lineRedness l)
```

Let's compile and run this. Here's the output:

```console
scratch: src/Main.hs:15:1-40: Non-exhaustive patterns in function lineRedness
```

Well, that's interesting but makes sense. The pattern in the definition of `lineRedness` cannot match the value `CMYK 0.5 0.5 0.5 0.0` since it was not constructed using the `RGB` data constructor.

This class of issue can be addressed in one of [several ways][haskellerrorreporting]:

* Provide a pattern match on the `CMYK` data constructor: this would require a valid conversion from the `CMYK` representation of a colour to `RGB` in order to provide a red component
* Provide a fall-through case using `_` to match any `Colour` value after the `RGB` match: this would require the existence of some "default" notion of line redness
* Augment the return type to include the notion of failure

The option you will choose will depend primarily on the semantics of the function. To make this determination, you'd need to ask questions such as:

* Is there a valid "conversion" from the other data constructors?
* Is there a semantically reasonable default value?

I'll illustrate these three alternatives to the existing behaviour. Note that there are also other ways to provide different runtime error messages through Haskell's various [exception mechanisms][haskellisexceptionallyunsafe] etc. We will discuss these more when we get to I/O.

Here we pattern-match on `CMYK` and apply a [formula][cmytorgb]:

```haskell
lineRedness :: Line -> Int
lineRedness (Line _ _ _ (RGB r _ _)) = r
lineRedness (Line _ _ _ (CMYK c _ _ _)) = round ((1.0 - c) * 255.0)
```

Look sideways and ignore the fact that we're ignoring the K-component!

Here we provide a catch-all or fall-through case:

```haskell
defaultRed :: Int
defaultRed = 0

lineRedness :: Line -> Int
lineRedness (Line _ _ _ (RGB r _ _)) = r
lineRedness _ = defaultRed
```

Here we augment the return type:

```haskell
lineRedness :: Line -> Maybe Int
lineRedness (Line _ _ _ (RGB r _ _)) = Just r
lineRedness _ = Nothing
```

We'll definitely talk more about `Maybe` later. Suffice it to say for now that it's a type constructor taking one type variable and is analogous to [option types][optiontypes] and [nullable types][nullabletypes] in other languages.

## Can't we do better?

*[Sources: [1][nonexhaustive1], [2][nonexhaustive2], [3][nonexhaustive3], [4][nonexhaustive4]]*

Something about this may irk you. Given that the compiler has full information about a given type's data constructor (at least in code defined in the same module as the type), surely it must be possible to detect when a pattern match misses one or more cases at compile time instead of at runtime. In fact, you should be demanding an explanation of this, given Haskell's much-vaunted strong static type system and supposed type safety.

Well, it turns out that this can be detected at compile time by enabling the `incomplete-patterns` warning. From GHCi, this can be done using the `:set` command:

```ghci
λ> :set -Wincomplete-patterns
λ> data Colour = Red | Green | Blue
λ> render :: Colour -> String; render Red = "red"

<interactive>:5:29: warning: [-Wincomplete-patterns]
    Pattern match(es) are non-exhaustive
    In an equation for ‘render’:
        Patterns not matched:
            Green
            Blue
```

When compiling source files, the same can be achieved by passing `-fwarn-incomplete-patterns` on the GHC command line, setting `ghc-options` in your project's [.cabal][cabaluserguide] file or by inserting a "pragma" into the top of a source file as follows:

```haskell
{-# OPTIONS_GHC -fwarn-incomplete-patterns #-}

data Colour = Red | Green | Blue

render :: Colour -> String
render Red = "red"

main :: IO ()
main = putStrLn (render Green)
```

However, this is still just a warning: therefore, the build still succeeds and the program still runs and fails at runtime. Therefore, we need to promote warnings to errors.

```haskell
{-# OPTIONS_GHC -fwarn-incomplete-patterns -Werror #-}

data Colour = Red | Green | Blue

render :: Colour -> String
render Red = "red"

main :: IO ()
main = putStrLn (render Green)
```

This will generate a similar warning as above and will abort the build.

So, why? The general consensus, among GHC developers at least, seems to be that the exhaustiveness checker ["is not very good"][nonexhaustive5]: it tends to report false positives and counterintuitive error messages given in terms of desugared forms instead of the original source code&mdash;we'll look at desugaring a little more later.

Personally, I think that this is a bit of a wart on Haskell in principle, but&mdash;in practice&mdash;I've never run into a false positive and so would recommend enabling the warning and promoting warnings to errors.

# Type aliases

Haskell provides `type` definitions used to create aliases, or alternative names, for existing types:

```haskell
type Ordinate = Int

data Point = Point Ordinate Ordinate

doubleInt :: Int -> Int
doubleInt x = x * 2

doubleX :: Point -> Int
doubleX (Point x _) = doubleInt x

main :: IO ()
main = print (doubleX (Point 100 200))
```

You'll see `type` used a lot in Haskell code even though it doesn't give you any type safety&mdash;as shown in the sample above, `Ordinate` is completely indistinguishable from a regular `Int`.

# Strongly-typed wrapper types using `newtype`

`newtype`, on the other hand, is an altogether different beast:

```haskell
newtype Ordinate = Ordinate { unOrdinate :: Int }

data Point = Point Ordinate Ordinate

doubleInt :: Int -> Int
doubleInt x = x * 2

doubleX :: Point -> Int
doubleX (Point x _) = doubleInt x

main :: IO ()
main = print (doubleX (Point 100 200))
```

Attempting to compile this example will result in the following:

```console
Main.hs:9:33: error:
    • Couldn't match expected type ‘Int’ with actual type ‘Ordinate’
    • In the first argument of ‘doubleInt’, namely ‘x’
      In the expression: doubleInt x
      In an equation for ‘doubleX’: doubleX (Point x _) = doubleInt x
```

`newtype` defines a distinct new type whose internal representation is equivalent to a base type. In this case the new type is `Ordinate` and the underlying (base) type is `Int`. Syntactically, a `newtype` definition is closer to a `data` definition&mdash;with exactly one data constructor and exactly one field inside it, which can be specified in normal or record syntax. Furthermore, like `data` and unlike `type`, the resulting type is distinct from, and not directly compatible with, the original type. In order to pass an `Ordinate` into a function expecting an `Int`, as in this example, one must first unwrap the field either using pattern matching or, in the case of record-style syntax, using the accessor function:

```haskell
newtype Ordinate = Ordinate { unOrdinate :: Int }
data Point = Point { x :: Ordinate, y :: Ordinate }

translateBy :: Int -> Int -> Int
translateBy o ord = ord + o

main :: IO ()
main =
    let p = Point (Ordinate 10) (Ordinate 20)
        translate = translateBy 10
        translatedP = Point (Ordinate (translate (unOrdinate (x p)))) (Ordinate (translate (unOrdinate (y p))))
        translatedX = unOrdinate (x translatedP)
        translatedY = unOrdinate (y translatedP)
    in putStrLn ("x=" ++ show translatedX ++ ", y=" ++ show translatedY)
```

We will return to this example shortly to explain some new things you may have spotted as well as some ways to make this less ugly while retaining type safety.

# Miscellaneous

*[Sources: [1][sections]]*

This last example is ugly. Can we make it more pleasing to the eye?

Recall `Ordinate (translate (unOrdinate someExpression))`. What we're really doing here is applying three functions in turn: we apply `unOrdinate` to `someExpression`, `translate` to the result of that and `Ordinate` to the result of that. This should leap out at you: it's simply the composition of three functions. This can, therefore, be rewritten as `(Ordinate . translate . unOrdinate) someExpression`. This doesn't save many characters of typing, but it does reveal a nice structure to the code by emphasizing the "valueness" of functions, since `Ordinate . translate . unOrdinate` is a value just like any other. This is core to a functional programming language.

Let's use `$` and also observe that `translateBy` is really just the "add this value to my argument function", which is the same as a _partially_ applied `+` operator. Here's our ugly `Point` example rewritten to use `.` and `+`:

```haskell
newtype Ordinate = Ordinate { unOrdinate :: Int }
data Point = Point { x :: Ordinate, y :: Ordinate }

main :: IO ()
main =
    let p = Point (Ordinate 10) (Ordinate 20)
        translateOrdinate = Ordinate . ((+) 10) . unOrdinate
        translatedP = Point (translateOrdinate (x p)) (translateOrdinate (y p))
        translatedX = unOrdinate $ x translatedP
        translatedY = unOrdinate $ y translatedP
    in putStrLn $ "x=" ++ show translatedX ++ ", y=" ++ show translatedY
```

Partial application of a binary operator is known as a _left_ or _right_ _section_ depending on the order in which the operands are handled. Conceptually:

* `(2^)` (left section) is equivalent to `\x -> 2 ^ x`
* `(^2)` (right section) is equivalent to `\x -> x ^ 2`

Of course, [there is more than one way to skin a cat][skinningacat], and this code can be elegantly reformulated using pattern matching:

```haskell
newtype Ordinate = Ordinate { unOrdinate :: Int } deriving Show
data Point = Point { x :: Ordinate, y :: Ordinate } deriving Show

translatedPoint :: Int -> Int -> Point -> Point
translatedPoint xOffset yOffset (Point (Ordinate xValue) (Ordinate yValue))
    = Point (Ordinate $ xValue + xOffset) (Ordinate $ yValue + yOffset)

main :: IO ()
main =
    let p = Point (Ordinate 10) (Ordinate 20)
        translatedP = translatedPoint 10 10 p
    in print translatedP
```

This also takes advantage of _automatic deriving_ to eliminate the calls to `show`.

[adts]: https://en.wikipedia.org/wiki/Algebraic_data_type
[cabaluserguide]: https://www.haskell.org/cabal/users-guide/
[cardinalityproof]: https://proofwiki.org/wiki/Cardinality_of_Cartesian_Product
[cmytorgb]: http://www.easyrgb.com/index.php?X=MATH&H=12#text12
[datadecl]: http://stackoverflow.com/questions/18204308/haskell-type-vs-data-constructor
[haskellerrorreporting]: http://www.randomhacks.net/2007/03/10/haskell-8-ways-to-report-errors/
[haskellisexceptionallyunsafe]: https://existentialtype.wordpress.com/2012/08/14/haskell-is-exceptionally-unsafe/
[optiontypes]: https://en.wikipedia.org/wiki/Option_type
[nonexhaustive1]: http://stackoverflow.com/questions/3804484/in-haskell-why-non-exhaustive-patterns-are-not-compile-time-errors
[nonexhaustive2]: https://blogs.janestreet.com/what-do-haskellers-have-against-exhaustiveness/
[nonexhaustive3]: http://stackoverflow.com/questions/31866379/haskell-non-exhaustive-pattern-matching-in-haskell
[nonexhaustive4]: https://mail.haskell.org/pipermail/glasgow-haskell-users/2009-May/017272.html
[nonexhaustive5]: https://mail.haskell.org/pipermail/glasgow-haskell-users/2009-May/017225.html
[nullabletypes]: https://en.wikipedia.org/wiki/Nullable_type
[nullaryconstructor]: https://en.wikipedia.org/wiki/Nullary_constructor
[pragmas]: https://downloads.haskell.org/~ghc/7.0.3/docs/html/users_guide/pragmas.html
[primitivetypes]: https://downloads.haskell.org/~ghc/7.0.1/docs/html/users_guide/primitives.html
[producttype]: https://en.wikipedia.org/wiki/Product_type
[sections]: https://wiki.haskell.org/Section_of_an_infix_operator
[skinningacat]: http://english.stackexchange.com/questions/32123/origin-of-the-phrase-theres-more-than-one-way-to-skin-a-cat
[taggedunion]: https://en.wikipedia.org/wiki/Tagged_union
[typedholes]: https://wiki.haskell.org/GHC/Typed_holes
[unittype]: https://en.wikipedia.org/wiki/Unit_type
