# Syntax (version 0.0.19)

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
[1, 2, 3]   # Lists
(1, 2, 'a') # Tuples
{ # ... # } # Scope
Type[a: 4]  # Struct
["key": 45] # Map
```

### Declaring A Variable
```aratar
# Int
def a: 1_000
# Hexadecimal
def a: 0xFF
# A function
def a(text, parse) -> Opt.Int {
    match parse [
        TRUE { text.Opt{Int}() } # Parse the text as an Int
        # Return the first unicode codepoint as an Int
        FALSE { text.codepoints().next().Opt{Int}() }
    ]
}

# Fully written out
let a Int{0~}: Int{0~}(1_000)
# Fully writen out Hexadecimal
let a Hex: Hex(0xFF)
# A function fully written out
let a: Fn(text: Text, parse: Bool) -> Opt{Int} {
    if parse [
        TRUE: { text.Opt.Int() } # Parse the text as an Int
        # Return the first unicode codepoint as an Int
        FALSE: { text.codepoints().next().Opt{Int}() }
    ]
}
```

### Order of operations
There is no explicit order of operations - use `{}` or functions when using
operators.

```aratar
5 * x ^ 3 + 4 * x ^ 2 + 3 * x                               # Doesn't compile
{5 * {x ^ 3}} + {4 * {x ^ 2}} + {3 * x}                     # Compiles
[                                                           # Compiles
    [5, x ^ 3].product(),
    [4, x ^ 2].product(),
    [3, x].product()
].sum()
```

### Functions
```aratar
def sin(num: Float) -> Float {
    # code
}
sin(3.0)
```

## Examples
```aratar
# Use functional replacement for `and` and `or` statements.
if [[a = 2, a != 4].any()
    [b = 5, b = 7].any()
   ].all()
{
    Term.print("a = ", a, " and b = ", b)
}
if [[a = 2, b = 5].all()
    [a != 4, b = 7].all()
   ].any()
{
    Term.print("a = ", a, " and b = ", b)
}

# Declare a function that adds a list of numbers together.
def add: Fn(numbers) -> _ {
    let ret: @Num.ZERO
    numbers.iter(Fn[num] { ret +: num })
    ret
}

let b: 42
let var: @Some(b)

# Maybe change `var` #

var.match[
    Some(b) {
        Term.print("`var` was not changed")
    }
    Some(a: _) {
        Term.print("`var` changed inner value to ", a)
    }
    None() {
        Term.print("`var` changed to NONE")
    }
]
```

## Syntactic Data Structures

### Brackets
- `[]` - List: Literal Arrays/Vectors, Generics, Maps, Sets.
- `{}` - Scope: Multiple expressions executed in sequence, grouping brackets.
- `()` - Tuple: Tuples, Parameters

```aratar
# Fixed-Sized List
let list: [Int; 4](1, 2, 3, 4)
# Dynamic-Sized List
let list: [Int](1, 2, 3, 4)
# Infer
let list: [1, 2, 3, 4]
# Tuple
let tuple: (Int, Text)(42, "Hello, world!")
# Scope
let int Int: {
    let mutable: @Int(4)
    for _: Range(0, 4) {
        mutable +: 1
    }
    mutable
} # int = 8
# Closure / Function
def Int.Text(num) -> Text {
    let ret: @if num < 0 { "-" } else { "" }
    let num: @num.Int().abs()

    for num: Iter(num, Fn(@value) { value /: 10, value != 0 }) {
        let digit: num % 10
        let digit: match digit [
            0 { "0" }
            1 { "1" }
            2 { "2" }
            3 { "3" }
            4 { "4" }
            5 { "5" }
            6 { "6" }
            7 { "7" }
            8 { "8" }
            9 { "9" }
        ]
        ret.append(digit)
    }

    ret
}
# List Functions
def multi_int_to_text: Fn(...: Int) -> Text {
    let ret @Text: @""
    for (i, num): numbers.enumerate() {
        ret.append(num.Text)
        if i != numbers.len - 1 {
            ret ++: ", "
        }
    }
    ret
}
assert[multi_int_to_text(1, 2, 3) = "1, 2, 3"]
```

## Types
```aratar
$Into String
enum Try{E, T}[Err(E): 0, Ok(T): 1]
$Into String, $Into Try
enum Opt{T}[NONE: 0, SOME[@T]: 1]
struct Complex(define real: @Real, define imag: @Imaginary)
typedef Text: [Grapheme]
struct Range(define start: Int, define end: Int)

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
use  # Import a library/module definition `import aratar.fft{0.1}`
api  # Exported public definition `export fft: Fn[@samples] { # ... # }`
def  # Private definition (not exported)
# Local Data Definitions
let     # Declare a variable `let samples: @[1, 2, 3, 4, 5, 6, 7, 8]`
# Global Type definitons
enum    # Define Associated Union Type
struct  # Define Record Type
typedef # Define Type-Safe Type Alias (Must use casting)
# Control Flow
if      # Conditional Branching
match   # Pattern matching
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
& # Pattern Matching And / Intersection Operator
| # Pattern Matching Or / Union Operator
! # Not Operator (includes bitwise)
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
~ # Inclusive range (Example: `1~10` "one thru ten")
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

# Ideas

## Adhoc Enums
The following program demonstrates usage of adhoc enums:

**adhoc.rtr**
```aratar
struct ErrA
struct ErrB
struct ErrC

api ErrA.Text: "A Failed"
api ErrB.Text: "B Failed"
api ErrC.Text: "C Failed"

enum Enum: ErrA | ErrB

api start(a: Int{1,2,4,5}) -> Try(Text, Enum | ErrC) {
    let result: fn_a(a)?
    Ok(fn_b(result)?)
}

def fn_a(int: Int{1,2,4,5}) -> Try(Int, Enum) {
    match int [
        1    { Err(ErrA) }
        2    { Err(ErrB) }
        a: _ { Ok(a) }
    ]
}

def fn_b(int: Int{4~5}) -> Try(Text, ErrC) {
    match int [
        4 { Err(ErrC) }
        5 { Ok("Hello, world!") }
    ]
}
```

Run it in the REPL:
```aratar
use adhoc
adhoc 1
# FAIL: A Failed
adhoc 2
# FAIL: B Failed
adhoc 3
# FAIL: Argument `a` set to 3, expected any of [1, 2, 4, 5]
adhoc 4
# FAIL: C Failed
adhoc 5
# Hello, world!
```

## Asynchronous Code
**async.rtr**
```aratar
# Program to wait one second then print out "1"
api start() {
    let thread: Thread(thread)
    let result: thread.yield()
    Term.print("Result = ", result)
}

def thread() -> Int {
    let seconds: 1
    Duration.Seconds(seconds).yield()
    seconds
}
```

Asynchronous code is built on `Future`:
```aratar
struct Future{T}(
    # Cooperatively gives up CPU until the `Future` is evaluated.
    def yield() -> T
)
```

Implementing `Future`:
```aratar
# Import C API from Aratar runtime
use aasi_timer(
    secs: Int64[0~],
    nanos: Int32[0~999_999_999]
) -> MemAddr

struct Task

api Task.Future: Future(
    yield: Fn() {
        # Set up callback
        let addr: aratar_sys_timer(1, 0)
        # `export` keyword takes a pattern; when matched, stops waiting
        api aasi_expire(addr)
    }
)
```

## Asynchronous Code With Adhoc Enums
```aratar
api start() {
    # Use "Select" operator (`||`) to run both futures at the same time.  This
    # builds an Adhoc enum with two variants with selector and result.
    match Select(get_float(), get_int()).yield() [
        Float(sel, a: _) {
            Term.print("Got float: ", a, " - Then int: ", sel.yield())
        }
        Int(sel, a: _) {
            Term.print("Got int: ", a, " - Then float: ", sel.yield())
        }
    ]
    # Alternatively, you may write want to structure your program like this:
    for result: Select(get_float(), get_int()) {
        match result [
            Float(a: _) {
                Term.print("Got float: ", a)
            }
            Int(a: _) {
                Term.print("Got int: ", a)
            }
        ]
    }
    # Get the results of the two `Future`s (takes 1000 millis, instead of 1500
    # because they're running at the same time)
    let _: [get_float(), get_int()].yield() # Returns [Real; 2]
    let _: (get_float(), get_int()).yield() # Returns (Float, Int)
    let _: {get_float(), get_int()}.yield() # Returns Int
}

def get_float() -> Float {
    Duration.from_millis(1000).yield()
    4.0
}

def get_int() -> Int {
    Duration.from_millis(500).yield()
    55
}
```

## Variadic Arguments
Variadic arguments must always be the last arguments.

```aratar
struct Term()

# Print something that can be turned into text to the terminal.
api Term.print(...: .Text) {
    for text_from: ... {
        for byte: text_from {
            Term.output.write(text_from.Text().List{Byte})
        }
    }
    Term.output.write(Text.Newline().List{Byte})
}
```
