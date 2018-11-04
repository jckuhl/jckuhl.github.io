---
author: jckuhl
comments: true
date: 2018-05-03 20:11:01+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/05/03/using-reduce-on-arrays-of-objects/
slug: using-reduce-on-arrays-of-objects
title: Using Reduce on Arrays of Objects
wordpress_id: 140
categories:
- JavaScript
- programming
tags:
- Arrays
- loops
- Objects
---

I encountered the following problem the other day.  I had a number of objects held in an array and I wanted the sum of one of the properties of each object.  Getting the sum out of an array is very simple, using `.reduce()`, provided that the values are simple primitives like strings or numbers.

```javascript  
[1,2,3].reduce( (x, y)=> x + y);
//Returns 6
```


It isn't so simple when those elements are objects.

Let me set up the real world example to make it a bit clearer what I'm talking about.  I made an application for a [WAM Sheet](https://github.com/jckuhl/WAM-Sheet) using Electron and Vue.  A WAM Sheet is a form used by cargo handlers to determine the center of balance for a vehicle or a trailer to be loaded onto an aircraft.  The calculation requires that you take the weight of each axle, and multiply that by distance from the forward edge of the vehicle to get the axle's moment.  You then have to add up all the weights and add up all the moments and then divide the total weight by the total moment to get the center of balance, which is a distance from the forward edge where the vehicle is balanced.

In my app, I store all the axle information in an object, and every axle is in an array.  I have the ability to add or remove axles (to a minimum of two axles) as I need, so my app can handle anything from a sedan to a semi truck, or technically any amount of axles.

The basic schema for the object is this:

```javascript 
{
    weight: 0,
    arm: 0,
    moment: 0
}
```


The weight is how much an axle weighs, the arm is the distance from the forward edge, and the moment is a computed property, of weight times arm.

To get the center of balance, I need to add up all the weights and all the calculated moments.  How might I do this?

Well, the first method that comes to mind is to just use a `for...of` loop.  I find loops to be, in general, less succinct and more complex.  But I should be able to use `reduce()` right?  No.  Not as is anyways.  The problem here is that there's no way for me to get at the internal properties of the object.  The parameters that the callback function in `reduce()` are the accumulator and the current value.  There's no way to let the method know which of the three object properties I want to reduce.

However, what we _can_ do is make a temporary array of all of the weights, and then another temporary array of all of the moments, using `map()` and then we can simply chain `reduce()` onto that.

I can grab my `total` object that I defined in my Vue root component and put the calculations in that:

```javascript
this.total.weight = axles.map((axle)=> axle.weight)
                            .reduce((x,y)=> x + y);
this.total.moment = axles.map((axle)=> axle.moment)
                            .reduce((x,y)=> x + y);
```

_Note that in Github, I'm also passing those values through parseInt because they're input as strings.  For simplicity sake and formatting, I've removed the parseInt._

By chaining higher order functions together, we're able to pull the data we need out of their objects and do our calculations.  Now that I've got the weight and moment stored in my `total` object, I can go ahead and find the center of balance that I'm looking for.

`reduce()` is a very powerful tool to use, but it has its shortcomings.  Being able to work with other higher order functions like `map()` can help you get around those shortcomings.

Of course, if you're worried about optimization, you might want to think about a `for...of` loop.  A `for...of` loop can do this in one loop, while each map method and each reduce method has their own internal looping mechanism.

The alternative would look something like this:

```javascript
    let totalWeight = 0;
    let totalMoment = 0;
    for(let axle of this.axles) {
        totalWeight += axle.weight;
        totalMoment += axle.moment;
    }
    this.total.weight = totalWeight;
    this.total.moment = totalMoment;
```


Here the for loop is less simple and less succinct.  I have to declare new variables to hold the values as I sum them, because I can't throw them onto my Vue app until the loop is complete.  Then I have to assign these new variables to the `total` object.  I get the same results with messier code.  Although, some might argue that by chaining map and reduce together, I'm putting too many characters in one line.

The for loop has less runtime complexity, O(n) vs O(4n), because you loop only once over the arrays, whereas the method I used with map and reduce, loops four times (once for each map and reduce).  If runtime is an issue for you, I'd go with the loop.  If you prefer functional paradigms and less verbose code, and runtime isn't an issue (it comes down to a few milliseconds and it's meaningless for such a simple app) then go with the higher order functions.  There's a place for either method when you code.

In the end, it's all about your coding style and your coding needs.

Happy Coding.
