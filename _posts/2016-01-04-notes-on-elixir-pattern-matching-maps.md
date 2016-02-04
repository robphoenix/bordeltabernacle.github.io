---
layout: post
title: "Notes on Elixir: Pattern-Matching Maps"
tags: elixir

---

Following on from my [last post][nopm] about pattern-matching, `maps`, the main
key-value store in Elixir, have an interesting capability that sets them apart
from other data structures with regards to pattern-matching.

<!--more-->

A `map` can actually pattern-match on just a subset of a value. The key(s) in
the pattern have to exist in the match, but the two structures don't have to
mirror each other in the same way a `list` or a `tuple` has to.  For example:

{% highlight elixir linenos %}
iex(1)> [a, b] = [1, 2, 3]
** (MatchError) no match of right hand side value: [1, 2, 3]

iex(1)> {a, b} = {1, 2, 3}
** (MatchError) no match of right hand side value: {1, 2, 3}

iex(1)> %{:a => one} = %{:a => 1, :b => 2, :c =>3}
%{a: 1, b: 2, c: 3}
iex(2)> one
1
iex(3)> %{:a => one, :c => three} = %{:a => 1, :b => 2, :c =>3}
%{a: 1, b: 2, c: 3}
iex(4)> three
3
iex(5)> one
1
iex(6)> %{} = %{:a => 1, :b => 2, :c =>3}
%{a: 1, b: 2, c: 3}
iex(7)> %{:d => four} = %{:a => 1, :b => 2, :c =>3}
** (MatchError) no match of right hand side value: %{a: 1, b: 2, c: 3}

iex(8)> %{:a => one, :d => four} = %{:a => 1, :b => 2, :c =>3}
** (MatchError) no match of right hand side value: %{a: 1, b: 2, c: 3}

{% endhighlight %}

We can see that neither the `list` nor the `tuple` matches if the data
structure of the pattern is different to the data structure of the match, namely
the size.  Whereas this is not the case for the `map`.  Because the key is
present in both the pattern and the match then the match is successful,
regardless of the different sizes of the pattern and the match.  An empty
`map` will also match.

However the match will not be successful if the key in the pattern is not in the
match. This is also the case even if there are matching keys. So any key used in
the pattern has to be present in the match.

You'll see this used extensively in the [Phoenix Framework][pf] when dealing
with the parameters passed to a function.  The function is able to pick out
of the parameters only those pieces of data it needs.

Here we have a function that creates a new user:

{% highlight elixir linenos %}
def create(conn, %{"user" => user_params}) do
  # do stuff with user_params
end
{% endhighlight %}

The map that is actually passed in as the second argument to this function is
this:

{% highlight elixir linenos %}
Parameters: %{"_csrf_token" => "UR99GwILHTtzbSBUYRwmBVpdeDY/AAAA3K70jEiO9UhgPVwh+d3WYw==",
              "_utf8" => "✓",
              "user" => %{"name" => "Memphis Minnie",
                          "password" => "[FILTERED]",
                          "username" => "minnie"}}
{% endhighlight %}

The `create` function pattern-matches against only the `"user"` key, binding the associated
value, in this case another map, to the `user_params` variable for use within
the function. This function basically picks out the user info from the
given parameters map, discarding the rest of the data as irrelevant to its
needs.

It is also possible to match against the whole map at the same time as pattern-matching
part of the map.  Let's change the `create` function to look like this:

{% highlight elixir linenos %}
def create(conn, parameters = %{"user" => user_params}) do
  IO.inspect user_params["name"]
  IO.inspect parameters["_utf8"]
  # do stuff with user_params
end
{% endhighlight %}

And we can see below when we use this function that we've matched `parameters`
to the Parameters map that is passed in, and `user_params` to the the value of
the `"user"` key in the Parameters map:

{% highlight elixir linenos %}
[info] POST /users
"Skip James"
"✓"
{% endhighlight %}

Interestingly, the `parameters` pattern-match can also be written the other way around:

{% highlight elixir linenos %}
def create(conn, %{"user" => user_params} = parameters) do
  IO.inspect user_params["name"]
  IO.inspect parameters["_utf8"]
  # do stuff with user_params
end
{% endhighlight %}

This is because the pattern here is actually `%{"user" => user_params} =
parameters` and the match is the Parameters map being passed in.  And when
you're *inside a pattern* you can also match different parts of the pattern,
binding them to different variables.  As far as I can tell, this is the more
idiomatic approach, and what you will see most often.

Pattern-matching provides a really nice way to reach in and grab data out of a
`map` key-value store.


## Further Reading

- [Elixir: pattern matching works differently for tuples and maps][so]
- [Phoenix Framework Getting Started Guide][pfgsg]

[nopm]: https://bordeltabernacle.github.io/2015/12/31/notes-on-elixir-pattern-matching.html
[pf]: http://www.phoenixframework.org/
[so]: https://stackoverflow.com/questions/23693173/elixir-pattern-matching-works-differently-for-tuples-and-maps/23695899#23695899
[pfgsg]: http://www.phoenixframework.org/docs/adding-pages#section-a-new-action
