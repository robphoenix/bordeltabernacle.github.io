---
layout: post
title: "Notes on JavaScript: Prototype"
date: "2015-08-24 09:23"
tags: javascript
---

In my attempts to find out what `HelloWorld.prototype.hello` was all about I fell down a mental JavaScript Objects rabbit hole that twisted my mind and made me question the existential reality of programming languages.


I wanted to know what prototype was and I fell down a rabbit hole.
In it's simplest terms `prototype` is a  way of assigning properties and methods to an object.

In JavaScript an Object is an *unordered list of name/value pairs*.  These name/value pairs can be properties or methods.  Let's start by looking at creating an Object using the *object literal* syntax:

```javascript
> var bluesSinger = {
...   name:"Sleepy John Estes",
...   born:"25th January 1899",
...   died:"5th June 1977",
...   sing:function() {
.....     console.log("Someday, baby, you aint gonna worry my mind anymore")
.....   }
... };
> bluesSinger.name
'Sleepy John Estes'
> bluesSinger.sing()
Someday, baby, you aint gonna worry my mind anymore
```

We can also add to this Object:

```javascript
> bluesSinger.birthplace = "Ripley, Tennessee, U.S.";
> bluesSinger.birthplace
'Ripley, Tennessee, U.S.'
> bluesSinger
{ name: 'Sleepy John Estes',
  born: '25th January 1899',
  died: '5th June 1977',
  sing: [Function],
  birthplace: 'Ripley, Tennessee, U.S.' }
```

Simple.  But this is essentially a one-off Object.
Alternatively we can use the *constructor object* syntax:

```javascript

Prototype is a way of adding to a function.
OOP - Prototypal based language

Using [exercism.io][e.io] to learn JavaScript I was presented with this code:

```javascript
var HelloWorld = function() {};

HelloWorld.prototype.hello = function(name) {
  name = name || 'world';
  return 'Hello, ' + name + '!';
};

module.exports = HelloWorld;

var helloWorld = new HelloWorld();
```




[e.io]: http://exercism.io/

https://stackoverflow.com/questions/19608933/javascript-prototype-applies-not-only-for-instance-objects
http://blog.kevinchisholm.com/javascript/difference-between-object-literal-and-instance-object/
https://stackoverflow.com/questions/310870/use-of-prototype-vs-this-in-javascript
https://stackoverflow.com/questions/15008710/javascript-object-literal-vs-constructor
http://www.howtocreate.co.uk/tutorials/javascript/objects
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects
https://stackoverflow.com/questions/2653299/creating-new-objects-in-javascript
http://javascriptissexy.com/javascript-objects-in-detail/
http://aspiringcraftsman.com/2011/12/19/solid-javascript-the-openclosed-principle/
http://joelabrahamsson.com/a-simple-example-of-the-openclosed-principle/
https://en.wikipedia.org/wiki/Open/closed_principle
http://ericleads.com/2012/09/stop-using-constructor-functions-in-javascript/
http://ericleads.com/2013/01/javascript-constructor-functions-vs-factory-functions/
http://www.javascriptkit.com/javatutors/oopjs2.shtml
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript
