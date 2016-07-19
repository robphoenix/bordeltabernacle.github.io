---
layout: post
title:  "Notes on Python: Optional Arguments"
date:   2015-08-21
tags:   python
---

Passing an optional argument to a Python function.  This is pretty basic stuff but I didn't know it, or at least it hadn't properly registered as a thing in my mind until today.  This post doesn't even take into consideration `*args` & `**kwargs`.

<!--more-->

Here we have a simple Python function, which takes one argument:

```python
>>> def the_blues(name):
...     print name + ' got the blues.'
...
>>>
>>> the_blues('Big Bill Broonzy')
Big Bill Broonzy got the blues.
>>>
>>> the_blues()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: the_blues() takes exactly 1 argument (0 given)
```

When we call the function with an argument we're all good.  But when we don't include an argument, no dice, we get an error.  Fair enough, like it says `the_blues() takes exactly 1 argument (0 given)`.
Often, I write scripts for other people to use.  My young son can be a bit timid around other people sometimes, but I tell him *"People are O.K."* Unfortunately other people don't always use scripts the way the writer intended.  You can't be too strict with these things, you've gotta account for people doing things differently, always.
So let's stick an `if/else` in this function to make up for this.

```python
>>> def the_blues(name):
...     if name != None:
...         print name + ' got the blues.'
...     else:
...         print 'You got the blues.'
...
>>> the_blues('Sister Rosetta Tharpe')
Sister Rosetta Tharpe got the blues.
>>> the_blues()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: the_blues() takes exactly 1 argument (0 given)

# or

>>> def the_blues(name):
...      if name:
...          print name + ' got the blues.'
...      else:
...          print 'You got the blues.'
...
>>> the_blues('Memphis Slim')
Memphis Slim got the blues.
>>> the_blues()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: the_blues() takes exactly 1 argument (0 given)
```

Same again, no dice.
So, this is where an optional argument can come in handy.  Of course, this is also a pretty good situation to pull in a `try/except`.

```python
>>> def the_blues(name=''):
...     if name:
...         print name + ' got the blues.'
...     else:
...         print 'You got the blues.'
...
>>> the_blues('J. B. Lenoir')
J. B. Lenoir got the blues.
>>> the_blues()
You got the blues.

# or even

>>> def the_blues(name='You'):
...     print name + ' got the blues.'
...
>>> the_blues("Lightnin' Hopkins")
Lightnin' Hopkins got the blues.
>>> the_blues()
You got the blues.
```

By defining our argument as `name=` rather than `name`, and passing an initial value to it, we make it optional to give an argument when calling the function.  We're basically saying *'Look, we'll use `this value` unless you give us something different'*.

Thanks to [exercism.io][exercism] for making me aware of this.  Exercism is very cool, check it out. Never stop learning.

[exercism]: http://exercism.io/
