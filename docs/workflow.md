# Workflow Documentation

## Overview

The system is split into **6 independent Zaps**, each reacting to one specific event rather than one giant branching workflow. Two kinds of events drive everything:

1. **A new Google Form submission** (booking-time events — Zaps 1–3)
2. **A staff-driven Appointment Status change on the Google Sheet** (lifecycle events — Zaps 4–6)

```
Trigger
   ↓
New Appointment Request (Google Form)
   ↓
Validate & Store in Google Sheets (system of record)
   ↓
Create Calendar Event + Send Confirmation
   ↓
Branch: New Patient? → Welcome Email
   ↓
Delay Until 24h Before Appointment → Reminder Email
   ↓
Staff Updates Appointment Status
   ↓
Branch on Status:
   • Completed → Delay ~1 day → Follow-up Email → Review Request
   • Cancelled → Cancellation Notice
   • No-Show   → Missed-Appointment Notice
   ↓
Update Status Columns in Google Sheets
```

## Why this design?

### Event-driven over one giant Zap

Rather than one large stateful workflow, the Google Sheet acts as the system of record, and each Zap is effectively a focused consumer of either the form-submit event or a specific column-change event — similar to how a backend service might listen to two kinds of webhooks.

### Scoped trigger columns

Zaps 4, 5, and 6 use Google Sheets' **"Updated Spreadsheet Row"** trigger with the **Trigger column** parameter locked to `Appointment Status`. Without this, any edit to *any* cell in a row (e.g. Reminder Status flipping to "Sent") would re-fire all three Zaps — wasting automation runs and risking duplicate emails. Scoping to one column means each Zap only wakes up when a human actually changes the Appointment Status.

### Filter by Zapier instead of native Paths

| Approach | Native Paths | Filter-gated separate Zaps (used here) |
|---|---|---|
| Trigger | One shared trigger for all branches | Each branch has its own trigger + filter |
| Debuggability | Harder to isolate one path's run history | Each Zap's history is independent |
| Extensibility | New branch = edit the shared Zap | New branch = add a new Zap |
| Step count per branch | Constrained to fit the shared structure | Each Zap sized to what its branch actually needs |

For a status-driven system where each branch needs a genuinely different number of steps (Cancelled/No-Show are 3 steps; Completed is 6 steps with a delay + two emails + two sheet updates), independent Zaps proved simpler to build, test, and reason about individually.

### Delay handling

- **24-Hour Reminder (Zap 3):** Formatter by Zapier parses the raw form date/time strings into a proper datetime, feeding a `Delay Until` step calculated as 24 hours before the appointment.
- **Post-visit Follow-up (Zap 4):** a `Delay For` step (~1 day) after Completed status is detected, before the follow-up + review-request emails.

## Testing approach

Every step of every Zap was tested individually in Zapier's step-level test runner before publishing, using a single seeded fictional patient record, sent only to an approved test inbox — never real patient data. Filter steps were validated for both the "should continue" and "should not continue" case (e.g. confirming the Cancelled filter correctly blocks a row whose status is `Confirmed`).
