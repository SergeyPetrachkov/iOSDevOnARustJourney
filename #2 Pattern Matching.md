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

