# Changelog

All notable changes to this repository will be documented in this file.

---
# Online Coach Intake
## [2.0.0] - 2025-12-07

### Added
- **New vs Existing client routing**
  - `FindExistingClient` node to look up the client in the Google Sheets mini-CRM (by email).
  - `IsNewClient` IF node to branch into:
    - **New client path** (create + append)
    - **Existing client path** (update).

- **Update path with change tracking**
  - `MergeExistingClient` node to combine current sheet data with the latest payload.
  - `BuildChangeSummaryInput` Function node to prepare a machine-friendly description of the previous vs new values.
  - `GetChangesFromModel` (OpenAI) node to generate a human-readable **LastChangeSummary**.
  - `UpdateExistingClient` node to update the existing row, including:
    - status
    - goal
    - weekly workouts
    - contact window
    - `LastChangeSummary`.

- **AI & messaging improvements**
  - `GetCustomMessageFromModel` (OpenAI) node to craft:
    - a short encouragement message for the client
    - a higher-level summary for the coach.
  - `NormalizeAIResponse` node to normalise the OpenAI output.
  - `BuildNotificationPayload` node to build a single, clean payload for email + Telegram.

- **Client email v2**
  - Dynamic intro (`emailIntro`) that changes depending on whether this is a **new onboarding** or an **update**.
  - Re-usable HTML template (IronFlow Coaching) with:
    - current plan and weekly workouts
    - contact time window
    - latest update section
    - AI coach note.

- **Telegram notification v2**
  - Rich text message including:
    - new vs existing client title (e.g. *New client registered* / *Existing client updated*)
    - name, email, plan, workouts, goal
    - preferred contact time
    - Google Drive folder URL
    - AI-generated change summary
    - AI coach note
    - footer indicating the message was sent automatically by n8n.

- **Visual structure & documentation inside the workflow**
  - Added coloured annotations / swimlanes:
    - *Intake & Validation*
    - *Client Routing (New vs Existing)*
    - *Persistence Layer (Sheets + Drive)*
    - *AI & Messaging (Coach + Client)*
    - *Error Handling*.
  - Updated screenshots for v2 in the repository.

### Changed
- **Refined error handling**
  - Kept the invalid-lead path but clarified the responsibilities:
    - `IsDataValid` decides valid vs invalid.
    - Invalid leads are appended to `InvalidLeads` sheet and trigger a specific Telegram error message.
    - Webhook now returns a structured JSON error payload.

- **Messaging logic**
  - Centralised the data for Gmail + Telegram into a single `BuildNotificationPayload` step instead of duplicating logic in each node.
  - Ensured that both paths (new client / existing client update) feed the same normalised structure into AI + messaging.

### Fixed
- Fixed cases where the workflow would still treat an existing client as “new” due to missing / undefined `row_number`.
- Fixed mapping issues between the sheet row and the normalised payload (name, email, goal, workouts, contact window).
- Reduced risk of partial updates by making sure that all write operations happen only after validation + routing.

---

## [1.0.0] - 2025-12-03

Initial version of the **Online Coach Intake** workflow.

### Added
- **Basic intake pipeline**
  - Webhook to receive form submissions.
  - `NormalizeReceivedData` Function node to:
    - clean and normalise the incoming payload
    - validate required fields
    - build a consistent JSON structure.

- **Persistence**
  - `CreateNewFolder` node to create a personal folder for each new client in Google Drive.
  - `SaveNewClient` node to append the new client to a Google Sheets “Clients” tab (mini-CRM) with status `WELCOME_SENT`.

- **AI-assisted welcome message**
  - OpenAI node to generate a short, personalised encouragement message based on:
    - goal
    - plan duration
    - weekly workouts.
  - HTML welcome email:
    - dark / orange IronFlow theme
    - client details
    - encouragement message
    - booking call button.

- **Coach notification**
  - Telegram message to the coach with:
    - client name + email
    - basic plan info
    - client goal
    - note that the message was sent automatically via n8n.

- **Error path for invalid data**
  - If validation fails:
    - append raw payload + errors to an `InvalidLeads` sheet
    - send a Telegram error notification
    - return a JSON error to the webhook caller.

---

## [Unreleased]

Ideas / planned for future versions (v3 and beyond):

- Multi-language support for messages (EN/IT).
- Support for multiple offer types / coaching products.
- Integration with a calendar / booking system (auto-create onboarding calls).
- Additional analytics (conversion rate, lead source tracking, drop-off points).
