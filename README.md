# 📧 Email Automation Using n8n

An efficient, self-hosted workflow for automating personalized email sending using **n8n**, **Google Sheets**, and **Gmail**.

This project provides the core logic and structure for a reliable email outreach automation system, including personalization and logging of send status.

---

## 💡 Workflow Overview

The n8n workflow is designed to execute the following steps in a loop, with a configurable delay between each email to prevent rate limiting:

1.  **Fetch Leads:** Retrieves the list of recipients (Name and Email) from **Sheet 1** in the designated Google Sheet.
2.  **Fetch Templates:** Retrieves the list of email subjects and bodies from **Sheet 2** in the designated Google Sheet.
3.  **Random Template Selection:** A custom JavaScript function picks **one random template** from the list for the current recipient.
4.  **Personalization:** Replaces placeholders (e.g., `[name]`) in the selected template with the recipient's personal data.
5.  **Send Email:** Uses the **Gmail node** to send the personalized email.
6.  **Logging:** Updates the Google Sheet with the **send status** and a **timestamp** for tracking.
7.  **Delay:** Introduces a pause before processing the next recipient.

---

## 📌 Prerequisites

To set up and run this automation, you will need the following:

* **n8n Instance:** A free or paid n8n instance (cloud-hosted or self-hosted).
* **Google Account:** An account with access to **Google Sheets** and **Gmail**.
    * **Google Sheets Node Credentials** (OAuth or API Key).
    * **Gmail Node Credentials** (OAuth).

### Google Sheet Structure

Your Google Sheet must contain two specific sheets:

| Sheet Name | Purpose | Required Columns |
| :--- | :--- | :--- |
| **Sheet 1** | List of Recipients | `Name`, `Email` |
| **Sheet 2** | Email Templates | `Subject`, `Body` |

---

## ⚙️ Key JavaScript and Expressions

The automation relies on specific JavaScript code within **Function** and **Set** nodes for dynamic behavior and logging.

### 1. Template Selection (Function Node)

This script randomly selects one email template from the available list and formats the body for HTML compatibility (replaces `\n` with `<br>`).

```javascript
// Get templates from previous node
const templates = items.map(item => item.json);

// Check if templates exist
if (!templates || templates.length === 0) {
    throw new Error('No templates found');
}

// Pick a random template
const index = Math.floor(Math.random() * templates.length);
const template = templates[index];

// Normalize keys to handle case or spacing differences
const keys = Object.keys(template);
const bodyKey = keys.find(k => k.trim().toLowerCase() === 'body');
const subjectKey = keys.find(k => k.trim().toLowerCase() === 'subject');

if (!bodyKey) {
    throw new Error('No "Body" field found in template');
}

// Safely handle missing or empty Body
const bodyText = template[bodyKey] || '';
const bodyHtml = bodyText.replace(/\n/g, '<br>');

// Return in n8n-compatible format
return [
    {
        json: {
            subject: template[subjectKey] || '(No Subject)',
            body: bodyHtml
        }
    }
];

2. Personalisation (Set Node Expression)
This expression is used within a Set Node to replace the placeholder [name] in the template body with the actual recipient's name from the lead data.
{{ $json.body.replace("[name]", $json["Name"] && $json["Name"].trim() !== '' ? $json["Name"].trim() : "there") }}

3. Send Status & Timestamp Logging (Google Sheet Update)
These expressions capture the send outcome from the Gmail node and the current time to log back into the Google Sheet.
Data Point	n8n Expression	Details
Send Status (Gmail)	{{ $json.labelIds[0] }}	Captures the Gmail label ID (SENT or other) as confirmation of sending.
Timestamp	{{ new Date().toLocaleTimeString("en-GB", { timeZone: "Asia/Kolkata", hour12: false }) }}	Logs the time of sending, ensuring consistent time zone reporting.


🚀 Installation and Setup
Import Workflow: Import the n8n workflow file (JSON) into your n8n instance.

Add Credentials: Update the Google Sheets and Gmail nodes with your respective OAuth credentials.

Configure Sheet Nodes:

Verify the Google Sheets Read nodes are correctly configured to pull data from Sheet 1 (Leads) and Sheet 2 (Templates).

Verify the Google Sheets Write node is set up to log the status and timestamp to the appropriate columns in your leads sheet.

Activate: Save and activate the workflow to begin sending.

