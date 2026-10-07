# Wazuh + n8n + AI Security Automation

> **SOAR + AI Security Automation Pipeline for Automated Security Alert Analysis and Response**

A cybersecurity automation project that integrates **Wazuh**, **n8n**, and **AI** to automatically receive, analyze, classify, and respond to security alerts.

The project demonstrates how a Security Operations Center (SOC) can automate repetitive alert-analysis tasks by connecting a SIEM/XDR platform with a workflow automation platform and an AI security analyst.

---

## 📌 Project Overview

Traditional SOC environments often require analysts to manually investigate alerts, determine their severity, identify the potential attack technique, and decide what action should be taken.

This project automates a significant portion of that process.

When Wazuh generates a security alert, the alert is automatically forwarded to an **n8n webhook**. n8n processes the alert and sends the relevant security information to an **AI analysis component**. The AI analyzes the event, produces a human-readable security assessment, determines the potential severity, maps relevant MITRE ATT&CK techniques where applicable, and recommends an appropriate response.

### Core workflow

```text
┌──────────────────────┐
│   Kali / Endpoint    │
│   Security Activity  │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│     Wazuh Agent      │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│    Wazuh Manager     │
│   Alert Generation   │
└──────────┬───────────┘
           │
           │ Alert JSON
           ▼
┌──────────────────────┐
│  Custom n8n          │
│  Integration Script  │
└──────────┬───────────┘
           │
           │ HTTP POST
           ▼
┌──────────────────────┐
│     n8n Webhook      │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   AI Security        │
│      Analysis        │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Decision / Risk     │
│     Assessment       │
└──────────┬───────────┘
           │
      ┌────┼────┐
      ▼    ▼    ▼
    HIGH MEDIUM LOW
      │    │    │
      └────┼────┘
           ▼
┌──────────────────────┐
│ Recommended Response │
│ / SOC Notification   │
└──────────────────────┘
```

---

# 🎯 Objectives

The main objectives of this project are to:

* Integrate Wazuh with n8n.
* Automatically forward Wazuh security alerts to n8n.
* Process security events through an automated workflow.
* Use AI to analyze security alerts.
* Generate human-readable security summaries.
* Identify potential attack techniques.
* Map relevant events to MITRE ATT&CK techniques.
* Classify security events according to risk/severity.
* Provide recommended response actions.
* Reduce repetitive manual SOC investigation tasks.
* Demonstrate a practical SOAR + AI security automation architecture.

---

# 🛡️ Key Features

### 1. Wazuh Alert Monitoring

Wazuh monitors endpoints and generates security alerts based on configured detection rules.

Examples include:

* Authentication failures
* SSH login attempts
* Privilege escalation events
* Suspicious system activity
* File integrity changes
* Other security events detected by Wazuh

---

### 2. Automated Alert Forwarding

A custom Wazuh integration forwards selected alerts to an n8n webhook.

The integration performs the following process:

```text
Wazuh Alert
     ↓
Read Alert JSON
     ↓
Process Alert
     ↓
HTTP POST
     ↓
n8n Webhook
```

This removes the need for a SOC analyst to manually copy alert information from Wazuh into another system.

---

### 3. n8n Security Automation

n8n acts as the workflow orchestration layer.

The workflow can:

* Receive Wazuh alerts.
* Extract important fields.
* Process security information.
* Send information to an AI analysis component.
* Evaluate the AI result.
* Determine the appropriate workflow path.
* Generate a response or notification.

---

### 4. AI-Based Security Analysis

The AI component acts as an automated security analyst.

It can analyze information such as:

* Wazuh rule ID
* Alert description
* Severity level
* Source IP
* Destination information
* Authentication information
* Event location
* MITRE ATT&CK information
* Event frequency
* Other available alert metadata

The AI produces a structured security assessment.

Example:

```text
Attack Type:
SSH Authentication Attack

Severity:
Medium

Confidence:
High

MITRE ATT&CK:
T1110.001 - Password Guessing

Assessment:
The event indicates a failed SSH authentication attempt
using a non-existent username. Repeated occurrences from
the same source could indicate automated account discovery
or password-guessing activity.

Recommended Action:
Monitor the source IP and investigate repeated authentication
failures. Consider blocking the source if malicious activity
is confirmed.
```

