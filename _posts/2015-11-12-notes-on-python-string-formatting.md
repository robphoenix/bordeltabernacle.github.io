---
layout: post
title: "Notes on Python: String Formatting"
tags: python
---

I've only recently realised I've been formatting text in Python in a really clunky manner.  The `.format()` string method is something that I've seen and even briefly used, but never stopped and got to know well enough to use more efficiently.
So, that's what this is, a little *get to know you* with `string.format()`

<!--more-->

In it's simplest terms `.format()` takes a string that contains braces (`{}`), referred to as *replacement fields*, and replaces them with an argument.

```python
In [1]: "Today, I'm listening to {}.".format("Blind Willie Johnson")
Out[1]: "Today, I'm listening to Blind Willie Johnson."
```

We can include a number of arguments...

```python
In [2]: listening = ("Today, I'm listening to the musician {}."
   ...: " Their songs include: {}, {}, {}.")

In [3]: listening.format("Blind Willie Johnson",
   ...: "Dark Was The Night, Cold Was The Ground",
   ...: "If I Had My Way I'd Tear The Building Down",
   ...: "It's Nobody's Fault But Mine")
Out[3]: "Today, I'm listening to the musician Blind Willie Johnson. Their songs include: Dark Was The Night, Cold Was The Ground, If I Had My Way I'd Tear The Building Down, It's Nobody's Fault But Mine."
```

Refer to these arguments positionally...

```python
In [5]: listening = ("Today, I'm listening to the musician {0}."
   ...: " Their songs include: {2}, {3}, {1}.")

In [6]: listening.format("Blind Willie Johnson",
   ...: "Dark Was The Night, Cold Was The Ground",
   ...: "If I Had My Way I'd Tear The Building Down",
   ...: "It's Nobody's Fault But Mine")
Out[6]: "Today, I'm listening to the musician Blind Willie Johnson. Their songs include: If I Had My Way I'd Tear The Building Down, It's Nobody's Fault But Mine, Dark Was The Night, Cold Was The Ground."
```

Or even use keyword arguments...

```python
In [7]: listening = ("Today, I'm listening to the album {album}, "
....: "by musician {musician}.")

In [9]: listening.format(
   ...: musician="Blind Willie Johnson",
   ...: album="Sweeter As The Years Go By")
Out[9]: "Today, I'm listening to the album Sweeter As The Years Go By, by musician Blind Willie Johnson."
```

We can also take an iterable, and access it's elements in the string.
For instance, a simple list:

```python
In [10]: songs = ["John The Revelator",
   ....: "Let Your Light Shine On Me",
   ....: "Everybody Ought To Treat A Stranger Right"]

In [11]: songs[1]
Out[11]: 'Let Your Light Shine On Me'

In [12]: "{[1]} is currently playing".format(songs)
Out[12]: 'Let Your Light Shine On Me is currently playing'
```

And even something a little more complex...

```python
In [33]: todays_music = {
   ....: "musician": "Blind Willie Johnson",
   ....: "album": "Sweeter As The Years Go By",
   ....: "songs": ["John The Revelator",
   ....: "Let Your Light Shine On Me",
   ....: "Everybody Ought To Treat A Stranger Right"]}

In [34]: "I'm listening to the album {[album]}".format(todays_music)
Out[34]: "I'm listening to the album Sweeter As The Years Go By"

In [35]: sentence = ("I'm listening to the album {0[album]}"
   ...: ", by {0[musician]},"
   ...: " currently playing is {0[songs][1]}")

In [36]: sentence.format(todays_music)
Out[36]: "I'm listening to the album Sweeter As The Years Go By, by Blind Willie Johnson, currently playing is Let Your Light Shine On Me"
```

Note that when we're accessing more than one element from the given data structure we need to explicitly refer to that data structure. If we don't we will *use up* the given data structure with the first reference to it and have nothing left available to us...

```python
In [9]: bad_sentence = ("I'm listening to the album {[album]}"
   ...: ", by {[musician]},")

In [10]: bad_sentence.format(todays_music)
---------------------------------------------------------------------------
IndexError                                Traceback (most recent call last)
<ipython-input-10-3c99705a5b66> in <module>()
----> 1 bad_sentence.format(todays_music)

IndexError: tuple index out of range
```

Above, we referred to our data structure by it's positional index, but we can also use a keyword argument...

```python
In [13]: named_sentence = ("I'm listening to the album {music[album]}"
   ....: ", by {music[musician]}")

In [14]: named_sentence.format(music=todays_music)
Out[14]: "I'm listening to the album Sweeter As The Years Go By, by Blind Willie Johnson"
```

As well as providing replacements for placeholders in strings, we can include options to specify the format of our arguments.

We can specify the `fill`, the minimum amount of space to be filled...

```python
In [18]: "{0:10} : {1:30}.".format("Musician", "Blind Willie Johnson")
Out[18]: 'Musician   : Blind Willie Johnson          .'
```

Here, within the braces we put a colon (`:`) before our *format specifiers*, the number afterwards is the size of the `fill`.
we can also include an alignment specifier, and a fill character...

```python
In [19]: "{0:10} : {1:>30}.".format("Musician", "Blind Willie Johnson")
Out[19]: 'Musician   :           Blind Willie Johnson.'

In [20]: "{0:^10} : {1:^30}.".format("Musician", "Blind Willie Johnson")
Out[20]: ' Musician  :      Blind Willie Johnson     .'

In [21]: "{0:_^10} : {1:_^30}.".format("Musician", "Blind Willie Johnson")
Out[21]: '_Musician_ : _____Blind Willie Johnson_____.'

In [23]: "{0:[>10} : {1:]<30}.".format("Musician", "Blind Willie Johnson")
Out[23]: '[[Musician : Blind Willie Johnson]]]]]]]]]].'

In [46]: "{0:_<{width}}{1:_<{width}}.".format("Musician", "Blind Willie Johnson", width=len("Blind Willie Johnson"))
Out[46]: 'Musician____________Blind Willie Johnson.'
```

We can also do some interesting things with numbers.
For instance, let's translate an IP address from decimal to binary and hexidecimal:

```python
In [27]: "{:0>8b}.{:0>8b}.{:0>8b}.{:0>8b}".format(192,168,1,255)
Out[27]: '11000000.10101000.00000001.11111111'

In [36]: "{:^#x} {:^#x} {:^#x} {:^#x}".format(192,168,1,255)
Out[36]: '0xc0 0xa8 0x1 0xff'
```

So, in the first example, translating to binary we specify our format as so:

`{:0>8b}`

`{:<fill the space with zeroes><align the number to the right><create a fill of 8 characters><provide the output in binary>}`

and in the second, translating to hex:

`{:^#x}`

`{:<center align><select alternate form of output><hex output>}`

`.format()` is a pretty useful tool, better than sticking `\t`'s in your strings to format them, as I've been known to do :|

References:

- [Python Docs](https://docs.python.org/2/library/string.html#format-specification-mini-language)
- [New Mexico Tech](http://infohost.nmt.edu/tcc/help/pubs/python/web/new-str-format.html)
