# Tushare Pro 数据接口说明

## 📋 概述

Tushare Pro 是一个金融数据平台，提供丰富的股票、基金、期货等金融数据接口。

**官方文档**: https://tushare.pro/document/2

---

## 🎯 我们系统需要的核心数据

### 1. **股票列表数据** ✅ 已实现

**接口**: `pro.stock_basic()`

**用途**: 获取所有A股上市公司基本信息

**参数**:
```python
df = pro.stock_basic(
    exchange='',        # 交易所: SSE上交所, SZSE深交所
    list_status='L',    # 上市状态: L上市, D退市, P暂停上市
    fields='ts_code,symbol,name,area,industry,list_date'
)
```

**返回字段**:
- `ts_code`: 股票代码（如 000001.SZ）
- `symbol`: 股票代码（如 000001）
- `name`: 股票名称
- `area`: 地域
- `industry`: 行业
- `market`: 市场类型（主板/创业板/科创板）
- `list_date`: 上市日期

**示例返回**:
```
ts_code    symbol  name    area  industry      list_date
000001.SZ  000001  平安银行  深圳   银行           19910403
000002.SZ  000002  万科A    深圳   房地产         19910129
```

---

### 2. **日线行情数据** ✅ 已实现

**接口**: `pro.daily()`

**用途**: 获取股票每日K线数据

**参数**:
```python
df = pro.daily(
    ts_code='000001.SZ',      # 股票代码
    trade_date='20231201',    # 交易日期（可选）
    start_date='20231101',    # 开始日期
    end_date='20231130'       # 结束日期
)
```

**返回字段**:
- `ts_code`: 股票代码
- `trade_date`: 交易日期
- `open`: 开盘价
- `high`: 最高价
- `low`: 最低价
- `close`: 收盘价
- `pre_close`: 昨收价
- `change`: 涨跌额
- `pct_chg`: 涨跌幅（%）
- `vol`: 成交量（手）
- `amount`: 成交额（千元）

**权限**: 免费用户可获取近3年日线数据

---

### 3. **实时行情数据** ⏳ 待实现

**接口**: `ts.realtime_quote()`

**用途**: 获取股票实时行情（分钟级）

**注意**: 
- 需要较高权限积分（2000分以上）
- 免费用户可能无法访问
- 替代方案：使用`pro.daily()`获取最新日线数据

**替代方案 - 最新日线**:
```python
# 获取今天的数据
df = pro.daily(trade_date='20231201')
```

---

### 4. **分钟级行情数据** ⏳ 可选

**接口**: `pro.stk_mins()`

**用途**: 获取1分钟/5分钟K线数据

**参数**:
```python
df = pro.stk_mins(
    ts_code='000001.SZ',
    freq='1min',              # 频率: 1min, 5min, 15min, 30min, 60min
    start_date='20231201',
    end_date='20231201'
)
```

**权限要求**: 需要5000积分以上

**对于免费用户**: 不可用，建议使用日线数据

---

### 5. **涨跌停数据** 📊 推荐

**接口**: `pro.limit_list()`

**用途**: 获取每日涨跌停股票统计

**参数**:
```python
df = pro.limit_list(
    trade_date='20231201',    # 交易日期
    limit_type='U'            # U涨停, D跌停
)
```

**返回字段**:
- `ts_code`: 股票代码
- `trade_date`: 交易日期
- `industry`: 行业
- `close`: 收盘价
- `pct_chg`: 涨跌幅
- `limit_times`: 涨停次数

**权限**: 免费用户可用

---

### 6. **龙虎榜数据** 📊 推荐

**接口**: `pro.top_list()`

**用途**: 获取沪深龙虎榜数据

**参数**:
```python
df = pro.top_list(
    trade_date='20231201'     # 交易日期
)
```

**返回字段**:
- `ts_code`: 股票代码
- `name`: 股票名称
- `close`: 收盘价
- `pct_chg`: 涨跌幅
- `amount`: 龙虎榜成交额
- `net_amount`: 龙虎榜净买额
- `buy`: 龙虎榜买入额
- `sell`: 龙虎榜卖出额

**权限**: 需要120积分

---

### 7. **股票每日指标** 📈 推荐

**接口**: `pro.daily_basic()`

**用途**: 获取股票每日重要基本指标

**参数**:
```python
df = pro.daily_basic(
    ts_code='000001.SZ',      # 股票代码
    trade_date='20231201'     # 交易日期
)
```