---

# 🧠 AI Security Analysis Pipeline

The AI processing stage follows a structured approach:

```text
Wazuh Alert
     │
     ▼
Extract Security Information
     │
     ▼
Analyze Event
     │
     ├── Identify Attack Type
     │
     ├── Determine Severity
     │
     ├── Estimate Confidence
     │
     ├── Identify MITRE ATT&CK Technique
     │
     ├── Assess False-Positive Possibility
     │
     └── Recommend Response
     │
     ▼
Structured Security Assessment
```

---

# 🔍 Example Security Event

One of the test events used in the project is a Wazuh SSH authentication alert.

Example:

```json
{
  "rule": {
    "id": 5710,
    "description": "sshd: Attempt to login using a non-existent user",
    "level": 5
  },
  "agent": {
    "name": "lab-target"
  },
  "srcip": "192.168.100.X",
  "location": "sshd"
}
```

The alert is forwarded to n8n and processed by the automation workflow.

---

# ⚙️ Technology Stack

| Technology       | Purpose                                    |
| ---------------- | ------------------------------------------ |
| **Wazuh**        | Security monitoring and alert generation   |
| **n8n**          | Workflow automation and orchestration      |
| **AI**           | Security alert analysis and recommendation |
| **Python**       | Wazuh-to-n8n integration                   |
| **Docker**       | n8n container deployment                   |
| **REST API**     | Communication between components           |
| **Webhooks**     | Real-time alert delivery                   |
| **MITRE ATT&CK** | Attack technique identification            |
| **Linux**        | Server and security environment            |

---

# 🏗️ Project Architecture

```text
                     SECURITY EVENT
                           │
                           ▼
                  ┌─────────────────┐
                  │   Wazuh Agent   │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │  Wazuh Manager  │
                  └────────┬────────┘
                           │
                     Alert JSON
                           │
                           ▼
                  ┌─────────────────┐
                  │ custom-n8n      │
                  │ Integration     │
                  └────────┬────────┘
                           │
                       HTTP POST
                           │
                           ▼
                  ┌─────────────────┐
                  │   n8n Webhook   │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │  Alert Parsing  │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │ AI Security     │
                  │ Analysis        │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │ Risk / Decision  │
                  │     Engine       │
                  └────────┬────────┘
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
          HIGH RISK     MEDIUM RISK    LOW RISK
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                  Recommended Action
```

---

# 📂 Repository Structure

```text
Wazuh-n8n-AI-Security-Automation/
│
├── README.md
├── LICENSE
├── .gitignore
│
├── wazuh/
│   ├── custom-n8n
│   ├── ossec.conf.example
│   └── install.sh
│
├── n8n/
│   ├── Wazuh-AI-Security-Automation.json
│   └── workflow-setup.md
│
├── docker/
│   └── docker-compose.yml
│
├── docs/
│   ├── architecture.md
│   ├── installation.md
│   ├── testing.md
│   └── screenshots/
│       ├── wazuh-alert.png
│       ├── n8n-workflow.png
│       ├── n8n-execution.png
│       └── wazuh-dashboard.png
│
└── examples/
    └── sample-wazuh-alert.json
```

---

# 🚀 Installation and Setup

## Prerequisites

Before deploying the project, make sure you have:

* A Wazuh Manager
* A Wazuh Agent or monitored endpoint
* Linux environment
* Docker
* n8n
* AI provider/API configured in n8n
* Network connectivity between Wazuh and n8n

---

## 1. Deploy n8n

The project uses Docker for n8n deployment.

Example:

```bash
docker compose -f docker/docker-compose.yml up -d
```

After deployment, n8n can be accessed through:

```text
http://<N8N_IP>:5678
```

---

## 2. Import the n8n Workflow

Open n8n and import:

```text
n8n/Wazuh-AI-Security-Automation.json
```

After importing:

1. Configure the required AI credentials.
2. Review the webhook configuration.
3. Verify the AI processing node.
4. Verify the decision/response nodes.
5. Activate the workflow.

> **Important:** Credentials and API keys are not included in this repository.

