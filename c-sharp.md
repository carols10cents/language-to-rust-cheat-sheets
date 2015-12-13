Thanks to @seejee for making this for me!!!

# C# to Rust Cheat Sheet

The goal of this is to have an easily-scannable reference for the most common syntax idioms in C# and Rust so that programmers most comfortable with C# can quickly get through the syntax differences and feel like they could read and write basic Rust programs.

What do you think? Does this meet its goal? If not, why not?

## Variables

C#:

```csharp
var foo = 1;
var bar = "hi";
var somethingThatVaries = 2;
somethingThatVaries += 1;
```

Rust:

```rust
let foo = 1;
let bar = "hi";
let mut something_that_varies = 2;
something_that_varies += 1;
```

## Functions

C#:

```csharp
// Function definition: takes an integer argument, returns an integer
static int DoSomething(int some_argument)
{
  return some_argument + 1;
}

// Function use
var results = DoSomething(3);
```

Rust:

```rust
// Function definition: takes an integer argument, returns an integer
fn do_something(some_argument: i32) -> i32 {
    some_argument + 1 // no semicolon in implicit return statements
}

// Function use
let results = do_something(3);
```

## Conditionals

C#:

```csharp
if (x == 3)
{
  // ...
}
else if (x == 0)
{
  // ...
}
else
{
  // ...
}
```

```csharp
// Or using a switch:
switch(x) {
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

C#:

```csharp
var x = 5;
System.Console.WriteLine("x has the value {0}", x);
```

Rust:

```rust
let x = 5;
println!("x has the value {}", x);
```

## Lists of variable size

I'm not saying the word "array" on purpose :) This is how to do Arrays that can grow in size.

C#:

```csharp
var i = new List<String>() { "a", "b", "c" };
i.Add("d");
Console.WriteLine(i[1]); // outputs b
```

Rust:

```rust
let i = vec!["a", "b", "c"];
i.push("d");
println!("{}", i[1]); // outputs b
```

## Iterating over the elements in a list

C#:

```csharp
var i = new List<String>() { "a", "b", "c" };

foreach(var j in i) {
  Console.WriteLine(j);
}

i.ForEach((j) => Console.WriteLine(j));
```

Rust:

```rust
let i = vec!["a", "b", "c"];
for j in i {
    println!("{}", j);
}
```

## Tests

C#: (NUnit)

```csharp
using NUnit.Framework;

[TestFixture]
public class SomeTestClass
 {
   [Test]
   public void SomeTest()
    {
      Assert.AreEqual(1, 1); //will pass
      Assert.AreEqual(1, 2); //will fail
    }
  }
}
```

Rust:

```rust
#[test]
fn some() {
    assert!(true); // will pass
    assert_eq!("expected", "actual"); // will fail
}
```

## Encapsulation of data + behavior

I'm not saying the word "object" on purpose :)

C#:

```csharp
class Highway {
  public int Length     {get; private set;}
  public int SpeedLimit {get; private set;}

  public Highway(int length, int speedLimit) {
    this.Length = length;
    this.SpeedLimit = speedLimit;
  }

  public int TimeToTravel() {
    return Length / SpeedLimit;
  }
}

new Highway(325, 65).TimeToTravel(); // returns 5
```

Rust:

```rust
struct Highway {
    length: i32,
    speed_limit: i32,
}

impl Highway {
    fn time_to_travel(&self) -> i32 {
        self.length / self.speed_limit
    }
}

Highway { length: 325, speed_limit: 65 }.time_to_travel() // returns 5
```
