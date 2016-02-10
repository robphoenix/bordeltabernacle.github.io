---
layout: post
title: "Notes on Python: args 'n' kwargs"
date: "2015-08-26"
tags: python
---

Python's `*args` & `**kwargs` have always intimidated me for some reason.  Since I've been working with Django they've turned up more frequently, for instance, when overriding a model's `save` function:

{% highlight python linenos %}
class myModel(models.Model):
    name = models.CharField()

    def save(self, *args, **kwargs):
        if not self.id:
            # Newly created object so set slug
            self.slug = slugify(self.name)

        super(myModel, self).save(*args, **kwargs)
{% endhighlight %}

And so I thought I'd get to the bottom of what they are.

<!--more-->

Basically, they allow a function to accept an indeterminate, variable number of arguments or keyword arguments.  The `args` and `kwargs` bits are actually not important, it's the `*` & `**` that are, similar I guess to their use elsewhere as a wildcard symbol.  So you can just use the asterisks on their own or even `*thing` & `**things`, but don't do that, you'll upset people.

For instance:

{% highlight python linenos %}
>>> def print_singers(*args):
...     # Notice when we do something with `args` we lose the asterisk
...     for arg in args:
...         print arg
...
>>> print_singers('R.L. Burnside')
R.L. Burnside
>>>
>>> print_singers('R.L. Burnside', 'Bessie Smith', 'Skip James', 'Memphis Minnie')
R.L. Burnside
Bessie Smith
Skip James
Memphis Minnie
{% endhighlight %}

We can also include a formal parameter alongside our `*args`.  A formal parameter just means it is mandatory, that an argument must be passed to it.

{% highlight python linenos %}
>>> def print_singers(genre, *args):
...     print 'Genre: ' + genre
...     for arg in args:
...         print arg
...
>>> print_singers('Blues', 'R.L. Burnside', 'Bessie Smith', 'Skip James', 'Memphis Minnie')
Genre: Blues
R.L. Burnside
Bessie Smith
Skip James
Memphis Minnie
{% endhighlight %}

This formal parameter is positional, so we have to make sure we pass our arguments to the function in order:

{% highlight python linenos %}
>>> print_singers('R.L. Burnside', 'Blues', 'Bessie Smith', 'Skip James', 'Memphis Minnie')
Genre: R.L. Burnside
Blues
Bessie Smith
Skip James
Memphis Minnie
{% endhighlight %}

This doesn't, however, work the other way around:

{% highlight python linenos %}
>>> def print_singers(*args, genre):
  File "<stdin>", line 1
    def print_singers(*args, genre):
                                 ^
SyntaxError: invalid syntax
{% endhighlight %}

`**kwargs` is much the same, but with named, `key/value`, arguments:

{% highlight python linenos %}
>>> def blues_singer(**kwargs):
...    # Notice we use `.items`, as `kwargs` returns a dictionary
...    for key, value in kwargs.items():
...        print key + ' = ' + value
...
>>> blues_singer(name='Memphis Minnie')
name = Memphis Minnie
>>>
>>> blues_singer(name='Memphis Minnie', born='3rd June 1897', died='6th August 1973')
born = 3rd June 1897
name = Memphis Minnie
died = 6th August 1973
{% endhighlight %}

`**kwargs` also works alongside formal parameters:

{% highlight python linenos %}
>>> def blues_singer(name, **kwargs):
...     print name
...     for key, value in kwargs.items():
...         print key + ' = ' + value
...
>>> blues_singer(name='Memphis Minnie', born='3rd June 1897', died='6th August 1973')
Memphis Minnie
born = 3rd June 1897
died = 6th August 1973
>>>
>>> blues_singer(name='Memphis Minnie', born='3rd June 1897', died='6th August 1973', popular_songs='Me and My Chauffeur, Evil Devil Woman Blues')
Memphis Minnie
born = 3rd June 1897
died = 6th August 1973
popular_songs = Me and My Chauffeur, Evil Devil Woman Blues
>>>
{% endhighlight %}

As well as being used in defining functions `*args` & `**kwargs` can also be used when calling functions:

{% highlight python linenos %}
>>> def blues_singer(name, born, died):
...     print name + ' was born on ' + born + ' and died on ' + died
...
>>> # We define our arguments in a separate variable
>>> memphis_minnie = ('Memphis Minnie', '3rd June 1897', '6th August 1973')
>>>
>>> # We call the function with the `*` before our arguments variable
>>> blues_singer(*memphis_minnie)
Memphis Minnie was born on 3rd June 1897 and died on 6th August 1973
>>>
>>> # Using `args` for our variable name might make more sense
>>> args = ('Memphis Minnie', '3rd June 1897', '6th August 1973')
>>>
>>> blues_singer(*args)
Memphis Minnie was born on 3rd June 1897 and died on 6th August 1973
>>>
>>> args = ('Bessie Smith', '15th April 1894', '26th September 1937')
>>>
>>> blues_singer(*args)
Bessie Smith was born on 15th April 1894 and died on 26th September 1937
>>>
>>> # And we can pass the values in as a combination of formal parameters and a list of `args`
>>> args = ('15th April 1894', '26th September 1937')
>>>
>>> blues_singer('Memphis Minnie', *args)
Memphis Minnie was born on 3rd June 1897 and died on 6th August 1973
>>>
{% endhighlight %}

Using `*args` when calling our function unpacks the values in the `args` variable and passes them as positional arguments to the function.
And if we try and ask it to unpack and pass a different number of values than is asked for by the function?

{% highlight python linenos %}
>>> args = ('Bessie Smith', '15th April 1894')
>>>
>>> blues_singer(*args)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: blues_singer() takes exactly 3 arguments (2 given)
>>>
>>> args = ('Bessie Smith', '15th April 1894', '26th September 1937', 'Gimme a Pigfoot and a Bottle of Beer')
>>>
>>> blues_singer(*args)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: blues_singer() takes exactly 3 arguments (4 given)
{% endhighlight %}

Just the same as if `*args` wasn't being used.
As before, using `**kwargs` is the same, except we pass a dictionary to the function:

{% highlight python linenos %}
>>> def blues_singer(name, born, died):
...      print name + ' was born on ' + born + ' and died on ' + died
...
>>> kwargs = {'name':'Memphis Minnie', 'born':'3rd June 1897', 'died':'6th August 1973'}
>>>
>>> blues_singer(**kwargs)
Memphis Minnie was born on 3rd June 1897 and died on 6th August 1973
>>>
>>> # We can also pass in the values as a combination of formal parameters and a `kwargs` dictionary
>>>
>>> kwargs = {'born':'3rd June 1897', 'died':'6th August 1973'}
>>>
>>> blues_singer(name='Memphis Minnie', **kwargs)
Memphis Minnie was born on 3rd June 1897 and died on 6th August 1973
{% endhighlight %}

So, that's that, then.

In writing this post I borrowed heavily from:

- [Agiliq](http://agiliq.com/blog/2012/06/understanding-args-and-kwargs/)
- [Python Tips](http://book.pythontips.com/en/latest/args_and_kwargs.html)
- [Stack Overflow](https://stackoverflow.com/questions/3394835/args-and-kwargs) (of course!)

Many thanks to these folks for helping me to understand this.