**返回字段**:
- `ts_code`: 股票代码
- `trade_date`: 交易日期
- `close`: 收盘价
- `turnover_rate`: 换手率（%）
- `turnover_rate_f`: 换手率（自由流通股）
- `volume_ratio`: 量比
- `pe`: 市盈率（总市值/净利润）
- `pe_ttm`: 市盈率（TTM）
- `pb`: 市净率（总市值/净资产）
- `ps`: 市销率
- `ps_ttm`: 市销率（TTM）
- `total_share`: 总股本（万股）
- `float_share`: 流通股本（万股）
- `free_share`: 自由流通股本（万股）
- `total_mv`: 总市值（万元）
- `circ_mv`: 流通市值（万元）

**权限**: 免费用户可用

**非常适合异动检测**：
- 换手率突增
- 量比异常
- 市值变化

---

### 8. **资金流向数据** 💰 推荐

**接口**: `pro.moneyflow()`

**用途**: 获取个股资金流向

**参数**:
```python
df = pro.moneyflow(
    ts_code='000001.SZ',
    start_date='20231101',
    end_date='20231130'
)
```

**返回字段**:
- `ts_code`: 股票代码
- `trade_date`: 交易日期
- `buy_sm_vol`: 小单买入量
- `buy_sm_amount`: 小单买入金额
- `sell_sm_vol`: 小单卖出量
- `sell_sm_amount`: 小单卖出金额
- `buy_md_vol`: 中单买入量
- `buy_md_amount`: 中单买入金额
- `sell_md_vol`: 中单卖出量
- `sell_md_amount`: 中单卖出金额
- `buy_lg_vol`: 大单买入量
- `buy_lg_amount`: 大单买入金额
- `sell_lg_vol`: 大单卖出量
- `sell_lg_amount`: 大单卖出金额
- `buy_elg_vol`: 特大单买入量
- `buy_elg_amount`: 特大单买入金额
- `sell_elg_vol`: 特大单卖出量
- `sell_elg_amount`: 特大单卖出金额
- `net_mf_vol`: 净流入量
- `net_mf_amount`: 净流入额

**权限**: 需要2000积分

**对异动检测非常有用**：
- 大单流入/流出
- 主力资金动向

---

### 9. **交易日历** 📅 基础

**接口**: `pro.trade_cal()`

**用途**: 获取交易所日历（交易日/休市日）

**参数**:
```python
df = pro.trade_cal(
    exchange='SSE',           # 交易所: SSE上交所, SZSE深交所
    start_date='20231101',
    end_date='20231130'
)
```

**返回字段**:
- `exchange`: 交易所
- `cal_date`: 日期
- `is_open`: 是否交易（0休市, 1交易）
- `pretrade_date`: 上一个交易日

**权限**: 免费用户可用

---

## 🔑 权限说明

Tushare Pro 采用积分制度：

| 积分等级 | 获取方式 | 可用接口 |
|---------|---------|---------|
| **0分（免费）** | 注册即可 | 股票列表、日线行情、每日指标、交易日历 |
| **120分** | 完善资料、分享 | + 龙虎榜数据 |
| **2000分** | 捐赠或积累 | + 资金流向、实时行情（受限） |
| **5000分+** | 捐赠 | + 分钟数据、tick数据 |

**获取积分**:
1. 完善个人资料：+50分
2. 每日签到：+1分
3. 分享到社交媒体：+20分
4. 捐赠：100元=2000分

---

## 💡 推荐的数据获取方案

### 方案一：免费版（0积分）

**可用数据**:
- ✅ 股票列表 (`stock_basic`)
- ✅ 日线行情 (`daily`)
- ✅ 每日指标 (`daily_basic`) - **推荐用于异动检测**
- ✅ 交易日历 (`trade_cal`)

**异动检测策略**:
```python
# 1. 获取昨天的数据
df_yesterday = pro.daily_basic(trade_date='20231130')

# 2. 获取今天的数据
df_today = pro.daily_basic(trade_date='20231201')

# 3. 计算异动指标
# - 换手率突增：今天换手率 > 昨天换手率 * 2
# - 量比异常：volume_ratio > 2
# - 价格异动：pct_chg > 5% 或 < -5%
```

**优点**: 完全免费，适合个人学习
**缺点**: 无实时数据，只能获取日线级别

---

### 方案二：基础版（120积分）

**额外可用**:
- ✅ 龙虎榜数据 (`top_list`) - 捕捉主力动向
- ✅ 涨跌停数据 (`limit_list`)

**异动检测增强**:
- 龙虎榜席位分析
- 涨停板监控

**获取方式**: 完善资料 + 分享，约1-2天可达到

---

### 方案三：专业版（2000积分+）

**额外可用**:
- ✅ 资金流向 (`moneyflow`) - **最强异动检测**
- ✅ 实时行情（受限）
- ✅ 更多财务数据

**异动检测**:
- 主力资金流入流出
- 大单异动
- 更精准的预警

