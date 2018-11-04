---
layout: post
title:  "JavaScript. 'Tis a silly place'"
date:   2018-11-03 14:04:05 -0400
categories: code javascript webdev
---

Before I begin - go vote on Tuesday.

JavaScript is a language of weak and dynamic types, meaning that there is no strong typing.  Types are inferred by the interpreter and can change on the fly.  You can build an array mixed with anything you want.  You can pass whatever objects to functions as parameters as your heart desire.  And while this has some advantages, this also has some serious drawbacks.

In my professional life, as a Software Development Engineer in Test (SDET), I use Java, specifically with Selenium Webdriver, and that means that I've become accustomed to static types.  If a function in Java takes a string, then you can't pass anything else in.

```java
public String add(String a, String b) {
    return a + b;
}
```

If I were to call this function, `add(1,2)`, the compiler would get mad and fail.  The function explicitly takes strings, and nothing else.  In JavaScript however, I can do the same:

```javascript
function add(a, b) {
    return a + b;
}
```

If I do that, then `add(1,2)` and `add('cat', 'dog')`, are valid, and they'll return `3` and `'catdog'` respectively.  Or I could do `add(1, 'cat')` and get `'1cat'` back.  JavaScript doesn't care.

This can lead to some very interesting behavior, and a common interview question.  Can the following statement ever be true?

```javascript
(a == 1 && a == 2 && a == 3)
```

The answer is, yes.  Of course this question doesn't yet have anything to do with type coercion, but we'll get there.

By overriding `valueOf()`, we can make this nonsensical statement true.  We can define `a` as the instance of some class, and override `valueOf()` such that it increments its value everytime it is called.  The way `==` works, is that it calls `valueOf()` when making comparisons, to see if the values are equal.  That's the definition of loose equality in JavaScript.  We want to see if the _values_ are equal, not the types.

If we do the following, we'll make that statement true.

```javascript
class Thing {
    constructor() {
        this.value = 1;
    }

    valueOf() {
        return this.value++;
    }
}

const a = new Thing();

if(a == 1 && a == 2 && a == 3) {
    console.log(a);
}
```

Each time `a` is compared, it is incremented.  Thus, it evaluates to true and it will log to the console `Thing: { value: 4}`.

But can we get weirder with type coercion?  We most certainly can.

Lets make a few edits to the code

```javascript
class Thing {
    constructor() {
        this.value = 1;
    }

    valueOf() {
        return this.value++;
    }

    toString() {
        return this.value++;
    }
}

const a = new Thing();

if (a == 1 && a == '2' && a == "3") {
    console.log(`${thing} = 1, 2 and 3`);
}
```

The loose equality operator will ignore and coerce types, so we get the same behavior.  The value of `a` will match and pass all three conditionals, and once put into the template literal, the `toString()` method will increment it even further, resulting in the following log to the terminal:

```javascript
4 = 1, 2 and 3
```

We can make this even worse by changing `toString()` to use a prefix increment, rather than post fix:

```javascript
class Thing {
    ///...
    toString() {
        return ++this.value;
    }
}
```

This will cause the value to be incremented, _before_ it gets used.  And then this nonsense would be logged to the terminal:

```javascript
5 = 1, 2 and 3
```

In short, JavaScript's type coercion system and the relative ease of overriding object methods can really hurt you if you're not careful with what you're doing.  You need to really understand how typing works and how overriding works to really get to know JavaScript.  Conditionals using `==` will implicitly call `valueOf()`.  String interpolation will implicitly call `toString()` and therefore if you've changed those, you can get strange behavior.

Don't change those things.  Be careful of type coercion. Use strict equality at all times, unless type coercion and comparison of only value is expressly what you want to happen.  There are times when I've used `==` on purpose.  I had a value coming in from an input box, which would be a string, and it needed to be compared to a number.  But in that situation, I was safe because I was _expecting_ to use type coercion.  When in doubt, use `===` until you have to use `==`.

Happy coding.