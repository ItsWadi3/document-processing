# Document Processing & Automation

![n8n](https://img.shields.io/badge/n8n-EA4B71?logo=n8n&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)
![PDF.co](https://img.shields.io/badge/PDF.co-0078D4?logo=adobeacrobatreader&logoColor=white)
![Gmail](https://img.shields.io/badge/Gmail-EA4335?logo=gmail&logoColor=white)

> An n8n workflow that automates document intake, classification, PDF form generation, and email delivery — no manual data entry required.

---

## 📋 Table of Contents

- [About](#-about)
- [Features](#-features)
- [Workflow Overview](#-workflow-overview)
- [Tech Stack](#️-tech-stack)
- [Getting Started](#-getting-started)
- [API Reference](#-api-reference)
- [Environment Variables & Credentials](#️-environment-variables--credentials)
- [Example Payload](#-example-payload)
- [Contributing](#-contributing)
- [License](#-license)
- [Authors](#-authors)

---

## 📖 About

**Document Processing & Automation** is an n8n workflow designed to streamline document intake and processing for businesses. It receives structured document data via a webhook, classifies documents (invoices, contracts, applications) using rule-based logic, fills standardized PDF forms, and delivers the completed documents via email — eliminating manual data entry and reducing processing time.

---

## ✨ Features

- 📨 **Webhook Intake** — Receive document data via a simple POST request
- 🏷️ **Rule-Based Classification** — Auto-detect document type, currency, target system, and priority
- ✅ **Checkbox Logic** — Dynamically set PDF form checkboxes based on incoming data
- 📝 **PDF Form Filling** — Populate standardized intake forms via the PDF.co API
- 📧 **Email Delivery** — Send processed documents as attachments via Gmail
- 🔄 **Data Normalization** — Handles flexible payload structures with automatic fallback logic
- 🌍 **Multi-Currency** — Supports EUR, USD, and GBP
- ⚡ **Priority Routing** — Low / Medium / High priority classification
- 🏢 **System Targeting** — Route documents to ERP or CRM systems

---

## 🔄 Workflow Overview

```
┌─────────────────┐     ┌────────────────────────┐     ┌──────────────────┐
│  Webhook (POST) │────▶│ Normalize Data &       │────▶│ Fill PDF Form    │
│  Receive Data   │     │ Set Checkboxes (JS)    │     │ (PDF.co API)     │
└─────────────────┘     └────────────────────────┘     └────────┬─────────┘
                                                                │
                        ┌────────────────────────┐     ┌────────▼─────────┐
                        │  Email Processed       │◀────│ Download         │
                        │  Document (Gmail)      │     │ Processed PDF    │
                        └────────────────────────┘     └──────────────────┘
```

### Nodes

| # | Node | Type | Description |
|---|------|------|-------------|
| 1 | **Receive Document Data** | Webhook | Listens for incoming POST requests with document data |
| 2 | **Normalize Data & Set Checkboxes** | Code (JS) | Normalizes payload structure and derives checkbox values |
| 3 | **Fill PDF Form (PDF.co)** | PDF.co API | Fills a PDF template with the normalized and classified data |
| 4 | **Download Processed PDF** | HTTP Request | Downloads the generated PDF from PDF.co |
| 5 | **Email Processed Document** | Gmail | Sends the PDF as an email attachment to the recipient |

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|------------|
| Automation Platform | [n8n](https://n8n.io) |
| PDF Processing | [PDF.co](https://pdf.co) |
| Email Delivery | Gmail (OAuth2) |
| Logic | JavaScript (n8n Code Node) |
| Data Format | JSON (Webhook) |

---

## 🚀 Getting Started

### Prerequisites

- [n8n](https://n8n.io) (self-hosted or cloud)
- A [PDF.co](https://pdf.co) account and API key
- A Gmail account with OAuth2 configured in n8n
- A PDF form template uploaded to PDF.co

### Installation

1. Clone the repository
   ```bash
   git clone https://github.com/ItsWadi3/document-processing.git
   cd document-processing
   ```

2. Import the workflow into n8n
   - Open your n8n instance
   - Go to **Workflows** → **Import from File**
   - Select `Document Processing & Automation.json`

3. Configure credentials
   - **PDF.co**: Add your API key under *Credentials* → *PDF.co account*
   - **Gmail**: Set up Gmail OAuth2 under *Credentials* → *Gmail OAuth2 API*

4. Update the PDF template URL
   - In the **Fill PDF Form** node, replace the file token with your own PDF form template URL

5. Activate the workflow
   - Toggle the workflow to **Active**

---

## 📡 API Reference

### `POST /webhook/<webhook-path>`

Send document data to the workflow for processing.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `document_type` | `string` | Yes | Type of document: `Invoice`, `Contract`, or `Application` |
| `document_number` | `string` | Yes | Unique document identifier |
| `document_date` | `string` | Yes | Date of the document (e.g. `2026-03-03`) |
| `sender_company` | `string` | Yes | Name of the sending company |
| `sender_name` | `string` | Yes | Name of the sender |
| `sender_email` | `string` | Yes | Email of the sender |
| `amount` | `string` | Yes | Document amount (e.g. `2,450.00`) |
| `currency` | `string` | Yes | Currency code: `EUR`, `USD`, or `GBP` |
| `description` | `string` | Yes | Description of the document contents |
| `target_system` | `string` | Yes | Target system: `ERP` or `CRM` |
| `priority` | `string` | Yes | Priority level: `Low`, `Medium`, or `High` |
| `processing_notes` | `string` | No | Additional processing notes |
| `recipient_email` | `string` | Yes | Email address to send the processed document to |

**Response**: The workflow processes the data asynchronously and delivers the filled PDF via email.

---

## ⚙️ Environment Variables & Credentials

| Credential | Service | Description |
|------------|---------|-------------|
| `PDF.co account` | PDF.co | API key for PDF form filling |
| `Gmail OAuth2 API` | Gmail | OAuth2 credentials for sending emails |

> **Note**: Credentials are managed within the n8n UI under **Settings** → **Credentials**. No `.env` file is required.

---

## 💡 Example Payload

```json
{
  "document_type": "Invoice",
  "document_number": "INV-2026-0315",
  "document_date": "2026-03-03",
  "sender_company": "TechSolutions GmbH",
  "sender_name": "Anna Müller",
  "sender_email": "anna.mueller@techsolutions.de",
  "amount": "2,450.00",
  "currency": "EUR",
  "description": "IT-Beratungsleistungen Februar 2026 – Systemintegration und API-Entwicklung",
  "target_system": "ERP",
  "priority": "High",
  "processing_notes": "Zahlung innerhalb 30 Tagen. Kostenstelle: IT-4200",
  "recipient_email": "team@example.com"
}
```

Send it with cURL:

```bash
curl -X POST https://your-n8n-instance.com/webhook/YOUR_WEBHOOK_PATH \
  -H "Content-Type: application/json" \
  -d @payload.json
```
---

## 📄 Document Intake Form

The **Document Intake Form** (`Document_Intake_Form.pdf`) was generated using **prompt engineering** techniques with **Antigravity** (Model: **Claude Opus 4.6**). The form serves as a structured input template for the document processing pipeline.

> **Note:** The prompt engineering methodology and workflow used to generate this form is a separate topic and could be shared as its own project in the future.

---

## 🤝 Contributing

Contributions are welcome! Here's how:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## 👤 Authors

**ItsWadi3**

- GitHub: [@ItsWadi3](https://github.com/ItsWadi3)

---

<p align="center">
  Made with ❤️ by <a href="https://github.com/ItsWadi3">ItsWadi3</a> and <a href="https://n8n.io">n8n</a>
</p>
