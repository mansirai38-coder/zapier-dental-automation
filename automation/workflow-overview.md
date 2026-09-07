# Automation Workflow Overview

All 6 Zaps below were actually implemented and tested in Zapier (not proposed/conceptual). Each row links to its full configuration in [`zap-configuration.md`](zap-configuration.md).

| # | Zap | Trigger | Steps | Status |
|---|---|---|---|---|
| 1 | New Appointment Confirmation & Calendar | Google Forms – New Form Response | Create Sheet Row → Formatter → Create Calendar Event → Send Email | ✅ Implemented & tested |
| 2 | New Patient Welcome Email | Google Forms – New Form Response | Filter (Patient Type) → Send Email | ✅ Implemented & tested |
| 3 | 24-Hour Appointment Reminder | Google Forms – New Form Response | Formatter → Delay Until → Send Email → Update Sheet Row | ✅ Implemented & tested |
| 4 | Appointment Completed – Follow-up & Review Request | Google Sheets – Updated Row (Appointment Status) | Filter → Delay → Send Email → Update Sheet Row → Send Email | ✅ Implemented & tested |
| 5 | Appointment Cancelled – Notification | Google Sheets – Updated Row (Appointment Status) | Filter → Send Email | ✅ Implemented & tested |
| 6 | Appointment No-Show – Notification | Google Sheets – Updated Row (Appointment Status) | Filter → Send Email | ✅ Implemented & tested |

## Apps connected

Google Forms, Google Sheets, Google Calendar, Gmail — all via OAuth through Zapier. No other integrations (CRM, SMS, payment) are implemented in this version; see [`docs/business-use-case.md`](../docs/business-use-case.md) and the main README's Future Improvements section for what's proposed but not yet built.

## Architecture decision: independent Zaps over native Paths

Documented in full in [`docs/workflow.md`](../docs/workflow.md). Short version: each appointment status is handled by its own Zap with its own Google Sheets trigger (scoped to the `Appointment Status` column) plus a Filter step, rather than one Zap with native Zapier Paths branching. This keeps each automation small, independently testable, and easy to extend.
