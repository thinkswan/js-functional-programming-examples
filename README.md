# Functional programming basics in ES6

These notes are taken from Jeremy Fairbank's talk, ["Functional Programming Basics in ES6"](https://www.youtube.com/watch?v=FYXpOjwYzcs).

## What is functional programming?

It's a paradigm that uses pure functions to build up higher-order functions.

A function simply maps an input (domain) to an output (range).

## Why use functional programming?

- **Predictable:** Pure, declarative functions
- **Safe:** State is immutable
- **Transparent:** State is first-class
- **Modular:** Compose first-class functions

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

## Predictable: Pure, declarative functions

Pure functions respect the following criteria:

- No side effects (including mutations and printing)
- No dependencies (including global state)
- Idempotent (inputs always map to the same outputs, regardless of how many times the function is called)

To illustrate the benefits of pure functions, consider these impure functions:

```javascript
let name = "Alice"

// Depends on global variable
const getName = () => name

// Mutates state
const setName = newName => (name = newName)

// Depends on global variable _and_ mutates state
const printUpperName = () => console.log(name.toUpperCase())
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

doubleNumbers([1, 2, 3]) // [2, 4, 6]
```

A **declarative** function declares _what the desired result is_. To rewrite the above function declaratively:

```javascript
const doubleNumbers = numbers => numbers.map(n => n * 2)

doubleNumbers([1, 2, 3]) // [2, 4, 6]
```

## Safe: State is immutable

State should be created, not mutated.

To illustrate the benefits of immutable state, consider this example:

```javascript
const hobbies = ["programming", "reading", "music"]

const firstTwo = hobbies.splice(0, 2) // ['programming', 'reading']

console.log(hobbies) // ['music']
```

One way to enforce immutable state is to use `Object#freeze`:

```javascript
const hobbies = Object.freeze[("programming", "reading", "music")]

const firstTwo = hobbies.splice(0, 2) // TypeError
```

Another approach is to free the state. Consider the `Point` class from earlier:

```javascript
class Point {
  constructor(x, y) {
    this.x = x
    this.y = y
  }

  moveBy(dx, dy) {
    this.x == dx
    this.y == dy
  }
}

const point = new Point(0, 0)

point.moveBy(5, 5)
point.moveBy(-2, 2)

console.log([point.x, point.y]) // [3, 7]
```

We can free this class' state as follows:

```javascript
const createPoint = (x, y) => Object.freeze([x, y])

cosnt movePointBy = ([x, y], dx, dy) => Object.freeze([x + dx, y + dy])

let point = createPoint(0, 0)

point = movePointBy(point, 5, 5)
point = movePointBy(point, -2, 2)

console.log(point) // [3, 7]
```

Since immutable state requires us to return new data structures with each call, it has some pros and cons:

- Pros
  - Safety
  - Free undo/redo logs (eg. Redux)
  - Explicit flow of data
  - Less memory usage (in some cases)
  - Concurrency safety (in some cases)
- Cons
  - Verbose
  - More object creation
  - More garbage collections
  - More memory usage

## Modular: Compose first-class functions

JavaScript treats functions as first-class citizens. This means you can assign them to variables, pass them as input, and receive them as ouput, just like you can a boolean, number, or string.

Before continuing, we should define a couple of terms:

- **Higher-order functions** return a new function.
- **Closures** encapsulate state.
- **Partially-applied functions** return a new function with 1 or more of the inputs set (similar to `bind`).
- **Curryable functions** are functions that can be partially-applied and will invoke once all inputs are set.

Consider the following example:

```javascript
// This function is both a higher-order function (because it returns a new function) and a closure (because it "closes over `x`")
const createAdder = x => y => x + y

// This function is a partially-applied function (because it applies `x = 3`, but not `y``)
const add3 = createAdder(3)

add3(2) // 5
add3(3) // 6
```

Perhaps a more practical example:

```javascript
const request = options => {
  return fetch(options.url, options).then(resp => resp.json())
}

const usersPromise = request({
  url: "/users",
  headers: { "X-Custom": "myKey" }
})
const tasksPromise = request({
  url: "/tasks",
  headers: { "X-Custom": "myKey" }
})
```

We can make this more reusable as follows:

```javascript
const createRequester = options => {
  return otherOptions => request(Object.assign({}, options, otherOptions))
}

const customRequest = createRequester({ headers: { "X-Custom": "myKey" } })

const usersPromise = customRequest({ url: "/users" })
const tasksPromise = customRequest({ url: "/tasks" })
```

Moving on to curryable functions, let's recreate the adder and requester from above:

```javascript
const add = x => y => x + y
const request = defaults => options => {
  options = Object.assign({}, defaults, options)

  return fetch(options.url, options).then(resp => resp.json())
}
```

With the building blocks of higher-order functions, closures, partially-applied functions, and curryable functions, let's look at a shopping cart example:

```javascript
const map = fn => array => array.map(fn)
const multiply = x => y => x * y
const pluck = key => object => object[key]

const discount = multiply(0.98)
const tax = multiply(1.0925)
const customRequest = request({ headers: { "X-Custom": "myKey" } })

customRequest({ url: "/cart/items" }) // [{ price: 5 }, { price: 10 }, { price: 3 }]
  .then(map(pluck("price"))) // [5, 10, 3]
  .then(map(discount)) // [4.9, 9.8, 2.94]
  .then(map(tax)) // [5.35, 10.71, 3.21]
```

We can also compose closures:

```javascript
const processWord = compose(
  hyphenate,
  reverse,
  toUpperCase
) // Same as `word => hyphenate(reverse(toUpperCase(word)))`

const words = ["hello", "functional", "programming"]

const newWords = words.map(processWord) // ['OL-LEH', 'LANOI-TCNUF', 'GNIMM-ARGORP']
```

To improve the performance of the shopping cart example, we can replace the 3 `map` interations with a single iteration:

```javascript
customRequest({ url: "/cart/items" }) // [{ price: 5 }, { price: 10 }, { price: 3 }]
  .then(
    map(
      compose(
        tax,
        discount,
        pluck("price")
      )
    )
  ) // [5, 10, 3] => [4.9, 9.8, 2.94] => [5.35, 10.71, 3.21]
```

Finally, to handle loops in functional programming, we can use recursion. Consider a function that solves a factorial:

```javascript
const factorial = n => {
  let result = 1

  while (n > 1) {
    result *= n
    n--
  }

  return result
}
```

To rewrite this recursively:

```javascript
const factorial = n => {
  if (n < 2) return 1

  return n * factorial(n - 1)
}
```

To avoid exceeding the call stack size, we can optimize this further using tail call optimization:

```javascript
const factorial = (n, accum = 1) => {
  if (n < 2) return accum

  return factorial(n - 1, n * accum)
}
```
