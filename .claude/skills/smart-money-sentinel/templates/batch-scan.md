# 📊 Smart Money Sentinel — Batch Scan

> **Date**: {{DATE}} | **Chain**: {{CHAIN_NAME}} | **Signals Scanned**: {{TOTAL_SIGNALS}}

---

## Signal Overview

| # | Token | Direction | SM Count | Max Gain | Exit Rate | Status | News | Signal Score |
|---|-------|-----------|----------|----------|-----------|--------|------|-------------|
{{SIGNAL_TABLE_ROWS}}

---

## Legend

| Column | Description |
|--------|-------------|
| SM Count | Number of smart money wallets involved |
| Max Gain | Maximum price gain since signal (%) |
| Exit Rate | Smart money exit percentage (%) |
| Status | active / timeout / completed |
| News | 🟢 Bullish / 🟡 Neutral / 🔴 Bearish / ⚪ N/A |
| Signal Score | Phase 1 signal strength score (/25) |

## Top Picks (Score ≥ 15/25 + Bullish News)

{{TOP_PICKS_SUMMARY}}

---

## Recommended Next Steps

For tokens with high signal scores and bullish news:
→ Run full sentinel analysis: `验证 $TOKEN 信号`

---

*Data sources: Binance Web3 API, OpenNews (6551.io)*
*Batch scan by Smart Money Sentinel on {{DATE}}*
*⚠️ This is not financial advice. DYOR.*
