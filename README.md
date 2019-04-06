# Functional programming basics in ES6

These notes are taken from Jeremy Fairbank's talk, ["Functional Programming Basics in ES6"](https://www.youtube.com/watch?v=FYXpOjwYzcs).

## What is functional programming?

It's a paradigm that uses pure functions to build up higher-order functions.

A function simply maps an input (domain) to an output (range).

## Why use functional programming?

- **Predictable:** Pure, declarative functions
- **Safe:** State is immutable
- **Transparent:** State is first-class
- **Modular:** Compose first-class closures

## Functional programming libs

- React
- Redux
- Lodash
- Ramda

## ES6 features useful in functional programming

### Arrow functions

```javascript
const add = (x, y) => x + y // add(2, 3) === 5
const identity = x => x // identity(1) === 1
```

### Rest-spread operator

```javascript
const array = (...elements) => elements // array(1, 2, 3) == [1, 2, 3]
const log = (...args) => console.log(...args) // log('Hello', 'Poznań') == 'Hello Poznań'
```

### Destructuring

```javascript
const [js, ...rest] = ["JavaScript", "Ruby", "Haskell"] // js === 'JavaScript', rest == ['Ruby', 'Haskell']
const head = ([x]) => x // head([1, 2, 3]) === 1
```

### Default arguments

```javascript
const greet = (name, greeting = "Hello") => console.log(greeting, name) // greet('Poznań') == 'Hello Poznań'
```

### Object merging

```javascript
Object.assign({}, { hello: "Poznań" }, { hi: "Warsaw" }) // { hello: 'Poznań', hi: 'Warsaw' }
```

### ES6 classes

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

# Predictable: Pure, declarative functions

Pure functions respect the following criteria:

- No side effects (including mutations and printing)
- No dependencies (including global state)
- Idempotent (inputs always map to the same outputs, regardless of how many times the function is called)

To illustrate the benefits of pure functions, consider these impure functions:

```javascript
let name = "Alice"

const getName = () => name // Depends on global variable

const setName = newName => (name = newName) // Mutates state

const printUpperName = () => console.log(name.toUpperCase()) // Depends on global variable _and_ mutates state
```

These functions are difficult to test:

```javascript
describe("api", () => {
  beforeEach(() => mockConsoleLog())
  afterEach(() => restoreConsoleLog())

  it("sets and prints the name", () => {
    printUpperName()

    expect(console.log).calledWith("ALICE")

    setName("Bob")
    printUpperName()

    expect(console.log).calledWith("BOB")
  })
})
```

How can we rewrite this example as a pure function with tests?

```javascript
const upperName = name => name.toUpperCase()

describe("api", () => {
  it("returns an uppercase name", () => {
    expect(upperName("Alice").to.equal("ALICE"))
    expect(upperName("Bob").to.equal("BOB"))
  })
})
```

An **imperative** function describes _how to achieve the result_. Consider this function:

```javascript
const doubleNumbers = numbers => {
  const doubled = []

  for (let i = 0; i < numbers.length; i++) {
    doubled.push(numbers[i] * 2)
  }

  return doubled
}

// doubleNumbers([1, 2, 3]) == [2, 4, 6]
```

A **declarative** function declares _what the desired result is_. To rewrite the above function declaratively:

```javascript
const doubleNumbers = numbers => numbers.map(n => n * 2)

// doubleNumbers([1, 2, 3]) == [2, 4, 6]
```
