# Wazuh + n8n + Gemini AI Security Automation — Workflow Setup

## 1. Overview

This workflow automates the analysis and initial response of Wazuh security alerts.

### Workflow

Wazuh Alert
→ n8n Webhook
→ Prepare Wazuh Alert
→ Gemini AI Analysis
→ Parse AI Decision
→ Severity Decision
→ Automated Response / Notification / Incident Logging

The workflow is designed for an educational SOC/SOAR lab and should be used only on systems that you are authorized to monitor.

---

## 2. Requirements

- Wazuh Manager
- Wazuh Agent or another authorized event source
- n8n
- Docker (recommended for n8n)
- Google Gemini API access
- SMTP account if email notifications are enabled
- Wazuh API access if the firewall-block response is enabled

---

## 3. Import the n8n Workflow

1. Open your n8n instance.
2. Open **Workflows**.
3. Choose **Import from File**.
4. Select:

```text
n8n/Wazuh-AI-Security-Automation.json
```

5. Open the imported workflow.
6. Configure the required credentials and environment-specific values.
7. Save the workflow.
8. Activate it only after completing the configuration and testing.

> The GitHub workflow file is a sanitized template. It does not contain the original n8n credential references or instance-specific metadata.

---

## 4. Configure the Wazuh Webhook

The first node is:

```text
Wazuh Webhook
```

Configuration:

- Method: `POST`
- Path: `wazuh-alert`

The production webhook will normally have the following structure:

```text
http://<N8N_IP>:5678/webhook/wazuh-alert
```

Do not commit your real internal IP address if you want the repository to remain environment-independent.

---

## 5. Prepare Wazuh Alert Node

The **Prepare Wazuh Alert** code node extracts important information from the Wazuh event, including:

- Source IP
- Rule ID
- Rule description
- Wazuh severity/level
- Agent name
- Agent ID
- Original alert
- Raw alert JSON

The node is designed to accept either a normal Wazuh alert structure or the alert object inside a webhook request body.

---

## 6. Configure Gemini AI

The **Gemini AI Analysis** node sends the normalized Wazuh alert to Gemini.

The public workflow uses a placeholder:

```text
YOUR_GEMINI_API_KEY
```

Replace this with your own API credential using a secure n8n credential/environment configuration.

Do not place a real API key directly into the GitHub workflow file.

The AI is instructed to return JSON containing:

```text
attack_type
severity
explanation
recommended_action
source_ip
confidence
summary
```

Severity is restricted to:

```text
Low
Medium
High
```

---

## 7. Parse the AI Decision

The **Parse AI Decision** node validates the AI response.

If the response cannot be parsed safely, the workflow falls back to:

```text
Attack Type: Unknown
Severity: Medium
Recommended Action: Review the Wazuh alert manually
```

This prevents an invalid AI response from directly controlling the workflow.

---

## 8. Severity Decision

The workflow contains two decision nodes:

```text
High Severity?
Medium Severity?
```

### High severity

The high-severity branch can:

- Block the source IP through Wazuh Active Response
- Send a high-severity email
- Create an incident record

### Medium severity

The medium-severity branch can:

- Send a medium-severity email
- Create an incident record

### Low severity

The low-severity branch:

- Creates a low-severity incident/log record

---

## 9. Configure Wazuh Active Response

The **Wazuh Firewall Block IP** node uses the Wazuh API endpoint:

```text
https://<WAZUH_MANAGER_IP>:55000/active-response
```

The public workflow contains a placeholder:

```text
YOUR_WAZUH_MANAGER_IP
```

Configure your own Wazuh API authentication securely.

The workflow sends a `firewall-drop` command using the source IP extracted from the alert.

### Important

Do not enable automatic blocking on production systems without testing and approval. An incorrect AI decision or false positive could block a legitimate system.

---

## 10. Configure Email Notifications

The workflow contains email notification nodes for:

```text
Email High Alert
Email Medium Alert
```

Configure your own SMTP credential in n8n.

Do not commit:

- SMTP username/password
- API credentials
- Mailbox passwords
- Authentication tokens

The email body includes information such as:

- Severity
- Attack type
- Source IP
- Agent
- Rule ID
- Rule description
- AI explanation
- Recommended response
- AI summary
- Event time

---

## 11. Testing with a Sample Wazuh Alert

Use the sample file:

```text
examples/sample-wazuh-alert.json
```

It represents a sanitized Wazuh SSH authentication event.

You can send it to the n8n webhook with:

```bash
curl -X POST http://<N8N_IP>:5678/webhook/wazuh-alert   -H "Content-Type: application/json"   --data @examples/sample-wazuh-alert.json
```

Replace `<N8N_IP>` with your n8n server address.

---

## 12. Expected Workflow Execution

A successful execution should follow this sequence:

```text
Wazuh Webhook
      ↓
Prepare Wazuh Alert
      ↓
Gemini AI Analysis
      ↓
Parse AI Decision
      ↓
High Severity?
   ↙        ↘
YES         NO
 ↓           ↓
Firewall    Medium Severity?
Block       ↙          ↘
 ↓         YES          NO
Email       ↓            ↓
Incident   Email        Low Incident
          Incident
```

The exact branch depends on the severity returned by the AI analysis.

---

## 13. Security Checklist

Before publishing or sharing the project:

- [ ] Remove API keys.
- [ ] Remove passwords.
- [ ] Remove JWT tokens.
- [ ] Remove SMTP credentials.
- [ ] Replace internal IP addresses with placeholders.
- [ ] Remove private webhook URLs/secrets.
- [ ] Remove n8n credential IDs.
- [ ] Do not upload `.env`.
- [ ] Do not upload the n8n data directory.
- [ ] Do not upload Wazuh databases or private logs.
- [ ] Review screenshots for passwords, tokens, IPs, and personal information.

---

## 14. GitHub Repository Placement

Recommended locations:

```text
n8n/
└── Wazuh-AI-Security-Automation.json

n8n/
└── workflow-setup.md

examples/
└── sample-wazuh-alert.json
```

---

## 15. Project Purpose

This workflow demonstrates a SOAR-style security automation pipeline in which Wazuh detects an event, n8n orchestrates the workflow, Gemini assists with security analysis, and the workflow performs severity-based notification and response actions.

Use the project only for authorized defensive security testing and educational purposes.
