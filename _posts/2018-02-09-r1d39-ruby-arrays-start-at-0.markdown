---
author: jckuhl
comments: true
date: 2018-02-09 03:48:45+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/02/08/r1d39-ruby-arrays-start-at-0/
slug: r1d39-ruby-arrays-start-at-0
title: 'R1D39: Ruby, Arrays Start At 0'
wordpress_id: 115
---

I spent a lot of my time today, after driving for 4 hours for another drill weekend (hurray!) learning Ruby.  I don't have much interest in the language, but I thought maybe I'd want to take a gander at Ruby on Rails later down the road, so at least getting the basics right now wouldn't be a bad diversion.

So far, not a fan of the language's syntax.  There's something off-putting about not having parenthesis on functions and methods, even the ones without parameters.  It makes the function names look too much like variables.  In other languages, having the () makes the function look distinctive.  I don't like that they aren't on method calls either, like if I do something like _myArray.pop.  _In my mind, it looks like I'm trying to get a property called pop, not call a method called pop.  That syntax alone bothers me.

I don't like the lack of return statements in Ruby.  I mean, yeah, Ruby has return statements, but they're optional.  And I don't like that.  Having a return statement at the end of a method says "hey, this function terminates here and delivers this value."  Ruby's returns are implicit, the function returns whatever value gets returned by the last expression in the function.  That sounds convenient, but I find it less readable.  I therefore find myself, in the future, putting in return statements anyways.

Maybe I'll grow to like it if I use it more.

I also spend a bit of time working on a Codepen project today.  I started work on a little poker game written in JavaScript.  I learned a valuable lesson in JavaScript arrays:  Always be super careful when dealing with indexes!  The problem came in this part of the code:

```javascript    
class Hand {
    ....//constructor method . . .
    
    createHand(deck) {
        if(this.cards.length != this.size) {
            const values = []
            do {
                let value = randomNumber(1, deck.cards.length-1); 
                if(values.indexOf(value) === -1) {
                    values.push(value);
                    this.cards.push(deck.cards[value]);
                    deck.cards.splice(value,1);
                }
            } while(this.cards.length < this.size);
            }
            return this.cards;
        }

...//rest of the class . . .
```

What this method does is essentially take a random card from the deck and put it in the hand.  More specifically, it creates a copy of the card in the deck, and then deletes the card from the deck.

Originally, _value_ was calculated as a random number between 1 and the deck's size.  I kept getting _undefined_ in my hands.  Then I realized that I was a moron.  The deck's length gets smaller as I take cards out.  The deck's size however, is always 52.  Easy enough of a fix.  I edited the code for it to be a random number between 1 and the deck's current length.

Well, everything seemed fine and dandy until . . . a wild _undefined_ appears!  But lets think real hard about this.  If I have an array of the following elements:

    
    const array = [1,2,3]


What is the array's length?  3.  And what is the index of the last item? 2.

Why is this?

Because _arrays start at zero!_

So of course I'm going to get undefined if it picks a value equal to the length of the array.  Accessing _deck.cards[deck.cards.length]_ will always return _undefined_ because the index is out of bounds (in other languages, like Python, this would in fact throw _index out of bounds_ error.)

Derp.

I added a _-1_ to the calculation and everything appears to work now.

Moral of the story:  Arrays start at zero!  Keep track of your index values!
