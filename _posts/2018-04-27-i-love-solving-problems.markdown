---
author: jckuhl
comments: true
date: 2018-04-27 02:49:19+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/04/26/i-love-solving-problems/
slug: i-love-solving-problems
title: I Love Solving Problems
wordpress_id: 132
categories:
- JavaScript
- programming
- Web Development
tags:
- webdev
---

I've been working on my primary webpage, Project Breakpoint, which is a repository of resources.  A feature of that webpage is to be able to filter those resources.  For example, on the JavaScript page, there are resources for multiple frameworks and libraries.  The website itself is not yet ready for public consumption, but I had the filter working.  I had a set of logos, you click on the logo, and only the appropriate resources are displayed.  Click on it again, and it gets deselected and everything comes back.  Click on a different logo, all other logos are then deselected so only one is selected at a time.  For example, if I click on the Angular logo, I get all the Angular resources.  If I then click on the Electron logo, the Angular logo gets deselected and I get only the Electron resources.

Pretty simple.  The code wasn't terrible.  I won't post the whole thing, just the event listener.  If you do want the full code, here's a link: [Github](https://github.com/jckuhl/project-breakpoint/blob/master/dist/scripts/index.js).  Note the link to the full code goes to the current code, which is no longer what I've pasted in this blog post, but you can find this in the history if you want to dig a bit.

```javascript    
techs.forEach( (tech)=> tech.addEventListener('click', ()=> {
    if(tech.classList.contains('deselected')) {
    // select the technology
        tech.classList.remove('deselected');
        tech.classList.add('selected');
        techs.forEach( (tech)=> {
        // remove the selected class from any previously select technology
            if(event.target.alt != tech.alt) {
                tech.classList.remove('selected');
                tech.classList.add('deselected');
            }
        });
        displayTech(tech.alt);
    } else {
        tech.classList.remove('selected');
        tech.classList.add('deselected');
        displayTech('all');
    }
}));
```


The way it works is pretty simple.  I have all my logos stored in a Node List called _techs_.  Each logo is an `<img>` tag and each `<img>` tag has an alt text equivalent to what technology it is.  The Angular logo has an alt text of _angular_, React is _react _and so on and so forth.  All of the information on my page is organized in cards.  These cards are just article tags with a card class (a box with rounded edges and some simple margins applied.  They are held in a card holder.

If I click on a logo, the event listener above is fired.  First, we check if the logo we clicked on is selected by seeing if the _de__selected_ class is present.  This is a CSS class that provides a black border, and is the default class applied to the image.  If this class is present, we swap it out for the _selected_ class (a red border.)  Then we go through all the images again and look for whichever images don't match the alt text of the image we clicked on and deselect those.  Then we pass that alt text (which is the technology we clicked on) to the `displayTech()`function.  `displayTech()`_ _will then call functions that filter the cards and display only the appropriate ones.  Each card has a `data-tech` tag that contains whichever technologies are relevant, and the script filters by matching the tech that was passed into `displayTech()` with those `data-tech` tags.

If however, the logo we click on (come to think of it, logo would be a better variable name than tech, maybe I'll change it) is already selected, we're going to deselect it and pass the string _'all'_ to `displayTech()` which will caused `displayTech()` to simply show all the cards.

Here's the problem with this approach:  None of the logos were labeled.  It assumed the reader knew what the React logo was, or the Vue logo.  It could also get confusing, because the Electron and the React logos are similar.  So I wanted to wrap my images in `<figure>` tags and use `<figcaption>`.  This works, sort of, except that means only the image is clickable, and this is not user-friendly.  The user should be able to click anywhere on the figure to select the technology that is desired.

I had to change the variable that holds the logos from holding the images themselves, to holding the figures that held the images.  This led to a bit of a mess.

For one thing, I couldn't use the image alt attributes anymore.  Or I could have, but then I'd have to mess with DOM traversal and I decided I'd just be more consistent with using the `data-` attribute on the `<figure>` tag, as I did on the cards.

Then I had to change how the _selected_ and _deselected_ classes were being applied.  Well, having a border go around the figures themselves looked pretty bad so I stuck with having a border go around the image.  DOM traversal couldn't be avoided, but it wasn't very complicated.  Using the `.children` property, I was able to find my images pretty quickly.  `.children` is an [HTMLCollection](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCollection) of all HTML elements that are children of the element that the property is called on.  The image tags are the first element of each one, so I can access them by simply using `.children[0]`.

And with that, everything was fixed.

Or so I thought, until I realized, it was no longer deselecting previously selected logos!  This puzzled me a moment.  See, I have no control over which element in the figure the user will click on.  Will it be the `<img>` tag?  The `<figcaption>` tag or the `<figure>` tag itself?  I wouldn't know.  I need to run the `.forEach()` method on the figures and deselect the ones that don't have a `dataset.tech` value that matches the one I clicked on, but how do I do this if I don't know what the user is click on?

Well, I could do it the hacky way and add a `data-tech` attribute to every element in the figures.  But that's just dumb.  There's a more elegant way to do it.  I can simply call the `.closest()` method, and pass in the CSS selector for a figure as the parameter.  That should work.

But it doesn't.  Why not?  Because there was one final problem and I hadn't caught it.  When I used `.children[0]` to access the images, I decided to put that in a variable rather than write it out a bunch of times.  I also put that within the second `.forEach()` loop.  Which meant it was referring to the wrong HTML elements.  This was causing it to miss behave.  So I ended up writing out `.children[0]` inside that `.forEach()` to make sure it was using the appropriate local variables.  Once I did that, everything worked.

And the code was complete.  I did go ahead and rename the variables, by the way:

```javascript   
// add event listeners to the images in the tech filter
logos.forEach( (logo)=> logo.addEventListener('click', ()=> {
    const logoImg = logo.children[0];
    if(logoImg.classList.contains('deselected')) {
        // select the technology
        logoImg.classList.remove('deselected');
        logoImg.classList.add('selected');
        logos.forEach( (logo)=> {
        // remove the selected class from any previously select technology
            if(event.target.closest('figure')
                .dataset.tech != logo.dataset.tech) {
                //console.log(tech.children);
                logo.children[0].classList.remove('selected');
                logo.children[0].classList.add('deselected');
            }
            });
            displayTech(logo.dataset.tech);
    } else {
            logoImg.classList.remove('selected');
            logoImg.classList.add('deselected');
            displayTech('all');
    }
}));
```


Sorry if the formatting is a bit weird, due to Wordpress's limitations, the line `if(event.target.closest('figure').dataset.tech != logo.dataset.tech) {` doesn't fit.  Some people might argue that such a line should be broken across multiple lines anyways.  That's a lot of characters (and dot operators) for one line!

Anyways, that's the essence of problem solving in programming.  Take things one step at a time, fix one problem at a time.  It was a bit of a rush to see it finally work.

Happy Coding
