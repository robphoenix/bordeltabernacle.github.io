---
layout: post
title:  "the Python List"
date:   2015-03-31
categories: python
---

> Lists are a really useful basic Python type that you will come back to over and again.

Like the numerable daily lists made with pen and paper, the list in Python is an incredibly handy way to store information, but with extra *power*.

We can start with an empty list:
{% highlight python %}
>>> switches = []
>>> type(switches)
<type 'list'>
{% endhighlight %}
add to it:
{% highlight python %}
>>> switches.append('switch-01')
>>> switches
['switch-01']
>>> switches.append('switch-02')
>>> switches
['switch-01', 'switch-02']
{% endhighlight %}
Create another list, and concatenate these two lists into a new list:
```python
>>> routers = ['router-03', 'router-01', 'router-02']
>>> routers
['router-03', 'router-01', 'router-02']
>>> devices = switches + routers
>>> devices
['switch-01', 'switch-02', 'router-03', 'router-01', 'router-02']
```
Loop through our new list:
```python
>>>count = 0
>>>for device in devices:
...		count += 1
...     print 'Hostname:', device
...
Hostname: switch-01
Hostname: switch-02
Hostname: router-03
Hostname: router-01
Hostname: router-02
>>>print 'There are', count, 'devices.'
There are 5 devices.
```
Sort the list:
```python
>>> devices
['switch-01', 'switch-02', 'router-03', 'router-01', 'router-02']
>>> devices.sort()
>>> devices
['router-01', 'router-02', 'router-03', 'switch-01', 'switch-02']
```
Delete elements from the list:
```python
>>> devices.pop()
'switch-02'
>>> devices
['router-01', 'router-02', 'router-03', 'switch-01']
>>>
>>> devices.remove('router-03')
>>> devices
['router-01', 'router-02', 'switch-01']
```
Find the number of items in our list, and the maximum and minimum elements in the list:
```python
>>> len(devices)
3
>>> max(devices)
'switch-01'
>>> min(devices)
'router-01'
```
Select elements in the list through their index (not forgetting that python lists are zero-indexed!):
```python
>>> devices[0]
'router-01'
>>> devices[1]
'router-02'
```
Lists can be really useful as an interim holding bay while parsing a file, or extracting information from a directory, such as a folder of *show run* or *show version* files.

This brief summary really is brief and pulls heavily from others:
- [Python for Informatics][1]
- [Google Python Course][2]
- [Dive Into Python][3]
- [Brent Salisbury Python Tutorial][4]
[1]: http://www.pythonlearn.com/html-009/book009.html
[2]: https://developers.google.com/edu/python/lists
[3]: http://www.diveintopython.net/native_data_types/lists.html
[4]: http://networkstatic.net/python-tutorial-functions-and-passing-lists-and-dictionaries-with-simple-examples/
