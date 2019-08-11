# Dive Spec
Specification for the Dive programming language.

# Keywords
- `DEF`: Define a function()
- `KEY`: Define a keyword (doesn't use () to call, compile-time code generation)

# Token types
- `0x`: Hexadecimal +
- `0b`: Binary +
- Decimal default +
- `#.#+#.#if` - Complex Floating Point
- `#.#+#.#i` - Complex Fixed Point or inference
- `#.#if`: Imaginary Floating Point (# is a digit within the base system)
- `#.#f`: Floating Point
- `#.#i`: Imaginary Fixed point or inference
- `#.#`: Fixed Point, or inference
- `#`: Int, or inference

# Types
```
[] - Indices, ranges or matrices/vectors
{} - Lists
() - Tuples
```

## Numeric Types (All are subtypes of `Num`)
- `Int` - Unconstrained integer can be any real integer.  Represented as big int, or if provable, a ranged int type.
  - `1_234`
  - `0x_A0_FF` - Must be capitalized.
  - `0b_1010_0100`
- `Dec` - Unconstrained fixed point can be any rational number.  Represented as big decimal, or if provable, a ranged fixed point type.
  - `10.25`
- `Float` - Unconstrained floating point can be any floating point number.  Represented as big float, or if provable, a ranged float.
  - `6.626_070_15e-34`
  - `+INF`
  - `NAN`
- `Complex` - Unconstrained complex number can be any complex number.  Represented as a big complex number, or if provable, a ranged complex number.
  - `4.1+3i`
- `Im` - Unconstrained imaginary number.
  - `5.3i`
- `Num` - Unconstrained number can be any number at all.  Represented as a formula, or if provable any of the other numeric types.

### Fixed size numeric types
- `Int8` - Signed 8-bit integer
- `IntU8` - Unsigned 8-bit integer
- `Int[0--255]` - integer with range 0 through 255 inclusive.
- `Int[0..256]` - integer with range 0 through 255 inclusive.

## `List`
`List`s have unconstrained size, represented as a growable array, or if provable, a fixed-size array type.
```
{1 2 3} # List with 3 integers
```
`List`s inherit functions from the type they are a List of.  For example,
```
assert {1 2 3} * {3 2 1} = {3 4 3}
```

## `Text`

## `Func`
Short for "function".

```
DEF add_func(first:Int second:Int) -> Int
    # Return the value
    break add_func: first + second
    
DEF div_func(
    first,second:Int
) -> (Int rem:Int)
    break div_func: (
        first / second
        rem: first % second
    )
```

# Literals and printing
```
# Prints 123
123
# Prints "Hello, world" (`"` is used for both strings and characters)
"Hello, world"
# Run command (repl creates keywords for each command)
'echo' Hello world
```

# Terms
- `big _` - A variable stored in the heap that can take up more or less bytes.
- `ranged _` - A variable stored in a register or on the stack, always same number of bytes.
