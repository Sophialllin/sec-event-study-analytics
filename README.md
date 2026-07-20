# SEC Event Study Analytics System
**Extensible Big Data Pipeline for Market Reaction Analysis Around SEC Filings**  

---

## Overview

An open-source, end-to-end event study platform that automates market reaction analysis around SEC filings (10-K, 10-Q, 8-K) for technology sector companies — replacing expensive tools like Bloomberg ($25K/year) with a free, fully customizable pipeline.

Built as a **modular, extensible system**: the analytics layer is fully configurable, meaning the same pipeline can power a wide range of investment research applications beyond the default AR/CAR computation. All key parameters (ticker, date range, event type, window length, keyword) are query-time inputs — **no code changes needed** to re-run on any time period, company, or market regime.

---

## System Architecture

```
SEC EDGAR REST API ──┐
                     ├──► ETL Pipeline ──► PostgreSQL  (structured: prices, events)
Yahoo Finance ────────┘         │         MongoDB      (unstructured: 65K+ filing docs)
                                │
                          Flask REST API
                                │
                     Interactive Dashboard
                  (Ticker / Date / EventType / Window / Keyword)
```

---

## My Contributions

I owned the full **data engineering architecture** of this system:

- Designed the dual-database schema (PostgreSQL + MongoDB) and CIK↔Ticker mapping logic
- Built the SEC EDGAR REST API ingestion pipeline (10-K, 10-Q, 8-K metadata + raw filing text)
- Built the Yahoo Finance market data pipeline via `yfinance` for 927 companies × 10 years
- Designed ETL transformation logic: CIK zero-padding, SIC code normalization, date standardization, OHLC type conversion, event table generation bridging filings ↔ prices
- Established Flask API layer connecting analytics output to the interactive dashboard

---

## Notebooks

| Notebook | Description |
|---|---|
| `01_database_schema_market_data.ipynb` | PostgreSQL schema design, company table, Yahoo Finance price ingestion |
| `02_sec_edgar_filings_ingestion.ipynb` | SEC EDGAR API pipeline, MongoDB ingestion, raw text extraction |
| `03_analytics_engine_dashboard.ipynb` | Event table, AR/CAR/Volatility engine, Flask dashboard |

---

## Extensible Analytics Layer

The default implementation computes **AR / CAR / Volatility** per event window.  
Because the pipeline cleanly separates data engineering from analytics, the computation layer can be swapped to support:

| Use Case | What to Change | Investment Application |
|---|---|---|
| **Custom time horizon** | Change `FIXED_START` / `window` parameter | Any lookback/forward period — intraday to multi-year |
| Event-driven backtesting | Replace AR with hold-period return + signal | Strategy P&L attribution |
| Earnings surprise analysis | Replace AR with EPS surprise vs price jump | Alpha generation |
| Information leakage detection | Shift window to `[-10, -1]` pre-event | Compliance monitoring |
| Sentiment-price impact | Replace AR with FinBERT score → return regression | NLP-driven alpha |
| Volatility forecasting | Replace CAR with pre/post realized vol ratio | Options strategy |
| Alternative factor construction | Use filing frequency / text complexity as factor | Factor investing |
| Contagion / spillover analysis | Compute peer-company AR on same event date | Risk management |
| Risk attribution | Measure event contribution to portfolio VaR | Portfolio analytics |

---

## Key Statistics

| Metric | Value |
|---|---|
| Companies covered | 927 technology sector companies |
| Filing documents (MongoDB) | 65,440 documents |
| Total dataset size | 4.02 GB |
| Unstructured filing text | 3.85 GB (MongoDB) |
| Structured relational data | 170 MB (PostgreSQL) |
| Historical coverage | 10 years (2015–present) |
| Filing types | 10-K, 10-Q, 8-K |

---

## Tech Stack

`Python` · `PostgreSQL` · `MongoDB` · `Flask`  
`pandas` · `NumPy` · `yfinance` · `BeautifulSoup` · `SEC EDGAR API`

---

## Setup & Installation

### Prerequisites
- Python 3.9+
- PostgreSQL (local or cloud)
- MongoDB (local or cloud)

### Install dependencies
```bash
pip install -r requirements.txt
```

### Configure environment
```bash
cp .env.example .env
# Edit .env with your database credentials and SEC User-Agent
```

### Run the full pipeline
```bash
# Step 1: Set up schema and ingest market data
jupyter nbconvert --to notebook --execute 01_database_schema_market_data.ipynb

# Step 2: Ingest SEC filings into MongoDB
jupyter nbconvert --to notebook --execute 02_sec_edgar_filings_ingestion.ipynb

# Step 3: Build event table and launch dashboard
jupyter nbconvert --to notebook --execute 03_analytics_engine_dashboard.ipynb
# → Dashboard available at http://localhost:5000
```

---

## Data Sources

- **SEC EDGAR API** — Public domain. Requires `User-Agent` header with your name and email.  
  https://www.sec.gov/search-filings/edgar-application-programming-interfaces
- **Yahoo Finance** — via `yfinance` library, free for non-commercial/academic research use.  
  https://finance.yahoo.com/

---

## Disclaimer

Academic project completed at Columbia University for educational purposes only.  
Data used under non-commercial research license. Not intended for production trading use.
