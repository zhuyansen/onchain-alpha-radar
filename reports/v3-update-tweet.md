# 推文 Thread — Daily Alpha Scanner v3.0 Update

---

### 1/9：Hook

Daily Alpha Scanner v3.0 — 加了历史回测验证层

v2.0 加了社交情报（新闻 + Reddit + Twitter KOL）
v3.0 再加 DexPaprika 历史 K 线回测

7 步流水线：
热门代币 → 聪明钱 → 社交情报 → Meme 扫链 → 安全体检 → 历史回测 → 7D 评分推荐

核心升级：用历史价格数据验证信号 — 「聪明钱买了，社交也在讨论，但价格趋势怎么样？是在涨还是在跌？是高位接盘还是低位抄底？」

完整教程 + 实战数据 + 开源代码 👇🧵

---

### 2/9：为什么需要回测 — v2.0 的盲区

v2.0 已经很强了：链上数据 + 社交情报 + 6D 评分

但还有一个维度缺失：价格历史

场景 1：聪明钱在买 + Twitter 在讨论 → 但价格已经从底部涨了 10 倍，现在进去就是高位接盘
场景 2：安全通过 + 社交冷淡 → 但价格已经跌了 80%，在底部缩量整理，可能是抄底机会
场景 3：所有信号都看好 → 但过去 7 天日均波动 50%，这个币太疯狂不适合中等风险

v3.0 解决的问题：用 DexPaprika 拉 DEX 历史 K 线，计算价格趋势、波动率、成交量变化、聪明钱入场位置

一句话：不只看「谁在买」「社交在聊什么」，还看「价格配合不配合」

---

### 3/9：DexPaprika — DEX 历史数据源

这是 v3.0 的核心新增工具

DexPaprika（@coinaboroficial）提供：
· 33 条链的 DEX 历史 K 线数据
· 支持 Solana、Base、ETH、BSC、Arbitrum
· 按合约地址查询（和我们的流水线完美对接）
· 免费、无需 API Key
· 支持 1m/5m/15m/1h/6h/24h 多时间粒度

```bash
# 查 Token 的交易池
curl -s "https://api.dexpaprika.com/networks/solana/tokens/<地址>/pools?limit=3"

# 拉 30 天日线
curl -s "https://api.dexpaprika.com/networks/solana/pools/<pool_id>/ohlcv?interval=24h&limit=30"

# 返回：open, high, low, close, volume — 标准 OHLCV
```

也可以装成 MCP server：`claude mcp add dexpaprika -- npx -y dexpaprika-mcp`

GitHub: https://github.com/coinpaprika/dexpaprika-mcp

---

### 4/9：Step 6 — 信号回测做了什么

在安全审计之后、最终评分之前，插入 Step 6: Signal Backtesting

对每个通过安全审计的候选 token（最多 5 个）：

```
1. 找池子
   token 地址 → DexPaprika 查流动性最大的 DEX 池

2. 拉 K 线
   · 30 天日线 → 看中期趋势
   · 7 天时线 → 看近期动量

3. 计算 4 组指标

   价格趋势：
   · 7d/30d 涨跌幅
   · 距 30d 最高/最低点位置

   波动率：
   · 日均振幅
   · 7d 最大回撤

   成交量趋势：
   · 7d 均量 vs 30d 均量（放量 or 缩量）
   · 量能异动日（> 3x 均量的天数）

   入场时机：
   · 聪明钱大概在什么价位买的？
   · 当前价离 SM 入场价多远？
   · SM 已经吃了多少涨幅？（吃完了你就是接盘的）
```

---

### 5/9：5 种回测信号标记

```
📈 UPTREND
  7d 涨 > 15% + 成交量放大
  → 趋势确认，可以跟

💎 DIAMOND ENTRY
  价格在 30d 低位附近 + 成交量开始放大
  → 潜在反转点，高性价比入场

🔄 CONSOLIDATION
  窄幅震荡 + 成交量萎缩
  → 蓄势待发，等方向选择

📉 DOWNTREND
  7d 跌 > 20% + 缩量
  → 趋势向下，别抄底

🏔️ NEAR ATH
  价格在 30d 高点附近（< 10%）
  → 追高风险，等回调
```

配合 v2.0 的社交信号：
· 📈 UPTREND + 🔥 VIRAL = 强趋势 + 强共识 → 最强信号
· 💎 DIAMOND_ENTRY + 🤫 SILENT_ALPHA = 底部 + 聪明钱 + 没人讨论 → 最佳 Alpha
· 🏔️ NEAR_ATH + ⚠️ HYPE_RISK = 高位 + KOL 喊单 + SM 在卖 → 最危险，跑

