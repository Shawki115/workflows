# 🚀 WhatsApp AI Command Center: Intelligent Multi-Agent Orchestrator

An advanced, multimodal personal assistant ecosystem built on **n8n**. This system transforms WhatsApp into a powerful command center, leveraging the **Evolution API** as a professional, open-source gateway for seamless integration, and **Large Language Models (LLMs)** to orchestrate specialized sub-agents.



---

## 🏗 System Architecture

The workflow follows a **Modular Orchestrator-Worker Pattern**:

1.  **Ingress Layer:** Inbound messages (Text, Audio, Image) are received via **Evolution API** webhooks. 
    * *Note: This project uses Evolution API instead of standard WhatsApp to provide an enterprise-grade, open-source integration layer.*
2.  **Multimodal Pre-processing:**
    * **Voice Notes:** Transcribed using OpenAI Whisper API.
    * **Images:** Analyzed using GPT-4o-mini Vision to generate textual descriptions.
    * **Text:** Routed directly for intent analysis.
3.  **Security Layer:** An internal filter validates the `remoteJid` to ensure only authorized phone numbers can trigger the system.
4.  **Orchestrator Agent (The Brain):** A central agent that evaluates user intent and context (using a Buffer Memory Window) to route tasks to specific workers.
5.  **Specialized Sub-Agents (Tools):**
    * 📧 **Mail-Agent:** Handles Gmail operations (Send, Read, Search, Delete).
    * 📅 **Schedule-Agent:** Manages Google Calendar (Events creation, Updates, Availability).
    * 🔍 **Research-Agent:** Real-time web access via SerpApi (Google Search), Wikipedia, and Hacker News.
    * 🐦 **Social-Agent:** Drafts trending content for X (Twitter) and logs them into Google Sheets for review.
6.  **Egress Layer:** Finalized responses are formatted and delivered back to the user via Evolution API.

---

## 🛠 Tech Stack

* **Automation Engine:** [n8n](https://n8n.io/) (Self-hosted via Docker).
* **WhatsApp Gateway:** [Evolution API](https://github.com/EvolutionAPI/evolution-api) (The open-source WhatsApp integration API).
* **Intelligence:** OpenAI GPT-4o / Gemini 2.0 Flash.
* **External APIs:** Gmail, Google Calendar, SerpApi.

---

## 🚀 Installation & Setup

### 1. Evolution API Installation
To set up the WhatsApp gateway, follow the official documentation and installation guide here:
👉 **[Evolution API GitHub Repository](https://github.com/EvolutionAPI/evolution-api)**

### 2. Workflow Deployment
1.  Import the `WhatsApp_AI_Command_Center.json` into your n8n dashboard.
2.  **Configure Credentials:**
    * Link your `OpenAI` or `Google Gemini` accounts.
    * Connect your `Gmail` and `Google Calendar` via OAuth2.
3.  **Authorization:** Update the "Security Filter" node with your authorized WhatsApp JID (e.g., `1234567890@s.whatsapp.net`).
4.  **Webhook Integration:** Copy the Production Webhook URL from n8n and set it in your Evolution API instance under the `messages.upsert` event.

---

## ✨ Why Evolution API?

Unlike standard WhatsApp personal accounts, **Evolution API** provides:
* **Open Source:** Full control over your data and infrastructure.
* **REST API:** Easy integration with any language or tool.
* **Multi-Instance:** Support for managing multiple WhatsApp numbers from a single server.
* **Webhooks:** Real-time event handling (messages, status, presence).

---

## 🔐 Security & Best Practices

* **Access Control:** The workflow verifies the `remoteJid`. Only authorized numbers can execute commands.
* **Network-Level Security:** We recommend deploying both n8n and Evolution API behind a reverse proxy (e.g., Cloudflare Tunnel or Nginx) with SSL enabled.
* **Environment Variables:** Keep your API keys and sensitive tokens in n8n's credential store, never hardcoded.

---

## 🛠 Troubleshooting

* **Media Fetching:** Ensure the Evolution API has correct file permissions to pass Base64 image/audio data to n8n.
* **Agent Loops:** If the agent fails to pick a tool, refine the "Tool Description" in the worker nodes.
* **Webhook Timeouts:** Consider adjusting your proxy's timeout settings for long-running research tasks.

---