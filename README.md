## Review Promises

```js
axios
  .get("https://api.chucknorris.io/jokes/random")
  .then((res) => {
    console.log("Did you know", res.data.value);
  })
  .catch((error) => {
    console.error("Couldn't reach the API");
  });
```

## Problem: Nesting for calls that must be sequential

Let's say there is an API that only allows a maximum of 1 call per second. So you must make your multiple calls sequentially.

Something like this:

```js
axios
  .get("https://api.chucknorris.io/jokes/random")
  .then((joke1) => {
    axios
      .get("https://api.chucknorris.io/jokes/random")
      .then((joke2) => {
        axios
          .get("https://api.chucknorris.io/jokes/random")
          .then((joke3) => {
            console.log(
              "Here are three jokes:",
              joke1.data.value,
              joke2.data.value,
              joke3.data.value
            );
          })
          .catch((error) => {
            console.error("Couldn't reach the API");
          });
      })
      .catch((error) => {
        console.error("Couldn't reach the API");
      });
  })
  .catch((error) => {
    console.error("Couldn't reach the API");
  });
```

There's got to be a better way

### Solution 1: Promise.all

Takes in an array of promises and returns a promise which will resolve to an array of values.

```js
Promise.all([
  axios.get("https://api.chucknorris.io/jokes/random"),
  axios.get("https://api.chucknorris.io/jokes/random"),
  axios.get("https://api.chucknorris.io/jokes/random"),
])
  .then((res) => {
    console.log(
      "Here are three jokes:",
      res[0].data.value,
      res[1].data.value,
      res[2].data.value
    );
  })
  .catch((error) => {
    console.error(error);
  });
```

The caveat? All 3 calls will happen in parallel.


### Solution 2 : async await

New language feature, available in functions marked as async.


```js
async function main() {
  // Async code
}

const main = async () => {
  // Async code
};
```

Inside an async function await can be used to wait on a promise and get access to the resolved value.

Reconsider this code

```js
function printChuckNorrisJoke() {
  axios
    .get("https://api.chucknorris.io/jokes/random")
    .then((res) => {
      console.log("Did you know", res.data.value);
    })
    .catch((error) => {
      console.error("Couldn't reach the API");
    });
}
```

This can be rewritten like this

```js
async function printChuckNorrisJoke() {
  try {
    const res = await axios.get("https://api.chucknorris.io/jokes/random");
    console.log(res.data.value);
  } catch (error) {
    console.error("Couldn't reach the API");
  }
}
```

If you return a value from an async function it will be wrapped in a promise automatically
This is true even if no await is happening inside the async function.

```js
async function tossCoin() {
  return Math.random() < 0.5;
}

tossCoin().then((heads) => console.log(heads ? "Heads" : "Tails"));
```

### Nesting Solution 1

```js
async function printThreeChuckNorrisJokes() {
  try {
    const joke1 = await axios.get("https://api.chucknorris.io/jokes/random");
    const joke2 = await axios.get("https://api.chucknorris.io/jokes/random");
    const joke3 = await axios.get("https://api.chucknorris.io/jokes/random");
    console.log(joke1.data.value);
    console.log(joke2.data.value);
    console.log(joke3.data.value);
  } catch (error) {
    console.error("Couldn't reach the API");
  }
}
```

### Nesting Solution 2

```js
async function printChuckNorrisJokes(count) {
  try {
    const jokes = [];

    for (let i = 0; i < count; ++i) {
      jokes.push(await axios.get("https://api.chucknorris.io/jokes/random"));
    }
    console.log(jokes.map((res) => res.data.value).join("\n"));
  } catch (error) {
    console.error("Couldn't reach the API");
  }
}
```

Ahh, there we go. Nice and sequential. Bonus challenge: can you make the above code cleaner by using a reducer?


## Challenge

https://github.com/devhausleipzig/async-await-exercise