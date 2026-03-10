# 推文草稿 — OnChain Alpha Radar

---

## Thread (推文串)

### Tweet 1/7 (Hook)

我用 Claude Code 搭了一套链上 Alpha 研究流水线

一句话自动跑完：
Token 发现 → 持仓分析 → Smart Money 追踪 → 研报输出

4 个工具串联，从链上原始信号到专业研报，全程自动化

已开源 👇🧵

### Tweet 2/7 (架构)

架构很简单：一个 Claude Code Skill 编排 4 个外部工具

onchainos (OKX) → 链上数据 34 条命令
opentwitter-mcp (6551) → Twitter 情报 12 个工具
deep-research → 研究方法论
excalidraw-diagram → 可视化图表

核心是 SKILL.md 里的 4 阶段方法论，Claude 按步骤自动执行

### Tweet 3/7 (Phase 1: 选标的)

Phase 1 最关键：四维交叉选标的

同时扫描：
- 24h 成交量 Top 20
- 4h 涨幅 Top 20
- Smart Money 信号 Top 20
- Meme 新币迁移池

然后交叉打分（满分100）：
量(30) + 动量(25) + 聪明钱(25) + 流动性(10) + 社区(10)

只有同时出现在 2+ 维度的才是真 Alpha

### Tweet 4/7 (实战结果)

实测 Solana 链：

$michi 以 69 分排名第 1 — 唯一同时命中「成交量」和「聪明钱」两个维度的 Token

第 2 名 HABIBI 47 分，只有涨幅没有聪明钱
第 3 名 HYPE 44 分，只有量没有信号

单维度高分 = 噪音
多维度交叉 = Alpha

[附图: michi-scoring-matrix.png]

### Tweet 5/7 (Smart Money + Twitter)

Phase 3 追踪聪明钱后发现有意思的分歧：

KOL 钱包：买入 $6.5K，仅卖出 15% → 极强信念
Smart Money：买入 $9.9K，卖出 69% → 部分获利
巨鲸：买入 $12.4K，卖出 89% → 大幅出货

同时 Twitter 6551 API 拉到 19 条推文
官方号 @michionsolana 48K 粉丝
多个 KOL 在讨论

KOL 看多 vs 巨鲸跑路 = 博弈转折点

### Tweet 6/7 (操作步骤)

想自己跑？3 步搞定：

① 安装 onchainos CLI
curl -sSL https://raw.githubusercontent.com/okx/onchainos-skills/main/install.sh | sh

② 配置 OKX API Key（申请教程：https://x.com/okxchinese/status/2028806839773839683）
export OKX_API_KEY="你的key"

③ 克隆 Skill
git clone https://github.com/zhuyansen/onchain-alpha-radar
cp -r onchain-alpha-radar/.claude .claude

然后对 Claude Code 说「扫描 Solana 上最热的 Token」

### Tweet 7/7 (开源 + CTA)

完整代码已开源：
https://github.com/zhuyansen/onchain-alpha-radar

包含：
- 4 阶段完整方法论 (SKILL.md)
- 3 套研报模板
- 评分矩阵算法
- 实战示例报告

串联的 4 个工具也全部开源：
@okaborofficial onchainos-skills
@6551Team opentwitter-mcp
@wshuyi deep-research
@coleam00 excalidraw-diagram

觉得有用就 Star 一下 ⭐

#ClaudeCode #Web3 #OnChain #SmartMoney #Solana
