# Error Handling in Navi

Comprehensive guide to error handling in Navi, including throws, try variants, custom errors, and best practices.

## The `throws` Keyword

```nv
// Function that can throw an error
fn divide(a: int, b: int): int throws {
    if (b == 0) {
        throw "Division by zero";
    }
    return a / b;
}

// Call must use try, try?, or try!
fn main() throws {
    let result = try divide(10, 2);  // Propagate error
}
```

## Error Interface

```nv
// Built-in Error interface
pub interface Error {
    fn error(self): string;
}

// String implements Error, so you can throw strings
throw "Error message";
```

## Custom Error Types

```nv
struct ValidationError {
    field: string,
    message: string,
}

impl Error for ValidationError {
    pub fn error(self): string {
        return `Validation error on ${self.field}: ${self.message}`;
    }
}

// Typed throws
fn validate_age(age: int): bool throws ValidationError {
    if (age < 0) {
        throw ValidationError {
            field: "age",
            message: "Age cannot be negative",
        };
    }
    if (age > 150) {
        throw ValidationError {
            field: "age",
            message: "Age seems unrealistic",
        };
    }
    return true;
}
```

## Handling Errors

### try - Propagate Error

```nv
fn process_data(data: string): Result throws {
    let parsed = try parse_data(data);  // Error propagates up
    return process(parsed);
}
```

### try? - Convert to Optional

```nv
fn safe_divide(a: int, b: int): int? {
    return try? divide(a, b);  // Returns nil on error
}

// Usage
let result = safe_divide(10, 0);
if (let r = result) {
    println(`Result: ${r}`);
} else {
    println("Division failed");
}
```

### try! - Panic on Error

```nv
fn main() throws {
    // Only use when you're certain no error will occur
    let result = try! divide(10, 2);  // Panics if error
}
```

### do-catch Block

```nv
do {
    let result = try risky_operation();
    println(`Success: ${result}`);
} catch (e) {
    // Catch any error
    println(`Error: ${e.error()}`);
} catch (e: ValidationError) {
    // Catch specific error type
    println(`Validation failed: ${e.field}`);
} finally {
    // Always executes (optional)
    cleanup();
}
```

## Error Conversion

```nv
type MyError2 = MyError1;

fn throws_error1() throws MyError1 {
    throw MyError1 { message: "error" };
}

fn convert_error() throws MyError2 {
    // Automatically converts MyError1 to MyError2
    try throws_error1();
}
```

## Panic

```nv
fn assert_positive(value: int) {
    if (value <= 0) {
        panic "Value must be positive";
    }
}

// Panic stops execution immediately
// Use only for programmer errors, not expected failures
```

## Error Handling Patterns

### Pattern 1: Early Return with try?

```nv
fn process_user(id: int): User? {
    let data = try? fetch_user_data(id) else {
        return nil;
    };

    let user = try? parse_user(data) else {
        return nil;
    };

    return user;
}
```

### Pattern 2: Graceful Degradation

```nv
fn load_config(): Config {
    let config = try? load_from_file("config.nv");
    return config || default_config();
}
```

### Pattern 3: Error Context

```nv
struct ContextError {
    context: string,
    inner: Error,
}

impl Error for ContextError {
    pub fn error(self): string {
        return `${self.context}: ${self.inner.error()}`;
    }
}

fn process_file(path: string) throws ContextError {
    do {
        try read_file(path);
    } catch (e) {
        throw ContextError {
            context: `Failed to process ${path}`,
            inner: e,
        };
    }
}
```

### Pattern 4: Result-like Pattern

```nv
struct Result<T> {
    value: T?,
    error: string?,
}

impl Result {
    fn ok(value: T): Result<T> {
        return Result { value, error: nil };
    }

    fn err(message: string): Result<T> {
        return Result { value: nil, error: message };
    }

    fn is_ok(self): bool {
        return self.error == nil;
    }

    fn is_err(self): bool {
        return self.error != nil;
    }

    fn unwrap(self): T {
        if (let v = self.value) {
            return v;
        }
        panic `Result was an error: ${self.error!}`;
    }
}

fn safe_operation(x: int): Result<int> {
    if (x < 0) {
        return Result.err("Negative value");
    }
    return Result.ok(x * 2);
}
```

