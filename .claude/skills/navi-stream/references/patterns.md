# Navi Stream 常见模式和策略

量化交易中常用的指标模式和策略实现。

## 趋势判断模式

### 均线排列

**多头排列（看涨）:**
```nvs
let ma5 = ema(close, 5);
let ma10 = ema(close, 10);
let ma20 = ema(close, 20);
let ma60 = ema(close, 60);

// 多头排列：短期 > 中期 > 长期
let bull_alignment =
    ma5 > ma10 &&
    ma10 > ma20 &&
    ma20 > ma60;

// 加强信号：价格在均线之上
let strong_bull = bull_alignment && close > ma5;
```

**空头排列（看跌）:**
```nvs
// 空头排列：短期 < 中期 < 长期
let bear_alignment =
    ma5 < ma10 &&
    ma10 < ma20 &&
    ma20 < ma60;

// 加强信号：价格在均线之下
let strong_bear = bear_alignment && close < ma5;
```

### 趋势强度

```nvs
// 趋势持续性
let trend_consistency = count(close > ma20, 10);

// 强趋势：10 周期中至少 8 个在均线上方
let strong_trend = trend_consistency >= 8;

// 趋势加速
let ma_slope = ma20 - ma20[5];
let accelerating = ma_slope > ma_slope[5];

// 综合判断
export let strong_uptrend =
    close > ma20 &&
    ma20 > ma60 &&
    trend_consistency >= 8 &&
    accelerating;
```

## 交叉信号模式

### 金叉死叉

**基本金叉:**
```nvs
let fast_ma = ema(close, 5);
let slow_ma = ema(close, 20);

// 金叉：快线上穿慢线
let golden_cross =
    fast_ma > slow_ma &&
    fast_ma[1] <= slow_ma[1];

// 死叉：快线下穿慢线
let death_cross =
    fast_ma < slow_ma &&
    fast_ma[1] >= slow_ma[1];
```

**多重确认金叉:**
```nvs
// 金叉 + 趋势确认
let confirmed_golden =
    golden_cross &&
    close > slow_ma &&          // 价格确认
    slow_ma > slow_ma[1];       // 均线向上

// 金叉 + 成交量确认
let volume_confirmed_golden =
    golden_cross &&
    volume > volume[1];         // 放量
```

### MACD 交叉

```nvs
let fast = ema(close, 12);
let slow = ema(close, 26);
let diff = fast - slow;
let dea = ema(diff, 9);
let macd = (diff - dea) * 2;

// MACD 金叉
let macd_golden =
    diff > dea &&
    diff[1] <= dea[1];

// MACD 零轴上方金叉（强势）
let strong_macd_golden =
    macd_golden &&
    diff > 0 &&
    dea > 0;

// MACD 零轴下方金叉（弱势反转）
let weak_macd_golden =
    macd_golden &&
    diff < 0;
```

## 突破模式

### 价格突破

```nvs
// 突破近期高点
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

let resistance = dhhv(high, 20);
let support = dllv(low, 20);

// 向上突破
export let breakout =
    close > resistance[1] &&
    close[1] <= resistance[1];

// 向下突破（跌破支撑）
export let breakdown =
    close < support[1] &&
    close[1] >= support[1];

// 有效突破（带确认）
export let valid_breakout =
    breakout &&
    close > close[1] * 1.02 &&  // 涨幅超过2%
    volume > volume[1];          // 放量
```

### 通道突破

```nvs
// 布林带突破
let mid = ma(close, 20);
let upper = mid * 1.02;
let lower = mid * 0.98;

// 突破上轨
export let break_upper =
    close > upper &&
    close[1] <= upper[1];

// 突破下轨
export let break_lower =
    close < lower &&
    close[1] >= lower[1];

// 回归中轨（反转信号）
export let return_to_mid_from_upper =
    close < mid &&
    close[1] >= mid &&
    close[2] > upper;

export let return_to_mid_from_lower =
    close > mid &&
    close[1] <= mid &&
    close[2] < lower;
```

## 背离模式

### 价格背离

