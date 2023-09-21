# Navi

Navi (/ˈnævi/) is a high-performance programming language &amp; indicator engine, which is used to perform complex calculations and analysis based on market data.

We designed to be used in the quantitative trading system, and can be used in other scenarios where complex calculations are required.

Navi is written in Rust, which has high scalability and extremely high computing performance.

Navi is a static type, compiled language, we can compile the source code into Bytecode (Without JIT) or Machine Code (With JIT). From the theoretical point of view of programming language design, Navi is comparable to Go, Rust, etc., and the performance is comparable to Go, Rust, C, etc.

> Navi named after the **Navy** and **Navigator** words, which emphasize its navigation and leadership functions, and also borrowed the language naming of the [Na'vi](https://learnnavi.org) tribe in the movie "Avatar". This name means that Navi leads users to navigate in the ocean of data analysis and decision-making, and also symbolizes the recognition and support of the diversity and flexibility of data analysis and decision-making.

## Features

- Provided for general programming language (Navi), indicator engine (Navi Stream).
- High performance: Navi is a static type, compiled language, which is comparable to Go, Rust, and C.
- Easy to use: Borrowing the syntax of excellent programming languages such as TypeScript, Rust, and Ruby, Navi syntax is more user-friendly.
- Stream computing (Navi Stream): Support stream computing, which can be used for real-time analysis.
- Cross-platform (Navi Stream): Support running on Linux, Windows, macOS, etc., support iOS, Android, and run on Browser via WASM.

## Installation

```bash
curl -sSL https://navi-lang.org/install | sh
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
  assert 1 == 1
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

We build a Navi LSP for provided a Language Server Protocol for Navi Stream, to provide a lot of features for most IDEs (VS Code, VIM ...).

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
