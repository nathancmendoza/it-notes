# The `protoc` Compiler

## Setup

- **Prerequisites**: the curl and unzip utilities installed
- Linux: respective Linux distribution package management
- MacOS: use `brew install protobuf`
- Windows: download the appropriate zip file from [here](https://github.com/protocolbuffers/protobuf/releases), extract it and add it to the `PATH` environment variable

## Usage

### `--$(language)_out`

The following language support is built into `protoc`

```shell
$ protoc --help
...
  --cpp_out=OUT_DIR           Generate C++ header and source.
  --csharp_out=OUT_DIR        Generate C# source file.
  --java_out=OUT_DIR          Generate Java source file.
  --kotlin_out=OUT_DIR        Generate Kotlin file.
  --objc_out=OUT_DIR          Generate Objective-C header and source.
  --php_out=OUT_DIR           Generate PHP source file.
  --pyi_out=OUT_DIR           Generate python pyi stub.
  --python_out=OUT_DIR        Generate Python source file.
  --ruby_out=OUT_DIR          Generate Ruby source file.
  --rust_out=OUT_DIR          Generate Rust sources.
...
```

Languages without a `*_out` (like JavaScript) require a separate plugin to be installed

### `-I` or `--proto_path`

Option used to specify an import search `PATH`

```shell
$ protoc --help
...
-IPATH, --proto_path=PATH   Specify the directory in which to search for
                              imports.  May be specified multiple times;
                              directories will be searched in order.  If not
                              given, the current working directory is used.
                              If not found in any of the these directories,
                              the --descriptor_set_in descriptors will be
                              checked for required proto file.
...
```

This is similar to the `-I` flag with gcc or clang to specify an include path

### `--encode` and `--decode`

```protobuf
// dummy.proto
message Hello {
  string text = 1;
}
```

These options work together to encode and decode protocol buffers. It is useful for debugging purposes

```plaintext
text: "Greetings"
```

- Use `cat message.txt > protoc --encode=Hello hello.proto > message.bin` to encode the message and store it into a file
- Use `cat message.bin > protoc --decode=Hello hello.proto` to decode the binary message. It should be the same as the original `message.txt`

### `--decode_raw`

```shell
$ protoc --help
...
  --decode_raw                Read an arbitrary protocol message from
                              standard input and write the raw tag/value
                              pairs in text format to standard output.  No
                              PROTO_FILES should be given when using this
                              flag.
...
```

This options allows working with protocol buffers when the message type is unknown. It decodes up until the tags and can help determine which definition what used to generate the message

```shell
$ cat message.txt > protoc --encode=Hello dummy.proto | protoc --decode_raw
1: "Greetings!"
```

Notice how the field names are replaced by a tag in the output. That tag should match the definition of the message used to serialize the contents
