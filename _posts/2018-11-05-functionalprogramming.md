---
layout: post
title:  "Functional Programming"
date:   2018-11-05 10:08:05 -0400
categories: code javascript webdev
---

Today I watched a video on functional programming:

[![Functional Programming in ES6](http://img.youtube.com/vi/FYXpOjwYzcs/0.jpg)](http://www.youtube.com/watch?v=FYXpOjwYzcs)

[https://www.youtube.com/watch?v=FYXpOjwYzcs](https://www.youtube.com/watch?v=FYXpOjwYzcs)

Speaker:  Jeremy Fairbanks

Here are the notes I took while watching it.  It roughly follows what the Mr. Fairbanks talked about.  I'm taking no claims for originality for the content below, it's mostly a rehash of what he talked about.

Functional Programming is a programming paradigm based on the mathematical concepts of functions.  In mathematics, a function is a _mapping_ of values from a domain to a range.  To put it simply, values go in, mapped values come out.

An example:

```
f(x) = x + 2

f(1) = 2
f(2) = 4
f(3) = 5
... etc
```

For every input, there is one output, and the output is the same every single time with the given input.

Haskell Curry came up with the idea of functional programming, putting the idea mathematical functions into the theory of computation, the same theory that he and his student Alan Turing, would develop.

Functional programming has four principles:
1. **Predicable.**  Pure functions and declarative style programming.  A given input will always give the same output
2. **Immutable.**  State is never mutated, instead a new state is created when necessary.
3. **Transparent.**  State is outly open, it isn't stuffed behind objects
4. **Modular.**   Functions are atomic building blocks.

ES6 variable bindings have `const` and `let` which should always be used over `var`.  `const` should be favored over `let`. 

ES6 brings us lambda functions in the form of arrow functions.

```javascript
()=> {}
```

> Note that if the `this` key word is used, the older syntax of `function() {}` must be used.

Lambdas can be used to easily define or pass functions into other functions or use them as first class citizens.

The spread operator is also a useful functional tool, as it can clean up having a mess of arguments in a function

```javascript
const array = (...elements) => elements;

const evens = array(2,4,6);
// evens === [2, 4, 6]
```

JavaScript also has destructuring features as well:

```javascript
const langs = ['JavaScript', 'Python', 'Haskell'];
const [js, ...otherLangs] = langs

//js === 'JavaScript'
// otherLangs === ['Python', 'Haskell']
```

And another useful JavaScript feature that is used frequently in functional programming is `Object.assign()`

```javascript
Object.assign(
    {},
    { name: 'tom' },
    { age: 47 }
);

// { name: 'tom', age: 47}
```

A **pure function** only depends on its arguments.  It does not mutate state, it has no side effects.  It has referrential transparency, meaning every input is guaranteed to produce the same output.

Why use pure functions?  Easier to test, easier to read, less likely to change the data you're working on.  Hidden state is uncertain state.

Functional programming takes us from _imperative_ to _declaritive_ programming.  Imperative means that the implementation is designed by the programmer and followed step by step.  Declarative _declares_ what you want to do.  SQL, for example, is declarative.  You tell the programmer what you want to do, but not how.  Functional programming wraps imperative code into functions allowing the program to be more declarative.

Functional programming also puts emphasis on immutability.  Do not mutate state, make new state and override the old.  `Array.prototype.slice()` should be favored over mutating methods like `Array.prototype.splice()`.  Methods like `Object.freeze()` and liberaries like [Immutable.js](https://facebook.github.io/immutable-js/) allow you to take better control of mutations.

Immutability also allows for state history to be stored, because you can save the previous state in some array, and then make new state.  We can go back and forward through history.

However, functional programming does tend to be more verbose, it leads to greater object creation and open state.  It can sometimes have performance trade offs, with more garbage collection and memory usage.  Immutable.js does aid with some performance issues.

With the basics out of the way, we can move on to first class functions.  Functions in JavaScript can be used in the same way as any other variable.  They can be passed into other functions, stored in arrays and objects, and they have a prototype with a number of methods that can be called.  They are, under the hood, objects, with a hidden "code" property.

Closures can then produce encapsulation for our state.  Closures are the execution contexts that remain when an outer function completes, but leaves its state available for an inner function.  We can do this because functions are first class citizens.

```javascript
function makeAdder(a) {
    return (b) => a + b;
}

const addSix = makeAdder(6);
addSix(6);
// 12
```

Partials allow you to call a specific function with some of the arguments already pre-filled.

```javascript
const partial = (fn, ...args) => {
    return fn.bind(null, args);
}
```

Partials can bring is to _currying_, which is the concept of mixing functions together.

```javascript
const add = x => y => x + y;
```

Here's a more complex, if contrived example:

```javascript
const map = fn => array => array.map(fn);
const multiply = x => y => x * y;
const pluck = key => object => object[key]

const discount => multiply(0.98);
const tax => multiply(1.0925);

const customRequest = request({
    headers: { 'X-Custom': 'my key' };
});

customRequest({url: './shop/cart/items';})
    .then(map(pluck('price')))
    .then(map(discount))
    .then(map(tax));
```

We curry together a map function that holds an array in a closure, a multiply function that can keep the state of multiplying by a discount rate or a tax rate, and a pluck function that can grab a value from an object if given a key.

Using these functions together, we can build a set of instructions that grabs a shopping cart's items, gets the prices, and applies discounts and taxes to them.

Finally, we have composing function closures.  Piping one thing to another.  One function's output becomes the next function's input.

```javascript
const compose = (...fns)=> fns.map()

const processWord = compose(hyphenate, reverse, toUpperCase);
```

But what about loops?  Loops are imperative, and use mutable state.  So how do we tackle it in functional programming?  Recursion.  Recursion defines a function in terms of itself

```javascript
const fact = (x) => {
    if(x === 1) {
        return 1
    }
    return x * (fact(x - 1));
}
```

We simply call the function within itself, becoming simpler and simpler while reducing towards a base case.

> Be careful!  With a bad or missing base case, this can lead to exceeding the maximum stack size!

Recursion can therefore be used to implement looping mechanisms.  You'll simply need to consider the base case, and how the function simplifies with each iteration.  Recursion calls functions and grows the call stack until the base case is met, and then the stack will start at the top and work its way down, popping off functions and returning answers.  However, this can lead to there being more functions than available stack frames.

Stack issues with recursion can potentially be fixed with **tail call optimization**, which comes with ES6.  What this means is that we have to call the recursive expression dead last, however in the above example, we're calling a multiplication expression that waits on the recursive expression.  Our last expression is `x * fact()`.

We can fix this by adding an accumulator argument to our function.

```javascript
const fact = (x, accum) => {
    if(x < 2) {
        return accum
    }
    return fact(x - 1, x * accum);
}
```

This version is now tail call optimized.

As a recap, functional programming is predictable and deterministic.  It is immutable and therefore helps you avoid bugs and changes to data.  It takes functions and atomizes them, creating building blocks with composition and currying.  Functional programming, as a style, is a paradigm worth practicing.  I myself tend to follow a functional style, though I'm not strict about it.  I use it where I can, but in web development, side effects (DOM manipulation, as an example) are unavoidable, so I try to be functional _where it is possible._

Happy coding.