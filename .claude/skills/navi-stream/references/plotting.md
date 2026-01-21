# Navi Stream Plotting System Reference

Complete reference for built-in plotting functions in Navi Stream.

## Plotting Module

Plotting functions are built into Navi Stream - no import required.

## plot() - Line Plot

### Syntax

```nvs
plot(value: number, title: string, color: color, key: string)
```

### Parameters

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| `value` | number | Value to plot | Yes |
| `title` | string | Series title | No |
| `color` | color | Line color (hexadecimal) | No |
| `key` | string | Unique series identifier | No |

### Basic Usage

```nvs
// Simple line plot
plot(close);

// With title
plot(ma20, title: "MA20");

// With color
plot(ma20, title: "MA20", color: #ff0000);

// Full parameters
plot(ma20, title: "MA20", color: #ddff53, key: "ma20");
```

### Multiple Lines

```nvs
// Plot multiple moving averages
plot(ma5, title: "MA5", color: #ddff53, key: "ma5");
plot(ma10, title: "MA10", color: #4781ff, key: "ma10");
plot(ma20, title: "MA20", color: #fc6ebc, key: "ma20");

// MACD indicator
plot(diff, title: "DIF", color: #ffffff);
plot(dea, title: "DEA", color: #ffff00);
plot(macd, title: "MACD", color: #ff00ff);
```

### Conditional Plotting

```nvs
// Plot different colors based on condition
if (close > ma20) {
    plot(close, title: "Price", color: #ff0000);  // Red
} else {
    plot(close, title: "Price", color: #00ff00);  // Green
}

// Plot only when condition met
if (signal_active) {
    plot(signal_line, title: "Signal", color: #ffff00);
}
```

### Color Usage

```nvs
// Hexadecimal colors
let red = #ff0000;
let green = #00ff00;
let blue = #0000ff;

// Common colors
let white = #ffffff;
let black = #000000;
let yellow = #ffff00;
let cyan = #00ffff;
let magenta = #ff00ff;

// Custom colors
let custom1 = #ddff53;  // Bright yellow-green
let custom2 = #4781ff;  // Bright blue
let custom3 = #fc6ebc;  // Pink
```

## stick() - Draw Stick/Candlestick

### Syntax

```nvs
stick(top: number, bottom: number, color: color, hollow: boolean)
```

### Parameters

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| `top` | number | Top position | Yes |
| `bottom` | number | Bottom position | Yes |
| `color` | color | Color | Yes |
| `hollow` | boolean | Whether hollow | No |

### Basic Usage

```nvs
// Draw solid stick
stick(high, low, #ff0000);

// Draw hollow stick
stick(high, low, #00ff00, hollow: true);
```

### Candlestick Drawing

```nvs
// Draw candlesticks based on price movement
if (close > open) {
    // Up: red solid
    stick(high, low, #ff0000, hollow: false);
} else if (close < open) {
    // Down: green hollow
    stick(high, low, #00ff00, hollow: true);
} else {
    // Flat: yellow
    stick(high, low, #ffff00, hollow: true);
}
```

### Range Marking

```nvs
// Mark price range
let upper = ma20 * 1.02;
let lower = ma20 * 0.98;

if (close > upper || close < lower) {
    stick(upper, lower, #blue, hollow: true);
}

// Support/resistance levels
if (signal_active) {
    stick(resistance, support, #yellow, hollow: true);
}
```

### Signal Marking

```nvs
// Mark golden cross position
if (golden_cross) {
    stick(close * 1.05, close * 0.95, #red, hollow: false);
}

// Mark death cross position
if (death_cross) {
    stick(close * 1.05, close * 0.95, #green, hollow: false);
}
```

## fill() - Fill Area

### Syntax

```nvs
fill(top: number, bottom: number, color: color)
```

### Parameters

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| `top` | number | Upper boundary | Yes |
| `bottom` | number | Lower boundary | Yes |
| `color` | color | Fill color | Yes |

### Basic Usage

```nvs
// Fill area between two lines
fill(upper_band, lower_band, #blue);
```

### Channel Fill

```nvs
// Bollinger Bands channel
let upper = mid * 1.02;
let lower = mid * 0.98;

fill(upper, lower, #4444ff);
plot(upper, color: #ff0000);
plot(mid, color: #ffff00);
plot(lower, color: #00ff00);
```

### Conditional Fill

```nvs
// Fill only on breakout
if (close > upper || close < lower) {
    fill(upper, lower, #ff4444);
}

// Trend area marking
if (uptrend) {
    fill(close, ma20, #44ff44);
} else if (downtrend) {
    fill(close, ma20, #ff4444);
}
```

### Multi-layer Fill

```nvs
// Multiple price ranges
let level1 = ma * 1.05;
let level2 = ma * 1.02;
let level3 = ma * 0.98;
let level4 = ma * 0.95;

fill(level1, level2, #ff4444);
fill(level2, ma, #44ff44);
fill(ma, level3, #4444ff);
fill(level3, level4, #ff44ff);
```

## polyline() - Draw Polyline

### Syntax

```nvs
polyline(value: number, color: color)
```

### Parameters

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| `value` | number | Value | Yes |
| `color` | color | Color | Yes |

### Basic Usage

```nvs
// Simple polyline
polyline(value, #ff0000);
```

### Conditional Drawing

```nvs
// Draw different colors based on trend
if (close > open) {
    polyline(close, #ff0000);  // Up: red
}

if (close < open) {
    polyline(close, #00ff00);  // Down: green
}
```

### Signal Lines

```nvs
// Breakout signal
if (close[1] < ma && close > ma) {
    polyline(close, #yellow);
}
```

## drawtext() - Draw Text

