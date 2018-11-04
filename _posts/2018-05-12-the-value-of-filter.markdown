---
author: jckuhl
comments: true
date: 2018-05-12 23:52:00+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/05/12/the-value-of-filter/
slug: the-value-of-filter
title: The value of filter()
wordpress_id: 145
---

`Array.prototype.filter()` is a method that can come in handy in many ways.  The other day I was working on a project and I needed to use a list of sliders to set some values.  I needed to match the slider to the value I needed to change.   I created an array of sliders and an array of matching outputs.  But how can pass the value of the right slider to the right output?

Well, the setup is pretty straightforward.  I run the sliders through a `forEach()` to apply event listeners, with which I can change the outputs, which are just HTML5 `<output>` elements.

```javascript   
const sliders = Array.from(document.querySelectorAll('input[type="range"]'));
const outputs = Array.from(document.querySelectorAll('output'));

sliders.forEach( (slider)=> slider.addEventListener('change', ()=> {
    setSliderValue(event.target.id, event.target.value);
}));
```


A slider is really just an input of type "range."  I'm using `document.querySelectorAll` to grab my HTML elements and using `Array.from()`, which is a static Array method, to convert the nodelists to arrays because I know I'll be calling filter.

When I make a change to a slider, it'll create an `event` object, and on that object, will be the slider I changed, as the target.  From the target object, I can grab it's id and it's value, and pass it into `setSliderValue()` which will allow our outputs to display the value of our slider.

But how do we match the slider id with the output?  Well traditionally, we might use a for loop, or reach for `forEach()` like I usually do, and find the matching output name:

```javascript 
function setSliderValue(id, value) {
    outputs.forEach( (output)=> {
        if(output.name === id) {
            output.value = value;
        }
    });
}

// or:

function setSliderValue(id, value) {
    for(let output of outputs) {
        if(output.name === id) {
            output.value = value;
        }
    }
}
```


And it would work too, right?  Sure.  But filter can make it a little simpler.  Filter will return an array with the matching elements.  Because each output and each slider have unique ids and names, we know that filter will return an array with only one item.

Let's use filter:

```javascript
function setSliderValue(id, value) {
    outputs.filter( (output)=> output.name === id)[0].value = value;
}
```


We've now got it all on one line.  We use filter to find the element that matches.  Because filter returns an array, we use the array accessor to grab that element, and then we set the value.

Simple right?  After all what are you doing when you're matching?  You're filtering out the things that don't match.  If you can use the word "filter" to describe what you're trying to accomplish, it's very likely you can use the filter method to make it happen.

In the end, it's all stylist choices.

Happy coding

