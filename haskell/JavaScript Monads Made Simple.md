```javascript
const x = 20; // Some data of type `a`
const f = n => n * 2 // A function from `a` to `b`
// JS has type lift sugar for arrays: [x]

// .map() applies the function f to the value x
// in the context of the array.
const result = arr.map(f); // [40]
```

从 a => F(b) , b => F(c) 等等组成函数，则需要 monad。

```
g: a => M (b)
f: b => M (c)
h = composeM(f, g): a => M (c)
```

之所以需要 Monad，是因为许多功能不是来自简单映射 a => b。一些功能需要处理副作用，处理分支，处理异常等。

# Monad 的本质

* 函数映射：a => b
* 函数与上下文映射：Functor(a) => Functor(b)
* Monad 展平并与上下文映射：Monad(Monad(a)) => Monad(b)

# Monad 由什么构成

Monad 基于简单的对称性——一种将值包装到上下文中，以及将值从上下文中解包的方法。

* Lift/Unit: A type lift from some type into the monad context: a => M(a)
* Flatten/Join: Unwrapping the type from the context: M(a) => a

And since monads are alose functors, they can also map:

* Map: Map with context preserved: M(a) -> M(b)

Combine flatten with map, and you get chain -- function composition for monad-lifting functions, aka Kleisli composition, named after Heinrich Kleisli:

* FlatMap/Chain: Flatten + map: M(M(a)) => M(b)

# 构建 monadic（又称 Kleisli）组成

```javascript
const composeM = method => (...ms) => (
    ms.reduce((f, g) => x => g(x)[method](f))
);
```

# The monad laws

1. Left identity: unit(x).chain(f) ==== f(x)
2. Right identity: m.chain(unit) ==== m
3. Associativity: m.chain(f).chain(g) ==== m.chain(x => f(x).chain(g))

## The Identity Laws

## Associativity

h(x).chain(x => g(x).chain(f)) === (h(x).chain(g)).chain(f)

## Proving the Monad Laws
