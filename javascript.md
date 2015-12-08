# JavaScript to Rust Cheat Sheet

The goal of this is to have an easily-scannable reference for the most common syntax idioms in JavaScript and Rust so that programmers most comfortable with JavaScript can quickly get through the syntax differences and feel like they could read and write basic Rust programs.

What do you think? Does this meet its goal? If not, why not?

## Variables

### hoisted variables
JavaScript:

```js
var foo = 1;
var bar = "hi";
var something_that_varies = 2;
something_that_varies += 1;
```

Rust:

```rust
let foo = 1i;
let bar = "hi";
let mut something_that_varies = 2;
something_that_varies += 1;
```

### constant immutable variables 

```JS
const myThing = "thing";
myThing = "bar"; // throws because of redefinition attempt! 
```

### Scoped variables 

```JS
if(aCondition){
  let thing = "" 
}
thing; //throws, undefined
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

```rust
// Function definition: takes an integer argument, returns an integer
fn do_something(some_argument: int) -> int {
    some_argument + 1 // no semicolon in implicit return statements
}

// Function use
let results = do_something(3);
```

### Variable scoped function

```JS
var doSomething = function (some_argument) {
  return some_argument + 1;
}

if(aCondition){
  doSomething();
}

```

### Fat arrow and auto binding
Autobinding of "this" scope via fat arrow syntax

```JS
const myList = 
const myThing{
  changeList(aList){
    return aList.map(item => item + "_changed!")
  }
}
```

### Currying functions

```JS
function adder(a){
  return b => a + b;
}

const add5 = adder(5)
console.log(add5(4)); // returns 9 
console.log(add5(2)); // returns 7 
```

## Exception handling

```JS
try{
  notAFunction();  
}catch(err){
  console.log(err)
  throw err;
}
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
let i = vec!["a", "b", "c"];
i.push("d");
println!("{}", i[1]); // outputs b
```

## Arrays and Vectors

### length
```js
let list = [1,2,3];
list.length; //3
```

### join
```js
let list = [1,2,3];
list.join("-"); "1-2-3";
```
### reverse
```js
let list = [1,2,3];
list.reverse(); //[3,2,1]
```
### sort
```js
let list = [1,3,2];
list.sort(); //[1,2,3]

let list2 = [{foo: 1},{foo: 3},{foo: 2}]
list.sort((prev, next) => prev.foo > next.foo)
```
### push
```js
let list = [1,2,3];
list.push(4); //adds 4
```
### pop
```js
let list = [1,2,3];
let value = list.pop(); //mutates list to [1,2]
```

### shift
```js
let list = [1,2,3];
```

### unshift
```js
let list = [1,2,3];
```
### concat
```js
let list = [1,2,3];
let newList = list.concat([4,5,6]); // [1,2,3,4,5,6]
```
###lastIndexOf and indexOf
```js
let list = [1,2,3,1];
list.indexOf(2); // 1

// using `from` argument
list.indexOf(1, 2); // 3
```

### forEach
```js
let list = [1,2,3];
list.forEach(item => console.log(i));
```

### map
```js
let list = [1,2,3];
let newList = list.map(x => x * x);
```

### filter
```js
let list = [1, "a", 2, "b" ,3];
list.filter(item => typeof item === "string"); //["a", "b"]
```

### reduce and reduceRight
```js
let list = [1,2,3];
list.reduce()
```

### some
```js
let list = [1,2,3];
list.some((item) => item === 2); // true
```

### every
```js
let list = [1,2,3];
list.every((item) => item > 0); // true
```
### find and findIndex
```js
let list = [{foo: 1},{foo: 3},{foo: 2}];
let obj3 = list2.find(item => item.foo === 2);
let index = list2.findIndex(item => item.foo === 3); // 2

```
### fill
```js
let list = [1, 2, 3].fill(4); // [4, 4, 4]
```

### entries and keys
```js
let list = [1,2,3];
for(let item of list.entries()){
  console.log(entries);
}
```
### includes
```js
let list = [1,2,3];
list.includes(2); //true
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
for j in i.iter() {
    println!("{}", j);
}
```
### Array.from(iterator)



## Object literals 

```JS 
const data = {
  prop: "value"
}
```

## 

## Combining filter, map, reduce

```JS
let list = ["", ,"foo", 1, 2, 3];
list.filter(item => typeof item === "number")
  .map(item => item * item)
  .reduce()

```

## Promises 

```
const resolved = Promise.resolve("value");
const rejected = Promise.reject("value");
const promises = Promise((resolve, reject)=>{
  if(someCondition){
    return resolve("yay!")
  }
  reject("boo!")
});
```

## Generators

```JS
function* idGenerator(key){
  while(true){
    yield key + "-" + Math.random();
  }
}
let idMaker = idGenerator("Foo");
let id = idGenetor.next().value; //e.g., "foo-0.1325556532963984"
```

## Fetching data from the network

```JS
fetch("someURL")
  .then(response => response.json())
  .then(processData);
```

## Async programming - async and await 

```JS
async getData function(url){
  const response = await fetch(url)
  const data = await data.json();
  return data;
}

getData.then(data).catch(err => console.log("oh no!", err));
```

## JSON

```JS 
const obj = {"a": "b"};
const str = JSON.stringify(obj);
const newObj = JSON.parse(srt);
```

## constructing objects/classes + prototype

## Traditional prototypical inheritance

```JS
function MySuperThing(){
  MyThing.call(MyThing);
}
function MyThing(){}

MyThing.prototype = {
  doThing(){
    console.log("doing thing!")
  }
  get foo(){
  }
  _bar: null;
  get bar(){}
  set bar(value){
  
  }
};

const superIntance = new MySuperthing(); 
```

### Default values

function foo({x="hi"} = {}){
  x;
}

foo({x: "hello"})

## Symbols

## Classes

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


## Maps and WeakMaps

### Maps
```
const map = new Map([["a", "b"]]);
map.size; //1
map.get("a"); // "b"
map.has("a"); // true
map.set("c", "d");
map.delete("a");
map.keys(); // keys iterator
map.values(); // values iterator
map.clear(); // clears
map.entries(); // Array of arrays
```

## WeakMap

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

## Set and WeakSet

### Set
```JS
const set = new Set(["a", "b", "c"]);
set.size; //3
set.has("d"); //false
set.add("d");
set.delete("a");
for(let entry of set.entries()){console.log(entry);}
for(let value of set.values()){console.log(value);}
for(let keys of set.keys()){console.log(keys);}
set.clear();
```

### WeakSet
```JS
const obj1 = {};
const obj2 = {};
const obj3 = {};
const weakSet = new WeakSet([obj1, obj2]);
weakSet.add(obj3)
weakSet.delete(obj1)
weakSet.has(obj1) // false
weakSet.clear();
```

## Template strings

```JS
let name = "Marcos";
//e.g., My name is Marcos and today is Tue, 08 Dec 2015 21:27:00 GMT
let b = `My name is ${name} and today is ${new Date().toUTCString()}`;
```

## Modules 

```
import x as y from "someURL";
y();
```


## Rest and spread


## Destructuring
```
let whatever = {x: "hi", y: "hello", "z": hello}
const {x, ...props} = whaterver;
x; // "hi"
props; //{y: "hello", "z": hello}; 
```

## Array comprehension 

```JS
let values = [1,2,3]; 
let newList = [x * x for x of values];
```

## Math random, abs, max, min, etc.

## Dates

## setTimeout() and setInterval()


