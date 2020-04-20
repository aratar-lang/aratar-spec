# Syntax

## Tokens
- `[]` - Vector: A comma/newline-separated list of same-typed data.
- `()` - Tuple: A comma/newline-separated list of different-typed data.
- `{}` - Block: A comma/newline-separated list of function calls.
- `name` - Identifier: A name of an item in scope.
- `value` - Value: an identifier, tuple, or vector.

## Simplification (Parenthesis)
One-element tuples, arrays, and scopes are equivalent to just the elements.

- `(a)` → `a`
- `[a]` → `a`
- `{a}` → `a`

## Function Calls
Function calls are separated by commas or newlines.

```aratar
function_name()                     # Call function w/ no args
function_name(value)                # Call function w/ 1 arg (*Simplification)
function_name(value, value, value)  # Call function w/ 3 args
function_name[value, value]         # Call function w/ variable args
function_name {                     # Call function w/ closure
    expression
    expression
}
```

## Methods
Methods must start with one of: `.`, `=`, `<`, `>`, `~`, `+`, `-`, `*`, `^`,
`?`, `/`, `%`, `&`, `|`, `!`, `:`, `@`, `$`; All are invalid characters in
function names.  These characters are used to determine whether after a newline
is actually a new function call, or a method call.

```aratar
function_name                       # Call function w/ no args
function_name(value)                # Call function w/ 1 arg (*Simplification)
function_name(value, value, value)  # Call function w/ 3 args
function_name[value, value]         # Call function w/ variable args
0.5.sin
1 = 2
```

### Suggested Method Meanings

#### Binary Operators
These are the only methods that don't require 1 arg to be parenthesis.

- `.` General method
- `,,` Range iterator up to (`0,,10` is range from 1.0 to 2.0, not including 2.0)
- `;;` Range iterator up to (`0;;10` is range from 1.0 to 2.0, including 2.0)
- `<<` Big Endian Bit Shift Left
- `>>` Big Endian Bit Shift Right
- `<` Less Than
- `>` More Than
- `<=` Less Than / Equal To (`≤`)
- `>=` More Than / Equal To (`≥`)
- `=` Exact Equivalence
- `!=` Not Equal To
- `+` Add
- `++` Concatenation
- `-` Subtract
- `*` Multiply
- `**` Dot Product / Matrix Multiply
- `^` Power (Exponent)
- `/` Division
- `//` Integer division
- `%` Modulo
- `%%` Is divisible by
- `%/` Division with remainder joined as struct.
- `&` Bitwise And
- `|` Bitwise Or
- `$` Bitwise Xor
- `:` Assignment (Can be prepended by another operator, for example add assign:
  `+:`)
- `::` Swap

#### Unary Operators
- `!` Bitwise Not
- `@` Get mutable reference by moving immutable reference, or share mutable ref.
- `~` Dereference (Move so that old variable is out of scope)
- `?` Try - If equal to 0 (or 0-equivalent), return 0 (or 0-equivalent).
  Otherwise, evaluate to non-zero value.

```
# Get the sine of 4.5, then add 1
4.5 .sin() + 1
#
let (a, b): (1, 2)
```

## Keywords
These functions are built in to the compiler.

- `if` - Branching Control Flow.
- `else` - Branching Control Flow.
- `match` - Pattern Matching.
- `def` - Define a global constant (can never change).
- `let` - Create a variable.  Once assigned, can't change value.
- `for` - Data iteration.
- `type` - Define tagged union (enum) type, record (struct), or typed alias.
- `return` - Return (optionally a value) from a scope (function or loop).

## Functions
```aratar
# Pattern selector (operand, [method (returning Bool), argument, block]).
match (a, b) [
    (2, 3) {
        out["a = 2, and b = 3"]
    }
    (2, var Int) {
        out["a = 2, and b ≠ 3, but rather b = ", var, "."]
    }
    var (Int, Int) {
        # Catch anything not covered yet (not a catch all - otherwise
        # this wouldn't compile because patterns that cover same input must have
        # same result).
        out["a ≠ 2, and b ≠ 3, (a, b) = ", var, "."]
    }
]

if all[a = 2, b = c = 3] {
    out["b & c are 3, a is 2"]
} else {
    out["either b or c are not 3, or a is not 2"]
}

fn function(argument_one Int, argument_two UInt32) Float32 {
    let ret: 1.0

    ret
}

## This function takes a list of things and strings them together as Text.
##
## ```aratar
## assert(format["Hello, ", "World! ", 123] = "Hello, World! 123")
## ```
fn format[elements] Text {
    let return_var @Text: ""
    'a: for element: elements {
        return_var ++: Text(element)
        # If we wanted to return from the loop call function: `return 'a`
    }
    return_var
}

## Create a discriminated (tagged) union type (enum).
type TaggedUnion SInt8 [
    VARIANT_A: 1
    VARIANT_B(int Int): 2
    VARIANT_C
]

## Create a data structure.  All fields are private (only methods can access).
type DataStruct(
    field_a SInt32
    field_b Float32
    field_c Text: "Default value"
)

## Create an alias for DataStruct, must cast to use as a DataStruct
type SameDataStruct: DataStruct

## Create a constructor for DataStruct.
fn DataStruct(self, field_a SInt32, field_b Float32) DataStruct {
    DataStruct(
        :field_a:
        :field_b:
    )
}
```

## Methods
```aratar
# Or (One-line function call with trailing comma)
assert(any[TRUE, FALSE = TRUE])

# If all are true (One-line function call)
assert(all[TRUE, FALSE = FALSE])

# If exactly one is true (Multi-line function call)
assert(one[TRUE, FALSE = TRUE])
```

## Functions
- `assert()`, `picture.send()`

```
# Assert
assert(TRUE)
# Send picture to display
picture.send(picture)
```
