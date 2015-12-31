---
layout: post
title: "Notes on Elixir: Pattern Matching"
tags: elixir
---

One of the first things you'll learn about in Elixir is Pattern-Matching, a
concept that re-thinks variable assignment and the meaning of the `=` symbol.

<!--more-->

In Elixir the `=` symbol is not used for *assignment*, where the expression `a =
1` *assigns* the literal value `1` to the variable `a`.

Instead, the equals sign is called a *match operator*, and is used to make the
left hand side *match* the right hand side. As Rob Conery has highlighted, it's an
*equals* sign, a symbol signifying *equality* between the values on either side.
So, `a = 1` is still valid Elixir code, but what's actually going on is that the
`a` and the `1` are being checked to see if they can match.  And as the `a` is
an empty variable name, this expression can be a valid match by binding `a` to
the literal value `1`.

```elixir
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

Here, `a` has initially been bound to `1` through pattern-matching, so when we
write `1 = a`, this expression matches.  We're not assigning here, we're
*matching*, and here `a` is `1` so the two values match.  We then re-bind `a` to
`2`, yep, we can do that, so now `1 = a` doesn't match, because `a` is now `2`,
and we get a `MatchError`.  If we want to try and match a variable without
risking it being re-bound to a new value we can use the `^` symbol(the *pin
operator*), in front of the variable. In the last expression, `b` hasn't been
initialized yet, so `1 = b` doesn't work, `b` holds no value, it can't be
matched, nor is it bound to the literal `1` on the left.  This happens because the
pattern is on the left of the `=` and the match is on the right.

```
pattern = match
```

And in this case, as `b` holds no value, it can't be matched against.  But it
can be used as a pattern to find a match for.

> The match operator can give the impression of working the same way as an
> assignment operator. But as Erlang creator Joe Armstrong has pointed out, it
> works more akin to the `=` symbol in algebra. For example:

> ```
> x + 5 = 10
>     x = 10 - 5
>     x = 5
> ```

> On every line of this simple algebraic formula each side of the `=` matches,
> leading us to the conclusion that `x` is the same as `5`.  `x` could then go on
> to be further used, as the literal value `5`, in the wider context of the formula:

> ```
> x + y = 20
>     y = 20 - x
>     y = 20 - 5
>     y = 15
> ```

Pattern-matching really starts to come into it's own when used for
destructuring, a way to extract and bind values from a data structure.

Let's start with a list;

```elixir
iex(1)> a = [1, 2, 3]
[1, 2, 3]
iex(2)> a
[1, 2, 3]
iex(3)> [x, y, z] = a
[1, 2, 3]
iex(4)> x
1
iex(5)> y
2
iex(6)> z
3
iex(7)> [a, b, c] = [1, 2, 3]
[1, 2, 3]
iex(8)> a
1
iex(9)> b
2
iex(10)> c
3
iex(11)> x
1
iex(12)> y
2
iex(13)> z
3
```

This looks a lot like the kind of multiple variable assignment you see in
Python.  Let's run through it.  `a` is easily matched to a list.  The pattern
`[x, y, z]` is then able to match `a` as both represent the same data structure,
a list of 3 values.  In the process of the match, `x`, `y` & `z` are bound to
the respective literal values.  This process is more obvious in the next
pattern-match.  Also notice that, due to the immutable nature of data in Elixir,
the values of `x`, `y` & `z` have not changed even though `a` has been re-bound.

A match won't happen if the pattern and match are different sizes, or different
types, such as a list and a tuple.  But the values can be of different types:

```elixir
iex(18)> [a, b, c] = [1, 2]
** (MatchError) no match of right hand side value: [1, 2]

iex(18)> [a, b] = [1, 2, 3]
** (MatchError) no match of right hand side value: [1, 2, 3]

iex(18)> [a, b, c] = [:hello, "World", 42]
[:hello, "World", 42]
iex(19)> a
:hello
iex(20)> b
"World"
iex(21)> c
42
iex(22)> [a, b, c] = {1, 2, 3}
** (MatchError) no match of right hand side value: {1, 2, 3}

iex(22)> {a, b, c} = [1, 2, 3]
** (MatchError) no match of right hand side value: [1, 2, 3]
```

Pattern-matching is widely used in Elixir, as a means of matching on one or more
specific values, and extracting other values from the matched data structure.

```elixir
iex(24)> {:success, data} = {:success, "Today is a good day"}
{:success, "Today is a good day"}
iex(25)> data
"Today is a good day"
iex(26)> {:success, data} = {:failure, "Oh, dear"}
** (MatchError) no match of right hand side value: {:failure, "Oh, dear"}
```

Sometimes we don't want everything, some values just don't matter.  If we create
a variable but don't use it, Elixir will complain.  It will still work, but
it'll just highlight your sloppiness to you. It's okay though, we can deal with
this, using the underscore symbol.

```
iex(27)> {:success, data, _} = {:success, "Today is a good day", "Whatever"}
{:success, "Today is a good day", "Whatever"}
iex(28)> data
"Today is a good day"
iex(29)> _
** (CompileError) iex:29: unbound variable _

