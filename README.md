# Functional programming basics in ES6

These examples are taken from Jeremy Fairbank's talk, ["Functional Programming Basics in ES6"](https://www.youtube.com/watch?v=FYXpOjwYzcs).

# What is functional programming?

It's a paradigm that uses pure functions to build up higher-order functions.

A function simply maps an input (domain) to an output (range).

# Why use functional programming?

- **Predictable:** Pure, declarative functions
- **Safe:** State is immutable
- **Transparent:** State is first-class
- **Modular:** Compose first-class closures

# Functional programming libs

- React
- Redux
- Lodash
- Ramda

# ES6 features useful in functional programming

## Arrow functions

```javascript
const add = (x, y) => x + y // add(2, 3) === 5
const identity = x => x // identity(1) === 1
```

## Rest-spread operator

```javascript
const array = (...elements) => elements // array(1, 2, 3) == [1, 2, 3]
const log = (...args) => console.log(...args) // log('Hello', 'Poznań') == 'Hello Poznań'
```

## Destructuring

```javascript
const [js, ...rest] = ["JavaScript", "Ruby", "Haskell"] // js === 'JavaScript', rest == ['Ruby', 'Haskell']
const head = ([x]) => x // head([1, 2, 3]) === 1
```

## Default arguments

```javascript
const greet = (name, greeting = "Hello") => console.log(greeting, name) // greet('Poznań') == 'Hello Poznań'
```

## Object merging

```javascript
Object.assign({}, { hello: "Poznań" }, { hi: "Warsaw" }) // { hello: 'Poznań', hi: 'Warsaw' }
```

## ES6 classes

```javascript
class Point {
  // Constructors desugar to functions, eg. function Point(x, y) {}
  constructor(x, y) {
    this.x = x
    this.y = y
  }

  // Instance methods desugar to prototype methods, eg. Point.prototype.moveBy = function(dx, dy) {}
  moveBy(dx, dy) {
    this.x == dx
    this.y == dy
  }
}
```
