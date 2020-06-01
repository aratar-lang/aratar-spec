# Syntax (version 0.0.16)

### Character Classes
 - Whitespace: \\SPACE
 - Alphabetic: a b c d e f g h i j k l m n o p q r s t u v w x y z
   A B C D E F G H I J K L M N O P Q R S T U V W X Y Z \_
 - Punctuation: ! " # $ % & ' ( ) * + - . / : ; < = > ? @ [ \ ] ^ \` \{ | \}
 - Separator: \\n ,
 - Integer: 0 1 2 3 4 5 6 7 8 9 \_
 - Decimal: 0 1 2 3 4 5 6 7 8 9 \_ .
 - Hexdigit: 0 1 2 3 4 5 6 7 8 9 A B C D E F \_

### Literals
Aratar has no character type, because the amount of data a grapheme cluster
takes up is variable.

```aratar
1_234       # Integer
3.5         # Decimal
6.626_1e-34 # Scientific
f2.0        # Floating point
xF2_A8      # Hexadecimal
b1010_1111  # Binary
1.0+4.0i    # Complex Decimal
f1.0+f4.0i  # Complex Floating point
alphabetic  # Identifier (Lowercase only, no numbers allowed)
"Text 1"    # UTF-8 Text
Bool.TRUE   # Enum Variant (Uppercase only, no numbers allowed)
@1_234      # Mutable data
```

### Built-In Functions ("Intrinsics", but not really)
 - `fn`: Needed to define other functions, can't really get around that
 - `let`: Needed to put variables into scope.
 - `match: fn T R (variant T) [branches Map(Key(T), Fn() -> R)] -> R`: Branching
   building block

### Order of operations
There is no explicit order of operations - use `{}` or functions when using
operators.

```aratar
5 * x ^ 3 + 4 * x ^ 2 + 3 * x                # Doesn't compile
{5 * {x ^ 3}} + {4 * {x ^ 2}} + {3 * x}      # Compiles
sum[mul[5, x ^ 3], mul[4, x ^ 2], mul[3, x]] # Compiles
```

### Functions
```aratar
let sin: Fn(num Float) -> Float {
    # code
}
sin(3.0)
3.0.sin()

# Function definition
let if: fn T (conditional Bool) {then -> T} -> T {
    match conditional [
        TRUE: then
        _: {}
    ]
}
# Function Call
if TRUE {
    out.info("Always runs")
}
```

## Examples
```aratar
# Use functional replacement for `and` and `or` statements.
if all[
    any[a = 2, a != 4]
    any[b = 5, b = 7]
] {
    info["a = ", a, " and b = ", b]
}
if any[
    all[a = 2, b = 5]
    all[a != 4, b = 7]
] {
    info["a = ", a, " and b = ", b]
}

# Declare a function that adds a list of numbers together.
let add: fn[numbers] -> _ {
    let ret: @Num.ZERO
    numbers.each fn(num) {
        ret +: num
    }
    ret
}

let b: 42
let var: @SOME(b)

# Maybe change `var` #

match var [
    SOME(b) {
        info["`var` was not changed"]
    }
    SOME(let a) {
        info["`var` changed inner value to ", a]
    }
    NONE {
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
    let mutable @Int: @4
    Range(0, 4).each fn(_) {
        mutable +: 1
    } # mutable = 8
    mutable
} # int = 8
# Closure / Function
let Int.Text: fn(num) -> Text {
    let ret @Text: if_else(num < 0, "-", "")
    let num @Int: abs(num)
    until num = 0 {
        let digit: num % 10
        let digit: if digit [
            0: { "0" }
            1: { "1" }
            2: { "2" }
            3: { "3" }
            4: { "4" }
            5: { "5" }
            6: { "6" }
            7: { "7" }
            8: { "8" }
            9: { "9" }
        ]
        ret ++: digit
        num /: 10
    }
    ret
}
# List Functions
let multi_int_to_text: fn[numbers Int] -> Text {
    let ret @Text: @""
    numbers.enumerate().iter(fn((i, num)) {
        ret ++: num.Text()
        if i != sub(numbers.len, 1) {
            ret ++: ", "
        }
    })
    ret
}
assert(multi_int_to_text[1, 2, 3] = "1, 2, 3")

let for: fn(iter Iterator $Item, loop Fn($Item)) {
    let iter: @iter
    match iter.next() [
        SOME(item): { loop(item), for(iter, loop) }
        NONE: { }
    ]
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
```

## Ascii Token List
```
# # Comment / Doc Comment
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
#! # Interpreter selector at the top of file
#? # Module level documentation comment
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