### Syntax

```nvs
drawtext(y_position: number, text: string, color: color)
```

### Parameters

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| `y_position` | number | Y-axis position | Yes |
| `text` | string | Text content | Yes |
| `color` | color | Text color | Yes |

### Basic Usage

```nvs
// Draw text at specified position
drawtext(close, "Mark", #ff0000);

// Relative positioning
drawtext(close * 1.05, "Text Above", #ff0000);
drawtext(close * 0.95, "Text Below", #00ff00);
```

### Signal Annotations

```nvs
// Golden cross signal
if (golden_cross) {
    drawtext(diff, "Golden Cross", #ff0000);
}

// Death cross signal
if (death_cross) {
    drawtext(diff, "Death Cross", #00ff00);
}

// Breakout annotation
if (close > resistance) {
    drawtext(close * 1.02, "Breakout", #ffff00);
}

// Breakdown annotation
if (close < support) {
    drawtext(close * 0.98, "Breakdown", #ff00ff);
}
```

### Overbought/Oversold Annotations

```nvs
// RSI overbought
if (rsi > 70 && rsi[1] <= 70) {
    drawtext(rsi, "Overbought", #ff0000);
}

// RSI oversold
if (rsi < 30 && rsi[1] >= 30) {
    drawtext(rsi, "Oversold", #00ff00);
}
```

### Divergence Annotations

```nvs
// Bearish divergence
if (bearish_divergence) {
    drawtext(rsi, "Bearish Div", #ff0000);
}

// Bullish divergence
if (bullish_divergence) {
    drawtext(rsi, "Bullish Div", #00ff00);
}
```

### Dynamic Text

```nvs
// Use string interpolation
if (signal) {
    drawtext(value, `Signal: ${value}`, #ff0000);
}

// Combined information
let label = `MA: ${ma20} RSI: ${rsi}`;
drawtext(close, label, #ffff00);
```

## Comprehensive Examples

### Complete MACD Indicator

```nvs
meta {
    title = "MACD",
    overlay = false,
}

use quote, ta;

param {
    Fast = 12,
    Slow = 26,
    Signal = 9,
}

// Calculate
let fast_ema = ema(close, Fast);
let slow_ema = ema(close, Slow);
let diff = fast_ema - slow_ema;
let dea = ema(diff, Signal);
let macd = (diff - dea) * 2;

// Plot lines
plot(diff, title: "DIF", color: #ffffff);
plot(dea, title: "DEA", color: #ffff00);
plot(macd, title: "MACD", color: #ff00ff);

// Plot zero line
plot(0, color: #888888);

// Signal detection
let golden = diff > dea && diff[1] <= dea[1];
let death = diff < dea && diff[1] >= dea[1];

// Annotate signals
if (golden) {
    drawtext(macd, "Golden Cross", #ff0000);
}

if (death) {
    drawtext(macd, "Death Cross", #00ff00);
}
```

### Complete Bollinger Bands Indicator

```nvs
meta {
    title = "Bollinger Bands",
    overlay = true,
}

use quote, ta;

param {
    Period = 20,
    Upper = 1.02,
    Lower = 0.98,
}

// Calculate
let mid = ma(close, Period);
let upper = mid * Upper;
let lower = mid * Lower;

// Plot channel
plot(upper, title: "Upper", color: #ff0000);
plot(mid, title: "Mid", color: #ffff00);
plot(lower, title: "Lower", color: #00ff00);

// Fill area
if (close > upper || close < lower) {
    fill(upper, lower, #4444ff);
}

// Breakout annotations
let break_up = close > upper && close[1] <= upper[1];
let break_down = close < lower && close[1] >= lower[1];

if (break_up) {
    drawtext(upper * 1.01, "Upper Break", #ff0000);
}

if (break_down) {
    drawtext(lower * 0.99, "Lower Break", #00ff00);
}

// Squeeze signal
let width = (upper - lower) / mid;
if (width < 0.02) {
    drawtext(mid, "Squeeze", #ffff00);
}
```

## Best Practices

### Color Selection

```nvs
// Good: Use contrasting colors
plot(ma5, color: #ff0000);   // Red
plot(ma10, color: #00ff00);  // Green
plot(ma20, color: #0000ff);  // Blue

// Bad: Use similar colors
plot(ma5, color: #ff0000);
plot(ma10, color: #ff3333);
plot(ma20, color: #ff6666);
```

### Layer Management

```nvs
// Good: Fill first, then lines, then annotations
fill(upper, lower, #4444ff);
plot(upper, color: #ff0000);
plot(lower, color: #00ff00);
drawtext(signal_pos, "Signal", #ffff00);

// Avoid too many layers
```

### Conditional Drawing

```nvs
// Good: Draw only when needed
if (significant_event) {
    drawtext(close, "Important Signal", #ff0000);
}

// Bad: Annotate every bar
drawtext(close, "Price", #ff0000);  // Too dense
```

### Text Positioning

```nvs
// Good: Relative positioning, avoid overlap
if (buy_signal) {
    drawtext(close * 0.95, "Buy", #ff0000);  // Below
}

if (sell_signal) {
    drawtext(close * 1.05, "Sell", #00ff00);  // Above
}

// Bad: Fixed position may overlap
drawtext(close, "Signal", #ff0000);
drawtext(close, "Another Signal", #00ff00);  // Overlaps
```

## Important Notes

1. **Performance Consideration**: Avoid too many plot calls
2. **Color Contrast**: Ensure colors are visible on different backgrounds
3. **Text Density**: Avoid overly dense text annotations
4. **Layer Order**: Important elements should be drawn last (top layer)
5. **Conditional Drawing**: Draw only at key moments to reduce visual noise
