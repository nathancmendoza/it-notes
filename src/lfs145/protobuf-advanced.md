# Advanced Protocol Buffers

## Advance features

### Choosing the correct integer type

- Sizes
  - Integer types have a numeric suffix of `32` or `64` representing the number of bits used in the representation
  - 32 bit integers have a range of about 4 billion representable numbers
  - 64 bit integers have a range of about 18 trillion representable numbers
  - The exact numbers in the range depend on whether the integer is signed or unsigned
- Encoding length
  - Integer types have a root word of either `int` or `fixed`, representing the way the value is serialized
  - `int` serialization are variable and depend on the value (small number take less bytes to serialize)
  - `fixed` serialization are the same length when serialized regardless of the value itself (4 bytes for 32 bit variants and 8 bytes for 64 bit variants)
- Signs
  - Integer types have a prefix of `u` of `s`, representing whether or not the integer is signed or not
  - Variants with `u` only accept non-negative numbers
  - Variants with `s` accept positive and negative numbers
  - Variants without a prefix only non-negative numbers
- Selecting the correct type
  - Do we need numbers more than 2B or 4B?
    - Yes: use a 64 bit variant
    - No: 32 bit variants should suffice
  - Do we need positive and negative numbers?
    - Yes: use a signed variant
    - No: unsigned variant will suffice

### Services

- Services are a generic concept that are not related to serialization and de-serialization
- Protocol buffers do not send messages over the network, they rely on frameworks like gRPC to do that
- Services specify a contract to make apps are aware of what to send and what will be sent

```protobuf
service FooService {
  rpc GetSomething(GetSomethingRequest) returns (GetSomethingResponse);
  rpc ListSomething(ListSomethingRequest) returns (ListSomethingResponse);
}
```

### Style guide

- Filenames should be in lower snake case. (`foo_bar.proto`)
- If a license is present, it should be at the top of the file
- File setup
  1) Syntax declaration
  2) Package name
  3) Imports (in alphabetical order)
  4) File options (if any)
- Definitions for types, messages, and services (names should be in pascal case)
  - Enumeration variants should be in all caps (words separated by underscores)
  - Message fields should be in lower snake case
  - Repeated field names should be pluralized
  - Service names should be in pascal case

## Internals

### Encoding

- Field types are defined as numbers
  - 0 represents varint,
  - 1 for 64 bits numbers
  - 2 for length-delimited
  - 5 for 32 bit numbers
- Field tags are encoded are 32 bit varints

```protobuf
syntax = "proto3";

message MyMessage {
  uint32 id = 1;
}
```

- The encoded value for the field type and tag is `08`

### Varints 

> The smaller the value, the smaller the overhead

Lets encode the value 300 (`100101100` in binary)

1) Separate the bits into groups of 7, prepending the MSB of 1 to each segment except for the first
2) Reverse the order of all the bytes
3) The value is serialized as `AC 02`

Let's decode the value `AC 02`

1) Transform into binary
2) Read bytes until the MSB is 0 (2 bytes in this case)
3) Drop the MSB and reverse the order of the bytes
4) Calculate binary into decimal

### ZigZag encoding

| Value | Encoded As |
|:-----:|:----------:|
| 0 | 0 |
| -1 | 1 |
| 1 | 2 |
| -2 | 3 |
| 2 | 4 |
| ... | ... |

- Encoded magically with: `(value >> bits-1) ^ (value << 1)`
- Decoded magically with: `(value >> 1) ^ -(value & 1)`
- This is done on top of varint encoding, reducing the size needed for negative numbers

### Length delimited types

```plaintext
id: test
```

The message above is encoded as `10 04 74 65 73 74`

- `10` is the type (string) with its tag value (1)
- `04` is the data length 
- Remaining bytes of the ASCII encoding of the string

### Repeated fields

- Packed repeated fields
  - Available for primitive types
  - Encoded similarly to length-delimited types: (tag & type + length + values)
- Other types
  - Available when serializing complex or user-defined types
  - Encoded similar to simple fields: (tag & type + value1 + tag & type + value2)

### Field order

- The serialization order of fields is *not* guaranteed
- Comparing the byte strings of two serialized messages may work sometimes but is not guaranteed, use the field getters instead
- The same thing applies to hashing serialized messages and comparing their hashes
