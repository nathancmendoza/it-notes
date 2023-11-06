# Basic troubleshooting

## Troubleshooting steps

1) **Understand the error message**: rust error messages are quite detailed, read them *carefully* to pinpoint the issue
2) **Check for typos and syntax errors**: a very common pitfall in programming; make sure to review your code

## Troubleshooting tools

### Rust compiler

> The first defense against syntax errors

### Clippy

> A linting tool for rust that goes beyond catching syntax errors

| Warning/Suggestion | Meaning |
|:------------------:| ------- |
| Simplified code | Recommendation for simpler, more readable code |
| Unsafe code | Flags codes that could lead to undefined behavior |
| Performance | Suggestions for faster alternatives |
| Style | Enforce consistent coding style |
| Deprecated items | Warns against the use of deprecated elements |
| Error handling | Recommends more idiomatic ways to handle errors |
| Redundant code | Suggests removal of redundant code |
| Type conversions | Flag unnecessary type conversions and advises of type appropriateness |
| Pattern matching | Guidance on making code more expressive via pattern matching |
| Complex expressions | Suggests breaking down intricate expressions into simpler parts |

### IDE and code editors

> Many popular editors and evironment offer a rust extension to faciliate early error detection

### Rust analyzer

> An advanced language server that integrates with IDEs

| Feature | Purpose |
|:-------:| ------- |
| Auto-completion | Offers *context-aware* completion suggestions |
| Code diagnostics | Highlights issues directly in the editor |
| Find references and go-to definitions | Quick navigation to definitions and usages |
| Documentation on hover | Display relevant documentation when hovering over code elements |
| Organize imports | Sort and clean up import statements |
| Rename symbol | Safely rename variables and functions across your code |
| Macro expansion | Show how macro expands |

### Rustfmt

> Improves readability by automatically formatting code according to rust's style guidelines

### Compile-time checks

> The type system and borrow checker identify issues that can lead to problems at runtime

### Testing 

> Help catch syntax and logical errors to ensure code performs as intended

### Continuous integration

> Help catch errors early by running automatically before merging code

### Debugging techniques

- Macros like `print!` or `dbg!` can print out state of a program at specified points
- Specialized binaries like `rust-gdb` and `rust-gdbgui` provide interfaces for more advanced debugging techniques
