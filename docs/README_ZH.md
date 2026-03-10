# OnChain Alpha Toolkit

<p align="center">
  <b>链上 Alpha 发现 · 聪明钱追踪 · 舆情预警</b><br>
  两个 Claude Code Skill，一站式加密研究工具箱
</p>

<p align="center">
  <a href="../README.md">English</a>
</p>

---

## 两个 Skill，一个工具箱

### 🔍 Skill 1: OnChain Alpha Radar（链上 Alpha 雷达）

**Token 发现 → 持仓分析 → Smart Money 追踪 → 研报输出**

基于 OKX OnchainOS 全市场扫描，四维交叉评分，产出专业研报。

```
onchainos (OKX) + opentwitter-mcp + deep-research + excalidraw-diagram
```

### 🔔 Skill 2: Smart Money Sentinel（聪明钱哨兵）*NEW*

**信号发现 → 新闻验证 → KOL 舆情 → 钱包确认 → 入场研判**

以币安聪明钱信号为起点，串联新闻、KOL 讨论、链上钱包数据交叉验证。

```
Binance Skills Hub + opennews-mcp + opentwitter-mcp + base-mcp
```

---

## 快速开始

### 1. 安装工具箱

```bash
git clone https://github.com/zhuyansen/onchain-alpha-radar.git
cd onchain-alpha-radar
cp -r .claude ~/your-project/.claude
cp -r .claude-plugin ~/your-project/.claude-plugin
```

### 2. 安装依赖

**Alpha Radar（OKX 系列）：**

```bash
# onchainos CLI
curl -sSL https://raw.githubusercontent.com/okx/onchainos-skills/main/install.sh | sh

# 配置 OKX API Key（教程：https://x.com/okxchinese/status/2028806839773839683）
export OKX_API_KEY="你的key"
export OKX_SECRET_KEY="你的secret"
export OKX_PASSPHRASE="你的passphrase"
```

**Smart Money Sentinel（币安系列）：**

```bash
# 币安 Skills（已内置，公开接口无需 API Key）

# opennews-mcp（从 https://6551.io/mcp 获取 token）
git clone https://github.com/6551Team/opennews-mcp.git .mcp-servers/opennews-mcp
claude mcp add opennews -e OPENNEWS_TOKEN=<你的token> -- uv --directory .mcp-servers/opennews-mcp run opennews-mcp

# base-mcp（可选，Base 链钱包分析）
claude mcp add-json base-mcp '{"command":"npx","args":["-y","base-mcp@latest"]}'
```

**共用工具：**

```bash
# opentwitter-mcp（两个 Skill 共用，从 https://6551.io/mcp 获取 token）
git clone https://github.com/6551Team/opentwitter-mcp.git .mcp-servers/opentwitter-mcp
claude mcp add twitter -e TWITTER_TOKEN=<你的token> -- uv --directory .mcp-servers/opentwitter-mcp run opentwitter-mcp

# 国内代理（Rust 程序需要小写！）
export all_proxy=http://127.0.0.1:7890
```

### 3. 开始使用

```
# Alpha Radar
给我扫描 Solana 上最热的 Token
做一份完整的链上研报

# Smart Money Sentinel
扫描最新的聪明钱信号
验证 $TOKEN 的入场时机
批量扫描 Solana 上的聪明钱异动
```

---

## 两个 Skill 对比

| | Alpha Radar | Smart Money Sentinel |
|--|--|--|
| **起点** | 全市场扫描（量/动量/SM/Meme） | 币安聪明钱信号 |
| **数据源** | onchainos (OKX) | Binance Skills Hub |
| **新闻** | 无 | opennews-mcp（利好/利空验证） |
| **社交** | opentwitter-mcp | opentwitter-mcp |
| **钱包** | onchainos 持仓/信号 | Binance query-address-info + base-mcp |
| **目标** | 发现 Alpha 标的 | 验证信号 → 判断入场时机 |
| **输出** | 研究报告 | 预警报告 + 行动建议 |

## 评分体系

### Alpha Radar（100 分）

| 维度 | 权重 | 数据源 |
|------|------|--------|
| 成交量 | 30 | 24h 成交量排名 |
| 动量 | 25 | 4h 涨跌幅 |
| 聪明钱 | 25 | SM/巨鲸/KOL 信号 |
| 流动性 | 10 | 流动性/市值比 |
| 社区 | 10 | 持有人数 |

### Smart Money Sentinel（100 分）

| 维度 | 权重 | 数据源 |
|------|------|--------|
| 信号强度 | 25 | 聪明钱数量、最大收益、退出率 |
| 新闻情绪 | 20 | 利好/利空新闻数量 |
| KOL 舆情 | 20 | 讨论量、KOL 参与度 |
| 持仓验证 | 20 | 买卖比、SM 持仓、集中度 |
| 基本面 | 15 | 市值、交易量、持有人、社交链接 |

---

## 工具依赖

| 工具 | 被使用 | 用途 |
|------|--------|------|
| [onchainos](https://github.com/okx/onchainos-skills) | Alpha Radar | 链上数据（34 条命令） |
| [Binance Skills Hub](https://github.com/binance/binance-skills-hub) | Sentinel | 聪明钱信号 + Token/钱包数据 |
| [opennews-mcp](https://github.com/6551Team/opennews-mcp) | Sentinel | 加密新闻 + AI 信号（11 个工具） |
| [opentwitter-mcp](https://github.com/6551Team/opentwitter-mcp) | 两者共用 | Twitter KOL 情报（12 个工具） |
| [base-mcp](https://github.com/base/base-mcp) | Sentinel | Base 链钱包分析（14 个工具） |
| [deep-research](https://github.com/wshuyi/deep-research) | Alpha Radar | 研究方法论 |
| [excalidraw-diagram](https://github.com/coleam00/excalidraw-diagram-skill) | Alpha Radar | 可视化图表 |

两个 Skill 均支持优雅降级——可选工具未安装时自动跳过，给予中性评分。

## 协议

MIT
