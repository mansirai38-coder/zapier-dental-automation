# Zap Configuration (Field-Level Detail)

All configuration below reflects Zaps that were actually built and tested. Nothing here is a proposal — where something is a suggested future addition, it's called out explicitly as **Proposed / Not Implemented**.

---

## Zap 1 — New Appointment Confirmation & Calendar

**Trigger:** Google Forms → New Form Response
**Form:** Dental Clinic Appointment Request

1. **Trigger — Google Forms: New Form Response**
2. **Action — Google Sheets: Create Spreadsheet Row** — maps form fields to columns (see [`field-mapping.md`](field-mapping.md)); sets initial `Appointment Status = Confirmed`
3. **Action — Formatter by Zapier: Date / Time** — parses Preferred Date + Preferred Time into a usable datetime
4. **Action — Google Calendar: Create Detailed Event** — title `Dental Appointment - {{Patient Name}}`; **no sensitive info** (no reason for visit, phone, or email) in the event
5. **Action — Gmail: Send Email** — confirmation email to `{{Email}}`

---

## Zap 2 — New Patient Welcome Email

**Trigger:** Google Forms → New Form Response

1. **Trigger — Google Forms: New Form Response**
2. **Filter — Filter by Zapier** — `Patient Type` (Text) Exactly matches `New Patient`
3. **Action — Gmail: Send Email** — subject `Welcome to Our Dental Clinic, {{Patient Name}}`

Tested: filter correctly passed for `New Patient` and would block `Existing`.

---

## Zap 3 — 24-Hour Appointment Reminder

**Trigger:** Google Forms → New Form Response

1. **Trigger — Google Forms: New Form Response**
2. **Action — Formatter by Zapier: Date / Time** — parses appointment datetime
3. **Action — Delay by Zapier: Delay Until** — 24 hours before the appointment
4. **Action — Gmail: Send Email** — reminder email
5. **Action — Google Sheets: Lookup Spreadsheet Row**
6. **Action — Google Sheets: Update Spreadsheet Row** — sets `Reminder Status = Sent`

---

## Zap 4 — Appointment Completed - Follow-up & Review Request

**Trigger:** Google Sheets → Updated Spreadsheet Row (Instant)
**Trigger column:** `Appointment Status`

1. **Trigger — Google Sheets: Updated Spreadsheet Row** (scoped to Appointment Status column only)
2. **Filter — Filter by Zapier** — `Appointment Status` Exactly matches `Completed`
3. **Action — Delay by Zapier: Delay For** — ~1 day
4. **Action — Gmail: Send Email** — follow-up email ("How was your visit?")
5. **Action — Google Sheets: Update Spreadsheet Row** — sets `Follow-up Status = Sent`
6. **Action — Gmail: Send Email** — review-request email, with a `[GOOGLE REVIEW LINK]` placeholder to be replaced with the clinic's real review URL

> **Proposed / Not Implemented:** a 7th step updating `Review Request Status = Sent` after step 6 — a straightforward extension not yet added.

---

## Zap 5 — Appointment Cancelled - Notification

**Trigger:** Google Sheets → Updated Spreadsheet Row (Instant)
**Trigger column:** `Appointment Status`

1. **Trigger — Google Sheets: Updated Spreadsheet Row** (scoped to Appointment Status column only)
2. **Filter — Filter by Zapier** — `Appointment Status` Exactly matches `Cancelled`
3. **Action — Gmail: Send Email** — cancellation notice

Tested: filter correctly blocked when status was `Confirmed`, correctly passed and sent when tested with `Cancelled`.

---

## Zap 6 — Appointment No-Show - Notification

**Trigger:** Google Sheets → Updated Spreadsheet Row (Instant)
**Trigger column:** `Appointment Status`

1. **Trigger — Google Sheets: Updated Spreadsheet Row** (scoped to Appointment Status column only)
2. **Filter — Filter by Zapier** — `Appointment Status` Exactly matches `No-Show`
3. **Action — Gmail: Send Email** — missed-appointment notice

Tested: filter correctly blocked when status was `Confirmed`; Send Email step verified via Zapier's "Data in" preview before publishing.

---

## Not implemented in this version (explicitly out of scope)

- SMS/WhatsApp reminders (Twilio) — **Proposed**, see main README Future Improvements
- CRM integration — **Proposed**, not built
- Payment/billing integration — **Not part of this project's scope**
