# Module Documentation

## Module Data.Maybe

#### `Maybe`

The `Maybe` type is used to represent optional values and can be seen as
something like a type-safe `null`, where `Nothing` is `null` and `Just x`
is the non-null value `x`.

    data Maybe a
      = Nothing 
      | Just a

#### `maybe`

Takes a default value, a function, and a `Maybe` value. If the `Maybe`
value is `Nothing` the default value is returned, otherwise the function
is applied to the value inside the `Just` and the result is returned.

``` purescript
maybe default f Nothing == default
maybe default f (Just x) == f x
```

    maybe :: forall a b. b -> (a -> b) -> Maybe a -> b

#### `fromMaybe`

Takes a default value, and a `Maybe` value. If the `Maybe` value is
`Nothing` the default value is returned, otherwise the value inside the
`Just` is returned.

``` purescript
fromMaybe default Nothing == default
fromMaybe default (Just x) == x
```

    fromMaybe :: forall a. a -> Maybe a -> a

#### `isJust`

Returns `true` when the `Maybe` value was constructed with `Just`.

    isJust :: forall a. Maybe a -> Boolean

#### `isNothing`

Returns `true` when the `Maybe` value is `Nothing`.

    isNothing :: forall a. Maybe a -> Boolean

#### `functorMaybe`

The `Functor` instance allows functions to transform the contents of a
`Just` with the `<$>` operator:

``` purescript
f <$> Just x == Just (f x)
```

`Nothing` values are left untouched:

``` purescript
f <$> Nothing == Nothing
```

    instance functorMaybe :: Functor Maybe

#### `applyMaybe`

The `Apply` instance allows functions contained within a `Just` to
transform a value contained within a `Just` using the `(<*>)` operator:

``` purescript
Just f <*> Just x == Just (f x)
```

`Nothing` values are left untouched:

``` purescript
Just f <$> Nothing == Nothing
Nothing <$> Just x == Nothing
```

Combining `Functor`'s' `<$>` with `Apply`'s `<*>` can be used transform a
pure function to take `Maybe`-typed arguments so `f :: a -> b -> c`
becomes `f :: Maybe a -> Maybe b -> Maybe c`:

``` purescript
f <$> Just x <*> Just y == Just (f x y)
```

The `Nothing`-preserving behaviour of both operators means the result of
an expression like the above but where any one of the values is `Nothing`
means the whole result becomes `Nothing` also:

``` purescript
f <$> Nothing <*> Just y == Nothing
f <$> Just x <*> Nothing == Nothing
f <$> Nothing <*> Nothing == Nothing
```

    instance applyMaybe :: Apply Maybe

#### `applicativeMaybe`

The `Applicative` instance enables lifting of values into `Maybe` with the
`pure` or `return` function (`return` is an alias for `pure`):

``` purescript
pure x :: Maybe _ == Just x
return x :: Maybe _ == Just x
```

Combining `Functor`'s' `<$>` with `Apply`'s `<*>` and `Applicative`'s
`pure` can be used to pass a mixture of `Maybe` and non-`Maybe` typed
values to a function that does not usually expect them, by using `pure`
for any value that is not already `Maybe` typed:

``` purescript
f <$> Just x <*> pure y == Just (f x y)
```

Even though `pure = Just` it is recommended to use `pure` in situations
like this as it allows the choice of `Applicative` to be changed later
without having to go through and replace `Just` with a new constructor.

    instance applicativeMaybe :: Applicative Maybe

#### `altMaybe`

The `Alt` instance allows for a choice to be made between two `Maybe`
values with the `<|>` operator, where the first `Just` encountered
is taken.

``` purescript
Just x <|> Just y == Just x
Nothing <|> Just y == Just y
Nothing <|> Nothing == Nothing
```

    instance altMaybe :: Alt Maybe

#### `plusMaybe`

The `Plus` instance provides a default `Maybe` value:

``` purescript
empty :: Maybe _ == Nothing
```

    instance plusMaybe :: Plus Maybe

#### `alternativeMaybe`

The `Alternative` instance guarantees that there are both `Applicative` and
`Plus` instances for `Maybe`.

    instance alternativeMaybe :: Alternative Maybe

#### `bindMaybe`

The `Bind` instance allows sequencing of `Maybe` values and functions that
return a `Maybe` by using the `>>=` operator:

``` purescript
Just x >>= f = f x
Nothing >>= f = Nothing
```

    instance bindMaybe :: Bind Maybe

#### `monadMaybe`

The `Monad` instance guarantees that there are both `Applicative` and
`Bind` instances for `Maybe`. This also enables the `do` syntactic sugar:

``` purescript
do
  x' <- x
  y' <- y
  pure (f x' y')
```

Which is equivalent to:

``` purescript
x >>= (\x' -> y >>= (\y' -> pure (f x' y')))
```

    instance monadMaybe :: Monad Maybe

#### `monadPlusMaybe`

The `MonadPlus` instance guarantees that there are both `Monad` and
`Alternative` instances for `Maybe`.

    instance monadPlusMaybe :: MonadPlus Maybe

#### `extendMaybe`

The `Extend` instance allows sequencing of `Maybe` values and functions
that accept a `Maybe a` and return a non-`Maybe` result using the
`<<=` operator.

``` purescript
f <<= Nothing = Nothing
f <<= Just x = Just (f x)
```

    instance extendMaybe :: Extend Maybe

#### `semigroupMaybe`

The `Semigroup` instance enables use of the operator `<>` on `Maybe` values
whenever there is a `Semigroup` instance for the type the `Maybe` contains.
The exact behaviour of `<>` depends on the "inner" `Semigroup` instance,
but generally captures the notion of appending or combining things.

``` purescript
Just x <> Just y = Just (x <> y)
Just x <> Nothing = Just x
Nothing <> Just y = Just y
Nothing <> Nothing = Nothing
```

    instance semigroupMaybe :: (Semigroup a) => Semigroup (Maybe a)

#### `showMaybe`

The `Show` instance allows `Maybe` values to be rendered as a string with
`show` whenever there is an `Show` instance for the type the `Maybe`
contains.

    instance showMaybe :: (Show a) => Show (Maybe a)

#### `eqMaybe`

The `Eq` instance allows `Maybe` values to be checked for equality with
`==` and inequality with `/=` whenever there is an `Eq` instance for the
type the `Maybe` contains.

    instance eqMaybe :: (Eq a) => Eq (Maybe a)

#### `ordMaybe`

The `Ord` instance allows `Maybe` values to be compared with
`compare`, `>`, `>=`, `<` and `<=` whenever there is an `Ord` instance for
the type the `Maybe` contains.

`Nothing` is considered to be less than any `Just` value.

    instance ordMaybe :: (Ord a) => Ord (Maybe a)


## Module Data.Maybe.Unsafe

#### `fromJust`

A partial function that extracts the value from the `Just` data
constructor. Passing `Nothing` to `fromJust` will throw an error at
runtime.

    fromJust :: forall a. Maybe a -> a