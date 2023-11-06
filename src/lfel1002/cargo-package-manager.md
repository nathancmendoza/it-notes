# Cargo: the rust package manager

## Cargo projects

### What is a cargo project?

- Any software development effort managed and built *using* `cargo`
- Makes building, testing, and managing projects effortless
- `cargo` works with building blocks called "crates" (essentially rust libraries)

### Initializing a new project

> Done using the `cargo new` command and creates the following directory structure

```
first-project
├── Cargo.toml
└── src
    └── main.rs
```

- `Cargo.toml`: the project's configuration file
- `src/`: the directory that houses the project's source code files
- `main.rs`: serves as an entry-point to the resulting executable

## Dependency management

> `cargo` tracks and resolves all external crate required by your project and is done by configuring the `Cargo.toml` file

### Package information

This section contains important metadata about the project

```toml
[package]
name = "first_project"
version = "0.1.0"
authors = ["Tux Penguin <tux@linux.com>"]
```

### Dependency

This section lists all crates your project depends on

```toml
[dependencies]
rand = "0.8.4"
```

They are listed as key-value pairs where the key in the crate name and the value is the version required

### Development dependencies

This section lists crates used for development and testing of your project

```toml
[dev-dependencies]
test_crate = "0.1.0"
```

They are **not** bundled with the final binary in a release build

### Custom build scripts

Projects that require a custom build script can specify it in the `[package]` section

```toml
[package]
name = "first_project"
version = "0.1.0"
authors = ["Your Name <your@email.com>"]
build = "build.rs"
```

The `build.rs` script is located in the root directory of the project

## Workspaces

> Workspaces help managed closely related rust projects

```toml
[workspace]
members = [
    "first_project",
    "Another_project",
    "Third_project",
]
```

The `[workspace]` section allows you to list member projects that belong in a workspace

## Other project settings

- Specify the rust edition (compiler "version")
- Settings for documentation

### Intelligent compilation

- The build system detects changes in your code
- Only modified parts are recompiled
- The `Cargo.lock` file records exact versions of project dependencies

### Cargo subcommands

- `cargo build`: complies the project
- `cargo test`: complies the project *and* runs all unit tests
- `cargo doc`: generate HTML documentation with `rustdoc` tool
- `cargo publish`: publishes your rust crate to the central package registry
