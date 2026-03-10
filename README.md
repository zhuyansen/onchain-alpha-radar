# OnChain Alpha Radar

<p align="center">
  <b>On-Chain Alpha Discovery & Research Pipeline</b><br>
  Token Discovery · Holdings Analysis · Smart Money Tracking · Research Report
</p>

<p align="center">
  <a href="./docs/README_ZH.md">中文</a>
</p>

---

A Claude Code Skill that chains together **4 external tools** to systematically discover, analyze, and report on-chain alpha — from raw blockchain signals to professional research reports with visualizations.

```
Token Discovery → Holdings Analysis → Smart Money Tracking → Research Report
     ↓                  ↓                    ↓                    ↓
  onchainos          onchainos            onchainos          deep-research
  + Twitter          + Twitter            + Twitter          + excalidraw
```

## What It Does

| Phase | Input | Output |
|-------|-------|--------|
| **Token Discovery** | Scan chain for trending, meme, smart money signals | Scored candidate list with cross-dimensional ranking |
| **Holdings Analysis** | Token address | Top 20 holders, concentration, dev reputation, wallet classification |
| **Smart Money Tracking** | Token address | SM/Whale/KOL signals, conviction assessment, wallet deep dive |
| **Research Report** | All above data + Twitter | Full Markdown report + Excalidraw visual diagrams |

## Quick Start

### 1. Install Dependencies

```bash
# Required: OKX OnchainOS CLI
curl -sSL https://raw.githubusercontent.com/okx/onchainos-skills/main/install.sh | sh

# Optional: Twitter MCP (get token at https://6551.io/mcp)
git clone https://github.com/6551Team/opentwitter-mcp.git
claude mcp add twitter -e TWITTER_TOKEN=<your-token> -- uv --directory ./opentwitter-mcp run twitter-mcp

# Optional: Deep Research skill
git clone https://github.com/wshuyi/deep-research.git
cp -r deep-research/skills/deep-research ~/.claude/skills/

# Optional: Excalidraw Diagram skill
git clone https://github.com/coleam00/excalidraw-diagram-skill.git
cp -r excalidraw-diagram-skill ~/.claude/skills/excalidraw-diagram
```

### 2. Set Environment Variables

```bash
# Required: OKX API Keys (get from https://www.okx.com/account/my-api)
export OKX_API_KEY="your-api-key"
export OKX_SECRET_KEY="your-secret-key"
export OKX_PASSPHRASE="your-passphrase"

# If in China: proxy for OKX API (use lowercase!)
export all_proxy=http://127.0.0.1:7890
```

### 3. Install This Skill

```bash
# Clone to your project
git clone https://github.com/zhuyansen/onchain-alpha-radar.git
cp -r onchain-alpha-radar/.claude .claude
cp -r onchain-alpha-radar/.claude-plugin .claude-plugin
```

### 4. Run

Tell Claude Code:

```
Research the hottest token on Solana right now
给我做一份 Solana 链上研报
Quick scan $TOKEN
Track smart money on Solana
```

## Scoring System

Tokens are scored across 4 dimensions (max 100 points):

| Dimension | Weight | Data Source |
|-----------|--------|-------------|
| Volume | 30pts | 24h trading volume rank |
| Momentum | 25pts | 4h price change rank |
| Smart Money | 25pts | SM/Whale/KOL wallet count + USD + sold ratio |
| Liquidity | 10pts | Liquidity/MCap ratio health |
| Community | 10pts | Holder count |

**Cross-dimensional tokens** (appearing in 2+ dimensions) are prioritized — they indicate genuine alpha vs single-factor noise.

## Execution Modes

| Mode | Trigger | What It Does |
|------|---------|-------------|
| **Full Pipeline** | "Research $TOKEN" / "做研报" | All 4 phases → complete report |
| **Quick Scan** | "Quick scan $TOKEN" / "快速扫描" | Price + holders + SM signal → 1-paragraph summary |
| **Phase-by-Phase** | "What's trending?" / "Track smart money" | Run individual phases |
| **Smart Money Briefing** | "Smart money on Solana" | SM/Whale/KOL signal dashboard |

## Example Output

See the [reports/](./reports/) directory for real examples:

- `michi-alpha-report-v2.md` — Full research report with scoring matrix + Twitter data
- `michi-scoring-matrix.png` — Visual scoring comparison

![Scoring Matrix](./reports/michi-scoring-matrix.png)

## Tool Dependencies

| Tool | Required | Purpose |
|------|----------|---------|
| [onchainos](https://github.com/okx/onchainos-skills) | **Yes** | On-chain data (34 commands: token, market, swap, gateway, portfolio) |
| [opentwitter-mcp](https://github.com/6551Team/opentwitter-mcp) | No | Twitter/X intelligence (12 tools) |
| [deep-research](https://github.com/wshuyi/deep-research) | No | 8-step research methodology |
| [excalidraw-diagram](https://github.com/coleam00/excalidraw-diagram-skill) | No | Visual diagram generation |

Pipeline degrades gracefully — if Twitter/deep-research/excalidraw are not installed, those sections are skipped.

## Project Structure

```
.claude/
└── skills/
    └── onchain-alpha-radar/
        ├── SKILL.md                    # Main skill (4-phase pipeline methodology)
        └── templates/
            ├── report-template.md      # Full report template
            ├── quick-scan-template.md  # Quick scan template
            └── smart-money-briefing.md # SM briefing template
.claude-plugin/
├── plugin.json                         # Plugin config + dependency declarations
└── marketplace.json                    # Marketplace metadata
reports/                                # Example outputs
```

## License

MIT

## Credits

Built with [Claude Code](https://claude.com/claude-code) by orchestrating:
- [OKX OnchainOS](https://github.com/okx/onchainos-skills) — on-chain data
- [OpenTwitter MCP](https://github.com/6551Team/opentwitter-mcp) — Twitter intelligence
- [Deep Research](https://github.com/wshuyi/deep-research) — research methodology
- [Excalidraw Diagram](https://github.com/coleam00/excalidraw-diagram-skill) — visual diagrams
