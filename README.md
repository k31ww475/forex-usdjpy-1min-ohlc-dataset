# High-quality USD/JPY forex rate 1-minute OHLC database (approx. 25 years, 8.5+ million records)

This database contains 1-minute candlestick data for USD/JPY forex rates spanning approximately 25 years from January 2001 to November 2025. Optimized for machine learning, statistical analysis, algorithmic trading research, and various other applications.

## Download

### Get the Database File

The database file `USDJPY.db` (1.05 GB) is available via GitHub Releases:

1. Go to [Releases](https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/releases)
2. Download `USDJPY.db` from the latest release
3. Place the file in your working directory

**Alternative: Using Git LFS**

If using Git LFS for large file management:

```bash
git lfs install
git clone https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset.git
cd forex-usdjpy-1min-ohlc-dataset
```

**Using wget or curl**

```bash
# Replace with actual release URL
wget https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/releases/download/v1.0/USDJPY.db

# Or using curl
curl -L -O https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/releases/download/v1.0/USDJPY.db
```

## Database Specifications

| Item | Details |
|------|---------|
| Filename | USDJPY.db |
| File Size | 1.05 GB |
| Format | SQLite 3.x |
| Total Records | 8,505,163 |
| Data Period | 2001-01-02 04:10:00 ~ 2025-11-14 16:58:00 |
| Duration | Approx. 24.9 years (9,082 days) |
| Currency Pair | JPY=X (USD/JPY) |
| Timeframe | 1-minute |

## Schema

| Column | Data Type | Description | Constraints |
|--------|-----------|-------------|-------------|
| symbol | TEXT | Currency pair identifier | PRIMARY KEY, NOT NULL |
| datetime | TEXT | Timestamp (YYYY-MM-DD HH:MM:SS) | PRIMARY KEY, NOT NULL |
| open | REAL | Open price | - |
| high | REAL | High price | - |
| low | REAL | Low price | - |
| close | REAL | Close price | - |

### Indexes

- `idx_datetime`: Single-column index on datetime (for fast searches)
- `sqlite_autoindex_tb1m_1`: Composite PRIMARY KEY on (symbol, datetime)

## Data Statistics

### Overall Statistics

| Metric | Open | High | Low | Close |
|--------|------|------|-----|-------|
| Minimum | 75.61 | 75.61 | 75.56 | 75.61 |
| Maximum | 161.95 | 161.95 | 161.94 | 161.95 |
| Average | 112.30 | 112.31 | 112.29 | 112.30 |
| Range | 86.34 | 86.34 | 86.38 | 86.34 |

### Yearly Breakdown

| Year | Records | Close Range | Avg Price |
|------|---------|-------------|-----------|
| 2001 | 244,200 | 113.60～132.45 | 121.35 |
| 2002 | 238,609 | 115.54～135.01 | 124.49 |
| 2003 | 330,526 | 106.80～121.89 | 115.82 |
| 2004 | 321,941 | 101.91～114.89 | 108.18 |
| 2005 | 312,962 | 101.73～121.42 | 110.13 |
| 2006 | 315,950 | 109.06～119.90 | 116.25 |
| 2007 | 322,788 | 107.26～124.17 | 117.61 |
| 2008 | 348,226 | 87.20～111.95 | 103.37 |
| 2009 | 343,456 | 84.91～101.46 | 93.66 |
| 2010 | 328,836 | 80.24～94.98 | 87.83 |
| 2011 | 300,286 | 75.61～85.51 | 79.79 |
| 2012 | 365,686 | 76.03～86.77 | 79.78 |
| 2013 | 370,808 | 86.53～105.40 | 97.58 |
| 2014 | 366,741 | 100.78～121.84 | 105.86 |
| 2015 | 371,891 | 115.87～125.81 | 121.04 |
| 2016 | 372,417 | 99.18～121.66 | 108.69 |
| 2017 | 371,298 | 107.32～118.59 | 112.15 |
| 2018 | 371,800 | 104.64～114.54 | 110.44 |
| 2019 | 372,525 | 104.51～112.40 | 109.01 |
| 2020 | 372,140 | 101.20～112.19 | 106.76 |
| 2021 | 369,333 | 102.60～115.50 | 109.87 |
| 2022 | 372,536 | 113.49～151.93 | 131.51 |
| 2023 | 322,896 | 127.25～151.90 | 141.04 |
| 2024 | 372,023 | 139.59～161.95 | 151.46 |
| 2025 | 325,289 | 139.90～158.84 | 148.69 |

## Usage Examples

### Python

```python
import sqlite3
import pandas as pd

# Connect to database
conn = sqlite3.connect('USDJPY.db')

# Fetch data for specific period
query = """
SELECT * FROM tb1m
WHERE datetime BETWEEN '2024-01-01' AND '2024-12-31'
ORDER BY datetime
"""
df = pd.read_sql_query(query, conn)

# Aggregate to daily OHLC
daily = df.groupby(df['datetime'].str[:10]).agg({
    'open': 'first',
    'high': 'max',
    'low': 'min',
    'close': 'last'
})

conn.close()
```

