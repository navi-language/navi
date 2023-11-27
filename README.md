# Navi

Navi (/ˈnævi/) is a high-performance programming and stream computing language developed in Rust, originally designed for complex and high-performance computing tasks. It is also suited as a glue language embedded within heterogeneous services in financial systems.

In addition to its capabilities as a statically-typed, compiled language, Navi offers the convenience of script-like execution. It can compile source code into Bytecode (without JIT) or Machine Code (with JIT), providing a flexible development workflow. Theoretically, Navi delivering competitive performance on par with Go, Rust, and C.

## Language Design Philosophy

- **Simple and Clean Syntax**

  Designed with a straightforward and clean syntax.

- **Modern Optional Type and Error Handling**

  With modern optional type and error handling, Navi allows developers to gracefully manage exceptional cases and abnormal data.

- **No NULL Pointer Panic, Safe Runtime**

  No NULL pointer exceptions. Once your code compiles, you can expect consistent and reliable execution.

- **Scripted or Compilied Execution**

  Supports script-like execution, but offering same performance comparable to compiled languages like Go.

## Installation

```bash
curl -sSL https://navi-lang.org/install | sh
```

This script is also used for upgrading.

Or install a specific version:

```bash
# Install the latest nightly version
curl -sSL https://navi-lang.org/install | sh -s -- nightly
# Install a specific version
curl -sSL https://navi-lang.org/install | sh -s -- v0.9.0-nightly
```

## Usage

Run `navi -h` to get help.

```bash
$ navi -h
Usage: navi [OPTIONS] [COMMAND]

Commands:
  run      Run a navi script
  test     Test a navi script
  compile  Compile a navi script and show the bytecode
  help     Print this message or the help of the given subcommand(s)

Options:
  -p, --path <PATH>  Module path
  -h, --help         Print help information
  -V, --version      Print version information
```

### Run a Navi program

You can create a file named with `.nv` extension, and write some code in it, for example:

```rust
// main.nv
use std.io;

fn main() {
  io.println("Hello World.");
}

test "Hello World" {
  assert 1 == 1;
}
```

Then run it by:

```bash
$ navi run
Hello world, this is Navi.

$ navi test
```

### Examples

In `examples` directory, we provide some examples, you can run it by:

```bash
$ navi run
```

## Development Tools

Provides a Language Server Protocol(LSP) for Navi and Navi Stream, supporting most IDEs like VS Code, VIM, etc.

### VS Code Extension

You can visit VS Code Marketplace and install it.

https://marketplace.visualstudio.com/items?itemName=huacnlee.navi

## Performance

With the [MACD](https://en.wikipedia.org/wiki/MACD) indicator (about 2000 times indicator calculation), Navi takes 300 µs to calculate MACD once, and due to the characteristics of stream computing, in the server-side scenario where a large number of real-time calculations are required, Navi can rely on the calculation data of history to improve the efficiency of calculation, and Navi will get hundreds of times performance improvement.

Currently, we test that the memory occupied by 1.2 million targets in the server-side is only 1G.

## Which scenarios can Navi be used?

- As a market monitoring, by writing complex calculation logic, you can implement various market monitoring functions (alarm, decision, data construction).
- As a market monitoring client, by integrating SDK, write a real-time observation market data program, and make complex decisions (such as alarm, trading, etc.).
- Used in market charts, can be used as real-time market computing and draw on charts.

## Where is the source code?

There still have a lot of works need to do (Performance, Programming Features, Documents, Development Tools ...).

We may open source this project in future when we completed all of that.