---

### 6/9：7D 评分模型

v1.0: 5 维
v2.0: 6 维（+社交）
v3.0: 7 维（+回测）

```
叙事(10) + 动量(10) + 聪明钱(25) + 社交(10) + 安全(25) + 回测(10) + 流动性(10) = 100
```

聪明钱和安全各占 25%（最重要 — 谁在买 + 能不能安全买）
社交、回测、流动性各占 10%（交叉验证维度）
叙事和动量各占 10%（基础面）

加减分：
· 📈 UPTREND → +3
· 💎 DIAMOND_ENTRY → +5
· 📉 DOWNTREND → -5
· 🏔️ NEAR_ATH → -3

和社交信号叠加：VIRAL +5, HYPE_RISK -10, COLD -3

弱特征叠加的思路 — 7 个维度、多个信号标记，每个力都不大，但对齐了就很强

---

### 7/9：实战数据 — TROLL 30 天 K 线

跑了一下 TROLL（Solana，$130M mcap）的回测数据：

```
日期          开盘       最高       最低       收盘       成交量
05-07    $0.0514   $0.0533   $0.0464   $0.0464   $997K
05-08    $0.0464   $0.0548   $0.0451   $0.0500   $767K
05-09    $0.0501   $0.0608   $0.0486   $0.0555   $1.3M
05-10    $0.0559   $0.1229   $0.0556   $0.1179   $12M  ← 暴涨日！量能 12x
05-11    $0.1182   $0.1462   $0.1051   $0.1195   $7.4M
05-12    $0.1190   $0.1225   $0.0914   $0.1041   $5.9M
05-13    $0.1040   $0.1417   $0.0891   $0.1306   $6M
```

回测分析：
· 7d 涨幅：+154%（$0.051 → $0.130）
· 5/10 暴涨日成交量是前一天的 12 倍
· 暴涨后 3 天在 $0.09-$0.14 区间震荡整理
· 当前价 $0.130 距 30d 高点 $0.146 约 -11%
· 信号：🏔️ NEAR_ATH — 已经涨了很多，追高有风险

这就是回测的价值 — 链上 + 社交都看好 TROLL，但价格已经涨了 154%，7D 评分需要扣分

---

### 8/9：完整工具链

v3.0 串联的开源工具：

链上数据（Step 1-2, 4-5）：
· OKX OnchainOS Skills — https://github.com/okx/onchainos-skills
  18 个技能：热门代币/聪明钱/Meme 扫链/安全检测

社交情报（Step 3）：
· opennews-mcp — https://github.com/6551Team/opennews-mcp
· Agent-Reach + xreach — https://github.com/Panniantong/Agent-Reach
· Reddit JSON API（免费，无需安装）

历史回测（Step 6）：
· DexPaprika MCP — https://github.com/coinpaprika/dexpaprika-mcp
  14 个工具，33 链 DEX 历史 K 线，免费无 Key

编排引擎：
· Claude Code + Skills CLI — https://skills.sh

---

### 9/9：安装 + 开源

v3.0 安装：

```bash
# 1. 链上数据
npx skills add okx/onchainos-skills

# 2. Alpha Scanner
npx skills add zhuyansen/daily-alpha-scanner

# 3. Twitter 数据（推荐）
pip install https://github.com/Panniantong/agent-reach/archive/main.zip
agent-reach install --env=auto
agent-reach configure proxy http://127.0.0.1:7890

# 4. 历史回测（可选，也可直接用 REST API）
claude mcp add dexpaprika -- npx -y dexpaprika-mcp
```

运行：
```
/daily-alpha-scanner
```

或者说「每日扫描」「v3 全量扫描」「今天买什么」

3-4 分钟自动输出 7D 研报 + 历史回测 + BUY/WATCH/AVOID

项目已全部开源：
https://github.com/zhuyansen/daily-alpha-scanner

版本历史：
· v1.0 — 5 步链上流水线（OKX OnchainOS）
· v2.0 — +社交情报层（opennews + Reddit + xreach）
· v3.0 — +历史回测层（DexPaprika OHLCV）

欢迎 Star / Fork / Issue / DM 交流 👋

#ClaudeCode #OKX #OnchainOS #SmartMoney #Web3 #Solana #AIAgent #AlphaScanner #DexPaprika
