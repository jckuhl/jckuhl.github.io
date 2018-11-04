---
author: jckuhl
comments: true
date: 2018-03-27 12:39:45+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/03/27/the-old-for-loop-is-dead/
slug: the-old-for-loop-is-dead
title: The Old For Loop Is Dead
wordpress_id: 125
categories:
- JavaScript
tags:
- es6
---

JavaScript, taking inspiration from both C and Java, has always included the old C-style for loop.  It's the looping structure that I like to also call the "classical" for loop, a structure that isn't even found in some modern languages like Python.

And there's a good reason for that.  It's time to put the classical for loop to bed.

This, by the way, is what I'm talking about:

```javascript  
for(let i = 0; i < array.length; i++) . . .
```

This unholy monstrosity right here.  With ES6, there really isn't much reason remaining to use it.  JavaScript gives us two iterating functions we can use on arrays and array-like objects.  The .map() and the .forEach() methods are on the Array prototype and forEach has been implemented for NodeList objects (but not for HTMLCollection objects!)  In the event that you need to use either .map() or .forEach(), Array.from() will convert that iterable object into an array.

When it comes to arrays, I see little reason to use the classic for loop to iterate over them.  If you come across a situation where iterating over elements in an array via .forEach() or .map() come along, there's also the ES6 _for of_ structure.

```javascript    
for(let elem of array) . . .
```

This structure is superior for two reasons.  First, it's more readable.  You don't have all these arcane symbols.  Instead, you have English.  **For** every **elem**ent **of** this **array**, do this.  Second, it is less error prone.  The _for of_ loop cannot produce an infinite loop.  It cannot produce an out of bounds error for trying to access too far into an array.  You don't have to worry about making sure your loop is within the size of your array, because the loop will only iterate for each element in an array.

But what if I wanted an index?  Well, ES6 destructuring can work for us here.

```javascript  
const array = [1,2,3];
for(let [index, value] of array.entries()) {
    console.log(index, value);
}

// 0 1
// 1 2
// 2 3
```

This will allow us to loop over the value of an array, and also access it's index.  This can be a bit ugly, especially if destructuring is a new concept to some developers, but unlike the classic for loop, it is not as error prone because it can't go out of bounds.  You won't get _undefined_ or _NaN_ values, at least, not because of the for loop structure.

Of course, [.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) and [.forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) provide you the ability to access the index by using it as a parameter in the callback function.

```javascript   
myArray.map( (currentValue, index, myArray)=> { . . . });
```

But what if I want to loop over the items in an object?  Well, JavaScript gives us that ability with the _for in_ structure.

```javascript  
for(let key of obj) {
    console.log(key); //print out the keys of the object
    console.log(obj[key]); //print out the values of the objects
}
```


It's simple and clean.  With all the ways of iterating through objects or arrays, I don't see a reason to stick with the classic for loop.  It's a messy structure that can be error prone, whereas the _for in, for of_ loops are cleaner and easier to read.  The functional options we're given through .forEach() and .map() are also available, and using those methods lead to more concise and readable code.

But what if I'm not working on an array or an object?  What if I need to repeat some instructions X amount of times?  Well . . . here is where we still need the classic for loop.  I mean, you can always do this with a regular while loop, but it'd take a few extra lines.  The classic for loop here shines because it's all in one concise line.  While, I still argue that it's ugly, it's less ugly than a while loop:

```javascript   
for(let i = 0; i < 10; i++) . . .

let i = 0;
while(i < 10) {
    ...
    i++
}
```


Unfortunate, we're still using the old for loop because JavaScript does not have range objects, like python does.  Look how crisp a for loop in Python is!

```python    
for i in range(10):
```

But alas, we who dally in JavaScript are made to suffer.  Okay, that was melodramatic, but anyways.  We could emulate this using lodash's [_.range](https://lodash.com/docs#range) function, but I hardly think that bringing in a library, just to avoid the classic for loop, is necessary, unless you're already using the [lodash](https://lodash.com/) library anyways.

Ultimately, the classic for loop is going out of phase.  There are cleaner and more function-oriented ways to accomplish the same tasks as the classic for loop, especially when it concerns arrays and objects.  There are still uses for the old for loop, but I argue this, if range objects ever come to JavaScript, the old for loop is dead.

Happy coding.
