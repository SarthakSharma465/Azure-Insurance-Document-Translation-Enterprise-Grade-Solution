# Azure-Insurance-Document-Translation-Enterprise-Grade-Solution
Automates document translation using Azure AI Translator, SharePoint, Power Automate, and Teams. Built in Copilot Studio, this agent-driven solution logs translation events, manages blob storage, and posts real-time status updates via Adaptive Cards in Teams.
Here's a complete and professional `README.md` for your **Aventum Translate – Azure Slip Translation** GitHub repository. It combines the core documentation, diagrams, and architecture into a clean, shareable format.

---

## 📘 Azure Slip Translation Agent

A robust solution that enables insurance slip translations using Azure Document Translation API, SharePoint, and Microsoft Power Platform — all seamlessly triggered by users through Microsoft Teams.

---

### 📌 Summary

> This project was developed to streamline insurance document translation using an AI-powered agent hosted on **Microsoft Copilot Studio**. It handles file intake, translation orchestration via Azure REST APIs, status tracking, logging, and delivery of the translated documents — all from within Teams.

---

## 🧠 Problem Statement

The original email-triggered translation solution was limited by:

* ❌ Azure Translation (Preview) connector instability
* ❌ Poor error monitoring
* ❌ Manual, mailbox-based triggers

This project rearchitects the pipeline for **scalability**, **control**, and **Teams-native accessibility** using Power Automate, REST APIs, and Azure services.

---

## 🚀 Key Features

* 🌍 File upload and status tracking through **Copilot Studio on Teams**
* 🛠️ REST-based integration with **Azure Translator**
* 📦 Centralized file and log management in **SharePoint**
* 🧾 Adaptive Cards to display status (e.g., *In Translation*, *Delivered*)
* 🪵 Granular error logging and Power Automate **flow run traceability**
* 🧠 Modular workflow blocks with retry and alert logic
* 📂 Blob storage for input, output, and glossary handling
* 🔒 Queue-safe execution using Power Automate concurrency control

---

## 🏗️ Architecture Overview

### 🔗 Platform Integration Architecture

![Architecture Diagram](./Platform%20Integration%20Architecture%20Diagram.jpg)

The pipeline uses Copilot Studio to push uploaded files into SharePoint, triggering Power Automate flows that interact with Azure Blob Storage and Azure Translator.

### 🔁 Technical Process Diagram

![Technical Process Diagram](.Technical%20Process%20Diagram.jpg)

The pipeline supports **non-prod** and **prod environments** using modular blocks:

* File Fetch & Upload
* Start Translation & Poll
* Retrieve File
* Deliver File via Teams

---

## 🧱 Component Overview

| Component                  | Purpose                                                      |
| -------------------------- | ------------------------------------------------------------ |
| **Teams + Copilot Studio** | User interface to upload files and track progress            |
| **SharePoint**             | Document libraries for input/output + logging                |
| **Power Automate**         | Workflow engine with REST API integration and error handling |
| **Azure Storage**          | Blob containers for source, translated files, and glossaries |
| **Azure Translator**       | Translation via REST API (`/batches` endpoint)               |
| **Adaptive Cards**         | Visual notifications in Teams on status updates              |

---

## 🔄 Flow Breakdown

### 1. File Upload

* File is uploaded via Copilot Studio in Teams
* File content and filename are stored using the `Activity.Attachments` table workaround

### 2. Logging Flow

* A log entry is created in SharePoint with initial status: `Request Received`
* Captures: File metadata, user email, status, and tracking link

### 3. Translation Flow (Single Flow)

* Uploads file to input blob
* Sends REST API request to Azure Translator
* Polls for translation job completion
* Fetches result from output blob
* Uploads to SharePoint and notifies user
* Updates status to `In Translation`, `Completed`, `Delivered`, or `Failed` with traceable errors

---

## 🔍 Logging and Monitoring

* Logs are updated with Power Automate **Flow Run Link**
* Status updates sent to:

  * SharePoint Logs List
  * Technical steward via email
  * User via Adaptive Card in Teams
* Errors appended to an `errorArray`, handled with a `Switch` block

---

## 📦 SharePoint Schema

### Input Document Library

| Column     | Description         |
| ---------- | ------------------- |
| Name       | File name           |
| User Email | Sender's email      |
| Created    | Timestamp of upload |

### Log List

| Column            | Description                            |
| ----------------- | -------------------------------------- |
| Status            | Request Received, In Translation, etc. |
| Source/Target URL | Direct links to files                  |
| Flow Run Link     | Power Automate execution trace         |

---

## 🔐 Deployment Notes

* Translator service must be assigned **Blob Contributor** role
* REST API is used instead of Power Automate’s **Preview connector**
* Set Power Automate **concurrency to 1** to avoid race conditions
* Avoid multiple flow splits — keep all steps in one single flow for stability

---

## ⏲️ Timestamp Handling

* Timestamps generated via **Power Automate**, not Copilot Studio
* For local time conversion in Copilot Studio:

  ```plaintext
  DateAdd(Now(), Conversation.LocalTimeZoneOffset, TimeUnit.Minutes)
  ```
* Formatted using:

  ```plaintext
  Text(Topic.nowLocal, DateTimeFormat.LongDateTime)
  ```



---

## 🔗 References

* [Azure Document Translation API](https://learn.microsoft.com/en-us/azure/ai-services/translator/document-translation/overview)
* [Copilot Studio Timezone Handling](https://learn.microsoft.com/en-us/power-virtual-agents/advanced-scenarios/time-zones)&#x20;

---

## 🧪 Future Enhancements

Add bulk document support

Build retry logic for partial failures

Support multiple language targets

Add SharePoint download link in Adaptive Cards

Use a Custom Connector for Azure Translator to:

Standardize API authentication

Abstract repetitive REST logic into reusable actions

Improve maintainability and avoid HTTP block duplication in Power Automate

Implementing a Custom Connector for Azure Translation will simplify flow logic, allow schema control over inputs/outputs, and enable secure reuse across environments without re-authentication or redundant expressions.

---

Let me know if you'd like this exported as a `README.md` file with linked assets.
