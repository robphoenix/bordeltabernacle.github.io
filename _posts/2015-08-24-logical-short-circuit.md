---
layout: post
title: "Notes on JavaScript: Logical Short-circuit"
date: "2015-08-24 09:23"
tags: javascript, python
---
Following on from my last two posts ([1][onePost]/[2][twoPost]), I was reading [Eloquent Javascript][ej] over the weekend and learnt about a behaviour of logical operators relevant to both posts.

<!--more-->

This bit of code from the [exercism.io][exercism] *"Hello, world"* exercise:

```javascript
name = name || 'world';
```

gives the `name` variable the ability to be either the string `'world'` or the variable `name`.  Which one it ends up being depends on whether the logical operator `||` reads the variable `name` as `true` or `false`.

The `||` works by converting the value on it's left to a Boolean, and if it's `true` then it will use that value, and if it converts to `false` it will use the value on it's right.

```javascript
> node  // Yep, node has a REPL!! Awesome!
> var name = false;  // This could also be null or '', but not ' '
> name
false
> console.log(name);
false
> console.log(name || 'Anonymous');
Anonymous
> name = 'Son House';
'Son House'
> console.log(name);
Son House
> console.log(name || 'Anonymous');
Son House
```

If we switch the values on either side of the `||`, then the left hand side will always convert to `true` and this will essentially make using this technique redundant.

```javascript
> name = 'Son House';
'Son House'
> console.log('Anonymous' || name);
Anonymous
```

This behaviour gives us a technique similar to the Python optional argument, and we can rewrite our Python `the_blues` function, from [previously][twoPost], in JavaScript as such:

```javascript
> var theBlues = function(name) {
... name = name || 'You';
... console.log(name + ' got the Blues.');
... };
> theBlues();
You got the Blues.
> theBlues('Son House');
Son House got the Blues.
```

And what about `&&`?  Well, it does the same except that if the value on its left converts to `false`, rather than `true`, it returns that value, otherwise returning the value on its right.

```javascript
> var name = false;
> console.log(name && 'Anonymous');
false
> name = 'Son House';
'Son House'
> console.log(name && 'Anonymous');
Anonymous
```

[Eloquent Javscript][ej] highlights the fact that *'the expression to their right is evaluated only when necessary.'* So, for `||` if the expression on the left is `true` then, no matter what it is, the expression on the right will not be evaluated.  and for `&&` if the expression on the left if `false`, the expression on the right will not be evaluated. This is called *short-circuit evaluation*.

As an aside, my solo exhibition in 2011 was at a gallery called [*ANDOR*][andor]. Funny, huh?


[onePost]: https://bordeltabernacle.github.io/2015/08/21/node-dipping-toes.html
[twoPost]: https://bordeltabernacle.github.io/2015/08/21/python-function-arguments.html
[ej]:       http://eloquentjavascript.net/
[exercism]: http://exercism.io/
[andor]: http://www.creativeandorcultural.com/index.php/nobodys-fault-but-mine-yours-ours-mine
