---
layout: post
title: "Notes on JavaScript: Prototype"
date: "2015-08-24 09:23"
tags: javascript
---
Functions have an empty `prototype` property by default that can be used, alternatively to `this`, to add a methods to objects.
<!--more-->

Using [exercism.io][e.io] to learn JavaScript I was presented with this code:
{% highlight javascript %}
var HelloWorld = function() {};

HelloWorld.prototype.hello = function(name) {
  name = name || 'world';
  return 'Hello, ' + name + '!';
};

module.exports = HelloWorld;

var helloWorld = new HelloWorld();
{% endhighlight %}

I understood the basics of what was going on but have never seen `prototype` before, and wondered why it was being used to add the `hello` method to the `HelloWorld` object rather than just using `this`.

It turns out to be quite simple, unless I'm missing something.  Using `this` creates a method that is owned by the object and so if it is changed, that change will only be reflected in that object.  Whereas `prototype` creates a method that is owned by all instances of the object, and changing it will change it for every object.

So,
{% highlight javascript %}
var helloWorld = function(name) {
  this.name = name || 'world';
  this.hello = function() {
    console.log('Hello, ' + this.name + '!');
  }
};

helloWorld.prototype.goodbye = function() {
  console.log('Goodbye, ' + this.name + '!');
};


var helloBillie = new helloWorld('Billie Holiday');

helloBillie.hello();
helloBillie.goodbye();
{% endhighlight %}


[e.io]: http://exercism.io/
