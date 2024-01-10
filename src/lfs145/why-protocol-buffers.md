# Why Protocol Buffers?

## What is serialization/de-serialization?

### Serialization

1) Start off with an object (say a user account with an email address)
2) Extract the data from the object and store it into 1 or more mediums (file, database, etc.)

### De-serialization

1) Read the data from the storage medium(s) (file, database, etc.)
2) Fill the data back into an object, recreating it from the stored data

### Issues solved

- **Language agnosticism**: allows serializing data to occur in one language and de-serializing to occur in another language, with no loss of information
- **Communication**: data should not be dependent on *any* operating system, and should be as small as possible to use over a network quickly
- **Object relationships**: object have relationships and any program using data should work the same way as expected

## Trade-offs of serialization formats

### When size matters

> When it matters

Optimizations can occur for speed or space when it comes to serialization.

For speed, we could optimize the time is takes to serialize/de-serialize data or the time is takes to transport serialized data over a network.

For space, we generally focus on the number of bytes the serialized data is taking.

> When it doesn't matter

Optimizations for serialization is usually considered for a production environment, but that could make it difficult to inspect that data should issues need investigating.

As a general rule of thumb:

- The smaller the serialized data size, the less human-readable it becomes
- The bigger the serialized data size, the less computer readable it becomes

*Human-readable* refers to the ability of a human to understand the relationships between objects. *Computer-readable* refers to the amount of computation needed to have data stored efficiently in memory

### When readability matters

> When it matters: quick configuration update

Non-human readable data needs to de-serialized, updated and serialized to update the configuration. Not very efficient, since we have to rely on serialization algorithms to update the application and it is generally done with code

> When it doesn't matter

If a UI gives choices to enable/disable properties, it acts as an intermediary between the user and any computation steps to update the application. It is safe to assume that human-readability does not matter and it is actually more efficient for a machine to edit serialized data

### When scheme strictness might matter

> A data schema is a kind of blueprint which describes the objects and the relations that they have with each other, and this blueprint is generally written in a specific “programming” language, like JSON.

```JSON
{
    “account”: {
        “id”: 42,
        “name”: “Linus”,
        “is_verified”: true
    }
}
```

The strictness of a schema is determined by how the field types are enforced. For example, if the `id` field of the scheme above had the value `"Torvald"` it would still be valid JSON.

Not having a strict schema, especially where human users are involved, makes app configuration more error-prone. Invalid field values can be prevented with a strict schema that immediately notifies the user of what they are doing wrong

### Popular serialization formats

```XML
<book>
  <title>Understanding Compression</title>
  <subtitle>Data Compression for Modern Developers</subtitle>
  <year>2016</year>
  <authors>
    <author>Colt McAnlis</author>
    <author>Aleks Haecky</author>
  </authors>
  <ISBN>9781491961506</ISBN>
</book>
```

```JSON
{
  "book": {
    "title": "Understanding Compression",
    "subtitle": "Data Compression for Modern Developers",
    "year": 2016,
    "authors": [ "Colt McAnlis", "Aleks Haecky" ],
    "ISBN": "9781491961506"
  }
}
```

```proto
syntax = "proto3";

message Book {
  string title = 1;
  string subtitle = 2;
  uint32 year = 3;
  repeated string authors = 4;
  string isbn = 5;
}
```

> Schema is *not* serialized, only its metadata and data is serialized as hexadecimal strings

| Strength | XML | JSON data | JSON schema | Protobuf data | Protobuf schema |
|:--------:|:---:|:---------:|:-----------:|:-------------:|:---------------:|
| Readability | 😐 | 🥳 | 😐 | 🤢 | 🥳 |
| Strictness | 🤢 | 😐 | 😐 | 🥳 | 🥳 |
| Size | 🤢 | 😐 | 😐 | 🥳 | 🥳 |

## Why protocol buffers are important?

### Interface description

Protocol buffers use a *I*nterface *D*escription *L*anguage to generate code for serializing and de-serializing objects

- Interface: defines types, properties, etc.
- Description: describes the relationship between types
- Language: has a syntax and rules that must be followed

Protocol buffers use a source-to-source (S2S) compiler, also known as a transpiler, to generate language specific representations of a protocol buffer schema.

```protoc
syntax = "proto3";

message Account {
  string name = 1;
  bool is_verified = 2;
}
```

Using the `protoc` tool, the following go code will be generated

```go
type Account struct {
  //...
  Name       string
  IsVerified bool
}
```

### Communication between services

- `protoc` generates code to serialize and de-serialize data
- Clients can send messages over gRPC to a service and services will respond with a serialized message clients can understand
- Allows services to be written in the language best suited for them
