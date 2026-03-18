# 量化策略参数配置详解

> 本文档详细列出10个测试策略的完整参数配置，可直接用于实盘或进一步研究

---

## 数据说明

| 项目 | 参数 |
|-----|-----|
| **数据范围** | BTC/ETH: 2020-01-01 ~ 2025-03-14 (5年2个月) |
| **SOL数据** | 2022-05-01 ~ 2025-03-14 (2年10个月) |
| **IS(样本内)** | 2020-01-01 ~ 2022-12-31 (3年) |
| **手续费** | 0.1% (单边) |
| **滑点** | 0.05% |
| **初始资金** | 10,000 USDT |
| **单笔仓位** | 5% |

---

## 最优策略 ⭐⭐⭐⭐⭐

### B1-1: EMA50/200+ADX+HIGH_VOL+PriceAction+结构止损

**回测表现 (ETH)**
- 夏普率: **3.78** (全场最高)
- 收益率: **6.32%**
- 胜率: **66.7%**
- 最大回撤: **1.11%**
- 交易次数: **24笔**

**日线层 (趋势判断)**
```python
{
    "name": "EMA50_200_ADX14_25",
    "type": "ema_adx",
    "ema_fast": 50,
    "ema_slow": 200,
    "adx_period": 14,
    "adx_threshold": 25
}
```

**4小时层 (结构分析)**
```python
{
    "name": "HIGH_VOL",
    "zigzag": 0.10,        # 10%偏差
    "bb_period": 20,
    "bb_std": 3.0,         # 3σ
    "atr": 14,
    "ci_period": 14,
    "ci_trend": 35,
    "ci_chop": 65
}
```

**1小时层 (入场信号)**
```python
{
    "name": "PRICEACTION_ONLY",
    "type": "price_action"  # 纯价格行为，无指标
}
```

**风控配置**
```python
{
    "name": "STRUCTURE",
    "stop_type": "structure",  # 结构位止损
    "buffer": 0.01             # 1%缓冲
}
```

---

## 次优策略 ⭐⭐⭐⭐

### B1-2: EMA50/200+ADX+CONSERVATIVE+PriceAction+结构止损

**回测表现 (ETH)**
- 夏普率: **3.02**
- 收益率: **3.95%**
- 胜率: **62.1%**
- 最大回撤: **1.40%**
- 交易次数: **29笔**

**与最优策略区别**: 4H层使用CONSERVATIVE参数，更保守

**4小时层配置**
```python
{
    "name": "CONSERVATIVE",
    "zigzag": 0.08,        # 8%偏差 (vs 10%)
    "bb_period": 25,       # 25周期 (vs 20)
    "bb_std": 2.5,         # 2.5σ (vs 3.0)
    "atr": 20,             # 20周期 (vs 14)
    "ci_period": 20,
    "ci_trend": 40,        # 40阈值 (vs 35)
    "ci_chop": 60          # 60阈值 (vs 65)
}
```

---

## BTC最佳波段策略 ⭐⭐⭐⭐

### B2-1: EMA20/50交叉+PATTERN+STOCH+ATR_Trailing

**回测表现 (BTC)**
- 夏普率: **2.42**
- 收益率: **1.48%**
- 胜率: **53.8%**
- 最大回撤: **0.38%** (全场最低)
- 交易次数: **13笔**

**日线层**
```python
{
    "name": "EMA20_50_CROSS",
    "type": "ema_cross",
    "ema_fast": 20,
    "ema_slow": 50
    # 无ADX过滤
}
```

**4小时层**
```python
{
    "name": "PATTERN_FOCUS",
    "zigzag": 0.05,
    "bb_period": 20,
    "bb_std": 2.0,
    "atr": 14,
    "ci_period": 14,
    "ci_trend": 38.2,      # 斐波那契数
    "ci_chop": 61.8,       # 斐波那契数
    "pattern_focus": true
}
```

**1小时层**
```python
{
    "name": "STOCH_14_5_25_75",
    "type": "stoch",
    "k": 14,
    "d": 5,
    "oversold": 25,        # 超卖阈值
    "overbought": 75       # 超买阈值
}
```

