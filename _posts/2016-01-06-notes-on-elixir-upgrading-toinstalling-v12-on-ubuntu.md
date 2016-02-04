---
layout: post
title: "Notes on Elixir: Upgrading to/Installing v1.2 on Ubuntu"
tags: elixir

---

**EDIT:** Erlang solutions has
[now updated](https://twitter.com/ErlangSolutions/status/685138290635333632)
their Elixir package so you should be able to install/upgrade to v1.2 with
`apt-get` now.  Thankyou [Erlang Solutions][es].


Elixir recently got bumped up to [version 1.2][v1.2].  Installing Elixir on
Ubuntu requires a package from [Erlang Solutions][es] which hasn't yet been
updated. Therefore upgrading to v1.2 doesn't yet work with `apt-get`.  Instead
you have to compile from source, which I just learnt how to do with some help
from the elixir-lang IRC channel.  Here's how.

<!--more-->

Open up your terminal, `git clone` the Elixir repo, checkout v1.2, test & make:

{% highlight bash linenos %}
git clone https://github.com/elixir-lang/elixir.git
cd elixir/
git checkout v1.2
make clean test
sudo make install
{% endhighlight %}

check the version:

{% highlight elixir linenos %}
elixir -v
Erlang/OTP 18 [erts-7.2] [source] [64-bit] [smp:2:2] [async-threads:10] [kernel-poll:false]

Elixir 1.2.0
{% endhighlight %}

And there you go.  This is on 14.04.1 btw.

As I mentioned this was from the kind folk that help people like me on IRC,
specifically Nicd- & Gazler, so, thankyou.

[v1.2]: http://elixir-lang.org/blog/2016/01/03/elixir-v1-2-0-released/
[es]: https://www.erlang-solutions.com/
