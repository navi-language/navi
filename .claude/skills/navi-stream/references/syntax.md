# Navi Stream Complete Syntax Reference

Detailed syntax specification for the Navi Stream (.nvs) language.

## File Structure

NVS files typically contain these sections (in order):

```nvs
// 1. Metadata declaration (optional)
meta { }

// 2. Module imports
use quote, ta;

// 3. Internationalization labels (optional)
@label_name { }

// 4. Parameter definition (optional)
param { }

// 5. Function definitions (optional)
fn function_name() { }

// 6. Variables and calculations
let variable = expression;

// 7. Export variables
export let output = value;

// 8. Conditions and plotting
if (condition) { }
```

## Metadata Declaration (Meta)

### Syntax

```nvs
meta {
    key = value,
    // More key-value pairs...
}
```

### Available Fields

```nvs
meta {
    title = "Indicator Name",     // String: indicator display name
    overlay = false,               // Boolean: overlay on price chart or separate window
    hideparams = true,             // Boolean: hide parameter panel
}
```

**Field Descriptions:**
- `title`: Display name of the indicator
- `overlay`:
  - `true` - Overlay on price chart (for trend lines, channels, etc.)
  - `false` - Display in separate window (for oscillators, MACD, etc.)
- `hideparams`: Whether to hide the parameter configuration panel

## Module Imports (Use)

### Syntax

```nvs
use module1;
use module1, module2;
use module1, module2, module3;
```

### Available Modules

**quote** - Market data access
```nvs
use quote;

// Access price data
let price = close;
let high_price = high;
```

**ta** - Technical analysis functions
```nvs
use ta;

// Use technical indicator functions
let ma = ta.ma(close, 20);
let ema_val = ema(close, 12);
```

**math** - Mathematical functions
```nvs
use math;

// Math operations
let abs_val = abs(diff);
let max_val = max(a, b);
let min_val = min(a, b);
```

## Comments

```nvs
// Single-line comment
let value = 100;  // End-of-line comment

// Multi-line descriptions:
// Use multiple single-line comments
// to represent multi-line content
```

**Note:** Navi Stream does not support multi-line comments `/* */`

## Variable Declaration

### Let (Immutable)

```nvs
// Type inference
let price = close;
let ma = ema(close, 20);

// Explicit type
let value: number = 100.0;
let flag: boolean = true;

// From expression
let diff = fast_ma - slow_ma;
```

### Var (Mutable)

```nvs
// Mutable variable
var counter = 0;
var sum: number = 0.0;

// Modify value
counter = counter + 1;
sum += price;
```

### Export

```nvs
// Export variable as output
export let signal = buy_signal;
export let value = calculated_value;
export let rsi = rsi_value;

// Exported variables can be accessed in Navi
// indicator.signal
// indicator.value
// indicator.rsi
```

## Data Types

### Number

```nvs
let price: number = 100.5;
let volume: number = 1000000;
let ratio = 0.618;

// Arithmetic operations
let sum = a + b;
let diff = a - b;
let product = a * b;
let quotient = a / b;
let remainder = a % b;

// Negative numbers
let neg = -100;
```

### Boolean

```nvs
let flag: boolean = true;
let is_up = close > open;
let crossed = fast_ma > slow_ma && fast_ma[1] <= slow_ma[1];

// Logical operations
let and_result = a && b;
let or_result = a || b;
let not_result = !flag;
```

### String

```nvs
// Double-quoted strings
let msg = "Hello";
let label = "Buy Signal";

// String interpolation (backticks)
let message = `Price: ${close}`;
let info = `MA=${ma} Signal=${signal}`;
```

### Nil

```nvs
let optional: number = nil;

// Check for nil
if (!isnil(optional)) {
    // Value exists
}
```

### Color

```nvs
// Hexadecimal colors
let red = #ff0000;
let green = #00ff00;
let blue = #0000ff;
let custom = #ddff53;

// Used in plotting
plot(value, color: #ff0000);
```

### Array

```nvs
// Create array
var values = array.new::<number>();

// Array operations
values.unshift(item);  // Insert at beginning
let first = values.get(0);  // Get element
let length = values.len();  // Length

// Iterate
for (let i in 0..values.len()) {
    let val = values.get(i);
}
```

## Parameter Definition (Param)

### Basic Syntax

```nvs
param {
    ParameterName = default_value,
    AnotherParam = default_value,
}
```

### Simple Parameters

```nvs
param {
    Period = 14,
    Length = 20,
    Multiplier = 2.0,
}

// Use in code
let ma = ema(close, Period);
```

### Parameters with Metadata

```nvs
param {
    @meta(title = "Period", range = 1..100)
    Period = 14,

    @meta(title = "Upper Multiplier", range = 0.1..5.0)
    Upper = 2.0,
}
```

**Metadata fields:**
- `title`: Parameter display name (string or i18n label)
- `range`: Parameter value range

### Using Internationalization Labels

```nvs
// Define label
@title_period {
    "en" = "Period",
    "zh-CN" = "周期",
    "zh-HK" = "週期",
}

// Use in parameter
param {
    @meta(title = @title_period, range = 1..100)
    Period = 14,
}
```

## Internationalization Labels

### Syntax

