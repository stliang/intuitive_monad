# An Algebraic Approach to Programming
## Key Functions
```
fmap :: Functor f => (a -> b) -> f a -> f b
(.) :: (b -> c) -> (a -> b) -> a -> c
(<*>) :: Applicative f => f (a -> b) -> f a -> f b
(>>=) :: m a -> (a -> m b) -> m b
return :: Monad => a -> m a
pure :: Applicative m => a -> m a
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
Prelude> f = (+) 2
Prelude> fmap f [1]
[3]
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
Identify law gives us base value behavior in folding operation over applicative instance.  Since "pure id" puts id funciton inside an applicative and <*>'s second argument is an applicative instance, it just means apply the id function to value inside the applicative v.  Since id :: a -> a, nothing changes in v.  This is the same identity behavior as in Functor except the legal test is done with the apply function aka "<*>".
#### Homomorphism Law
```
pure f <*> pure x = pure (f x)
```
#### Interchange Law
```
u <*> pure y = pure ($ y) <*> u
```
#### Composition Law
```
pure (.) <*> u <*> v <*> w = u <*> (v <*> w)
```
### Exercise
```
Prelude> f = (+) 2
Prelude> g = (*) 2
Prelude> (<*>) [f,g] [1,2,3,4]
[3,4,5,6,2,4,6,8]
```
## Monad Law
### identity laws
```
return a >>= f = f
m >>= return = m
```
### associativity law
```
(m >>= f) >>= g = m >>= (\x -> f x >>= g)
```

### References:
- Class [hierarchy](https://wiki.haskell.org/Typeclassopedia)
- => Type [constraint](https://stackoverflow.com/questions/39084551/what-does-the-sign-mean-in-haskell)
- Operator [pronunciation](https://stackoverflow.com/questions/7746894/are-there-pronounceable-names-for-common-haskell-operators)
- Functional Programing is doing [polynomial](https://intuitive-functional-programming.blogspot.com/2017/09/why-is-functional-programming-intuitive_7.html)
- Monad is [nesting of functions](https://intuitive-functional-programming.blogspot.com/2017/09/prove-functor-applicative-and-monad-are.html)
- Functor, Applicative, and Monad [laws](https://mmhaskell.com/monads/laws)
- Search Haskell Functions in [Hoogle](https://hoogle.haskell.org/)