### SQLite CLI

```bash
sqlite3 USDJPY.db
```

```sql
# Get data for specific date
SELECT * FROM tb1m
WHERE date(datetime) = '2024-06-15'
LIMIT 10;

# Calculate moving average
SELECT datetime, close,
       AVG(close) OVER (
           ORDER BY datetime
           ROWS BETWEEN 19 PRECEDING AND CURRENT ROW
       ) as sma_20
FROM tb1m
WHERE datetime >= '2024-01-01'
LIMIT 100;
```

### R

```r
library(RSQLite)
library(DBI)

# Connect to database
con <- dbConnect(RSQLite::SQLite(), "USDJPY.db")

# Fetch data
query <- "SELECT * FROM tb1m WHERE datetime >= '2024-01-01' LIMIT 10000"
df <- dbGetQuery(con, query)

dbDisconnect(con)
```

## Common Queries

### Date Range Query

```sql
SELECT * FROM tb1m
WHERE datetime BETWEEN '2024-01-01' AND '2024-12-31'
LIMIT 1000;
```

### Single Day Query

```sql
SELECT * FROM tb1m
WHERE date(datetime) = '2024-06-15';
```

### Daily OHLC Aggregation

```sql
SELECT
    date(datetime) as date,
    (SELECT open FROM tb1m t2
     WHERE date(t2.datetime) = date(t1.datetime)
     ORDER BY datetime LIMIT 1) as day_open,
    MAX(high) as day_high,
    MIN(low) as day_low,
    (SELECT close FROM tb1m t2
     WHERE date(t2.datetime) = date(t1.datetime)
     ORDER BY datetime DESC LIMIT 1) as day_close
FROM tb1m t1
GROUP BY date(datetime)
ORDER BY date;
```

### Volatility Analysis

```sql
SELECT
    date(datetime) as date,
    AVG((high - low) / close * 100) as avg_volatility_pct,
    MAX((high - low) / close * 100) as max_volatility_pct
FROM tb1m
GROUP BY date(datetime)
ORDER BY date;
```

## Performance

Optimized indexes enable fast queries even on large datasets.

| Query Type | Execution Time | Notes |
|------------|----------------|-------|
| Date range search | ~2ms | Using idx_datetime |
| Specific date | <1ms | Using idx_datetime |
| Full count | ~250ms | Using covering index |

Designed to handle 1000+ concurrent accesses.

## System Requirements

- Minimum disk space: 1.6 GB (including workspace)
- Recommended memory: 8GB or more
- SQLite: 3.x or higher
- Python: 3.7 or higher (when using pandas)

## Best Practices

- Read-only usage is recommended
- Use `LIMIT` clause when handling large amounts of data
- The `datetime` column is indexed for fast date searches
- For time-series data, utilize `ORDER BY datetime`
- Use `pd.read_sql_query()` when loading with Pandas

## Use Cases

- Machine learning model training and validation
- Statistical arbitrage strategy development
- Time-series forecasting model construction
- Technical analysis indicator validation
- Market microstructure research
- High-frequency trading algorithm backtesting

## License

Free for personal learning, research, and educational purposes.

Separate license agreement required for commercial use.

See [LICENSE](LICENSE) file for details.

## Data Source

This dataset is independently collected, processed, and optimized based on publicly available market data.

## Changelog

- 2025-11-17: Initial release

## Contributing

Issues and Pull Requests are welcome.

For questions or issues, please use [GitHub Issues](https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/issues).

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed contribution guidelines.


## Download

### Get the Database File

The database file `USDJPY.db` (1.05 GB) is available via GitHub Releases:

1. Go to [Releases](https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/releases)
2. Download `USDJPY.db` from the latest release
3. Place the file in your working directory

**Alternative: Using Git LFS**

If using Git LFS for large file management:

```bash
git lfs install
git clone https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset.git
cd forex-usdjpy-1min-ohlc-dataset
```

**Using wget or curl**

```bash
# Replace with actual release URL
wget https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/releases/download/v1.0/USDJPY.db

# Or using curl
curl -L -O https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/releases/download/v1.0/USDJPY.db
```


---

# 高品質なUSD/JPY為替レートの1分足OHLCデータベース（約25年分、850万件超）

このデータベースは、2001年1月から2025年11月までの約25年間にわたるUSD/JPY為替レートの1分足データを格納したSQLiteデータベースです。機械学習、統計分析、アルゴリズムトレーディング研究など、幅広い用途に最適化されています。

## ダウンロード

### データベースファイルの取得

