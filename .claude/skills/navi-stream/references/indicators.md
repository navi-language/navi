# Navi Stream Technical Indicator Functions Reference

Complete reference for built-in technical analysis functions in Navi Stream.

## Module Imports

```nvs
use ta;      // Technical analysis functions
use quote;   // Market data
use math;    // Mathematical functions
```

## Moving Average Functions

### ma() - Simple Moving Average

**Syntax:**
```nvs
ma(source: number, period: number): number
```

**Description:**
Calculates Simple Moving Average (SMA), the average of the last N periods.

**Examples:**
```nvs
// 20-period simple moving average
let sma20 = ma(close, 20);

// Apply to different data sources
let high_ma = ma(high, 10);
let low_ma = ma(low, 10);
let volume_ma = ma(volume, 20);
```

**Use Cases:**
- Trend identification
- Support/resistance levels
- Moving average crossover strategies

### ema() - Exponential Moving Average

**Syntax:**
```nvs
ema(source: number, period: number): number
```

**Description:**
Calculates Exponential Moving Average (EMA), giving more weight to recent data. Responds faster than SMA.

**Examples:**
```nvs
// MACD uses EMA
let fast_ema = ema(close, 12);
let slow_ema = ema(close, 26);
let diff = fast_ema - slow_ema;
let dea = ema(diff, 9);

// Multiple period EMAs
let ema5 = ema(close, 5);
let ema10 = ema(close, 10);
let ema20 = ema(close, 20);
```

**Use Cases:**
- MACD indicator
- Trend following
- Quick response to price changes

**Characteristics:**
- More sensitive to recent data
- Less smooth than SMA
- Lower lag than SMA

## Mathematical Functions

### min() - Minimum Value

**Syntax:**
```nvs
min(a: number, b: number): number
```

**Description:**
Returns the smaller of two numbers.

**Examples:**
```nvs
// Get smaller value
let lower = min(price1, price2);

// Cap value
let capped = min(value, 100);

// Find minimum in loop
let min_val = values.get(0);
for (let i in 1..values.len()) {
    min_val = min(min_val, values.get(i));
}

// Avoid division by zero
let safe_divisor = max(denominator, 0.0001);
let ratio = numerator / safe_divisor;
```

**Use Cases:**
- Find minimum value
- Set lower bounds
- Avoid division by zero errors

### max() - Maximum Value

**Syntax:**
```nvs
max(a: number, b: number): number
```

**Description:**
Returns the larger of two numbers.

**Examples:**
```nvs
// Get larger value
let higher = max(price1, price2);

// Set minimum value
let positive = max(value, 0);

// Calculate gains
let gain = max(close - close[1], 0);

// Find maximum in loop
let max_val = values.get(0);
for (let i in 1..values.len()) {
    max_val = max(max_val, values.get(i));
}
```

**Use Cases:**
- Find maximum value
- Set upper bounds
- Ensure non-negative values

### abs() - Absolute Value

**Syntax:**
```nvs
abs(value: number): number
```

**Description:**
Returns the absolute value of a number.

**Examples:**
```nvs
// Calculate decline (positive value)
let loss = abs(min(close - close[1], 0));

// Price volatility
let volatility = abs(high - low);

// Used in RSI calculation
let price_change = close - close[1];
let gain = max(price_change, 0);
let loss = abs(min(price_change, 0));

// Check magnitude of change
let change = abs(close - open);
if (change > threshold) {
    // High volatility
}
```

**Use Cases:**
- Calculate amplitude
- RSI and other indicators
- Direction-independent distance measurements

## Statistical Functions

### count() - Conditional Count

**Syntax:**
```nvs
count(condition: boolean, periods: number): number
```

**Description:**
Counts how many times a condition was true in the last N periods.

**Examples:**
```nvs
// Count up days in last 10 periods
let up_count = count(close > open, 10);

// Count golden crosses in last 20 periods
let cross_count = count(fast_ma > slow_ma, 20);

// Check for consecutive ups
let consecutive_up = count(close > close[1], 5) == 5;

// Trend strength
let trend_strength = count(close > ma20, 10);
if (trend_strength >= 8) {
    // Strong uptrend
}
```

**Use Cases:**
- Trend strength assessment
- Frequency statistics
- Continuity checks

### barslast() - Bars Since Condition

**Syntax:**
```nvs
barslast(condition: boolean): number
```

**Description:**
Returns the number of bars since the condition was last true.

**Examples:**
```nvs
// Bars since last golden cross
let bars_since_cross = barslast(fast_ma > slow_ma && fast_ma[1] <= slow_ma[1]);

// MACD divergence detection
let n1 = barslast(macd[1] >= 0 && macd < 0);  // Last time turned negative
let mm1 = barslast(macd[1] <= 0 && macd > 0);  // Last time turned positive

// Check duration
let bars_above_ma = barslast(close <= ma20);
if (bars_above_ma > 10) {
    // Been above MA for more than 10 bars
}
```

