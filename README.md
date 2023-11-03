# Navi

Navi (/ˈnævi/) is a high-performance programming and stream computing language developed in Rust, originally designed for complex and high-performance computing tasks. It is also suited as a glue language embedded within heterogeneous services in financial systems.

In addition to its capabilities as a statically-typed, compiled language, Navi offers the convenience of script-like execution. It can compile source code into Bytecode (without JIT) or Machine Code (with JIT), providing a flexible development workflow. Theoretically, Navi delivering competitive performance on par with Go, Rust, and C.

> The name "Navi" is inspired by the concepts of the **Navy** and **Navigator**, emphasizing its role in guiding users through the vast expanse of financial data analysis and decision-making. It also pays tribute to the [Na'vi](https://learnnavi.org) tribe from the movie "Avatar," whose harmonious coexistence with nature is reflected in Navi's high performance and efficiency in the utilization of computational resources. The [Na'vi](https://learnnavi.org) unique ability to connect with the living world of Pandora through their braids finds a parallel in Navi's cross-platform capabilities and native Cloud support, ensuring seamless operation across various systems and environments. This symbolizes Navi's commitment to supporting a diversity of application environments and adaptability.

## Language Design Philosophy

- **Simple Syntax:** Navi is designed with a straightforward and clean syntax to reduce complexity and enhance readability, making it accessible for new programmers.
- **No Implicit Type Conversion:** The language enforces explicit type conversion to prevent unexpected behavior and errors, ensuring that data types are managed with intention and clarity.
- **Enhanced Optional Types and Error Handling:** Navi introduces robust optional types and error handling mechanisms, allowing developers to write safer code with clear pathways for managing the absence of values and the propagation of errors.
- **No Null Pointer Panic, Safe Runtime:** Navi eliminates the possibility of null pointer exceptions, one of the common pitfalls in programming, thereby providing a more secure and stable runtime environment.
- **Scripted Running:** Navi supports script-like execution without compromising on speed, offering compilation and runtime performance that is comparable to that of Go, thus blending the best of both scripting and compiled languages.

## Functionalities

- **Versatile Programming:** Provided serves as a general programming language (Navi), while also a streaming computing engine (Navi Stream).
- **High Performance:** As a statically-typed, compiled language, which is comparable to Go, Rust, and C.
- **User-Friendly Syntax:** Borrowing the syntax of excellent programming languages such as TypeScript, Rust, and Ruby, making it more intuitive and accessible for developers.
- **Stream Computing (Navi Stream):** Support stream computing, enabling efficient and real-time data analysis for dynamic services.
- **Native Financial Support:** Navi is equipped with native support for incremental financial data computation, making it ideal for real-time calculation and analysis of stock market data. It boasts a rich set of scientific computing capabilities, includes built-in functions for technical stock market indicators, and standard library support for LongPort OpenAPI, significantly reducing development costs for algorithmic trading.
- **Native Cloud Computing Support:** With its standard library, Navi enables seamless manipulation of cloud computing resources as if they were local, simplifying the integration and management of cloud services within services or applications.
- **Cross-platform:** Running on Linux, Windows, macOS, , and through WebAssembly (WASM), it extends its reach to iOS, Android, and Web Browsers.

## Installation

```bash
curl -sSL https://navi-lang.org/install | sh
```

This script is also used for upgrading.

Or install a specific version:

```bash
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

io.println("Hello World.");

test "Hello World" {
  assert 1 == 1;
}
```

Then run it by:

```bash
$ navi run main.nv
Hello world, this is Navi.

$ navi test .
```

### Examples

In `examples` directory, we provide some examples, you can run it by:

```bash
$ navi run examples/macd/main.nv
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
