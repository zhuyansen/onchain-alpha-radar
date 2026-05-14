# 推文 Thread — Daily Alpha Scanner v2.0 Update

---

### 1/8：Hook

Daily Alpha Scanner 升级 v2.0 — 加了社交情报层

v1.0 只看链上数据，v2.0 加了三个社交信号源：
加密新闻 + Reddit 社区热度 + Twitter KOL 追踪

6 步流水线：
热门代币 → 聪明钱信号 → 社交情报 → Meme 扫链 → 安全体检 → 6D 评分推荐

从「链上数据能看到聪明钱在买什么」升级到「社交上有没有人讨论、讨论的人是谁」

完整教程 + 实战结果 + 开源代码 👇🧵

---

### 2/8：为什么需要社交情报 — v1.0 的盲区

v1.0 只有链上数据（OKX OnchainOS），能发现：
· 热门代币趋势
· 聪明钱/KOL/鲸鱼在买什么
· Meme 新盘安全检测
· 蜜罐/增发/假LP 扫描

但有几个场景看不到：

1）聪明钱在买，但 Twitter 没人聊 → 可能是早期 Alpha，也可能是无效信号
2）Twitter 一堆 KOL 喊单，但聪明钱已经在卖 → 经典出货模式
3）token 有新闻催化（上所、合作），链上数据看不到

v2.0 的核心升级：用社交数据交叉验证链上信号

这也是弱特征叠加的思路 — 链上信号是一个方向的力，社交信号是另一个方向的力，两个力对齐时信号才强

---

### 3/8：三源社交情报架构

v2.0 在 Step 2（聪明钱）和 Step 4（Meme 扫链）之间插入了 Step 3: Social Sentiment Scan

三个数据源，优先级从高到低：

```
源 1: opennews MCP（加密新闻）
→ mcp__opennews__search_news_by_coin
→ 新闻情绪评分 + 催化事件发现
→ 权重 40%

源 2: Reddit JSON API（社区热度）
→ 搜索 r/cryptocurrency + r/CryptoMoonShots + r/solana
→ 帖子数 + 投票数 + 评论数 + 情绪判断
→ 权重 30%
→ 免费，无需认证

源 3: xreach CLI（Twitter/X KOL 追踪）
→ Agent-Reach 安装，走本地代理
→ KOL 提及数 + 点赞/转发/浏览量 + 情绪分析
→ 权重 30%
```

关键设计：优雅降级
· opennews 额度用完 → 自动跳过，用 Reddit + Twitter
· xreach 网络不通 → 跳过 Twitter，用 opennews + Reddit
· 全挂 → socialScore = 50（中性），不影响其他步骤
· 至少一个源工作就有增量价值

---

### 4/8：Agent-Reach + xreach 一行配 Twitter

Twitter 数据是 v2.0 最大的增量，用 Agent-Reach 配置：

```bash
# 安装 Agent-Reach（一键安装 xreach CLI + 13 个平台工具）
pip install https://github.com/Panniantong/agent-reach/archive/main.zip
agent-reach install --env=auto

# 配代理（国内必须）
agent-reach configure proxy http://127.0.0.1:7890

# 验证
agent-reach doctor
```

配好后 xreach 直接可用：

```bash
# 搜索 token 相关推文
xreach search "TROLL solana" --json -n 10 --proxy http://127.0.0.1:7890

# 返回：推文内容、点赞数、转发数、浏览量、用户信息
# 自动识别 KOL（粉丝 10K+ 或单条 50+ likes）
```

不装 Agent-Reach 也能跑 — 只是没有 Twitter 维度，Reddit + opennews 照常工作

---

### 5/8：6D 评分模型升级

v1.0 是 5 维评分：

```
旧：叙事(15) + 动量(20) + 聪明钱(25) + 安全(25) + 流动性(15) = 100
```

v2.0 升级为 6 维：

```
新：叙事(10) + 动量(15) + 聪明钱(25) + 社交(15) + 安全(25) + 流动性(10) = 100
```

社交维度占 15% — 不会主导评分，但提供关键的交叉验证信号

额外加减分机制：
· VIRAL（社交 > 80）→ +5 分
· SILENT ALPHA（聪明钱买 + 社交沉默）→ 不变
· HYPE RISK（社交火爆 + 聪明钱在卖）→ -10 分
· COLD（社交 < 30）→ -3 分

