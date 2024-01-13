# Language Interoperability

## Setup

### Prerequisites

- Gradle installed
- Java 18+ (early versions may not work)

### Java environment

Setup of the environment with Gradle

```shell
$ gradle init

Welcome to Gradle 8.5!

Here are the highlights of this release:
 - Support for running on Java 21
 - Faster first use with Kotlin DSL
 - Improved error and warning messages

For more details see https://docs.gradle.org/8.5/release-notes.html

Starting a Gradle Daemon (subsequent builds will be faster)

Select type of project to generate:
  1: basic
  2: application
  3: library
  4: Gradle plugin
Enter selection (default: basic) [1..4] 2

Select implementation language:
  1: C++
  2: Groovy
  3: Java
  4: Kotlin
  5: Scala
  6: Swift
Enter selection (default: Java) [1..6] 3

Generate multiple subprojects for application? (default: no) [yes, no] no
Select build script DSL:
  1: Kotlin
  2: Groovy
Enter selection (default: Kotlin) [1..2] 2

Select test framework:
  1: JUnit 4
  2: TestNG
  3: Spock
  4: JUnit Jupiter
Enter selection (default: JUnit Jupiter) [1..4]

Project name (default: java): pb
Source package (default: pb):
Enter target version of Java (min. 7) (default: 21): 18
Generate build using new APIs and behavior (some features may change in the next minor release)? (default: no) [yes, no]

> Task :init
To learn more about Gradle by exploring our Samples at https://docs.gradle.org/8.5/samples/sample_building_java_applications.html

BUILD SUCCESSFUL in 1m 3s
2 actionable tasks: 2 executed
```

In your build script include

```gradle
plugins {
  id "com.google.protobuf" version "0.8.19"
}

buildscript {
  ...
  dependencies {
    classpath 'com.google.protobuf:protobuf-gradle-plugin:0.8.19'
  }
}
```

Set up protoc compilation

```gradle
protobuf {
  protoc {
    artifact = 'com.google.protobuf:protoc:3.21.6'
  }
}
```

## De-serialize from java

### Code generation

Place a `proto` directory under `src/main` to be recognized by Gradle

```protobuf
syntax = "proto3";

option java_package = "org.lf.pbtutorial";
option java_multiple_files = true;

// These options will influence the generated code. See docs for all available options

message Account {
  uint64 id = 1;
  string name = 2;
  bool is_verified = 3;
  repeated uint64 following = 4;
}
```

Using Gradle, the protocol buffer will by place in the `build/generated` directory

### De-serialization

Say that the file `account.bin` was serialized using Python code. The following Java program can de-serialize into an `Account` class 

```java
package pb;

import java.io.FileInputStream;
import java.io.IOException;

import org.lf.pbtutorial.Account;

public class App {
  private static void readFrom(String path) {
    try {
      FileInputStream fs = new FileInputStream(path);
      Account account = Account.parseFrom(fs);
      
      System.out.println(account);
    } catch (IOException e) {
      e.printStackTrace();
    }
  }

  public static void main(String[] args) {
    if (args.length != 1) {
      System.out.println("The program needs one arguments!");
      return;
    }

    readFrom(args[0]));
  }
}
```