```nvs
@label_name {
    "language_code" = "translated text",
    // More languages...
}
```

### Supported Language Codes

```nvs
@my_label {
    "en" = "English Text",
    "zh-CN" = "Simplified Chinese",
    "zh-HK" = "Traditional Chinese",
}
```

### Using Labels

```nvs
// In parameter metadata
param {
    @meta(title = @my_label)
    Period = 14,
}

// As variable
let label_text = @my_label;
```

## Function Definition

### Basic Syntax

```nvs
fn function_name(param1: type1, param2: type2): return_type {
    // Function body
    return result;
}
```

### Examples

```nvs
// Simple function
fn average(a: number, b: number): number {
    return (a + b) / 2;
}

// Use function
let avg = average(10, 20);
```

### Stateful Functions

```nvs
fn calculate_lowest(x: number, n: number): number {
    var values = array.new::<number>();

    // Use barstate check
    if (barstate.is_confirmed) {
        values.unshift(x);
    }

    // Find minimum
    let result: number = values.get(0);
    for (let i in 1..min(n, values.len())) {
        result = min(result, values.get(i));
    }

    return result;
}
```

## Market Data Access

### Current Data

```nvs
use quote;

// Price data
let current_close = close;
let current_open = open;
let current_high = high;
let current_low = low;

// Volume data
let vol = volume;
let amt = turnover;

// Time
let timestamp = time;
```

### Historical Data (Time Series Access)

```nvs
// Access historical data
let prev_close = close[1];     // Previous period
let prev2_close = close[2];    // 2 periods ago
let prev_n = close[n];         // N periods ago

// Used for comparison
if (close > close[1]) {
    // Current close is higher than previous period
}

// Multi-period analysis
if (close > high[20]) {
    // Current price breaks above high from 20 periods ago
}
```

### Available Fields

| Field | Type | Description |
|-------|------|-------------|
| `close` | number | Close price |
| `open` | number | Open price |
| `high` | number | High price |
| `low` | number | Low price |
| `volume` | number | Volume |
| `turnover` | number | Turnover |
| `time` | number | Timestamp |

## Control Flow

### If Statements

```nvs
// Basic if
if (condition) {
    // ...
}

// if-else
if (condition) {
    // ...
} else {
    // ...
}

// if-else if-else
if (condition1) {
    // ...
} else if (condition2) {
    // ...
} else {
    // ...
}
```

### For Loops

```nvs
// Range loop
for (let i in 0..10) {
    // i from 0 to 9
}

for (let i in 1..n) {
    // i from 1 to n-1
}

// Common use with arrays
for (let i in 0..values.len()) {
    let val = values.get(i);
}
```

## Operators

### Arithmetic Operators

```nvs
a + b    // Addition
a - b    // Subtraction
a * b    // Multiplication
a / b    // Division
a % b    // Modulo
-a       // Negation
```

### Comparison Operators

```nvs
a == b   // Equal
a != b   // Not equal
a > b    // Greater than
a >= b   // Greater than or equal
a < b    // Less than
a <= b   // Less than or equal
```

### Logical Operators

```nvs
a && b   // Logical AND
a || b   // Logical OR
!a       // Logical NOT
```

### Compound Assignment

```nvs
a += b   // a = a + b
a -= b   // a = a - b
a *= b   // a = a * b
a /= b   // a = a / b
```

## Special Constructs

### Barstate

```nvs
// Check bar state
if (barstate.is_confirmed) {
    // Current bar is confirmed
    values.unshift(close);
}
```

### IsNil Check

```nvs
let value: number = nil;

if (!isnil(value)) {
    // Value is not nil
    process(value);
}

if (isnil(value)) {
    // Value is nil
}
```

## Best Practices

### Variable Naming

```nvs
// Good: Descriptive names
let fast_ma = ema(close, 12);
let slow_ma = ema(close, 26);
let buy_signal = fast_ma > slow_ma;

// Bad: Unclear names
let a = ema(close, 12);
let b = ema(close, 26);
let c = a > b;
```

### Avoid Repeated Calculations

```nvs
// Good: Store result
let ma20 = ema(close, 20);
let signal1 = close > ma20;
let signal2 = ma20 > ma20[1];

// Bad: Repeated calculations
let signal1 = close > ema(close, 20);
let signal2 = ema(close, 20) > ema(close, 20)[1];
```

### Use Intermediate Variables

```nvs
// Good: Break down complex expressions
let price_above_ma = close > ma20;
let ma_trending_up = ma20 > ma20[1];
let strong_signal = price_above_ma && ma_trending_up;

// Bad: Everything in one line
let signal = close > ma20 && ma20 > ma20[1];
```

### Parameter Ranges

```nvs
// Good: Set reasonable ranges
param {
    @meta(range = 5..250)
    Period = 20,  // Limited to 5-250
}

// Bad: No limits
param {
    Period = 20,  // Could be set to unreasonable values
}
```

## Limitations and Notes

1. **Streaming Processing**: Code processes data sequentially by time
2. **Historical Data Only**: Can only access past data, not future data
3. **State Persistence**: Use `var` and arrays to maintain state across executions
4. **No Concurrency**: NVS is single-threaded, sequential execution
5. **Type Constraints**: Primarily uses number type for numerical calculations
