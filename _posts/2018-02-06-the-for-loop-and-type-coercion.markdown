---
author: jckuhl
comments: true
date: 2018-02-06 05:02:25+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/02/06/the-for-loop-and-type-coercion/
slug: the-for-loop-and-type-coercion
title: The For Loop and Type Coercion
wordpress_id: 105
categories:
- JavaScript
- programming
tags:
- Arrays
- es6
- for loops
- loops
- weirdness
---

JavaScript has three variants of the for loop.  The first is the classical C-syntax for loop that is constructed with an initializer, a conditional and an incrementer.  Anyone from C or C++ recognizes it as:

```javascript
for(let i =0; i < 10; i++) { . . .
```

But with ES6, JavaScript gives us two new for loops, the _[for of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)_ and the _[for in](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in)_ loops.  These loops are very similar, yet different, than Python's _for in_ loop and both need to be used with care, because they are also very different from each other.

Consider the following example:  A while back, while I was going through more basic JavaScript, I wanted to write a function that took an indefinite amount of numbers as parameters and returned the average of those numbers.  If we want to write a function like this, we'll need to use the [arguments](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) object.  Arguments is an object that holds all parameters passed into a function.  It is iterable like an array, but it is not an array and only has access to the .length property and none of the Array.prototype methods.   I came up with the following script:

```javascript   
function average() {
    let sum = 0;
    for (let i in arguments) {
        sum += i;
    }
    return sum / arguments.length;
}

average(1,2,3);
```

I put this together ran it . . . and I got the number 4.  This made no sense, until I looked into it further.  Why would it produce a 4?  The reason for this error is in part due to me using _for in_ when I should have been using _for of_, and due to JavaScript's type coercion.

You see, _for of _will iterate through all of the values in an iterable object, which is precisely what the arguments object is.  But _for in_ will iterate through all the _enumerable _properties of an object.  And even though the arguments object isn't exactly an array, it behaves like one, for the most part.  So when I iterate through the arguments of the function call _average(1,2,3)_ using _for in_, I'm iterating over the object keys.  Those keys are 0, 1, and 2.

This is going to cause some strange behavior with type coercion, because the keys of any object, including (under the hood) the keys of an array, are strings.  If you're skeptical, right click anywhere on the screen, click on "inspect" and move to the console tab.  Enter the following script:

```javascript
let array = [1,2,3];
let result = 0;
for (let key in array) {
        result += key;
}
console.log(result);
```

You'll see the string "0012" pop out.  What happens here is the same thing that happens in my broken average function.  The JavaScript compiler sees the number zero (the initial value of the sum variable) being added to the string value of "0" (the key of the first element in the arguments object) and coerces the number to a string.  Then as it goes through the rest of the loop, it's simple string concatenation.  In the return statement, we end up with "0012" divided by arguments.length, which is three.  This coerces "0012" back into a number, 12, and then does division.  So we get the result of four.

JavaScript is weird.

This of course would all be avoided had I used the right for loop.  The _for of_ loop would be appropriate here.  You'll want the _for of_ loop for the arguments object or for any array.  If I change my code like so:

```javascript   
function average() {
    let sum = 0;
    for (let i of arguments) {
        sum += i;
    }
    return sum / arguments.length;
}

average(1,2,3);
```

Then the right value pops out.   This function returns a two.  It's important to be aware of the differences between the two loop structures to avoid these sorts of issues.  _For of_ iterates through the values of an iterable object, such as the arguments object, an array or a Nodelist.

_For in_ is for objects.  You can use _for in_ to iterate through the enumerable properties of an object.  For example, if I have the following object literal:

```javascript    
const object = {
    a: 1, b: 2, c: 3,
};
```

I can use _for in_ to iterate through the properties in the object.

```javascript   
string = "";
for(let property in object) {
    string += property + " ";
}
console.log(string.trim());
```

This would log out

```javascript  
"a b c"
```

And to get at the values, all we'd need to do is use bracket notation.

```javascript    
string = "";
for(let property in object) {
    string += object[property] + " ";
}
console.log(string.trim());
```

This would log out

```javascript    
"1 2 3"
```

In short, use _for in_ to iterate through the properties of an object, and use _for of_ to iterate through the values of an array.  Note that you cannot use _for of_ on an object because an object is not iterable.

I hope this cleared up the difference between _for of _and _for in_ for you, while giving you another taste of JavaScript's type coercion weirdness.  Be careful when you use these and always be sure to use the right one for your program.

Happy Coding
