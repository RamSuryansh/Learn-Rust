Rust provides three distinct loop constructs—loop, while, and for—each built for a specific control-flow pattern. Unlike many languages, loops in Rust are expressions, meaning they can evaluate to and return a value.
1. loop (Infinite & Value-Returning Loops)
The loop keyword executes a block of code forever until you explicitly tell it to stop with break. Because the compiler knows a loop won't terminate accidentally, it allows you to return a value directly from the loop by placing an expression after break.
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            // Exits the loop and returns counter * 2
            break counter * 2;
        }
    };

    println!("The result is: {result}"); // Prints: The result is: 20
}

 * continue: Skips the remaining code in the current iteration and jumps straight to the next one.
 * break: Exits the loop immediately.
2. while (Conditional Loops)
Use while when you want a loop to run as long as a specific condition evaluates to true. Note that while loops cannot return values via break <value> because the compiler cannot guarantee the loop body will ever execute.
fn main() {
    let mut countdown = 3;

    while countdown > 0 {
        println!("{countdown}...");
        countdown -= 1;
    }

    println!("Liftoff!");
}

3. for (Iterator & Range Loops)
The for loop is the safest and most idiomatic way to iterate in Rust. It eliminates off-by-one errors and avoids runtime bounds-checking overhead by operating directly on iterators and ranges.
fn main() {
    // 1. Iterating over a collection
    let languages = ["Rust", "Go", "C++"];
    for lang in languages {
        println!("Language: {lang}");
    }

    // 2. Iterating over a range (1 to 4, exclusive of 5)
    for number in 1..5 {
        print!("{number} "); // Prints: 1 2 3 4
    }
    println!();

    // 3. Inclusive range (1 to 5, inclusive) in reverse
    for number in (1..=5).rev() {
        print!("{number} "); // Prints: 5 4 3 2 1
    }
}

> Ownership Tip: Iterating directly over a collection like for x in vec consumes (moves) the vector. To borrow elements without taking ownership, iterate over a reference (for x in &vec) or a mutable reference (for x in &mut vec).
> 
4. Loop Labels (Nested Loop Control)
When nesting loops, break and continue apply only to the innermost loop by default. You can prefix an outer loop with a loop label (starting with a single quote, like 'outer) to break out of or continue a specific outer loop.
fn main() {
    'outer: for x in 0..5 {
        for y in 0..5 {
            if x == 2 && y == 2 {
                println!("Breaking out of 'outer at ({x}, {y})");
                break 'outer; // Terminates both loops immediately
            }
        }
    }
}

Quick Comparison
| Construct | Best Used For | Can Return a Value? |
|---|---|---|
| loop | Retrying operations, server event loops, or computing a value until a condition is met | Yes (break val;) |
| while | Looping until an external state or boolean condition changes | No (() only) |
| for | Traversing arrays, vectors, strings, and numeric ranges | No (() only) |
