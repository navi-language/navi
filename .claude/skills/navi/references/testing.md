# Testing in Navi

Comprehensive guide to writing and running tests in Navi.

## Test Functions

### Basic Test

```nv
test "test name" {
    let result = add(2, 3);
    assert result == 5;
    assert_eq result, 5;
    assert_ne result, 0;
}
```

### Multiple Tests

```nv
fn add(a: int, b: int): int {
    return a + b;
}

test "addition" {
    assert_eq add(2, 3), 5;
}

test "addition with negatives" {
    assert_eq add(-1, 1), 0;
}

test "addition with zero" {
    assert_eq add(5, 0), 5;
}
```

## Assertions

### assert

```nv
test "boolean assertions" {
    assert true;
    assert 1 == 1;
    assert "hello" == "hello";
}
```

### assert_eq

```nv
test "equality assertions" {
    assert_eq 1 + 1, 2;
    assert_eq "hello", "hello";
    assert_eq [1, 2], [1, 2];

    // With custom message
    assert_eq result, expected, "Values should match";
}
```

### assert_ne

```nv
test "inequality assertions" {
    assert_ne 1, 2;
    assert_ne "hello", "world";
    assert_ne [1, 2], [3, 4];
}
```

## Doc Tests

### Writing Doc Tests

````nv
/// Adds two numbers together.
///
/// # Examples
///
/// ```nv
/// let result = add(2, 3);
/// assert_eq result, 5;
/// ```
fn add(a: int, b: int): int {
    return a + b;
}
````

### Running Doc Tests

```shell
navi test --doc        # Run only doc tests
navi test             # Run both regular and doc tests
```

## Test Annotations

### ignore - Skip Test

````nv
/// ```nv,ignore
/// // This test is skipped
/// let result = unimplemented_feature();
/// ```
````

### should_panic - Expect Panic

````nv
/// ```nv,should_panic
/// panic "Expected error";
/// ```
fn test_panic() {
    panic "Expected error";
}
````

### no_run - Compile Only

````nv
/// ```nv,no_run
/// loop {
///     // Infinite loop, don't run
/// }
/// ```
````

### compile_fail - Expect Compilation Failure

````nv
/// ```nv,compile_fail
/// let x = undefined_variable;
/// ```
````

## Testing Patterns

### Pattern 1: Setup and Teardown

```nv
test "with setup and teardown" {
    // Setup
    let temp_file = create_temp_file();

    defer {
        // Teardown
        cleanup_temp_file(temp_file);
    }

    // Test
    write_to_file(temp_file, "data");
    let content = read_from_file(temp_file);
    assert_eq content, "data";
}
```

### Pattern 2: Testing Error Conditions

```nv
fn divide(a: int, b: int): int throws {
    if (b == 0) {
        throw "Division by zero";
    }
    return a / b;
}

test "division by zero" {
    let result = try? divide(10, 0);
    assert_eq result, nil;
}

test "successful division" {
    let result = try? divide(10, 2);
    assert_eq result, 5;
}
```

### Pattern 3: Testing Optional Values

```nv
fn find_user(id: int): User? {
    // Implementation
}

test "user found" {
    let user = find_user(1);
    assert user != nil;
    assert_eq user!.name, "Alice";
}

test "user not found" {
    let user = find_user(999);
    assert_eq user, nil;
}
```

### Pattern 4: Testing Collections

```nv
test "array operations" {
    let items = [1, 2, 3];

    assert_eq items.len(), 3;
    assert_eq items[0], 1;

    items.push(4);
    assert_eq items.len(), 4;

    let last = items.pop();
    assert_eq last, 4;
}

test "map operations" {
    let config = {"host": "localhost", "port": "8080"};

    assert_eq config.len(), 2;
    assert_eq config["host"], "localhost";

    config["timeout"] = "30";
    assert_eq config.len(), 3;
}
```

### Pattern 5: Table-Driven Tests

```nv
test "addition table" {
    let cases = [
        {a: 1, b: 2, expected: 3},
        {a: 0, b: 0, expected: 0},
        {a: -1, b: 1, expected: 0},
        {a: 10, b: 5, expected: 15},
    ];

    for (let c in cases) {
        let result = add(c.a, c.b);
        assert_eq result, c.expected;
    }
}
```

### Pattern 6: Testing Concurrent Code

```nv
test "concurrent operations" {
    let ch = channel::<int>();

    spawn {
        try! ch.send(42);
    }

    let result = try! ch.recv();
    assert_eq result, 42;
}
```

## Track Caller

### Using #[track_caller]

```nv
#[track_caller]
fn assert_positive(value: int) {
    assert value > 0;
}

test "custom assertion" {
    assert_positive(5);   // OK
    assert_positive(-1);  // Error points to this line, not assert_positive
}
```

### Caller Location

