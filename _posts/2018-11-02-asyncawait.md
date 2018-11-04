---
layout: post
title:  "Async/Await and Promises"
date:   2018-11-02 10:51:05 -0400
categories: code javascript webdev
---

Today's topic is my new found love of Async/Await with Promises.  Async/Await are the greatest thing to come to JavaScript since sliced bread.  The beauty in the structure is that you can read your code from top to bottom, and you can think about your code from top to bottom and it will run the way you read and think about it.

Let me explain.  First, you have your basic Promise.  A [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise), if you don't know, is a JavaScript object that will set out to complete some asynchronous task and then return at some point in the future with a result or an error.  Promises are used primarily for fetching data from a RESTful API, usually through `fetch()`, but that's hardly their only purpose.  Promises solve a lot of problems, namely, the issue of callback hell.  This isn't to say Promises don't have call backs, they do, but they aren't nested in each other.  Furthermore, with `.catch()` you have a one stop shop for all your errors.  If you've chained a whole bunch of promises together and one of them goes boom, you can catch it in one spot.

Promises are incredibly useful for handling many of JavaScript's asynchronous APIs, even the ones that are not promises can be turned into promises with a little help.  Here I'll give an example from one of my projects where we make use of the `geolocation` object to get a user's location:

(In this example, it is a property of an exported object)

