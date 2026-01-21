# Navi Stream Language Skill

User guide and best practices for the Navi Stream language.

## Overview

Navi Stream (.nvs) is a domain-specific language designed specifically for quantitative trading and technical analysis. It is optimized for real-time streaming data processing and technical indicator calculations.

**Core Features:**
- **Streaming Processing** - Process market data tick by tick
- **Technical Indicators** - Rich built-in TA function library
- **Visualization** - Native plotting support
- **Parameterization** - Dynamic parameter configuration
- **Internationalization** - Multi-language label support
- **Navi Integration** - Seamless integration with Navi language

## Skill Structure

```
navi-stream/
├── SKILL.md              # Core skill (loaded when triggered)
├── references/           # Detailed documentation (load as needed)
│   ├── syntax.md        # Complete syntax reference
│   ├── indicators.md    # Technical indicator functions in detail
│   ├── plotting.md      # Plotting system guide
│   └── patterns.md      # Common patterns and strategies
├── examples/            # Runnable examples
│   ├── macd.nvs         # MACD indicator
│   ├── ma_cross.nvs     # Moving average crossover
│   ├── bollinger.nvs    # Bollinger Bands
│   └── rsi.nvs          # RSI indicator
└── README.md            # This file
```

## How to Use This Skill

### Automatic Activation

This skill automatically activates when you work with:
- Writing Navi Stream code
- Working with `.nvs` files
- Developing technical indicators
- Quantitative trading related tasks

### Main SKILL.md

The main `SKILL.md` provides:
- Quick syntax reference
- Core concepts explanation
- Common patterns
- When to load detailed references

### Reference Files

Load reference files when you need detailed information:

```
Read ~/.claude/skills/navi-stream/references/syntax.md      # Complete syntax
Read ~/.claude/skills/navi-stream/references/indicators.md  # Technical indicators
Read ~/.claude/skills/navi-stream/references/plotting.md    # Plotting system
Read ~/.claude/skills/navi-stream/references/patterns.md    # Common patterns
```

## Quick Reference

### Basic NVS File

```nvs
// 1. Metadata
meta {
    title = "My Indicator",
    overlay = false,
}

// 2. Import modules
use quote, ta;

// 3. Parameters
param {
    Period = 20,
}

// 4. Calculation
let ma = ema(close, Period);

// 5. Export
export let signal = close > ma;
```

### Using in Navi

```nv
use nvs.my_indicator;

let indicator = my_indicator.new();
indicator.execute(
    time: timestamp,
    close: price,
    // ... other fields
);

println(`signal=${indicator.signal:?}`);
```

## Key Concepts

### Streaming Processing

NVS code is streaming:
- Each `execute()` processes one new data point
- Can access historical data (e.g., `close[1]`)
- State persists across multiple calls

### Data Access

```nvs
close        // Current close price
close[1]     // Previous period close
close[n]     // N periods ago close
```

### Exported Variables

```nvs
export let signal = buy_signal;  // Becomes output
export let value = calculated;   // Accessible in Navi
```

## Common Use Cases

### Technical Indicator Development

Create custom technical indicators:
- Moving average systems
- Oscillators
- Trend indicators
- Volume indicators

### Strategy Signals

Generate trading signals:
- Buy/sell signals
- Trend determination
- Divergence detection
- Pattern recognition

### Data Analysis

Real-time data analysis:
- Price momentum
- Volatility calculation
- Correlation analysis
- Statistical indicators

## Best Practices

1. **Use meaningful parameter names** and set reasonable ranges
2. **Avoid repeated calculations** of same expressions
3. **Use intermediate variables** to improve code readability
4. **Add internationalization labels** for multi-language support
5. **Export key variables** for external access
6. **Use colors to differentiate** indicator lines
7. **Add text annotations** to mark key signals

## Learning Path

1. **Getting Started** - Read `SKILL.md` quick reference
2. **Syntax** - Review `references/syntax.md` complete syntax
3. **Indicators** - Learn `references/indicators.md` technical functions
4. **Plotting** - Master `references/plotting.md` visualization
5. **Patterns** - Study `references/patterns.md` common strategies
6. **Examples** - Run code in `examples/` directory

## Differences from Navi Language

| Feature | Navi (.nv) | Navi Stream (.nvs) |
|---------|------------|-------------------|
| Purpose | General programming | Technical indicators/quantitative |
| Data Model | Standard variables | Streaming time series data |
| Built-in Modules | std.* | ta, quote |
| Special Features | spawn, Worker | plot, historical data access |
| Execution | Direct run | Called from Navi |

## Examples

See `examples/` directory for complete examples:
- MACD indicator implementation
- Moving average crossover strategy
- Bollinger Bands channel
- RSI overbought/oversold

## Resources

- **Official Website**: https://navi-lang.org
- **File Extension**: `.nvs`
- **Navi Integration**: Import via `use nvs.module`

## Contributing to This Skill

To improve this skill:

1. **SKILL.md** - Keep it concise, update core patterns
2. **references/** - Add detailed documentation
3. **examples/** - Add practical examples
4. **README.md** - Update overview

Principle: SKILL.md for quick reference, references/ for deep dives.

## Version

Based on Navi language documentation v0.10.0+ (January 2025)

## License

This skill documentation follows the Navi language's license and usage terms.