---

## 3. Configure Wazuh

Use the example configuration:

```text
wazuh/ossec.conf.example
```

Configure the Wazuh integration with the n8n webhook:

```text
http://<N8N_IP>:5678/webhook/wazuh-alert
```

Replace `<N8N_IP>` with the IP address of your n8n server.

---

## 4. Install the Wazuh Integration

Copy the integration script into:

```text
/var/ossec/integrations/custom-n8n
```

Set the required permissions:

```bash
sudo chmod 750 /var/ossec/integrations/custom-n8n
sudo chown root:wazuh /var/ossec/integrations/custom-n8n
```

Restart Wazuh:

```bash
sudo systemctl restart wazuh-manager
```

---

# 🧪 Testing

After configuration, generate a security event from the monitored endpoint.

The expected flow is:

```text
Security Event
      ↓
Wazuh Agent
      ↓
Wazuh Manager
      ↓
Wazuh Rule
      ↓
custom-n8n
      ↓
n8n Webhook
      ↓
AI Analysis
      ↓
Decision
      ↓
Response
```

Verify the following:

### Wazuh

Confirm that the security alert is generated.

### n8n

Confirm that the webhook receives the alert.

### AI

Confirm that the AI analyzes the event.

### Execution

Confirm that the complete n8n workflow executes successfully.

---

# 📊 Expected Result

A successful execution should produce an automated security assessment similar to:

```text
Security Event Detected
        │
        ▼
Alert Received
        │
        ▼
AI Analysis
        │
        ▼
Attack Classification
        │
        ▼
Risk Assessment
        │
        ▼
MITRE ATT&CK Mapping
        │
        ▼
Recommended Response
```

This demonstrates an automated SOC workflow in which a Wazuh alert can be processed without requiring an analyst to manually perform every initial investigation step.

---

# 🔐 Security Considerations

This project is intended for **authorized cybersecurity labs, educational environments, and defensive security operations**.

Do not commit sensitive information to the repository.

The following must never be uploaded:

```text
.env files
API keys
Passwords
Private keys
Authentication tokens
n8n credentials
Wazuh private configuration
Production secrets
```

Use placeholders such as:

```text
<N8N_IP>
<API_KEY>
<WEBHOOK_URL>
```

instead.

The `.gitignore` file is included to help prevent accidental commits of sensitive files.

---

# ⚠️ Disclaimer

This project is designed for educational, research, and authorized defensive security purposes.

Only deploy the automation against systems and networks that you own or have explicit permission to monitor.

The AI-generated security assessment should be treated as an analyst-assistance mechanism rather than an unquestionable security decision. Alerts should be validated using additional evidence before performing high-impact response actions.

---

# 📸 Project Screenshots

Screenshots demonstrating the project implementation will be available in:

```text
docs/screenshots/
```

Recommended screenshots include:

* Wazuh security alert
* n8n workflow
* n8n webhook
* Successful n8n execution
* AI analysis result
* Wazuh dashboard

---

# 🔮 Future Improvements

Possible future improvements include:

* Automated IP blocking
* Firewall integration
* Email/Telegram/Slack notifications
* Automated incident ticket creation
* Threat intelligence enrichment
* VirusTotal IOC lookup
* AlienVault OTX enrichment
* Automated IOC extraction
* Automated MITRE ATT&CK enrichment
* Case management integration
* SOC dashboard
* Alert correlation
* Automated incident response
* Human approval before high-impact actions

---

# 👨💻 Author

**Muhammad Faizan Ali**

Cyber Security Student | Ethical Hacking Enthusiast | Network Security Learner

### Areas of Interest

* Cyber Security
* Security Operations Center (SOC)
* Ethical Hacking
* Network Security
* Threat Intelligence
* Security Automation
* Digital Forensics
* Incident Response

---

# ⭐ Project Purpose

This project demonstrates the practical integration of:

```text
SIEM/XDR
   +
SOAR
   +
Artificial Intelligence
   =
Automated Security Operations
```

The goal is to demonstrate how modern security teams can use automation and AI to reduce repetitive alert-analysis tasks, improve response speed, and assist security analysts in investigating potential threats.

---

# 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
