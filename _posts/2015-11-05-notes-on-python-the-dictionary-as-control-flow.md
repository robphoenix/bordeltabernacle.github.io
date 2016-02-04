---
layout: post
title:  "Notes on Python: The Dictionary as Control Flow"
tags:   python
---

This is pretty much lifted straight from Mark Lutz's [Learning Python][lp].  It's just a different way of implementing a simple `if/elif/else` block akin to JavaScript's `switch/case` statement, or Clojure's `cond`.

<!--more-->

So, normally in Python if you want a series of potential outcomes based on an input value then you implement a series of `if/elif` statements...

{% highlight python linenos %}
In [2]: def sing_blues():
   ...:     print "Choices:\nSkip James\nBessie Smith\nLeroy Carr\nVictoria Spivey\n"
   ...:     choice = raw_input("Who would you like to hear today?\n")
   ...:     if choice == "Skip James":
   ...:         print "\nYou know, I'd rather be the ol' devil\n"
   ...:     elif choice == "Bessie Smith":
   ...:         print "\nI hate to see de evenin' sun go down\n"
   ...:     elif choice == "Leroy Carr":
   ...:         print "\nHow long babe how long\n"
   ...:     elif choice == "Victoria Spivey":
   ...:         print "\nDetroit's a cold, hard place, and I ain't got a dime to my name\n"
   ...:     else:
   ...:         print "\nNo Dice\n"
   ...:

In [3]: sing_blues()
Choices:
Skip James
Bessie Smith
Leroy Carr
Victoria Spivey

Who would you like to hear today?
Skip James

You know, I'd rather be the ol' devil

{% endhighlight %}

This *multiway branching* is pretty cumbersome and tedious, so alternatively we can use a dictionary to hold our choices, accessing it according to the input given...

{% highlight python linenos %}
In [4]: def sing_blues():
   ...:     print "Choices:\nSkip James\nBessie Smith\nLeroy Carr\nVictoria Spivey\n"
   ...:     choice = raw_input("Who would you like to hear today?\n")
   ...:     print {"Skip James": "\nYou know, I'd rather be the ol' devil\n",
   ...:            "Bessie Smith": "\nI hate to see de evenin' sun go down\n",
   ...:            "Leroy Carr": "\nHow long babe how long\n",
   ...:            "Victoria Spivey": "\nDetroit's a cold, hard place, and I ain't got a dime to my name\n"
   ....:          }[choice]
   ...:

In [5]: sing_blues()
Choices:
Skip James
Bessie Smith
Leroy Carr
Victoria Spivey

Who would you like to hear today?
Victoria Spivey

Detroit's a cold, hard place, and I ain't got a dime to my name
{% endhighlight %}

Much more concise, eh?  And to include a default `else` clause...

{% highlight python linenos %}
In [13]: def sing_blues():
   ....:     print "Choices:\nSkip James\nBessie Smith\nLeroy Carr\nVictoria Spivey\n"
   ....:     choice = raw_input("Who would you like to hear today?\n")
   ....:     choices = {"Skip James": "\nYou know, I'd rather be the ol' devil\n",
   ....:     "Bessie Smith": "\nI hate to see de evenin' sun go down\n",
   ....:     "Leroy Carr": "\nHow long babe how long\n",
   ....:     "Victoria Spivey": "\nDetroit's a cold, hard place, and I ain't got a dime to my name\n"}
   ....:     print choices.get(choice, "\nNo Dice\n")
   ....:

In [14]: sing_blues()
Choices:
Skip James
Bessie Smith
Leroy Carr
Victoria Spivey

Who would you like to hear today?
Bessie Smith

I hate to see de evenin' sun go down


In [15]: sing_blues()
Choices:
Skip James
Bessie Smith
Leroy Carr
Victoria Spivey

Who would you like to hear today?
Memphis Slim

No Dice
{% endhighlight %}

I like this, I think it's a pretty neat way of describing potential branches, though I'm also aware that I'm constantly using newly discovered tools *just because*, like sticking lengthy `list comprehensions` everywhere, rather than being more mature about these things and using tools appropriately.  Mark suggests a rule of thumb to always err on the side of simplicity and readability, which I think is pretty sensible.  Mark goes on to describe more complex branching beyond the simple key/value dictionary used here, using `lambda functions`.

{% highlight python linenos %}
In [22]: def maths_on_two_numbers(action, a, b):
   ....:     actions = {"+": (lambda a, b: a + b),
   ....:                "-": (lambda a, b: a - b),
   ....:                "*": (lambda a, b: a * b),
   ....:                "/": (lambda a, b: a / b)}
   ....:     print actions[action](a, b)
   ....:

In [23]: maths_on_two_numbers("+", 5, 7)
12

In [24]: maths_on_two_numbers("*", 5, 7)
35
{% endhighlight %}

Note how when we access the dictionary we have to pass in the arguments the `lambda` expects.  Creating the dictionary creates the `lambda` functions, it doesn't call them.  That happens when we add the parenthesis to the dictionary access statement, which in turn requires the expected arguments.  Without the parens we'd just get back the function...

{% highlight python linenos %}
In [16]: def maths_on_two_numbers(action, a, b):
   ....:     actions = {"+": (lambda a, b: a + b),
   ....:                "-": (lambda a, b: a - b),
   ....:                "*": (lambda a, b: a * b),
   ....:                "/": (lambda a, b: a / b)}
   ....:     print actions[action]
   ....:

In [17]: maths_on_two_numbers("+", 5, 7)
<function <lambda> at 0x7f671d601848>
{% endhighlight %}

Well, this is just a brief note on something I found interesting, which I guess is what a blog post is.  All credit due to [Mark Lutz][lp].  I'm really enjoying going back over what I now know in Python and digging deeper, understanding properly what I'm doing and the more detailed nuances of the language.


[lp]: http://www.amazon.co.uk/Learning-Python-Mark-Lutz-ebook/dp/B00DDZPC9S/ref=sr_1_1?s=digital-text&ie=UTF8&qid=1446717595&sr=1-1&keywords=learning+python
