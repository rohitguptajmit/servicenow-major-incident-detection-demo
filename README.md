# ServiceNow Incident & Major Incident Detection System – n8n Workflow

This repository contains an end-to-end automated **Incident Management & Major Incident Detection System**, implemented using **n8n**.  
It replicates how the automation will eventually integrate with **ServiceNow** by temporarily using a **Google Sheets spreadsheet (SERVICE NOW.xlsx)** because ServiceNow API keys are not currently available.

---

## 📄 Included Reference File – SERVICE NOW.xls

The repository includes a reference spreadsheet named **SERVICE NOW.xlsx**, which contains two tabs:

### 1️⃣ Incidents (Sheet)
Simulates the ServiceNow **incident** table.

Columns:

- `number`  
- `server_name`  
- `assignment_group`  
- `short_desc`  
- `state`  
- `created_at`  
- `is_major_child`  
- `parent_mi_number`  
- `notes`  

### 2️⃣ MajorIncidents (Sheet)
Simulates the ServiceNow **sn_major_incident** table.

Columns:

- `mi_number`  
- `server_name`  
- `summary`  
- `child_list`  
- `state`  
- `created_at`  

This spreadsheet helps visualize how real ServiceNow tables will be mapped once the workflow connects to the actual API.

---

## 🚀 What This Workflow Automates

### ✔ Automated Incident Ingestion  
Each new incident is received via webhook and inserted into the mock ServiceNow dataset in Google Sheets.

### ✔ Intelligent Pattern Detection  
The workflow evaluates:

- Server repeatedly impacted  
- Similar short descriptions  
- Frequency of incidents  
- Time-based clustering  
- Custom business rules  

If the number of repeated incidents crosses your defined threshold → it triggers Major Incident logic.

### ✔ Automatic Major Incident Creation  
If no active MI exists for the server:

- Generates a **new MI number**
- Creates a new **Major Incident record**
- Builds a detailed MI summary  
- Prepares a child list of all related incidents  

### ✔ Auto-Update Existing Major Incident  
If an **active** MI already exists:

- Appends the new incident to the MI  
- Updates MI summary & child list  
- Marks the new incident as a `major child`  
- Assigns `parent_mi_number`

### ✔ AI-Based Summary  
An OpenAI GPT node generates a clean, human-readable explanation for why the MI was created or updated.

---

## 🧩 Workflow Architecture

### Nodes Overview

**1. Receive Incident (Webhook)**  
Accepts POST requests (later from ServiceNow).

**2. Workflow Configuration**  
Defines system-wide logic like thresholds & pattern rules.

**3. Get All Incidents**  
Reads all incidents from spreadsheet.

**4. Generate Incident Number**  
Creates a sequential `INCxxxxxx` number.

**5. Prepare New Incident**  
Adds timestamps and normalizes fields.

**6. Append Incident to Sheet**  
Saves the incident into the “Incidents” sheet.

**7. Analyze Incident Patterns**  
Identifies repeated issues or clustering.

**8. Check Threshold Exceeded**  
Decision point → Should major incident logic run?

**9. Get Existing Majors**  
Loads active major incidents for correlation.

**10. Generate Major Incident**  
Creates new MI metadata (ID, summary, child list).

**11. Append Major Incident**  
Writes MI entry into “MajorIncidents” sheet.

**12. Prepare Child Updates**  
Builds update list for related incidents.

**13. Update Child Incidents**  
Marks incidents as children of the MI.

**14. Prepare Response Data**  
Formats output JSON.

**15. Generate AI Response**  
Creates an executive summary using GPT.

**16. Return Response**  
Webhook responds to caller.

---

## 🛠️ Technologies Used

| Component | Purpose |
|----------|---------|
| **n8n** | Automation engine |
| **JavaScript** | Logic & data processing |
| **Google Sheets** | Temporary ServiceNow mock database |
| **OpenAI GPT** | AI-based summaries |
| **cURL/Webhooks** | External triggering |

---

## 🛠️ How to Trigger an Incident (Testing via Command Prompt)

You can simulate ServiceNow sending an Incident by manually posting data to the webhook.

## Step-by-Step Instructions

1. **Open Command Prompt / Terminal**
   - Windows → `Win + R` → type **cmd**  
   - macOS → Launch **Terminal**  
   - Linux → open default terminal  

2. **Copy your webhook URL**
   - In n8n → open **Webhook Trigger Node**  
   - Find **Test URL** or **Production URL**

3. **Run the cURL command below**

---

## 🔧 Generic cURL Example

> Replace `YOUR_N8N_WEBHOOK_URL` with your personal n8n webhook URL.

```bash
curl -X POST "YOUR_N8N_WEBHOOK_URL" \
-H "Content-Type: application/json" \
-d "{
  \"server_name\": \"SERVER-01\",
  \"assignment_group\": \"Network Team\",
  \"short_desc\": \"Server connectivity issue\",
  \"state\": \"New\"
}"
```

---

## 💡 Example (ONLY for reference – do not use as-is)

Use this sample only to understand the format.  
**Do NOT use this exact URL** — replace it with your own n8n webhook endpoint.

```bash
curl -X POST "https://<your-n8n-host>.cloud/webhook-test/incident" \
-H "Content-Type: application/json" \
-d "{
  \"server_name\": \"SERVER-01\",
  \"assignment_group\": \"Network Team\",
  \"short_desc\": \"Server connectivity issue\",
  \"state\": \"New\"
}"
```

---

## 📥 Importing This Workflow into n8n

Follow these steps:

1. **Download the JSON file 

2. **Open your n8n instance**

3. Click **Import Workflow** (top-right)

4. Upload the JSON file

5. Update the following:
   - Google Sheets credential  
   - Spreadsheet ID  
   - Webhook node (optional)

6. **Save & Activate** the workflow

---

## 🔮 Future Enhancements

Planned improvements:

- 🔗 Direct ServiceNow API integration (Incident + Major Incident tables)
- 🧠 Automatic RCA prediction using AI/LLMs
- 🔄 Auto-reopen Major Incident when related incidents arrive
- 🌐 Multi-CI dependency graph correlation
- 📊 Power BI dashboard for MI analytics & trends
- 🚨 Slack / Microsoft Teams alerts for new MI creation

---

## 👤 Author

**Rohit Gupta**  
Automation Engineer • ServiceNow Enthusiast 

---
