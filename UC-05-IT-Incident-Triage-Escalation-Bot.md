# Use Case 05 — IT Incident Triage & Escalation Bot

**Category:** IT Operations & ITSM
**Author:** Christopher Ayodeji Ojo — [Trublshut](https://whop.com/trublshut)
**Tools:** n8n · Jira REST API · Slack API · Microsoft Teams · Email (SMTP / Graph API)

---

## 🔴 The Problem

Critical incidents get buried in ticket queues. On-call engineers miss P1 alerts. Escalations happen too late — after SLAs are already breached and stakeholders are already frustrated.

Manual monitoring of an incident queue is unreliable at any scale. Engineers are busy. Tickets come in at all hours. One missed P1 can bring down a service for hours while someone eventually notices.

---

## ✅ The Solution

An intelligent triage bot that monitors incoming Jira incidents in real time, classifies severity using priority and keyword logic, auto-escalates P1/P2 incidents to on-call engineers via multiple channels simultaneously, and monitors for acknowledgement — re-alerting if none is received within the defined SLA window.

No missed incidents. No manual queue watching. Structured escalation every time.

---

## ⚙️ How It Works

```
New Jira incident ticket created
        ↓
n8n polling trigger (every 2 mins) OR Jira webhook
        ↓
Read ticket fields: priority, category, affected system, keywords
        ↓
Classify severity: P1 / P2 / P3 / P4
        ↓
P1/P2 → immediate multi-channel alert to on-call engineer
         (Slack DM + Teams message + Email)
P3/P4 → standard queue notification to IT channel
        ↓
Post structured incident card to Teams/Slack channel
        ↓
Start SLA acknowledgement timer
        ↓
No acknowledgement in X mins → re-alert + escalate to IT Manager
        ↓
Log full escalation chain to audit record (SharePoint)
```

---

## 🛠️ Tech Stack

| Tool | Role |
|------|------|
| n8n | Orchestration + polling / webhook trigger |
| Jira REST API | Incident ticket reading |
| Slack API | On-call DM + channel alerts |
| Microsoft Teams | Incident channel cards |
| Email (SMTP / Graph API) | Backup escalation channel |
| SharePoint | Escalation audit log |

---

## 🔧 Key Logic

- **Polling vs webhook:** n8n polls Jira every 2 minutes as the reliable default; Jira webhook as a faster optional trigger
- **Severity classification:** uses Jira `priority` field as primary + keyword scan of ticket summary for override triggers (e.g. "production down", "database unreachable", "all users affected" → force P1 regardless of assigned priority)
- **On-call routing:** on-call schedule stored in a SharePoint list or Google Sheet — n8n reads the current on-call engineer and routes alerts to their contact details
- **Acknowledgement check:** n8n waits X minutes after escalation, then re-queries Jira for status change. No change → re-alert + escalate up one level
- **Escalation chain:** Engineer → IT Manager → CTO (configurable levels with configurable wait times between each)
- **Structured alert card:** incident ID, title, priority, affected system, time since creation, direct link to Jira ticket

---

## 📊 Outcomes

- ✅ P1 incidents surface immediately — zero missed critical alerts
- ✅ SLA acknowledgement times dramatically reduced
- ✅ On-call engineers receive structured, actionable alerts across multiple channels
- ✅ Escalation chain fires automatically if no response — no manager manually chasing
- ✅ Full escalation audit trail for post-incident review and process improvement

---

## 📁 How to Use This

1. In n8n, create a Schedule trigger (every 2 mins) OR configure a Jira webhook to fire on new incident creation
2. Use the Jira node to read ticket fields: `priority`, `summary`, `components`, `created`
3. Add an IF node to classify severity — map priority levels + keyword conditions to P1/P2/P3/P4
4. For P1/P2: add parallel branches — Slack DM, Teams message, and Email nodes — all firing simultaneously
5. For P3/P4: post to IT team channel only
6. Use an n8n Wait node + HTTP Request to Jira to check for status change after X minutes
7. If no change: trigger re-alert and escalation to next level
8. Write escalation record to SharePoint: ticket ID, severity, escalation chain, timestamps

---

## 🔗 Related Use Cases

- [Use Case 01 — IT Service Request Auto-Routing](./UC-01-IT-Service-Request-Auto-Routing.md)
- [Use Case 06 — SLA Monitoring & Breach Alert Automation](./UC-06-SLA-Monitoring-Breach-Alert.md)

---

*© 2026 Christopher Ayodeji Ojo · Trublshut AI Automation · christopherayodeji131@gmail.com*
