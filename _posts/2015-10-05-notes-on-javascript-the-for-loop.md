---
layout: post
title: 'Notes on JavaScript: The for loop'
date: '2015-10-05 11:16'
tags: javascript

---

Being used to the simplicity of the Python `for` loop, it took me a little while to get used to the JavaScript `for` loop syntax.

<!--more-->

The `for` loop in Python is basically just the statement `for x in y do z`, where `y` is an iterable such as a list or a string, which is fairly self-explanatory.  For me, the JavaScript `for` loop didn't seem as transparent when I first started using it.

Here is a simple example:

```javascript
> for (var i = 0; i < 10; i++) {
...     console.log(i);
... }
0
1
2
3
4
5
6
7
8
9
>
```

The `for` loop is made up of 4 statements:

```javascript
for ([initiating statement]; [conditional statement]; [end of loop statement]) {
    [execution statement];
}
```

- `[initiating statement]` : This is executed before the loop starts, usually initiating a counter variable that is used as a kind of measure for how long the loop has been running.
- `[conditional statement]` : This describes the condition that controls the loop, stating under what circumstances it should continue to execute.
- `[end of loop statement]` : This is executed at the end of each loop, usually incrementing the counter initialised at the beginning of the loop.
- `[execution statment]` : This is the code that should be executed on each iteration of the loop.

All these statements are optional.  For example the `[initiating statement]` may be previously set outside of the loop, or it can even be used to set multiple initiating variables. The other statements can also take into consideration multiple values.

```javascript
> for (i = 0, j = 1, k = 2; i + j + k < 30; i++, j++, k++) {
...     console.log('i: ' + i + ' j: ' + j + ' k: ' +k)
... }
i: 0 j: 1 k: 2
i: 1 j: 2 k: 3
i: 2 j: 3 k: 4
i: 3 j: 4 k: 5
i: 4 j: 5 k: 6
i: 5 j: 6 k: 7
i: 6 j: 7 k: 8
i: 7 j: 8 k: 9
i: 8 j: 9 k: 10
>
```

Funnily enough it was only later that I found out there is actually a `for ... in` loop in JavaScript similar to that in Python.  Though it doesn't behave exactly the same.

```javascript
> var blues = ["Son House", "Blind Willie Johnson", "Elizabeth Cotten"]
undefined
> blues
[ 'Son House', 'Blind Willie Johnson', 'Elizabeth Cotten' ]
> for (var singer in blues) {
... console.log(singer)
... }
0
1
2
```

This `for ... in` loop iterates over the properties of an object, iterating over the index if a property is not explicitly called.  So in this case we have to iterate over them like so...

```javascript
> for (var singer in blues) {
... console.log(blues[singer]);
... }
Son House
Blind Willie Johnson
Elizabeth Cotten
```

Although [ECMAScript 2015 introduces][ecma6] a new type of `for` loop, the `for ... of` loop...

```javascript
> for (var singer of blues) {
... console.log(singer);
... }
Son House
Blind Willie Johnson
Elizabeth Cotten
```

My understanding is that the first `for` method described here is the most reliable for working in all versions of JavaScript.  [W3Schools][w3s] & [MDN][mdn] are both useful resources for understanding the `for` loop beyond this basic overview.

[ecma6]: https://strongloop.com/strongblog/introduction-to-es6-iterators/
[w3s]: http://www.w3schools.com/js/js_loop_for.asp
[mdn]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for
