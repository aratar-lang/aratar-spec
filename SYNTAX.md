# Syntax (version 0.0.14)

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
    for i: (0, 4) {
        mutable +: 1
    } # mutable = 8
    mutable
} # int = 8
# Closure / Function
let int_to_text Func(num Int) -> Text: (num Int) -> Text {
    let ret @Text: num < 0 .if_else("-", "")
    let num @Int: abs(num) # or num.abs()
    while num != 0 {
        let digit: num % 10
        let digit: match digit [
            0: "0",
            1: "1",
            2: "2",
            3: "3",
            4: "4",
            5: "5",
            6: "6",
            7: "7",
            8: "8",
            9: "9",
        ]
        ret.append(digit)
        num /: 10
    }
    ret
}
# List Functions
let multi_int_to_text Func[numbers Int] -> Text: [numbers Int] -> Text {
    let ret @Text: ""
    numbers.iter()
        .do_skip_last(
            { ret ++: int_to_text(num) }    # Iterate through all numbers
            { ret ++: " " }                 # Iterate through all but last
        )
    ret
}
```

All of these sequences implement `Transparent T` on one element, where the
element is type `T`.

```aratar
# Transparent allows using all types of brackets as parentheses
assert({[(1 + 1) * 2] * 2} * 2 = 16)
```

## Types
```aratar
enum Try E T: [FAIL(E): 0, PASS(T): 1]
enum Opt T: [NONE: 0, SOME(T): 1]
struct Complex: (real Real, imag Imaginary)
typedef Text: [Grapheme]
struct Range: (start Int, end: Int)
```

## Keyword List
```aratar
enum    # Define Assoicated Union Type
struct  # Define Record Type
typedef # Define Type Alias (Transparent, but type safe)
let     # Declare a variable
if      # Conditional test
else    # Other path
for     # Iterative Loops
match   # Conditional branching based on pattern matching
break   # Break out of innermost block (or a named block) - includes return
while   # Infinite Loops / Conditional Loop
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
$ # Bitwise XOR
! # Bitwise Not
? # Break out of innermost block (or named block) If Not Opt.SOME / Try.PASS
< # Less
> # More
= # Equal
. # "Self" Method Call
, # Separator (In sequences)
" # Text Literals
; # Repetition (within [] lists)
: # Assignment
' # Naming of code blocks
\ # Attributes
` # Include raw text in file
@ # Mutable Reference
~ # Dereference (Move / Copy out of reference)
<< # Shift Left
>> # Shift Right
<= # Less Than / Equal To (`≤`)
>= # More Than / Equal To (`≥`)
!= # Not Equal To
++ # Concatenation
** # Dot Product / Matrix Multiply
// # Integer division
%% # Is divisible by
```
