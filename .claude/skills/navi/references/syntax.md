# Navi Syntax Reference

Complete syntax reference for the Navi programming language.

## Comments

```nv
// Single-line comment

/// Doc comment (supports Markdown)
/// Used for documentation generation
fn documented_function() {
    // Implementation
}
```

No multi-line comments - use `//` for each line.

## Variables

### Declaration

```nv
let name = "World";              // Mutable, type inferred
let age: int = 30;               // Explicit type
const MAX_SIZE = 100;            // Immutable constant
```

### Identifiers

- Start with letter or underscore `_`
- Followed by letters, underscores, or digits
- Convention: `snake_case` for variables/functions, `CamelCase` for types
- Reserved keywords cannot be used

### Scope

```nv
const global = "Global scope";   // Module-level scope

fn main() throws {
    let local = "Function scope"; // Function scope

    {
        let block = "Block scope"; // Block scope
    }
    // `block` not accessible here
}
```

## Primitive Types

### Integer (int)

```nv
let n = 246;                // Decimal
let neg = -100;             // Negative
let large = 1_000_000;      // With separators for readability

// All integers are 64-bit signed (i64)
// Range: -9223372036854775808 to 9223372036854775807
```

### Float (float)

```nv
let pi = 3.14;              // Decimal
let sci = 10.23e+10;        // Scientific notation
let neg = -2.0;             // Negative

// All floats are 64-bit (f64) with 53 bits precision
```

### Boolean (bool)

```nv
let passed = true;
let failed = false;
```

### String (string)

```nv
// Double quotes
let msg = "Hello, 世界";

// String interpolation with backticks
let name = "Alice";
let greeting = `Hello ${name}!`;

// Multi-line interpolation
let multiline = `
    Line 1
    Line 2: ${name}
`;

// Escape sequences
let escaped = "Quote: \"text\"\nNew line\tTab";

// Strings are immutable
```

**Escape Sequences:**
- `\n` - Newline
- `\r` - Carriage return
- `\t` - Tab
- `\\` - Backslash
- `\"` - Double quote
- `\'` - Single quote

### Character (char)

```nv
let c = 'a';
let emoji = '🎉';
let from_string = "abc"[0];  // Extract char from string
```

### Bytes

```nv
let byte = b'a';             // Single byte as int (97)
let bytes = b"Hello";        // Bytes type
```

## Operators

### Arithmetic

```nv
1 + 2    // Addition
1 - 2    // Subtraction
1 * 2    // Multiplication
1 / 2    // Division (integer division for ints)
1 % 2    // Modulo
-1       // Negation
```

### Assignment

```nv
a += 1   // Add and assign
a -= 1   // Subtract and assign
a *= 2   // Multiply and assign
a /= 2   // Divide and assign
a %= 3   // Modulo and assign
```

### Comparison

```nv
a == b   // Equal
a != b   // Not equal
a < b    // Less than
a <= b   // Less than or equal
a > b    // Greater than
a >= b   // Greater than or equal
```

### Logical

```nv
a && b   // Logical AND
a || b   // Logical OR (also used for optional unwrap)
!a       // Logical NOT (also used for force unwrap)
```

### Optional

```nv
a?.field   // Optional chaining
a || b     // Unwrap or default
a!         // Force unwrap (panics if nil)
```

## Collections

### Arrays

```nv
// Declaration with type inference
let numbers = [1, 2, 3];

// Explicit type
let names: [string] = [];

// Initialize with same value
let zeros: [int] = [0; 10];  // [0, 0, 0, ...]

// Access
let first = numbers[0];
numbers[1] = 5;

// Methods
numbers.len()           // Length
numbers.push(4)         // Add to end
numbers.pop()           // Remove from end (returns T?)
numbers.shift()         // Remove from start (returns T?)
numbers.unshift(0)      // Add to start

// Nested arrays
let matrix: [[int]] = [[1, 2], [3, 4]];
```

### Maps

```nv
// Declaration with type inference
let config = {"name": "Navi", "version": "0.1"};

// Explicit type
let empty: <string, int> = {:};

// Type annotation: <KeyType, ValueType>
let scores: <string, int> = {"Alice": 100};

// Access
let name = config["name"];
config["name"] = "Updated";

// Methods
config.len()            // Number of entries
config.keys()           // Array of keys
config.values()         // Array of values
```

## Control Flow

### If Statements

```nv
if (condition) {
    // ...
} else if (other_condition) {
    // ...
} else {
    // ...
}

// If-let for optionals
if (let value = optional) {
    // value is unwrapped here
}

// If-let for type assertion
if (let user = value.(User)) {
    // value is User type here
}
```

### Let-Else

```nv
let value = optional else {
    // Handle nil case
    return;
};
// value is unwrapped here
```

### Switch

```nv
switch (value) {
    case 1:
        println("One");
    case 2, 3:
        println("Two or Three");
    default:
        println("Other");
}

// Type switch
switch (let v = value.(type)) {
    case int:
        // v is int here
    case string:
        // v is string here
    default:
        // v is original type
}
```

### While Loop

```nv
while (condition) {
    // ...
}

// While-let for optionals
while (let item = iterator.next()) {
    // Process item
}

// Break and continue
while (true) {
    if (should_exit) {
        break;
    }
    if (should_skip) {
        continue;
    }
}
```

### For Loop