HYPE RISK 的 -10 分是最重要的 — 直接惩罚「KOL 喊单 + 聪明钱出货」的出货模式

---

### 6/8：5 种社交信号标记

v2.0 新增了 5 种信号标记，对应不同的交易场景：

```
🔥 VIRAL（socialScore > 80）
  多平台正面共振，强共识
  → 加速催化，但要确认是否已 price in

📰 NEWS_CATALYST
  检测到重大新闻（上所、合作、黑客攻击）
  → 判断利好/利空，看聪明钱反应

🤫 SILENT ALPHA（聪明钱买入 + 社交零讨论）
  可能是早期信号，社交还没跟上
  → 密切关注，等社交验证

⚠️ HYPE RISK（社交火爆 + 聪明钱已卖 > 60%）
  经典出货模式：KOL 喊单吸引散户接盘
  → 回避！不做别人的退出流动性

🧊 COLD（socialScore < 30）
  链上有数据但没人讨论
  → 缺乏社区共识，小心无人接盘
```

最实用的是 🤫 SILENT ALPHA 和 ⚠️ HYPE RISK — 这两个信号 v1.0 完全看不到

---

### 7/8：实战效果 — 2026.05.14 扫描结果

今天跑了 v2.0 完整 6 步，Solana + Base 两条链：

BUY（76 分）：
· TROLL — $130M mcap，59K holders，社区认证 + smartMoneyBuy + LP 烧 87%
  社交：Twitter 5 KOL 提及，140 万 views，Reddit 3 帖 45 投票
  → 链上 + 社交双重验证通过

WATCH：
· BURNIE（60 分）— Twitter 9 个 KOL 提及（最高！），327K views
  但链上 0 个聪明钱钱包买入 → Twitter vs 链上背离，等 SM 入场
· WOJAK（59 分）— 经典 Meme，16K holders，Twitter 4 KOL
  稳定但无催化
· ASTEROID（53 分）— ⚠️ Dev 创建 175,738 个代币，2,234 次 rug pull
  虽有 smartMoneyBuy 标签，但 Dev 背景极差 → 社交层确认不值得追

社交层的关键发现：
· 🤫 LUKSO — 聪明钱买入（25% sold），但社交评分 46，零讨论 → 早期信号待确认
· BURNIE 的 Twitter vs 链上背离 — 9 KOL 喊单但 0 SM 买入，v1.0 看不出这个矛盾

---

### 8/8：安装 + 开源

v2.0 安装只需 3 步：

Step 1: 安装 OKX 链上技能包

```bash
npx skills add okx/onchainos-skills
```

Step 2: 安装 Daily Alpha Scanner v2.0

```bash
npx skills add zhuyansen/daily-alpha-scanner
```

Step 3: 配 Twitter 数据源（可选，推荐）

```bash
pip install https://github.com/Panniantong/agent-reach/archive/main.zip
agent-reach install --env=auto
agent-reach configure proxy http://127.0.0.1:7890
```

配好后在 Claude Code 里输入：

```
/daily-alpha-scanner
```

或者直接说「每日扫描」「今天买什么」「v2 全量扫描」

2-3 分钟自动输出完整 6D 研报 + BUY/WATCH/AVOID 推荐

项目已全部开源：
https://github.com/zhuyansen/daily-alpha-scanner

v2.0 新增依赖：
· Agent-Reach — https://github.com/Panniantong/Agent-Reach（Twitter/Reddit 等 16 平台）
· opennews-mcp — https://github.com/6551Team/opennews-mcp（加密新闻）

底层依赖：
· OKX OnchainOS Skills — https://github.com/okx/onchainos-skills
· Skills CLI — https://skills.sh

技术栈：
· Claude Code + Agent Skills 编排
· OKX OnchainOS API（18 个链上技能）
· Agent-Reach + xreach CLI（Twitter/X 数据）
· Reddit JSON API（免费社区数据）
· opennews MCP（加密新闻情绪）
· 零代码，纯 SKILL.md 定义流水线

欢迎 Star / Fork / Issue / DM 交流 👋

#ClaudeCode #OKX #OnchainOS #SmartMoney #Web3 #Solana #AIAgent #AlphaScanner #AgentReach
