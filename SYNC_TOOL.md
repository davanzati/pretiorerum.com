# Pretio Rerum Sync Tool - Complete Reference

All commands run from `/Users/jjohnson/projects/pretio-rerum/`:

```bash
go run ./cmd/sync [flags]
```

---

## Quick Start

```bash
# Just show prices (no updates)
go run ./cmd/sync --prices

# Full sync to Google Sheets
go run ./cmd/sync --extended

# Update site with weekly changes
go run ./cmd/sync --historical --range=5d
```

---

## Price & Market Data

| Flag | Description | API | Output |
|------|-------------|-----|--------|
| `--prices` | Fetch and display all prices | Yahoo Finance | Gold, Silver, 84 ETFs, indices, forex |
| `--forex` | Fetch 42 forex pairs + 30 crypto | Yahoo Finance | DXY, EUR/USD, BTC, ETH, etc. |
| `--commodities` | Commodity prices and ratios | Yahoo Finance | Gold/Oil, Copper/Gold, Oil/NatGas |
| `--vix` | VIX term structure | Yahoo Finance | VIX, VIX3M, contango/backwardation |
| `--put-call` | Put/call ratio | Yahoo Finance | Market sentiment indicator |

### Example: Get current prices
```bash
go run ./cmd/sync --prices
# Output:
#   Gold:      $4,879.80
#   Silver:    $87.34
#   G/S Ratio: 55.87
```

---

## Sentiment & Positioning

| Flag | Description | API | Output |
|------|-------------|-----|--------|
| `--fear-greed` | CNN Fear & Greed Index | CNN | 0-100 scale, sentiment label |
| `--cot` | CFTC Commitment of Traders | CFTC | Spec/Commercial positioning by contract |
| `--holdings` | ETF metal holdings | Yahoo Finance | GLD, SLV, PSLV tonnage |
| `--miners` | Miner/physical ratios | Yahoo Finance | GDX/GLD, SIL/SLV valuation |

### Example: Fear & Greed
```bash
go run ./cmd/sync --fear-greed
# Output:
#   Current: 14/100 (Extreme Fear)
#   1 Week Ago: 20 (Extreme Fear)
#   Signal: Contrarian bullish - consider buying
```

### Example: COT Positioning
```bash
go run ./cmd/sync --cot
# Output:
#   Gold:   Specs +42% OI (EXTREME LONG - contrarian bearish)
#   Silver: Specs +15% OI (neutral)
#   Copper: Specs +17% OI (neutral)
```

### Example: ETF Holdings
```bash
go run ./cmd/sync --holdings
# Output:
#   Gold ETFs:   2,418 tonnes (GLD + IAU + PHYS)
#   Silver ETFs: 20,708 tonnes (SLV + SIVR + PSLV)
```

---

## Key Ratios (Priced in Gold)

| Flag | Description | API | Output |
|------|-------------|-----|--------|
| `--ratios` | Assets priced in gold/silver | Yahoo Finance | Dow/Gold, S&P/Gold, Home/Gold |

### Example: Everything in Gold
```bash
go run ./cmd/sync --ratios
# Output:
#   Dow Jones:   10.0 oz of gold
#   S&P 500:     1.4 oz of gold
#   Median Home: 86.2 oz of gold
#
#   Dow Jones:   560 oz of silver
#   S&P 500:     79 oz of silver
#
#   Historical: Dow/Gold hit 1:1 in 1980 (both ~800)
```

---

## Treasury & Debt

| Flag | Description | API | Output |
|------|-------------|-----|--------|
| `--treasury` | National debt data | Treasury Direct | Total, daily change, per citizen |
| `--debt-history=N` | N days of debt history | Treasury Direct | Time series |
| `--auctions` | Treasury auction results | Treasury Direct | Bid-to-cover ratios |

### Example: US Debt
```bash
go run ./cmd/sync --treasury
# Output:
#   Total Outstanding: $38.569 T
#   Daily Change:      $25.96 B
#   Annualized Rate:   $9.47 T/year
#   Per Citizen:       $115,129.85
```

---

## Macro Economic Data (Requires FRED_API_KEY)

| Flag | Description | API | Output |
|------|-------------|-----|--------|
| `--real-rates` | Gold vs 10Y TIPS | FRED | Real rate, breakeven inflation |
| `--yield-curve` | Treasury yield curve | FRED | 2s10s spread, inversion signal |
| `--fed-balance` | Fed balance sheet | FRED | ~$7T total assets |
| `--m2` | M2 money supply | FRED | ~$21T, YoY change |
| `--spreads` | Credit spreads | FRED | IG, HY spreads (risk indicator) |
| `--inflation` | Inflation expectations | FRED | 5Y5Y forward, breakevens |
| `--recession` | Recession indicators | FRED | LEI, yield curve, unemployment |
| `--bls` | BLS employment/CPI data | BLS | NFP, CPI components |

### Setup FRED API Key
```bash
# Get free key at: https://fred.stlouisfed.org/docs/api/api_key.html
export FRED_API_KEY=your_key_here
```

### Example: Real Rates
```bash
go run ./cmd/sync --real-rates
# Output:
#   Gold Price:          $4,875
#   10Y Treasury:        4.50%
#   10Y TIPS (Real):     2.10%
#   Breakeven Inflation: 2.40%
#   Signal: Positive real rates = headwind for gold
```

---

## Analysis Tools

