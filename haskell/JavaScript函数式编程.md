# JavaScript 函数式编程

## 组合与管道

### 组合的优势

#### 组合满足结合律

```javascript
compose(f, compose(g, h)) = compose(compose(f, g), h)
```

#### 使用 tap 函数调试



## 函子（Functor）

### 什么是函子？

函子是一个普通对象（在其他语言中，可能是一个类），它实现了 map 函数，在遍历每个对象值的时候生成一个新对象。

#### 函子是容器

##### 函子是一个持有值的容器。

```javascript
const Container = function(val) {
    this.value = val
}
```

> 为什么不使用箭头语法编写 Container 函数？
> 箭头函数不具有内部方法 [[Construct]] 和 prototype 属性。

##### 创建 of 静态方法，可以在创建新的 Container 时省略 new 关键字

```javascript
Container.of = function(value) {
    return new Container(value)
}
```

#### 函子实现 map 方法

map 函数从 Container 中取出值，将传入的函数应用于其上，并将结果放回 Container。

```javascript
Container.prototype.map = function(fn) {
    return Container.of(fn(this.value))
}
```

### MayBe 函子

MayBe 函数定义：

```javascript
const MayBe = function(val) {
    this.value = this.val
}

MayBe.of = function(val) {
    return new MayBe(val)
}

MayBe.prototype.isNothing = function() {
    return this.val == null
}

MayBe.prototype.map = function(fn) {
    return this.isNothing() ? MayBe.of(null) : MayBe.of(fn(this.value))
}
```

### Either 函子

Either 函子解决分支扩展问题。

#### 实现函子

```javascript
const Nothing = function(val) {
    this.value = val
}

Nothing.of = function(val) {
    return new Nothing(val)
}

Nothing.prototype.map = function(f) {
    return this;
}

const Some = function(val) {
    this.value = val
}

Some.of = function(val) {
    return new Some(val)
}

Some.prototype.map = function(val) {
    return Some.of(fn(this.val))
}

const Either = {
    Some,
    Nothing
}
```

Nothing 保存了错误信息并阻断了函数的映射，Some 返回预期的结果。

### Pointed 函子

Pointed 函子是一个函子的子集，它具有实现了 of 契约的接口。

ES6 增加了 Array.of，这使数组成为了一个 Pointed 函子。

## 深入理解 Monad

Monad 就是一个含有 chain 方法的函子。
