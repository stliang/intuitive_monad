![Functional Programming](https://github.com/stliang/intuitive_monad/blob/main/glovebox.jpeg?raw=true)
# An Algebraic Approach to Programming
This exercise hope to show the similarity between composing polynomial equation and Functional Programing.  We will look at the laws of Algebraic objects such as Monoid, Functor, Applicative,and Monad because they made up the machinary of polynomial equation.

### Key Functions
fmap function:
```
fmap :: Functor f => (a -> b) -> f a -> f b
```
compose function:
```
(.) :: (b -> c) -> (a -> b) -> a -> c
```
apply function:
```
(<*>) :: Applicative f => f (a -> b) -> f a -> f b
```
bind function:
```
(>>=) :: m a -> (a -> m b) -> m b
```
return function:
```
return :: Monad => a -> m a
```
pure function:
```
pure :: Applicative m => a -> m a
```
Left-to-right Kleisli function:
```
(>=>) :: Monad m => (a -> m b) -> (b -> m c) -> a -> m c
```
right-associative fold function:
```
foldr :: Foldable => (a -> b -> b) -> b -> t a -> b
```
### Class Hierarchy
![class hierarchy diagram](https://github.com/stliang/intuitive_monad/blob/main/Typeclassopedia-diagram.png)

## Functor
### Definition
```
class Functor f where
  fmap :: (a -> b) -> f a -> f b

  (<$) :: a        -> f b -> f a
  (<$) = fmap . const
```
fmap allows the transformation of one type to and other type also implies one value to another value

### Functor Instance
```
instance Functor [] where
  fmap :: (a -> b) -> [a] -> [b]
  fmap _ []     = []
  fmap g (x:xs) = g x : fmap g xs
```
The [] is a type constructor where [1] is a list of num
### Laws
#### Identity Law
```
fmap id = id
```
Identify law gives us base value behavior in folding operation over functor instance.  Like 0 when folding with (+) or 1 when folding with (*) in type num.
#### Composition Law
```
fmap (g . f) = fmap g . fmap f
```
In value transformation inside a functor, the composition law gives us the ability to transform value with a compostion of functions.
### Exercise
```
-- ID Test
Prelude> fmap id [2]
[2]
Prelude> id [2]
[2]
-- Composition Test
Prelude> f = (+) 2
Prelude> g = (*) 2
Prelude> q = fmap( g . f)
Prelude> q [4]
[12]
Prelude> p = fmap g . fmap f
Prelude> p [4]
[12]
```
## Applicative
### Definition
```
class Functor f => Applicative f where
  pure  :: a -> f a
  infixl 4 <*>, *>, <*
  (<*>) :: f (a -> b) -> f a -> f b

  (*>) :: f a -> f b -> f b
  a1 *> a2 = (id <$ a1) <*> a2

  (<*) :: f a -> f b -> f a
  (<*) = liftA2 const
```
The "=>" in class definition is a type constraint and I read it literally as Functor f is "greater than or equal to" Applicative f.  That is Appilcative f can not be more general than Functor f.  In another word, Applicative is a subclass of Functor.

### Applicative Instance
```
instance Applicative [] where
  pure :: a -> [a]
  pure x = [x]

  (<*>) :: [a -> b] -> [a] -> [b]
  gs <*> xs = [ g x | g <- gs, x <- xs ]
```
### Laws

#### Identity Law
```
pure id <*> v = v
```
Identify law gives us base value behavior in folding operation over applicative instance.  Since "pure id" puts id funciton inside an applicative and `<*>`'s second argument is an applicative instance, it just means apply the id function to value inside the applicative v.  Since id :: a -> a, nothing changes in v.  This is the same identity behavior as in Functor except the legal test is done with the apply function aka `<*>`.
#### Homomorphism Law
```
pure f <*> pure x = pure (f x)
```
A homomorphism is a map between two algebraic structures of the same type (that is of the same name), that preserves the operations of the structures. That is if a function "f" is put inside an applicative then apply to a value x inside another applicative, the result should be the same as evalue f of x then put it in an applicateive instance.

#### Interchange Law
```
u <*> pure y = pure ($ y) <*> u
```
Intuitively, this says that when evaluating the application of an effectful function to a pure argument, the order in which we evaluate the function and its argument doesn't matter.
#### Composition Law
```
pure (.) <*> u <*> v <*> w = u <*> (v <*> w)
```
In some sense it is expressing a sort of associativity property of (<*>).

Composition allows reassociating (<*>); interchange allows moving occurrences of pure leftwards; and homomorphism allows collapsing multiple adjacent occurrences of pure into one.
### Exercise
```
-- ID Test
Prelude> pure id <*> [1]
[1]
-- Homomorphism Test
Prelude> f = (*) 2
Prelude> x = 2
Prelude> pure f <*> pure x
4
Prelude> pure ( f x)
4
-- Interchange Test
Prelude> u = [f]
Prelude> y = 4
Prelude> u <*> pure y
[8]
Prelude> pure ($ y) <*> u
[8]
-- Composition Test
Prelude> g = (+) 6
Prelude> u = [g]
Prelude> h = (+) 7
Prelude> v = [h]
Prelude> w = [8]
Prelude> pure (.) <*> u <*> v <*> w
[21]
-- Application of a list of functions
Prelude> f = (+) 2
Prelude> g = (*) 2
Prelude> (<*>) [f,g] [1,2,3,4]
[3,4,5,6,2,4,6,8]
```
## Monad
### Definition
```
class Applicative m => Monad m where
  return :: a -> m a
  (>>=)  :: m a -> (a -> m b) -> m b
  (>>)   :: m a -> m b -> m b
  m >> n = m >>= \_ -> n
```
### Monad Instance
```
instance Monad Maybe where
  return :: a -> Maybe a
  return = Just

  (>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b
  (Just x) >>= g = g x
  Nothing  >>= _ = Nothing
```
Maybe can be think of as a single cell list, that is may be there is something in the cell or may be not.
### Laws
#### Identity Laws
```
return a >>= k  =  k a
m >>= return    =  m
```
Same law expressed in Kleisli arrow
```
return >=> g  =  g
g >=> return  =  g
```
These are the left and right identities.
#### Associativity Law
```
m >>= (\x -> k x >>= h)  =  (m >>= k) >>= h
```
Same law expressed in Kleisli arrow
```
(g >=> h) >=> k  =  g >=> (h >=> k)
```
The associativity of a binary operator allows for any number of operands to be combined by applying the binary operator with any arbitrary grouping to get the same well-defined result, just like the result of summing up a list of numbers is fully defined by the binary (+) operator no matter which parenthesization is used (yes, just like in folding a list of any type of monoidal values).

### Exercise
```
-- Identity
Prelude> a = 2
Prelude> k = \x -> [x]
Prelude> return a >>= k
[2]
Prelude> k a
[2]
Prelude> m = [3]
Prelude> m >>= return
[3]
Prelude> m
[3]
-- Associativity Test Kleisli Function
Prelude> import Control.Monad
Prelude Control.Monad> g = \x -> x + 2
Prelude Control.Monad> g = \x -> [x + 2]
Prelude Control.Monad> h = \y -> [y * 2]
Prelude Control.Monad> k = \z -> [z + 4]
Prelude Control.Monad> f = (g >=> h) >=> k
Prelude Control.Monad> f 5
[18]
Prelude Control.Monad> f = g >=> (h >=> k)
Prelude Control.Monad> f 5
[18]
-- Associativity Test with Bind Function
Prelude Control.Monad> m = [5]
Prelude Control.Monad> m >>= (\x -> k x >>= h)
[18]
Prelude Control.Monad> (m >>= k) >>= h
[18]
```
## Monoid
### Definition
```
class Monoid a where
  mempty  :: a
  mappend :: a -> a -> a

  mconcat :: [a] -> a
  mconcat = foldr mappend mempty
```
Many semigroups have a special element e for which the binary operation \oplus is the identity, that is, e \oplus x = x \oplus e = x for every element x. Such a semigroup-with-identity-element is called a monoid.  For example, 0 to the operation of addition (+) and 1 to the operation of multiplication (*).

### Monoid Instance
```
[a] is a Monoid, with mempty = [] and mappend = (++).
```
### Laws
#### Identity Laws
```
mempty `mappend` x = x
x `mappend` mempty = x
```
Left and right identity laws
#### Associativity Law
```
(x `mappend` y) `mappend` z = x `mappend` (y `mappend` z)
```

Use Monoid to make ((,) e) an instance of Applicative:
```
instance Monoid e => Applicative ((,) e) where
  pure :: Monoid e => a -> (e,a)
  pure x = (mempty, x)

  (<*>) :: Monoid e => (e,a -> b) -> (e,a) -> (e,b)
  (u, f) <*> (v, x) = (u `mappend` v, f x)
```
### Exercise
```
-- Identity
Prelude> mempty `mappend` [5]
[5]
Prelude> [5]
[5]
Prelude> [5] `mappend` mempty
[5]
Prelude> [5]
[5]
-- Associativity Test
Prelude> ([1] `mappend` [2]) `mappend` [3]
[1,2,3]
Prelude> [1] `mappend` ([2] `mappend` [3])
[1,2,3]
```
##
A category is a collection of "objects" that are linked by "arrows". A category has two basic properties: the ability to compose the arrows associatively and the existence of an identity arrow for each object. A simple example is the category of sets, whose objects are sets and whose arrows are functions.

![A Category](https://github.com/stliang/intuitive_monad/blob/50d300d75ec2deef326db5d48a24d06f7debd76c/a_category.png)

### References:
- [Slides](https://docs.google.com/presentation/d/187Uf3EwmZEq_VXibjZ86ienC8IDR2jN5zuajZVq7drM/edit?usp=sharing)
- Class [hierarchy](https://wiki.haskell.org/Typeclassopedia)
- => Type [constraint](https://stackoverflow.com/questions/39084551/what-does-the-sign-mean-in-haskell)
- Operator [pronunciation](https://stackoverflow.com/questions/7746894/are-there-pronounceable-names-for-common-haskell-operators)
- Functional Programing is doing [polynomial](https://intuitive-functional-programming.blogspot.com/2017/09/why-is-functional-programming-intuitive_7.html)
- Monad is [nesting of functions](https://intuitive-functional-programming.blogspot.com/2017/09/prove-functor-applicative-and-monad-are.html)
- Functor, Applicative, and Monad [laws](https://mmhaskell.com/monads/laws)
- Search Haskell Functions in [Hoogle](https://hoogle.haskell.org/)

