# 🤖 AI News Telegram Agent

An automated AI news curator that aggregates the latest AI research, blogs, and product launches from multiple sources and delivers a daily digest to your Telegram — **completely free, runs 24/7 without a laptop**.

![n8n](https://img.shields.io/badge/Built_with-n8n-EA4B71?logo=n8ndotio)
![Groq](https://img.shields.io/badge/AI-Groq_Llama_3.3-orange)
![Telegram](https://img.shields.io/badge/Delivery-Telegram-26A5E4?logo=telegram)
![License](https://img.shields.io/badge/License-MIT-green)

---

## 🎯 What It Does

Every day at **9:00 AM**, the agent:

1. 📰 Pulls 2000+ articles from **6 AI sources** (ArXiv, OpenAI, HuggingFace, Hacker News, VentureBeat, Google News)
2. 🔍 Filters to the **last 48 hours** only
3. 🧠 Uses **Groq (Llama 3.3 70B)** to pick the **top 6 most important** items and summarize them
4. 📩 Sends a clean, formatted digest to your **Telegram**

**Sample output:**
```
🤖 Daily AI Digest

1. OpenAI launches GPT-5 with advanced reasoning
Major upgrade focused on scientific research and coding.
Read more

2. Meta releases Llama 4 open-source
New benchmarks show 80% improvement over previous version.
Read more
...
```

---

## 🏗️ Architecture

```
┌─────────────────┐
│ Schedule: 9 AM  │
└────────┬────────┘
         │
    ┌────┴────┬────────┬────────┬────────┬────────┐
    ▼         ▼        ▼        ▼        ▼        ▼
┌────────┐┌──────┐┌────────┐┌──────┐┌────────┐┌─────┐
│Google  ││ArXiv ││HuggFace││OpenAI││HackerNw││ VB  │
│  News  ││Papers││  Blog  ││ Blog ││   AI   ││ AI  │
└────┬───┘└──┬───┘└────┬───┘└──┬───┘└────┬───┘└──┬──┘
     └───────┴─────────┴───────┴─────────┴───────┘
                          │
                          ▼
                  ┌──────────────┐
                  │  Combine All │
                  └──────┬───────┘
                         ▼
                  ┌──────────────┐
                  │ Filter <48h  │
                  └──────┬───────┘
                         ▼
                  ┌──────────────┐
                  │ Groq Summary │
                  │ (Llama 3.3)  │
                  └──────┬───────┘
                         ▼
                  ┌──────────────┐
                  │  Telegram    │
                  │  Bot Send    │
                  └──────────────┘
```

---

## 🛠️ Tech Stack

| Component | Tool | Cost |
|-----------|------|------|
| Automation | [n8n](https://n8n.io) (self-hosted on Render) | Free |
| AI Summarization | [Groq API](https://console.groq.com) (Llama 3.3 70B) | Free (14,400 req/day) |
| Messaging | [Telegram Bot API](https://core.telegram.org/bots) | Free |
| Hosting | [Render.com](https://render.com) | Free tier |
| Uptime Pinger | [cron-job.org](https://cron-job.org) | Free |
| News Sources | 6 RSS feeds | Free |

**Total cost: $0/month forever** 🎉

---

## 🚀 Quick Setup

### Prerequisites
- Telegram account
- Google account (for Render & GitHub)
- 45 minutes

### Steps

1. **Get API Keys**
   - Telegram Bot Token from [@BotFather](https://t.me/BotFather)
   - Your Telegram Chat ID from [getUpdates API](https://core.telegram.org/bots/api#getupdates)
   - Groq API key from [console.groq.com](https://console.groq.com)

2. **Deploy n8n on Render**
   - Fork/use Docker image `n8nio/n8n:latest`
   - Set environment variables (see [detailed setup](#detailed-setup))
   - Deploy on free tier

3. **Keep n8n awake**
   - Set up [cron-job.org](https://cron-job.org) to ping `/healthz` every 10 min

4. **Import this workflow**
   - Download [`workflow.json`](./workflow.json)
   - In n8n: Create workflow → Paste → Replace 3 placeholders:
     - `YOUR_GROQ_API_KEY`
     - `YOUR_TELEGRAM_BOT_TOKEN`
     - `YOUR_TELEGRAM_CHAT_ID`

5. **Activate** → Enjoy daily AI news at 9 AM! ✨

---

## 📚 Detailed Setup

<details>
<summary>Click to expand full setup guide</summary>

### 1. Create Telegram Bot
1. Open Telegram → search `@BotFather`
2. Send `/newbot` → follow prompts
3. Save the token

### 2. Get Chat ID
1. Message your bot anything
2. Open `https://api.telegram.org/bot<TOKEN>/getUpdates`
3. Find `"chat":{"id":XXXXX}` → that's your Chat ID

### 3. Get Groq Key
1. Sign up at [console.groq.com](https://console.groq.com)
2. Create API Key → copy it

### 4. Deploy n8n on Render
Environment variables to set:
```
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=yourpassword
N8N_HOST=your-app.onrender.com
N8N_PROTOCOL=https
WEBHOOK_URL=https://your-app.onrender.com/
GENERIC_TIMEZONE=Asia/Kolkata
TZ=Asia/Kolkata
N8N_PORT=5678
N8N_RUNNERS_ENABLED=true
N8N_SECURE_COOKIE=false
```

### 5. Set up uptime pinger
- URL: `https://your-app.onrender.com/healthz`
- Schedule: Every 10 minutes

</details>

---

## 🎨 Customization

### Change Schedule
In `Daily 9AM Trigger` node, modify cron expression:
- `0 9 * * *` → Daily 9 AM
- `0 9,18 * * *` → 9 AM + 6 PM
- `0 9 * * 1-5` → Weekdays only

### Add More Sources
Add new `RSS Feed Read` nodes connecting to "Combine All Feeds":
- Reddit ML: `https://www.reddit.com/r/MachineLearning/.rss`
- MIT News: `https://news.mit.edu/topic/mitartificial-intelligence2-rss.xml`
- Anthropic: `https://www.anthropic.com/news/rss.xml`

### Multi-User Broadcast
Update `Build Telegram Request` node to send to multiple chat IDs:
```javascript
const recipients = ["CHAT_ID_1", "CHAT_ID_2", "CHAT_ID_3"];
return recipients.map(chatId => ({
  json: { chat_id: chatId, text, parse_mode: "HTML" }
}));
```

---

## 📸 Screenshots

_Add your screenshots here_

---

## 🤝 Contributing

Contributions welcome! Open an issue or PR for:
- New RSS sources
- Better prompts for summarization
- Additional features (on-demand commands, voice, etc.)

---

## 📄 License

MIT License — free to use, modify, and share.

---

## 🙌 Credits

Built with ❤️ using:
- [n8n](https://n8n.io) - Workflow automation
- [Groq](https://groq.com) - Ultra-fast LLM inference
- [Telegram Bot API](https://core.telegram.org/bots)

---

## ⭐ Star This Repo

If this helped you, please star the repo — it helps others discover it!
