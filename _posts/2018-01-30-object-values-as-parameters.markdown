---
author: jckuhl
comments: true
date: 2018-01-30 01:40:07+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/01/29/object-values-as-parameters/
slug: object-values-as-parameters
title: Object Values As Parameters
wordpress_id: 97
categories:
- JavaScript
tags:
- object-oriented
- programming
---

I started playing with Canvas the other day.  I started throwing together a game of Pong.  It's fairly basic.  As I'm running along programming the game from ground up, I realized I had a bit of a problem.  Every time I wanted to draw some text on the canvas, I had to write out a long and annoying chunk of code.  I had to define the font, the font size, the position.  It got pretty ugly.  I thought about wrapping it all into a function, but the function would be forced to have six or seven parameters, and that was just ugly.

But there was a solution.  This was a Canvas project, so as I went a long, I was sort of "refactoring as I go," building what I call the EasyCanvas project as well.  I refactored my text drawing functions into my new library and I could do this because JavaScript lets you pass entire objects into functions and use their key-value pairs as parameters.

So I wrote a function in my EasyCanvas library that would display some text.  It was a generic function.  It takes two parameters, a context parameter, and an object with the text properties in key-value pairs.

```javascript    
exports.drawText = function(context, props = {
            string: string, 
            fontsize: fontsize, 
            face: face, 
            color: black, 
            just: "left", 
            x: x, 
            y: y
    }) {
    context.font = this.setFont(props.fontsize, props.face);
    context.fillStyle = props.color;
    context.textAlign = props.just;
    context.fillText(props.string, props.x, props.y);
}
```


So how does this work?  Well, when you call the function and pass in a props object, the values of the corresponding keys will be passed into the body of the function just like any other parameter.  We can also set default values, like you can see I did with the _color_ property and the _just_ property.

This made my code back on my pong.js file  neater.  Instead of writing out all the draw functions each time I need to draw text on my canvas, I simply need to make an object with the properties I need.

I ended up further refactoring my code, after realizing that all of my text are the same font and the same color, and in general, fairly similar.  I was able write a function that generated the properties object for me.  This snippet would be from pong.js, rather than the EasyCanvas library.

```javascript
function displayMenuText(string, fontsize, x, y, just="center") {
    const victoryProps = {
        string: string,
        fontsize: fontsize,
        face: fontVT323,
        color: green,
        just: just,
        x: x,
        y: y
    }
    EasyCanvas.drawText(menuCtx, victoryProps);
}
```


This function extends about as far as I'm willing to go with parameters, which is five.  Fortunately, as only two of my texts are _not_ centered, I can set one of these parameters to a default and ignore it most of the time.  This allows me to pass the values I want to an object, and then pass that object to the function in EasyCanvas that actually does the drawing.  This keeps my code simple.  It allows my EasyCanvas code to remain generic to general Canvas functions and my pong code specific to the game of pong.   It also reduced the code at every point where I needed to display text to a single line of code:

```javascript
    displayMenuText("Hit Esc or Space to continue", fontSize.Md, center.x, center.y + 36);
```

It's still a long piece of code, Wordpress doesn't show all of it.  But Brackets and Sublime do.  I don't, after all, code in Wordpress!  There's still some argument to be made about reformatting the code a bit.  Some people live by a rule of sixty characters, or maybe even thirty characters per line.  But I don't see that being necessary.

I could do:

```javascript
displayMenuText(
    "Hit Esc or Space to contine", 
    fontSize.Md, 
    center.x, 
    center.y + 36
);
```

But again, Brackets has a wide screen, so I think it's fine the way is above.  It was much dirtier before with objects all over the place, and even before that, with calls to various Canvas functions.  The code is cleaner and I dropped like 20 lines of code.

Passing the arguments of a function in bundled as an object is a powerful tool when you encounter situations where a function has unwieldy parameters.  Keep it in handy in your tool box.

You can see the current progress of both pong.js and EasyCanvas at my [github](https://github.com/jckuhl/pong) page.  As of writing this, neither of them are yet fit for use but feel free to peruse.  When it's developed enough, EasyCanvas will be separated from pong.js and in its own repository.

Happy Coding.
