# Aratar Spec
Specification for the Aratar programming language.  Aratar is similar to Python,
but lower level (like Rust).

## Style
- `snake_case`: mutable variable
- `snake_case()`: function
- `Darwin_Case`: type, constant
- `Darwin_Case()`: constructor, enum variant with associated data

## C Functions Interoperability
- `mylib.My_Type() -> My_Type` -> `mylib_MyType mylib_MyType_New()`
- `mylib.My_Type.my_function()` -> `void mylib_MyType_myFunction()`

## Brackets
- `()`: Records with either named (structs) or unnamed (tuples) fields.
- `[]`: Dynamic-sized or fixed-sized lists, enumerations
- `{}`: Scope for list of expressions

## Function Calls
- `myfunction a, b` calls function `myfunction()` with two parameters: `a`, `b`.

## Prelude (Keywords)
- `def function(...) {}` - Define a function, returns unit `()`.
- `def function(...) -> Type {}` - Define a function, returns `Type`.
- `let field: value` - Declare a variable, infer type.
- `let field Type: value` - Declare a variable, specifying type.
- `if expression {}` - Execute code if expression is true.
- `return expression` - Return function.
- `for loop_field` - Infinite loop, manual break.
- `for loop_field: iterator` - Iteration loop.
- `break loop_field: expression` - Break loop by name.
- `assert expression` - Fail to compile if expression can ever be false.

## 
Declare mutable or immutable variable (depending on style):
```aratar
def VAR: value # constant
def var: value # immutable
let var: value # mutable (yes, identifiers can have same name as keywords)
```

Define function (parameters are always immutable - implicit `let`):
```aratar
@function(param Type, param_2 Another_Type):
    /* Code */
:function()
```

Define enum, discriminated union:
```aratar
def My_Enum [
    FIRST_VARIANT
    SECOND_VARIANT(Int)
] My_Enum
def CONSTANT: 0
```

## If Statements
```aratar
def var: 5
if var [
    5 {
        # If var = 5 is true
    } 5
    _ {
        # If var = 5 is false
    } _
] var
if var = 5 {
    # If var = 5 
}
if var [
    < 5 {
    }
    = 5 {
    }
    > 5 {
    }
]
if (var > 5 and < 10) or (constant = 2 or 5) [
    YES {
    }
    NO {
    }
]

# 
def my_var: Opt.YES 5
if my_var [
    YES a {
        Text a
    }
    NO {
        "variable my_var is NO"
    }
]
```

## Terminal
You can use aratar as a terminal shell:

```aratar
~/ # Implicit cd to container's root folder.
ls -a, -l # List files (or `ls -al`).
```

Will print out the files in the root folder of the container.

```aratar
def ls | args ... {
    aratar.exec ~/.path/ls, args
}
```

## Nested Functions
```
assert (max (min 4, 2), 3) = 3
```

## Built-In Trait Functions
Functions that return `Opt[Opt]` return `FALSE` if it cannot be determined, `TRUE[FALSE]` if it is not true, and `TRUE[TRUE]` when it is true.

<!--
- Equivalence `Any.=(other Any) -> Opt[Opt]` for `self = other` (`Any_Eq()`)
```
= ~=
```
-->

- Not `Any.not() -> Opt` for `~other`, purposely not implemented on numeric types for safety (Use `Int(!Data(my_int))`, if you need to).
```
!
```

- Less Than `Any.order() -> Opt[Opt]` for `self < other` (`Any_Order`)
```
< > <= >= = !=
```

##

```
# Declare a variable
let var Int: 0
let var: 0

# Set a variable
var: 1
# Cast a variable
var: Float(0)
# Get reference
var: @Int @var
# Declare local immutable variable
VAR Int: var + 2

# Declare a global constant
VAR Num: 0

# Define a function (parameterized variable)
function() {
    # Code
} function

# Define a macro (parameterized variable), resolves to a constant or algorithm.
FUNCTION() {
    # Code
} FUNCTION

# If/Case/Elif/Else statement (possible)
on(value Any) {} # on is a prefix function on Any.
Bool.and(other Bool) -> bool
Bool.or(other Bool)
Bool.xor(other Bool)
Bool.not(other Bool)

if a = 0 {
    # If statement.
}

if a = b < c or a = 0 {
    # If statement with logical connector.
}

if a
    = b < c {
        # if (a = b) & (b < c)
    }
    = 2 {
        # else, if a = 2 (case 2:)
    }
    = _ and b = 3 {
        # else, if b = 3
    }
    = _ {
        # else (case default:)
    }

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
[]  # Lists (Possibly vectors or matrices), Records, HashMaps
{}  # Scopes
()  # Tuples (including function parameters), Slice / Element Indices

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
{1 2 3} # List with 3 integers
```
`List`s inherit functions from the type they are a List of.  For example,
```
? {1 2 3} * {3 2 1} = {3 4 3}
```

## `Data`

## `Text`

## `Bool`

## `Func`
Short for "function".

```
add_func(first: Int; second: Int): Int {
    # Return the value
    break add_func: first + second
} add_func()
    
div_func(
    first, second: Int
): (Int rem:Int)
{
    break div_func: (
        first / second
        rem: first % second
    )
} div_func()
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
