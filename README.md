# Yeet Spec
Specification for the Yeet programming language.

# Types
```
[] - Indices or ranges
{} - Lists
() - 
```

## Numeric Types (All are subtypes of `Num`)
- `Int` - Unconstrained integer can be any real integer.  Represented as big int, or if provable, a ranged int type.
  - `1_234`
  - `0xA0_FF` - Must be capitalized.
  - `0b1010_0100`
- `Dec` - Unconstrained fixed point can be any rational number.  Represented as big decimal, or if provable, a ranged fixed point type.
  - `10.25`
- `Float` - Unconstrained floating point can be any floating point number.  Represented as big float, or if provable, a ranged float.
  - `6.626_070_15e-34`
  - `+inf`
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
def add_func: Int
    # Get first & second parameter
    par first: Int
    par second: Int
    
    # Return the value
    break add_func: first + second
    
def mod_func: Int; rem: Int
    par first, second: Int
    
    break mod_func: first / second
        rem: first % second
```

# Literals and printing
```
# Prints 123
123
# Prints "Hello, world"
"Hello, world"
```

# Terms
- `big _` - A variable stored in the heap that can take up more or less bytes.
- `ranged _` - A variable stored in a register or on the stack, always same number of bytes.
