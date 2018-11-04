---
author: jckuhl
comments: true
date: 2018-05-17 18:37:58+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/05/17/fun-with-webpack/
slug: fun-with-webpack
title: Fun With Webpack?
wordpress_id: 153
---

The last few days were a mix of frustration and success as I wrangled with Webpack to untangle a complicated piece of code into a series of smaller, more manageable units.  I was working on a project I called "Evolution" and it's really just an experiment where I simulate a bunch of things living in a box and watch as they eat food and each other.  There are settings the user can set, the user can watch individual creatures and see their current status and so on and so forth.

You can view the current state of the project at [https://github.com/jckuhl/Evolution](https://github.com/jckuhl/Evolution).  It's not complete and I'm not hosting it anywhere.  You can see how I've split it apart into modules under the `dist/src` directory.

The reason for this was because the code got too unwieldy.  I originally only had two JavaScript files, one that ran the controls, and one that ran the simulation.  The simulation file got too big.  The whole thing was some 500 lines of code long.  The Creature Class and the Simulation Class were each some 200+ lines of code and I'd get lost trying to fix or add things.

I broke it apart into modules.  After about an hour of figuring out how to get `require()`to work and working with Webpack, I got the project up and running again.  Instead of having two 500ish line long JavaScript files, I had about 8 little files that are easier to manage.  Webpack puts them together into a `bundle.js` and I don't have to even look at that file.

What I learned was that, if I'm working on a big project, I should just assume, from the beginning, that I should use Webpack.  Modularizing everything was difficult and complicated, especially since I kept breaking what I had already fixed.  I learned how useful the `webpack --watch` command is.  I can set that command, and every time I save, Webpack just goes.

Moral of the story, I should start planning to use Webpack, before I start a project.

Happy Coding.
