# Automated Incident Response System

An n8n workflow that automatically triggers multi-channel alerts when
an incident is reported via webhook. Built to demonstrate real-world 
DevOps automation using no-code/low-code tooling.

---

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [How It Works](#how-it-works)
- [Setup & Configuration](#setup--configuration)
- [Usage](#usage)
- [Notification Channels](#notification-channels)
- [Known Limitations](#known-limitations)
- [Screenshots](#screenshots)

---

## Overview

In a production environment, incident response time is critical. 
This workflow automates the alerting process by receiving an incident
payload via HTTP webhook and simultaneously notifying the relevant teams 
across multiple channels — Slack, SMS, Email, and logging the incident
to Google Sheets for tracking.

---

## Architecture

```
Monitoring Tool / API Client
        |
        | POST /webhook (JSON payload)
        v
   [Webhook Node]
        |
        v
   [Edit Fields] ──── maps & enriches payload
        |
   ┌────┼────┬─────────┐
   v    v    v         v
[Slack] [SMS] [Email] [Google Sheets]
```

The workflow is hosted on n8n Cloud at `thepresentdevopsengineer.cloud`.

---

## Features

- Webhook-triggered automation via HTTP POST
- Automatic incident ID generation using timestamp
- Multi-channel notification: Slack, SMS, Email, Google Sheets
- Incident logging with status tracking
- Configurable alert message templates

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| n8n Cloud | Workflow automation engine |
| Twilio | SMS notifications |
| Slack API | Team messaging alerts |
| SMTP (Gmail) | Email notifications |
| Google Sheets | Incident log/tracker |
| ReqBin | Webhook testing |

---

## How It Works

1. An external monitoring tool or API client sends a POST request to
2.  the webhook URL with an incident payload
3. The **Webhook node** receives and passes the data downstream
4. The **Edit Fields node** maps the incoming fields and auto-generates
5.  an `incident_id` and `timestamp`
6. The workflow simultaneously triggers:
   - A Slack message to the `#devops` channel
   - An SMS alert via Twilio
   - An email alert via SMTP
   - A new row appended to a Google Sheet for incident logging

---

## Setup & Configuration

### Prerequisites

- n8n Cloud account (or self-hosted n8n)
- Twilio account with a verified phone number
- Slack workspace with a bot token and a `#devops` channel
- Gmail account with SMTP credentials
- Google Sheets with appropriate column headers

### Webhook Payload Format

Send a POST request to your webhook URL with the following JSON body:

```json
{
  "alert_name": "Server Down",
  "severity": "critical",
  "message": "Production server is unresponsive. Immediate attention required."
}
```

### Required Credentials in n8n

- `Slack account` — OAuth2 bot token with `chat:write` scope
- `Twilio account` — Account SID and Auth Token
- `SMTP account` — Gmail SMTP credentials
- `Google Sheets account` — OAuth2 Google credentials

### Google Sheets Column Headers

Ensure your sheet has the following headers:

| incident_id | alert_name | severity | timestamp | status |

---

## Usage

### Testing with ReqBin or curl

```bash
curl -X POST https://thepresentdevopsengineer.cloud/webhook/nov7iMhPqWRlh52P \
  -H "Content-Type: application/json" \
  -d '{
    "alert_name": "Server Down",
    "severity": "critical",
    "message": "Production server is unresponsive."
  }'
```

**Expected response:**

```json
{
  "message": "Workflow was started"
}
```

---

## Notification Channels

### Slack
- Sends a formatted critical alert message to the `#devops` channel
- Includes incident ID, alert name, severity, and status

### SMS (Twilio)
- Sends an SMS to the configured phone number
- **Note:** Twilio trial accounts restrict SMS delivery to verified numbers
-  only and have geographic limitations for certain countries (e.g. Nigeria +234).
-   Upgrading to a paid Twilio account or using an alternative provider such
-   as [Termii](https://termii.com) or [Africa's Talking](https://africastalking.com)
-    resolves this for Nigerian numbers.

### Email (SMTP)
- Sends a critical alert email with subject: `Critical Alert: {alert_name}`
- Confirmed working via Gmail SMTP

### Google Sheets
- Appends a new row to the incident log sheet with all incident fields
- Confirmed working — rows append in real time on each trigger

---

## Known Limitations

- Twilio trial account cannot send SMS to unverified or geographically restricted
- numbers (+234 Nigeria). This is a Twilio trial limitation, not a workflow issue.
- The node is correctly configured and would work on a paid account.
- Slack delivery depends on Slack service availability. During testing, a Slack
-  platform outage temporarily affected message delivery (confirmed via slack-status.com).

---

## Screenshots

> Screenshots are located in the `/screenshots` folder of this repository.

| Screenshot | Description |
|------------|-------------|
| `workflow-overview.png` | Full n8n workflow diagram |
| `webhook-200ok.png` | Successful POST request returning 200 OK |
| `execution-success.png` | n8n execution showing all nodes triggered |
| `email-node-success.png` | Email node executed successfully |
| `email-inbox.png` | Received alert email in Gmail inbox |
| `sheets-node-success.png` | Google Sheets node executed successfully |
| `sheets-row-appended.png` | New incident row in Google Sheet |
| `slack-node-config.png` | Slack node configuration |
| `twilio-node-config.png` | Twilio SMS node configuration |
| `twilio-verified-callers.png` | Verified Caller IDs in Twilio console |

---

## Author

Isaac Ambi — DevOps Engineer  
[GitHub](https://github.com/isaacambi) | [LinkedIn](https://www.linkedin.com/in/isaac-ambi-012b75135/)
