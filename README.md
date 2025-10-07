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


<img width="1357" height="725" alt="image" src="https://github.com/user-attachments/assets/71ce1e59-948c-4c88-8835-e7caf63a4d89" />

This is great! I'll organize your text into a comprehensive and professional GitHub README file.

Here is the draft:

Markdown

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
2. Personalization (Set Node Expression)
This expression replaces the [name] placeholder in the email body with the recipient's name from Sheet 1. It defaults to "there" if the name is missing or empty.
{{ $json.body.replace("[name]", $json["Name"] && $json["Name"].trim() !== '' ? $json["Name"].trim() : "there") }}
3. Send Status Logging (Google Sheet Update)
These expressions are used to log the outcome of the Gmail node back into the Google Sheet.
Data Point	n8n Expression	Purpose
Send Status (Gmail)	{{ $json.labelIds[0] }}	Captures the label ID from the sent email, indicating successful sending.
Timestamp	{{ new Date().toLocaleTimeString("en-GB", { timeZone: "Asia/Kolkata", hour12: false }) }}	Logs the time of sending in a speci
🚀 Installation and Setup
Import the Workflow: Download the n8n workflow JSON file (not provided in this snippet, but assumed to exist) and import it into your n8n instance.

Connect Credentials: Update the Google Sheets and Gmail nodes with your respective OAuth or API credentials.

Configure Nodes:

Google Sheets Read (Leads): Ensure the Sheet Name points to Sheet 1 and the range covers your lead data.

Google Sheets Read (Templates): Ensure the Sheet Name points to Sheet 2 and the range covers your template data.

Google Sheets Write (Logging): Ensure the node is configured to write to the correct sheet and column based on your layout.

Activate: Save and activate the workflow.
