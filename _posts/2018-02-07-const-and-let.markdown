---
author: jckuhl
comments: true
date: 2018-02-07 01:59:55+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/02/06/const-and-let/
slug: const-and-let
title: Const and Let
wordpress_id: 109
---

In my last post I brought up const and let for a moment, so I decided to dive deeper in that subject.  ES6 replaced the _var_ keyword almost totally.  Minus a few edge cases, the community is generally of the consensus that _var_ is dead and wholly replaced by _const_ and _let._

But what are these new keywords and how are they different than _var_?  Well to begin with, let's start with scoping.  Var was function scoped.  Wherever you declare var in a function, the variable would be available.  This turned out to be problematic, especially with for loops.

Check out the following fiddle:


<blockquote>[Let vs Var](https://jsfiddle.net/jkuhl/L5x2643p/)</blockquote>


In this example, you can see a for loop is being used to apply an event listener to eight buttons.  Click on a button, a small message appears telling you which button you clicked on.

However, with the var keyword, we have a problem.  Var is function scoped and in this case, it'll get scoped to the nearest enclosing scope, which would be the global scope.  The script will run eight times and then be stored in memory with the value of eight.  This has the effect that every time you click on a button, it'll tell you that button eight was pressed.  Doesn't matter which one you press, it could be the first, the fifth, the seventh, it'll always say eight.  This is because the value of i, in the global scope, is eight when the for loop finishes.

There are workarounds.  Using _forEach() _rather than a for loop to assign event listeners is cleaner and more functional, but this is just a band aid for unexpected behavior.

Now we have the _let_ keyword, which fixes this.  Let, and const, are block scoped.  This means they only exist in the block of instruction they are created in.  Here's an example:

```javascript
if(someConditionIsTrue) {
    let someVariable = 7;
}
```

The variable _someVariable_ only exists inside that if statement.  If we leave that block, even if we go into the else block, that variable ceases to exist.  The variable can be used by any block that that block includes, but it cannot be used by any parent block.

```javascript    
if(condition) {
//someOtherVariable is unavailable
if(condition) {
    let someOtherVariable;
    //someOtherVariable is available
    if(condition) {
            //someOtherVariable is still available
    }
} else {
    //someOtherVariable is no longer available
```

Returning to our JSFiddle example, if we change i to be a _let,_ i will be scoped only to the for loop rather than hoisted to the global scope.  This means that when the event listeners are run, their individual value of i isn't replaced by a global scoped variable, but instead retains the value of i they were given when the loop originally ran.

_Let _and _const _allow us finer control on variable scoping.  There are some edge cases where you might use var.  A discussion I read over at [reddit](https://www.reddit.com/r/javascript/comments/6i0ho4/is_there_any_reason_to_use_javascripts_var/) gave a scenario about continuing to use var in an extreme optimization case, but for the most part, I think it's safe to say that _var _is obsolete.  But give a read through those comments and look at his code.  Maybe _var_ is, as Miracle Max would put it, only _mostly_ dead.

![mostlydead.jpg](https://projectbreakpoint.files.wordpress.com/2018/02/mostlydead.jpg)

Okay, so they replaced _var_ so we could fine tune our control over scope.  But why do we have both _const _and _let_?  Why do we now have two types of variable declarations?  Well, this has everything to do with the idea of mutability vs immutability.

Mutability is the capability to change the state of a program.  This means giving a variable a new value, or putting new values in an existing array, or changing the properties of a function.  It's an antithesis of functional programming, and while I am in no way a strict functional programmer, I do believe that the paradigm should be followed more often than not.  If you want some real fun with functional programming and an immutable language, go give [Haskell](https://www.haskell.org/) a whirl for an afternoon.

Immutable is the exact opposite.  Immutable variables cannot be changed once assigned.  Immutability is desired.  You want as much of your program to be as immutable as possible.  The more immutable it is, the less likely there are errors in code.  Immutability prevents side effects.  It ensures that variables don't change unless you need them to change.

When you declare a variable using _let,_ that variable is mutable.  You can reassign it any value, and any type, that you desire.  However, if you assign a variable using _const, _which is short for _constant_, you cannot reassign that variable ever again.  In short, if you use the equals sign once, the assignment operator, on a _const_ variable, you may never use it again.

It's good practice to declare all your variables as _const_ and only change them to _let_ once you know you need to change their values.  There are a few caveats you must consider however.

Variables declared in for-loops cannot be _const_ because the value is being reassigned every iteration of the loop.

Values inside objects assigned to _const_ variables are still mutable.  If I declare an array with _const_, I can still change the values inside the array.  However, I cannot reassign the variable that stores the array itself.  The same goes for an object.  If I use _const _to declare a variable to hold an object, I can still change the properties of that object.

```javascript
const object = {
    x: 1,
    y: 2,
    str: "string",
}
```

Here I have an object stored as a _const_ variable known as "object."  I cannot reassign that variable somewhere else.  But I can change the properties in the object.  All of the following are valid:

```javascript   
object.z = 3;
object.bool = true;
object.x = 2;
delete object.y
```

But this is forbidden:

```javascript   
object = someOtherValue;
//Uncaught TypeError: Assignment to constant variable
```

The same holds true for arrays.  You can perform push and pop on arrays.  You can change the values of each element in the array.  You can even perform filter and map on constant arrays, because those methods create a new array, they do not affect the array they are call on (unless you explicitly reassign the new array over the old one which, if the array is declared as a constant, is forbidden.)  You simply cannot reassign the variable holding the array itself.

So  be aware that even if you declare objects and arrays as _const_, they still aren't wholly immutable_. _

Get into the habit of using _let_ and _const.  _They're here to stay.

Happy coding.
