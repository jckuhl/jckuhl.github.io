---
author: jckuhl
comments: true
date: 2018-05-06 00:51:22+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/05/05/javascript-values-vs-references/
slug: javascript-values-vs-references
title: 'JavaScript: Values vs References'
wordpress_id: 142
---

JavaScript can be a quirky language sometimes that can lead to very strange seeming effects.  Take the following code for example:

```javascript   
let a = [1,2,3];
let b = [1,2,3];
let c = "1,2,3";

a == c;  // true
b == c;  // true
a == b;  // false
```

Well . . . this makes no sense at all.  How can `a` and `b` both be the same array and equal to `c` but not to each other?

That code makes no sense . . . if you don't understand what's going on under the hood.  `a` and `b` appear to be equal, but they are not.  In JavaScript, arrays are objects.  They are objects with array methods on their prototype, where the key/value pairs are built like this:

```javascript
    Array = {
        "0": "First Element",
        "1": "Second Element"
        // And so on ...
    }
```


Arrays are objects, therefore they are specific values held in memory.  The variable that holds the array is not actually holding the value of the array, but a reference to the value of the array.  JavaScript does not technically have pointers, but under the hood a variable that holds an object (and therefore an array) is a pointer to the memory address that the value of that object lives at.  This memory address is unique.  Even though `a` and `b` hold the same values, those values are held in different places in memory, therefore `a` and `b` are not equal.

So why are `a` and `c` or `b` and `c` equal?  Well, we used `==` rather than `===` which forced type coercion.  Both variables are coerced into primitive values where the types are the same.   We aren't dealing with objects after the type coercion, we're dealing with the values directly, therefore the values are considered equal, according to the loose equality operator.

Here's another example of values vs reference:

```javascript 
let a = {};
let b = a;
b.prop = 2
```


What is the value of `a`?

```javascript
{ prop: 2 }
```

Why?  Because as I said, variables that are assigned to objects are references.  Whatever object `a` points to, `b` also points to.  Whatever you do to `a`, you'll do to `b` and vice versa.  `a` and `b` are both pointers, they contain the same memory address of the same object in memory.

JavaScript has pointers, they're just more subtle and under the hood, unlike the pointers in C++ or other lower level languages.  Fortunately, as it is JavaScript, we don't really have to handle them that much.  We really just need to understand the difference between values and references and what quirks these differences can make.

Happy Coding
