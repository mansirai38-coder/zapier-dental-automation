# Field / Data Mapping

## Google Form → Google Sheets

| Form Field | Sheet Column | Type | Notes |
|---|---|---|---|
| Patient Name | Patient Name | Text | Used in calendar event title and every email greeting |
| Email | Email | Email | Recipient for every automated email |
| Phone | Phone | Text | Stored for staff reference only — never sent in emails or calendar events |
| Patient Type | Patient Type | Choice: `New Patient` / `Existing` | Drives the Zap 2 welcome-email filter |
| Preferred Date | Appointment Date | Date | Used for reminder-delay calculation and calendar event |
| Preferred Time | Appointment Time | Time | Used for reminder-delay calculation and calendar event |
| Reason for Visit | Reason for Visit | Paragraph | Internal reference only — deliberately excluded from the calendar event |

## Automation-managed columns (not from the form)

| Sheet Column | Set By | Values |
|---|---|---|
| Appointment Status | Clinic staff (manual edit) | `Confirmed` / `Completed` / `Cancelled` / `No-Show` |
| Reminder Status | Zap 3 | blank → `Sent` |
| Follow-up Status | Zap 4 | blank → `Sent` |
| Review Request Status | Zap 4 (proposed extension — see [`zap-configuration.md`](zap-configuration.md)) | blank → `Sent` |

## Email personalization tokens

Every automated email uses live Zapier field references, e.g. `{{Patient Name}}`, `{{Email}}` — pulled directly from the trigger step's data, never hardcoded. See [`examples/sample-email.md`](../examples/sample-email.md) for a rendered example.