データベースファイル `USDJPY.db` (1.05 GB) はGitHub Releasesから入手可能です：

1. [Releases](https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/releases)ページにアクセス
2. 最新リリースから`USDJPY.db`をダウンロード
3. 作業ディレクトリに配置

**代替方法: Git LFSを使用**

Git LFSで大容量ファイルを管理する場合：

```bash
git lfs install
git clone https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset.git
cd forex-usdjpy-1min-ohlc-dataset
```

**wgetまたはcurlを使用**

```bash
# 実際のリリースURLに置き換えてください
wget https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/releases/download/v1.0/USDJPY.db

# またはcurlを使用
curl -L -O https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/releases/download/v1.0/USDJPY.db
```

## データベース仕様

| 項目 | 詳細 |
|------|------|
| ファイル名 | USDJPY.db |
| ファイルサイズ | 1.05 GB |
| フォーマット | SQLite 3.x |
| 総レコード数 | 8,505,163件 |
| データ期間 | 2001-01-02 04:10:00 ～ 2025-11-14 16:58:00 |
| 期間長 | 約24.9年 (9,082日) |
| 通貨ペア | JPY=X (USD/JPY) |
| 時間軸 | 1分足 |

## スキーマ

| カラム名 | データ型 | 説明 | 制約 |
|----------|----------|------|------|
| symbol | TEXT | 通貨ペア識別子 | PRIMARY KEY, NOT NULL |
| datetime | TEXT | 日時（YYYY-MM-DD HH:MM:SS） | PRIMARY KEY, NOT NULL |
| open | REAL | 始値 | - |
| high | REAL | 高値 | - |
| low | REAL | 安値 | - |
| close | REAL | 終値 | - |

### インデックス

- `idx_datetime`: datetime列に対する単独インデックス（高速検索用）
- `sqlite_autoindex_tb1m_1`: (symbol, datetime)複合PRIMARY KEY

## データ統計

### 全体統計

| 指標 | Open | High | Low | Close |
|------|------|------|-----|-------|
| 最小値 | 75.61 | 75.61 | 75.56 | 75.61 |
| 最大値 | 161.95 | 161.95 | 161.94 | 161.95 |
| 平均値 | 112.30 | 112.31 | 112.29 | 112.30 |
| レンジ | 86.34 | 86.34 | 86.38 | 86.34 |

### 年別内訳

| 年 | レコード数 | Close範囲 | 平均価格 |
|----|------------|-----------|----------|
| 2001 | 244,200 | 113.60～132.45 | 121.35 |
| 2002 | 238,609 | 115.54～135.01 | 124.49 |
| 2003 | 330,526 | 106.80～121.89 | 115.82 |
| 2004 | 321,941 | 101.91～114.89 | 108.18 |
| 2005 | 312,962 | 101.73～121.42 | 110.13 |
| 2006 | 315,950 | 109.06～119.90 | 116.25 |
| 2007 | 322,788 | 107.26～124.17 | 117.61 |
| 2008 | 348,226 | 87.20～111.95 | 103.37 |
| 2009 | 343,456 | 84.91～101.46 | 93.66 |
| 2010 | 328,836 | 80.24～94.98 | 87.83 |
| 2011 | 300,286 | 75.61～85.51 | 79.79 |
| 2012 | 365,686 | 76.03～86.77 | 79.78 |
| 2013 | 370,808 | 86.53～105.40 | 97.58 |
| 2014 | 366,741 | 100.78～121.84 | 105.86 |
| 2015 | 371,891 | 115.87～125.81 | 121.04 |
| 2016 | 372,417 | 99.18～121.66 | 108.69 |
| 2017 | 371,298 | 107.32～118.59 | 112.15 |
| 2018 | 371,800 | 104.64～114.54 | 110.44 |
| 2019 | 372,525 | 104.51～112.40 | 109.01 |
| 2020 | 372,140 | 101.20～112.19 | 106.76 |
| 2021 | 369,333 | 102.60～115.50 | 109.87 |
| 2022 | 372,536 | 113.49～151.93 | 131.51 |
| 2023 | 322,896 | 127.25～151.90 | 141.04 |
| 2024 | 372,023 | 139.59～161.95 | 151.46 |
| 2025 | 325,289 | 139.90～158.84 | 148.69 |

## 使用例

### Python

```python
import sqlite3
import pandas as pd

# データベース接続
conn = sqlite3.connect('USDJPY.db')

# 特定期間のデータ取得
query = """
SELECT * FROM tb1m
WHERE datetime BETWEEN '2024-01-01' AND '2024-12-31'
ORDER BY datetime
"""
df = pd.read_sql_query(query, conn)

# 日次OHLCに集計
daily = df.groupby(df['datetime'].str[:10]).agg({
    'open': 'first',
    'high': 'max',
    'low': 'min',
    'close': 'last'
})

conn.close()
```

