# 🤖 AI-Powered Customer Support Telegram Bot

[![n8n](https://img.shields.io/badge/n8n-0.238.0-blue.svg)](https://n8n.io/)
[![Gemini](https://img.shields.io/badge/Gemini-1.5--Flash-orange.svg)](https://ai.google.dev/)
[![Telegram](https://img.shields.io/badge/Telegram-BotAPI-blue.svg)](https://core.telegram.org/bots/api)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## 📋 Table of Contents
- [Overview](#-overview)
- [Key Features](#-key-features)
- [Architecture](#-architecture)
- [Prerequisites](#-prerequisites)
- [Step-by-Step Setup](#-step-by-step-setup)
- [Workflow Configuration](#-workflow-configuration)
- [Advanced Features](#-advanced-features)
- [Testing & Deployment](#-testing--deployment)
- [Future Roadmap](#-future-roadmap)
- [Troubleshooting](#-troubleshooting)

---

## 🚀 Overview

This is a production-ready, AI-powered customer support chatbot built using **n8n automation platform**, **Google Gemini AI**, and **Google Sheets**. The bot intelligently analyzes customer messages, detects intent and sentiment, answers FAQs from a dynamic knowledge base, and automatically creates support tickets with priority levels when needed.

---

## ✨ Key Features

### 🤖 Intelligent Classification
- **Intent Detection**: Accurately identifies user intent (`faq`, `login_issue`, `bug`, `escalation`)
- **Sentiment Analysis**: Detects user mood (`positive`, `neutral`, `negative`)
- **Priority System**: Auto-assigns priority (`low`, `medium`, `high`) based on urgency

### 📊 Dynamic Knowledge Base
- **FAQ Matching**: Searches Google Sheets for exact question matches
- **Auto-Learning**: Unanswered questions automatically added to FAQ sheet for future reference
- **Multi-Sheet Database**: Separate sheets for Customers, Tickets, FAQ, and Logs

### 🎟️ Smart Ticket Management
- **Auto Ticket Creation**: Generates tickets when FAQ has no answer or user escalates
- **Priority Integration**: Tickets include user sentiment and priority details
- **Team Notifications**: Sends formatted alerts to support team via Telegram
- **Bug Detection**: Automatic identification of error codes and system crashes

### 🔄 Memory & Analytics
- **Session Memory**: Remembers user conversation context
- **Similar Ticket Search**: Prevents duplicate tickets
- **Follow-up Automation**: Scheduled ticket status checks
- **Activity Logging**: Tracks all interactions for analytics

---

## 🏗️ Architecture

### System Flow Diagram
```
┌─────────────────────────────────────────────────────────────────┐
│                        USER INPUT                               │
│                  (Telegram / Web)                               │
└─────────────────────┬───────────────────────────────────────────┘
                      ▼
┌─────────────────────────────────────────────────────────────────┐
│                   n8n Webhook / Trigger                         │
│                   (Telegram Trigger)                            │
└─────────────────────┬───────────────────────────────────────────┘
                      ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Data Extraction                              │
│              (Edit Fields - Set Node)                          │
│         Extracts: chat_id, message, username                   │
└─────────────────────┬───────────────────────────────────────────┘
                      ▼
┌─────────────────────────────────────────────────────────────────┐
│              AI Intent Classifier                               │
│            (Gemini 1.5 Flash Model)                           │
│  Output: {intent, sentiment, priority, needs_ticket}          │
└─────────────────────┬───────────────────────────────────────────┘
                      ▼
              ┌───────┴───────┐
              │               │
          Decision          Decision
           Engine           Engine
              │               │
      ┌───────┼───────┬───────┴───────┬───────┐
      │       │       │               │       │
   login   FAQ     Bug          Escalation  Other
   Issue   Search  Detection     Handler   Intents
      │       │       │               │       │
      │       ▼       │               ▼       │
      │   Google      │          Ticket     Custom
      │   Sheets      │          Create     Reply
      │   Lookup      │               │       │
      │   (answer)    │               │       │
      │       │       │               │       │
      ▼       ▼       ▼               ▼       ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Response Handler                             │
│          Telegram Reply / Ticket Creation / Logging            │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📋 Prerequisites

### Required Accounts & Tools
| Service | Purpose | Cost |
|---------|---------|------|
| [n8n](https://n8n.io/) | Automation platform | Free (Self-hosted) |
| [Google Cloud Console](https://console.cloud.google.com/) | Google Sheets API & Service Account | Free |
| [Google AI Studio](https://aistudio.google.com/) | Gemini API Key | Free (with limits) |
| [Telegram Bot](https://core.telegram.org/bots#6-botfather) | Chat interface | Free |
| [Google Sheets](https://sheets.google.com/) | Database & Knowledge Base | Free |

---

## 🛠️ Step-by-Step Setup

### Step 1: Install n8n

#### Option A: Local Installation (npm)
```bash
npm install n8n -g
n8n start
```

#### Option B: Docker Installation (Recommended)
```bash
docker run -it --rm \
  -p 5678:5678 \
  n8nio/n8n
```

Access n8n at: `http://localhost:5678`

---

### Step 2: Create Telegram Bot

1. Open Telegram and search for `@BotFather`
2. Send the command:
   ```
   /newbot
   ```
3. Choose a name for your bot
4. Choose a username (must end with `bot`)
5. Save the **API Token** provided by BotFather

![BotFather Setup](images/telegram_botfather.png)

---

### Step 3: Set Up Google Sheets Database

Create a new Google Spreadsheet with the following 4 sheets:

#### 📊 Sheet 1: `Customers`
| customer_id | name | email | phone |
|-------------|------|-------|-------|
| 123456789   | John Doe | john@email.com | +1234567890 |

#### 🎟️ Sheet 2: `Tickets`
| ticket_id | customer_id | issue | status | priority | date |
|-----------|-------------|-------|--------|----------|------|
| TK-001    | 123456789   | Login error | Open | High | 2024-01-01 |

#### ❓ Sheet 3: `FAQ`
| question | answer |
|----------|--------|
| What is your office timing? | Our office is open from 9 AM to 6 PM |
| How to reset password? | Click 'Forgot Password' on login page |

#### 📋 Sheet 4: `Logs`
| time | user_message | ai_reply | intent | sentiment |
|------|--------------|----------|--------|-----------|
| 2024-01-01 10:00 | Hi | Hello! | greeting | positive |

---

### Step 4: Get Gemini API Key

1. Visit [Google AI Studio](https://aistudio.google.com/)
2. Click on **"Get API Key"**
3. Create a new API key
4. Copy and save the API key
5. ⚠️ **Important**: Keep this key secure

---

### Step 5: Google Cloud Service Account Setup

#### Create Service Account:
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing
3. Enable **Google Sheets API** and **Google Drive API**
4. Navigate to **IAM & Admin → Service Accounts**
5. Click **Create Service Account**
6. Assign role: **Editor**
7. Click **Create Key** → Select **JSON**
8. Download the JSON key file

#### Service Account JSON Structure:
```json
{
  "type": "service_account",
  "project_id": "your-project-id",
  "private_key_id": "abc123...",
  "private_key": "-----BEGIN PRIVATE KEY-----\n...",
  "client_email": "your-service@project.iam.gserviceaccount.com",
  "client_id": "1234567890",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://oauth2.googleapis.com/token"
}
```

#### Share Google Sheet:
1. Open your Google Sheet
2. Click **Share** button
3. Add the `client_email` from the JSON file
4. Grant **Editor** permission
5. Click **Send**

---

### Step 6: n8n Workflow Configuration

#### Node 1: Telegram Trigger
```yaml
Node Type: Telegram Trigger
Event: Message Received
Credential: Your Bot Token
```

#### Node 2: Edit Fields (Set Data)
```yaml
Node Type: Edit Fields (Set)
Fields:
  - Name: chat_id
    Value: {{ $json.message.chat.id }}
  - Name: message
    Value: {{ $json.message.text }}
  - Name: username
    Value: {{ $json.message.from.username || $json.message.from.first_name }}
```

![Edit Fields Configuration](images/edit_fields_node.png)

#### Node 3: Gemini AI Classifier
```yaml
Node Type: Message a model (Google Gemini)
Model: gemini-1.5-flash
Credential: Your Gemini API Key
Output Content as JSON: ON
```

**System Prompt:**
```
You are an advanced AI Customer Support Classifier. Analyze the user's input message and return a valid JSON object ONLY. Do not include any markdown styling, backticks, or prose.

Analyze the message based on these parameters:
1. "intent": Choose exactly one from:
   - "login_issue": If user mentions login, password reset, or OTP problems.
   - "faq": If user asks a general query that can be answered via documentation.
   - "escalation": If user specifically demands a human agent, manual help, or threatens to leave.
   - "bug": If user shares error codes (e.g., 404, NullPointerException), logs, system crashes, or code faults.

2. "sentiment": Choose exactly one from:
   - "positive": User is polite, happy, or thankful.
   - "neutral": General question, normal expressionless query.
   - "negative": User is angry, using caps, expressing frustration, or complaining.

3. "priority": Determine the urgency based on sentiment and intent:
   - Set to "high" if sentiment is "negative", or intent is "bug", or intent is "escalation".
   - Set to "medium" if intent is "login_issue".
   - Set to "low" for general "faq" with a neutral/positive sentiment.

4. "needs_ticket": true (if intent is escalation, bug, or login_issue), false otherwise.

Output Format (Strictly valid JSON):
{
  "intent": "bug",
  "sentiment": "negative",
  "priority": "high",
  "needs_ticket": true
}
```

---

### Step 7: Decision Engine (IF Nodes)

#### IF Node 1: Login Issue Check
```yaml
Condition: {{ $node["Message a model"].json.intent }} equals login_issue
True Path: Send troubleshooting message
False Path: Go to FAQ Check
```

#### IF Node 2: FAQ Check
```yaml
Condition: {{ $node["Message a model"].json.intent }} equals faq
True Path: Google Sheets Lookup
False Path: Go to Escalation Check
```

#### IF Node 3: Escalation Check
```yaml
Condition: {{ $node["Message a model"].json.intent }} equals escalation OR bug
True Path: Create Ticket
False Path: Default response
```

---

### Step 8: Google Sheets FAQ Lookup

```yaml
Node Type: Google Sheets
Operation: Get Row(s)
Document: Your Spreadsheet URL
Sheet: FAQ
Filter: question equals {{ $node["Edit Fields"].json.message }}
Settings: Always Output Data (ON)
```

---

### Step 9: Ticket Creation

#### Support Team Notification:
```html
⚠️ <b>New Support Ticket Created!</b>

<b>User ID:</b> {{ $node["Edit Fields"].json.chat_id }}
<b>Unanswered Question:</b> {{ $node["Edit Fields"].json.message }}

📊 <b>Details:</b>
• <b>Priority:</b> {{ $node["Message a model"].json.priority.toUpperCase() }}
• <b>User Sentiment:</b> {{ $node["Message a model"].json.sentiment }}

<i>Please reply to this user as soon as possible.</i>
```

#### User Confirmation:
```
Dear user,

We have received your request. A support ticket has been created for your issue. Our team will contact you shortly.

Thank you for your patience.
```

---

### Step 10: Log Everything

```yaml
Node Type: Google Sheets
Operation: Append Row
Sheet: Logs
Data:
  - time: {{ $now }}
  - user_message: {{ $node["Edit Fields"].json.message }}
  - ai_reply: {{ $json.response }}
  - intent: {{ $node["Message a model"].json.intent }}
  - sentiment: {{ $node["Message a model"].json.sentiment }}
```

---

## 🔧 Advanced Features Implementation

### 1. Sentiment & Priority System
Already integrated in Gemini prompt. The classifier returns sentiment and priority automatically.

### 2. Bug Detection
Intent list includes `bug` which triggers immediate high-priority ticket creation.

### 3. Auto-FAQ Learning
When a ticket is created, automatically append the question to FAQ sheet:
```yaml
Google Sheets Append Row
Sheet: FAQ
Data:
  question: {{ $node["Edit Fields"].json.message }}
  answer: "Pending Review"
```

### 4. Memory System (Optional)
Add a **Window Buffer Memory** node after the Telegram trigger to remember conversation context.

### 5. Auto Follow-up (New Workflow)
Create a separate workflow:
```yaml
Trigger: Schedule (24 hours interval)
Google Sheets: Get Rows with status "Open" and date > 24 hours
Telegram: Send follow-up message
Google Sheets: Update status to "Followed Up"
```

### 6. Similar Ticket Search
Before creating a new ticket:
```yaml
Google Sheets: Get Rows where customer_id matches and status is "Open"
IF Node: If found, don't create new ticket
```

---

## 🧪 Testing & Deployment

### Test Workflow:
1. Send message to bot: "How to reset password?"
2. Check if bot replies from FAQ
3. Send: "I'm getting error 404" → Should create high-priority ticket
4. Send angry message: "I'm extremely frustrated!" → Should escalate

### Activate Workflow:
1. Click **Active** toggle in top-right corner
2. Click **Save** to save workflow
3. Test with real messages

---

## 🗺️ Future Roadmap

### Phase 2: Enhanced Intelligence
- [ ] **Multi-language Support**: Translate and respond in user's language
- [ ] **Voice Message Support**: Transcribe and analyze voice notes
- [ ] **Image Recognition**: OCR and analyze screenshots
- [ ] **Advanced Sentiment Analysis**: More nuanced emotion detection

### Phase 3: Business Integration
- [ ] **CRM Integration**: Salesforce, HubSpot, Zoho
- [ ] **Slack Notifications**: Real-time team alerts
- [ ] **Email Support**: Auto-reply to emails
- [ ] **Analytics Dashboard**: Google Looker Studio integration

### Phase 4: AI Improvements
- [ ] **RAG (Retrieval-Augmented Generation)**: Better context understanding
- [ ] **Agent Handoff**: Smooth transition to human agent
- [ ] **Smart Routing**: Assign tickets based on expertise
- [ ] **Predictive Analytics**: Predict ticket volume and issues

### Phase 5: Enterprise Features
- [ ] **Multi-Bot Support**: Separate bots for different departments
- [ ] **SLA Management**: Auto-escalation based on time
- [ ] **Knowledge Base Import**: Bulk upload from documents
- [ ] **Custom Workflows**: Drag-and-drop workflow builder

---

## 🐛 Troubleshooting

### Issue: "No output data returned" from Google Sheets
**Solution**: Ensure the question exists exactly as typed in the FAQ sheet. Enable "Always Output Data" in node settings.

### Issue: "Unsupported start tag br" in Telegram
**Solution**: Use plain text with line breaks instead of `<br>` tags, or use `\n` in Markdown mode.

### Issue: Priority/Sentiment showing as undefined
**Solution**: Use full path to Gemini node:
```javascript
{{ $node["Message a model"].json.priority }}
```
Instead of:
```javascript
{{ $json.priority }}
```

### Issue: Telegram bot not responding
**Solutions**:
1. Verify bot token is correct
2. Ensure workflow is **Active** (green toggle)
3. Check n8n is running (port 5678 accessible)
4. Verify Telegram webhook is set correctly

### Issue: Google Sheets permission denied
**Solution**: 
1. Check service account email has Editor access
2. Verify Google Sheets API is enabled
3. Re-generate service account key if expired

### Issue: Gemini API key not working
**Solution**:
1. Verify API key is active in Google AI Studio
2. Check if billing is enabled (free tier has limits)
3. Ensure correct model name (gemini-1.5-flash)

---

## 📊 Performance Metrics

| Metric | Expected Value |
|--------|---------------|
| Response Time | < 3 seconds |
| Intent Accuracy | > 90% |
| Sentiment Accuracy | > 85% |
| FAQ Match Rate | > 70% |
| Ticket Escalation | > 95% accuracy |
| Uptime | 99.9% |

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- [n8n](https://n8n.io/) for the amazing automation platform
- [Google](https://ai.google.dev/) for Gemini AI
- [Telegram](https://core.telegram.org/bots/api) for Bot API
- All contributors and testers

---

## 📞 Support

- **Documentation**: [Wiki](https://github.com/yourusername/chatbot/wiki)
- **Issues**: [GitHub Issues](https://github.com/yourusername/chatbot/issues)
- **Community**: [Discord Server](https://discord.gg/yourchannel)

---

**Built with ❤️ for the open-source community**

---

*"Automate Customer Support with Intelligence"*
