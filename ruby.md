# Ruby to Rust Cheat Sheet

The goal of this is to have an easily-scannable reference for the most common syntax idioms in Ruby and Rust so that programmers most comfortable with Ruby can quickly get through the syntax differences and feel like they could read and write basic Rust programs.

What do you think? Does this meet its goal? If not, why not?

## Variables

Ruby:

```ruby
foo = 1
bar = "hi"
something_that_varies = 2
something_that_varies += 1
```

Rust:

```rust
let foo = 1;
let bar = "hi";
let mut something_that_varies = 2;
something_that_varies += 1;
```

## Functions

Ruby:

```ruby
# Function definition
def do_something(some_argument)
  some_argument + 1
end

# Function use
results = do_something(3)
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

Ruby:

```ruby
if x == 3
  # ...
elsif x == 0
  # ...
else
  # ...
end
```

Less commonly in Ruby, it seems to me, is the `case` statement (not exactly equivalent since `when` uses `===`):

```ruby
case x
when 3
  # ...
when 0
  # ...
else
  # ...
end
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

Ruby:

```ruby
x = 5
puts "x has the value #{x}"
```

Rust:

```rust
let x = 5;
println!("x has the value {}", x);
```

## Lists of variable size

I'm not saying the word "array" on purpose :) Rust does have arrays, but they're fixed size, so you probably want a vector :)

Ruby:

```ruby
i = ["a", "b", "c"]
i << "d"
puts i[1] # outputs b
```

Rust:

```rust
let i = vec!["a", "b", "c"];
i.push("d");
println!("{}", i[1]); // outputs b
```

## Iterating over the elements in a list

Ruby:

```ruby
i = ["a", "b", "c"]
i.each do |j|
  puts j
end
```

Rust:

```rust
let i = vec!["a", "b", "c"];
for j in i {
    println!("{}", j);
}
```

## Tests

Using the standard libraries. No, I am not translating every Ruby testing library for you.

Ruby:

```ruby
def some_test
  assert true # will pass
  assert_equal("expected", "actual") # will fail
end
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

Ruby:

```ruby
class Highway
  def initialize(length, speed_limit)
    @length = length
    @speed_limit = speed_limit
  end

  def time_to_travel
    length / speed_limit
  end
end

Highway.new(325, 65).time_to_travel # returns 5
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
