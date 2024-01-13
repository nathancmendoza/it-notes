# Python + Protocol Buffers

## Setup

Prerequisites

- `protoc` installed
- Python 3.7 or later (earlier version not guaranteed to work)
- `pip` installed

### Virtual environment

1) Create the virtual environment

```shell
$ python -m venv .venv
```

2) Activate the virtual environment

```shell
$ source .venv/bin/activate
```

3) Upgrade `pip`

```shell
$ pip install --upgrade pip
```

4) Install the `protobuf` python package

```shell
$ pip install protobuf
```

### Boilerplate (Yay!)

```python
# main.py

import sys

if __name__ == '__main__':
  fns = {

  }

  if len(sys.argv) != 2:
    print(f'Usage: main.py [{"|".join(fns)}]')
    sys.exit(-1)

  fn = fns.get(sys.argv[1], None)

  if not fn:
    print(f'Unknown function: {sys.argv[1]}')
    sys.exit(-1)

  print(fn())
```

## Getting started

### A simple message

```protobuf
// account.proto

syntax = "proto3";

message Account {
  uint64 id = 1;
  string name = 2;
  bool is_verified = 3;
  repeated uint64 following = 4;
}
```

Compile with `protoc` and a python file should be created. Import the file and create an object like so

```python
def account():
  return account_pb.Account(
    id=42,
    name='linux_torvalds',
    is_verified=True,
    following=[0, 1]
  )

```

### A more complex one

```protobuf
// user.proto

syntax = proto3;

message User {
  uint64 id = 1;
  string name = 2;
  repeated User following = 3;
}
```

Compile with `protoc` and import the created file to create an object like before

```python
def user():
  return user_pb.User(
    id=42,
    name='Linus Torvalds',
    following=[
      user_pb.User(id=0, name='Linux Foundation'),
      user_pb.User(id=1, name='Clement Jean')
    ]
  )
```

Alternatively, objects can be created and values set to their respective fields

```python
def user2():
  u = user_pb.User()
  u.id = 42
  u.name = 'Linus Torvalds'
  u.follows.add(id=0, name='Linux Foundation')
  u.follows.add(id=1, name='Clement Jean')
  return u
```

### Dealing with enumerations

```protobuf
// product.proto

syntax = "proto3";

enum ProductType {
  PRODUCT_TYPE_UNSPECIFIED = 0;
  PANTS = 1;
  SHOES = 2;
  SHIRTS = 3;
  ACCESSORIES = 4;
}

message Product {
  uint64 id = 1;
  ProtuctType type = 2;
}
```

The more legible way to specify enumeration variants is to use the provided type

```python
def product():
  return product_pb.Product(
    id=42,
    type=product_pb.ProductType.PANTS
  )
```

Alternatively, the tag value can be used

```python
def product():
  return product_pb.Product(
    id=42,
    type=1
  )
```

### Dealing with maps

```protobuf
//phone_book.proto

syntax = "proto3";

message PhoneBook{
  map<string, string> phones = 1;
}
```

Simply use the Python `dict` type to interface with map fields

```python
def phone_book():
  return phone_book_pb.PhoneBook(
    phones={
      'Linus Torvalds': '11111111',
      'Clement Jean': '22222222'
    }
  )

def phone_book_2():
  book = phone_book_pb.PhoneBook()
  book.phones['Linus Torvalds'] = '11111111'
  book.phones['Clement Jean'] = '22222222'
  return book
```

### Dealing with one of options

```protobuf
// login.proto

syntax = "proto3";

message Token {

}

message LoginResult {
  oneof result {
    string error = 1;
    Token token = 2;
  }
}
```

Simply interface with the appropriate field name. Assigning a new `oneof` value to one that already exist, it will be **overwritten**

```python
def login_error():
  return login_pb.LoginResult(
    error='The username or password are not correct'
  )

def login_success():
  return login_pb.LoginResult(
    token=login_pb.Token()
  )
```

### Duration and timestamps

These are well-known types provided by Google

```python
import google.protobuf.duration_pb2 as duration_pb

# From keyword arguments
def duration():
  return duration_pb.Duration(
    seconds=3,
    nanos=0
  )

# From a timedelta
def duration2():
  from datetime import timedelta
  td = timedelta(days=3, minues=10, microseconds=15)
  d = duration_pb.Duration()
  d.FromTimeDelta(td) # There are more From* methods available
  return d
```

```python
import google.protobuf.timestamp_pb2 as timestamp_pb

# Using the current time
def timestamp():
  t = timestamp_pb.TimeStamp()
  t.GetCurrentTime()
  return t
```

The well-known types interact fairly nicely with language libraries

### Field masks

This well-known type helps filter out unwanted fields, similar to the `SELECT` clause in SQL

```python
import google.protobuf.field_mask_pb2 as field_mask_pb

def field_mask():
  acc = account()
  fm = field_mask_pb.FieldMask(
    paths=['id', 'is_verified']
  )

  # A new instance to store the result
  iiv = account_pb.Account()
  fm.MergeMessage(acc, iiv) # Apply the field mask
  return iiv

def two_field_masks():
  mask = field_mask_pb.FieldMask()
  mask.FromJsonString('id,name')
  mask2 = field_mask_pb.FieldMask()
  mask2.FromJsonString('id,isVerified')
  mask3 = field_mask_pb.FieldMask()
  mask3.Union(mask, mask2)

  acc = account()
  iniv = account_pb.Account()
  mask3.MergeMessage(acc, iniv)
  return iniv
```

### Wrappers

These are useful for implementing services as a common understanding between procedure calls

```python
import google.protobuf.wrappers_pb2 as wrappers_pb

def wrapper():
  return [
    wrappers_pb.BoolValue(value=True),
    wrappers_pb.BytesValues(value=b'these are bytes'),
    wrappers_pb.FloatValue(value=42.0)
    # More wrapper values available
  ]
```

## External representations

### Binary serialization/de-serialization

```python
def file():
  acc = account()
  path = 'account.bin'

  #Write to file
  with open(path, 'wb') as f:
    bytes_str = acc.SerializeToString() #Bytes string
    f.write(bytes_str)

  #Read from file
  with open(path, 'rb') as f:
    acc = account_pb.Account().FromString(f.read())
```

### JSON serialization/de-serialization

```python
from google.protobuf import json_format

def to_json(message):
  return json_format.MessageToJson(message, indent=4, preserving_proto_field_name=True) # More options are available, see docs

def from_json(json_str, msg_type):
  return json_format.Parse(json_str, msg_type(), ignore_unknown_fields=True)

def json():
  acc = account()
  json_str = to_json(acc)
  print(json_str)

  print(from_json(json_str, account_pb.Account))

```
