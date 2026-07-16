---
title: "AI Hedge Fund 项目体验记录"
date: 2026-07-16
summary: "用 AI 在本地部署了一个多 Agent 协作的对冲基金系统，记录完整过程和踩坑。"
---

## 项目信息

- 仓库：https://github.com/virattt/ai-hedge-fund
- 本地路径：`~/Projects/ai-hedge-fund`
- 体验日期：2026-07-16

## 项目架构

多 Agent 协作的 AI 对冲基金系统，核心模式：

```
Portfolio Input (股票/持仓/现金)
        ↓
┌───────────────────────────────────┐
│  多个 Analyst Agent 并行分析       │
│  - Warren Buffett (价值投资)       │
│  - Michael Burry (逆向投资)        │
│  - Charlie Munger (护城河)         │
│  - Peter Lynch (成长投资)          │
│  - Bill Ackman (激进投资)          │
│  - Technical Analyst (技术分析)    │
│  - Fundamentals Analyst (基本面)   │
│  - Growth Analyst (成长性)         │
│  - Sentiment Analyst (情绪面)      │
│  - News Sentiment (新闻情绪)       │
│  - Valuation Analyst (估值)        │
│  ...共十几个                       │
└───────────────────────────────────┘
        ↓ 各自输出 signal (buy/sell/neutral) + confidence + reasoning
┌───────────────────────────────────┐
│  Portfolio Manager (汇总决策)      │
│  综合所有 analyst 信号，输出最终    │
│  交易建议：ticker/action/quantity  │
└───────────────────────────────────┘
        ↓
Investment Report (可视化结果)
```

## 技术栈

- **框架**：LangGraph（多 Agent 编排）+ LangChain（LLM调用）
- **后端**：FastAPI + SQLite（flow 持久化）
- **前端**：React + Vite + React Flow（可视化节点编排）
- **模型支持**：OpenAI / Anthropic / DeepSeek / Groq / Ollama(本地) 等
- **数据源**：financialdatasets.ai（付费API，提供财务报表/价格/内幕交易等）

## 本地部署过程

### 1. 环境准备

```bash
git clone https://github.com/virattt/ai-hedge-fund.git ~/Projects/ai-hedge-fund
cd ~/Projects/ai-hedge-fund
poetry install
```

### 2. 安装 Ollama + 本地模型

- 从 https://ollama.com/download 手动下载 macOS 版安装
- 拉取模型：`caffeinate -i ollama pull qwen3:8b`（约5.2GB，注意防休眠）
- 验证：`ollama list` → qwen3:8b

### 3. 配置 .env

```
DEEPSEEK_API_KEY=sk-xxx（余额不足，未实际使用）
FINANCIAL_DATASETS_API_KEY=xxx（免费tier无余额，402报错）
```

### 4. 修复 Bug

项目 `src/cli/input.py` 有个 bug：`--ollama --model qwen3:8b` 时，`find_model_by_name` 从 `ollama_models.json` 找到 provider="Alibaba" 直接返回，没走 Ollama 分支。

修复：在 `select_model` 函数开头加判断，当 `model_flag` 和 `use_ollama` 同时为真时，强制 provider 为 Ollama。

### 5. 启动 Web UI

```bash
# 后端
nohup poetry run uvicorn app.backend.main:app --host 0.0.0.0 --port 8000 &
# 前端
cd app/frontend && nohup npm run dev &
# 访问 http://localhost:5173/
```

## 体验结果

### 能跑通的部分

- Ollama 本地模型推理 ✅
- Web UI 可视化 Flow 编辑器 ✅
- 节点拖拽连线、选择模型 ✅
- Agent 调用 LLM 生成分析 ✅

### 未跑通的部分

- 金融数据 API 需付费（免费注册无余额），所有 Analyst 输出 "Insufficient data"
- 只有一个 Analyst 给 signal 时，Portfolio Manager 信息不足，默认 sell

### 核心限制

- financialdatasets.ai **只支持美股**，A股/港股无法使用
- 免费 tier 无余额，需充值才能获取财务数据
- 本地 8B 模型推理较慢（每个 agent 约1-2分钟）
