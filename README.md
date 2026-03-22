# SEC EDGAR Fundamental Analysis with Valuation & Recommendation

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A Python tool that downloads financial data directly from the SEC EDGAR database, calculates key metrics, performs a Discounted Cash Flow (DCF) valuation, and compares the intrinsic value with the current market price to produce a **Buy/Sell/Hold** recommendation.

---

## Overview

This project automates the process of:
- Retrieving a company's CIK (Central Index Key) using the SEC’s official mapping.
- Fetching financial facts (income statement, balance sheet, cash flow) from the SEC’s XBRL API.
- Computing free cash flow (FCF) and other essential metrics.
- Estimating a historical growth rate from past FCF.
- Approximating the Weighted Average Cost of Capital (WACC) using industry‑standard parameters.
- Performing a two‑stage DCF valuation to obtain an intrinsic value per share.
- Comparing the intrinsic value with the current stock price (from Yahoo Finance) and issuing a recommendation based on a 20% margin of safety.

Ideal for equity researchers, investors, and anyone wanting to quickly assess a company’s valuation using official SEC data.

---

## Features

- **Automatic CIK lookup** – No manual mapping; fetches ticker‑to‑CIK from the SEC.
- **Real‑time SEC data** – Direct access to the SEC EDGAR XBRL API (latest 10‑K filings).
- **Comprehensive financial extraction** – Handles multiple US‑GAAP fact names (revenue, net income, operating cash flow, capital expenditures, debt, shares outstanding).
- **DCF Valuation** – Projects FCF, estimates WACC, and computes intrinsic value per share.
- **Market price integration** – Fetches current price from Yahoo Finance.
- **Actionable recommendation** – BUY (20%+ upside), SELL (20%+ downside), or HOLD.

---

## Data Sources

- **SEC EDGAR** – Company facts via the `companyfacts` endpoint (no API key needed, but a `User-Agent` header is required).
- **Yahoo Finance** – Current stock price via `yfinance`.
- **SEC Company Tickers** – `company_tickers.json` for CIK lookup.

All data is free and publicly available.

---

## Methodology

### 1. CIK Lookup
The script downloads the SEC’s `company_tickers.json` file, mapping ticker symbols to CIK numbers. The CIK is then used to query the company’s financial facts.

### 2. Financial Data Extraction
From the returned JSON, we extract:
- Revenue, Net Income
- Operating Cash Flow, Capital Expenditures
- Total Assets, Total Liabilities
- Long‑term and Short‑term Debt
- Shares Outstanding (tries multiple fact names)

### 3. Discounted Cash Flow (DCF) Valuation

- **Free Cash Flow (FCF)** = Operating Cash Flow – Capital Expenditures
- **Historical growth rate** – CAGR of FCF over the last 5 years (or default 3% if insufficient data)
- **WACC** – Approximated using:
  - Risk‑free rate = 3%
  - Market risk premium = 5%
  - Beta = 1.0
  - Tax rate = 21%
  - Debt ratio = 20%
  - Cost of debt = 5%
- **Explicit forecast** – 5 years at the estimated growth rate
- **Terminal value** – Perpetual growth of 2% after explicit period
- **Enterprise Value** = PV(projected FCF) + PV(terminal value)
- **Equity Value** = Enterprise Value – Net Debt (using total debt as a proxy; cash is ignored for simplicity)
- **Intrinsic Price** = Equity Value / Shares Outstanding

### 4. Recommendation
- **BUY** if intrinsic price > current price by 20% or more.
- **SELL** if intrinsic price < current price by 20% or more.
- **HOLD** otherwise.

---
