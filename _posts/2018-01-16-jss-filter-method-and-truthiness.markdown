---
author: jckuhl
comments: true
date: 2018-01-16 16:06:34+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/01/16/jss-filter-method-and-truthiness/
slug: jss-filter-method-and-truthiness
title: JS's Filter Method and Truthiness
wordpress_id: 62
categories:
- JavaScript
- programming
tags:
- Arrays
---

JavaScript has a handy little method called [filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) that will take an array, apply a conditional test on all the elements and return a new array with only the elements that pass the test.  It's a useful way of getting things out of an array that avoids messy for loops and produces more concise code.

Filter takes a closure (also known as, in this instance, a _callback_) that has three parameters, and a second argument that will be used to hold the callback's _this_ value.  This second argument is optional.  The three parameters the closure uses are the element, the index, and the array.



	
  * The element parameter references the element the filter array is currently working on.

	
  * The index parameter is the index of the current element

	
  * The array parameter is the array filter is called on.


Filter only requires that the closure has an element parameter.  The closure we use has a boolean return, it only returns true or false.  Any element that causes the closure to output true will be put in the new array that filter returns, else it'll be excluded.  Now that we have a brief overview of it, let's take a look at it in action.  It's less complicated than it sounds.

Here's an example from [FreeCodeCamp's](https://www.freecodecamp.org) coding challenges.  Here you can see a closure is passed through via arrow notation, the parameter _value_ is used to refer to the current element, and all other parameters and arguments are omitted.  This piece of code goes through all the elements of an array and returns a new array with only values that are valid or _truthy._

```javascript
function bouncer(arr) {
    return arr.filter((value) => {
        return value;
    });
}
bouncer([7, "ate", "", false, 9, 0]);
```




So before I continue, what is it that I mean by truthy?  Well, I'm sure you're aware of true and false.  These boolean values are fairly straight forward.  However, in JavaScript, as in many other languages, there's a concept of _truthy _and _falsy._  Generally an invalid value such as null or undefined or an empty string will be considered falsy.  JavaScript and Python both (Boolean(0) and bool(0) respectively) consider zero to also be falsy.  All we need to do in our bouncer function is check the _truthiness_ of a value.  If an element has a valid value, it passes the test.




[caption id="attachment_63" align="aligncenter" width="300"]![Stephen Colbert Truthiness](https://projectbreakpoint.files.wordpress.com/2018/01/truthiness.png) No, I'm not referring to Colbert's version of Truthiness.[/caption]

Our closure has a very simple return function, it only looks at the argument _value_ which refers to the current element.  This is a clever little shortcut we can use to check the truthiness of the value.  This works because the closure inside filter _only_ returns true or false.  So all we have to do is type _return value;_ and that'll be enough.  If value is a valid JavaScript value, if it is truthy or holds a value that is not null, undefined, an empty string or zero, it passes the filter and gets put in the new array.

The result of our filter is [7, "ate", 9], which I guess is a joke on FreeCodeCamp's part, but it illustrates how truthiness works as well as how filter works.

We can also, with a bit of arm twisting, use filter for string manipulation.  Filter is on the Array prototype, so it can't be called on a string directly, but it may be advantageous to do so nonetheless.

Consider the following code.  What if I wanted to remove all the e's from a string?  Why would I want to do this?  Who knows, maybe some cryptography game?  It doesn't matter, let's just go with it.  To do so, I might use the replace() function and a substring or RegEx that'll replace the e's with an empty string.  Of course, replace() will only remove the first occurance of the letter e, so we'll have to repeat the process in a do while loop:

```javascript   
function noMoreEs(string) {
    do {
        string = string.replace("e","");
    } while(string.indexOf("e") != -1);
    return string;
}
```


Filter can do all of this on one line and avoid using a loop (or rather, abstract away the loop as I'm pretty sure it uses looping under the hood.)

```javascript
function noMoreEs(string) {
    return string.split("").filter( (c)=> c != "e").join("");
}
```


It's that simple.  We turn the string into an array, call filter, and join it back together a as a string.  Inside our filter method, we have a very simple closure.  We're going to call each character in the string "c" and return c if c does not equal the letter e.  I was able to omit the return statement and keep it all in one line because arrow notation does not require a return statement for single-line instructions.

But where might I use filter in a real application?  Well, who said code challenges aren't real applications?  I kid, I know how you feel.  Checking for truthiness might be mildly useful.  Imagine you've got some JSON data from an API.  We can use .filter to go through the data and return only the objects that are valid.  Removing the letter "e", however, is a bit pointless.

We can also use filter for garbage collection.  Consider my [Circles](https://codepen.io/jkuhl/pen/NgYBro) game on [Codepen](https://codepen.io/).  In this game, all the red circles are stored as objects in an array called "baddyArray."

    
    baddyArray = baddyArray.filter( (redCircle)=> redCircle.health );


Because I'm creating objects constantly in this game, my game is consuming memory, so I need to remove objects once they're no longer useful.  By taking the dead ones out of my baddyArray, I remove all references to those objects, which allows the garbage collector to remove them.  baddyArray will only have live red circles, and it's only the live red circles that I need, so why waste memory on the dead?   . . . . That sounded harsh.

I hope this primer helped you understand how the filter method works and how it can save you a bit of time and give you more concise code.  It's a very powerful method that can do a lot for you.  Happy coding.