| Flag | Description | API | Output |
|------|-------------|-----|--------|
| `--analyze` | YoY analysis on key symbols | Yahoo Finance | Highs, lows, validation |
| `--real-returns` | All 88 sectors vs gold/silver | Yahoo Finance | Real purchasing power |
| `--thesis` | 21-signal collapse tracker | Multiple | Signal status dashboard |
| `--correlations` | Asset correlation matrix | Yahoo Finance | Cross-asset correlations |
| `--strength` | Relative strength rankings | Yahoo Finance | Momentum leaders |
| `--currencies` | Currency strength rankings | Yahoo Finance | Strong/weak currencies |
| `--breadth` | Market breadth indicators | Yahoo Finance | Advance/decline, new highs |
| `--technicals` | RSI/MACD dashboard | Yahoo Finance | Overbought/oversold |
| `--volatility` | ATR, realized volatility | Yahoo Finance | Volatility analysis |
| `--seasonality=SYM` | Seasonal patterns | Yahoo Finance | Monthly tendencies |

### Example: Real Returns (The Killer Feature)
```bash
go run ./cmd/sync --real-returns
# Shows how every sector performs vs gold/silver
# Output:
#   XLE (Energy):     +12% nominal, -35% in gold, -52% in silver
#   QQQ (Tech):       +19% nominal, -28% in gold, -47% in silver
#   GDX (Gold Miners): +45% nominal, -15% in gold, -38% in silver
```

### Example: Thesis Tracker
```bash
go run ./cmd/sync --thesis
# Output:
#   21/21 COLLAPSE SIGNALS TRIGGERED
#   ✓ DXY < 100 (currently 97)
#   ✓ Gold +68% YoY
#   ✓ Silver +144% YoY
#   ✓ Debt/GDP > 100% (currently 125%)
#   ... etc
```

---

## Historical Data

| Flag | Description | Range |
|------|-------------|-------|
| `--historical` | OHLCV for all symbols | Default 1Y |
| `--range=X` | Set range: 1d,5d,1mo,3mo,6mo,1y,2y,5y,10y,ytd,max | - |
| `--interval=X` | Set interval: 1m,5m,15m,1h,1d,1wk,1mo | Default 1d |
| `--save` | Save to CSV in data/historical/ | - |

### Historical Flags
| Flag | Description |
|------|-------------|
| `--fear-greed-history=N` | N days of Fear & Greed |
| `--miners-history` | 1Y of GDX/GLD ratio |
| `--ratios-history` | 1Y of Dow/Gold ratio |
| `--commodity-history` | 1Y of Gold/Oil ratio |
| `--spreads-history` | 1Y of credit spreads |
| `--real-rates-history` | 1Y of real rates |
| `--yield-history` | 1Y of 2s10s spread |
| `--m2-history` | 5Y of M2 money supply |
| `--fed-balance-history` | 5Y of Fed balance sheet |
| `--thesis-history` | Signal triggers over time |

### Example: Weekly Changes
```bash
go run ./cmd/sync --historical --range=5d
# Output:
#   BTC-USD: $84,562 → $77,782 (-8.0%)
#   ETH-USD: $2,818 → $2,307 (-18.2%)
#   GC=F:    $4,850 → $4,880 (+0.6%)
```

### Example: Save Historical Data
```bash
go run ./cmd/sync --historical --range=1y --save
# Saves CSV files to data/historical/
```

---

## Output Generation

| Flag | Description | Output |
|------|-------------|--------|
| `--js=SYM` | Export symbol to JS array | For charts |
| `--crash-data=PATH` | Generate crash event data | JS file |
| `--etf-data-js=PATH` | Generate ETF data | JS file |
| `--dry-run` | Preview without updating sheets | Console only |

### Example: Generate Chart Data
```bash
go run ./cmd/sync --js=GC=F
# Output: JavaScript array for Chart.js
```

---

## Combined Commands

| Flag | Description |
|------|-------------|
| `--extended` | Fetch ALL extended data |
| `--macro` | All macro data (spreads, rates, fed, auctions) |

### Example: Full Update
```bash
go run ./cmd/sync --extended
# Fetches everything and updates Google Sheets
```

---

## Site Update Workflow

```bash
# 1. Navigate to project root
cd /Users/jjohnson/projects/pretio-rerum

# 2. Fetch all data for site
go run ./cmd/sync --prices --forex --fear-greed --ratios \
  --commodities --miners --cot --holdings --treasury

# 3. Get weekly changes
go run ./cmd/sync --historical --range=5d

# 4. Update site/index.html with new values

# 5. Commit and push
cd site
git add index.html
git commit -m "Update prices $(date +%Y-%m-%d)"
git push
```

---

## Data Sources

| Source | API Key Required | Data |
|--------|------------------|------|
| Yahoo Finance | No | ETFs, stocks, futures, forex, crypto, indices |
| Treasury Direct | No | National debt, auctions, interest rates |
| CFTC | No | Commitment of Traders (COT) |
| CNN | No | Fear & Greed Index |
| FRED | Yes (free) | Macro data, Fed balance, M2, yields, spreads |
| BLS | Optional (free) | CPI components, employment detail |
| Google Sheets | Service Account | Read/write to spreadsheets |

---

## Troubleshooting

```bash
# If FRED data fails:
export FRED_API_KEY=your_key_here

# If Google Sheets fails:
# Check credentials.json in scripts/

# If Yahoo Finance rate limits:
# Wait a few minutes, or reduce concurrent requests
```