```javascript
getLocalPosition: ()=> {
  return new Promise((resolve, reject)=> {
    if('geolocation' in navigator) {
      navigator
        .geolocation
        .getCurrentPosition(
           (position)=> resolve(position), 
           (error)=> reject(error)
        );
    } else {
        reject(new GeoError(`Your browser doesn't support geolocation`));
    }
  });
}
```
So what's going on here?  Well, first I'm creating and returning a new Promise.  Promises take a call back and they feed that call back two functions, `resolve()` and `reject()`.  `resolve()` is the happy path, it is the method you want called when the desired outcome occurs.  `reject()` is for any errors and is automatically sent to either a `.catch()` statement, or the catch block of a `try...catch`.

Inside the function, I'm checking that the `navigator` object has a `geolocation` object and if so, I grab the location of the user with `getCurrentPosition()`.  This method takes three callbacks, two of which I'm using.  The first call back gives you a position object you can do something with, the second gives you an error and the third gives you some options to play with (I decided to go for the defaults and pass nothing.)  In the first callback, I pass the position object to `resolve()` and `reject()` the error object.

In this way, I've turned the .getCurrentPosition() method into a Promise.

But what does this have to do with async/await?  Well, to understand async/await, it is necessary to understand Promises.  After all, async/await only work with promises.  Thus the following would have been invalid (and yes, I tried it):

```javascript
getLocalPosition: async ()=> {
  const position = await navigator
                    .geolocation
                    .getCurrentPosition(
                       position=> position, 
                       error => error
                     );
```
Even though `getCurrentPosition()` is asynchronous, it is not a Promise.  But since I turned it into a Promise with the code in my first example, we can use async/await.

I used this code in a function that grabs information from Open Weather Map's API.  In this function, I actually had three asynchronous calls.  The first goes to a server to grab an API key, the second calls my `getLocalPosition()` method to get the user's location, and the third finally does the call to Open Weather Map.

Here's what it looks like:

```javascript
getLocalWeather: async function(city=null, countryCode=null) {
  try {
    const API = await fetch('http://localhost:8000')
                       .then(response=> response.json())
                       .then(data=> data)
                       .catch(error=> console.error(error));
    const currentLocation = await GeoLoc.getLocalPosition()
                                   .then(position=>position);
    const url = this.URI
      +`/data/2.5/weather?`
      +`lat=${currentLocation.coords.latitude}`
      +`&lon=${currentLocation.coords.longitude}`
      +`&APPID=${API.key}`;
    return await fetch(url)
                  .then(response=> response.json())
                  .then(data=> data)
                  .catch(error=> console.error(error));
     } catch(error) {
       console.error(error);
  }
}
```
You'll notice that the enclosing function, `getLocalWeather()` is marked as `async`.  This tells the JavaScript interpreter that the function may contain one or more `await` statements and when it encounters those statements, it will stop at that line and await the completion of whatever is on that line.  This is tremendously powerful because I can simply write my code from top to bottom in the order as it happens.  I don't have to nest call backs.  I don't have to enclose my code in a callback inside a `.then()`  method.  I can simply use async/await and run the code from top to bottom.  This is insanely useful.

It reads better, it flows better.  It's like you're writing regular synchronous JavaScript.  You're not, but it's like you are.

And don't limit yourself to network calls either when thinking asynchronously.  With Promises and Async/Await, it's easy to make anything asynchronous.  As an example, I have a snake game I've been working on.  The code I'm about to show is not yet complete, but it'll serve my purpose to talk about promises a little more.  Every game has a game loop that runs the game itself.  Before the game loop, you have some set up and after the game loop, you have some tear down.  In my case, I want to display a text that says "game over" and your final score.  Here's the code:

```javascript
async start() {
    let gameLoop;
    const pointsOnBoundary = p => p.x < this.bounds.width && p.y < this.bounds.height;
    const random = (len) => Math.floor(Math.random() * len);
    let position = {
        x: 0,
        y: 0
    };
    const filteredCoords = this.coords.filter(pointsOnBoundary);
    let {
        x,
        y
    } = filteredCoords[random(filteredCoords.length)];
    this.snake = new Snake(y, x, 16);
    const startAnimation = (resolve) => {
        if (!this.snake || !this.snake.alive) {
            cancelAnimationFrame(gameLoop);
            resolve('Game Over');
            return;
        }
        if (!this.pellet) {
            let {
                x,
                y
            } = filteredCoords[random(filteredCoords.length)];
            this.pellet = new Pellet(x, y, 16);
        }
        position.x += this.snake.speed.x;
        position.y += this.snake.speed.y;
        this.snake.div.style.top = position.y + 'px';
        this.snake.div.style.left = position.x + 'px';
        this.snake.detectPellet(this.pellet);
        this.snake.detectEdge(this.bounds.width, this.bounds.height);
        if (this.pellet.touched) {
            this.board.removeChild(this.pellet.div);
            this.pellet = null;
        }
        this.scoreBoard.setScore(this.snake.score);
        gameLoop = requestAnimationFrame(() => startAnimation(resolve));
    }
    const endgame = await new Promise(startAnimation)
        .then(endgame => endgame);
    const gameOverDiv = document.createElement('div');
    gameOverDiv.id = 'game-over';
    gameOverDiv.innerHTML = `
        <p>${endgame}</p>
        <p><small>Your score was ${this.scoreBoard.score}</small></p>
        `;
    this.board.appendChild(gameOverDiv);
}
```
Here's the source on Github, note this code may change from after I've written this post.  [https://github.com/jckuhl/snakejs/blob/master/scripts/board.js](https://github.com/jckuhl/snakejs/blob/master/scripts/board.js)

Here's what makes this function work.  `start()` starts the game.  It sets it up, it defines `startAnimation()` and then calls the function as the callback of a new Promise.  `startAnimation()` therefore takes `resolve()` as it's sole parameter (and I have no use in this scenario for `reject()`)  It will then run everything in a recursive loop using `requestAnimationFrame()`.  This will keep going until `this.snake.alive` is false.  If the player is dead (he touched his tail or the edge of the map) then `resolve()` will be called.  When I pass this function into a Promise, the `await` keyword halts the rest of `start()`.  I can write out all the code that I want to happen at the end of the game and it will wait for the game to end.  If I had not used `await`, then the `gameOverDiv` would be created and displayed immediately.  I would have had to have done all my end game code up where I had `cancelAnimationFrame()` and that would be a weird, non-semantic, place to put it.  I could have factored it out into a new function, but it would still have been weird.  It wouldn't follow the regular flow of the game.  Instead, by using async/await and a promise, `start()` reads clearly from up to down.  Set up the game, play the game, end the game.

I hope this gave you a taste for what async/await can do for your code.

Happy Coding.