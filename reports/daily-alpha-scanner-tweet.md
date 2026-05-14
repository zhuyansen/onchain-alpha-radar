# 推文 Thread — Daily Alpha Scanner

---

### 1/6：Hook

我用 Claude Code + OKX OnchainOS 搭了一个每日链上 Alpha 扫描器

一句话自动跑完 5 步流水线：
热门代币 → 聪明钱信号 → Meme 扫链 → 安全体检 → 购买建议

从 500+ DEX 的链上原始数据到 BUY / WATCH / AVOID 推荐，全程自动化

完整教程 + 已开源 👇🧵

---

### 2/6：OKX OnchainOS 是什么

OKX 刚开源了一套链上 AI Agent 工具包：onchainos-skills

一行命令安装 18 个链上技能：

```
npx skills add okx/onchainos-skills
```

覆盖 DEX 交易全链路：
· 代币搜索 / 热门榜 / K线 / 盈亏分析
· 聪明钱 / KOL / 鲸鱼信号追踪
· Meme 扫链（pump.fun / fourmeme）
· 安全扫描（蜜罐 / 假LP / 增发检测）
· DEX 聚合兑换 / 跨链桥接
· Agentic Wallet（Agent 钱包）

支持 Solana / Base / ETH / BSC / Arbitrum 等 20+ 链

免费额度：Basic 100万次/月 + Premium 10万次/月
API Key 申请：https://web3.okx.com/onchainos/dev-portal

---

### 3/6：Daily Alpha Scanner 做了什么

我基于 onchainos 搭了一个 5 步自动化 Alpha 扫描器：

```
Step 1: 热门代币发现
onchainos token hot-tokens → 跨链 Trending 排名
按叙事分类：AI / Meme / DeFi / Infra

Step 2: 聪明钱信号
onchainos signal list → 谁在买？买了多少？卖了多少？
关键指标：walletCount（几个钱包买）+ soldRatio（卖出比例）

Step 3: Meme 扫链
onchainos memepump tokens → pump.fun 新盘扫描
查开发者跑路历史 + 狙击者占比 + 捆绑钱包检测

Step 4: 安全体检
onchainos security token-scan → 批量蜜罐/增发/假LP检测
onchainos token advanced-info → 开发者信誉 + LP 销毁率

Step 5: 综合评分 + 推荐
5 维打分（满分 100）：
叙事(15) + 动量(20) + 聪明钱(25) + 安全(25) + 流动性(15)
→ BUY / WATCH / AVOID
```

---

### 4/6：实战效果 — 2026.05.10 扫描结果

今天跑了一轮，扫描 Solana + Base 两条链：

BUY（可以建仓）：
· TROLL — $89M mcap，53K 持有者，社区共识 + 聪明钱买入，安全全通过，评分 75
· aura — $30M mcap，38K 持有者，Top10 仅 12.2%（最分散），LP 烧 97%，评分 72

WATCH（继续观察）：
· HANTA — Dev 0 跑路 + volumeSurge，但 LP 仅烧 10%
· CLAWNCH（Base）— 社区认可，聪明钱仅卖出 64%

AVOID（不碰）：
· BMNTP — Dev 创建 4572 个代币、58 次跑路
· UNOS — Dev 创建 766 个代币
· NOCK — 可增发代币（isMintable）

关键发现：聪明钱买入金额普遍很小（$100-$5K），大资金仍在观望，不是激进入场的好时机

---

### 5/6：怎么用

Step 1: 安装 OKX 链上技能包

```bash
npx skills add okx/onchainos-skills
```

Step 2: 安装 Daily Alpha Scanner

```bash
npx skills add zhuyansen/daily-alpha-scanner
```

Step 3: 申请 OKX Web3 API Key（免费）

打开 https://web3.okx.com/onchainos/dev-portal
授权钱包 → 选择初创级 → 复制 Key

```bash
export OKX_API_KEY="your-key"
export OKX_SECRET_KEY="your-secret"
export OKX_PASSPHRASE="your-passphrase"
```

Step 4: 开扫

在 Claude Code 里输入：

```
/daily-alpha-scanner
```

或者直接说「每日扫描」「今天买什么」「扫链」

等 2-3 分钟，自动输出完整研报 + 推荐

---

### 6/6：开源 + 联系

项目已全部开源：

Daily Alpha Scanner — 每日链上 Alpha 扫描器
https://github.com/zhuyansen/daily-alpha-scanner

底层依赖：
· OKX OnchainOS Skills — https://github.com/okx/onchainos-skills
· Skills CLI — https://skills.sh

技术栈：
· Claude Code + Agent Skills 编排
· OKX OnchainOS API（18 个链上技能）
· 零代码，纯 SKILL.md 定义流水线

欢迎 Star / Fork / Issue / DM 交流 👋

#ClaudeCode #OKX #OnchainOS #SmartMoney #Web3 #Solana #AIAgent #AlphaScanner
