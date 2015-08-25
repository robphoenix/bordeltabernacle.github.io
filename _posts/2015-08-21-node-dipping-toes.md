---
layout: post
title:  "Notes on Node.js: Dipping my toes in"
date:   2015-08-21
tags:   node.js, javascript
---
I started using [exercism.io][exercism] this morning after hearing about it on [Code Newbies][cn], mainly to get more practice with Python.  I'm also learning JavaScript as well and, seeing there was a JS track, I thought I'd have a glance at it, and inadvertently started using node.js, something I've wanted to do for a while now.  I got that new *'don't quite understand but feel real excited'* feeling.
<!--more-->

As far as my understanding goes node.js is basically JavaScript, the programming language of the web, on a computer, desktop/laptop/server etc., rather than in a browser. It's easy enough to work out how to install node.js so I won't go into that here.  
I started out with a `Hello World` exercise, as you do.  I gotta say, it looked strange, no `$(document).ready()` here, and I did end up sneaking a peek [here][js-hello].  I really didn't know where to start with fixing the given code to make the tests pass, my mind was going down overly complicated unorganised paths, and I figured I'd be better off working backwards.  Which is what this will be a part of, break apart the code, understand the parts.

So, anyway, this is the code.

```javascript
'use strict';

var HelloWorld = function() {};

HelloWorld.prototype.hello = function(name) {
  name = name || 'world';
  return 'Hello, ' + name + '!';
};

module.exports = HelloWorld;

var helloWorld = new HelloWorld();
```

The `var` & `function` stuff I understand, but `strict`? `prototypes`? `module.exports`? Wha'fu'?

What was interesting was running it.  Just like Python really; `node .\hello-world.js` in the CLI.  Let's stick a `console.log('Hello node, whaddya know?')` in there:

```javascript
 > node .\hello-world.js
Hello node, whaddya know?
```

Cool, huh? And to run the tests:

```javascript
 > jasmine-node .\hello-world.js


Finished in 0 seconds
0 tests, 0 assertions, 0 failures, 0 skipped
```

So, it's basic, it's obvious, but it's blowin' my mind just a bit.  I'm well excited to get started with this and explore it further.  I'm gonna go over this little code snippet and work it out as best I can, and then who knows, maybe I'll get a MEAN stack up and running for my *new awesome web app idea*.

[exercism]: http://exercism.io/
[cn]:       http://www.codenewbie.org/podcast/nitpicks-and-devils
[js-hello]: https://github.com/exercism/xjavascript/blob/master/hello-world/example.js
