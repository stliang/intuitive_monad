# intuitive_monad
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

## Functor Law
### identity law
```
fmap id = id
```
### composition law
```
fmap (g . f) = fmap g . fmap f
```
## Applicative Law
### identity law
```
pure id <*> v = v
```
### homomorphism law
```
pure f <*> pure x = pure (f x)
```
### interchange law
```
u <*> pure y = pure ($ y) <*> u
```
### composition law
```
pure (.) <*> u <*> v <*> w = u <*> (v <*> w)
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
- Monad is [nesting] of functions(https://intuitive-functional-programming.blogspot.com/2017/09/prove-functor-applicative-and-monad-are.html)
- Functor, Applicative, and Monad [laws](https://mmhaskell.com/monads/laws)

