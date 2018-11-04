---
author: jckuhl
comments: true
date: 2018-01-25 16:34:28+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/01/25/r1d25-getting-sassy/
slug: r1d25-getting-sassy
title: R1D25, Getting Sassy.
wordpress_id: 91
categories:
- CSS
- Sass
- Web Development
tags:
- webdev
---

The bad news is, my ski day is canceled.  There are only two mountains in the area open today, one is prohibitively expensive, the other only has four trails open, so I'm either spending too much, or not getting my bang for my buck.  February tends to be the prime skiing month, so maybe I'll get two days off and hit the slopes then.

The good news is, I can spend more time getting to know Flexbox and Sass.  Sass is a preprocessor for CSS, and a life saver.  Sass stands for Syntactically Awesome Style Sheets and it allows you to write your CSS programmatically.   How it works is you write Sass code, and then Sass preprocessor compiles it into CSS code.

Sass comes with many features that CSS lacks, features that make Sass so much easier to work with than CSS.  Sass helps developers stay DRY, giving us access to variables, mixins, loops and many other features.  Mixins and loops in particular allow us to create multiple similar classes without repeating code so much.  Variables and nesting allow us to organize our code in ways that are easy to organize.  Honestly, head over to [sasslang.com](http://sass-lang.com/) to check out the official tutorials and documentation.

There are two types of Sass, .sass and .scss.  I prefer the latter because .scss is compatible with regular css and easier to learn.  They both do the same thing though.

If you use Sublime Text, you'll need to set it up to get proper syntax highlighting.  [Stack Overflow](https://stackoverflow.com/questions/42159810/how-to-set-scss-syntax-in-sublime-3) has instructions on how to do that.

It's written in Ruby and can be downloaded from the terminal in a Ruby gem with the following command, so long as you've got Ruby installed:

    
    gem install sass


Once it's done, type the following into your terminal:

    
    sass -v


And if it installed properly, it'll give you your current version of Sass.  Once it's installed, all you need to do is set up the watcher and start coding.  What the watcher does is compile your .scss into .css code every time you make a save.  To set that up, you save your Sass file in one folder, and provide another folder for your output CSS.  I keep things simple, I call the Sass folder "scss" and the CSS folder "css."

To set up the watcher, it's as simple as:


    sass --watch scss:css


And it'll compile your CSS as you save your work.  Just be sure it's running while your working.

I'm giddy as a schoolboy working with this.  I hated CSS.  CSS gets so repetitive, which is the antithesis of coding.  I mean, sure, once you've got the CSS done, your website looks nice, but the CSS file is just so, blech.  Well, your output CSS file is going to be an ugly wad of CSS, there's no getting around that, but at least with Sass, you don't have to look at it.

Consider the following example.  I have a gallery of images.  Each image is in a card containing an image tag and a paragraph tag.  I also have a large image on display that a user can swap through using JavaScript (which I haven't implemented yet.)  This larger image is also a card, but a larger card.  So it's got most of the same properties, but a different width and a different text size for its caption.  The CSS for that is monstrous.  Seven rules with 3-5 properties each, most of which are repeating.  With Sass, you can do much more with less (and also, [Less](http://lesscss.org/) is another CSS preprocessor if you're curious.)

Lets see what we can do in Sass.  Here's what I came up with as I was working on my project:

```scss 
@mixin font-properties($size, $align) {
    font-family: $fonts;
    font-size: $size;
    text-align: $align;
    color: $color-accent;
}

@mixin gallery-images {
    border: $border;
    border-radius: $border-radius;
    background-color: $color-card;
}

.card-lg{
    @include gallery-images;
    width: 50%;
    max-width: 50%;
    height: auto;
    border-radius: $border-radius;
    p {
        @include font-properties(1.5em, center);
    }
    img {
        border-radius: $border-radius;
        width: 100%;
    }
}
.card-sm {
    @include gallery-images;
    width: 30%;
    max-width: 30%;
    margin: $card-gutter;
    p {
        @include font-properties(1em, center);
    }
    img {
        border-radius: $border-radius;
        width: 100%;
    }
}
```


You can get a taste of what Sass can do.  With mixins, I can keep my code shorter, and include common properties.  I can also use arguments to modify my mixins when I apply them to various rules.  I can use variables to set values, allowing me to only change them once, if I need be.  It's cleaner, and more concise.  With nesting, it's better organized.

But we can do more.  Because I have two card classes that are almost exactly the same.  The only difference is the width and the font size of the classes.  What I can do, is take everything in the card rule and put it in the gallery-images mixin, descendant selectors and all, and then just run a simple for loop in the parent div to get the two classes I need:

```scss
@mixin gallery-images($width,$fontsize) {
    border: $border;
    border-radius: $border-radius;
    background-color: $color-card;
    width: $width;
    max-width: $width;
    margin: $card-gutter;
    img {
        border-radius: $border-radius;
        width: 100%;
    }
    p {
        @include font-properties($fontsize, center);
    }
}

.flex-container {
    @include center-block(flex);
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: center;
    width: 80%;
    $width: null;
    $fontsize: null;
    $size: null;
    @for $i from 1 through 2 {
        @if $i == 1 {
            $width: 50%;
            $fontsize: 1.5em;
            $size: "lg";
        } @else {
            $width: 30%;
            $font-size: 1em;
            $size: "sm";
        }
        .card-#{$size} {
            @include gallery-images($width, $fontsize);
        }
    }
}
```


What the for loop does is output two card classes and append the strings "lg" or "sm" to the name of the card classes.  It'll then apply all the properties of the gallery-images mixin, with the appropriate $width and $fontsize values.  This allows me to define everything once.  The output CSS is a huge chunk of code that, if I were just using regular CSS, I'd have to type out myself, line by line, repeating things over and over.  Instead, I've just got a single mixin and a for loop that does the work for me.  It's cleaner and its drier.

There's little to no repeated code, it's easier to maintain, it's easy to learn as well.  If you know basic programming and CSS, Sass simply combines the two.  Pick it up and apply it to your web sites.

Happy coding.
