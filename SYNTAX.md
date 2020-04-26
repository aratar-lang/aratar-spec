# Syntax (version 0.0.14)

## Examples
```
# Same as if [{a = 2 or a != 4} and {b = 5 or b = 7}]
if a = 2 or a != 4
    and b = 5 or b = 7
{
    info["a = ", a, " and b = ", b]
}

# Same as if [{a = 2 and b = 5} or {a != 4 and b = 7}]
if a = 2 and b = 5
    or a != 4 and b = 7
{
    info["a = ", a, " and b = ", b]
}

# Declare a function that adds a list of numbers together.
let add: fn[numbers] -> _ {
    let ret @: 0
    for num :: numbers {
        ret +: num
    }
    ret
}

let b: 42
let var @: SOME(b)

# Maybe change `var` #

if var [
    = SOME(b) {
        info["`var` was not changed"]
    }
    = SOME(let a) {
        info["`var` changed inner value to ", a]
    }
    = NONE {
        info["`var` changed to NONE"]
    }
]


```

## Syntactic Data Structures

### Sequences of Expressions
- `[]` - List: Same-typed (Literal Arrays/Vectors).
- `()` - Tuple: Different-typed (Parameters, Literal Tuples)
- `{}` - Block: Multiple expressions executed in sequence.

```aratar
# Fixed-Sized List
let list [Int; 4]: [1, 2, 3, 4]
# Dynamic-Sized List
let list [Int]: [1, 2, 3, 4]
# Infer
let list: [1, 2, 3, 4]
# Tuple
let tuple (Int, Text): (42, "Hello, world!")
# Block
let int Int: {
    let mutable @Int: 4
    for _ :: Range(0, 4) {
        mutable +: 1
    } # mutable = 8
    mutable
} # int = 8
# Closure / Function
let int_to_text Fn(num Int) -> Text: fn(num Int) -> Text {
    let ret @Text: if_else(num < 0, "-", "")
    let num @Int: abs(num)
    until num = 0 {
        let digit: num % 10
        let digit: if digit [
            = 0 { "0" }
            = 1 { "1" }
            = 2 { "2" }
            = 3 { "3" }
            = 4 { "4" }
            = 5 { "5" }
            = 6 { "6" }
            = 7 { "7" }
            = 8 { "8" }
            = 9 { "9" }
        ]
        ret ++: digit
        num /: 10
    }
    ret
}
# List Functions
let multi_int_to_text Fn[numbers Int] -> Text: fn[numbers Int] -> Text {
    let ret @Text: ""
    for (i, num) :: enumerate(numbers) {
        ret ++: int_to_text(num)
        if i != len(numbers) - 1 {
            ret ++: " "
        }
    }
    ret
}
assert(multi_int_to_text[1, 2, 3] = "1 2 3")
```

All of these sequences implement `Transparent T` on one element, where the
element is type `T`.

```aratar
# Transparent allows using all types of brackets as parentheses
assert({[(1 + 1) * 2] * 2} * 2 = 16)
```

## Types
```aratar
$[Into String]
enum Try E T: [FAIL(E): 0, PASS(T): 1]
$[Into String, Into Try]
enum Opt T: [NONE: 0, SOME(@T): 1]
struct Complex: (real @Real, imag @Imaginary)
typedef Text: [Grapheme]
struct Range: (start Int, end Int)

# List of built-in types
Fn()
Bool
Text
Opt
Int
Float
Num
Double
Grapheme
AsciiText
AsciiChar
Data
Imaginary
Real
Complex
Dec
```

## Keyword List
```aratar
enum    # Define Associated Union Type
struct  # Define Record Type
typedef # Define Type Alias (Transparent, but type safe)
let     # Declare a variable
if      # Conditional Branching / Pattern Matching
return  # Early return out of innermost block or named block - includes break
until   # Conditional Loop
for     # Iterative Loops
and     # Boolean and
or      # Boolean or
not     # Boolean not
xor     # Boolean xor
```

## Ascii Token List
```
+ # Add / Identity
- # Subtract / Negation
* # Multiply
/ # Divide
% # Modulo
^ # Exponentiate
& # Bitwise And
| # Bitwise Or
~ # Bitwise Xor
! # Bitwise Not
? # Break out of innermost block (or named block) If Not Opt.SOME / Try.PASS
< # Less
> # More
= # Equal
. # Structure / Enum Access
, # Separator (In sequences)
" # Text Literals
; # Repetition (within [] lists)
: # Assignment
' # Naming of code blocks
` # Include raw text in file
@ # Mutable Reference
\ # Dereference (Move / Copy out of reference)
$ # Attribute
<< # Shift Left
>> # Shift Right
<= # Less Than / Equal To (`≤`)
>= # More Than / Equal To (`≥`)
!= # Not Equal To
++ # Concatenate
-- # Truncate
** # Dot Product / Matrix Multiply
// # Integer division
%% # Is divisible by
:: # Iterative Assign
+: # Add Assign
-: # Subtract Assign
*: # Multiply Assign
/: # Divide Assign
%: # Modulo Assign
^: # Exponentiate Assign
&: # Bitwise And Assign
|: # Bitwise Or Assign
~: # Bitwise XOR Assign
!: # Bitwise Not Assign
!! # (Set) Not - Uses Second Set as the domain.
&& # (Set) And
|| # (Set) Or
~~ # (Set) Xor
++: # Concatenate Assign
--: # Truncate Assign
>>: # Right Shift Assign
<<: # Left Shift Assign
//: # Integer Division Assign
**: # Dot Product Assign
!!: # (Set) Not Assign - Uses Second Set as the domain.
&&: # (Set) And Assign
||: # (Set) Or Assign
~~: # (Set) Xor Assign
```

## Numeric Literals
```
255
0xFF
0b1111_1111
INF
NAN
255+3i
3i
6.626_070_15e-34
```
