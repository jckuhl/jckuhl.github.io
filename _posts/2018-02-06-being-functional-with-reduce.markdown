---
author: jckuhl
comments: true
date: 2018-02-06 16:42:09+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/02/06/being-functional-with-reduce/
slug: being-functional-with-reduce
title: Being Functional with Reduce()
wordpress_id: 107
categories:
- JavaScript
- programming
tags:
- es6
---

The ES6 Standard brought us a new method we can call on array-like objects that turns them into arrays.  This is the [Array.prototype.from()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from) method.  Now this isn't to say that prior to ES6, we had no way of accomplishing this, except that it was more complicated and less intuitive.  Now that ES6 has been out a few years and most browsers have updated to the new standard, there should be no fear for using the from() method.

With this method, it is very easy for me to refactor my average() function from the post I made yesterday.  I can call from() on the arguments object of a function and turn it into an array.  This then allows me to utilize all of the Array prototype methods on my function's arguments.

```javascript
Array.from(arguments)
```


This in turn gives me access to [Array.prototype.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) which I can then use to calculate the sum of my arguments.  Reduce is a fairly simple method.  It takes a callback function as an argument and that function takes two parameters, an accumulator and a currentValue.  Now, you don't have to call those parameters by those names, but, based on their position in the parameter block, those roles will be assigned.  The third parameter would be the current index, and the fourth would be the array that reduce is called upon.  For this example, we don't need those last two parameters.

What reduce() does is that it takes the currentValue of the element it's currently on, does something to it, and puts it into the accumulator.  If I had

```javascript
reduce( (accumulator, currentValue)=> accumulator * currentValue);
```

That would have the effect of multiplying every value with each other consecutively in the array.  Reduce will ultimately always return a single value out of an array.  Thus, it becomes very useful for my average() function.

All I need to do is turn the arguments object into an array, and then use reduce to sum all the values together.  We can do this all in one line via method chaining:

```javascript
function average() {
    const sum = Array.from(arguments).reduce( (x, y)=> x + y);
    return sum / arguments.length;
}
```


We then simply need to divide sum by the number of arguments we have.  I elected to keep that division on its own line because otherwise the first line would start to get really long and a bit hard to read and reason about.

And there you have it, by using reduce, I _reduced_ the lines of code in my function (get it?  It's funny because I used the word reduce and the method is called . . . never mind.)  This makes your code fall in line with the functional programming paradigm and allows the code to be cleaner.

Ultimately, wherever you see a for loop, always think to yourself, can this be done with _reduce, __map, _or_ forEach_?  If so, consider using those methods instead.

Note that the arguments object does not have access to the Array.prototype methods, but it does have its own .length property and a _forEach_ property.  If you're using those methods, you do not need to convert it to an array.

One final thing, I just want to make a comment on something unrelated.  I used _const_ to declare my sum variable._  _Always use _const._  Rule of thumb, if you're not going to assign a new value to a variable, then declare it with _const_ every time.  In fact, declare all your variables with _const_ and change them later to _let_ if they need to be mutated.  This makes sure the compiler screams at you if you accidently try to overwrite a value that should not be overwritten.

Happy coding.
