# Chomsky 语法类型

|语法类型（Grammar Type）|接受语法（Grammar Accepted）|接受语言（Language Accepted）|自动机（Automaton）|
|-|-|-|-|
|Type 0|无限制语法（Unrestricted grammer）|递归可枚举语言（Recursively）|图灵机（Turing Machine）|
|Type 1|上下文有关语法（Context-sensitive grammar)|上下文无关语言（Context-sensitive language）|
Linear-bounded automaton）|
|Type 2|上下文无关语法（Context-free grammar）|上下文无关语言（Context-free language）|下推式自动机（Pushdown automaton）|
|Type 3|正则语法（Regular grammar）|正则语言（Regular language）|有限状态自动机（Finite state automaton）|

## Type - 3 语法

Type-3 语法生成正则语言。Type-3 语法的左侧必须有一个非终结符，而右侧必须有一个终结符，或跟随一个非终结符的终结符。

### 产生规则

```
X → a or X → aY
X, Y ∈ N (Non terminal)
a ∈ T (Terminal)\
```

如果 S 没有出现在任何规则的右侧，S → ε 规则是被允许的。

### 例子

```
X → ε 
X → a | aY
Y → b 
```

## Type - 2 语法

Type-2 语法生成上下文无关语言。

### 产生规则

```
A → γ
A ∈ N (Non terminal)
γ ∈ (T ∪ N)* (String of terminals and non-terminals)
```

这些语法生成的语言可以由非确定性下推自动机识别。

### 例子

```
S → X a 
X → a 
X → aX 
X → abc 
X → ε
```

## Type - 1 语法

Type-1 语法生成上下文有关的语言。

### 产生规则

```
α A β → α γ β
A ∈ N (Non-terminal)
α, β, γ ∈ (T ∪ N)*
```

α 和 β 可以为空，但是 γ 不可为空。

### 例子

```
AB → AbBc 
A → bcA 
B → b 
```

## Type - 0 语法

Type-0 语法生成递归可枚举的语言。产生规则没有限制。

### 例子

```
S → ACaB 
Bc → acB 
CB → DB 
aD → Db 
```

# JavaScript 不是上下文无关语言
