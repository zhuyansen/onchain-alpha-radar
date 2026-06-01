# Daily Alpha Scanner Briefing

> **Date**: {{DATE}} | **Chains**: {{CHAINS}} | **Risk Tolerance**: {{RISK_TOLERANCE}}
> **Data Completeness**: {{DATA_COMPLETENESS}}

---

## Executive Summary

{{EXECUTIVE_SUMMARY}}

---

## 1. Hot Tokens by Narrative

### AI / Agent
| Token | Chain | Price | 24h% | MCap | Liquidity | Holders | Top10% | Score |
|-------|-------|-------|------|------|-----------|---------|--------|-------|
{{AI_TOKENS_ROWS}}

### Meme
| Token | Chain | Price | 24h% | MCap | Liquidity | Holders | Top10% | Score |
|-------|-------|-------|------|------|-----------|---------|--------|-------|
{{MEME_TOKENS_ROWS}}

### DeFi
| Token | Chain | Price | 24h% | MCap | Liquidity | Holders | Top10% | Score |
|-------|-------|-------|------|------|-----------|---------|--------|-------|
{{DEFI_TOKENS_ROWS}}

### Other Narratives
| Token | Chain | Narrative | Price | 24h% | MCap | Score |
|-------|-------|-----------|-------|------|------|-------|
{{OTHER_TOKENS_ROWS}}

---

## 2. Smart Money Signals

### Strongest Signals (sorted by wallet count)
| Token | Chain | MCap | # Wallets | Buy Amount | Sold% | Signal |
|-------|-------|------|-----------|-----------|-------|--------|
{{SMART_MONEY_ROWS}}

### Cross-Reference Hits
Tokens appearing in BOTH trending AND smart money:
{{CROSS_REFERENCE_LIST}}

---

## 3. Social Sentiment

> **Data Sources**: {{SOCIAL_SOURCES_ACTIVE}}
> **Coverage**: {{SOCIAL_TOKEN_COUNT}} tokens scanned

### Sentiment Overview
| Token | News Score | Reddit Score | Twitter Score | Social Score | Signal Flag |
|-------|-----------|-------------|--------------|-------------|-------------|
{{SOCIAL_SENTIMENT_ROWS}}

### Key Social Findings
{{SOCIAL_KEY_FINDINGS}}

### Notable Signals
- 🔥 VIRAL: {{VIRAL_TOKENS}}
- 🤫 SILENT ALPHA: {{SILENT_ALPHA_TOKENS}}
- ⚠️ HYPE RISK: {{HYPE_RISK_TOKENS}}

---

## 4. Meme Coin Scan

### New Launches Worth Watching
| Token | Chain | MCap | Bonding% | Holders | Sniper% | Bundle% | Dev History | Social |
|-------|-------|------|----------|---------|---------|---------|-------------|--------|
{{MEME_SCAN_ROWS}}

### Developer Reputation
| Token | Dev Address | Tokens Created | Rug Pulls | Launched | Verdict |
|-------|-------------|---------------|-----------|----------|---------|
{{DEV_REPUTATION_ROWS}}

---

## 5. Security Audit

### Batch Scan Results
| Token | Chain | Risk Level | Honeypot | Mintable | Fake LP | Dumping | Wash |
|-------|-------|-----------|----------|---------|---------|---------|------|
{{SECURITY_SCAN_ROWS}}

### Advanced Risk Analysis
| Token | Dev Tokens | Rug History | LP Burned% | Sniper% | Bundle% | Tags |
|-------|-----------|-------------|-----------|---------|---------|------|
{{ADVANCED_RISK_ROWS}}

---

## 6. Historical Validation (Backtesting)

> **Data Source**: DexPaprika OHLCV API
> **Coverage**: {{BACKTEST_TOKEN_COUNT}} tokens backtested

### Price & Volume Summary (30d)
| Token | 7d Change | 30d Change | From ATH | From ATL | Daily Vol | Vol Trend | Backtest Score |
|-------|----------|-----------|---------|---------|-----------|-----------|---------------|
{{BACKTEST_SUMMARY_ROWS}}

### Entry Timing Analysis
| Token | Est. SM Entry | Current Price | SM Gain/Loss | Entry Zone | Signal |
|-------|--------------|--------------|-------------|-----------|--------|
{{ENTRY_TIMING_ROWS}}

### Backtest Signals
- 📈 UPTREND: {{UPTREND_TOKENS}}
- 💎 DIAMOND ENTRY: {{DIAMOND_ENTRY_TOKENS}}
- 🔄 CONSOLIDATION: {{CONSOLIDATION_TOKENS}}
- 📉 DOWNTREND: {{DOWNTREND_TOKENS}}
- 🏔️ NEAR ATH: {{NEAR_ATH_TOKENS}}

---

## 7. Final Recommendations

### BUY (High Conviction)
| Token | Chain | MCap | Score | Why |
|-------|-------|------|-------|-----|
{{BUY_ROWS}}

### WATCH (Monitor)
| Token | Chain | MCap | Score | Why |
|-------|-------|------|-------|-----|
{{WATCH_ROWS}}

### AVOID
| Token | Chain | Score | Why |
|-------|-------|-------|-----|
{{AVOID_ROWS}}

---

## Key Metrics Glossary

- **Top10%**: percentage of supply held by top 10 wallets (lower = more distributed)
- **Sold%**: how much smart money has already sold (lower = higher conviction)
- **Bundle%**: percentage held by bundled/coordinated wallets (lower = more organic)
- **Sniper%**: percentage held by sniper bots at launch (lower = fairer distribution)
- **LP Burned%**: percentage of liquidity pool tokens burned (higher = safer, cannot rug LP)
- **CTO**: Community Take Over — dev left, community runs it
- **Social Score**: combined sentiment from crypto news + Reddit + Twitter (0-100)
- **Backtest Score**: historical price + volume validation via DexPaprika OHLCV (0-100)
- **Score**: composite score 0-100 based on 7 dimensions: narrative + momentum + smart money + social + security + backtest + liquidity
- 🔥 **VIRAL**: socialScore > 80, strong positive buzz across multiple platforms
- 🤫 **SILENT ALPHA**: smart money buying but zero social buzz — potential early signal
- ⚠️ **HYPE RISK**: high social buzz but smart money already selling — possible exit liquidity
- 📈 **UPTREND**: 7d change > +15% with expanding volume — momentum confirmed
- 💎 **DIAMOND ENTRY**: price near 30d low + volume expanding — potential reversal
- 📉 **DOWNTREND**: 7d change < -20% with declining volume — avoid
- 🏔️ **NEAR ATH**: price within 10% of 30d high — late entry risk

---

*Generated by Daily Alpha Scanner v3.0 | Data sources: OKX OnchainOS API, opennews, Reddit, xreach, DexPaprika*
*This is not financial advice. All crypto investments carry extreme risk. DYOR.*
