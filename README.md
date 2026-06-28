# n8n AI News Digest

> **Fetch the latest AI articles from dev.to, filter by rating, extract topic and summary in Ukrainian — save to Google Sheets.**

![Budget](https://img.shields.io/badge/Budget-%240–5%2Fmo-brightgreen)
![Stack](https://img.shields.io/badge/Stack-n8n%20%2B%20Groq%20%2B%20Google%20Sheets-orange)
![Language](https://img.shields.io/badge/Output-Ukrainian-yellow)

---

## What This Is

An n8n workflow that **curates a short AI news digest on each run** — not a monthly batch job.

On every execution it:

1. **Fetches the latest 10 articles** from dev.to (`tag=ai`, `per_page=10`)
2. **Filters by quality** — keeps only articles with **>5 reactions**, a non-empty description, and a valid URL
3. **For each article**, Groq LLM:
   - takes the **original title**
   - **determines the topic/category**
   - writes a **short Ukrainian summary** of the news description
   - shortens the title to **3–4 words in Ukrainian**
4. **Saves rows** to Google Sheets via Apps Script

**Example output row:**

| title | category | summary | url | status |
|-------|----------|---------|-----|--------|
| Нові моделі LLM | AI Tools | Коротка вигрузка суті новини українською... | https://dev.to/... | Готово |

---

## How It Works (step by step)

```
Manual Trigger (or Schedule)
        │
        ▼
dev.to API — last 10 AI articles
        │
        ▼
Filter — reactions > 5, description & URL present
        │
        ▼
Information Extractor (Groq qwen3.6-27b)
  • title (UA, 3–4 words)
  • category / topic
  • summary_ua — short excerpt of the news
  • url
        │
        ▼
Google Apps Script → Google Sheets
```

**Important:** the workflow does **not** collect articles for a calendar month. Each run processes **whatever is currently in the top 10** from dev.to at that moment. Run it daily, weekly, or manually — you decide.

---

## Quick Start

### 1. Google Sheets + Apps Script

1. Create a Google Sheet with columns: `title`, `url`, `category`, `summary`, `status`
2. **Extensions → Apps Script** — add a `doPost(e)` handler that appends rows
3. **Deploy → New deployment → Web app** (Execute as: Me, Access: Anyone)
4. Copy the Web App URL

### 2. Import workflow in n8n

1. Open n8n → **Import from file** → `workflow/ai_news_digest.json`
2. In **Send to Google Sheet** — replace `YOUR_SCRIPT_ID` with your Apps Script URL
3. In **Groq Chat Model** — add Groq API credentials
4. Run manually, or add a **Schedule Trigger** (e.g. once per day)

### 3. Run

Click **Execute workflow** — filtered articles appear as new rows in your Sheet.

---

## Cost

| Component | Tool | Cost |
|-----------|------|------|
| Automation | n8n (self-hosted) | $0 |
| LLM | Groq free tier | $0 |
| Data source | dev.to public API | $0 |
| Storage | Google Sheets | $0 |
| **Total** | | **$0/month** |

Groq free tier is enough for up to 10 articles per run. For more articles — increase `per_page` in the HTTP node or switch to a paid LLM.

---

## Customization

| What to change | Where |
|----------------|-------|
| Number of articles | `Fetch dev.to AI articles` — `per_page=10` |
| Tag / source | Same node — `tag=ai` in URL |
| Rating threshold | `Filter popular articles` — `>5` reactions |
| Summary length / tone | `Information Extractor` prompt |
| Title language | Prompt: "Заголовок українською, 3–4 слова" |
| LLM model | `Groq Chat Model` node |
| Run frequency | Manual Trigger → Schedule Trigger |

> ⚠️ **Note:** Results are draft-quality. Category and summary depend on the prompt and model — review rows in Sheets before publishing.

---

## Project Structure

```
n8n_ai-news-digest/
├── workflow/
│   └── ai_news_digest.json    # n8n workflow (import this)
├── .env.example               # Groq + Apps Script URL template
├── .gitignore
└── README.md
```

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Automation | n8n |
| Data source | dev.to REST API (latest 10 articles) |
| LLM | Groq (qwen3.6-27b) via Information Extractor |
| Output | Google Sheets + Apps Script |

---

## Українська

### Що це

Автоматизація **AI-дайджесту новин** — не щомісячний звіт, а обробка **останніх 10 статей** з dev.to за один запуск.

### Що робить workflow

1. Забирає **10 останніх статей** з тегом `ai` через dev.to API
2. **Відсіює** статті з рейтингом ≤5 або без опису / URL
3. Для кожної статті, що пройшла фільтр, Groq:
   - бере **назву** (перекладає і стискає до 3–4 слів українською)
   - **визначає тему / категорію**
   - робить **коротку вигрузку** опису новини українською
4. Записує рядки в **Google Таблицю**

### Як запустити

1. Google Таблиця + Apps Script (Web App URL)
2. Імпорт `workflow/ai_news_digest.json` в n8n
3. Groq API key + URL Apps Script у workflow
4. Запуск вручну або по розкладу (наприклад, раз на день)

### Бюджет

**$0/міс** — dev.to API, Groq free tier, Google Sheets.
