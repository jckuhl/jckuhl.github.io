---
author: jckuhl
comments: true
date: 2018-05-15 19:58:26+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/05/15/hitting-two-birds-with-one-stone/
slug: hitting-two-birds-with-one-stone
title: Hitting Two Birds With One Stone
wordpress_id: 150
categories:
- '#100DaysOfCode'
- JavaScript
- Web Development
tags:
- vuejs
---

Today was a good day.  I solved two problematic bugs in one blow.  I've been working on a webpage where people can log their progress for #100DaysOfCode.  I had two rather challenging problems, unrelated to each other, or so I thought, that I spent about two frustrating days trying to find a solution to.

The webpage is written in Vue.js and it's divided into components.  The sidebar component allows you to track your round, and the main component allows you to add posts and view older posts.![100 days of code screen 1](https://projectbreakpoint.files.wordpress.com/2018/05/100-days-of-code-screen-1.png)

Here you can see it as it is right now.  It's incomplete.  The dates don't work right and the CSS is far from finalized.  There's also no back end right now, this webpage is mostly only UI, but it's UI that _mostly_ works right now.  A few things to clean up and it should be done.

The problem I had was with deleting and posting posts.  Originally I posted my post by simply pushing the post data onto an array using `.push()`.  This worked fine, except it meant the most recent post was on the bottom.  So I swapped `.push()` for `.unshift()` and suddenly I had problems.  Every post was identical to the first post I put up.  Furthermore, the delete buttons wouldn't work.  They appeared to delete out of order and would not delete the last item.  The only time the delete button worked as intended was if I deleted in order from bottom to top.

It turns out, Vue's `v-for` directive was the culprit.  You see, `v-for` uses, by default, an "in place patch" strategy.  In fact, I think I'll just let Vue's official docs explain it best:


<blockquote>When Vue is updating a list of elements rendered with `v-for`, by default it uses an “in-place patch” strategy. If the order of the data items has changed, instead of moving the DOM elements to match the order of the items, Vue will patch each element in-place and make sure it reflects what should be rendered at that particular index. This is similar to the behavior of `track-by="$index"` in Vue 1.x.

This default mode is efficient, but only suitable **when your list render output does not rely on child component state or temporary DOM state (e.g. form input values)**.

To give Vue a hint so that it can track each node’s identity, and thus reuse and reorder existing elements, you need to provide a unique `key` attribute for each item. An ideal value for `key`would be the unique id of each item. This special attribute is a rough equivalent to `track-by`in 1.x, but it works like an attribute, so you need to use `v-bind` to bind it to dynamic values.

-[Vue.js Guide: Key](https://vuejs.org/v2/guide/list.html#key)</blockquote>


I was using `v-for` and relying on the index as my "unique" key.  This doesn't work because Vue uses the index to determine where components go, which ended up making my app all sorts of screwy if I did anything that moved the elements out of order.

Thus my problem with `unshift()` and my delete buttons weren't unrelated at all, they were caused by the same problem!  `push()` worked because it wasn't changing the order of the array.  `unshift()` moves everything over by one after sliding an element into the front, so that caused a problem.

Creating a unique key was a substantially easier solution.  I rigged up a "proto-hash" function that created a unique string from a random number generator and stored the values in a [set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) so I could ensure they were unique.  I used that value as the ID of each post.  Then I set the key to my `v-for` to that particular unique value:

```xml
<Status 
    class="component-padding" 
    v-for="status in statuses"
    :key="status.id"
    v-bind:status="status"
    @delete-post="deletePost($event)"
/>
```


This fixed everything.  I was now adding and deleting posts properly.  Moral of the story, if you're using `v-for` and you'll be doing actions on those elements that will alter the order of your array, implement a unique id to use as a key.  Don't use the index.  Even though the index is unique itself, Vue makes use of it under the hood that can really screw with you.

Happy coding.
