# Programming a Guessing Game

## Store Values with Variables

use let statement to create varible and assign value to it

```rust
let x = 5;
```

Varibles wqj are immutable by default

```rust
let x = 5;
x = 6; // error
```

To make varible mutable, use mut keyword

```rust
let mut x = 5;
x = 6; // ok
```

mutable:

* can change value
* can shadow
* can't change type
* can't use let mut x = 5; twice

immutable:

* can't change value
* can shadow
* can change type
* can use let mut x = 5; twice
* cannot use String::from

The “type annotations” and “let mut” have no effect in Rust. The compiler infers the type.

```rust
let mut x = 5;
let x = x + 1;
let x = x * 2;
```

Variables can be overwritten:

```rust
let x = 5;
let x = x + 1;
let x = x * 2;
```

### Call functions in Rust

```rust
    fn main() {
        another_function();
    }   
    fn another_function() {
        println!("Another function.");
    }
```

### Function Parameters

```rust
    fn main() {
        another_function(5);
    }   
    fn another_function(x: i32) {
        println!("The value of x is: {}", x);
    }
```

### Function Bodies Contain Statements and Expressions

```rust
    fn main() {
        let x = 5;
        let y = {
            let x = 3;
            x + 1
        };
        println!("The value of y is: {}", y);
    }
```

### Return Values

Functions can return values to the caller, using the `return` keyword.

A return value is optional: a `return` expression without a value is the same as if the `return` expression was `return ();`.

A function body containing a \`

```rust
    fn main() {
        let x = five();
        println!("The value of x is: {}", x);
    }
    fn five() -> i32 {
        5
    }
```

### Returning multiple values

Rust functions can return one value:

```rust
    fn main() {
        let (x, y) = two_values();
        println!("The value of x is: {}", x);
        println!("The value of y is: {}", y);
    }
    fn two_values() -> (i32, i32) {
        (5, 6)
    }
```

### Handle Rotential failure with result

```rust
    fn main() {
        let x = divide(5, 3);
        println!("The value of x is: {:?}", x);
    }
    fn divide(x: i32, y: i32) -> Result<i32, String> {
        if y == 0 {
            Err(String::from("y cannot be zero!"))
        } else {
            Ok(x / y)
        }
    }
```

### result type

values of the result type , like values of any types, have methods defined on them. An instance of result has an expect method that you can call.

If this instance of result is an err values, expect will cause the program to crash and display the message that you passed as an argument to expect.

### Using a crete to get more functionality

Crate is a collection of Rust source code files.

### Using external crates

```rust
    use rand::Rng;
    fn main() {
        let secret_number = rand::thread_rng().gen_range(1, 101);
        println!("The secret number is: {}", secret_number);
    }
```

Rust will download dependences in cargo.toml

### Ensuring Reproductcible builds with the cargo.lock file

cargo.lock file lock the version of depnedences.

user cargo update to ignore the cargo.lock file, only looking for small upgrader version. Update the cargo.toml could help to upgrade the dependence to any version