```nv
use std.backtrace;

#[track_caller]
fn require(condition: bool, message: string) {
    if (!condition) {
        let caller = backtrace.caller_location()!;
        panic `${message} at ${caller.file}:${caller.line}`;
    }
}

test "requirement" {
    require(true, "Should pass");
    require(false, "Should fail");  // Error shows this line
}
```

## Running Tests

### Command Line

```shell
# Run all tests in current directory
navi test

# Run specific file
navi test file.nv

# Run specific module
navi test module/

# Run doc tests
navi test --doc

# Run with verbose output
navi test -v
```

### Test Output

```shell
$ navi test
Testing .
test main.nv .. ok in 1ms
All 2 tests 2 passed finished in 0.02s.
```

### Test Failures

```shell
$ navi test
Testing
test main.nv .. fail in 708ms

  main test_name
    thread 'thread 1#' at 'assertion failed: true == false', main.nv:2

    stack backtrace:
      0: test#0()
        at main.nv:2

All 2 tests 0 passed, 2 failed finished in 0.79s.
```

## Testing Best Practices

### DO: Test Public API

```nv
// Good: Test public interface
pub fn create_user(name: string): User {
    // ...
}

test "create_user" {
    let user = create_user("Alice");
    assert_eq user.name, "Alice";
}

// Don't: Test internal helpers unless necessary
fn internal_helper() {
    // ...
}
```

### DO: Use Descriptive Test Names

```nv
// Good
test "creates user with valid name" {
    // ...
}

test "returns error when name is empty" {
    // ...
}

// Bad
test "test1" {
    // ...
}

test "test_function" {
    // ...
}
```

### DO: Test Edge Cases

```nv
test "handles empty array" {
    let result = process([]);
    assert_eq result, [];
}

test "handles single element" {
    let result = process([1]);
    assert_eq result, [2];
}

test "handles large array" {
    let large = [0; 1000];
    let result = process(large);
    assert_eq result.len(), 1000;
}
```

### DO: Test Error Conditions

```nv
test "throws on invalid input" {
    let result = try? parse_data("invalid");
    assert_eq result, nil;
}

test "succeeds on valid input" {
    let result = try? parse_data("valid");
    assert result != nil;
}
```

### DON'T: Test Implementation Details

```nv
// Bad: Testing private internals
test "internal_helper returns formatted string" {
    // Don't test internal helpers
}

// Good: Test public behavior
test "format_output produces correct format" {
    let result = format_output(data);
    assert result.starts_with("[");
}
```

### DON'T: Have Flaky Tests

```nv
// Bad: Time-dependent test
test "operation completes in time" {
    let start = time.now();
    operation();
    let elapsed = time.now() - start;
    assert elapsed < 1.0;  // Flaky!
}

// Good: Test behavior, not timing
test "operation completes successfully" {
    let result = operation();
    assert result.is_ok();
}
```

## Test Organization

### Group Related Tests

```nv
// user.nv
pub struct User {
    name: string,
    age: int,
}

impl User {
    pub fn new(name: string, age: int): User {
        return User { name, age };
    }

    pub fn is_adult(self): bool {
        return self.age >= 18;
    }
}

// Tests for User
test "user creation" {
    let user = User.new("Alice", 30);
    assert_eq user.name, "Alice";
    assert_eq user.age, 30;
}

test "adult check" {
    let adult = User.new("Bob", 25);
    let minor = User.new("Charlie", 15);

    assert adult.is_adult();
    assert !minor.is_adult();
}
```

### Separate Test Files (Optional)

```
models/
├── user.nv       # Implementation
└── user_test.nv  # Tests (if you prefer separation)
```

## Coverage Considerations

While Navi doesn't have built-in coverage tools, ensure:

1. Test happy path (success cases)
2. Test error conditions
3. Test edge cases (empty, nil, boundaries)
4. Test different input types (for union types)
5. Test concurrent scenarios (if applicable)

## Continuous Integration

```shell
# In CI script
navi test            # Run all tests
navi test --doc      # Run doc tests

# Exit code
# 0 - All tests passed
# 1 - Some tests failed
```

## Example: Complete Test Suite

```nv
// calculator.nv
pub fn add(a: int, b: int): int {
    return a + b;
}

pub fn divide(a: int, b: int): int throws {
    if (b == 0) {
        throw "Division by zero";
    }
    return a / b;
}

// Tests
test "addition with positive numbers" {
    assert_eq add(2, 3), 5;
}

test "addition with negative numbers" {
    assert_eq add(-2, -3), -5;
}

test "addition with zero" {
    assert_eq add(5, 0), 5;
}

test "division success" {
    let result = try? divide(10, 2);
    assert_eq result, 5;
}

test "division by zero" {
    let result = try? divide(10, 0);
    assert_eq result, nil;
}

/// Doc test
/// ```nv
/// assert_eq add(1, 1), 2;
/// ```

/// Error case
/// ```nv,should_panic
/// let _ = try! divide(10, 0);
/// ```
```
