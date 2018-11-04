---
author: jckuhl
comments: true
date: 2018-02-04 04:36:10+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/02/03/type-coercion-is-weird/
slug: type-coercion-is-weird
title: Type Coercion Is Weird
wordpress_id: 99
categories:
- JavaScript
- Web Development
tags:
- udemy
- weirdness
---

Common sense dictates that the following statement is true:


<blockquote>3 > 2 > 1</blockquote>


It also dictates that the following statement is false:


<blockquote>3 < 2 < 1</blockquote>


I mean, after all, 3 is greater than 2, and 2 is greater than 1.  Furthermore, 3 is not less than 2 and 2 is not less than 1.  We all learned this like . . . way back when we first learned to count.

But JavaScript is weird.  And if you want to learn just how weird it is, take the Udemy course [JavaScript, Understanding the Weird Parts](https://www.udemy.com/understand-javascript).  The bit I'm talking about is from Lecture 25, and granted, I'm mostly writing this for my own benefit.  It's useful to explain to yourself (and others who might also read along) the lessons you learn from others.  It helps me internalize things, and I found this particular part of the course interesting.

Let's take a look as to why this counterintuitive behavior happens.  The reasoning behind it is that JavaScript is not a human.  It does not read the greater than or less than symbols the same way we do.  Instead, it treats each operator as its own function.  They are in fact "infix" functions.  The values on the left and the right are the parameters, and this function returns _true_ or _false_ depending on those values.   Furthermore, it reads them left to right, resolving the first function before moving to the next.  To illustrate, let's put it more familiar function syntax:

```javascript
function greaterThan(x, y) {
    return x > y;
}

greaterThan(1, greaterThan(3, 2));
```


This is how 3 > 2 > 1 would look in a regular function declaration and invocation.  And lets just see what happens when it gets evaluated.  First, the greaterThan() function in the second parameter gets evaluated.  3 is of course greater than 2, so we get true.

```javascript
    greaterThan(1, true);
```

And here is where it gets weird.  True will end up being coerced into the number 1.  1 is not greater than 1.  Thus the expression ultimately returns _false._

Therefore, according to JavaScript, 3 > 2 > 1 is false.

We can do the same with the other example:

```javascript   
function lessThan(x, y) {
    return x < y;
}
lessThan(1, lessThan(3, 2));
//is equivalent to:
lessThan(1, false);
//is equivalent to:
lessThan(1, 0)
//Which returns:
true;
```


To look at it another way, using the operator syntax, we can resolve it by grouping each operator function together and moving from left to right.

```javascript
//Here's the expression
3 > 2 > 1;
//Grouping the first function
(3 > 2) > 1;
//The parameters are 3 and 2.  3 is greater than 2, this is true
true > 1;
//True is coerced into 1
1 > 1;
//This is false.
false;

//Again, with less than
3 < 2 < 1;
(3 < 2) < 1;
false < 1;
0 < 1;
true;
```


JavaScript has many strange little quirks hidden away in its bowels and type coercion is one of those things.  I highly suggest you take [Understanding the Weird Parts](https://www.udemy.com/understand-javascript) because there's far more to the weirdness of JavaScript than what I have explained.   Understanding how JavaScript works at a fundamental level, understanding type coercion, lexical scoping, execution contexts and all the other nitty gritty subjects is very important to writing good code.  These are details that people often wave away, but it's better to know and understand how the JavaScript engine runs through your code.  You'll develop more efficient code with fewer bugs and fewer behaviors you don't understand.

Happy Coding.
