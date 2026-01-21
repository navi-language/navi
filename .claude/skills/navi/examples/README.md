# Navi Code Examples

This directory contains practical, runnable examples demonstrating various Navi language features and patterns.

## Running Examples

```bash
# Run a specific example
navi run examples/01-basic-syntax.nv

# Run tests in an example
navi test examples/02-structs.nv

# Run all tests
navi test examples/
```

## Example Files

### 01-basic-syntax.nv
**Fundamental language features**

Covers:
- Variables and constants
- Primitive types (int, float, bool, string, char)
- String operations and interpolation
- Control flow (if, switch, for, while)
- Functions and closures

**When to use**: Learning the basics or refreshing syntax knowledge.

```bash
navi run examples/01-basic-syntax.nv
navi test examples/01-basic-syntax.nv
```

### 02-structs.nv
**Struct definition and methods**

Covers:
- Struct declaration with optional fields and defaults
- Static and instance methods
- Nested structs
- Destructuring assignment
- ToString implementation

**When to use**: Learning how to define and work with custom types.

```bash
navi run examples/02-structs.nv
navi test examples/02-structs.nv
```

### 03-error-handling.nv
**Error handling patterns**

Covers:
- Custom error types
- `throws`, `try`, `try?`, `try!`
- `do-catch` blocks
- Error propagation
- Multiple error types
- Early return patterns

**When to use**: Learning robust error handling strategies.

```bash
navi run examples/03-error-handling.nv
navi test examples/03-error-handling.nv
```

### 04-optionals.nv
**Optional type handling**

Covers:
- Optional creation and checking
- Unwrapping patterns (`||`, if-let, let-else)
- Optional chaining (`?.`)
- Optional methods (map, unwrap_or, etc.)
- Practical optional handling patterns

**When to use**: Learning safe nil handling without null pointer exceptions.

```bash
navi run examples/04-optionals.nv
navi test examples/04-optionals.nv
```

### 05-collections.nv
**Arrays and maps**

Covers:
- Array operations (push, pop, shift, unshift)
- Map operations (keys, values, iteration)
- Iteration patterns (arrays, maps, ranges)
- Collection transformations (filter, map, reduce)
- Nested collections

**When to use**: Working with collections and data structures.

```bash
navi run examples/05-collections.nv
navi test examples/05-collections.nv
```

### 06-patterns.nv
**Design patterns**

Covers:
- Builder pattern (fluent API)
- Factory pattern (object creation)
- Strategy pattern (algorithm selection)
- Iterator pattern (custom iteration)
- Observer pattern (event notification)
- Singleton pattern (global instance)

**When to use**: Learning idiomatic Navi design patterns.

```bash
navi run examples/06-patterns.nv
navi test examples/06-patterns.nv
```

### 07-concurrency.nv
**Concurrent programming**

Covers:
- `spawn` for concurrent tasks
- Channels for communication
- Worker pool pattern
- Pipeline pattern
- Fan-out/fan-in
- Timeout pattern
- Synchronization
- Error handling in concurrent code

**When to use**: Building concurrent (not parallel) applications.

**Important**: Navi uses concurrent, not parallel execution (single-threaded).

```bash
navi run examples/07-concurrency.nv
navi test examples/07-concurrency.nv
```

### 08-testing.nv
**Testing patterns**

Covers:
- Basic test functions
- Assertions (assert, assert_eq, assert_ne)
- Testing structs and methods
- Testing error handling
- Testing optionals
- Testing collections
- Table-driven tests
- Setup/teardown patterns
- Edge case testing
- Doc tests

**When to use**: Learning how to write effective tests.

```bash
navi test examples/08-testing.nv
navi test --doc examples/08-testing.nv  # Doc tests
```

## Learning Path

Recommended order for learning:

1. **01-basic-syntax.nv** - Start here for fundamentals
2. **02-structs.nv** - Learn custom types
3. **04-optionals.nv** - Master safe nil handling
4. **03-error-handling.nv** - Learn robust error handling
5. **05-collections.nv** - Work with data structures
6. **08-testing.nv** - Write tests for your code
7. **06-patterns.nv** - Apply design patterns
8. **07-concurrency.nv** - Build concurrent applications

## Quick Reference

### Run Example
```bash
navi run examples/01-basic-syntax.nv
```

### Run Tests
```bash
navi test examples/01-basic-syntax.nv
```

### Run All Tests
```bash
navi test examples/
```

### Run Doc Tests
```bash
navi test --doc examples/
```

## Example Structure

Each example file typically includes:

1. **Comments** explaining concepts
2. **Code examples** demonstrating features
3. **Main function** for running as a program
4. **Test functions** for verification

## Contributing

When adding new examples:

1. **Keep focused** - One topic per file
2. **Add comments** - Explain what and why
3. **Include tests** - Demonstrate correctness
4. **Follow naming** - Use ##-topic.nv format
5. **Update README** - Document the new example

## Tips

- Start with `main()` function to see usage
- Read comments for explanations
- Run tests to verify understanding
- Modify examples and experiment
- Use as templates for your own code

## See Also

- **SKILL.md** - Quick syntax reference
- **references/** - Detailed documentation
- **README.md** - Skill overview

## Running Tips

### Watch for Changes (if available)
```bash
# Run on file change (if Navi supports it)
navi run --watch examples/01-basic-syntax.nv
```

### Run with Verbose Output
```bash
navi test -v examples/
```

### Run Specific Test
```bash
navi test examples/02-structs.nv -t "user creation"
```

## Common Patterns

These examples demonstrate common Navi patterns:

- **Builder**: Fluent API for object construction
- **Early Return**: Exit early with optionals and errors
- **Defer**: Resource cleanup
- **Channels**: Concurrent communication
- **Optional Chaining**: Safe navigation through nil values
- **Error Context**: Adding context to errors
- **Table-Driven Tests**: Testing multiple cases

Happy coding with Navi! 🚀
