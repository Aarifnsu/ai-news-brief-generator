# AI News Brief Generator (Project 1 of 5)

Automatically collects public news from trusted RSS sources, analyzes each article with an AI model, and publishes a formatted daily brief to Google Docs (with an optional email notification).

## What it does

Every morning at 08:00, the workflow:
1. Pulls the latest articles from 5 public RSS feeds
2. Combines them, keeps only the top 10, and removes duplicates
3. Sends each article to an AI model (Google Gemini) with a fixed analyst prompt
4. Formats the results into a single report
5. Creates a Google Doc in a dedicated Drive folder and writes the report into it
6. Optionally emails a notification with a link to the doc

## Data sources (where the data comes from)

All sources are public RSS feeds — no scraping, no private APIs:

| Source | Feed URL | Covers |
|---|---|---|
| BBC World | `https://feeds.bbci.co.uk/news/world/rss.xml` | International news |
| Al Jazeera | `https://www.aljazeera.com/xml/rss/all.xml` | International news |
| UN News | `https://news.un.org/feed/subscribe/en/news/all/rss.xml` | Global / institutional news |
| Bangladesh (Daily Star) | `https://www.thedailystar.net/rss.xml` | Bangladesh news |
| TechCrunch AI | `https://techcrunch.com/category/artificial-intelligence/feed/` | AI industry news |

Each feed returns article title, link, publish date, and a short snippet — this raw data is what gets passed to the AI model, nothing is invented.

## AI analysis

Model: **Google Gemini 2.5 Flash** (free tier), called through n8n's LangChain Agent node.

System prompt given to the model:
> You are an AI News Analyst. Analyze each article and produce a concise, factual Markdown block with Headline, Summary (3 sentences), Category, Keywords (5), and Importance (High/Medium/Low). Do not invent facts not present in the input.

Categories used: Politics, Technology, Economy, International, Security, Sports, Business, Health.

## Tech stack

- **n8n** — workflow orchestration and scheduling
- **RSS Feed Read node** — pulls source data
- **Merge / Limit / Remove Duplicates nodes** — cleans the combined feed
- **LangChain Agent + Gemini Chat Model node** — AI analysis
- **Code node** — formats the final report text
- **Google Docs node** — creates and writes the document
- **Gmail node** — optional notification

## Workflow diagram

```
5 RSS feeds ──► Combine + Top 10 + Dedupe ──► AI News Analyst (Gemini)
                                                       │
                                                       ▼
                                        Merge into report text
                                                       │
                                                       ▼
                                     Create + write Google Doc
                                                       │
                                                       ▼
                                      Gmail notification (optional)
```

## Setup

1. Import `workflow.json` into n8n (or open the live workflow link shared separately).
2. Attach credentials:
   - **Google Gemini API** (free tier) on the "Gemini Model" node
   - **Google Docs OAuth2** on "Create Google Doc" and "Insert Report Text" (requires a Google Cloud OAuth client — Client ID + Secret — with the Google Docs API and Google Drive API enabled)
   - **Gmail OAuth2** on the notification node (optional)
3. Set the target Drive folder ID on the "Create Google Doc" node.
4. Activate the schedule trigger, or run manually with "Execute workflow".

## Output example

Each run produces a Google Doc titled `Daily News Brief <date>` containing one block per article:

```
Headline: ...
Summary: ...
Category: ...
Keywords: ...
Importance: High/Medium/Low
```

## Roadmap (planned extensions)

- AI translation (English → Bengali)
- Sentiment analysis per article
- Trend detection across days
- Telegram notification
- PDF export
- Weekly roll-up report

---
Part of a 5-project AI automation series: News Brief Generator, Social Media Trend Collector, Public Sentiment Analyzer, Competitor Monitoring, and Daily Intelligence Report Generator — all built on the same n8n + AI + Google Docs pattern.
