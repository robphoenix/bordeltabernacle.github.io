---
layout: post
title: "Notes on Python: Dictionaries"
date: "2015-09-28 19:42"
tags: python
---

A dictionary is a key/value store.  A value is bound to a key, stored within a data structure, and referenced by this key.  In the common dictionary we all know the word is the key, and it's meaning is the value.  Dictionaries are indispensable to language, and in Python they are a fundamental data structure.

<!--more-->

We can create an empty dictionary object in a couple of ways.

```python
>>> blues = {}
>>> blues
{}
>>> type(blues)
<type 'dict'>
>>> delta_blues = dict()
>>> print delta_blues
{}
>>> type(delta_blues)
<type 'dict'>
```

Let's create a dictionary with something already in it.

```python
>>> blues_songs = {'Memphis Slim': 'Miss Ida B', 'Otis Spann': 'Good Morning Mr. Blues'}
>>> blues_songs
{'Otis Spann': 'Good Morning Mr. Blues', 'Memphis Slim': 'Miss Ida B'}
```

And add to it...

```python
>>> blues_songs['J.B. Lenoir'] = 'Down In Mississippi'
>>> blues_songs
{'J.B. Lenoir': 'Down In Mississippi', 'Otis Spann': 'Good Morning Mr. Blues', 'Memphis Slim': 'Miss Ida B'}
```

Update a pre-existing entry...

```python
>>> blues_songs['Memphis Slim'] = 'Born With the Blues'
>>> blues_songs
{'J.B. Lenoir': 'Down In Mississippi', 'Otis Spann': 'Good Morning Mr. Blues', 'Memphis Slim': 'Born With the Blues'}
```

Check the number of entries we have in our dictionary...

```python
>>> len(blues_songs)
3
```

Remove entries...

```python
>>> blues_songs.pop('Memphis Slim')
'Miss Ida B'
>>> blues_songs
{'Otis Spann': 'Good Morning Mr. Blues'}
>>> del blues_songs['Otis Spann']
>>> blues_songs
{'J.B. Lenoir': 'Down In Mississippi'}
```

And combine two dictionaries...

```python
>>> blues_songs
{'Memphis Slim': 'Miss Ida B', 'Otis Spann': 'Good Morning Mr. Blues'}
>>> more_blues_songs = {'J.B. Lenoir': 'Down In Mississippi', 'Elmore James': 'The Sky Is Crying'}
>>> blues_songs.update(more_blues_songs)
>>> blues_songs
{'J.B. Lenoir': 'Down In Mississippi', 'Elmore James': 'The Sky Is Crying', 'Otis Spann': 'Good Morning Mr. Blues', 'Memphis Slim': 'Miss Ida B'}
>>> more_blues_songs
{'J.B. Lenoir': 'Down In Mississippi', 'Elmore James': 'The Sky Is Crying'}
```

Let's see what we've got in our dictionary....

```python
>>> blues_songs['Otis Spann']
'Good Morning Mr. Blues'
>>> blues_songs['Elmore James']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'Elmore James'
>>> 'Elmore James' in blues_songs
False
>>> 'Memphis Slim' in blues_songs
True
>>> blues_songs.has_key('Elmore James')
False
>>> blues_songs.has_key('Memphis Slim')
True
>>> blues_songs.get('Memphis Slim')
'Miss Ida B'
>>> blues_songs.get('Elmore James', 'No Dice!')
'No Dice!'
>>> for singer in blues_songs:
...     print singer
...
J.B. Lenoir
Otis Spann
Memphis Slim
>>> for singer in blues_songs:
...     print blues_songs[singer]                                                                                                           
...
Down In Mississippi
Good Morning Mr. Blues
Born With the Blues
>>> for singer in blues_songs:
...     print singer + ' sings ' + blues_songs[singer]
...
J.B. Lenoir sings Down In Mississippi
Otis Spann sings Good Morning Mr. Blues
Memphis Slim sings Born With the Blues
```

The dictionary object we've created has some built-in methods we can use to access the items within it...

```python
>>> blues_songs.items()
[('J.B. Lenoir', 'Down In Mississippi'), ('Otis Spann', 'Good Morning Mr. Blues'), ('Memphis Slim', 'Born With the Blues')]
>>> type(blues_songs.items())
<type 'list'>
>>> for item in blues_songs.items():
...     print type(item)
...
<type 'tuple'>
<type 'tuple'>
>>> for singer, song in blues_songs.items():
...     print singer + '\t' + song
...
Otis Spann      Good Morning Mr. Blues
Memphis Slim    Miss Ida B
>>> blues_songs.keys()
['J.B. Lenoir', 'Otis Spann', 'Memphis Slim']
>>> blues_songs.values()
['Down In Mississippi', 'Good Morning Mr. Blues', 'Born With the Blues']
>>> for singer in blues_songs.iteritems():
...     print singer
...
('J.B. Lenoir', 'Down In Mississippi')
('Otis Spann', 'Good Morning Mr. Blues')
('Memphis Slim', 'Born With the Blues')
>>> for singer, song in blues_songs.iteritems():
...     print singer + ' = ' + song
...
J.B. Lenoir = Down In Mississippi
Otis Spann = Good Morning Mr. Blues
Memphis Slim = Born With the Blues
>>> for key, value in blues_songs.iteritems():
...     print 'The Key: ' + key + '\tThe Value: ' + value
...
The Key: J.B. Lenoir    The Value: Down In Mississippi
The Key: Otis Spann     The Value: Good Morning Mr. Blues
The Key: Memphis Slim   The Value: Born With the Blues
>>> type(blues_songs.iteritems())
<type 'dictionary-itemiterator'>
>>> for singer in blues_songs.iteritems():
...     print type(singer)
...
<type 'tuple'>
<type 'tuple'>
```

A dictionary can also be nested, enabling the building up of more complicated data structures...

```python
>>> blues_singers = {'Memphis Slim': {'sings': ['Miss Ida B', 'Born With the Blues'], 'born': '03/09/1915', 'died': '24/02/1988'}, 'Otis Spann': {'sings': ['Good Morning Mr. Blues', 'Must Have Been the Devil'], 'born': '21/03/1930', 'died': '24/04/2970'}}
>>> blues_singers
{'Otis Spann': {'sings': ['Good Morning Mr. Blues', 'Must Have Been the Devil'], 'died': '24/04/2970', 'born': '21/03/1930'}, 'Memphis Slim': {'sings': ['Miss Ida B', 'Born With the Blues'], 'died': '24/02/1988', 'born': '03/09/1915'}}
>>> blues_singers.keys()
['Otis Spann', 'Memphis Slim']
>>> blues_singers.values()
[{'sings': ['Good Morning Mr. Blues', 'Must Have Been the Devil'], 'died': '24/04/2970', 'born': '21/03/1930'}, {'sings': ['Miss Ida B', 'Born With the Blues'], 'died': '24/02/1988', 'born': '03/09/1915'}]
>>> blues_singers['Memphis Slim']
{'sings': ['Miss Ida B', 'Born With the Blues'], 'died': '24/02/1988', 'born': '03/09/1915'}
>>> blues_singers['Memphis Slim']['born']
'03/09/1915'
```

There you have the dictionary.  Thanks to books by [Mark Lutz][learnpy] and [Charles Severance][pyinfor] for letting me lean on their knowledge.

[learnpy]: 
[pyinfor]:
