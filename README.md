[buildbadge]: https://travis-ci.org/jasonwhite/io.svg?branch=master
[buildstatus]: https://travis-ci.org/jasonwhite/io

# IO Streams [![Build Status][buildbadge]][buildstatus]

This is a POSIX and Windows compatible IO stream library for D.

The primary purpose of this package is to provide a better API than what is
currently available in the D standard library. Secondly, it is meant to be fast.
All file operations are implemented using the low-level system calls provided by
the operating system.

The goal is to eventually replace the disparate IO modules in [Phobos][] with
this package. Currently, these include [`std.stdio`][std.stdio],
[`std.mmfile`][std.mmfile], [`std.stream`][std.stream], and
[`std.cstream`][std.cstream].

[Phobos]: http://dlang.org/phobos/
[std.stdio]: http://dlang.org/phobos/std_stdio.html
[std.mmfile]: http://dlang.org/phobos/std_mmfile.html
[std.stream]: http://dlang.org/phobos/std_stream.html
[std.cstream]: http://dlang.org/phobos/std_cstream.html

## Features

 * File streams
 * Memory mapped files
 * Pipes
 * Temporary files
 * Generic stream buffering
 * Text serialization to streams

## Examples

### Sorting Lines

```d
import io, std.array, std.algorithm;

void main()
{
    stdin
        .byLineCopy
        .array
        .sort()
        .each!println;
}
```

### Temporary Files

```d
import io;

void main()
{
    // Create a temporary file to write to. This returns the path to the file
    // and its file handle. The file is automatically deleted when the file
    // handle is closed. The file handle is closed when it falls out of scope.
    auto temp = tempFile();

    // Print to standard output.
    println("Temporary file path: ", temp.path);

    // Write an arbitrary array to the stream.
    temp.file.write("Hello world!");

    // Seek to the beginning.
    temp.file.position = 0;

    // Read in 5 characters.
    char[5] buf;
    temp.file.read(buf);
    assert(buf == "Hello");
}
```

### Memory Maps

```d
import io;
import std.parallelism, std.random;

void main()
{
    // Creates a 1 GiB file
    auto f = File("big_random_file", FileFlags.writeNew);
    f.length = 1024^^3; // 1 GiB

    // Fill the file with random data using a memory map.
    auto map = f.memoryMap!size_t(Access.write);
    foreach (i, ref e; parallel(map[]))
        e = uniform!"[]"(size_t.min, size_t.max);
}
```

## License

[Boost license](/LICENSE.md)
