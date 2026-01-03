# AI-Powered Facebook Auto-Reply Workflow (n8n)

A robust automation system built with n8n to automatically monitor and reply to Facebook comments using Artificial Intelligence. This workflow ensures personalized engagement while preventing duplicate replies using Google Sheets as a database.

## 🚀 Features
- **Multi-Post Monitoring**: Automatically fetches recent posts from a Facebook Page and scans for comments.
- **AI-Driven Responses**: Generates human-like, context-aware replies using AI models (Groq/OpenAI).
- **Duplicate Prevention**: Tracks replied comments in Google Sheets to ensure no comment is answered twice.
- **Scalable Processing**: Handles multiple comments across different posts in a single execution.

## 🛠️ Tech Stack
- **n8n**: Workflow automation platform.
- **Facebook Graph API**: (Via Community Node) to fetch posts, comments, and send replies.
- **Google Sheets**: Acts as a database for tracking `comment_id`.
- **AI Model**: Groq/OpenAI for generating responses.

## 📦 Special Requirements (Important)
This workflow utilizes a specific community node for Facebook interactions:
* **Community Node**: `@umairabubakkar/n8n-nodes-private-fb`
* **Installation**: You must install this node in your n8n instance via **Settings > Community Nodes > Install**.
* **Alternative**: If you cannot install community nodes, you will need to manually replace these nodes with the official **n8n Facebook nodes** and re-map the fields.

### 🛠️ How to Install the Facebook Community Node
To use this workflow, you need to install the custom Facebook node manually in your n8n instance:
1. Open your **n8n** dashboard.
2. Go to **Settings** (bottom left corner).
3. Click on **Community Nodes**.
4. Click on **Install a community node**.
5. Copy and paste this package name: `@umairabubakkar/n8n-nodes-private-fb`.
6. Click **Install**.
7. Once installed, the nodes will automatically connect in the imported workflow.

## 📋 Prerequisites
- An **n8n** instance (Cloud or Self-hosted).
- **Facebook Developer Account**: With a Page Access Token.
- **Google Cloud Console**: Access to Google Sheets API.
- **AI API Key**: (e.g., Groq or OpenAI).

## ⚙️ Setup Instructions
1. **Install Community Node**: Install `@umairabubakkar/n8n-nodes-private-fb` first.
2. **Import Workflow**: Download the `.json` file and import it into n8n.
3. **Configure Credentials**: Set up your Facebook, Google Sheets, and AI credentials.
4. **Database Setup**: Create a Google Sheet with columns for `comment_id` and `post_id`.
5. **Activate**: Set the `Schedule Trigger` frequency (e.g., every 15 minutes).

## ⚠️ Important Note
Credentials and sensitive API keys are **not** included in the exported JSON file. You must configure them manually within your n8n environment.