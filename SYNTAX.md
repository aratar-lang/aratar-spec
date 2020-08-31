# Syntax (version 0.0.20)

### Literals
Starts with:
 - `\t`|`\r`: Compiler Error
 - `_`:
   - `0~9`|`a-z`|`_`: Unused identifier
   - `A-Z`: Compiler error
 - `a`~`z`:
   - `0~9`|`a-z`|`_`: Identifier (functions, variables, and keywords)
     - `import`: Import a library/module definition `import aratar.fft{0.1}`
     - `export`: Exported public definition `export fft: Fn[@samples] { # ... # }`
     - `def`: Definition
     - `let`: Declare a variable `let samples: @[1, 2, 3, 4, 5, 6, 7, 8]`
     - `enum`: Define Associated Union Type
     - `struct`: Define Record Type
     - `typedef`: Define Type-Safe Type Alias (Must use casting)
     - `for`: Iteration
     - `if`: Conditional Branching
     - `else`: Alternative Conditional Branch
     - `elif`: Compressed `else { if {} }`
     - `match`: Pattern matching
     - `return`: Early exit from outermost scope or named inner scope
     - `syscall`: A syscall (unsafe, can only call from module named "sys")
   - `A`~`Z`: Compiler error
 - `A`~`Z`:
   - `0`~`9`|`A`~`Z`: Type (constants, structs, enums, and enum variants)
   - `a`~`z`|`_`: Copmiler Error
 - `0`~`9`: Literal Number
   - `0`~`9`|`_`|`.`: Decimal (base 10) Literal
     - `e`: ×10^x
     - `f`: Floating point
     - `i`|`j`: Imaginary
   - `0x`: Hexadecimal (base 16) Literal
     - `0`~`9`|`A`~`F`: Hexadecimal
     - `a`~`z`|`G`~`Z`: Compiler Error
   - `0b`: Binary (base 2) Literal
     - `0~1`: Binary
   - `0o`: Octal (base 8) Literal
     - `0~7`: Octal
 - `"`: Literal Text (Until `"`, unless it's `""` then include `"` in literal)
 - `'`: Loop Label
 - `:`: Assignment
 - `%`|`&`|`*`|`+`|`/`|`;`|`<`|`=`|`>`|`^`|`|`|`~`: Binary Operator
   - **Binary Operators**
   - `+`: Addition (`+`)
   - `++`: Concatenate / Join (`⨝`)
   - `+-`: Either Positive or Negative (`±`)
   - `*`: Multiplication (`×`)
   - `**`: Dot Product / Matrix Multiply (`·`)
   - `/`: Division (`÷`)
   - `//`: Integer division (rounding to zero `/`)
   - `%`: Modulo (`8 % 3 = 2`, `-8 % 3 = 1`, `8 % -3 = -1`, `-8 % -3 = -2`)
   - `%%`: Remainder (`8 % 3 = 2`, `-8 % 3 = -2`, `8 % -3 = -2`, `-8 % -3 = 2`)
   - `%=`: Is divisible by
   - `^`: Power (`4 ^ 2 = 4² = 16`)
   - `^^`: Bitwise XOR (`^`)
   - `&`: Pattern Matching And / Sets Intersection (`∩`)
   - `&&`: Bitwise AND (`&`)
   - `|`: Pattern Matching Or / Sets Union (`∪`)
   - `||`: Bitwise OR (`|`)
   - `=`: Equal (`=`)
   - `>`: Greater Than (`>`)
   - `>=`: Greater Than or Equal (`≥`)
   - `>>`: Right Shift (`»`)
   - `<`: Less Than (`<`)
   - `<=`: Less Than or Equal (`≤`)
   - `<<`: Left Shift (`«`)
   - `;`: Data Repetition (`"abc" ; 2 = "abcabc"`, `(4; 3, 5) = (4, 4, 4, 5)`)
   - `~`: Inclusive Range (For pattern matching and type bounds)
 - `!`|`@`|`?`|`-`: Possibly Unary Operator
   - `-`: Negation, Subtraction (`-`)
   - `!`: Pattern Matching Not, Bitwise Not (`¬`)
   - `!=`: Not equal (`≠`)
   - `@`: Mutable Reference
   - `?`: Try: Early return on error for outermost scope or named inner scope.
 - `#`: Comment Open (Until newline or `#`)
 - `$`: Auto-Derive Conversion Implementations
 - `(`|`)`: Tuple/Arguments/Order Open / Close
 - `[`|`]`: List/Map/Set/Generics Open / Close
 - `{`|`}`: Scope/Order Open / Close
 - `.`:
   - `.`: Access
   - `...`: Variable arguments
 - `\`: Equivalent to Rust's `r#`
 - ```: Multi-line comment
 - ` `: Token Separator
 - `,`|`\n`: List Separator

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
$Text
enum Try[E, T](
    Err(E): 0
    Ok(T): 1
)

$Text, $Try
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