**获取方式**: 捐赠100元

---

## 📊 我们系统的数据获取建议

### 阶段一：基础功能（免费版）

```python
# 1. 股票列表 - 系统初始化
stocks = pro.stock_basic(list_status='L')

# 2. 每日更新行情
daily_data = pro.daily(trade_date='today')
daily_basic = pro.daily_basic(trade_date='today')

# 3. 历史K线（用于图表展示）
history = pro.daily(ts_code='000001.SZ', start_date='20231101', end_date='20231130')

# 4. 异动检测（基于每日指标）
# - 换手率 > 10%
# - 量比 > 2
# - 涨跌幅 > 5%
```

**可实现功能**:
- ✅ 股票列表展示
- ✅ K线图表
- ✅ 基础异动预警
- ✅ 价格预警
- ✅ 换手率预警

---

### 阶段二：增强功能（120积分）

```python
# 5. 龙虎榜监控
top_list = pro.top_list(trade_date='today')

# 6. 涨停监控
limit_up = pro.limit_list(trade_date='today', limit_type='U')
limit_down = pro.limit_list(trade_date='today', limit_type='D')
```

**新增功能**:
- ✅ 龙虎榜异动
- ✅ 涨跌停统计
- ✅ 主力动向追踪

---

### 阶段三：专业功能（2000积分+）

```python
# 7. 资金流向分析
moneyflow = pro.moneyflow(ts_code='000001.SZ', start_date='today')

# 8. 实时数据（如果可用）
realtime = ts.realtime_quote(ts_code='000001.SZ')
```

**新增功能**:
- ✅ 大单流入流出预警
- ✅ 主力资金异动
- ✅ 实时监控（如果可用）

---

## 🎯 数据更新频率

| 数据类型 | 更新频率 | 建议获取频率 |
|---------|---------|------------|
| 股票列表 | 不定期 | 每周1次 |
| 日线行情 | 每日收盘后 | 每日18:00后 |
| 每日指标 | 每日收盘后 | 每日18:00后 |
| 龙虎榜 | 每日收盘后 | 每日19:00后 |
| 资金流向 | 每日收盘后 | 每日19:00后 |

---

## ⚠️ API调用限制

**免费用户**:
- 每分钟调用次数：200次
- 每小时调用次数：不限（但单接口有限制）
- 单个接口限制：如 `stock_basic` 每小时1次

**注意**:
- 测试连接时不要频繁调用
- 使用数据库缓存，避免重复请求
- 遵循"先缓存，后调用"原则

---

## 💻 代码示例

### 完整的数据获取示例

```python
import tushare as ts
from datetime import datetime, timedelta

# 初始化
pro = ts.pro_api('YOUR_TOKEN')

# 1. 获取股票列表（每周更新一次）
def get_stock_list():
    df = pro.stock_basic(
        list_status='L',
        fields='ts_code,symbol,name,area,industry,market,list_date'
    )
    return df

# 2. 获取今日行情
def get_today_market():
    today = datetime.now().strftime('%Y%m%d')
    df = pro.daily_basic(trade_date=today)
    return df

# 3. 异动检测
def detect_anomaly(df):
    # 换手率异动
    high_turnover = df[df['turnover_rate'] > 10]
    
    # 量比异动
    high_volume_ratio = df[df['volume_ratio'] > 2]
    
    # 价格异动
    price_change = df[(df['close'] > df['close'].shift(1) * 1.05) | 
                      (df['close'] < df['close'].shift(1) * 0.95)]
    
    return {
        'high_turnover': high_turnover,
        'high_volume_ratio': high_volume_ratio,
        'price_change': price_change
    }

# 4. 获取历史K线
def get_history_kline(ts_code, days=60):
    end_date = datetime.now()
    start_date = end_date - timedelta(days=days)
    
    df = pro.daily(
        ts_code=ts_code,
        start_date=start_date.strftime('%Y%m%d'),
        end_date=end_date.strftime('%Y%m%d')
    )
    return df
```

---

## 📌 总结

**推荐起步方案**：
1. **使用免费版** - 完全够用于学习和个人使用
2. **重点使用**：
   - `stock_basic` - 股票列表
   - `daily` - 日线行情
   - `daily_basic` - **核心！用于异动检测**
3. **异动检测指标**：
   - 换手率（turnover_rate）
   - 量比（volume_ratio）
   - 涨跌幅（pct_chg）
   - 成交量（vol）

**后续升级**：
- 完善资料获取120积分 → 龙虎榜数据
- 如需更专业功能，考虑捐赠获取2000分

---

**官方资源**:
- 官网: https://tushare.pro
- 文档: https://tushare.pro/document/2
- 积分规则: https://tushare.pro/document/1?doc_id=13