iex(29)> {:success, data, _blah} = {:success, "Today is a good day", "Whatever"}
{:success, "Today is a good day", "Whatever"}
iex(30)> data
"Today is a good day"
iex(31)> _blah
"Whatever"
iex:31: warning: the underscored variable "_blah" is used after being set. A leading underscore indicates that the value of the variable should be ignored. If this is intended please rename the variable to remove the underscore
```

The underscore by itself lets the Elixir compiler know to just ignore that
variable, that it doesn't have to do anything.  You can also put an underscore
in front of a variable name, for readability, if you want it to be apparent what
it is you are ignoring.  This variable will still be treated like any other but
affects compiler warnings.  You won't be warned for not using it, but will be
warned if you do try and use it, as you can see above.

Another much used pattern-matching technique, especially in recursion, is
matching the head and tail of a list, where the head is the first element, and
the tail is the rest of the list.

```
iex(32)> [head|tail] = [1, 2, 3, 4, 5]
[1, 2, 3, 4, 5]
iex(33)> head
1
iex(34)> tail
[2, 3, 4, 5]
iex(35)> [new_head|new_tail] = tail
[2, 3, 4, 5]
iex(36)> new_head
2
iex(37)> new_tail
[3, 4, 5]
iex(38)> [h|t] = new_tail
[3, 4, 5]
iex(39)> h
3
iex(40)> t
[4, 5]
iex(41)> [first|rest] = [1, 2, 3, 4, 5]
[1, 2, 3, 4, 5]
iex(42)> first
1
iex(43)> rest
[2, 3, 4, 5]
iex(44)> [head|tail] = [1]
[1]
iex(45)> head
1
iex(46)> tail
[]
iex(47)> [head|tail] = []
** (MatchError) no match of right hand side value: []
```

As you can see `head` and `tail` are naming conventions rather than required for
this to work. You can match against a single element list, the `tail` is just an
empty list.  You can't, however, match against an empty list, it has no first
element, nor any other elements. This is one of the methods that replaces the
`for loop` in Elixir, along with comprehensions and `map`, `filter` & `reduce`.
It's incredibly useful.

Here's a basic example:

```
iex(52)> defmodule Increment do
...(52)>
...(52)>   def by_one([head|tail]) do
...(52)>     [head + 1|by_one(tail)]
...(52)>   end
...(52)>
...(52)>   def by_one([]) do
...(52)>     []
...(52)>   end
...(52)>
...(52)> end
{:module, Increment,
 <<70, 79, 82, 49, 0, 0, 4, 216, 66, 69, 65, 77, 69, 120, 68, 99, 0, 0, 0, 153, 131, 104, 2, 100, 0, 14, 101, 108, 105, 120, 105, 114, 95, 100, 111, 99, 115, 95, 118, 49, 108, 0, 0, 0, 4, 104, 2, ...>>,
 {:by_one, 1}}
iex(53)> Increment.by_one([1, 2, 3, 4, 5])
[2, 3, 4, 5, 6]
```

Don't worry too much about the recursion if it doesn't make sense, just notice
that we are essentially creating a new list by taking the `head` of the initial list
by pattern-matching, adding `1` to it, and then taking the pattern-matched
`tail` of the list and passing it back into the function as a separate list. in
this way we essentially loop through the list.

This example also highlights the other significant use of pattern-matching. In
our `Increment` module we have two functions with the same name, but that take
different arguments.  One takes a list that has something in it, the other takes
an empty list.  This works because of pattern-matching.  Which function is used
depends on what the given argument is pattern matched against.

```
iex(57)> defmodule Math do
...(57)>
...(57)>   def two_become_one(:add, x, y),      do: x + y
...(57)>   def two_become_one(:subtract, x, y), do: x - y
...(57)>   def two_become_one(:divide, x, y),   do: x / y
...(57)>   def two_become_one(:multiply, x, y), do: x * y
...(57)>
...(57)> end
{:module, Math,
 <<70, 79, 82, 49, 0, 0, 5, 156, 66, 69, 65, 77, 69, 120, 68, 99, 0, 0, 0, 187, 131, 104, 2, 100, 0, 14, 101, 108, 105, 120, 105, 114, 95, 100, 111, 99, 115, 95, 118, 49, 108, 0, 0, 0, 4, 104, 2, ...>>,
 {:two_become_one, 3}}
iex(58)> Math.two_become_one(:add, 2, 3)
5
iex(59)> Math.two_become_one(:subtract, 2, 3)
-1
iex(60)> Math.two_become_one(:divide, 2, 3)
0.6666666666666666
iex(61)> Math.two_become_one(:multiply, 2, 3)
6
```

Here we're defining a module with four functions.  Each with the same name, yet
each one does something different depending on what the first argument matches
against.  Here pattern-matching is working as a control-flow mechanism.

Pattern-matching is one of the core techniques of Elixir, and is used
extensively.  Once it makes sense, it's a wonderful thing.

### Further Reading

- [Elixir docs](http://elixir-lang.org/getting-started/pattern-matching.html)
-
[Pattern Matching In Elixir - Quick Left](https://quickleft.com/blog/pattern-matching-elixir/)
-
[Assignments and Pattern-Matching in Elixir](https://asymmetric.github.io/2015/11/25/assignments-and-pattern-matching-in-elixir/)
-
[On Elixir and Static Typing](http://blog.johanwarlander.com/2015/07/19/on-elixir-and-static-typing.html)
-
[Introducing Elixir](http://shop.oreilly.com/product/0636920030584.do)
- [Programming Elixir](https://pragprog.com/book/elixir/programming-elixir)
- [LearnElixir.tv](https://www.learnelixir.tv/)
- [red:4](http://www.redfour.io/)
