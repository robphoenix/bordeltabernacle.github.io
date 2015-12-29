---
layout: post
title: "Notes on Elixir: Pattern Matching"
tags: elixir
---

One of the first things you'll learn about in Elixir is Pattern-Matching, a concept that re-thinks variable assignment and the meaning of the `=` symbol.

<!--more-->

In Elixir the `=` symbol is not used for *assignment*, where `a = 1` *assigns* the literal value `1` to the variable `a`.

In Elixir, the equals sign is called a *match operator*, and is used to match either side of the `=`. So, `a = 1` is still valid Elixir code, but what's actually going on is that the `a` and the `1` are being made to be the same, so that both sides of the `=` can match.  This is possible here as the `a` is an empty variable name, which, now that it's been matched in the `a = 1` statement, is bound to the literal value `1`.
Here the match operator gives the impression of working the same way as an assignment operator. But as Erlang creator Joe Armstrong has pointed out, it works more akin to the `=` symbol in algebra. For example:

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

Where the match operator really starts to shine however is in *pattern-matching*, which takes the concept and applies it to patterns.
