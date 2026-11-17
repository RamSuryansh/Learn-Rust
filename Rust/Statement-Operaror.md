Because Rust treats match, loop, and if let as expressions rather than statements, you can evaluate them directly into variables, pass them as function arguments, or use them as implicit return values.
1. match as an Expression
A match expression evaluates the pattern that fits and returns the value of that arm's expression. Every arm must evaluate to the exact same type, and the match must be exhaustive (cover all possible values).
enum HttpStatus {
    Ok,
    NotFound,
    ServerError(u16),
}

fn format_response(status: HttpStatus) -> String {
    // The entire match expression evaluates to a String and is implicitly returned
    match status {
        HttpStatus::Ok => String::from("200: Request succeeded"),
        HttpStatus::NotFound => String::from("404: Resource missing"),
        HttpStatus::ServerError(code) => {
            // Multi-line arms use braces; omit the semicolon to return the value
            let alert = format!("CRITICAL ({})", code);
            alert
        }
    }
}

fn main() {
    let message = format_response(HttpStatus::ServerError(503));
    println!("{}", message); // Prints: CRITICAL (503)
}

Note: Notice there are no semicolons after the arms—only commas separating them.
2. loop as an Expression
Unlike while or for (which always evaluate to the unit type () because they might run zero times), an infinite loop is guaranteed to run until it hits a break. By placing a value after break, the loop evaluates to that value.
This is commonly used for retry logic or polling until a condition is met:
fn main() {
    let mut attempts = 0;

    let connection_id = loop {
        attempts += 1;

        if attempts == 3 {
            // Exits the loop and yields the value 42 to `connection_id`
            break 42;
        }
    }; // <-- Semicolon required to end the `let connection_id = ...` statement

    println!("Connected with ID {} after {} attempts.", connection_id, attempts);
}

Tip: If you have nested loops, you can target a specific outer loop using a loop label: break 'outer_loop 42;.
3. if let (and let-else) as Expressions
if let acts as a concise single-arm match. When used as an expression to assign a value, you must include an else block that returns the same type so the variable is guaranteed to be initialized.
fn get_user_role(header: Option<&str>) -> &str {
    // Evaluates to a &str regardless of whether `header` is Some or None
    let role = if let Some(token) = header {
        if token == "admin_secret" { "Admin" } else { "Member" }
    } else {
        "Guest"
    };

    role
}

fn main() {
    println!("Role 1: {}", get_user_role(Some("admin_secret"))); // Admin
    println!("Role 2: {}", get_user_role(None));                 // Guest
}

Modern Alternative: let-else
When you want to unwrap a value into the current scope and diverge (return early, break, or panic) if it doesn't match, let-else avoids deep nesting:
fn parse_port(input: Option<&str>) -> u16 {
    // If `input` is None, the `else` block MUST diverge (e.g., return)
    let Some(raw_port) = input else {
        return 8080; // Default fallback
    };

    raw_port.parse().unwrap_or(8080)
}

Summary of Return Types
| Construct | Evaluates To | Requirement for Yielding a Value |
|---|---|---|
| match | Type T | All arms must return type T and cover all cases. |
| loop | Type T | Every break <expr>; path must yield type T. |
| if let | Type T | Must include an else branch that also returns type T. |
| while / for | () (Unit) | Cannot yield values via break; always evaluates to (). |