### Pattern 5: Retry Logic

```nv
fn retry_operation(max_attempts: int): Result throws {
    let attempts = 0;

    while (attempts < max_attempts) {
        let result = try? perform_operation();
        if (let r = result) {
            return r;
        }

        attempts += 1;
        if (attempts < max_attempts) {
            println(`Retry ${attempts}/${max_attempts}`);
        }
    }

    throw "Max retries exceeded";
}
```

## Best Practices

### When to Use Each Handler

```nv
// Use try when error should propagate (most common)
fn process() throws {
    let data = try fetch_data();
    return data;
}

// Use try? when failure is acceptable
fn optional_feature(): string? {
    return try? experimental_api();
}

// Use try! only when error is impossible
fn init() {
    let config = try! load_builtin_config();  // Can't fail
}

// Use do-catch for custom error handling
fn robust_process() {
    do {
        try risky_operation();
    } catch (e: NetworkError) {
        retry_with_backoff();
    } catch (e) {
        log_error(e);
    }
}
```

### Error Type Design

```nv
// Good: Specific error types with context
struct ParseError {
    line: int,
    column: int,
    message: string,
}

struct IoError {
    path: string,
    operation: string,
    reason: string,
}

// Bad: Generic errors
struct Error {
    message: string,  // Too generic
}
```

### Error Messages

```nv
// Good: Descriptive with context
throw ValidationError {
    field: "email",
    message: "Invalid format: must contain @",
};

// Bad: Vague
throw "Invalid input";
```

### Don't Swallow Errors

```nv
// Bad: Silent failure
let _ = try? important_operation();

// Good: Handle or propagate
let result = try? important_operation();
if (result == nil) {
    log_error("Operation failed");
    return default_value;
}
```

### Prefer throws Over Optional

```nv
// Good: Use throws for failures
fn load_config(path: string): Config throws {
    if (!file_exists(path)) {
        throw `Config not found: ${path}`;
    }
    return try parse_config(path);
}

// Bad: Optional doesn't explain what went wrong
fn load_config(path: string): Config? {
    // Caller doesn't know why it failed
    return try? parse_config(path);
}
```

### Track Caller for Better Errors

```nv
#[track_caller]
fn require(condition: bool, message: string) {
    if (!condition) {
        panic message;
    }
}

// Error shows caller location, not require() location
require(age > 0, "Age must be positive");
```

## Testing Error Handling

```nv
test "error handling" {
    // Test error is thrown
    let result = try? divide(10, 0);
    assert_eq result, nil;

    // Test success case
    let result = try? divide(10, 2);
    assert_eq result, 5;
}

/// Doc test with should_panic
/// ```nv,should_panic
/// panic "Expected error";
/// ```
fn test_panic() {
    panic "Expected error";
}
```

## Common Error Handling Anti-Patterns

### Anti-Pattern 1: Overusing try!

```nv
// Bad: Crashes program on any error
let data = try! fetch_data();
let parsed = try! parse_data(data);
let result = try! process_data(parsed);

// Good: Handle errors gracefully
do {
    let data = try fetch_data();
    let parsed = try parse_data(data);
    let result = try process_data(parsed);
} catch (e) {
    log_error(e);
    return default_result;
}
```

### Anti-Pattern 2: Catching and Re-throwing

```nv
// Bad: Useless catch
do {
    try operation();
} catch (e) {
    throw e;  // Just propagating
}

// Good: Use try directly
try operation();
```

### Anti-Pattern 3: Too Broad Catching

```nv
// Bad: Catches everything
do {
    try operation1();
    try operation2();
    try operation3();
} catch (e) {
    // Which operation failed?
}

// Good: Handle each separately
try operation1();
try operation2();
try operation3();
```
