# intuitive_monad
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
...
### References:
Functional Programing is doing [polynomial](https://intuitive-functional-programming.blogspot.com/2017/09/why-is-functional-programming-intuitive_7.html)

