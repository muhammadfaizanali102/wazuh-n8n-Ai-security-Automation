# 🛡️ Wazuh + n8n + AI Security Automation (SOAR)

## 📖 Overview
This project implements a basic automated security response system (SOAR) by integrating **Wazuh** (SIEM), **n8n** (Workflow Automation), and **Google Gemini AI**. The system monitors security events, analyzes them using artificial intelligence, and automates incident response actions such as sending notifications, logging incidents, and blocking malicious IP addresses.

## ✨ Features
- **🔍 SIEM Monitoring**: Wazuh monitors system logs and detects suspicious activities (e.g., SSH brute-force attacks).
- **⚙️ Automated Workflow**: n8n orchestrates the incident response process based on webhooks triggered by Wazuh.
- **🧠 AI-Powered Analysis**: Google Gemini AI analyzes Wazuh alerts to determine the attack type, severity, and recommended actions, generating human-readable summaries.
- **⚖️ Dynamic Decision Logic**:
  - **🔴 High Severity**: Automatically blocks the attacker's IP using Wazuh's Active Response, logs the incident, and sends a critical email alert.
  - **🟡 Medium Severity**: Logs the incident and sends a warning email to the security administrator without blocking the IP.
  - **🟢 Low Severity**: Silently logs the incident for record-keeping purposes.

## 🏗️ Architecture & Workflow
1. **🚨 Attack Simulation & Detection**: Wazuh detects a threat (e.g., repeated failed SSH logins) and generates an alert.
2. **🔌 Custom Integration**: A custom Python script (`custom-n8n`) forwards the JSON alert from Wazuh Manager to an n8n Webhook.
3. **🧹 Data Preparation**: n8n cleans and formats the raw JSON alert, extracting essential fields (Source IP, Rule ID, Description, Level, Agent Name).
4. **🤖 AI Analysis**: The cleaned data is sent to the Gemini API, which returns a structured response containing the attack type, severity, explanation, and recommended action.
5. **⚡ Response Execution**: Based on the AI's severity classification, n8n follows predefined paths (High, Medium, or Low) to log the incident and optionally trigger firewall blocks or email notifications.

## 📋 Requirements
- 🛡️ Wazuh Manager (e.g., v4.14.7)
- 🔄 n8n (running in Docker or standalone)
- 🔑 Google Gemini API Key
- 📧 Email Account (for SMTP notifications)

## 🚀 Setup Instructions

### 1️⃣ n8n Setup
Run n8n using Docker with a persistent volume:
```bash
docker volume create n8n_data
docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8n/n8n
```
Access the n8n web interface at `http://localhost:5678`.

### 2️⃣ Wazuh Custom Integration
1. On the Wazuh Manager, create a custom integration script at `/var/ossec/integrations/custom-n8n`.
2. This script should read the Wazuh alert file and send it as an HTTP POST request to your n8n webhook URL.
3. Configure the integration in the `ossec.conf` file to forward alerts to the script.
4. Restart the Wazuh Manager service.

### 3️⃣ n8n Workflow Configuration
1. Create a workflow in n8n starting with a **Webhook** node (`POST` method, listening on a specific path like `wazuh-alert`).
2. Add a **Code Node** ("Prepare Wazuh Alert") to clean the raw JSON alert and extract essential fields using safe operators.
3. Add an **HTTP Request Node** ("Gemini AI Analysis") to send the cleaned data to the Gemini API for analysis.
4. Add another **Code Node** ("Parse AI Decision") to safely parse the AI's JSON response and extract fields like severity, attack type, and recommended action.
5. Use **If Nodes** to route the workflow based on the AI's determined severity:
   - **🔴 High Severity**: Send a PUT request to the Wazuh Manager API (`/active-response`) to block the source IP, log the incident, and send a critical email alert.
   - **🟡 Medium Severity**: Log the incident and send a warning email.
   - **🟢 Low Severity**: Log the incident only.

## 🎯 Conclusion
This integration transforms standard SIEM alerts into a smart, automated Security Operations Center (SOC) pipeline. By leveraging AI to translate technical alerts into plain English and applying automated decision-making, it significantly reduces manual triage effort and accelerates incident response times.
