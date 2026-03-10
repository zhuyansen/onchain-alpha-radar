# OnChain Alpha Toolkit

<p align="center">
  <b>On-Chain Alpha Discovery · Smart Money Tracking · Sentiment Alerts</b><br>
  Two Claude Code Skills for comprehensive crypto research
</p>

<p align="center">
  <a href="./docs/README_ZH.md">中文</a>
</p>

---

## Two Skills, One Toolkit

### 🔍 Skill 1: OnChain Alpha Radar

**Token Discovery → Holdings Analysis → Smart Money Tracking → Research Report**

Uses OKX OnchainOS to scan the entire market, cross-dimensionally score tokens, and produce research reports.

```
onchainos (OKX) + opentwitter-mcp + deep-research + excalidraw-diagram
```

### 🔔 Skill 2: Smart Money Sentinel *(NEW)*

**Signal Discovery → News Validation → KOL Sentiment → Wallet Verification → Entry Verdict**

Starts from Binance smart money signals, then cross-validates with news, KOL discussions, and on-chain wallet data.

```
Binance Skills Hub + opennews-mcp + opentwitter-mcp + base-mcp
```

---

## Quick Start

### 1. Install This Toolkit

```bash
git clone https://github.com/zhuyansen/onchain-alpha-radar.git
cd onchain-alpha-radar
cp -r .claude ~/your-project/.claude
cp -r .claude-plugin ~/your-project/.claude-plugin
```

### 2. Install Tool Dependencies

**For Alpha Radar (OKX-based):**

```bash
# onchainos CLI
curl -sSL https://raw.githubusercontent.com/okx/onchainos-skills/main/install.sh | sh

# Set OKX API keys (tutorial: https://x.com/okxchinese/status/2028806839773839683)
export OKX_API_KEY="your-key"
export OKX_SECRET_KEY="your-secret"
export OKX_PASSPHRASE="your-passphrase"
```

**For Smart Money Sentinel (Binance-based):**

```bash
# Binance Skills (already included — no API key needed for public endpoints)

# opennews-mcp (get token at https://6551.io/mcp)
git clone https://github.com/6551Team/opennews-mcp.git .mcp-servers/opennews-mcp
claude mcp add opennews -e OPENNEWS_TOKEN=<your-token> -- uv --directory .mcp-servers/opennews-mcp run opennews-mcp

# base-mcp (optional, for Base chain wallet analysis)
claude mcp add-json base-mcp '{"command":"npx","args":["-y","base-mcp@latest"]}'
```

**Shared:**

```bash
# opentwitter-mcp (used by both skills, get token at https://6551.io/mcp)
git clone https://github.com/6551Team/opentwitter-mcp.git .mcp-servers/opentwitter-mcp
claude mcp add twitter -e TWITTER_TOKEN=<your-token> -- uv --directory .mcp-servers/opentwitter-mcp run opentwitter-mcp

# China proxy (lowercase required for Rust binaries!)
export all_proxy=http://127.0.0.1:7890
```

### 3. Run

```
# Alpha Radar
Research the hottest token on Solana right now
给我做一份 Solana 链上研报

# Smart Money Sentinel
扫描最新的聪明钱信号
验证 $TOKEN 的入场时机
Batch scan smart money signals on Solana
```

---

## Skill Comparison

| | Alpha Radar | Smart Money Sentinel |
|--|--|--|
| **Starting Point** | Full market scan (volume/momentum/SM/meme) | Binance smart money signals |
| **Data Source** | onchainos (OKX) | Binance Skills Hub |
| **News** | — | opennews-mcp (bullish/bearish) |
| **Social** | opentwitter-mcp | opentwitter-mcp |
| **Wallet** | onchainos holders/signals | Binance query-address-info + base-mcp |
| **Goal** | Discover alpha targets | Validate signals → entry timing |
| **Output** | Research report | Alert report + action advice |

## Scoring Systems

### Alpha Radar (100 pts)

| Dimension | Weight | Source |
|-----------|--------|--------|
| Volume | 30 | 24h volume rank |
| Momentum | 25 | 4h price change |
| Smart Money | 25 | SM/Whale/KOL signals |
| Liquidity | 10 | Liq/MCap ratio |
| Community | 10 | Holder count |

### Smart Money Sentinel (100 pts)

| Dimension | Weight | Source |
|-----------|--------|--------|
| Signal Strength | 25 | SM count, max gain, exit rate |
| News Sentiment | 20 | Bullish/bearish news count |
| KOL Sentiment | 20 | Discussion volume, KOL participation |
| Wallet Verification | 20 | Buy/sell ratio, SM holders, concentration |
| Fundamentals | 15 | MCap, volume, holders, social links |

---

## Tool Dependencies

| Tool | Used By | Purpose |
|------|---------|---------|
| [onchainos](https://github.com/okx/onchainos-skills) | Alpha Radar | On-chain data (34 commands) |
| [Binance Skills Hub](https://github.com/binance/binance-skills-hub) | Sentinel | Smart money signals + token/wallet data |
| [opennews-mcp](https://github.com/6551Team/opennews-mcp) | Sentinel | Crypto news with AI signals (11 tools) |
| [opentwitter-mcp](https://github.com/6551Team/opentwitter-mcp) | Both | Twitter/X KOL intelligence (12 tools) |
| [base-mcp](https://github.com/base/base-mcp) | Sentinel | Base chain wallet analysis (14 tools) |
| [deep-research](https://github.com/wshuyi/deep-research) | Alpha Radar | Research methodology |
| [excalidraw-diagram](https://github.com/coleam00/excalidraw-diagram-skill) | Alpha Radar | Visual diagram generation |

Both skills degrade gracefully — optional tools are skipped with neutral scoring if unavailable.

## Project Structure

```
.claude/
└── skills/
    ├── onchain-alpha-radar/          # Skill 1: Alpha Radar
    │   ├── SKILL.md
    │   └── templates/
    ├── smart-money-sentinel/         # Skill 2: Smart Money Sentinel
    │   ├── SKILL.md
    │   └── templates/
    ├── trading-signal/               # Binance: SM signals
    ├── query-token-info/             # Binance: token data
    └── query-address-info/           # Binance: wallet data
.claude-plugin/
└── plugin.json                       # Unified plugin config
reports/                              # Example outputs
```

## License

MIT

## Credits

Built with [Claude Code](https://claude.com/claude-code) by orchestrating:
- [OKX OnchainOS](https://github.com/okx/onchainos-skills) — on-chain data
- [Binance Skills Hub](https://github.com/binance/binance-skills-hub) — smart money signals
- [OpenNews MCP](https://github.com/6551Team/opennews-mcp) — crypto news
- [OpenTwitter MCP](https://github.com/6551Team/opentwitter-mcp) — Twitter intelligence
- [Base MCP](https://github.com/base/base-mcp) — Base chain wallet
- [Deep Research](https://github.com/wshuyi/deep-research) — research methodology
- [Excalidraw Diagram](https://github.com/coleam00/excalidraw-diagram-skill) — visual diagrams
