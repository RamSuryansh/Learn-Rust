Rust enforces memory safety at compile time without a garbage collector, trading slightly steeper syntax upfront for high runtime performance and reliability.
1. Variables and Mutability
Variables are immutable by default. Use mut to allow changes, and const for compile-time constants (which always require type annotations).
fn main() {
    let x = 5;              // Immutable (type inferred as i32)
    let mut y = 10;         // Mutable
    y += 5;

    const MAX_POINTS: u32 = 100_000; // Constant with underscore readability

    // Shadowing: redeclaring a variable with the same name (can change type)
    let x = "now a string";
    println!("x: {x}, y: {y}, max: {MAX_POINTS}");
}

2. Basic Data Types
Rust is statically typed. Common scalar and compound types include:
 * Integers: Signed (i8, i16, i32, i64, i128, isize) and unsigned (u8, u16, u32, u64, u128, usize). Default is i32.
 * Floats: f32 and f64 (default).
 * Booleans & Characters: bool (true/false) and char (4-byte Unicode scalar value, single quotes 'a').
 * Tuples: Fixed-length grouping of mixed types.
 * Arrays: Fixed-length grouping of a single type, allocated on the stack.
fn main() {
    let tup: (i32, f64, bool) = (500, 6.4, true);
    let (a, b, c) = tup;     // Destructuring
    let first = tup.0;       // Index access

    let arr: [i32; 3] = [1, 2, 3];
    let zeros = [0; 5];      // [0, 0, 0, 0, 0]
}

3. Functions and Expressions
Rust is an expression-based language. Statements perform actions and end with ;. Expressions evaluate to a value and omit the trailing semicolon to return implicitly.
fn add(a: i32, b: i32) -> i32 {
    a + b // No semicolon = implicit return
}

fn main() {
    let result = {
        let base = 10;
        base * 2 // Block evaluates to 20
    };
    println!("Sum: {}", add(result, 5));
}

4. Control Flow
Conditions in if statements must evaluate strictly to bool (no truthy/falsy integers). if is also an expression.
fn main() {
    let number = 7;
    let parity = if number % 2 == 0 { "even" } else { "odd" };

    // Infinite loop with break value
    let mut count = 0;
    let doubled = loop {
        count += 1;
        if count == 5 {
            break count * 2;
        }
    };

    // Range-based for loop (1..4 is exclusive, 1..=4 is inclusive)
    for i in 1..=3 {
        println!("Step {i}: {parity}, {doubled}");
    }
}

5. Ownership and Borrowing
Ownership prevents memory leaks and data races without a garbage collector:
 * Each value has a single owner variable.
 * When the owner goes out of scope, the value is dropped (freed).
 * Assigning or passing heap-allocated data (like String) moves ownership rather than copying it.
To use data without taking ownership, pass a reference (&). You can have either multiple immutable references (&T) or one mutable reference (&mut T) in a given scope—never both simultaneously.
fn main() {
    let s1 = String::from("hello");
    let len = calculate_length(&s1); // Immutable borrow; s1 remains valid
    println!("'{s1}' has length {len}");

    let mut s2 = String::from("hello");
    append_world(&mut s2);           // Mutable borrow
    println!("{s2}");
}

fn calculate_length(s: &String) -> usize {
    s.len()
}

fn append_world(s: &mut String) {
    s.push_str(", world!");
}

6. Structs, Enums, and Pattern Matching
struct groups related fields; impl attaches methods. enum defines a type that can be one of several variants, each capable of holding different data.
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
}

fn process(msg: Message) {
    // match must be exhaustive (cover every variant)
    match msg {
        Message::Quit => println!("Quitting"),
        Message::Move { x, y } => println!("Moving to ({x}, {y})"),
        Message::Write(text) => println!("Text: {text}"),
    }
}

7. Error Handling (Option and Result)
Rust has no null and no exceptions. Instead, it uses two built-in generic enums:
 * Option<T> (Some(T) or None) for values that might be absent.
 * Result<T, E> (Ok(T) or Err(E)) for operations that might fail.
The ? operator unwraps Ok/Some values or returns early with Err/None.
fn divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        Err(String::from("Division by zero"))
    } else {
        Ok(a / b)
    }
}

fn compute() -> Result<f64, String> {
    let val = divide(10.0, 2.0)?; // Propagates Err automatically if it fails
    Ok(val + 3.0)
}

