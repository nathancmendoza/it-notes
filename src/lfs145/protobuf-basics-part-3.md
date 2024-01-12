# Protocol Buffers Basics (Part 3)

## All in one file

The advice "define a class per file" for object-oriented programming does not *always* apply to protocol buffer definitions. Consider some wrapper messages to wrap JSON values. Since these are tightly related, it would not be useful to define a single message per file.

Additionally, protocol buffers only define a schema, and no business logic. Thus, they require less space that a class.

On the other hand, defining *all* messages in a single file is not ideal either. 

1) A poorly chosen file name may become too broad and entice developers to place messages where they would not fit elsewhere
2) Large protocol buffer definitions can be difficult to navigate and might scare off other developers

## Imports

To import a .proto file in a message, simply write: `import “myfile.proto”;`

- Gives you access to messages defined in `myfile.proto`
- May be many messages that could be tightly related, or a single message

## Packages

Packages are defined in `.proto` files like this: `package a.b;`

- A package parent gives a broader context than the file name
- The file name's context has the same nuance as before
- Packages allow usage of message defined by others

Take the example definition of a company file system

```protobuf
package mycompany.fs;

message File { /*…*/ }

message Folder {
  repeated File files = 1;
}
```

A file might need to track the `created_at` timestamp, perhaps using a `Timestamp` message defined by Google

```
package mycompany.fs;

import “google/protobuf/timestamp.proto”; 

message File {
  google.protobuf.Timestamp created_at = 1;
}
```

## Nested messages

Protocol buffers allow the nesting of messages where the inner class is only relevant in the context of the parent class. For instance, let's define 2 message for a `Dog` and a `Cat` that also store the breed of the animal

```protobuf
message Cat {
  enum Breed {
    UNSPECIFIED = 0;
    BENGAL = 1;
    BURMESE = 2;
  } 

  Breed breed = 1;
} 

message Dog {
  enum Breed {
    UNSPECIFIED = 0;
    DALMATIAN = 1;
    DOBERMANN = 2;
    //…
  } 

  Breed breed = 1;
}
```

The two animals are defining their specific set of breeds, and if an external message wants to access a breed, it will have to use the fully qualified name for that message which `Cat.Breed` or `Dog.Breed`

## Practice

In order to practice what we learned in the previous section, create the following messages/enums in your favorite text editor/IDE:

Course with the following fields:

- Name
- Authors (0+ authors)
- Lectures (searchable by lecture name, so the name is linked to a lecture)

Lecture with the following fields:

- Content (either Video or Article)

Video with the following fields:

- Type (only supporting MP4 and MOV right now)
- Url

VideoType with the following variants:

- MP4
- MOV
- An alternative for non-supported types

Article with the following fields:

- Text

### Messages at the same level

```protobuf
syntax = "proto3";

message Article {
  string text = 1;
}

enum VideoType {
  UNSPECIFIED = 0;
  MP4 = 1;
  MOV = 2;
}

message Video {
  VideoType type = 1;
  string url = 2;
}

message Lecture {
  oneof content {
    Video video = 1;
    Article article = 2;
  }
}

message Course {
  string name = 1;
  repeated string authors = 2;
  map<string, Lecture> lectures = 3;
}
```

### Nested messages

```protobuf
syntax = "proto3";

message Course {

  message Lecture {

    message Video {

      enum Type {
        UNSPECIFIED = 0;
        MP4 = 1;
        MOV = 2;
      }

      VideoType type = 1;
      string url = 2;
    }

    message Article {
      string text = 1;
    }

    oneof content {
      Video video = 1;
      Article article = 2;
    }
  }

  string name = 1;
  repeated string authors = 2;
  map<string, Lecture> = 3;
}
```

### Imports

#### `Course.proto`

```protobuf
syntax = "proto3";

import "lecture.proto";

message Course {
  string name = 1;
  repeated string authors = 2;
  map<string, Lecture> lectures = 3;
}
```

#### `Video.proto`

```protobuf
syntax = "proto3";

enum VideoType {
  UNSPECIFIED = 0;
  MP4 = 1;
  MOV = 2;
}

message Video {
  VideoType type = 1;
  string url = 2;
}
```

#### `Article.proto`

```protobuf
syntax = "proto3";

message Article {
  string text = 1;
}
```

#### `Lecture.proto`

```protobuf
syntax = "proto3";

import "article.proto";
import "video.proto";

message Lecture {
  oneof content {
    Video video = 1;
    Article article = 2;
  }
}
```

### Packages

#### `Course.proto`

```protobuf
syntax = "proto3";

package mycompany.mooc;

import "lecture.proto";

message Course {
  string name = 1;
  repeated string authors = 2;
  map<string, Lecture> lectures = 3;
}
```

#### `Video.proto`

```protobuf
syntax = "proto3";

package mycompany.mooc.content;

enum VideoType {
  UNSPECIFIED = 0;
  MP4 = 1;
  MOV = 2;
}

message Video {
  VideoType type = 1;
  string url = 2;
}
```

#### `Article.proto`

```protobuf
syntax = "proto3";

package mycompany.mooc.content;

message Article {
  string text = 1;
}
```

#### `Lecture.proto`

```protobuf
syntax = "proto3";

package mycompany.mooc;

import "article.proto";
import "video.proto";

message Lecture {
  oneof content {
    mycompany.mooc.content.Video video = 1;
    content.Article article = 2;
  }
}
```
