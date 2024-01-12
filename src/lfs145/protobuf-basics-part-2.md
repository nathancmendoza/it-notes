# Protocol Buffers Basics (Part 2)

## Enumerations

Enumerations are useful when we have an exhaustive list of states

```protobuf
enum FileType {
  UNSPECIFIED = 0;
  MP3 = 1;
  MP4 = 2;
  JPEG = 3;
}
```

Notice how the smallest tag in an enumeration is `0`, which differs from a message's smallest tag of `1`

1) A field with a tag of `0` in a message will be incorrect
2) A enumeration variant with a tag of `0` defines the **default value** of the field in a message using the enumeration

## Repeated fields

Consider the following message definition

```protobuf
message User {
  string middle_names = 1;
}
```

There are 3 possible scenarios

1) `User` has no middle name
2) `User` has exactly 1 middle name
3) `User` has 2 or more middle names

Scenarios 1 and 2 would work fine with the current schema. Scenario 3 would also work if we introduced a delimiter between middle names. However, that will require an additional step to process the individual names after de-serialization. To fix that:

```protobuf
message User {
  repeated string middle_names = 1;
}
```

The type of the `middle_names` field is now a list of strings. The 3 possible scenarios work out as follows

1) `User` has no middle name, so the value would be `[]`
2) `User` has a single middle name, so the value would be `["abc"]`
3) `User` has 2 or more middle names, so the value would be `["abc", "def", ...]`

Bonus: when data is de-serialized, they is no preprocessing necessary to obtain an iterable value

## Maps

```protobuf
message PhoneBook {
  map<string, string> contacts = 1;
}
```

### Using maps

- Useful for linking a key with a value
- The types that can be keys are restricted
- The default value for a map type is the empty map

### Rules for maps

- Only the simple types can be used as keys, with the exception of floating point types and bytes
- Maps themselves cannot be repeated

## One of

```protobuf
message Cat {/*...*/}
message Dog {/*...*/}

message CatOrDog {
  oneof result {
    Cat cat = 1;
    Dog dog = 1;
  }
}
```

### Using one of

- Useful for representing mutually exclusive data
- Represent data that is more complex that a simple boolean
- The default value is a field with no value set

### Rules for one of

- Can only be defined in messages
- Cannot use repeated fields or maps inside of a `oneof` definition
- Cannot be a repeated field itself
