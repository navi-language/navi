# Navi Language Skill

Expert knowledge and best practices for the Navi programming language.

## Overview

Navi (/ˈnævi/) is a high-performance, statically-typed compiled language designed for complex computing tasks. It combines the convenience of script-like execution with performance comparable to Go, Rust, and C.

**Key Features:**
- **No NULL pointer exceptions** - Once compiled, code runs reliably
- **Modern optional types** - Similar to Rust's Option
- **Comprehensive error handling** - throws, try, try?, try!
- **Built-in concurrency** - spawn and channels (single-threaded)
- **Cross-platform** - Linux, Windows, macOS, WebAssembly

## Skill Structure

```
navi/
├── SKILL.md              # Core skill (loaded when triggered)
├── references/           # Detailed documentation (loaded as needed)
│   ├── syntax.md        # Complete syntax reference
│   ├── types.md         # Type system: structs, enums, interfaces, optionals
│   ├── error-handling.md # Error handling patterns and best practices
│   ├── concurrency.md   # spawn, channels, and concurrent patterns
│   ├── modules.md       # Module system and imports
│   ├── testing.md       # Testing framework and patterns
│   └── patterns.md      # Common idioms and anti-patterns
├── examples/            # Runnable code examples (8 files)
│   ├── 01-basic-syntax.nv   # Variables, types, control flow, functions
│   ├── 02-structs.nv        # Struct definition and methods
│   ├── 03-error-handling.nv # Error patterns (try, try?, throws)
│   ├── 04-optionals.nv      # Optional type handling
│   ├── 05-collections.nv    # Arrays and maps
│   ├── 06-patterns.nv       # Design patterns (Builder, Factory, etc.)
│   ├── 07-concurrency.nv    # spawn and channels
│   ├── 08-testing.nv        # Testing patterns
│   └── README.md            # Examples guide
└── README.md            # This file
```

## How This Skill Works

### Automatic Activation

This skill automatically activates when you're working with Navi code or when you mention:
- Writing Navi code
- Navi language features
- `.nv` files
- Navi-related errors or debugging

### Main SKILL.md

The main `SKILL.md` provides:
- Quick syntax reference
- Essential patterns
- Common operations
- When to load detailed references

### Reference Files

Load reference files when you need detailed information:

```
Read ~/.claude/skills/navi/references/syntax.md        # Full syntax
Read ~/.claude/skills/navi/references/types.md         # Type system
Read ~/.claude/skills/navi/references/error-handling.md # Errors
Read ~/.claude/skills/navi/references/concurrency.md   # Concurrency
Read ~/.claude/skills/navi/references/modules.md       # Modules
Read ~/.claude/skills/navi/references/testing.md       # Testing
Read ~/.claude/skills/navi/references/patterns.md      # Patterns
```

## Quick Reference

### Installation

```bash
curl -sSL https://navi-lang.org/install | sh
```

### CLI Commands

```bash
navi run              # Run main.nv
navi run file.nv      # Run specific file
navi test             # Run all tests
navi test --doc       # Run doc tests
navi build            # Build project
navi compile          # Show bytecode
```

### Hello World

```nv
fn main() throws {
    let name = "World";
    println(`Hello ${name}!`);
}
```

### Key Concepts

**No NULL Pointer Exceptions:**
```nv
let value: string? = nil;
let result = value || "default";  // Safe
// let result = value!;            // Only if certain not nil
```

**Error Handling:**
```nv
fn divide(a: int, b: int): int throws {
    if (b == 0) {
        throw "Division by zero";
    }
    return a / b;
}

let result = try? divide(10, 0);  // Returns nil on error
```

**Concurrency (Not Parallelism):**
```nv
let ch = channel::<int>();

spawn {
    try! ch.send(42);
}

let value = try ch.recv();
```

## What Makes Navi Different

### vs Go
- Similar syntax and concurrency model
- Different: spawn is concurrent not parallel (single-threaded)
- Different: More advanced optional type system
- Different: No goroutines (async runtime instead)

### vs Rust
- Similar optional types (Option) and error handling
- Different: Simpler ownership (implicit references)
- Different: No lifetimes
- Different: Easier learning curve

### vs TypeScript
- Similar optional chaining (`?.`)
- Different: Compiled to bytecode/machine code
- Different: True optional types (not undefined)
- Different: No prototype-based inheritance

## Learning Path

1. **Start with basics** - Read `SKILL.md` for quick reference
2. **Learn syntax** - Review `references/syntax.md`
3. **Understand types** - Study `references/types.md` (especially optionals)
4. **Master errors** - Read `references/error-handling.md`
5. **Explore patterns** - Check `references/patterns.md`
6. **Try concurrency** - Read `references/concurrency.md`
7. **Write tests** - Follow `references/testing.md`
8. **Organize code** - Learn from `references/modules.md`

## Common Use Cases

### Script-like Execution
- Quick data processing
- Automation scripts
- Prototyping

### High-Performance Computing
- Financial calculations
- Real-time data processing
- Stream computing
- Market monitoring

### Systems Programming
- CLI tools
- Services and daemons
- Data pipelines

## Best Practices

1. **Use optional types** to avoid null pointer errors
2. **Prefer try?** over try! for safer error handling
3. **Use defer** for resource cleanup
4. **Test public APIs** not internal implementation
5. **Keep functions small** and focused
6. **Handle errors explicitly** don't swallow them
7. **Use channels** for concurrent communication
8. **Use newtypes** for type safety

## Resources

- **Official Site**: https://navi-lang.org
- **Standard Library**: https://navi-lang.org/stdlib/
- **GitHub**: https://github.com/navi-language/navi
- **Documentation**: https://navi-lang.org/learn/
- **Full Reference**: https://navi-lang.org/llms-full.txt

## File Extension

All Navi source files use the `.nv` extension.

## Contributing to This Skill

To improve this skill:

1. **SKILL.md** - Keep it concise, update only core patterns
2. **references/** - Add detailed documentation here
3. **examples/** - Add practical code examples
4. **README.md** - Update this overview

Follow the principle: SKILL.md is for quick reference, references/ is for deep dives.

## Version

Based on Navi language documentation v0.10.0+ (January 2025)

## License

This skill documentation follows the Navi language's license and usage terms.
