---
author: jckuhl
comments: true
date: 2018-02-13 14:25:23+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/02/13/this-and-the-new-operator/
slug: this-and-the-new-operator
title: this, And The new Operator
wordpress_id: 117
categories:
- JavaScript
tags:
- object-oriented
---

In JavaScript, when you initialize an object from a class or a constructor function, there's a mysterious little keyword, _new_, that we have to use.  But why?  Many of us, myself included, simply used _new_ because we were told to.  It was just an idiosyncrasy of the language, an artifact of a time when the designers of JavaScript wanted JS to look like Java (which it totally does guys!)

[caption id="attachment_118" align="aligncenter" width="245"]![rdjeyeroll](https://projectbreakpoint.files.wordpress.com/2018/02/rdjeyeroll.gif) JavaScript looks _nothing_ like Java.[/caption]

But . . . why though?  Why do we use this weird little operator?  It has a lot to do with how the _this_ keyword behaves.  We need _new_ to force the JavaScript engine to treat the function as an object constructor, and not as a function, and to have _this_ reference the right object.   The new class syntax in JavaScript will enforce the use of the _new _keyword and throw an error if you forget it.  However the constructor function syntax will happily let you omit the _new_ keyword . . . and give you an undefined value.  The keyword _this_ is behind it, as is the lack of a return value.

Why?  Let me show you by example.  Imagine we have the following constructor function:

```javascript 
function Person(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
}
```

And we want to make a new object:

```javascript   
const Jim = Person("Jim", "Doe");
```

What went wrong here?  Open your browser's console and type it in.  Jim will have the value of _undefined_.  Why?  Because we're not creating an object.  We're invoking the Person function, which has no return statement.  Because it returns nothing, it returns _undefined.  _Therefore, Jim is assigned the value of undefined.

But what happened to the two properties in the function?  When you use _this_ in a function declaration, _this _points to the _window_ object, because _window_ is the enclosing object holding the function declaration.  If you type "window" into the console and look through it's properties, you'll eventually find "firstName" and "lastName" set to "Jim" and "Doe" respectively.  The function set our properties, but put them on the wrong object.

So what does _new_ do?

```javascript    
const John = new Person("John", "Doe");
```

The _new_ operator sets up a new empty object called _John_ and tells the JavaScript engine to set _this_ to the John object.  Then as it invokes the constructor function, it sets the properties of the John object, because _this_ is now pointing to John and not the window.

The _new _operator behaves the same way whether you're using a class or a constructor function, the only difference being that with a class, omission of the keyword will result in an error, whereas with a constructor, omission of the keyword will result in _undefined_ and pollution of the _window_ object.

Hopefully by understanding why you need _new_ you'll have a better understanding of how JavaScript objects work.

Happy Coding
