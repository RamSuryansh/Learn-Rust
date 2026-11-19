Rust’s data structures differ from most languages because they must strictly adhere to the ownership and borrowing rules. Understanding how Rust allocates memory on the stack versus the heap is key to choosing the right structure.
1. Stack-Allocated (Fixed Size)
These have a known size at compile time, making them fast to allocate and copy.
 * Tuples: Group multiple values of different types into one compound type.
   let person: (&str, i32, bool) = ("Alice", 30, true);
let name = person.0; // Dot-index access
let (n, age, active) = person; // Destructuring

 * Arrays ([T; N]): Fixed-length collections of the same type.
   let numbers: [i32; 4] = [10, 20, 30, 40];
let zeros = [0; 5]; // [0, 0, 0, 0, 0]

 * Structs: Custom data types that name and package related values.
   struct User {
    username: String,
    sign_in_count: u64,
}

 * Enums: Define a type by enumerating its possible variants—far more powerful than C-style enums because variants can hold data.
   enum WebEvent {
    PageLoad,
    Click { x: i64, y: i64 },
    KeyPress(char),
}

2. Heap-Allocated Standard Collections (std::collections)
When data needs to grow or shrink dynamically at runtime, Rust uses heap-allocated collections.
| Collection | Best Used For | Time Complexity (Lookup) |
|---|---|---|
| Vec<T> | Growable lists of items (the workhorse of Rust). | O(1) by index |
| String | Growable, UTF-8 encoded text (a wrapper around Vec<u8>). | O(n) (UTF-8 isn't fixed-width) |
| HashMap<K, V> | Unordered key-value lookups. | O(1) average |
| BTreeMap<K, V> | Key-value lookups where keys must stay sorted. | O(\log n) |
| HashSet<T> | Ensuring uniqueness without associated values. | O(1) average |
| VecDeque<T> | Double-ended queues (fast push/pop at both ends). | O(1) at ends |
Practical Collection Examples
use std::collections::HashMap;

fn main() {
    // 1. Vector
    let mut scores = vec![10, 20, 30];
    scores.push(40);
    // Safe access using .get() returns an Option<&T>
    if let Some(third) = scores.get(2) {
        println!("Third score: {third}");
    }

    // 2. HashMap
    let mut inventory = HashMap::new();
    inventory.insert(String::from("Apples"), 50);
    
    // Insert only if the key doesn't already exist
    inventory.entry(String::from("Bananas")).or_insert(25);
}

3. Essential Wrapper Types (Smart Pointers)
Because Rust forbids recursive types of unknown size or multiple mutable owners by default, you will frequently wrap data structures in smart pointers:
 * Option<T>: Represents a value that might be Some(T) or None (replaces null).
 * Result<T, E>: Represents either success (Ok(T)) or failure (Err(E)).
 * Box<T>: Allocates a single value on the heap. Essential for recursive structures like linked lists or trees:
   enum List {
    Cons(i32, Box<List>),
    Nil,
}

 * Rc<T> / Arc<T>: Reference-counted pointers that allow multiple parts of your code to read the same data (Arc is the thread-safe version).
 * RefCell<T> / Mutex<T>: Allow mutating data even when there are immutable references to it (interior mutability).
4. Building a Custom Data Structure (Stack Example)
Here is how structs, generics (<T>), Vec, and Option come together to build a safe, generic Stack:
pub struct Stack<T> {
    elements: Vec<T>,
}

impl<T> Stack<T> {
    pub fn new() -> Self {
        Stack { elements: Vec::new() }
    }

    pub fn push(&mut self, item: T) {
        self.elements.push(item);
    }

    pub fn pop(&mut self) -> Option<T> {
        self.elements.pop() // Naturally returns None if empty
    }

    pub fn peek(&self) -> Option<&T> {
        self.elements.last() // Borrows the top item without removing it
    }
}