```nvs
// 自定义最高最低值函数
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

// 顶背离：价格创新高但指标未创新高
let price_high = dhhv(close, 20);
let indicator_high = dhhv(diff, 20);

export let bearish_divergence =
    close > price_high[5] &&        // 价格创新高
    diff < indicator_high[5];        // 指标未创新高

// 底背离：价格创新低但指标未创新低
let price_low = dllv(close, 20);
let indicator_low = dllv(diff, 20);

export let bullish_divergence =
    close < price_low[5] &&         // 价格创新低
    diff > indicator_low[5];         // 指标未创新低
```

### MACD 背离

```nvs
// MACD 计算
let fast = ema(close, 12);
let slow = ema(close, 26);
let diff = fast - slow;
let dea = ema(diff, 9);

// 寻找转折点
let n1 = barslast(diff[1] >= 0 && diff < 0);  // 上次转负
let m1 = barslast(diff[1] <= 0 && diff > 0);  // 上次转正

// 价格和指标的低点
let price_low1 = dllv(close, n1 + 1);
let price_low2 = price_low1[m1 + 1];
let diff_low1 = dllv(diff, n1 + 1);
let diff_low2 = diff_low1[m1 + 1];

// 底背离判断
export let macd_bullish_div =
    close < price_low2 &&      // 价格创新低
    diff > diff_low2 &&        // MACD 未创新低
    diff < 0;                  // 在零轴下方

// 标注信号
if (macd_bullish_div) {
    drawtext(diff, "底背离", #00ff00);
}
```

## 超买超卖模式

### RSI 超买超卖

```nvs
// RSI 计算
let change = close - close[1];
let gain = max(change, 0);
let loss = abs(min(change, 0));
let avg_gain = ema(gain, 14);
let avg_loss = ema(loss, 14);
let rs = avg_gain / max(avg_loss, 0.0001);
let rsi = 100 - (100 / (1 + rs));

// 超买超卖区域
export let overbought = rsi > 70;
export let oversold = rsi < 30;

// 进入超买超卖区域
let enter_overbought = rsi > 70 && rsi[1] <= 70;
let enter_oversold = rsi < 30 && rsi[1] >= 30;

// 离开超买超卖区域（反转信号）
export let sell_signal = rsi < 70 && rsi[1] >= 70;
export let buy_signal = rsi > 30 && rsi[1] <= 30;

// 极度超买超卖
export let extreme_overbought = rsi > 80;
export let extreme_oversold = rsi < 20;
```

### 多指标确认

```nvs
// RSI + 价格位置
let rsi_oversold = rsi < 30;
let price_near_support = close < ma20 * 0.98;

export let strong_buy_signal =
    rsi_oversold &&
    price_near_support &&
    rsi > rsi[1];  // RSI 开始回升

// RSI + MACD
let macd_golden = diff > dea && diff[1] <= dea[1];

export let confirmed_buy =
    rsi_oversold &&
    macd_golden &&
    volume > volume[1];
```

## 形态识别模式

### 双底形态

```nvs
// 简化的双底识别
let low1 = dllv(low, 20);
let low2_period = barslast(low <= low1 * 1.01);  // 找第二个低点

// 双底条件
export let double_bottom =
    low2_period > 10 &&           // 两个低点间隔足够
    low2_period < 40 &&           // 不要太远
    low <= low1 * 1.01 &&         // 第二个低点接近第一个
    low >= low1 * 0.99 &&
    close > close[1];              // 开始反弹
```

### 头肩形态（简化）

```nvs
// 识别左肩、头部、右肩
let peak1 = dhhv(high, 10);
let peak2 = dhhv(high[15], 10);
let peak3 = dhhv(high[30], 10);

// 头肩顶特征
export let head_and_shoulders =
    peak2 > peak1 * 1.05 &&       // 头部明显高于左肩
    peak2 > peak3 * 1.05 &&       // 头部明显高于右肩
    abs(peak1 - peak3) < peak1 * 0.03;  // 双肩高度接近
```

## 成交量模式

### 放量突破