**风控配置**
```python
{
    "name": "ATR_2_0X_TRAIL",
    "stop_type": "atr",
    "atr_multiplier": 2.0,
    "trailing": true,
    "trail_activate": 1.5  # 盈利1.5倍ATR后启动
}
```

---

## 其他策略速查表

| 编号 | 策略名称 | 日线 | 4H | 1H | 风控 | 夏普率 |
|:---:|---------|------|-----|-----|------|-------:|
| B1-1-BTC | EMA趋势+HIGH_VOL | EMA50/200+ADX14/25 | HIGH_VOL | PriceAction | 结构止损 | 2.15 |
| B1-2-BTC | EMA趋势+CONSERVATIVE | EMA50/200+ADX14/25 | CONSERVATIVE | PriceAction | 结构止损 | 2.10 |
| B2-2 | EMA20/50+ADX+PATTERN+STOCH | EMA20/50+ADX14/25 | PATTERN_FOCUS | STOCH 14/5/25/75 | ATR 2.0x+Trail | 1.99 |
| B2-3 | EMA20/100+ADX+PATTERN+PA | EMA20/100+ADX14/25 | PATTERN_FOCUS | PriceAction | ATR 2.0x+Trail | 1.71 |
| B2-4 | EMA20/100+ADX+PATTERN+STOCH | EMA20/100+ADX14/25 | PATTERN_FOCUS | STOCH 14/5/25/75 | ATR 2.0x+Trail | 1.67 |
| B2-5 | EMA20/50交叉+PATTERN+PA | EMA20/50_CROSS | PATTERN_FOCUS | PriceAction | ATR 2.0x+Trail | 1.55 |
| B2-6 | EMA50/200+ADX20/20+PATTERN+PA | EMA50/200+ADX20/20 | PATTERN_FOCUS | PriceAction | ATR 2.0x+Trail | 1.41 |

---

## 参数含义说明

### 日线层参数

| 参数 | 含义 | 常用值 |
|-----|-----|-------|
| `ema_fast` | 快线周期 | 20, 50 |
| `ema_slow` | 慢线周期 | 50, 100, 200 |
| `adx_period` | ADX计算周期 | 14, 20 |
| `adx_threshold` | 趋势强度阈值 | 20, 25, 30 |

### 4小时层参数

| 参数 | 含义 | 常用值 |
|-----|-----|-------|
| `zigzag` | ZigZag偏差百分比 | 0.03~0.10 |
| `bb_period` | 布林带周期 | 15, 20, 25 |
| `bb_std` | 布林带标准差 | 1.5, 2.0, 2.5, 3.0 |
| `ci_trend` | Choppiness趋势阈值 | 35, 38.2, 40 |
| `ci_chop` | Choppiness震荡阈值 | 60, 61.8, 65 |

### 1小时层参数

| 参数 | 含义 | 常用值 |
|-----|-----|-------|
| `type: price_action` | 纯价格行为 | 支撑阻力突破/回调 |
| `type: stoch` | 随机指标 | K=14, D=5 |
| `oversold` | 超卖阈值 | 20, 25 |
| `overbought` | 超买阈值 | 75, 80 |

### 风控参数

| 参数 | 含义 | 常用值 |
|-----|-----|-------|
| `stop_type: structure` | 结构位止损 | 前高/前低 ± 缓冲 |
| `stop_type: atr` | ATR止损 | 1.5x, 2.0x, 2.5x |
| `trailing` | 是否追踪止损 | true/false |
| `trail_activate` | 追踪止损激活倍数 | 1.5 (盈利1.5ATR后启动) |

---

## 快速开始

如果你想复现最优策略，只需配置以下参数：

```python
# 日线: 趋势判断
ema_fast = 50
ema_slow = 200
adx_period = 14
adx_threshold = 25

# 4H: 波动率过滤
zigzag = 0.10
bb_period = 20
bb_std = 3.0

# 1H: 入场
entry_type = "price_action"  # 纯价格行为

# 风控
stop_type = "structure"
stop_buffer = 0.01  # 1%
```

---

*报告生成时间: 2026-03-17*
