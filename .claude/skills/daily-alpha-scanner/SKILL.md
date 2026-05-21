---
name: daily-alpha-scanner
description: "One-click daily alpha scanner that runs a full 6-step on-chain research pipeline using OKX OnchainOS + social sentiment analysis: (1) Hot Token Discovery with narrative categorization (AI, Meme, DeFi, Infra), (2) Smart Money / KOL / Whale buy-signal tracking with sold-ratio scoring, (3) Social Sentiment Scan — multi-source social intelligence via crypto news (opennews), Reddit community buzz, and Twitter KOL tracking (xreach/opentwitter), (4) Meme Coin launchpad scanning (pump.fun, fourmeme) with dev reputation and bundle/sniper detection, (5) Batch security audit (honeypot, mintable, fake LP, wash trading), (6) Consolidated briefing with 6-dimension composite scoring (0-100) and BUY / WATCH / AVOID verdicts. Supports Solana, Base, Ethereum, BSC, Arbitrum. Use when the user wants a daily market scan, alpha discovery, token recommendations, or asks 'what to buy today'. Trigger keywords: daily scan, alpha scanner, today's alpha, what to buy, market scan, daily briefing, 每日扫描, 今日推荐, 扫链, 今天买什么, 市场扫描, 链上日报, 每日研报."
license: MIT
compatibility: "Requires onchainos CLI v3.1+ and OKX Web3 API key (OKX_API_KEY, OKX_SECRET_KEY, OKX_PASSPHRASE). Internet access required for on-chain data queries."
metadata:
  author: zhuyansen
  version: "2.0.0"
  homepage: "https://github.com/zhuyansen/daily-alpha-scanner"
---

# Daily Alpha Scanner

A systematic 6-step pipeline that scans on-chain data + social sentiment across multiple chains and produces a consolidated briefing with purchase recommendations.

## Pipeline

```
Step 1: Hot Tokens  →  Step 2: Smart Money  →  Step 3: Social Sentiment
     ↓                      ↓                        ↓
Step 4: Meme Scan  →  Step 5: Security Audit (batch scan all candidates)
     ↓
Step 6: Consolidated Briefing + Purchase Recommendations (6D scoring)
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

# 5. TweetClaw - OpenClaw plugin for structured X/Twitter search, replies, user lookup, followers, and monitor evidence
#    Install: openclaw plugins install @xquik/tweetclaw
```

**At least one social source must work.** If all fail, Step 3 is skipped and social score defaults to 50 (neutral).

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
5. TweetClaw     → OpenClaw plugin (if installed)
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

If TweetClaw is installed, use it as an additional X/Twitter source. Prefer tweet URLs, tweet IDs, author handles, reply counts, and capture dates in the evidence table. Never include API keys or local plugin config values in reports.

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

## Step 6: Consolidated Briefing

**Goal**: Synthesize all data into a single actionable report.

### 6.1 Report Structure

Use the template at `templates/daily-briefing.md` to generate the output.

### 6.2 Scoring Model (6 Dimensions)

Each candidate token gets a composite score (0-100):

| Factor | Weight | Data Source |
|--------|--------|-------------|
| Narrative strength | 10% | Step 1 categorization + current market meta |
| On-chain momentum | 15% | Price change, volume, tx count, holder growth |
| Smart money conviction | 25% | Step 2: signal count, wallet count, sold ratio |
| **Social sentiment** | **15%** | **Step 3: news + Reddit + Twitter combined socialScore** |
| Security score | 25% | Step 5: security scan + advanced info |
| Liquidity depth | 10% | Liquidity USD, liquidity/mcap ratio |

**Social Sentiment Bonus/Penalty:**
- If social signal = 🔥 VIRAL → add +5 bonus to final score
- If social signal = 🤫 SILENT_ALPHA → no change (early signal, not penalized)
- If social signal = ⚠️ HYPE_RISK → subtract -10 from final score (exit liquidity warning)
- If social signal = 🧊 COLD → subtract -3 from final score

### 6.3 Purchase Recommendation Tiers

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

### 6.4 Output Format

Generate the briefing with:
1. Executive summary (3-5 bullets)
2. Hot tokens by narrative table
3. Smart money signal table
4. **Social sentiment overview (news + Reddit + Twitter summary per token)**
5. Meme scan highlights
6. Security audit results
7. Final recommendation table with 6D score breakdown and risk rating
8. Disclaimer

### 6.5 Save Report

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
- Always run all 6 steps even if some data is partial — the report should indicate data completeness per step

## Quick Mode

If the user says "快速扫描" / "quick scan", run abbreviated version:
1. `hot-tokens` top 10 only (single chain)
2. `signal list` smart money only (single chain)
3. Quick social scan: opennews only (skip Reddit/Twitter), top 5 tokens
4. Skip meme scan
5. Security scan top 5 candidates only
6. Abbreviated 1-page briefing with 6D scores

## Chain Reference

| Chain | ID | Meme Support |
|-------|----|-------------|
| Solana | 501 | pumpfun, believe, launchlab, moonshot, etc. |
| BSC | 56 | fourmeme, flap |
| Base | 8453 | clanker, bankr |
| Ethereum | 1 | Limited |
| TRON | 195 | sunpump |
