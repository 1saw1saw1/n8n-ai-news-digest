# n8n AI News Digest

> **Automated AI news digest: fetch trending articles from dev.to, summarize in Ukrainian with Groq, save to Google Sheets.**

![Budget](https://img.shields.io/badge/Budget-%240–5%2Fmo-brightgreen)
![Stack](https://img.shields.io/badge/Stack-n8n%20%2B%20Groq%20%2B%20Google%20Sheets-orange)
![Language](https://img.shields.io/badge/Output-Ukrainian-yellow)

---

## What This Is

An n8n workflow that builds a **monthly AI news report** without manual copy-paste:

1. Pulls top AI articles from [dev.to API](https://dev.to/api)
2. Filters articles with **>5 reactions** and valid metadata
3. Uses **Groq LLM** to categorize and summarize each article **in Ukrainian**
4. Sends structured rows to **Google Sheets** via Apps Script

**Example output row:**

| title | category | summary | url | status |
|-------|----------|---------|-----|--------|
| Нові моделі LLM | AI Tools | Короткий огляд релізу... | https://dev.to/... | Готово |

---

## Architecture

```
Manual Trigger (or Schedule)
        │
        ▼
dev.to API  ──►  Filter (>5 reactions)
        │
        ▼
Information Extractor  ◄──  Groq Chat Model (qwen3.6-27b)
        │
        ▼
Google Apps Script  ──►  Google Sheets
```

---

## Quick Start

### 1. Google Sheets + Apps Script

1. Create a Google Sheet with columns: `title`, `url`, `category`, `summary`, `status`
2. **Extensions → Apps Script** — paste a simple `doPost(e)` handler that appends rows
3. **Deploy → New deployment → Web app** (Execute as: Me, Access: Anyone)
4. Copy the Web App URL

### 2. Import workflow in n8n

1. Open n8n → **Import from file** → `workflow/ai_news_digest.json`
2. In node **Send to Google Sheet** — replace `YOUR_SCRIPT_ID` with your Apps Script URL
3. In node **Groq Chat Model** — add Groq API credentials
4. Run manually or add **Schedule Trigger** (e.g. 1st day of month)

### 3. Run

Click **Execute workflow** — new rows appear in your Google Sheet.

---

## Cost

| Component | Tool | Cost |
|-----------|------|------|
| Automation | n8n (self-hosted) | $0 |
| LLM | Groq free tier | $0 |
| Data source | dev.to public API | $0 |
| Storage | Google Sheets | $0 |
| **Total** | | **$0/month** |

Groq free tier is enough for ~10 articles per run. For larger volumes — Groq paid or swap to OpenAI/Anthropic.

---

## Customization

| What to change | Where |
|----------------|-------|
| Article source / tag | `Fetch dev.to AI articles` node URL |
| Popularity threshold | `Filter popular articles` node (`>5` reactions) |
| Summary language / tone | `Information Extractor` prompt text |
| LLM model | `Groq Chat Model` node |
| Sheet columns | Apps Script + HTTP body in `Send to Google Sheet` |
| Run frequency | Replace Manual Trigger with Schedule Trigger |

> ⚠️ **Note:** Output quality depends on prompt and model. With 10 articles per run, results are good for a draft digest — review and edit rows in Sheets before publishing.

---

## Project Structure

```
n8n_ai-news-digest/
├── workflow/
│   └── ai_news_digest.json    # n8n workflow (import this)
├── .env.example             # Groq + Apps Script URL template
├── .gitignore
└── README.md
```

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Automation | n8n |
| Data source | dev.to REST API |
| LLM | Groq (qwen3.6-27b) via LangChain node |
| Output | Google Sheets + Apps Script |

---

## Українська

### Що це

Автоматизація для **щомісячного AI-дайджесту**: n8n забирає популярні статті з dev.to, Groq робить короткий висновок українською, результат потрапляє в Google Таблицю.

### Як запустити

1. Створи Google Таблицю + Apps Script (Web App URL)
2. Імпортуй `workflow/ai_news_digest.json` в n8n
3. Підключи Groq API key
4. Заміни URL Apps Script у ноді **Send to Google Sheet**
5. Запусти workflow вручну або по розкладу

### Бюджет

**$0/міс** — dev.to API, Groq free tier, Google Sheets безкоштовно.
