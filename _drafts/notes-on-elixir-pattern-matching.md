---
layout: post
title: "Notes on Elixir: Pattern Matching"
tags: elixir
---

One of the first things you'll learn about in Elixir is Pattern-Matching, a concept that re-thinks variable assignment and the meaning of the `=` symbol.

<!--more-->

In Elixir the `=` symbol is not used for *assignment*, where the expression `a = 1` *assigns* the literal value `1` to the variable `a`.

Instead, the equals sign is called a *match operator*, and is used to make the left hand side *match* the right hand side. So, `a = 1` is still valid Elixir code, but what's actually going on is that the `a` and the `1` are being made to match.  As the `a` is an empty variable name, for it to match it is bound to the literal value `1`.

```
iex(1)> a = 1
1
iex(2)> a
1
iex(3)> 1 = a
1
iex(4)> a = 2
2
iex(5)> 1 = a
** (MatchError) no match of right hand side value: 2

iex(5)> ^a = 3
** (MatchError) no match of right hand side value: 3

iex(5)> 1 = b
** (CompileError) iex:5: undefined function b/0
```

Here, `a` has initially been bound to `1` through pattern-matching, so when we write `1 = a`, this expression matches.  We're not assigning here, we're *matching*, and here `a` is `1` so the two values match.  We then re-bind `a` to `2`, yep, we can do that, so now `1 = a` doesn't match and we get a `MatchError`.  If we want to try and match a variable without risking it being re-bound to a new value we can use the `^` symbol(the *pin operator*), in front of the variable.  When pattern-matching the *pattern* is on the left side of the `=`, which is why `1 = b` doesn't work, `b` hasn't been initialized, it holds no value, and as it's on the right side, it is not bound to the `1` on the left.

The match operator can give the impression of working the same way as an assignment operator. But as Erlang creator Joe Armstrong has pointed out, it works more akin to the `=` symbol in algebra. For example:

```
x + 5 = 10
    x = 10 - 5
    x = 5
```

On every line of this simple algebraic formula each side of the `=` matches, leading us to the conclusion that `x` is the same as `5`.  `x` could then go on to be further used, as the literal value `5`, in the wider context of the formula:

```
x + y = 20
    y = 20 - x
    y = 20 - 5
    y = 15
```

- destructuring/(extract & bind values from a data structure)complex patterns, inc {:ok, result}
- underscore
- [h|t]
- function clauses
