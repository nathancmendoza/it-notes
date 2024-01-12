# Protocol Buffers Basics (Part 1)

## First message walkthrough

From the last chapter, we saw the following example

```protobuf
syntax = "proto3";

message Book {
  string title = 1;
  string subtitle = 2;
  uint32 year = 3;
  repeated string authors = 4;
  string isbn = 5;
}
```

- The `syntax` line declares the version of protocol buffers in use. In this case, it is version 3
- The `message` line declares a collection of fields. It is synonymous to objects in OOP languages or structs in procedural languages
- Each line in the `message` declaration is a field with
  1) A field type
  2) A field name
  3) A field tag that uniquely identifies a field (it is **not** an assignment)

## Anatomy of a field

### Available types

- Integer types provide the most options for numbers *without* decimal points (10 options)
- Floating point types are provided for `float` and `double` precision
- Boolean types provide a single type that can take on the values `true` or `false`
- Length delimited types provide support for strings (arrays of character) and bytes (arrays of byte)

### Field names

- During serialization, only fields metadata and value is included (The name is **not** included).
- The name and types are important for the code that uses protocol buffers

### Rules for tags

- Smallest tag is 1 and the largest is 536,870,911
- Tags 19000 - 19999 are reserved (still usable, but libraries may introduce undefined behavior)

## Default values

> Every field is optional

- Number types (integers and floating point types) have a default value of 0
- Boolean values have a default value of `false`
- Length delimited types have a default value of an empty array

> Beware the defaults

1) Try not to give business logic to defaults
2) If required, implement nullable types
