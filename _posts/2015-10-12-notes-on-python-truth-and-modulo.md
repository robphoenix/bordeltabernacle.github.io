---
layout: post
title: "Notes On Python: Truth and Modulo"
date: "2015-10-12 11:14"
tags: python

---

Today I learnt how to be less verbose when using the `%` operator in Python.

**SPOILER:** I learnt this while trying to apply a functional approach to the first problem on [Project Euler][pe] and I describe my answer here.

<!--more-->

So, to set the scene, the first problem on [Project Euler][pe] is presented as such:

*"If we list all the natural numbers below 10 that are multiples of 3 or 5, we get 3, 5, 6 and 9. The sum of these multiples is 23.*

*Find the sum of all the multiples of 3 or 5 below 1000."*

I initially answered this with the following function:

{% highlight python linenos %}
def sum_mul_three_five(end_num):
    """
    Returns the sum of all the multiples of 3 or 5 below end_num
    """
    total = []
    for num in range(end_num):
        if num % 3 == 0 or num % 5 == 0:
            total.append(num)
    return sum(total)
{% endhighlight %}

This code is pretty self-explanatory, but the line I want to focus on is the `if` statement.  The `%` operator returns the remainder of a division, and is popularly used in Python for such things as sorting even numbers.
For instance:

{% highlight python linenos %}
>>> numbers = range(10)
>>> numbers
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> for number in numbers:
...     if number % 2 == 0:
...         print number
...
0
2
4
6
8
{% endhighlight %}

Here we iterate through a simple list of numbers.  The `if` statement uses the `%` operator to check what the remainder will be when each number is divided by `2`.  If there is no remainder, ie. the result is `0` then the number is equally divided by `2` and therefore `even`...

{% highlight python linenos %}
>>> for number in range(10):
...     if number % 2 == 0:
...         print "%d is even" % number
...     else:
...         print "%d is odd" % number
...
0 is even
1 is odd
2 is even
3 is odd
4 is even
5 is odd
6 is even
7 is odd
8 is even
9 is odd
{% endhighlight %}

So, in my solution for [Project Euler][pe] I am using `%` to test whether each number is divisible by 3 or 5.  When I tried to refactor this with a more Functional approach using a list comprehension, I got this long mess:

{% highlight python linenos %}
def sum_mul_three_five(end_num):
    """
    Returns the sum of all the multiples of 3 or 5 below end_num
    """
    total = []
    [total.append(num) for num in range(end_num) if num % 3 == 0 or num % 5 == 0]
    return sum(total)
{% endhighlight %}

*Eeek!* As I have [been told][ttp], while this works, it's probably the [wrong way][sww].  What I didn't realise is that we can be more concise with the modulo statements, and use them as a `True/False` test.  This is because `0` and `1`, the two results we can get from using `%`, equate to `False` and `True` respectively.

***EDIT*** See Below...

This may seem simple but it's perhaps overlooked when learning Python, especially for those without a CS background, such as myself, or perhaps my view of the wood was blocked by the trees.

{% highlight python linenos %}
>>> 1 == True
True
>>> 0 == False
True
{% endhighlight %}

Therefore `if num % 3 == 0` is specifying the condition is met when the remainder of dividing a number by 3 is `0`, which also corresponds to `False`, or `not True`.

{% highlight python linenos %}
>>> 0 != True
True
>>> 1 != True
False
{% endhighlight %}

Now, the `if` condition of an `if/else` conditional expression is met when the condition is `True`, or `not False`.  This is where my mind started to fold over into itself.  So, to return the numbers divisible by `3` in an `if` statement the result needs to be `True`, whereas with `%` it is `False`...

{% highlight python linenos %}
>>> for number in range(10):
...     if number % 3:
...         print number
...
1
2
4
5
7
8
>>> for number in range(10):
...     if not number % 3:
...         print number
...
0
3
6
9
{% endhighlight %}

Basically we're replacing `== 0` with the concept of `True` and `False`, which ends up as this:

{% highlight python linenos %}
def sum_mul_three_five(end_num):
    """
    Returns the sum of all the multiples of 3 or 5 below end_num
    """
    total = []
    [total.append(num) for num in range(end_num) if not num % 3 or not num % 5]
    return sum(total)
{% endhighlight %}

Which is saying *return the number if it is `not True`, ie. `False`*, a condition met when the remainder is `0` when the number is divided by `3`.

Though this is still a bit ugly, so let's simplify the rest a bit...

{% highlight python linenos %}
def sum_mul_three_five(end_num):
    """
    Returns the sum of all the multiples of 3 or 5 below end_num
    """
    return sum([num for num in range(end_num) if not num % 3 or not num % 5])
{% endhighlight %}

[Ahh yes ain't that fresh. Everybody wants to get down like that.][lrd]

I think that makes sense, I hope it does.

***EDIT*** Ok, so I totally overlooked that the result of `%` isn't restricted to `0` or `1`, so this example is perhaps not as black and white as I first believed.

{% highlight python linenos %}
>>> for number in range(10):
...     print number % 2
...
0
1
0
1
0
1
0
1
0
1
>>> for number in range(10):
...     print number % 3
...
0
1
2
0
1
2
0
1
2
0
>>> for number in range(10):
...     print number % 5
...
0
1
2
3
4
0
1
2
3
4
{% endhighlight %}

So, what happens with remainders that aren't `0` or `1`?  Well, they are interpreted as `False` in this case

{% highlight python linenos %}
>>> for number in range(10):
...     if number % 3:
...         print "False: \tNumber: %d \tRemainder: %d" % (number, number % 3)                                                              
...     else:
...         print "True: \tNumber: %d \tRemainder: %d" % (number, number % 3)
...
True:   Number: 0       Remainder: 0
False:  Number: 1       Remainder: 1
False:  Number: 2       Remainder: 2
True:   Number: 3       Remainder: 0
False:  Number: 4       Remainder: 1
False:  Number: 5       Remainder: 2
True:   Number: 6       Remainder: 0
False:  Number: 7       Remainder: 1
False:  Number: 8       Remainder: 2
True:   Number: 9       Remainder: 0
{% endhighlight %}

I have to admit, this hasn't totally clicked in my brain yet. I mean, I get it, but the whole *is it True, is it False?* thing is a little hard to keep track of y'know.


[pe]: https://projecteuler.net/problem=1
[ttp]: https://twitter.com/supertylerc/status/650003121058353152
[sww]: https://www.youtube.com/watch?v=uLifSFBs_Lk
[lrd]: https://youtu.be/T69CCsfaZlA?t=19s