### SQLite CLI

```bash
sqlite3 USDJPY.db
```

```sql
# 特定日のデータ取得
SELECT * FROM tb1m
WHERE date(datetime) = '2024-06-15'
LIMIT 10;

# 移動平均の計算
SELECT datetime, close,
       AVG(close) OVER (
           ORDER BY datetime
           ROWS BETWEEN 19 PRECEDING AND CURRENT ROW
       ) as sma_20
FROM tb1m
WHERE datetime >= '2024-01-01'
LIMIT 100;
```

### R

```r
library(RSQLite)
library(DBI)

# データベース接続
con <- dbConnect(RSQLite::SQLite(), "USDJPY.db")

# データ取得
query <- "SELECT * FROM tb1m WHERE datetime >= '2024-01-01' LIMIT 10000"
df <- dbGetQuery(con, query)

dbDisconnect(con)
```

## 一般的なクエリ

### 日時範囲クエリ

```sql
SELECT * FROM tb1m
WHERE datetime BETWEEN '2024-01-01' AND '2024-12-31'
LIMIT 1000;
```

### 特定日クエリ

```sql
SELECT * FROM tb1m
WHERE date(datetime) = '2024-06-15';
```

### 日次OHLC集計

```sql
SELECT
    date(datetime) as date,
    (SELECT open FROM tb1m t2
     WHERE date(t2.datetime) = date(t1.datetime)
     ORDER BY datetime LIMIT 1) as day_open,
    MAX(high) as day_high,
    MIN(low) as day_low,
    (SELECT close FROM tb1m t2
     WHERE date(t2.datetime) = date(t1.datetime)
     ORDER BY datetime DESC LIMIT 1) as day_close
FROM tb1m t1
GROUP BY date(datetime)
ORDER BY date;
```

### ボラティリティ分析

```sql
SELECT
    date(datetime) as date,
    AVG((high - low) / close * 100) as avg_volatility_pct,
    MAX((high - low) / close * 100) as max_volatility_pct
FROM tb1m
GROUP BY date(datetime)
ORDER BY date;
```

## パフォーマンス

インデックス最適化により、大規模データセットでも高速なクエリを実現しています。

| クエリタイプ | 実行時間 | 備考 |
|--------------|----------|------|
| 日時範囲検索 | ~2ms | idx_datetime使用 |
| 特定日検索 | <1ms | idx_datetime使用 |
| 全件カウント | ~250ms | カバリングインデックス使用 |

1000人の同時アクセスにも対応可能な設計です。

## システム要件

- 最小ディスク容量: 1.6 GB（作業用領域含む）
- 推奨メモリ: 8GB以上
- SQLite: 3.x以上
- Python: 3.7以上（pandas使用時）

## ベストプラクティス

- データベースは読み取り専用での使用を推奨します
- 大量データを扱う際は`LIMIT`句を使用してください
- `datetime`列にインデックスが設定されているため、日時検索は高速です
- 時系列データのため、`ORDER BY datetime`を活用してください
- Pandasで読み込む場合は`pd.read_sql_query()`を使用してください

## 利用用途

- 機械学習モデルの学習・検証
- 統計的アービトラージ戦略の開発
- 時系列予測モデルの構築
- テクニカル分析指標の検証
- マーケットマイクロストラクチャー研究
- 高頻度取引アルゴリズムのバックテスト

## ライセンス

個人学習・研究・教育目的での利用は無料です。

商用利用をご希望の場合は別途ライセンス契約が必要です。

詳細は[LICENSE](LICENSE)ファイルをご確認ください。

## データソース

本データセットは公開されている市場データを基に、独自に収集・処理・最適化を行ったものです。

## 変更履歴

- 2025-11-17: 初回リリース

## コントリビューション

Issues、Pull Requestsを歓迎します。

## ダウンロード

### データベースファイルの取得

データベースファイル `USDJPY.db` (1.05 GB) はGitHub Releasesから入手可能です：

1. [Releases](https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/releases)ページにアクセス
2. 最新リリースから`USDJPY.db`をダウンロード
3. 作業ディレクトリに配置

**代替方法: Git LFSを使用**

Git LFSで大容量ファイルを管理する場合：

```bash
git lfs install
git clone https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset.git
cd forex-usdjpy-1min-ohlc-dataset
```

**wgetまたはcurlを使用**

```bash
# 実際のリリースURLに置き換えてください
wget https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/releases/download/v1.0/USDJPY.db

# またはcurlを使用
curl -L -O https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/releases/download/v1.0/USDJPY.db
```



質問や問題がある場合は、[GitHub Issues](https://github.com/k31ww475/forex-usdjpy-1min-ohlc-dataset/issues)をご利用ください。

詳細なコントリビューションガイドラインについては[CONTRIBUTING.md](CONTRIBUTING.md)をご覧ください。
