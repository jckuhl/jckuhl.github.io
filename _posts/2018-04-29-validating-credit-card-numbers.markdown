---
author: jckuhl
comments: true
date: 2018-04-29 15:40:17+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/04/29/validating-credit-card-numbers/
slug: validating-credit-card-numbers
title: Validating Credit Card Numbers
wordpress_id: 134
categories:
- Algorithms
- JavaScript
- programming
---

Today I thought I'd go over a simple JavaScript example of checking for a valid credit card number.  Nothing to complicated.  It's really just another example of breaking down a problem algorithmically.  It's also a good use of higher order functions, and an excellent example of the dangers of type coercion.  Every credit card and debit card number has a specific formula that determines if it is valid or not.  Each card number has 16 digits and a certain calculation of those digits, should be divisible by 10.  If it does not meet this criteria, the number is not valid.

Cards use a formula called the [Luhn Algorithm](https://en.wikipedia.org/wiki/Luhn_algorithm) and the way it works can be broken down into 6 steps.



	
  1. Record the 16th number as a checksum and set it aside.

	
  2. Double the value of every second number

	
  3. If the doubled value of this second number, is greater than 9, add the digits of that resulting number

	
  4. Add all the fifteen digits together.

	
  5. Add the checksum back to the result

	
  6. Check if it is divisible by 10


So how do we go about doing this?  Well, first we need to make a function.  Let's call it `validateCC(numStr)`.  This function will take a numeric string as a parameter, but let's also assume we don't know where our web app gets this number from.  Maybe it'll be an input from a `<input>` tag, maybe it'll come from a database.  So we might get a number, we might get a string.  The first thing we'll need to do, because we'll need to iterate over the numbers in our input, is make it a string, so we can eventually make it into an array.

We can use `typeof` to determine if it is a string, and if not, convert it.  We can also do our first check after we've done this.  Every card has sixteen digits, and if this number doesn't have sixteen digits, we know already the number is invalid, and we can return false.

```javascript   
function validateCC(numStr) {
    if (typeof numStr !== 'string') {
        numStr = numStr.toString();
    }
    if (numStr.length !== 16) {
        return false;
    } 
}
```


Now we get to the fun part.  We need to implement Luhn's formula.  First, we need to do some setup.  We need to turn `numStr` into an array, and grab the checksum, which is the last element on our string.  We can do the first part with the `Array.prototype.split()` method, and passing in an empty string.  This will split our `numStr` into an array by characters.  Then we can use `Array.prototype.pop()` to grab that last value and put it into a checksum variable.

We should only do this if the second if-branch is false, so let's wrap the rest of the code into an else clause.

```javascript 
function validateCC(numStr) {
    if (typeof numStr !== 'string') {
        numStr = numStr.toString();
    }
    if (numStr.length !== 16) {
        return false;
    } else {
        let ccNumArray = numStr.split(''); 
        const checksum = ccNumArray.pop();
    }
}
```


So why am I saving our number in a new variable?  Because I might need that numStr again with all 16 digits.  In this example, I don't, but maybe if I expanded on this validator, I might, so I'd prefer to not mutate it.  The `pop()` method does mutate an array so our `ccNumArray` only has a length of fifteen elements.  `checksum` now has the value of the last digit of our credit card.

Next I'm going to implement Luhn's Algorithm via the `Array.prototype.map()` method.  This method will mutate `ccNumArray`, which is why I declared that variable via `let` rather than `const`.  Once we're in the array, we need to do calculations on every _second_ number_ _so we need to pass the index, as well as the number into the map method.

<del>Here's the trick though.  Arrays start at zero.  To get at every second element, we actually need to get at the odd numbered indexes.  So that'd be indices 1, 3, 5, and so on.  That threw me off when I wrote this because it's a bit counter intuitive.  Outside of programming, our counts tend to start at one.  Be careful when working around array indices.</del>

Author's note, I made an error here.  According to Luhn's algorithm, you start from the checksum, which is the 16th digit (or in programmer's parlance, the 15th digit) that we popped off at the start, and you go every other number from the checksum.  So that _does _mean we want to use every _even _index.

```javascript
function validateCC(numStr) {
    if (typeof numStr !== 'string') {
        numStr = numStr.toString();
        }
        if (numStr.length !== 16) {
            return false;
        } else {
            let ccNumArray = numStr.split(''); 
            const checksum = ccNumArray.pop();
            ccNumArray = ccNumArray.map( (number, index)=> {
                //Luhn's Algorithm is implemented here.
            });
        }
}
```

For brevity's sake, the next few code examples we'll just assume it goes inside that map method.  You'll note I elected to use arrow functions with [curly bracers](http://i0.kym-cdn.com/photos/images/original/001/257/329/781.jpg).  I prefer the arrow function syntax for callback functions, (except when `this` is involved, but that's another article for another time) because it's crisper.  We do have to use curly bracers because we're not going to get all of this in one statement.

Once inside our map method, we're going to be handling numbers.  The values in our array are right now of the type string, so we need to change that.  We're going to wrap our number in `parseInt()`.  We don't want to rely on type coercion here because we'll be doing addition and JavaScript will think we want string concatenation.  We do not.  We want mathematical addition.

Then we can go ahead and check if our index is even or not.  If it is, we can continue with our algorithm.  If not, we can return the number as is.  This is the code that goes inside our map's callback function.

```javascript   
number = parseInt(number);
if (index % 2 === 0) {

} else {
    return number;
}
```


Inside that if statement, we then have to double the number.  If the number is greater than 9, we add the digits of those number together.  Otherwise, we simply return the number.  Now if we want to add the digits of a number together, we have to turn it into a string, split it, and add the numbers together.  Writing all that out got pretty long, especially since it's tabbed in so far.  I ended up factoring that out as its own function.

    
    function addDigits(num) {
       return num.toString().split('').reduce((x, y) => x + y);
    }


So what happens here, is we turn the number into a string, so we can call `split('')` on it and turn it into an array.  We did this earlier in the code.  Next we call reduce to do the summation.  `Array.prototype.reduce()` is a fantastic method that allows us to reduce all the values of an array into a single value.  In reduce, you have two parameters, the accumulated value, and the current value.  The accumulated value is all the values of the previous elements that have been previously reduced.  The current value is the value that reduce is currently working on.  In our case, x is all the digits that have already been added, and y is the digit we're currently adding.

We can now add `addDigits` to our callback function.

    
    number = parseInt(number);
    if (index % 2 === 0) {
       number *= 2;
       if (number > 9) {
           number = addDigits(number);
       }
       return parseInt(number);
    } else {
       return number;
    }


Except there's a problem.  If you used `console.log()` to look at our ccNumArray, you'll notice we still have numbers greater than nine in our array.  Why is this?

The answer took me a while, but after some investigating I realized type coercion was to blame.  When I call ​​`addDigits` and pass in our number, it gets turned into a string, and then into an array.  And when I call reduce over that array, it's adding strings, not numbers.  So if I put '16' into `addDigits(16)`, then reduce will add '1' + '6' which is concatenation, and results in '16.'  We need to go into our function and make sure that x and y are numbers.  We can do this by wrapping them both in `parseInt()`.

    
    function addDigits(num) {
        return num.toString().split('')
            .reduce((x, y) => parseInt(x) + parseInt(y));
    }


Now if we use our console to test our function so far, all the values are numeric and calculated appropriately.  Everything inside our callback function is now complete.  The whole thing should look like this so far:

```javascript  
function addDigits(num) {
    return num.toString().split('')
        .reduce((x, y) => parseInt(x) + parseInt(y));
}

function validateCC(numStr) {
    if (typeof numStr !== 'string') {
        numStr = numStr.toString();
    }
    if (numStr.length !== 16) {
        return false;
    } else {
        let ccNumArray = numStr.split('');
        const checksum = ccNumArray.pop();
        ccNumArray = ccNumArray.map((number, index) => {
            number = parseInt(number);
            if (index % 2 === 0) {
                number *= 2;
                if (number > 9) {
                    number = addDigits(number);
                }
                return parseInt(number);
            } else {
                return number;
            }
        });
    }
}
```


Now all we need to do is add up all of our numbers, add the checksum to that calculation, and see if it is divisible by 10.  If so, we can return true and our number is a valid credit card number.

We can do this in two lines of code, again using the reduce function.  We call reduce on `ccNumArray` and add everything together, including the checksum, and put that into a constant variable called `result`.  Then we can return a simple conditional, using the modulo operator to see if the result is divisible by 10.

    
    const result = ccNumArray.reduce( (x,y)=> x + y) + checksum; 
    return result % 10 === 0;


If we run our function and put our credit card number into it then this will . . . return false.  Why?  Because there's still a problem in our code.  A simple problem, one that we've seen before.  If we run console.log on our result, we get something funny.

It's a three digit number with our checksum as the last digit.  This is the result of a concatenation!  The checksum value is still a string!  Oops.  You can fix this by passing it through `parseInt()` like we did before.  You can do that either where checksum is declared, or where it is used to calculate `result`.  It doesn't really matter.  I chose to fix this at the site where the variable is declared, because the calculation of `result` was already pretty long.

Here is the code in full, you can also play with it the [gist](https://gist.github.com/jckuhl/873047d8936aab5682ed5553537e25a5) at Github.

    
    function addDigits(num) {
        return num.toString().split('')
            .reduce((x, y) => parseInt(x) + parseInt(y));
    }
    
    function validateCC(numStr) {
        if (typeof numStr !== 'string') {
            numStr = numStr.toString();
        }
        if (numStr.length !== 16) {
            return false;
        } else {
            let ccNumArray = numStr.split('');
            const checksum = parseInt(ccNumArray.pop());
            ccNumArray = ccNumArray.map((number, index) => {
                number = parseInt(number);
                if (index % 2 === 0) {
                    number *= 2;
                    if (number > 9) {
                         number = addDigits(number);
                    }
                    return parseInt(number);
                } else {
                    return number;
                }
            });
            const result = ccNumArray.reduce( (x,y)=> x + y) + checksum;
            return result % 10 === 0;
        }
    }


Plug in any credit card number and it should return true.  Here's an example that is fake, but will pass the validation:

    
    validateCC(1234567887654339)


Any test you make for this is relatively simple.  You need to have fifteen digits that when added will result in a number that is the checksum away from being divisible by ten.  So if your fifteen digits sum to 73, your checksum needs to be 7.  That number can pass the validator.  Thus this code will not weed out every invalid credit card number.  But it will weed out most of them.

There's still more you can do.  Every Visa begins with a 4.  Every Mastercard begins with 5.  You could implement this into the validator.  You could have it return a string, rather than a boolean, which can allow you to determine if it failed due to an invalid length, or if it failed Luhn's Algorithm.  However with this code, you do have something to start with.

Furthermore, there are some cards that have less or more than 16 digits.  You'll need to modify the code to accommodate those cards, if you need to accept them.  You can always refer to the [wikipedia page](https://en.wikipedia.org/wiki/Payment_card_number) for more information.

You might also want to add some RegEx (ew) to the code to filter out any spaces or dashes users might input.

With this code, you saw how you can break a problem down algorithmically, you saw how useful higher order functions like `reduce()` and `map()` are and you saw how little things like type coercion can work against you in sneaky ways.  The only way to really understand these concepts is to practice your ABC's.  Always Be Coding.

Happy coding.
