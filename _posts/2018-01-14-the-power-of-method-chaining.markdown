---
author: jckuhl
comments: true
date: 2018-01-14 02:34:12+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/01/14/the-power-of-method-chaining/
slug: the-power-of-method-chaining
title: The Power of Method Chaining
wordpress_id: 30
categories:
- '#100DaysOfCode'
- JavaScript
---

JavaScript can be a feisty little programming language with its little quirks and tricks, and over the past few weeks I've learned quite a bit about it.  One neat little trick is method chaining, which is valuable to both vanilla JavaScript and jQuery.  Method chaining is simple in theory, but it's not always obvious when you can do it.  Essentially, if the method your using returns an object, you can chain methods on methods with the dot notation.  It's a lot less complicated than it sounds.  Consider the following code:

```javascript  
function LetterCapitalize(str) {
    let sentence = str.split(" ");
    sentence = sentence.map((word)=> {
        first = word.slice(0,1).toUpperCase();
        word = first + word.slice(1);
        return word;
    });
    str = sentence.join(" ");
    return str;
}
```

Ignoring the fact that I forgot to declare the variable "first," this code is a lot more verbose than it needs to be.  It's a snippet of code I used for one of [Coderbyte's](https://coderbyte.com/challenges) challenges.  This code can be compressed into five lines of code with method chaining.  On top of that, we can also eliminate all of the variables declared in the code.

Lets just take this step by step.  First, we split the string into an array by separating each word by a space.  Well, because the split method returns an array, we don't need to stuff the array in a variable to call map, we can just call map() directly, so we get:

```javascript
function LetterCapitalize(str) {
    let sentence = str.split(" ").map((word)=> {
        first = word.slice(0,1).toUpperCase();
        word = first + word.slice(1);
        return word;
    });
    str = sentence.join(" ");
    return str;
}
```


But we're not done.  Let's look inside the closure we use in `map()`.  We're using `slice()` to grab the first letter of a word and capitalizing it, saving that into a variable and then using slice to get all of the letters after and including the second letter and concatenating that to the first capitalized letter.  We can get rid of the first variable altogether, and put this all on one return statement like so:

```javascript
function LetterCapitalize(str) {
    let sentence = str.split(" ").map((word)=> {
        return word.slice(0,1).toUpperCase() + word.slice(1);
    });
    str = sentence.join(" ");
    return str;
}
```


That cleaned up the closure, but we can do more.  `.map()` returns an array.  Since we need to return a string object we can just chain that `.join()` to the `.map()` method.

```javascript
function LetterCapitalize(str) {
    let sentence = str.split(" ").map((word)=> {
        return word.slice(0,1).toUpperCase() + word.slice(1);
    }).join(" ");
    return str;
}
```


Of course, now we're not returning anything useful because the variable str itself isn't being modified.  But who cares.  We have one more step before we're complete.  It'll clean everything up, and get rid of that sentence variable.  All we have to do is move the return to where `sentence` is declared and fix the syntax by eliminating the now useless assignment operator.  We do that, and we're done:

```javascript 
function LetterCapitalize(str) {
    return str.split(" ").map((word)=> {
        return word.slice(0,1).toUpperCase() + word.slice(1);
    }).join(" ");
}
```


Now we have a much more concise piece of code that does the same thing the first one did but without any variables (aside from the method argument) and in only five lines of code.  Method chaining is a powerful tool, as you can see with some refactoring, it cleans up the code nicely.

Here's how it works:  The first line in the function is going to split() a string by the spaces in the string and creates an array.  This array contains every word in the sentence in the string passed into the function. Because split() creates an array, we can chain `map()` to it.  `Map()` will pass every element of the array (passed into the closure as the argument "word") into the closure and modify it.  Inside the `map()` method closure, we use slice to get the first letter of the world.  `Slice()` returns a string, so you can chain toUpperCase() to it.  Then we can concatenate all the remaining letters of the word to our uppercased letter.  Outside the `map()` method, because `map()` returns an array, we can turn it back into a string by chaining `join()` to it.  Finally, we return the string, now with all the first letters capitalized.

Furthermore, the closure in map is simple enough to put on one return statement, as is the return statement in the function itself.  I also prefer arrow-notation whenever I can against the older notation for anonymous functions.  It looks cooler and it's shorter.  That's literally the reason I prefer it.  I am aware of the technical difference, occasionally, when dealing with the `this` key word, you'll have to use the older `function () { `notation but that's an article for another time.

When refactoring your code, always look for ways to chain your methods together.  It makes your code cleaner and more concise and allows you to avoid unnecessary variables.  And it's just prettier.
