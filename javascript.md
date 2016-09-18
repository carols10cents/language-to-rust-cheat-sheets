# JavaScript to Rust Cheat Sheet

The goal of this is to have an easily-scannable reference for the most common syntax idioms in JavaScript and Rust so that programmers most comfortable with JavaScript can quickly get through the syntax differences and feel like they could read and write basic Rust programs.

What do you think? Does this meet its goal? If not, why not?

## Variables

### Hoisted variables

JavaScript:

```js
var foo = 1;
var bar = "hi";
var something_that_varies = 2;
something_that_varies += 1;
```

Rust:

Note that the concept of "hoisting" doesn't really exist in Rust; all variable bindings are scoped. See the [Variable Bindings section of the book](http://doc.rust-lang.org/book/variable-bindings.html) for more details.

```rust
let foo = 1;
let bar = "hi";
let mut something_that_varies = 2;
something_that_varies += 1;
```

### Constant immutable variables

JavaScript:

```JS
const myThing = "thing";
myThing = "bar"; // throws because of redefinition attempt!
```

Rust:

Note that by default, variables declared with `let` are immutable by default unless you use the `mut` keyword -- see the [Mutability section of the book](http://doc.rust-lang.org/book/mutability.html). Rust *also* has `const` and `static` that have different semantics than an immutable variable binding. See the [Const and Static section of the book](http://doc.rust-lang.org/book/const-and-static.html) for more detail.

```rust
const my_thing: i32 = 1;
my_thing = 2; // throws with error: invalid left-hand side expression
```

### Scoped variables

JavaScript:

```JS
if(true) {
  let thing = "";
}
let bar = thing; // throws, undefined
```

Rust:

```rust
if true {
  let thing = "";
}

let bar = thing; // error: unresolved name `thing`
```

## Functions

### Hoisted functions

JavaScript:

```js
// Function definition
function do_something(some_argument) {
  return some_argument + 1;
}
// Function use
var results = do_something(3);
```

Rust:

Note again that the concept of "hoisting" doesn't really exist in Rust; all function definitions are scoped.

```rust
// A function definition consists of:
// fn [function name]([argument list, consisting of name: type pairs]) -> [return type] {}
// So this function, named do_something, takes one argument named some_argument that is of type i32,
// and returns an i32.
// Since Rust is a systems language, you do have to think about and specify how much space you
// want your number types to take up-- i32 is a reasonable default, [see the rest in the reference](https://doc.rust-lang.org/nightly/reference.html#integer-literals).

fn do_something(some_argument: i32) -> i32 {
    some_argument + 1 // no semicolon in implicit return statements
}

// Function use
let results = do_something(3);
```

### Variable scoped function

JavaScript:

```JS
var doSomething = function (some_argument) {
  return some_argument + 1;
}

if(true) {
  doSomething(3);
}
```

Rust:

[See the closures section of the book](http://doc.rust-lang.org/book/closures.html).

```rust
// Closures/anonymous functions/lambdas are functions whose arguments are specified within pipes
// (|...|), still in argument name/type pairs, followed by the function definition.
let do_something = |some_argument: i32| {
  some_argument + 1
};

if true {
  do_something(3);
}
```


### Fat arrow and auto binding

JavaScript:

Autobinding of "this" scope via fat arrow syntax

```JS
var person = {
  name: "Jane",

  logHello: function (friends) {
    // If we instead made this `friends.forEach(function(friend) { ... })`, the output
    // would be undefined since `this` would be scoped to the inner function.
    friends.forEach((friend) => {
      console.log(this.name + " says hello to " + friend)
    });
  }
}

person.logHello(["John", "Sue"]);
// output:
// Jane says hello to John
// Jane says hello to Sue
```

Rust:

Note that Rust doesn't bind a context/scope like JavaScript does, so there isn't a need for a fat arrow syntax to opt out of the `this` binding.

```rust
struct Person {
    name: String,
}

// impl is short for implementation and is where you define methods on a struct.
impl Person {
    fn log_hello(self, friends: Vec<&str>) -> String {
        friends.iter().map(|friend| {
            // format! is a macro that works similarly to printf in C.
            // http://doc.rust-lang.org/nightly/std/fmt/index.html
            format!("{} says hello to {}", self.name, friend)
        }).collect::<Vec<_>>().join("\n")
    }
}

let j = Person { name: String::from("Jane") };
println!("{}", j.log_hello(vec!["John", "Sue"]));
// output:
// Jane says hello to John
// Jane says hello to Sue
```

### Currying functions

JavaScript:

```JS
function adder(a) {
  return b => a + b;
}

const add5 = adder(5);
console.log(add5(4)); // outputs 9
console.log(add5(2)); // outputs 7
```

Rust:

See [the section on closures from the book](http://doc.rust-lang.org/book/closures.html) for more info.

```rust
fn adder(a: i32) -> Box<Fn(i32) -> i32> {
    Box::new(move |b| a + b)
}

let add5 = adder(5);
println!("{}", add5(4)); // outputs 9
println!("{}", add5(2)); // outputs 7
```

## Exception handling

Exception handling in JavaScript is done using `try`/`catch` blocks and throwing and catching exceptions. Rust, however, does not throw exceptions-- instead, it has a `Result` value that can either be `Ok` or `Err` and you must explicitly handle both cases.

It turns out there are very few situations where JavaScript throws an exception and the Rust standard library returns a `Result`! Many cases where JavaScript throws an exception are compile-time errors in Rust. In many cases where Rust returns a `Result`, JavaScript would return `NaN` or similar non-exception values. There are crates that, for example, do parsing of dates or JSON that return `Result`s, but then the code wouldn't be easily runnable on [play.rust-lang.org](https://play.rust-lang.org/), and that's just not as fun :)

JavaScript:

```JS
try {
  let s = String.fromCodePoint(-1);
} catch (e) {
  console.log(e); // outputs "RangeError: -1 is not a valid code point"
  throw e
}
```

Rust:

```rust
match String::from_utf16(&[55296]) {
    Ok(s) => println!("Got a valid string: {}", s),
    Err(e) => println!("Got an error: {}", e),
}
// outputs "Got an error: invalid utf-16: lone surrogate found"
```

## Conditionals

JavaScript:

```js
if(x == 3) {
  // ...
} else if(x == 0) {
  // ...
} else {
  // ...
}
```

Less commonly in JavaScript, it seems to me, is the `switch` statement (not exactly equivalent since `case` uses `===`):

```js
switch (x) {
  case 3:
    // ...
    break;
  case 0:
    // ...
    break;
  default:
    // ...
    break;
}
```

Rust:

```rust
if x == 3 {
    // ...
} else if x == 0 {
    // ...
} else {
    // ...
}
```

More commonly used in Rust is pattern matching, which gives you other benefits:

```rust
match x {
    3 => {
        // ...
    },
    0 => {
        // ...
    },
    _ => {
        // ...
    }
}
```

## Output to the screen

In JavaScript, this is usually done using output to the JavaScript console:

```js
var x = 5;
console.log("x has the value " + x);
```

Rust:

```rust
let x = 5;
println!("x has the value {}", x);
```

## Lists of variable size

I'm not saying the word "array" on purpose :) Rust does have arrays, but they're fixed size, so you probably want a vector :)

JavaScript:

```js
var i = ["a", "b", "c"];
i.push("d");
console.log(i[1]); // outputs b
```

Rust:

```rust
// vec! is a macro that makes defining `Vec`s (vectors) easier and more similar to the syntax
// for defining arrays. Arrays in Rust have a fixed size, so if you don't know how many elements
// you will have at compile time, you should use a Vec.
let i = vec!["a", "b", "c"];
i.push("d");
println!("{}", i[1]); // outputs b
```

## Arrays and Vectors

### length

JavaScript:

```js
let list = [1, 2, 3];
console.log(list.length); // outputs 3
```

Rust:

```rust
let list = vec![1, 2, 3];
println!("{}", list.len()); // outputs 3
```

### join

JavaScript:

```js
let list = [1, 2, 3];
console.log(list.join("-")); // outputs "1-2-3"
```

Rust:

```rust
let list = vec!["1", "2", "3"];
println!("{}", list.join("-")); // outputs "1-2-3"

// Rust is stricter about types; to do this on a list of integers we need to do:
let list2 = vec![1, 2, 3];
println!("{}", list2.iter().map(|x| format!("{}", x)).collect::<Vec<_>>().join("-"));
```

### reverse

JavaScript:

```js
let list = [1, 2, 3];
console.log(list.reverse()); // outputs [3, 2, 1]
```

Rust:

```rust
let mut list = vec![1, 2, 3];
list.reverse();
println!("{:?}", list); // outputs [3, 2, 1]
```

### sort

JavaScript:

```js
let list = [1, 3, 2];
console.log(list.sort()); // outputs [1, 2, 3]

let list2 = [{foo: 1}, {foo: 3}, {foo: 2}]
console.log(list2.sort((prev, next) => prev.foo > next.foo)); // outputs [{foo: 1}, {foo: 2}, {foo: 3}]
```

Rust:

```rust
let mut list = vec![1, 3, 2];
list.sort();
println!("{:?}", list); // outputs [1, 2, 3]

// an annotation that automatically implements the Debug trait for our struct, which lets us
// use "{:?}" in println!
#[derive(Debug)]
struct Bar {
  foo: i32,
}
let mut list2 = vec![Bar {foo: 1}, Bar {foo: 3}, Bar {foo: 2}];
list2.sort_by(|a, b| a.foo.cmp(&b.foo));
println!("{:?}", list2); // outputs [Bar { foo: 1 }, Bar { foo: 2 }, Bar { foo: 3 }]
```

### push

JavaScript:

```js
let list = [1, 2, 3];
list.push(4);
console.log(list); // outputs [1, 2, 3, 4]
```

Rust:

```rust
let mut list = vec![1, 3, 2];
list.push(4);
println!("{:?}", list); // outputs [1, 2, 3, 4]
```

### pop

JavaScript:

```js
let list = [1, 2, 3];
let value = list.pop(); // mutates list to [1, 2]
console.log(value); // outputs 3
console.log(list); // outputs [1, 2]
```

Rust:

```rust
let mut list = vec![1, 2, 3];
let value = list.pop(); // mutates list to [1, 2]
println!("{:?}", value); // outputs Some(3)
println!("{:?}", list); // outputs [1, 2]
```

### shift

JavaScript:

```js
let list = [1, 2, 3];
let value = list.shift();
console.log(value); // outputs 1
console.log(list); // outputs [2, 3]
```

Rust:

See [Issue #10852](https://github.com/rust-lang/rust/issues/10852) if you're interested in the discussion of why Rust doesn't have shift/unshift.

```rust
let mut list = vec![1, 2, 3];
let value = list.remove(0);
println!("{:?}", value); // outputs 1
println!("{:?}", list); // outputs [2, 3]
```

### unshift

JavaScript:

```js
let list = [1, 2, 3];
let value = list.unshift(4);
console.log(value); // outputs 4
console.log(list); // outputs [4, 1, 2, 3]
```

Rust:

```rust
let mut list = vec![1, 2, 3];
list.insert(0, 4);
println!("{:?}", list); // outputs [4, 1, 2, 3]
```

### concat

JavaScript:

```js
let list = [1, 2, 3];
let newList = list.concat([4, 5, 6]);
console.log(list); // outputs [1, 2, 3]
console.log(newList); // outputs [1, 2, 3, 4, 5, 6]
```

Rust:

```rust
let list = vec![1, 2, 3];
let mut new_list = list.clone();
new_list.extend(vec![4, 5, 6]);
println!("{:?}", list); // outputs [1, 2, 3]
println!("{:?}", new_list); // outputs [1, 2, 3, 4, 5, 6]
```

### indexOf

JavaScript:

```js
let list = [1, 2, 3, 1];
console.log(list.indexOf(2)); // outputs 1

// using `from` argument
console.log(list.indexOf(1, 2)); // outputs 3
```

Rust:

```rust
let list = vec![1, 2, 3, 1];
println!("{:?}", list.iter().position(|x| *x == 2)); // outputs Some(1)
```

### forEach

JavaScript:

```js
let list = [1, 2, 3];
list.forEach(item => console.log(item));
```

Rust:

```rust
let list = vec![1, 2, 3];
for item in list {
  println!("{}", item);
}
```

### map

JavaScript:

```js
let list = [1, 2, 3];
let newList = list.map(x => x * x);
console.log(newList); // outputs [1, 4, 9]
```

Rust:

```rust
let list = vec![1, 2, 3];
let new_list = list.iter().map(|x| x * x);
// Iterators are lazy, so we have to consume them. One way is using
// `for i in list`, which has been used in other examples; using
// `collect` is another way.
println!("{:?}", new_list.collect::<Vec<_>>()); // outputs [1, 4, 9]
```

### filter

JavaScript:

```js
let list = [1, 2, 3, 4, 5];
console.log(list.filter(item => item % 2 === 0)); // outputs [2, 4]
```

Rust:

```rust
let list = vec![1, 2, 3, 4, 5];
println!("{:?}", list.iter().filter(|item| *item % 2 == 0).collect::<Vec<_>>()); // outputs [2, 4]
```

### reduce and reduceRight

JavaScript:

```js
let list = [1, 2, 3];
console.log(list.reduce((prev, curr) => prev - curr)); // outputs -4
console.log(list.reduceRight((prev, curr) => prev - curr)); // outputs 0
```

Rust:

```rust
// TODO
```

### some

JavaScript:

```js
let list = [1, 2, 3];
console.log(list.some((item) => item === 2)); // outputs true
console.log(list.some((item) => item < 0)); // outputs false
```

Rust:

```rust
// TODO
```

### every

JavaScript:

```js
let list = [1, 2, 3];
console.log(list.every((item) => item > 0)); // outputs true
console.log(list.every((item) => item % 2 === 0)); // outputs false
```

Rust:

```rust
// TODO
```

### find and findIndex

JavaScript:

```js
let list = [{foo: 1}, {foo: 3}, {foo: 2}];
let obj3 = list.find(item => item.foo === 2);
console.log(obj3); // outputs {foo: 2}
let index = list.findIndex(item => item.foo === 3);
console.log(index); // outputs 1
```

Rust:

```rust
// TODO
```

### fill

JavaScript:

```js
let list = [1, 2, 3].fill(4);
console.log(list); // outputs [4, 4, 4]
```

Rust:

```rust
// TODO
```

### entries and keys

JavaScript:

```js
let list = ["a", "b", , "c"];
for(let item of list.entries()) {
  console.log(item);
}
// outputs:
// [0, "a"]
// [1, "b"]
// [2, undefined]
// [3, "c"]
for(let item of list.keys()) {
  console.log(item);
}
// outputs:
// 0
// 1
// 2
// 3
```

Rust:

```rust
// TODO
```

### includes

JavaScript (ES7):

```js
let list = [1, 2, 3];
console.log(list.includes(2)); // outputs true
```

Rust:

```rust
// TODO
```

### Iterating over the elements in a list

JavaScript:

```js
var i = ["a", "b", "c"];
i.forEach(function(element, index, array) {
  console.log(element);
});
```

Rust:

```rust
let i = vec!["a", "b", "c"];
for j in i {
    println!("{}", j);
}
```

### Array.from(iterator)

JavaScript:

```js
console.log(Array.from("foo")); // outputs ["f", "o", "o"]
```

Rust:

```rust
// TODO
```

## Object literals

JavaScript:

```JS
const data = {
  prop: "value"
}
```

Rust:

```rust
// TODO
```

## Combining filter, map, reduce

JavaScript:

```JS
let list = ["", , "foo", 1, 2, 3];
let result = list.filter(item => typeof item === "number")
  .map(item => item * item)
  .reduce((prev, curr) => prev + curr);
console.log(result); // outputs 14
```

Rust:

```rust
// TODO
```

## Promises

JavaScript:

```
let resolvedPromise = Promise.resolve("value1");
let rejectedPromise = Promise.reject("value2");
let promise = new Promise((resolve, reject) => {
  if(true) {
    return resolve("yay!")
  }
  reject("boo!")
});
Promise.all([
  resolvedPromise,
  promise
]).then((values) => console.log("Then: " + values))
  .catch((values) => console.log("Catch: " + values));
// outputs "Then: value1,yay!"

Promise.all([
  resolvedPromise,
  rejectedPromise,
  promise
]).then((values) => console.log("Then: " + values))
  .catch((values) => console.log("Catch: " + values));
// outputs "Catch: value2"
```

Rust:

```rust
// TODO
```

## Generators

JavaScript:

```JS
function* idGenerator(key) {
  while(true) {
    yield key + "-" + Math.random();
  }
}
let idMaker = idGenerator("foo");
let id = idMaker.next().value;
console.log(id); // outputs, e.g., "foo-0.1325556532963984"
```

Rust:

```rust
// TODO
```

## Fetching data from the network

JavaScript:

```JS
fetch("someURL")
  .then(response => response.json())
  .then(processData);
```

Rust:

```rust
// TODO
```

## Async programming - async and await

JavaScript (ES7):

```JS
async function getData(url) {
  const response = await fetch(url);
  const data = await response.json();
  return data;
}

getData("https://api.github.com").then(data).catch(err => console.log("oh no!", err));
```

Rust:

```rust
// TODO
```

## JSON

JavaScript:

```JS
const obj = {"a": "b"};
const str = JSON.stringify(obj);
const newObj = JSON.parse(str);
```

Rust:

```rust
// TODO
```

## Constructing objects/classes + prototype

JavaScript:

```js
// TODO
```

Rust:

```rust
// TODO
```

## Traditional prototypical inheritance

JavaScript:

```JS
function MySuperThing() {
  MyThing.call(MyThing);
}
function MyThing() {}

MyThing.prototype = {
  doThing() {
    console.log("doing thing!");
  }
  get foo() {
  }
  _bar: null;
  get bar() {}
  set bar(value) {

  }
};

const superIntance = new MySuperthing();
```

Rust:

```rust
// TODO
```

### Default values

JavaScript:

```JS
function foo({x="hi"} = {}) {
  x;
}

foo({x: "hello"})
```

Rust:

```rust
// TODO
```

## Symbols

JavaScript:

```js
// TODO
```

Rust:

```rust
// TODO
```

## Classes

JavaScript:

```JS
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    console.log(this.name + ' makes a noise.');
  }
}

class Dog extends Animal {
  speak() {
    console.log(this.name + ' barks.');
  }
}
```

Rust:

```rust
// TODO
```

## Maps and WeakMaps

### Maps

JavaScript:

```
const map = new Map([["a", "b"]]);
map.size; // 1
map.get("a"); // "b"
map.has("a"); // true
map.set("c", "d");
map.delete("a");
map.keys(); // keys iterator
map.values(); // values iterator
map.clear(); // clears
map.entries(); // Array of arrays
```

Rust:

```rust
// TODO
```

### WeakMap

JavaScript:

```JS
const obj1 = {};
const obj2 = {};
const obj3 = {};
const weakMap = new WeakMap([[obj1, "hi"]]);

weakMap.has(obj1); // true
weakMap.get(obj1); // "hi"
weakMap.set(obj2, "hello");
weakMap.delete(obj1);
weakMap.clear();
```

Rust:

```rust
// TODO
```

## Set and WeakSet

### Set

JavaScript:

```JS
const set = new Set(["a", "b", "c"]);
set.size; // 3
set.has("d"); // false
set.add("d");
set.delete("a");
for(let entry of set.entries()){ console.log(entry); }
for(let value of set.values()){ console.log(value); }
for(let keys of set.keys()){ console.log(keys); }
set.clear();
```

Rust:

```rust
// TODO
```

### WeakSet

JavaScript:

```JS
const obj1 = {};
const obj2 = {};
const obj3 = {};
const weakSet = new WeakSet([obj1, obj2]);
weakSet.add(obj3);
weakSet.delete(obj1);
weakSet.has(obj1) // false
weakSet.clear();
```

Rust:

```rust
// TODO
```

## Template strings

JavaScript:

```JS
let name = "Marcos";
let b = `My name is ${name} and today is ${new Date().toUTCString()}`;
console.log(b); // outputs, e.g., My name is Marcos and today is Tue, 08 Dec 2015 21:27:00 GMT
```

Rust:

```rust
// TODO
```

## Modules

JavaScript:

```
import x as y from "someURL";
y();
```

Rust:

```rust
// TODO
```

## Rest and spread

JavaScript:

```js
// TODO
```

Rust:

```rust
// TODO
```

## Destructuring

JavaScript:

```JS
let whatever = {x: "hi", y: "hello", "z": hello};
const {x, ...props} = whatever;
console.log(x); // outputs "hi"
console.log(props); // outputs {y: "hello", "z": hello}
```

Rust:

```rust
// TODO
```

## Array comprehension

JavaScript:

```JS
let values = [1, 2, 3];
let newList = [x * x for x of values];
```

Rust:

```rust
// TODO
```

## Math

JavaScript:

```js
// TODO
```

Rust:

```rust
// TODO
```

### Random

JavaScript:

```js
// TODO
```

Rust:

```rust
// TODO
```

### Abs

JavaScript:

```js
// TODO
```

Rust:

```rust
// TODO
```

### Max

JavaScript:

```js
// TODO
```

Rust:

```rust
// TODO
```

### Min, etc.

JavaScript:

```js
// TODO
```

Rust:

```rust
// TODO
```

## Dates

JavaScript:

```js
// TODO
```

Rust:

```rust
// TODO
```

## setTimeout() and setInterval()

JavaScript:

```js
// TODO
```

Rust:

```rust
// TODO
```
