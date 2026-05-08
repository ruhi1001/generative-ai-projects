# 🤖 AI News Telegram Agent

Automated AI news curator that delivers daily AI digests to Telegram.

## 🎯 What It Does

Every day at 9 AM, this agent:
1. Pulls 2000+ articles from 6 AI sources (ArXiv, OpenAI, HuggingFace, Hacker News, VentureBeat, Google News)
2. Filters to last 48 hours
3. Uses Groq AI (Llama 3.3 70B) to summarize top 6 items
4. Sends formatted digest to Telegram

## 🛠️ Tech Stack

- **n8n** (self-hosted on Render) - Workflow automation
- **Groq API** - LLM summarization (free tier)
- **Telegram Bot API** - Message delivery
- **6 RSS feeds** - News aggregation

**Cost: $0/month** 🎉

## 🚀 Quick Setup

1. Get API keys: Telegram Bot Token, Groq Key
2. Deploy n8n on Render (free)
3. Import [`workflow.json`](./workflow.json)
4. Replace 3 placeholders with your keys
5. Activate → enjoy!

[See full setup guide below](#detailed-setup)

## 📸 Screenshots

![Workflow](./screenshots/workflow.png)
![Telegram Output](./screenshots/telegram.jpg)

## 📚 Detailed Setup

<details>
<summary>Click to expand</summary>

### 1. Create Telegram Bot
- Message @BotFather → /newbot → save token

### 2. Get Chat ID  
- Message your bot → visit `api.telegram.org/bot<TOKEN>/getUpdates`

### 3. Get Groq Key
- Sign up at console.groq.com → create key

### 4. Deploy n8n on Render
Use Docker image `n8nio/n8n:latest` with these env vars:
```
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=yourpass
TZ=Asia/Kolkata
N8N_RUNNERS_ENABLED=true
N8N_SECURE_COOKIE=false
```

### 5. Keep awake with cron-job.org
Ping `/healthz` every 10 min

</details>

## 🎨 Architecture

```
Schedule → 6 RSS Feeds → Combine → Filter (48h) → Groq AI → Telegram
```

## 🔗 Related

- [← Back to all projects](../README.md)
