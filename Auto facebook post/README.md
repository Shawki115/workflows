# 🤖 AI-Powered News Curator & Facebook Automator

This n8n workflow automates the process of fetching, ranking, and publishing AI news. It integrates a "Human-in-the-loop" mechanism via Gmail to ensure high-quality content selection before posting to Facebook.

![Project Preview](./images/Auto facebook post.PNG)


---

## 🛠 Workflow Architecture

The automation follows a structured pipeline:

1.  **Trigger:** Daily scheduled check at 1:00 PM (Asia/Aden).
2.  **Ingestion:** Fetches the latest tech news via RSS (VentureBeat).
3.  **Ranking (AI):** Google Gemini 2.0 Flash analyzes the feed and ranks the top 5 most important topics.
4.  **Verification (Human-in-the-loop):** Sends the ranked list to Gmail. The workflow pauses using the `Send and Wait` node until the user provides the preferred URL.
5.  **Extraction:** Once the URL is received, **Firecrawl** performs a deep scrape of the article content.
6.  **Content Creation (AI):** Gemini transforms the raw article into a professional, engaging Facebook post in English.
7.  **Publishing:** The final post is published automatically to the Facebook Page via Graph API.

---

## 🏗 Prerequisites

* **n8n Instance:** (Docker-hosted recommended).
* **Google Gemini API Key:** For content analysis and generation.
* **Firecrawl API Key:** For advanced web scraping and markdown conversion.
* **Facebook Developer App:** With `pages_manage_posts` and `pages_read_engagement` permissions.
* **Gmail (OAuth2):** To handle the notification and feedback loop.

---

## 📋 Node Breakdown

### 1. Daily Schedule & RSS
- **Schedule Trigger:** Sets the heartbeat of the automation.
- **RSS Read:** Monitors `https://venturebeat.com/feed/` for new entries.

### 2. The Ranking Logic
- **Aggregate Node:** Consolidates multiple RSS items into a single batch for the AI.
- **Gemini (Ranker):** Uses a custom prompt to filter news based on relevance and impact.

### 3. Human Feedback Loop
- **Gmail (Send and Wait):** This is the core control node. It sends an interactive email. The workflow stops and waits for the user to reply with the specific URL they want to promote.



### 4. Content Processing
- **Firecrawl:** Scrapes the target URL and converts it into clean Markdown for the AI to read easily.
- **Gemini (Copywriter):** Optimized with a professional prompt to create Facebook posts following specific formatting rules (short paragraphs, emojis, and hashtags).

### 5. Social Integration
- **Facebook Graph API:** Executes a POST request to the `/feed` edge of the specified Page ID.

---

## ⚙️ Configuration (.env)

Ensure the following environment variables or credentials are set in your n8n instance:

```env
N8N_EMAIL_USER=your-email@gmail.com
FB_PAGE_ID=your_page_id
GEMINI_API_KEY=your_key
FIRECRAWL_API_KEY=your_key