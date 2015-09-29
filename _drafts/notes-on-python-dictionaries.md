---
layout: post
title: "Notes on Python: Dictionaries"
date: "2015-09-28 19:42"
tags: python
---


Let's create a dictionary

```python
>>> blues = {}
>>> blues
{}
>>> type(blues)
<type 'dict'>
>>> dir(blues)
['__class__', '__cmp__', '__contains__', '__delattr__', '__delitem__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__init__', '__iter__', '__le__', '__len__', '__lt__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'clear', 'copy', 'fromkeys', 'get', 'has_key', 'items', 'iteritems', 'iterkeys', 'itervalues', 'keys', 'pop', 'popitem', 'setdefault', 'update', 'values', 'viewitems', 'viewkeys', 'viewvalues']
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
>>> blues_songs['J.B. Lenoir'] = 'Down In Mississippi'  # Add a new singer/song
>>> blues_songs
{'J.B. Lenoir': 'Down In Mississippi', 'Otis Spann': 'Good Morning Mr. Blues', 'Memphis Slim': 'Miss Ida B'}
>>> blues_songs['Memphis Slim'] = 'Born With the Blues'  # Add a singer that already exists
>>> blues_songs
{'J.B. Lenoir': 'Down In Mississippi', 'Otis Spann': 'Good Morning Mr. Blues', 'Memphis Slim': 'Born With the Blues'}
>>> len(blues_songs)  # see how many entries we have
3
>>> blues_songs['Otis Spann']  # Call a singer to see what their song is
'Good Morning Mr. Blues'
>>> blues_songs['Elmore James']  # Call a singer that isn't in our dictionary
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'Elmore James'
#  So, let's check if an entry is there already
>>> 'Elmore James' in blues_songs
False
>>> 'Memphis Slim' in blues_songs
True
# iterate over the entries in the dictionary
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
>>> blues_songs.items()
[('J.B. Lenoir', 'Down In Mississippi'), ('Otis Spann', 'Good Morning Mr. Blues'), ('Memphis Slim', 'Born With the Blues')]
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