```nvs
// 成交量均线
let vol_ma = ma(volume, 20);

// 放量定义
let high_volume = volume > vol_ma * 1.5;

// 放量上涨
export let volume_breakout =
    close > close[1] * 1.02 &&
    high_volume;

// 放量下跌
export let volume_breakdown =
    close < close[1] * 0.98 &&
    high_volume;
```

### 缩量整理

```nvs
// 缩量定义
let low_volume = volume < vol_ma * 0.7;

// 缩量横盘
let narrow_range = (high - low) < close * 0.01;

export let consolidation =
    low_volume &&
    narrow_range &&
    count(low_volume, 5) >= 3;  // 连续3天以上缩量
```

## 组合策略模式

### 趋势跟踪策略

```nvs
// 多重趋势确认
let ma5 = ema(close, 5);
let ma20 = ema(close, 20);
let ma60 = ema(close, 60);

let uptrend =
    ma5 > ma20 &&
    ma20 > ma60 &&
    close > ma5;

// 回调买入
let pullback_buy =
    uptrend &&                    // 趋势向上
    close < ma5 &&                // 回调到5日线
    close > ma20 &&               // 但在20日线上方
    close > close[1];             // 开始反弹

// 趋势加速
let trend_acceleration =
    uptrend &&
    (ma5 - ma20) > (ma5[5] - ma20[5]);

export let buy_signal = pullback_buy || trend_acceleration;
```

### 反转策略

```nvs
// 多重反转信号
let rsi_oversold = rsi < 30;
let price_low = close < dllv(close, 20) * 1.01;
let bullish_div = /* 底背离逻辑 */;
let volume_spike = volume > ma(volume, 20) * 1.5;

// 强烈反转信号
export let strong_reversal =
    rsi_oversold &&
    price_low &&
    bullish_div &&
    volume_spike;

// 一般反转信号（至少满足2个条件）
let reversal_score =
    (rsi_oversold ? 1 : 0) +
    (price_low ? 1 : 0) +
    (bullish_div ? 1 : 0) +
    (volume_spike ? 1 : 0);

export let reversal_signal = reversal_score >= 2;
```

### 波段交易策略

```nvs
// 波段高低点识别
let swing_high = dhhv(high, 10);
let swing_low = dllv(low, 10);

// 进入信号：突破波段高点
export let enter_long =
    close > swing_high[1] &&
    close[1] <= swing_high[1] &&
    volume > ma(volume, 20);

// 退出信号：跌破波段低点
export let exit_long =
    close < swing_low[1] &&
    close[1] >= swing_low[1];

// 止损：跌破近期低点的3%
let stop_loss_level = dllv(close, 5) * 0.97;
export let stop_loss = close < stop_loss_level;
```

## 最佳实践

### 信号确认

```nvs
// Good: 多重确认
let signal =
    golden_cross &&           // 技术信号
    close > ma20 &&           // 价格确认
    volume > volume[1] &&     // 成交量确认
    rsi > 30 && rsi < 70;     // 动量确认

// Bad: 单一信号
let signal = golden_cross;  // 容易产生假信号
```

### 参数优化

```nvs
// Good: 使用标准参数
param {
    ShortMA = 12,   // 标准MACD参数
    LongMA = 26,
    SignalMA = 9,
}

// Bad: 过度拟合
param {
    ShortMA = 13,   // 非标准参数
    LongMA = 27,    // 可能过度优化
    SignalMA = 8,
}
```

### 风险控制

```nvs
// 设置止损止盈
let entry_price = close;
export let stop_loss = entry_price * 0.95;   // 5%止损
export let take_profit = entry_price * 1.10;  // 10%止盈

// 仓位控制信号
export let position_size =
    rsi < 30 ? 1.0 :     // 超卖：满仓
    rsi < 50 ? 0.5 :     // 中性：半仓
    0.2;                  // 超买：轻仓
```

## 注意事项

1. **避免过度优化**: 使用标准参数，避免针对历史数据过度拟合
2. **多重确认**: 组合多个指标减少假信号
3. **考虑市场环境**: 不同策略适用于不同市场状态
4. **风险管理**: 始终设置止损，控制仓位
5. **回测验证**: 在历史数据上验证策略有效性
6. **持续优化**: 根据市场变化调整策略参数
