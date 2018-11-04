---
author: jckuhl
comments: true
date: 2018-01-28 02:30:26+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/01/27/data-attributes-dataset-and-javascript/
slug: data-attributes-dataset-and-javascript
title: Data attributes, Dataset, and JavaScript
wordpress_id: 93
categories:
- HTML
- JavaScript
- Web Development
tags:
- '#100DaysOfCode'
- DOM
- html5
- webdev
---

HTML5 came with a number of new features.  Among those features is the [data- attribute](https://developer.mozilla.org/en-US/docs/Learn/HTML/Howto/Use_data_attributes).  The data- attribute is a custom attribute developers can add to whichever HTML tag they want.  This is a feature that can be incredibly useful.

Setting up a data- attribute is simple.  You name it whatever you want, with a prefix of "data-%".  Replace the % with anything.  So if I wanted to have an attribute of "tag" for whatever reason, I could add "data-tag" as an attribute to my tag.  You can make as many of these data- attributes as you want to any tag, and assign them any value.

This comes in handy because you can use these data- attributes to identify elements, or have them hold some information.  You can also select them with CSS selectors, using the attribute selector (example: div[data-tag="some value"].)  But using them to store variables is far more exciting than yet another redundant (but convenient) way of selecting elements.

Suppose I'm building a portfolio site to pump up my web development skills.  I've got a section of my page where I display some of my work in a gallery and a list of what frameworks or libraries I used to make those things.  I have all of these projects arranged in individual div tags.I want to make this pop out a bit more.  I want to provide a list of libraries and let the user chose an item on that list.  For whatever the user clicks on, the appropriate project is highlighted.  So if I click on "jQuery", my projects that used the jQuery library is highlighted.

Using the data- attribute, this becomes really simple to pull off.  First, we set up the data- attribute in the HTML.  I decided to call my data- attribute "data-tech," because it refers to web development technologies.  I'll apply these tags to my list, where each <li> contains an image of the various logos for the web dev technologies.

```html 
<ul class="tech-picker">
    <li><img data-tech="html5" src="imgs/logos/html5-logo-sm.png"></li>
    <li><img data-tech="css3" src="imgs/logos/css3-logo-sm.png"></li>
    <li><img data-tech="javascript" src="imgs/logos/js-logo-sm.png"></li>
    <li><img data-tech="sass" src="imgs/logos/sass-logo-sm.png"></li>
    <li><img data-tech="jquery" src="imgs/logos/jquery-logo-sm.png"></li>
    <li><img data-tech="react" src="imgs/logos/react-logo-sm.png"></li>
    <li><img data-tech="angular" src="imgs/logos/angular-logo-sm.png"></li>
</ul>
```


I simply need give my custom data-tech attribute the appropriate string value.  Next, we need to set up the divs.  Here's a div for a project that uses React and Sass:

```html 
<div class="flex-gallery-img" data-tech="html5 css3 javascript sass react">
```

Every div with the class "flex-gallery-img" is a flexbox div that contains all the related information each individual project on my portfolio, one project per div.  Here I've set the data-tech attribute with multiple values.  This is perfectly legal.  Now we're ready to get started with the JavaScript.

But how do we get at these values?  Well, every DOM element has an object called "dataset" and every dataset will contain an attribute for every data- attribute you define in the HTML.  This attribute is what you define after the data- prefix.  So the attribute we're looking for in the dataset is the "tech" attribute.

Here's an example:

```javascript
const techPickerBtn = document.querySelectorAll("img[data-tech]")[0];
techPickerBtn.dataset.tech;
```


What this line would do, is grab the first HTML element in the NodeList, look at it's dataset object and grab the value of "tech."  The first element on this NodeList, the .tech-picker list, is an <img> tag for the HTML5 logo image.  That image has the data-tech attribute we're looking for.  The value of that data-tech attribute, the string "html5", is the value of the dataset.tech attribute in JavaScript.

Now we have a way of retrieving these values.  It's just a matter of comparing the data-tech value of the image that was clicked on, with the datatech values of the divs that contain our projects.  We know how to get the data, the rest is fairly trivial.

First thing I'm going to do is get two arrays of all the appropriate HTML items.  We need one array of the images in the .tech-picker list, and we need one array of every .flex-gallery-img div.  The [querySelectorAll()](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) method does not return an array, but it returns what's known as a [NodeList](https://developer.mozilla.org/en-US/docs/Web/API/NodeList).   But that's okay, we'll just pass that NodeList into "[Array.from()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from)" and turn it into an array.

```javascript
const techPickerBtns = Array.from(document.querySelectorAll("img[data-tech]")); 
const galleryDivs = Array.from(document.querySelectorAll("div[data-tech]"));
```

Now we've got our arrays.  Next, we need to set up our event listeners for the techPickerBtns array.  I prefer to use a "[forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)" method for this.  There are some arguments and comparisons claiming that for loops and the map function may or may not be faster and for larger applications, they [might have a point](https://codeburst.io/javascript-map-vs-foreach-f38111822c0f).  But we've only got five items and a very simple script so it doesn't matter which method you use.  For me, forEach is short and concise and because we don't make any use of the array map would return, I see no reason to use map.

```javascript    
techPickerBtns.forEach( (btn)=> {
    btn.addEventListener("click", selectDiv);
});
```


I'll just slap that underneath our galleryDivs declaration.  You've probably already gathered that I'm naming the function it calls "selectDiv()."  What selectDiv() will do, is, when called, determine which div is selected, which one is highlighted, based on which button we click on.

So let's go ahead and start setting up that function.  In this particular case, I prefer to not use a closure because of all the extra indentation.  Between our galleryDivs declaration and our techPickerBtns.forEach call, let's insert our new function:

```javascript
const selectDiv = function() {

}
```


Typically I'd use the arrow notation, because it's more concise, but here we'll run into a problem with the [this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this) keyword.  [Arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) don't have their own _this_ binding.  If I use an arrow function, _this_ will refer to the Window object.  To get around this, I use the older syntax.  One common thing I do is that I always, always, always, console.log(this) before using it, to make sure I'm getting the right object.

We have our function setup.  Once inside, we need to get the dataset.tech attribute of the image we're clicking on.  Well, this is why we need the _this_ keyword.  _This_, in an event listener, will refer to the item that was doing the event listening.  Therefore _this _refers to the image we click on.  We'll take that attribute and, because it's kind of wordy, we'll pass it into a const.  It's honestly a judgement call if you think it's worth declaring a new variable or not.  This is the age old programmer's dilemma of readability vs conciseness.

```javascript  
const selectDiv = function() {
    const tech = this.dataset.tech;
}
```


Now what we need to do, is see if the value of tech is in any of the data-tech attributes of the gallery divs.  Remember, the data-tech attributes of those divs had multiple values.  If we access and console.log those values, you'll find that they're stored as one single string, with each value separated by a space.  To compare these values with the tech variable, it'll be easier if we turn that string into an array with the [split()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split) method.

So what we're going to do is use forEach again to go through all the gallery divs, get the dataset.tech value, and turn that value into an array.

```javascript  
const selectDiv = function() {
    const tech = this.dataset.tech;
    galleryDivs.forEach( (gallery)=> {
        const technologies = gallery.dataset.tech.split(" ");
    
    });
}
```

Our last step is fairly simple.  We need to check if the value of tech is in the technologies array for each gallery div.  If so, we'll change the background of that div to a darker shade of grey, and if not, we'll change it back to the pale, almost white, shade of grey I'm using in my styling.

We can accomplish this by using the [indexOf](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf) method.  The indexOf method is called on an array and checks that array for the first position of the value passed into the method.  If that value is not found in the array, it returns a negative one.  If we call this method and check for a negative one value, all that's left to do is to change the color of the background of the div appropriately.

We can do this by going into the gallery's style object and setting the backgroundColor attribute the color we want in a string value.

```javascript
const selectDiv = function() {
    const tech = this.dataset.tech;
    galleryDivs.forEach( (gallery)=> {
        const technologies = gallery.dataset.tech.split(" ");
        if(technologies.indexOf(tech) !== -1) {
            gallery.style.backgroundColor = "#ccc";
        } else {
            gallery.style.backgroundColor = "#eee"
        }
    });
}
```


And there we have it.  That's the entire app.  Now I tend to wrap things in an IIFE (Immediately Invoked Function Expression) to avoid global scope conflicts, so the entire script is the following:

```javascript
(function() {

    const techPickerBtns = Array.from(document.querySelectorAll("img[data-tech]"));
    const galleryDivs = Array.from(document.querySelectorAll("div[data-tech]"));
    
    const selectDiv = function() {
        const tech = this.dataset.tech;
        galleryDivs.forEach( (gallery)=> {
            const technologies = gallery.dataset.tech.split(" ");
            if(technologies.indexOf(tech) !== -1) {
                gallery.style.backgroundColor = "#ccc";
            } else {
                gallery.style.backgroundColor = "#eee"
            }
        });
    }

    techPickerBtns.forEach( (btn)=> {
        btn.addEventListener("click", selectDiv);
    });
    
})();
```


By being able to define our own custom data- attributes and using the dataset object, we have another method to manipulate DOM objects with conciseness and precision.  This method is also scalable.  I can add more gallery objects to my DOM and simply apply the appropriate datatech attributes.  I'll never have to touch the script again in future updates.  This script is scalable.  You can view the code, and my, as I write this, unfinished portfolio on my [GitHub page](https://github.com/jckuhl/portfolio).

The data- attribute is definitely something to add to your toolbox.  It has its uses.  If you ever take Wes Bos's [30 Days of Code](https://javascript30.com/) challenge, the very first project is a drumkit project.  There are boxes on a screen that correspond with the home row keys on the keyboard.  Press the appropriate button, the box lights up and a drum sound specific to that box plays.  This little project makes good use of the data- attribute, allowing us to match the keycode of the button press with his custom "data-key" attribute of both the box div elements and the audio elements in the project.  It's a great example of how useful the data- attribute can be.

Now that you've got this power, just remember, with great power, comes great responsibilities.

Happy Coding.
