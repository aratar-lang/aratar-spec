# Syntax (version 0.0.18)

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
2.0f        # Floating point
0xF2_A8     # Hexadecimal
0b1010_1111 # Binary
1.0+4.0i    # Complex Decimal
1.0f+f4.0i  # Complex Floating point
alphabetic  # Identifier (Lowercase only, no numbers allowed)
"Text 1"    # UTF-8 Text
Bool.TRUE   # Enum Variant (Uppercase only, no numbers allowed)
@1_234      # Mutable data
[1, 2, 'a'] # Lists and tuples
{ # ... # } # Function
Type[a: 4]  # Struct
["key": 45] # Map
```

### Declaring A Variable
```aratar
# Int
let a: 1_000
# Hexadecimal
let a: 0xFF
# A function
let a: Fn[text, parse] -> Opt.Int {
    if parse [
        TRUE { text.Int[] } # Parse the text as an Int
        # Return the first unicode codepoint as an Int
        FALSE { text.codepoints[].next[].Opt.Int[] }
    ]
}

# Fully written out
let a Int.Min(0): Int.Min(0)[1_000]
# Fully writen out Hexadecimal
let a Hex: Hex[xFF]
# A function fully written out
let a Fn[p Text, q Bool] -> Opt.Int: Fn[text Text, parse Bool] -> Opt.Int {
    if parse [
        TRUE: { text.Int.Opt(Int) } # Parse the text as an Int
        # Return the first unicode codepoint as an Int
        FALSE: { text.codepoints[].next[].Opt(Int) }
    ]
}
```

### Built-In Functions ("Intrinsics", but not really)
 - `let`: Needed to put variables into scope.
 - `match: fn(T)(R)[variant T] [branches Map(Key(T), Fn() -> R)] -> R`: Branching
   building block

### Order of operations
There is no explicit order of operations - use `{}` or functions when using
operators.

```aratar
5 * x ^ 3 + 4 * x ^ 2 + 3 * x                               # Doesn't compile
(5 * (x ^ 3)) + (4 * (x ^ 2)) + (3 * x)                     # Compiles
[[5, x ^ 3].mul[], [4, x ^ 2].mul[], [3, x].mul[]].sum[]    # Compiles
```

### Functions
```aratar
let sin: Fn[num Float] -> Float {
    # code
}
3.0.sin[]
```

## Examples
```aratar
# Use functional replacement for `and` and `or` statements.
if [[a = 2, a != 4].any[]
    [b = 5, b = 7].any[]
   ].all[]
{
    sys.out["a = ", a, " and b = ", b]
}
if [[a = 2, b = 5].all[]
    [a != 4, b = 7].all[]
   ].any[]
{
    sys.out["a = ", a, " and b = ", b]
}

# Declare a function that adds a list of numbers together.
let add: Fn[numbers] -> _ {
    let ret: @Num.ZERO
    numbers.iter[Fn[num] { ret +: num }]
    ret
}

let b: 42
let var: @SOME[b]

# Maybe change `var` #

var.match[
    SOME[b]: {
        sys.out["`var` was not changed"]
    }
    SOME[let a]: {
        sys.out["`var` changed inner value to ", a]
    }
    NONE: {
        sys.out["`var` changed to NONE"]
    }
]
```

## Syntactic Data Structures

### Brackets
- `[]` - List: Literal Arrays/Vectors, Parameters, Tuples, Maps, Sets.
- `{}` - Scope: Multiple expressions executed in sequence.
- `()` - Parens: Generics, single expression, group in pattern matching

```aratar
# Fixed-Sized List
let list [Int; 4]: [1, 2, 3, 4]
# Dynamic-Sized List
let list [Int]: [1, 2, 3, 4]
# Infer
let list: [1, 2, 3, 4]
# Tuple
let tuple [Int, Text]: [42, "Hello, world!"]
# Scope
let int Int: {
    let mutable @Int: @4
    Range[0, 4]
        .map[Fn[_] { mutable +: 1 }]
    mutable
} # int = 8
# Closure / Function
let Int.Text: Fn[num] -> Text {
    let ret @Text: (num < 0).then{ "-" }.else{ "" }
    let num @Int: num.abs[]

    Fn[] {
        let digit: num % 10
        let digit: digit.match[
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
        (num = 0).then{ None }.else{ Some[[]] }
    }
        .Iter

    ret
}
# List Functions
let multi_int_to_text: Fn[numbers Int] -> Text {
    let ret @Text: @""
    numbers.enumerate[]
        .map Fn[i, num] {
            ret ++: num.Text
            (i != numbers.len - 1).then{ ret ++: ", " }
        }
    ret
}
assert[multi_int_to_text[1, 2, 3] = "1, 2, 3"]
```

## Types
```aratar
$Into String
enum Try(E, T)[FAIL[E]: 0, PASS[T]: 1]
$Into String, $Into Try
enum Opt(T)[NONE: 0, SOME[@T]: 1]
struct Complex[real @Real, imag @Imaginary]
typedef Text: [Grapheme]
struct Range[start Int, end Int]

# List of built-in types
Fn
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
# Global Data Definitions
use     # Import a library/module definition `use aratar.fft{0.1}`
def     # Exported variable `def fft: Fn[@samples] { # ... # }`
# Local Data Definitions
let     # Declare a variable `let @samples: [1, 2, 3, 4, 5, 6, 7, 8]`
# Global Type definitons
enum    # Define Associated Union Type
struct  # Define Record Type
typedef # Define Type-Safe Type Alias (Must use casting)
# Control Flow
if      # Conditional Branching / Pattern Matching
return  # Early exit from outermost scope or named inner scope
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
& # Pattern Matching Filter (Can be chained)
| # Pattern Matching Or (Can be chained)
! # Pattern Matching Not
? # Try: Early return on error for outermost scope or named inner scope.
< # Less
> # More
= # Equal
. # Structure / Enum Access
, # Separator (In sequences)
" # Text Literals
; # Repetition (within [] lists)
: # Assignment (can have any operator before it to combine, not all work)
' # Naming of code blocks
` # Include raw text in file
@ # Mutable Reference
$ # Attribute
~ # Bitwise Not
&& # Bitwise And
|| # Bitwise Or
^^ # Bitwise Xor
++ # Concatenate
-- # Truncate
** # Dot Product / Matrix Multiply
// # Integer division
%% # Is divisible by
<< # Shift Left
>> # Shift Right
<= # Less Than / Equal To (`≤`)
>= # More Than / Equal To (`≥`)
!= # Not Equal To
#! # Interpreter selector at the top of file
#? # Module level documentation comment
```
