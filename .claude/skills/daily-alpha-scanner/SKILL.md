---
name: daily-alpha-scanner
description: "One-click daily alpha scanner that runs a full 7-step on-chain research pipeline using OKX OnchainOS + social sentiment + historical backtesting: (1) Hot Token Discovery with narrative categorization, (2) Smart Money / KOL / Whale buy-signal tracking, (3) Social Sentiment Scan via crypto news (opennews), Reddit, and Twitter KOL tracking (xreach), (4) Meme Coin launchpad scanning with dev reputation and bundle/sniper detection, (5) Batch security audit (honeypot, mintable, fake LP, wash trading), (6) Historical Signal Validation via DexPaprika OHLCV — backtest what happened after past smart money entries, (7) Consolidated briefing with 7-dimension composite scoring (0-100) and BUY / WATCH / AVOID verdicts. Supports Solana, Base, Ethereum, BSC, Arbitrum. Use when the user wants a daily market scan, alpha discovery, token recommendations, or asks 'what to buy today'. Trigger keywords: daily scan, alpha scanner, today's alpha, what to buy, market scan, daily briefing, 每日扫描, 今日推荐, 扫链, 今天买什么, 市场扫描, 链上日报, 每日研报."
license: MIT
compatibility: "Requires onchainos CLI v3.1+ and OKX Web3 API key (OKX_API_KEY, OKX_SECRET_KEY, OKX_PASSPHRASE). Internet access required for on-chain data queries."
metadata:
  author: zhuyansen
  version: "3.0.0"
  homepage: "https://github.com/zhuyansen/daily-alpha-scanner"
---

# Daily Alpha Scanner

A systematic 7-step pipeline that scans on-chain data + social sentiment + historical backtesting across multiple chains and produces a consolidated briefing with purchase recommendations.

## Pipeline

```
Step 1: Hot Tokens  →  Step 2: Smart Money  →  Step 3: Social Sentiment
     ↓                      ↓                        ↓
Step 4: Meme Scan  →  Step 5: Security Audit (batch scan all candidates)
     ↓
Step 6: Signal Backtesting (DexPaprika OHLCV — historical validation)
     ↓
Step 7: Consolidated Briefing + Purchase Recommendations (7D scoring)
```

## Prerequisites

### Required: OKX OnchainOS
```bash
which onchainos && onchainos --version
```
If missing: `curl -sSL https://raw.githubusercontent.com/okx/onchainos-skills/main/install.sh | sh`

