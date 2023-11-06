# Basic rust programs

## Syntax and tokens

### Keywords

> Rust keywords fall into 3 categories

| Keyword type | Description |
|:------------:| ----------- |
| Strict keywords | Dictate how the fundamental building blocks are defined. Misuse results in compile-time errors |
| Reserved keywords | Reserved for future language expansions |
| Weak keywords | Have a particular function but *only* under certain cirumstances |

### Identifiers

- The customizable parts of your code
    - Names for variables
    - Signatures for functions
- Following naming conventions and keeping names descriptive makes code more readable and maintainable

### Literals

- Allow values to be embedded directly into code
- Guarantees immutability and has a performance advantage
- Rust supports
    - Numeric types: `42` or `3.14`
    - Text: `'a'` or `"hello"`
    - Array and tuple literals: `([1, 2, 3], (1, 'a'))`

### Lifetimes

- Ensures that references don't outlive the data they point to 
- This avoid *dangling references*

### Punctuation

- Semicolons (`;`) are used to terminate statements
- Colons (`:`) are used to specify types

### Delimiters

> Symbols that work in pairs to define scopes and sequences as the *punctuation marks* of coding

| Delimiter | Usage |
|:---------:| ----- |
| Curly braces (`{}`) | Used to define blocks of code as a separate scope |
| Square brackets (`[]`) | Used for declaring arrays and indexing into arrays |
| Parenthesis (`()`) | Used in function declarations and calls to encapsulate parameters and arguments. Also used to clarify order in mathematical expressions |

## Starting with "Hello, World!"

```rust
// Begin with fn main() and an opening curly brace 
fn main() { 
// Print a message to the standard out enclosed with parentheses and ended with a semicolon 
    println!("Hello, open source world!"); 
// Close the main function 
}
```

## Factorial calculation

```rust
// Define a function to calculate the factorial of a number. 
fn factorial(n: u64) -> u64 { 
    // Base case: If n is 0 or 1, the factorial is 1. 
    if n == 0 || n == 1 { 
        1 
    } else { 
        // Recursive case: Calculate factorial by calling the function recursively. 
        n * factorial(n - 1) 
    } 
} 

fn main() { 
    // Define the number for which we want to calculate the factorial. 
    let num: u64 = 5; 

    // Call the factorial function and store the result. 
    let result = factorial(num); 

    // Print the result to the console. 
    println!("Factorial of {} is: {}", num, result); 
}
```
