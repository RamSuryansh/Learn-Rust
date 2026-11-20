The Rust compiler (rustc) transforms your .rs source code into native machine code through a multi-stage pipeline. Understanding how it works—and how to use its tools—will make you much faster at fixing borrow-checker errors, optimizing performance, and debugging builds.
1. The rustc Compilation Pipeline
Unlike interpreted languages, Rust compiles ahead-of-time using LLVM as its backend. Your code passes through several distinct representations before becoming an executable:
 * Lexing & Parsing → AST (Abstract Syntax Tree): rustc breaks your text into tokens and builds a tree structure representing the syntax.
 * Macro Expansion & Name Resolution: Procedural and declarative macros (println!, vec!) expand into regular Rust code, and identifiers are linked to their definitions.
 * HIR (High-Level Intermediate Representation): Syntax sugar (like for loops and ? operators) is desugared into explicit constructs. Type checking happens here.
 * MIR (Mid-Level Intermediate Representation): HIR is lowered into a control-flow graph. This is where the magic happens:
   * Borrow Checking: Verifies lifetimes and ownership rules.
   * MIR Optimizations: Performs dead-code elimination and inlining before handing code to LLVM.
 * LLVM IR & Machine Code Generation: MIR is translated into LLVM Intermediate Representation, where heavy target-specific optimizations occur, resulting in your final binary or library.
2. Using rustc Directly vs. cargo
While 99% of your daily work uses cargo (Rust's build system and package manager), calling rustc directly helps you understand what happens under the hood.
Basic Compilation
Create a file named main.rs:
fn main() {
    let x = 42;
    println!("The answer is {}", x);
}

Compile and run it directly:
rustc main.rs
./main

Useful rustc Flags
 * Optimization levels (-C opt-level):
   rustc -C opt-level=3 main.rs   # Maximum speed optimizations (similar to cargo --release)

 * Inspecting Compiler Stages (--emit): You can tell rustc to stop partway and output the intermediate code so you can read it:
   rustc --emit=mir main.rs       # Outputs main.mir (see what the borrow checker sees)
rustc --emit=llvm-ir main.rs   # Outputs main.ll (see the LLVM instructions)
rustc --emit=asm main.rs       # Outputs main.s (see the raw assembly)

 * Viewing Expanded Macros: To see what macros turn into (using nightly or cargo-expand):
   rustc +nightly -Zunpretty=expanded main.rs

3. Essential Compiler Diagnostics & Flags
rustc is famous for its helpful error messages, and it includes built-in tools to explain them:
 * Explain any error code: Whenever you hit a cryptic error like E0382 (use of moved value), run:
   rustc --explain E0382

   This prints a self-contained mini-tutorial with broken code examples and how to fix them.
 * Compiler Lints (#[warn(...)] / #[deny(...)]): You can configure how strict the compiler is directly in your code or via flags:
   #![deny(unused_variables)] // Turns unused variable warnings into hard compile errors

4. How cargo Wraps rustc
When you run cargo build, Cargo resolves your dependencies (Cargo.toml), determines the build order, and invokes rustc once per crate (each dependency is compiled as a library .rlib, then linked into your final binary).
To see the exact rustc commands Cargo is executing behind the scenes, pass the verbose flag:
cargo build --verbose