Required env vars: `OKX_API_KEY`, `OKX_SECRET_KEY`, `OKX_PASSPHRASE` (Web3 API key from https://web3.okx.com/onchainos/dev-portal)

### Social Sentiment Tools (graceful degradation — all optional, use what's available)

Check availability in order of priority:

```bash
# 1. opennews MCP — crypto news sentiment (best source, MCP server)
#    If configured: mcp__opennews__search_news_by_coin tool is available
#    Install: see https://github.com/6551Team/opennews-mcp

# 2. Reddit JSON API — community buzz (no auth needed, works everywhere)
curl -s "https://www.reddit.com/r/cryptocurrency/hot.json?limit=1" -H "User-Agent: agent-reach/1.0" | python3 -c "import json,sys; print('Reddit: OK')" 2>/dev/null || echo "Reddit: unavailable"

# 3. xreach CLI — Twitter/X KOL tracking (needs proxy in China)
#    Configure proxy first: agent-reach configure proxy http://127.0.0.1:7890
which xreach && xreach search "test" --json -n 1 --proxy http://127.0.0.1:7890 2>/dev/null && echo "xreach: OK" || echo "xreach: unavailable"

# 4. opentwitter-mcp — Twitter MCP server (alternative to xreach)
#    If configured: opentwitter MCP tools are available
```

**At least one social source must work.** If all fail, Step 3 is skipped and social score defaults to 50 (neutral).

### Historical Data: DexPaprika (free, no API key)

```bash
# DexPaprika provides DEX historical OHLCV data by token contract address
# Supports Solana, Base, Ethereum, BSC, Arbitrum + 33 networks
# REST API — no installation needed, just curl

# Test:
curl -s "https://api.dexpaprika.com/search?query=TROLL" | python3 -c "import json,sys; print(json.loads(sys.stdin.read()).get('tokens',[])[0])"

# Optional: add as MCP server for richer integration
# claude mcp add dexpaprika -- npx -y dexpaprika-mcp
```

If DexPaprika API is unreachable, Step 6 is skipped and backtestScore defaults to 50 (neutral).

## Execution

### User Parameters

Ask the user before starting (or use defaults):

| Parameter | Default | Options |
|-----------|---------|---------|
| Chains | Solana + Base | Solana (501), Ethereum (1), Base (8453), BSC (56), Arbitrum (42161) |
| Focus | All narratives | AI, Meme, DeFi, Infra, BTC-eco, Political |
| Risk tolerance | Medium | Conservative, Medium, Aggressive |
| Output | Terminal | Terminal, Markdown file |

If the user just says "扫一下" or "daily scan" without parameters, use defaults (Solana + Base, all narratives, medium risk).

---

## Step 1: Hot Token Discovery

**Goal**: Find trending tokens across target chains, categorize by narrative.

### 1.1 Fetch Trending Tokens

Run in parallel for each target chain:

```bash
# Trending by OKX trending score (hot-tokens ranking)
onchainos token hot-tokens --chain <chainId> --limit 20

# Volume-based trending (complementary view)
onchainos token trending --chains <chainIds> --sort-by 5 --time-frame 4 --limit 30
```

### 1.2 Extract & Categorize

From the results, extract for each token:
- `tokenSymbol`, `tokenContractAddress`, `chainIndex`
- `price`, `change` (24h %), `volume`, `marketCap`, `liquidity`
- `holders`, `top10HoldPercent`, `bundleHoldPercent`, `devHoldPercent`
- `riskLevelControl` (1=low, 2=medium, 3=high)

Categorize by narrative based on token name, symbol, and tags:
- **AI/Agent**: tokens related to AI, agents, virtual, autonomous
- **Meme**: animal names, cultural references, ironic/funny names
- **DeFi**: DEX tokens, lending, yield, staking
- **Infra**: L1/L2, bridges, oracles, tooling
- **BTC-eco**: BTC wrappers, ordinals, BTC-related
- **Political**: political figures, events
- **Stablecoin/Yield**: USD-pegged, yield-bearing stablecoins

### 1.3 Filter Criteria

Remove tokens that are obvious noise:
- Top10 concentration > 80% (extreme whale control)
- Market cap < $5K (dust)
- Volume/MCap ratio > 100x (likely bot wash trading)
- Zero holders or zero liquidity

Keep a shortlist of **top 15-20 interesting tokens** for further analysis.

---

## Step 2: Smart Money Signal Scan

**Goal**: Identify what smart money, KOLs, and whales are actively buying.

### 2.1 Aggregated Buy Signals

Run in parallel for each target chain:

```bash
# Smart money buy signals
onchainos signal list --chain <chainId> --wallet-type 1 --limit 10

# KOL buy signals
onchainos signal list --chain <chainId> --wallet-type 2 --limit 10

# Whale buy signals
onchainos signal list --chain <chainId> --wallet-type 3 --limit 10
```

### 2.2 Extract Signal Data

For each signal entry:
- `token.symbol`, `token.tokenAddress`, `token.marketCapUsd`
- `triggerWalletCount` (number of smart money addresses buying)
- `amountUsd` (total buy amount)
- `soldRatioPercent` (how much they've already sold — key metric!)
- `token.top10HolderPercent`, `token.holders`

### 2.3 Signal Quality Scoring

Rate each signal:
- **Strong**: 5+ wallets buying, soldRatio < 30%, marketCap > $100K
- **Medium**: 3+ wallets buying, soldRatio < 60%
- **Weak**: 1-2 wallets, soldRatio > 70% (already exiting)
- **Exit signal**: soldRatio > 90% (smart money dumping)

### 2.4 Cross-reference with Hot Tokens

Flag tokens that appear in BOTH Step 1 (trending) AND Step 2 (smart money buying) — these have the strongest alpha signal.

---

## Step 3: Social Sentiment Scan

**Goal**: Gauge market sentiment from crypto news, Reddit communities, and Twitter/X KOL discussions for candidate tokens from Steps 1-2.

### 3.1 Determine Available Sources

At the start of this step, check which social tools are available:

```
Source priority (use all that work):
1. opennews MCP  → mcp__opennews__search_news_by_coin (if MCP configured)
2. Reddit API    → curl to reddit JSON endpoints (always available)
3. xreach CLI    → Twitter search (if installed + authenticated)
4. opentwitter   → Twitter MCP (if MCP configured)
```

Record which sources are active. If none work, skip this step entirely and assign `socialScore = 50` (neutral) for all tokens.

### 3.2 Crypto News Sentiment (opennews)

For each candidate token (top 10 from Steps 1-2 combined):

```
Tool: mcp__opennews__search_news_by_coin
Args: coin = "<TOKEN_SYMBOL>", limit = 5
```

From results, extract:
- **newsCount**: how many articles in the last 24-48h
- **sentiment**: classify each headline as bullish / bearish / neutral
- **catalysts**: any major events (listing, partnership, hack, regulation)

Scoring:
- 5+ bullish articles, no bearish → `newsSentiment = 90`
- 3+ bullish, no bearish → `newsSentiment = 75`
- Mixed (both bullish & bearish) → `newsSentiment = 50`
- Mostly bearish → `newsSentiment = 25`
- No news at all → `newsSentiment = 50` (neutral, not penalized)

### 3.3 Reddit Community Buzz

For each candidate token, search crypto subreddits:

```bash
# Search across crypto Reddit
curl -s "https://www.reddit.com/r/cryptocurrency+CryptoMoonShots+solana+defi/search.json?q=<TOKEN_SYMBOL>&limit=10&sort=new&t=week" \
  -H "User-Agent: daily-alpha-scanner/2.0"
```

From results, extract:
- **postCount**: number of posts mentioning this token in the past week
- **totalScore**: sum of upvotes across all matching posts
- **commentCount**: total comments (engagement depth)
- **sentimentRatio**: ratio of positive vs negative posts (read titles + top comments)

Scoring:
- 10+ posts, totalScore > 100, mostly positive → `redditBuzz = 85`
- 5+ posts, totalScore > 30, mixed-positive → `redditBuzz = 65`
- 1-4 posts, low engagement → `redditBuzz = 45`
- Zero mentions → `redditBuzz = 30` (no buzz is slightly bearish for a "hot" token)

### 3.4 Twitter/X KOL Tracking (if available)

If xreach CLI is available:
```bash
xreach search "$TOKEN_SYMBOL crypto" --json -n 15 --proxy http://127.0.0.1:7890
```

If opentwitter MCP is available, use its search tool instead.

From results, extract:
- **tweetCount**: number of tweets in past 24-48h
- **kol_mentions**: tweets from accounts with 10K+ followers
- **sentiment**: bullish / bearish / neutral ratio
- **engagementRate**: avg (likes + retweets + replies) per tweet

Scoring:
- 3+ KOL mentions, mostly bullish, high engagement → `twitterScore = 90`
- 1-2 KOL mentions, positive sentiment → `twitterScore = 70`
- Mentions exist but mixed/low engagement → `twitterScore = 50`
- No data (tool unavailable) → `twitterScore = null` (excluded from average)

### 3.5 Social Score Calculation

Combine available sources into a single `socialScore` (0-100):

```
If all 3 sources available:
  socialScore = newsSentiment × 0.40 + redditBuzz × 0.30 + twitterScore × 0.30

If 2 sources available:
  socialScore = weighted average of available sources (equal weights)

If 1 source available:
  socialScore = that source's score

If 0 sources:
  socialScore = 50 (neutral)
```

### 3.6 Social Signal Flags

Flag notable patterns:
- 🔥 **VIRAL**: socialScore > 80 — strong positive buzz across multiple platforms
- 📰 **NEWS_CATALYST**: major news event detected (listing, partnership, whale buy)
- 🤫 **SILENT_ALPHA**: smart money buying (Step 2) but zero social buzz — potential early signal
- ⚠️ **HYPE_RISK**: social score > 85 but smart money already selling (soldRatio > 60%) — possible exit liquidity
- 🧊 **COLD**: socialScore < 30 — no one is talking about this token

---

## Step 4: Meme Coin Scan

**Goal**: Scan launchpads for new launches worth tracking.

### 4.1 New Token Scan

```bash
# Latest meme launches on Solana
onchainos memepump tokens --chain 501

# Latest meme launches on BSC (if in target chains)
onchainos memepump tokens --chain 56
```

### 4.2 Extract Key Metrics

For each new meme token:
- `symbol`, `tokenAddress`, `bondingPercent` (bonding curve progress)
- `market.marketCapUsd`, `market.buyTxCount1h`, `market.sellTxCount1h`
- `tags.snipersPercent`, `tags.bundlersPercent`
- `tags.top10HoldingsPercent`, `tags.devHoldingsPercent`, `tags.freshWalletsPercent`
- `tags.totalHolders`
- `social.x` (has Twitter?), `social.website`, `social.telegram`
- `creatorAddress` (for dev reputation lookup)

### 4.3 Developer Reputation Check

For promising tokens (survived bonding, has social presence):

```bash
onchainos memepump token-dev-info --address <tokenAddress> --chain <chainId>
```

Key fields:
- `devCreateTokenCount` — how many tokens this dev has created
- `devRugPullTokenCount` — how many rugged
- `devLaunchedTokenCount` — how many survived past bonding curve

### 4.4 Bundle/Sniper Detection

```bash
onchainos memepump token-bundle-info --address <tokenAddress> --chain <chainId>
```

### 4.5 Meme Scoring

Filter out garbage:
- Sniper % > 50% → likely bot-controlled → skip
- Top10 holdings > 60% → extreme concentration → skip
- 0 holders or < 3 holders → too early → skip
- No social presence at all → skip
- Dev has > 5 rug pulls → skip

Keep tokens that have:
- Survived bonding curve (migrated)
- Sniper < 20%, bundle < 10%
- Has at least Twitter presence
- Dev rug pull rate < 20%
- Growing holder count

---

## Step 5: Security Audit

**Goal**: Batch security scan all candidate tokens from Steps 1-4.

### 5.1 Collect Candidates

Merge the shortlisted tokens from Steps 1, 2, 3, and 4 (deduplicate by address). Max 10 tokens for batch scan.

### 5.2 Batch Token Security Scan

```bash
onchainos security token-scan --tokens "<chainId1>:<addr1>,<chainId2>:<addr2>,..."
```

Up to 10 tokens per batch. Key fields to check:
- `riskLevel`: LOW / MEDIUM / HIGH
- `isHoneypot`: true = INSTANT REJECT
- `isMintable`: true = can create infinite tokens
- `isDumping`: true = active sell pressure
- `isFakeLiquidity`: true = liquidity is fake
- `isLiquidityRemoval`: true = LP being pulled
- `isCounterfeit`: true = fake/copycat token
- `isWash`: true = wash trading detected

### 5.3 Advanced Risk Analysis

For tokens that pass the security scan, run advanced info:

```bash
onchainos token advanced-info --address <address> --chain <chainId>
```

Key fields:
- `devCreateTokenCount` — serial deployer?
- `devRugPullTokenCount` — rug history
- `lpBurnedPercent` — LP lock safety (higher = safer)
- `sniperHoldingPercent` — sniper concentration
- `bundleHoldingPercent` — bundle bot concentration
- `suspiciousHoldingPercent` — flagged wallets
- `tokenTags` — look for: `communityRecognized`, `smartMoneyBuy`, `dsPaid`, `CTO`

### 5.4 Risk Classification

| Risk Level | Criteria | Action |
|------------|----------|--------|
| SAFE | LOW risk, no flags, LP burned > 90%, dev clean | Can consider |
| CAUTION | LOW risk but some flags (high dev token count, moderate concentration) | Small position only |
| DANGER | Any honeypot/mintable/fake liquidity flag | REJECT |
| CRITICAL | Multiple red flags, dev rug history, active dumping | REJECT + warn |

---

## Step 6: Signal Backtesting (Historical Validation)

**Goal**: For BUY/WATCH candidates that passed security audit, validate the signal by checking historical price performance using DexPaprika OHLCV data.

### 6.1 Select Candidates for Backtesting

Only backtest tokens that:
- Passed Step 5 security audit (Risk = LOW or MEDIUM, no critical flags)
- Have a composite score > 45 from Steps 1-5 (skip obvious AVOIDs)
- Max 5 tokens to backtest (API rate limiting)

### 6.2 Get Token Pool (Highest Liquidity)

For each candidate, find the highest-liquidity DEX pool:

```bash
curl -s "https://api.dexpaprika.com/networks/<network>/tokens/<tokenAddress>/pools?limit=3"
```

Network mapping:
- Solana → `solana`
- Base → `base`
- Ethereum → `ethereum`
- BSC → `bsc`
- Arbitrum → `arbitrum-one`

From results, pick the pool with highest `volume_usd`. Record `pool_id`.

### 6.3 Pull Historical OHLCV

```bash
# 30-day daily candles
curl -s "https://api.dexpaprika.com/networks/<network>/pools/<pool_id>/ohlcv?start=<30d_ago>&end=<today>&interval=24h&limit=30"

# 7-day hourly candles (for recent price action)
curl -s "https://api.dexpaprika.com/networks/<network>/pools/<pool_id>/ohlcv?start=<7d_ago>&end=<today>&interval=1h&limit=168"
```

Available intervals: `1m`, `5m`, `10m`, `15m`, `30m`, `1h`, `6h`, `12h`, `24h`

### 6.4 Compute Signal Metrics

From OHLCV data, calculate:

**Price Trend:**
- `price_7d_change`: 7-day price change %
- `price_30d_change`: 30-day price change %
- `price_from_ath`: distance from 30-day all-time high %
- `price_from_atl`: distance from 30-day all-time low %

**Volatility:**
- `daily_volatility`: average daily (high-low)/open %
- `max_drawdown_7d`: worst peak-to-trough decline in 7 days

**Volume Trend:**
- `volume_trend`: is 7d avg volume higher or lower than 30d avg? (expanding = bullish)
- `volume_spike_days`: days with volume > 3x average in last 30d

**Support/Resistance:**
- `current_vs_7d_vwap`: current price relative to 7-day VWAP (above = bullish, below = bearish)
- `consolidation_range`: is price in a tight range (<10% spread) in last 3 days? (potential breakout)

### 6.5 Smart Money Entry Timing Analysis

If the token appeared in Step 2 (smart money signals), analyze:
- Where is the current price relative to when smart money likely entered?
- Did SM enter near recent lows (good timing) or near highs (chasing)?
- Is the token still within 20% of SM entry zone? (potential upside remains)
- Has SM already captured > 50% of the recent move? (late entry risk)

Estimate SM entry price by looking at the OHLCV around the signal detection time.

### 6.6 Backtest Scoring

Rate each token's historical performance:

| backtestScore | Criteria |
|---------------|----------|
| 80-100 | Uptrend + expanding volume + near support + SM entered low |
| 60-79 | Mixed trend but positive volume + reasonable entry zone |
| 40-59 | Sideways/declining + no volume expansion |
| 20-39 | Downtrend + shrinking volume + SM entry near highs |
| 0-19 | Crash + extreme drawdown + volume collapse |

### 6.7 Backtest Signal Flags

- 📈 **UPTREND**: 7d change > +15%, expanding volume → momentum confirmed
- 🔄 **CONSOLIDATION**: tight range + volume drying up → potential breakout/breakdown
- 📉 **DOWNTREND**: 7d change < -20%, declining volume → avoid or wait for bottom
- 💎 **DIAMOND_ENTRY**: price within 10% of 30d low + volume expanding → potential reversal point
- 🏔️ **NEAR_ATH**: price within 10% of 30d high → late entry risk, wait for pullback

---

## Step 7: Consolidated Briefing

**Goal**: Synthesize all data into a single actionable report.

### 7.1 Report Structure

Use the template at `templates/daily-briefing.md` to generate the output.

### 7.2 Scoring Model (7 Dimensions)

Each candidate token gets a composite score (0-100):

| Factor | Weight | Data Source |
|--------|--------|-------------|
| Narrative strength | 10% | Step 1 categorization + current market meta |
| On-chain momentum | 10% | Price change, volume, tx count, holder growth |
| Smart money conviction | 25% | Step 2: signal count, wallet count, sold ratio |
| Social sentiment | 10% | Step 3: news + Reddit + Twitter combined socialScore |
| Security score | 25% | Step 5: security scan + advanced info |
| **Historical validation** | **10%** | **Step 6: DexPaprika OHLCV backtest — price trend, volume, entry timing** |
| Liquidity depth | 10% | Liquidity USD, liquidity/mcap ratio |

**Signal Bonus/Penalty (Social + Backtest combined):**
- If social signal = 🔥 VIRAL → add +5 bonus to final score
- If social signal = 🤫 SILENT_ALPHA → no change (early signal, not penalized)
- If social signal = ⚠️ HYPE_RISK → subtract -10 from final score (exit liquidity warning)
- If social signal = 🧊 COLD → subtract -3 from final score
- If backtest signal = 📈 UPTREND → add +3 bonus (momentum confirmed by history)
- If backtest signal = 💎 DIAMOND_ENTRY → add +5 bonus (price near bottom + volume expanding)
- If backtest signal = 📉 DOWNTREND → subtract -5 from final score
- If backtest signal = 🏔️ NEAR_ATH → subtract -3 from final score (late entry risk)

### 7.3 Purchase Recommendation Tiers

Based on composite score and risk tolerance:

**BUY (Score > 70, Security = SAFE)**
- Strong narrative + smart money backing + clean security
- Suggested position: 2-5% of portfolio

**WATCH (Score 50-70, Security = SAFE/CAUTION)**
- Promising but needs more confirmation
- Set price alerts, monitor daily

**AVOID (Score < 50 or Security = DANGER/CRITICAL)**
- Too risky or no clear edge
- Explain specific red flags

### 7.4 Output Format

Generate the briefing with:
1. Executive summary (3-5 bullets)
2. Hot tokens by narrative table
3. Smart money signal table
4. Social sentiment overview (news + Reddit + Twitter summary per token)
5. Meme scan highlights
6. Security audit results
7. **Historical validation (price chart summary, backtest scores, entry timing)**
8. Final recommendation table with 7D score breakdown and risk rating
9. Disclaimer

### 7.5 Save Report

```bash
# Save to reports directory
mkdir -p reports
# Filename: daily-alpha-YYYY-MM-DD.md
```

---

## Error Handling

- If `onchainos` returns error 50125 (region restriction): inform user to check API key region or use VPN
- If any step returns empty data: skip that step, note it in the report, continue with available data
- If security scan returns empty for a token: mark as "UNSCANNED" in the report, do NOT recommend
- **Social sentiment graceful degradation**: if opennews MCP unavailable → use Reddit + Twitter only; if Reddit blocked → use opennews + Twitter; if all social tools fail → assign socialScore = 50 (neutral) and note "Social data unavailable" in report
- If xreach times out (common without proxy): fall back to opentwitter-mcp or skip Twitter entirely
- **DexPaprika graceful degradation**: if API returns error or empty data → skip backtesting, assign backtestScore = 50 (neutral) and note "Historical data unavailable" in report
- If pool not found for a token (new token, no DEX history) → backtestScore = 50, note "No trading history"
- Always run all 7 steps even if some data is partial — the report should indicate data completeness per step

## Quick Mode

If the user says "快速扫描" / "quick scan", run abbreviated version:
1. `hot-tokens` top 10 only (single chain)
2. `signal list` smart money only (single chain)
3. Quick social scan: opennews only (skip Reddit/Twitter), top 5 tokens
4. Skip meme scan
5. Security scan top 5 candidates only
6. Skip backtesting (quick mode does not run DexPaprika)
7. Abbreviated 1-page briefing with 7D scores (backtest = neutral)

## Chain Reference

| Chain | ID | Meme Support |
|-------|----|-------------|
| Solana | 501 | pumpfun, believe, launchlab, moonshot, etc. |
| BSC | 56 | fourmeme, flap |
| Base | 8453 | clanker, bankr |
| Ethereum | 1 | Limited |
| TRON | 195 | sunpump |
