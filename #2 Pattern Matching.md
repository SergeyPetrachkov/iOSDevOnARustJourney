# Swift vs Rust pattern matching

Hey everybody! It’s #2 record in the iOS dev on the Rust journey journal. 
Today we’re gonna talk about pattern matching. Both Swift and Rust are modern languages that have lots of syntax sugar to make our lives sweeter. 

I came to Swift after C# and Obj-C when it was still young and unstable language. But even back then if-let, guard-let, switch and enums with associated values were revolutionary to me. I still find Swift super fun language to work with, so let’s take a look at what we have in Rust, is it the same fun?

## Basic switch / match

When we have some value and we want to go through our options we can do something basic like this:

```Swift
let value = 2
switch value {
case 1:
  print("One")
case 2:
  print("Two")
default:
  print("Something else \(value)")
}
```

In Rust the same functionality would look like this:

```rust
let value = 2;
match value {
    1 => println!("One"), // one arm
    2 => println!("Two"), // another arm
    other => println!("Something else {}", other), // catch-all pattern
}
```

## Simple Enums

Both languages can apply switch/match to enums.

```Swift
enum Direction {
  case north
  case south
  case west
  case east
}
```
or 
```Swift
enum Direction {
  case north, south, west, east
}
```

Let's spice things up and combine simple switch with switch-expression that was introduced in Swift 5.1

```Swift
let direction = Direction.north

let directionString = switch direction {
    case .north: "North"
    case .south: "South"
    case .west: "West"
    case .east: "East"
  }
print("Direction: \(directionString)")
```

Does Rust have the same? It does!

```Rust
enum Direction { North, South, West, East }

fn main() {
  let direction = Direction::North;
  let direction_string = match direction {
    Direction::North => "North",
    Direction::South => "South",
    Direction::West => "West",
    Direction::East => "East"
  }
  println!("Direction: {}", direction_string);
}
```

As you can see it's really close :) In Rust they use snake_case instead of the camelCase though ;-)

## Enums with associated values

Both Swift and Rust support fancy enums. And they do look like two dialects of the same language :D 

```Swift
enum Res {
  case ok(Int)
  case err(String)
}

func divideInTwo(n: Int) -> Res {
    if n % 2 == 0 {
      .ok(n / 2)
    } else {
      .err("Cannot divide \(n) by 2")
    }
}

func main() {
  let n = 101
  switch divideInTwo(n) {
    case .ok(let half):
      print("\(n) divided in two is \(half)")
    case .err(let message):
      print("Sorry, an error has happened: \(message)")
  }
}
```

```rust
enum Res {
    Ok(i32),
    Err(String),
}

fn divide_in_two(n: i32) -> Res {
    if n % 2 == 0 {
        Res::Ok(n / 2)
    } else {
        Res::Err(format!("Cannot divide {n} by 2"))
    }
}

fn main() {
  let n = 101;
  match divide_in_two(n) {
      Result::Ok(half) => println!("{n} divided in two is {half}"),
      Result::Err(msg) => println!("Sorry, an error has happened: {msg}"),
  }
}
```

Let's build something more interesting. Let's build an arithmetic expression tree, some companies still ask that in their leetcode section :)

We need to calculate evaluate an expression and return a value.
Something like this:

<img width="559" alt="Screenshot 2025-04-25 at 15 42 24" src="https://github.com/user-attachments/assets/39021a37-e517-4e39-99ee-bffd8a977d34" />

We can build this thingy using enums and recursion.

With swift:
```Swift
import Testing

enum Operation {
	case add, sub, mul, div
}

indirect enum Expression {
	case op(Operation, Expression, Expression)
	case value(Int)
}

func eval(e: Expression) -> Int {
	switch e {
	case .value(let value):
		return value
	case .op(let op, let lhs, let rhs):
		let lhsValue = eval(e: lhs)
		let rhsValue = eval(e: rhs)
		switch op {
		case .add:
			return lhsValue + rhsValue
		case .sub:
			return lhsValue - rhsValue
		case .mul:
			return lhsValue * rhsValue
		case .div:
			return lhsValue / rhsValue
		}
	}
}

@Test
func recursion() async throws {
	let lhs = Expression.op(.mul, .value(10), .value(9))
	let rhs = Expression.op(.mul, .op(.sub, .value(3), .value(4)), .value(5))

	let result = eval(e: Expression.op(.add, lhs, rhs))
	#expect(result == 85)
}
```

And with Rust:
```rust
enum Operation {
    Add,
    Sub,
    Mul,
    Div,
}

enum Expression {
    /// An operation on two subexpressions.
    Op { op: Operation, left: Box<Expression>, right: Box<Expression> },

    /// A literal value
    Value(i64),
}

fn eval(e: Expression) -> i64 {
    match e {
        Expression::Value(value) => value,
        Expression::Op { op, left, right } => {
            let left_value = eval(*left);
            let right_value = eval(*right);
            match op {
                Operation::Add => left_value + right_value,
                Operation::Sub => left_value - right_value,
                Operation::Mul => left_value * right_value,
                Operation::Div => left_value / right_value,
            }
        }
    }
}

#[test]
fn test_recursion() {
    let term1 = Expression::Op {
        op: Operation::Mul,
        left: Box::new(Expression::Value(10)),
        right: Box::new(Expression::Value(9)),
    };
    let term2 = Expression::Op {
        op: Operation::Mul,
        left: Box::new(Expression::Op {
            op: Operation::Sub,
            left: Box::new(Expression::Value(3)),
            right: Box::new(Expression::Value(4)),
        }),
        right: Box::new(Expression::Value(5)),
    };
    assert_eq!(
        eval(Expression::Op {
            op: Operation::Add,
            left: Box::new(term1),
            right: Box::new(term2),
        }),
        85
    );
}
```

Okay okay!

What does Rust have that swift doesn't?

Not exactly the pattern matching, but something about initialisers. Let's imagine we have a struct Person and we have an instance of such struct. And then we want to create a clone, but we want only one field to be different. Then we can do the following:

```Rust
struct Person {
    name: String,
    age: u8,
    job: String,
}

let peter = Person {
        name: String::from("Peter"),
        age: 27,
        job: "Developer"
    };

let avery = Person { name: String::from("Avery"), ..peter };
```

Pretty cool, huh!?

Ok, that's all for today :) See you soon.