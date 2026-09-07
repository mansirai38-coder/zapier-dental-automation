# Setup Instructions

Follow these steps to configure this automation from scratch in your own Zapier + Google Workspace account.

## 1. Google Form

Create a new Google Form named **"Dental Clinic Appointment Request"** with these fields:

| Field | Type |
|---|---|
| Patient Name | Short answer |
| Email | Short answer (validated as email) |
| Phone | Short answer |
| Patient Type | Multiple choice: `New Patient` / `Existing` |
| Preferred Date | Date |
| Preferred Time | Time |
| Reason for Visit | Paragraph |

## 2. Google Sheet

Link the form to a Google Sheet (Form → Responses tab → green Sheets icon), then add these extra columns manually so the automation can write to them:

```
Appointment Status | Reminder Status | Follow-up Status | Review Request Status
```

See [`examples/sample-appointment-request.json`](../examples/sample-appointment-request.json) for the exact field shape.

## 3. Connect apps in Zapier

In your Zapier account, connect:
- Google Forms
- Google Sheets
- Google Calendar
- Gmail

Use OAuth "Sign in with Google" for every connection — never paste API keys or passwords into Zapier fields manually.

## 4. Build the 6 Zaps

See [`automation/zap-configuration.md`](../automation/zap-configuration.md) for the exact field-by-field configuration of each Zap. In summary:

| # | Zap Name | Trigger |
|---|---|---|
| 1 | New Appointment Confirmation & Calendar | Google Forms – New Form Response |
| 2 | New Patient Welcome Email | Google Forms – New Form Response |
| 3 | 24-Hour Appointment Reminder | Google Forms – New Form Response |
| 4 | Appointment Completed – Follow-up & Review Request | Google Sheets – Updated Row |
| 5 | Appointment Cancelled – Notification | Google Sheets – Updated Row |
| 6 | Appointment No-Show – Notification | Google Sheets – Updated Row |

**Important for Zaps 4–6:** on the Google Sheets trigger step, set the **Trigger column** field to `Appointment Status`. This stops the Zap from re-firing every time an unrelated column (like Reminder Status) is edited.

## 5. Replace the placeholder review link

In Zap 4's review-request email, replace any `[GOOGLE REVIEW LINK]` placeholder with your clinic's real Google Business review URL before going live.

## 6. Test before going live

- Add one fictional row (see `examples/sample-appointment-request.json`) and manually test each Zap step in Zapier's step-by-step tester.
- For Zaps 4–6, test the Filter step against both a matching and a non-matching status to confirm it blocks/passes correctly.
- Only connect real patient data once every step shows a green checkmark.

## Security reminder

Never commit real patient data, Zapier webhook URLs containing tokens, or Google OAuth credentials to this repo. See [`.gitignore`](../.gitignore) for what's already excluded.
