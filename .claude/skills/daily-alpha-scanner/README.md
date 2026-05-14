# Daily Alpha Scanner

A one-click on-chain alpha discovery skill for AI coding agents. Runs a systematic 5-step research pipeline and outputs actionable BUY / WATCH / AVOID recommendations.

## What It Does

```
Step 1: Hot Token Discovery    Trending tokens across chains, categorized by narrative
          ↓
Step 2: Smart Money Signals    What smart money, KOLs, and whales are buying RIGHT NOW
          ↓
Step 3: Meme Coin Scan         New launches on pump.fun / fourmeme with dev & sniper checks
          ↓
Step 4: Security Audit         Batch honeypot, rug pull, fake liquidity detection
          ↓
Step 5: Consolidated Briefing  Composite scoring (0-100) with buy/avoid verdicts
```

Each token gets scored on 5 dimensions:

| Factor | Weight |
|--------|--------|
| Narrative strength | 15% |
| On-chain momentum (price, volume, txs) | 20% |
| Smart money conviction (wallets, sold ratio) | 25% |
| Security score (scans + dev history) | 25% |
| Liquidity depth | 15% |

## Installation

```bash
npx skills add zhuyansen/daily-alpha-scanner
```

## Prerequisites

### 1. OKX OnchainOS CLI

```bash
curl -sSL https://raw.githubusercontent.com/okx/onchainos-skills/main/install.sh | sh
```

Verify: `onchainos --version` (requires v3.1+)

### 2. OKX Web3 API Key

Get a free API key from [OKX Web3 Dev Portal](https://web3.okx.com/onchainos/dev-portal):

1. Connect your wallet
2. Select the Starter plan (free)
3. Copy your API Key, Secret Key, and Passphrase

Set environment variables:

```bash
export OKX_API_KEY="your-api-key"
export OKX_SECRET_KEY="your-secret-key"
export OKX_PASSPHRASE="your-passphrase"
```

Free monthly quota: 1,000,000 Basic API calls + 100,000 Premium API calls.

## Usage

### Full Scan (default: Solana + Base)

```
/daily-alpha-scanner
```

Or use natural language:

- `"daily scan"` / `"每日扫描"`
- `"what to buy today"` / `"今天买什么"`
- `"alpha scanner"` / `"扫链"`
- `"market scan"` / `"市场扫描"`

### Quick Scan (abbreviated)

Say `"quick scan"` or `"快速扫描"` for a faster, single-chain version that skips meme scanning.

### Custom Parameters

| Parameter | Default | Options |
|-----------|---------|---------|
| Chains | Solana + Base | Solana (501), Ethereum (1), Base (8453), BSC (56), Arbitrum (42161) |
| Focus | All narratives | AI, Meme, DeFi, Infra, BTC-eco, Political |
| Risk tolerance | Medium | Conservative, Medium, Aggressive |
| Output | Terminal | Terminal, Markdown file |

Example: `"scan Solana and BSC, focus on meme tokens, aggressive risk"`

## Output

The skill generates a structured briefing report:

1. **Executive Summary** - 3-5 bullet overview
2. **Hot Tokens by Narrative** - AI, Meme, DeFi, Infra categories with metrics
3. **Smart Money Signals** - Buy signals with wallet count, amount, sold ratio
4. **Meme Scan** - New launches with dev reputation and sniper detection
5. **Security Audit** - Batch scan results (honeypot, mintable, fake LP, etc.)
6. **Recommendations** - BUY / WATCH / AVOID with composite scores

Reports are saved to `reports/daily-alpha-YYYY-MM-DD.md`.

## Supported Chains

| Chain | ID | Meme Launchpads |
|-------|----|----------------|
| Solana | 501 | pump.fun, believe, launchlab, moonshot, meteora |
| BSC | 56 | fourmeme, flap |
| Base | 8453 | clanker, bankr |
| Ethereum | 1 | Limited |
| TRON | 195 | sunpump |

## Security Checks

The scanner detects these risk factors:

| Check | Source |
|-------|--------|
| Honeypot detection | `onchainos security token-scan` |
| Mintable token supply | `onchainos security token-scan` |
| Fake liquidity | `onchainos security token-scan` |
| Active dumping | `onchainos security token-scan` |
| Wash trading | `onchainos security token-scan` |
| Dev rug pull history | `onchainos token advanced-info` |
| Sniper/bundle concentration | `onchainos token advanced-info` |
| LP burn percentage | `onchainos token advanced-info` |

## Data Sources

All data comes from [OKX OnchainOS](https://web3.okx.com/onchainos) API via the `onchainos` CLI:

- `onchainos token hot-tokens` / `trending` - Hot token discovery
- `onchainos signal list` - Smart money / KOL / whale signals
- `onchainos memepump tokens` / `token-dev-info` / `token-bundle-info` - Meme scanning
- `onchainos security token-scan` - Security audit
- `onchainos token advanced-info` - Advanced risk analysis

## Example Report

See [reports/daily-alpha-2026-05-10.md](reports/daily-alpha-2026-05-10.md) for a real example output.

## Skill Structure

```
daily-alpha-scanner/
  SKILL.md              # Skill definition with full pipeline instructions
  templates/
    daily-briefing.md   # Report template with placeholders
  reports/              # Generated reports (gitignored)
  README.md
  LICENSE
```

## Disclaimer

This skill is for informational and educational purposes only. It does not constitute financial advice. Cryptocurrency investments carry extreme risk. Always do your own research (DYOR) before making any investment decisions.

## License

MIT
