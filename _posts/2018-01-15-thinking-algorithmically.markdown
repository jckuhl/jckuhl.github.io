---
author: jckuhl
comments: true
date: 2018-01-15 14:24:12+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/01/15/thinking-algorithmically/
slug: thinking-algorithmically
title: Thinking Algorithmically
wordpress_id: 57
categories:
- Algorithms
- JavaScript
- programming
---

At the heart of every program, is an algorithm.  An algorithm is a black box.  Input goes in, something happens and some output gets returned.  The trick to programming is figuring out the black box.  You're given a problem and you solve that problem by working one step at a time to get to your desired output.  Being able to do this requires that you understand the basic primitives that you're working with.  These primitives would be the language syntax, the data types, the native functions built into the language, and so on and so forth.

It's like building with LEGOs.  You want to build a airplane, so you pick the pieces, one at a time, that will deliver, as an output, a airplane.  You have your input, your LEGO pieces, and you have your output, an airplane.  The algorithm is the method in which you put the pieces together.  In a sense, writing a program itself is an algorithm.  You want to create a program that has some desired operation, and so you get your pieces, the primitives of your programming language, and you assemble them in a way that gives you your program.

Let's take a JavaScript example.  Yesterday I made a post with the following code:

```javascript
function squareDigits(num) {
    //may the code be with you
    return parseInt(num.toString().split("").map((digit) => {
        return Math.pow(digit, 2);
    }).join(""))
}
```


To illustrate my point, I'll go through, step by step, how I came up with this.

This came from a code challenge at [Codewars](https://www.codewars.com/dashboard).  The problem was as follows:  Take any number, and return a number such that every digit of the output number is the square of every digit of the input number.  If I have 123 as my input number, I'll have 149 as my output.  If I have 987 as my input, I'll have 816449 as my output.  Essentially what I'm doing is this:  9 * 2 is 81, 8 * 2 is 64 and 7 * 2 is 49, which gives us 816449.

This is not a useful function whatsoever, but it does get you to think algorithmically, to think about the logical steps that need to be taken in order to achieve a desired output.  So how do we do this?

Well, first we need to consider our primitives.  Our input is a number, an integer to be more precise.  JavaScript does not give us a means to iterate through the digits of a number and manipulate them.  Maybe there's some sort of mathematical formula you could figure out, or maybe we can go the simpler route and simply change the data type.  We can iterate through the characters in a string, so why don't we just turn it into a string?  JavaScript gives us a native `toString()` function that works for our purposes.

```javascript 
function squareDigits(num){
    //may the code be with you
    return num.toString()
}
```


So now we have a string object.  We need to iterate through each digit in our number and manipulate it.  We need to get its square.  Then we need to return a modified string that we can turn back into a number.  So we can use a for of loop.  But . . . why use a for of loop when we have a perfectly good JavaScript native method that can iterate through all the elements of an array and return a new array for us?  A method that does all of that work with less fuss than a for of loop?  We have a [map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) function for a reason!  Let's use it!  But before we can use map(), we have to turn our string into an array.

You can see here, we're breaking the problem down into step by step and considering what each step requires and how it'll eventually get us to our goal.  If we can turn our string into an array, we can then modify the contents of that array using map, and then put it back together as a number when we're finished manipulating it.

Lets make our string an array.  We can do this by calling [split("")](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split).  Split() with an empty string as an argument will return an array where every element in the array is each individual character.  Then we can call map() on it.

```javascript   
function squareDigits(num){
    //may the code be with you
    return num.toString().split("").map(

    );
}
```


Map() takes a closure that will modify every member of the array and return a new array with all the modified values.  I won't go into detail on closures, but essentially, they're functions within functions.  Because JavaScript considers functions as objects, as first-class citizens, we can pass a function into `map()`.  Or we can just implement it directly in map as an anonymous function.  In this case, keeping the closure anonymous is simpler.

I'll be using [arrow notation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) here to write our anonymous closure.  The Mozilla Developer Network has some good information on the specifics of arrow notation, but for our purposes here, it doesn't matter which anonymous function syntax you use.  I just prefer arrow notation because it's more concise.

Now that we've got our number turned into an array with every digit as an element of said array, we can solve our problem.  Our closure will be doing that work.  It'll take one parameter, which I'll call "digit" and return the square of that digit.  I'll be using the math object here, though you could also simply do "digit * digit."  With arrow notation, a parameter is placed before the arrow.  If you only have one parameter, you don't need the parenthesis, but I like to use them so I do.  I also prefer to keep a space between the opening map() parenthesis and the parameter block of my anonymous function.  The parameter is simply a label for every element in the array (like we would use in a "for of" loop) so we can refer to those elements in the body of the closure.

All our closure needs to do is return the square of the element.  Very simple.  Because JavaScript uses type inference, we don't even need to convert "digit" from a string to an int.  JavaScript will handle the type conversion for us.

```javascript
function squareDigits(num){
    //may the code be with you
    return num.toString().split("").map( (digit)=> {
        return Math.pow(digit, 2);
    });
}
```


And there we go, we're almost finished.  We have an array with all of our digits squared.  But the problem asked us to return an integer number.  Well, we turned our number into a string and then into an array.  So we can solve this problem by working backwards.  We can turn our an array back into a string, and use parseInt() to turn it back into a number.  Two simple steps.

First, we'll chain [join("")](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/join) to our map() function.  What join() does is it joins all the elements of an array into a string, passing a string as an argument.  It'll place the string we pass in directly between each element.  If we pass an empty string, there'll be nothing between each element.  And so we end up with:

```javascript   
function squareDigits(num){
    //may the code be with you
    return num.toString().split("").map( (digit)=> {
        return Math.pow(digit, 2);
    }).join("");
}
```


Then finally we have to turn the whole thing back into an integer.  We can do this with [parseInt()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/parseInt).  We'll wrap the whole thing inside parseInt and that'll get our solution.  Note that the semicolon after the join method would be moved outside of the closing parenthesis of the parseInt function.

```javascript
function squareDigits(num){
    //may the code be with you
    return parseInt(num.toString().split("").map( (digit)=> {
        return Math.pow(digit, 2);
    }).join(""));
}
```


And there you have it, our function is complete.  So you can see that by breaking the problem down into the primitives that your language includes, you can build up a script, piece by piece.  You just have to ask yourself, what LEGO bricks do I need to construct this script?  What language syntax, native functions, and data types can I manipulate to achieve my desired outcome?  If you start thinking in baby steps, you'll start to think like a programmer.

If you want further information on algorithms and problem solving, Harvard, with Professor David Malan, has produced an introductory computer science course called [CS50](https://www.edx.org/course/introduction-computer-science-harvardx-cs50x).  It is free online at edX.  There's about 20 hours worth of courses, challenging problem sets and multiple side videos as well.   It's a good course that I took over the summer.  You can also get a certificate for $90, but it's up to you to decide if it's worth it or not.  The course work is free, the certificate is not.

As a final word, let me point out that I linked to the MDN multiple times in this post.  Always refer to the documentation.  There's no shame in that.  The MDN is my bible.  It may provide clues as to what the next step in your algorithm may be.  If you're programming in a language that isn't JavaScript, there's documentation out there for you to use.  And there's also [Stack Overflow](https://stackoverflow.com/) if you still can't get your question answered.  Programming is problem solving, and problem solving can quite often require research.
