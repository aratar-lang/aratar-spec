# Aratar (v0.0.18) Programming Language Specification
Aratar takes inspiration mostly from Rust, but also borrows ideas from many
other languages (Ada, Python, Mathematica, C, Haskell, Swift, Lisp and Dart).
It's designed to be at least as easy as Python, while still being strongly typed
with functional programming constructs and a focus on determinism and
correctness verified at compile time.

Aratar is designed to only compile to web assembly, specifically
[WASI](https://github.com/WebAssembly/WASI/blob/master/phases/snapshot/docs.md).
Additional modules for hardware support may also be required.  Translation to
other assemblies may be done afterwards.  This makes it easier to write
optimizations, as it only needs to be done once.

The standard library is the most minimal version of batteries-included possible.
What this means is things like opening a window and playing an audio sample will
be in the standard library.  Most standard libraries at least have file
saving/loading as a system iterface, but these APIs are included to make system
interface support feel more complete.  Things like parsing a WAV file will not
be included, though.

The goal for the compiler is to compile very fast and produce very fast code.
One way this is done is by compiling modules before their dependencies, and by
using very simple incremental compilation (if a function changes, recompile the
function, not the entire module).  The compiler also shall use techniques
inspired from `cargo-watch` and `cargo-web`.

## Style
Recommended: 4 space tabs, 80 column width

- `snake_case`: mutable variable
- `snake_case[]`: function
- `CamelCase`: type
- `CamelCase[]`: struct constructor
- `SCREAMING_SNAKE_CASE` constant, enum variant without associated data
- `SCREAMING_SNAKE_CASE[]` enum variant with associated data

# Specification
- [Syntax](SYNTAX.md)
- [Semantics](SEMANTICS.md)