**Use Cases:**
- Signal duration
- Divergence detection
- Pattern recognition

## Helper Functions (Custom)

### dhhv() - Highest Value in Range

**Implementation:**
```nvs
fn dhhv(x: number, n: number): number {
    var values = array.new::<number>();
    if (barstate.is_confirmed) {
        values.unshift(x);
    }

    let r: number = values.get(0);
    for (let i in 1..min(n, values.len())) {
        r = max(r, values.get(i));
    }
    return r;
}
```

**Usage:**
```nvs
// Highest price in last 20 periods
let highest = dhhv(high, 20);

// Highest MACD in last N periods
let macd_high = dhhv(macd, n);

// Breakout detection
if (close > dhhv(high, 20)[1]) {
    // Breaking above 20-period high
}
```

**Use Cases:**
- Breakout detection
- Resistance identification
- Divergence analysis

### dllv() - Lowest Value in Range

**Implementation:**
```nvs
fn dllv(x: number, n: number): number {
    var values = array.new::<number>();
    if (barstate.is_confirmed) {
        values.unshift(x);
    }

    let r: number = values.get(0);
    for (let i in 1..min(n, values.len())) {
        r = min(r, values.get(i));
    }
    return r;
}
```

**Usage:**
```nvs
// Lowest price in last 20 periods
let lowest = dllv(low, 20);

// Lowest MACD in last N periods
let macd_low = dllv(macd, n);

// Support check
if (close < dllv(low, 20)[1]) {
    // Breaking below 20-period low
}
```

**Use Cases:**
- Breakdown detection
- Support identification
- Divergence analysis

## Common Technical Indicator Implementations

### MACD

```nvs
param {
    Fast = 12,
    Slow = 26,
    Signal = 9,
}

let fast_ema = ema(close, Fast);
let slow_ema = ema(close, Slow);

export let diff = fast_ema - slow_ema;
export let dea = ema(diff, Signal);
export let macd = (diff - dea) * 2;

// Signals
let golden_cross = diff > dea && diff[1] <= dea[1];
let death_cross = diff < dea && diff[1] >= dea[1];
```

### RSI

```nvs
param {
    Period = 14,
}

let change = close - close[1];
let gain = max(change, 0);
let loss = abs(min(change, 0));

let avg_gain = ema(gain, Period);
let avg_loss = ema(loss, Period);

let rs = avg_gain / max(avg_loss, 0.0001);
export let rsi = 100 - (100 / (1 + rs));

// Overbought/oversold
export let overbought = rsi > 70;
export let oversold = rsi < 30;
```

### Bollinger Bands (Simplified)

```nvs
param {
    Period = 20,
    UpperMult = 1.02,
    LowerMult = 0.98,
}

let mid = ma(close, Period);

export let upper = mid * UpperMult;
export let middle = mid;
export let lower = mid * LowerMult;

// Signals
export let break_up = close > upper && close[1] <= upper[1];
export let break_down = close < lower && close[1] >= lower[1];
```

## Best Practices

### Parameter Selection

```nvs
// Good: Reasonable defaults and ranges
param {
    @meta(range = 5..100)
    ShortPeriod = 12,  // Common value

    @meta(range = 10..200)
    LongPeriod = 26,
}

// Bad: Extreme or unreasonable defaults
param {
    Period = 500,  // Too long
    Multiplier = 100,  // Too large
}
```

### Avoid Overfitting

```nvs
// Good: Use standard periods
let ma20 = ema(close, 20);
let ma60 = ema(close, 60);

// Bad: Over-optimized periods
let ma23 = ema(close, 23);
let ma67 = ema(close, 67);
```

### Performance Optimization

```nvs
// Good: Reuse calculation results
let ma20 = ema(close, 20);
let signal1 = close > ma20;
let signal2 = ma20 > ma20[1];
let signal3 = ma20 > ma20[5];

// Bad: Repeated calculations
let signal1 = close > ema(close, 20);
let signal2 = ema(close, 20) > ema(close, 20)[1];
let signal3 = ema(close, 20) > ema(close, 20)[5];
```

### Combine Indicators

```nvs
// Trend + Oscillator combination
let trend = ema(close, 20);
let rsi_val = calculate_rsi(14);

// Multiple confirmations
let buy_signal =
    close > trend &&           // Uptrend
    trend > trend[1] &&        // Trend accelerating
    rsi_val < 30 &&           // Oversold
    rsi_val > rsi_val[1];     // RSI turning up

export let strong_buy = buy_signal;
```

## Important Notes

1. **Historical Data Dependency**: Indicators need sufficient historical data for accurate calculations
2. **Lag**: Moving averages and similar indicators have inherent lag
3. **Parameter Sensitivity**: Different parameters can significantly affect indicator performance
4. **Market Adaptability**: Indicators perform differently in various market conditions
5. **Combined Use**: Single indicators are prone to false signals; combine multiple indicators
