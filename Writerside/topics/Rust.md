# Rust

This document outlines the coding standards and best practices for Rust development in our projects.

## Naming Conventions

### Types and Traits
- Use **PascalCase** for types, traits, and enum variants

```rust
struct UserAccount {
    username: String,
    email: String,
}

trait Processable {
    fn process(&self) -> Result<(), Error>;
}

enum Status {
    Active,
    Inactive,
    Suspended,
}
```

### Functions and Variables
- Use **snake_case** for functions, methods, and variables
- Use **SCREAMING_SNAKE_CASE** for constants

```rust
fn calculate_total(items: &[Item]) -> f64 {
    let mut total_price = 0.0;
    // Implementation
    total_price
}

const MAX_CONNECTIONS: usize = 100;
const DEFAULT_TIMEOUT: Duration = Duration::from_secs(30);
```

## Error Handling

### Use Result Type
Always use `Result<T, E>` for operations that can fail. Never unwrap unless safety is absolutely guaranteed.

```rust
// BAD
struct Config {
    path: Option<String>
}

fn get_path_from_cfg(cfg: &Config) -> Result<String> {
    Ok(cfg.path.unwrap()) // runtime panic if cfg.path is None
}

// GOOD
struct Config {
    path: Option<String>
}

fn get_path_from_cfg(cfg: &Config) -> Result<String> {
    if let Some(path) = cfg.path {
        return Ok(path);
    }
    bail!("Missing path in config")
}

```

### Custom Error Types
Define custom error types for your domain.

```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum DataError {
    #[error("Invalid data format: {0}")]
    InvalidFormat(String),
    
    #[error("Data not found")]
    NotFound,
    
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),
}
```

## Ownership and Borrowing

### Prefer Borrowing
Use references to avoid unnecessary cloning.

```rust
// Good - uses borrowing
fn process_data(data: &Vec<String>) {
    for item in data {
        println!("{}", item);
    }
}

// Avoid - takes ownership
fn process_data_bad(data: Vec<String>) {
    // data is consumed
}
```

### Use Appropriate Mutability
Only use mutable references when necessary.

```rust
fn read_only(data: &Data) {
    // Can only read
}

fn modify(data: &mut Data) {
    data.value = 42;
}
```

## Idiomatic Rust

### Use Iterator Methods
Leverage iterator combinators instead of loops when appropriate.

```rust
// Good - functional style
let sum: i32 = numbers
    .iter()
    .filter(|&&x| x > 0)
    .map(|&x| x * 2)
    .sum();

// Acceptable for complex logic
let mut sum = 0;
for num in &numbers {
    if *num > 0 {
        sum += num * 2;
    }
}
```

### Pattern Matching
Use pattern matching extensively.

```rust
match result {
    Ok(value) => println!("Success: {}", value),
    Err(e) => eprintln!("Error: {}", e),
}

// Use if let for single pattern
if let Some(value) = optional {
    println!("Got: {}", value);
}
```

### Use `?` Operator
Use the `?` operator for error propagation.

```rust
fn process_file(path: &str) -> Result<(), Box<dyn std::error::Error>> {
    let content = read_file_content(path)?;
    let parsed = parse_content(&content)?;
    save_result(&parsed)?;
    Ok(())
}
```

## Documentation

### Use Doc Comments
Document public APIs with `///` comments.

```rust
/// Calculates the fibonacci number at position `n`.
///
/// # Arguments
///
/// * `n` - The position in the fibonacci sequence
///
/// # Examples
///
/// ```
/// let result = fibonacci(10);
/// assert_eq!(result, 55);
/// ```
///
/// # Panics
///
/// Panics if `n` is greater than 186 due to overflow.
pub fn fibonacci(n: u64) -> u64 {
    match n {
        0 => 0,
        1 => 1,
        _ => fibonacci(n - 1) + fibonacci(n - 2),
    }
}
```

## Testing

### Write Unit Tests
Include unit tests in the same file using a `tests` module.

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_calculate_total() {
        let items = vec![1, 2, 3, 4, 5];
        assert_eq!(calculate_sum(&items), 15);
    }

    #[test]
    #[should_panic(expected = "division by zero")]
    fn test_divide_by_zero() {
        divide(10, 0);
    }
}
```

## Best Practices

- **Run `cargo clippy`** - Use Clippy to catch common mistakes
- **Run `cargo fmt`** - Format code consistently
- **Avoid `unwrap()` and `expect()` in production code** - Handle errors properly
- **Use `derive` macros** - Automatically implement common traits
- **Keep functions small** - Each function should have a single responsibility
- **Use `const` and `static` appropriately** - For compile-time constants
- **Leverage the type system** - Use newtype pattern to prevent bugs

## Code Organization

```rust
// Import order
use std::collections::HashMap;
use std::fs::File;

use serde::{Deserialize, Serialize};
use tokio::runtime::Runtime;

use crate::models::User;
use crate::utils::helpers;

// Module structure
pub mod models;
pub mod services;
pub mod utils;

// Re-exports
pub use models::User;
pub use services::DataService;
```

## Performance Tips

- Use `&str` instead of `String` when you don't need ownership
- Use `Vec::with_capacity()` when you know the size
- Use `clone()` judiciously - consider borrowing or `Rc`/`Arc`
- Profile before optimizing - don't guess
- Consider using `Cow<str>` for string handling flexibility