```nv
// Range
for (let i in 0..10) {
    // i from 0 to 9
}

// Range with step
for (let i in (0..10).step(2)) {
    // 0, 2, 4, 6, 8
}

// Array
for (let item in array) {
    // ...
}

// Map
for (let key, value in map) {
    // ...
}

// Custom iterator (needs iter() method)
for (let item in custom_iterable) {
    // ...
}
```

## Functions

### Basic Function

```nv
fn function_name(param1: Type1, param2: Type2): ReturnType {
    return value;
}

// No return type
fn print_message(msg: string) {
    println(msg);
}

// With throws
fn risky_operation(): ResultType throws {
    if (error_condition) {
        throw "Error message";
    }
    return result;
}
```

### Parameters

```nv
// Positional parameters
fn add(a: int, b: int): int {
    return a + b;
}

// Optional parameters
fn greet(name: string, title: string?): string {
    let t = title || "Mr.";
    return `${t} ${name}`;
}

// Arbitrary arguments (variadic)
fn sum(first: int, rest: ..int): int {
    let total = first;
    for (let n in rest) {
        total += n;
    }
    return total;
}

sum(1, 2, 3, 4);     // Call with multiple args
let nums = [2, 3, 4];
sum(1, ..nums);      // Spread operator

// Keyword arguments
fn create_user(name: string, age: int, active: bool = true): User {
    return User { name, age, active };
}

create_user("Alice", 30);
create_user("Bob", 25, active: false);
```

### Closures

```nv
// Closure type: |(param_types): return_type|
let add: |(int, int): int| = |a, b| {
    return a + b;
};

// No parameters
let get_value: |(): int| = || {
    return 42;
};

// No return
let print: |()| = || {
    println("Hello");
};

// Can throw
let risky: |(int): throws int| = |x| {
    if (x < 0) {
        throw "Negative";
    }
    return x * 2;
};
```

## Type Casting and Conversion

```nv
// Type casting (zero-cost)
let n = 100 as float;    // int to float
let i = 3.14 as int;     // float to int
let b = true as int;     // bool to int (1 or 0)

// Type conversion (may fail)
let n = "100".parse_int();      // Returns int?
let f = "3.14".parse_float();   // Returns float?
let s = 100.to_string();        // Always succeeds
```

## Destructuring

```nv
struct Point {
    x: int,
    y: int,
}

// Simple destructuring
let point = Point { x: 1, y: 2 };
let Point { x, y } = point;

// Nested destructuring
struct Line {
    start: Point,
    end: Point,
}

let line = Line {
    start: Point { x: 1, y: 2 },
    end: Point { x: 3, y: 4 }
};

let Line {
    start: Point { x: x1, y: y1 },
    end: Point { x: x2, y: y2 }
} = line;
```

## Type Annotations

### Struct Attributes

```nv
// Rename all fields
#[serde(rename_all = "camelCase")]
struct User {
    user_name: string,  // Becomes "userName" in JSON
}

// Deny unknown fields
#[serde(deny_unknown_fields)]
struct Config {
    host: string,
}
```

### Field Attributes

```nv
struct User {
    #[serde(rename = "name")]
    user_name: string,

    #[serde(alias = "mail")]
    email: string,

    #[serde(skip)]
    internal: string = "hidden",

    #[serde(flatten)]
    extra: <string, Any>,
}
```

### Enum Attributes

```nv
// Serialize as integer
#[serde(int)]
enum Status {
    Active,   // 0
    Inactive, // 1
}

// Rename variants
#[serde(rename_all = "UPPERCASE")]
enum Role {
    Admin,    // "ADMIN"
    User,     // "USER"
}
```

### Function Attributes

```nv
// Track caller (for better error messages)
#[track_caller]
fn assert_positive(value: int) {
    assert value > 0;
}
```

## Defer

```nv
fn process() throws {
    let resource = acquire_resource();

    defer {
        release_resource(resource);
    }

    // Work with resource
    // defer executes when function returns (LIFO)
}
```

## Spawn and Channels

```nv
// Create channel
let ch = channel::<int>();

// Spawn concurrent task
spawn {
    try! ch.send(42);
}

// Receive from channel
let value = try ch.recv();
```

## Literals

```nv
// Nil
let none: string? = nil;

// Boolean
true
false

// Integer with bases
let dec = 123;
let hex = 0xFF;     // Not yet supported
let bin = 0b1010;   // Not yet supported

// Character escapes
'\n'  // Newline
'\t'  // Tab
'\''  // Single quote
```

## Keywords

Reserved keywords that cannot be used as identifiers:

```
as          assert      assert_eq   assert_ne
bench       benches     break       case
catch       const       continue    default
defer       do          else        enum
false       finally     fn          for
if          impl        in          interface
let         loop        nil         panic
pub         return      select      self
spawn       struct      switch      test
tests       throw       throws      true
try         type        use         while
```

## Visibility

```nv
// Public items (accessible from other modules)
pub struct User { /* ... */ }
pub fn create_user() { /* ... */ }
pub const MAX: int = 100;

// Private items (module-only)
struct Internal { /* ... */ }
fn helper() { /* ... */ }
```

## Module Imports

```nv
use std.io;                  // Import module
use std.url.Url;             // Import type
use std.{io, json};          // Import multiple
use std.http as client;      // Import with alias
use std.net.http.NotFound;   // Import constant
```
