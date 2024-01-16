# Data Evolution

## Backwards and forwards compatibility

> **Forward compatibility** is a design characteristic that allows a system to accept input intended for a *later* version of itself

```protobuf
syntax = "proto3";

// App v1

message Account {
  string first_name = 1;
  string last_name = 2;
}
```

- Input that follows the v1 schema will still work with the v2 schema
- The missing `phone` field will be assigned its default value (an empty string in this case)

```protobuf
syntax = "proto3";

// App v2

message Account {
  string first_name = 1;
  string last_name = 2;
  string phone = 3;
}
```

> **Backward compatibility** is a design characteristic that allows a system to accept input intended for a *earlier* version of itself

```protobuf
syntax = "proto3";

// App v2

message Account {
  string first_name = 1;
  string last_name = 2;
  string phone = 3;
}
```

- Input in the form of the v2 schema will still work with the v1 schema
- The additional `phone` field is essentially skipped and only the first 2 fields are de-serialized

```protobuf
syntax = "proto3";

// App v1

message Account {
  string first_name = 1;
  string last_name = 2;
}
```

The magic happens **at de-serialization**.

- Forward compatibility: unknown fields are set to the *default values*
- Backward compatibility: unknown fields are *skipped*

## Updating schemas

### Adding a field

```protobuf
// v1 proto

syntax = "proto3";

package org.lf.pbtutorial.v1;

message Account {
  uint32 id = 1;
}
```

```protobuf
// v2 proto

syntax = "proto3";

package org.lf.pbtutorial.v2;

message Account {
  uint32 id = 1;
  string name = 2;
}
```

- If a message is serialized with the second version and de-serialized with the first version, the `name` field will be unrecognized and skipped
- If a message is serialized with the first version and de-serialized with the second version, the missing `name` field will be given its default value

### Renaming a field


```protobuf
// v3 proto

syntax = "proto3";

package org.lf.pbtutorial.v3;

message Account {
  uint32 id = 1;
  string alias = 2;
}
```

- If a message is serialized with the second version and de-serialized with the third version, the `name` field will be recognized as the `alias` field
- If a message is serialized with the third version and de-serialized with the second version, the `alias` field will be recognized as the `name` field
- This is possible because serialization/de-serialization only cares about the type and the tag of a field

### Removing a field

```protobuf
// v4 proto

syntax = "proto3";

package org.lf.pbtutorial.v4;

message Account {
  uint32 id = 1;
}
```

- If a message is serialized with the third version and de-serialized with the fourth version, the `alias` field will be unrecognized and skipped
- If a message is serialized with the fourth version and de-serialized with the third version, the missing `alias` field will be given its default value
- This action requires a little more effort to preserve backwards and forwards compatibility. Simply removing the field is not enough

### The `reserved` keyword

```protobuf
message Account {
  reserved 2; // Prevents any field from using the tag value of 2
  reserved "first_name"; // Prevents any field from using the name "first_name"
  uint32 id = 1;
}
```

- Reserving tags
  - Reserve a single tag or multiple tags on a single line
  - Reserve tag ranges in an inclusive way
- Reserving names
  - Reserve a single field name or multiple names on a single line
  - Cannot be used in conjunction to reserving tags (they must occur on separate lines)
- Why bother?
  - Message serialization/de-serialization will lose information about the schema, in particular the field names  

## Update rules

1) Do not change or reuse tags
    - Add new fields instead
    - Reserve fields to prevent reuse in the future
2) Take care when changing field types
    - Check documentation for type compatibility
    - Consider adding a new field instead (if type compatibility in non-trivial)
