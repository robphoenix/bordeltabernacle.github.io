---
layout: post
title: "Notes on Elixir: Bodyless Function Clauses"
tags: elixir

---

```elixir
def func(argument)
# NOTHING TO SEE HERE
```

A function head without a body clause? What's the point in that, huh?

The bodyless function clause is something I've only come across recently, one of
those things that I think I probably read about at some point but didn't
register until I [submitted a pull request][pr]. I've not been able to find out
much about them, but here's a quick rundown of when you might see a bodyless
function clause.

<!--more-->

As far as I know, the bodyless function clause is only something used within
Elixir & Erlang, and possibly Prolog. The [Erlang docs][ed] give an interesting
explanation of what happens when a function is called.  This explanation hints
at the separation of the head and body of functions and I believe applies to
Elixir as well.

This explanation breaks functions up into the head and body, where the head *"consists
of the function name, an argument list, and an optional guard sequence beginning
with the keyword when"*, and the body *"consists of a sequence of expressions"*.

When a function is called, the function code is located, and the function clauses
are checked in turn to find one where *"the patterns in the clause head can be
successfully matched against the given arguments"*, and *"the guard sequence, if
any, is true"*.  It's only if a function head corresponds to this
that the function body is then evaluated.  This gives the impression of the head
and body being separate entities, the head being a kind of control mechanism
that doesn't necessarily require a body.

So, what is this useful for?  The two reasons I know of for using a bodyless
function clause in Elixir are [documentation][doc] and [default arguments][defargs].

The Elixir compiler infers function argument names from the code. If the
argument name is not explicit, for instance in the case of a pattern match on a
data structure, this may sometimes result in something you don't want. If so, you
can use a bodyless function to define the name you want the argument to have,
the name that would be used in the function's documentation, using [ExDocs][exdoc] for example.

The compiler will infer the argument name for [this function][hd] as `hash_dict`;

```elixir
def size(%HashDict{size: size}) do
  size
end
```

If we would rather it was `dict` we could add in an extra function head:

```elixir
def size(dict)

def size(%HashDict{size: size}) do
  size
end
```

If you are including default arguments for a function with multiple clauses you
have to declare them in a separate function head:

```elixir
defmodule Lorem do
  def ipsum(size, type \\ :paragraph, style \\ :lorem_ipsum)

  def ipsum(size, :paragraph, style), do: ...
  def ipsum(size, :sentence, style), do: ...
  def ipsum(size, :word, style), do: ...
end
```

In this very basic example there are 3 function clauses that have a body, and
 one bodyless function that defines the defult arguments for these function
 clauses.

So, there you go, the function head, a mechanism in it's own right, separate from
the main function body.  If you know of any other uses of the bodyless function
please do let me know.


[pr]: https://github.com/elixir-lang/elixir/pull/4109/files#r48349021
[ed]: http://www.erlang.org/doc/reference_manual/functions.html
[doc]: https://github.com/elixir-lang/elixir/blob/master/lib/elixir/pages/Writing%20Documentation.md#function-arguments
[defargs]: http://elixir-lang.org/getting-started/modules.html#default-arguments
[hd]: http://elixir-lang.org/docs/stable/elixir/HashDict.html#size/1
[exdoc]: https://github.com/elixir-lang/ex_doc
