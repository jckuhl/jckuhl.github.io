---
author: jckuhl
comments: true
date: 2018-04-20 21:36:19+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/04/20/visual-studio-code/
slug: visual-studio-code
title: Visual Studio Code
wordpress_id: 129
categories:
- programming
- VSCode
- Web Development
---

A few weeks ago, I downloaded [Visual Studio Code](https://code.visualstudio.com/) (VSCode.)  I don't recall what prompted me to do it but I am super glad I did.  This lightweight little text editor might be the greatest thing since sliced bread.  Seriously, if it morphed into a woman I'd marry it right now and live happily ever after.  I might name my first born kid "VSCode."

So it's kind of ironic, to me, that it is a Microsoft product.  If you knew me personally, you'd know I do not have a happy relationship with Microsoft.  I had a year or so of going from Windows machine to Windows machine, unhappy with the OS, unhappy with Microsoft in general.  For me, finding an excellent Microsoft product for my Macbook is surprising.  It's like walking into Congress and finding a decent human being; you just don't expect it to happen.

VSCode is written in [Electron](https://electronjs.org/), which is a framework for writing desktop apps using the Holy Trinity (HTML/CSS/JS.)  It comes with an integrated git version control, integrated terminals, extensions and a debugger.  Wes Bos, of [wesbos.com](https://wesbos.com/) and [#JavaScript30](http://script30.com/) describes it as almost being a lightweight IDE platform in his [Syntax](https://syntax.fm/) podcast.  It supports syntax highlighting, path completion and multiple themes out of the box, as well as thousands of custom extensions ready for download.

I've used multiple text editors.  I've gone from Aptana Studios to Brackets to Sublime and I personally find VSCode the best.  Aptana was slow to launch, Brackets kept throwing linting errors that made no sense and had inconsistent syntax highlighting and Sublime wasn't nearly as good with code completion.  Bracket's has the live preview option and Aptana has a preview button built in, but that's made a moot point by using Node.JS's live-server command (which isn't a VSCode feature, but a Node program you can install via NPM, but it does eliminate Bracket's advantage of the live preview.)

[caption id="attachment_130" align="alignnone" width="2048"]![webdev](https://projectbreakpoint.files.wordpress.com/2018/04/webdev.png) Here you can see I've got my project folder open, I have one terminal running the Sass watcher and another terminal running live-server.[/caption]

VSCode on the other hand has very smart code completion.  It can read across multiple files.  If I'm writing in the template for an Angular component, it can recognize the properties I've defined in the TypeScript code.  It makes development super easy.  Long JavaScript lines like _document.querySelectorAll_ can be written in a few keystrokes.  Press _d+o_, hit tab to highlight "document", press _q+s+a_ and hit tab to select _.querySelectorAll_.  Five key strokes to write such a verbose JavaScript native function (come on ECMAScript, why so verbose?  addEventListener?  getElementsByTagName?  Seriously guys?)  With a typed language like TypeScript, it will also help you keep track of your types and autocomplete those, especially if you're using some weird HTML element type that you can't remember how to spell.  Finally, like any standard text editor these days, it comes with [Emmet](https://emmet.io/) installed by default.

Not only does it have auto complete, but it also has full definitions.  Seriously.  Cmd clicking (or ctrl for you Windows types) a function or a variable or any other declaration will take you straight to the definition, even if it's the official ES2016 docs for TypeScript!  So if you've to a bunch of modules and you're calling a function that you defined a few weeks ago in another file and you can't remember how the function works?  Cmd click it and jump straight to it.

Another favorite feature of mine, VSCode puts a little icon next to your file to let you know what exactly that file is.  It might not seem like a big deal, but it is actually great to be able to look at a glance and see what a file is.  Oh look, that file has the Sass icon, that must be the .scss partial.  That other one has the octothorpe for CSS, that must be the Sass watcher's output CSS file.  It'll give you the C++ symbol for .cpp files, the <> for HTML files, {} for a JSON file and so on and so forth.

Performance-wise, VSCode is fast.  It takes hardly any time to load or take any action.  It doesn't take a lot of space on your computer.

I freaking love this thing.  I could go on for days about it.  But I'll just end by linking some tutorials from [Level Up Tuts about VSCode.](https://www.leveluptutorials.com/tutorials/vscode-tutorials)  Seriously, download this thing and give it a try.  What's the worst that can happen?  You'll hate it and go back to Atom?  Maybe.  But I bet you won't.

Happy Coding.
