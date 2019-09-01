# Aratar Spec
Specification for the Aratar programming language.  Aratar is similar to Python,
but lower level.

```
# Declare a variable
var := Int 0
# Set a variable
var: 1
# Cast a variable
var: Float(0)
# Get reference
var: @Int @var

# Declare a global constant
VAR :: Num 0

# Define a function (parameterized variable)
function():
    # Code
function;

# Define a macro (parameterized variable), resolves to a constant.
FUNCTION():
    # Code
FUNCTION;

# If/Case/Elif/Else statement
a = b < c:          # if (a = b) & (b < c)
    # Code
2:                  # else, if a = 2 (case 2:)
    # Code
b=3:                # else, if b = 3
    # Code
_:                  # else (case default:)
    # Code
;

# Infinite Loop
'a:
    # Code
'a;

# Iterator Loop (prints "1" "2" "3" each on their own line).
'i: [1 2 3]
    i
'i;

# While Loop
running :: Bool TRUE
'a running:
    # Code
'a;
```

# Syntax
```
:   # Declare immutable (cannot be set again), open scope.
::  # Declare mutable (can be set again).
:=  # Set mutable (can be set again).
;   # Close scope, array repeat.
.   # Access inside module, struct, array (indices and ranges), decimal point
..  # Inclusive-Exclusive range, math notation: [)
... # Inclusive range, math notation: []
@   # Reference Modifier, Get A Mutable Reference (Immutable can be inferred)
[]  # Lists (Possibly vectors or matrices), Slice / Element Indices
{}  # Sets (Lists without duplicates - have additional operators), HashMaps
()  # Tuples (including function parameters)

## OPERATORS ##
+-/*%   # Add, Subtract (Negation), Divide, Multiply, Modulo (Remainder)
++      # Concatenate
//      # Integer (discrete) Divide
**      # Exponent
%%      # Dot Product, Matrix Multiplication
>>      # Shift Right
<<      # Shift Left
==      # Is approximately equal (for floats)
~~      # (Set) Not - Uses First Set as the domain.
&&      # (Set) And
||      # (Set) Or
^^      # (Set) Xor
=       # Is Equal To
~=      # Is Not Equal To (Equal, Followed by Not)
<       # Is Less Than
>       # Is More Than
<=      # Is Less Than Or Equal To
>=      # Is More Than Or Equal To
~       # (Bitwise) Not
&       # (Bitwise) And
~&      # (Bitwise) Nand (And, Followed by Not)
|       # (Bitwise) Or
~|      # (Bitwise) Nor (Or, Followed by Not)
^       # (Bitwise) Xor
~^      # (Bitwise) Xnor (Xor, Followed by Not)
?       # Rust-Style Try Operator
## Double operators with no special meaning ##
--      # Double negation (Addition, No-Op)
??      # Double Try Operator
## A few _ assign operators
+:      # Add assign
++:     # Append assign
```

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
- `Int[0...255]` - integer with range 0 through 255 inclusive.
- `Int[0..256]` - integer with range 0 through 255 inclusive.

## `List`
`List`s have unconstrained size, represented as a growable array, or if provable, a fixed-size array type.
```
[1 2 3] # List with 3 integers
```
`List`s inherit functions from the type they are a List of.  For example,
```
assert [1 2 3] * [3 2 1] = [3 4 3]
```

## `Text`

## `Func`
Short for "function".

```
add_func(first: Int; second: Int): Int
    # Return the value
    break add_func: first + second
:add_func
    
div_func(
    first, second: Int
): (Int rem:Int)
    break div_func: (
        first / second
        rem: first % second
    )
div_func;
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
