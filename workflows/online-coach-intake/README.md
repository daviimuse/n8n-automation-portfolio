# Online Coach Lead Intake Automation (n8n)

## Problem

Online coaches usually collect leads via forms or DMs and then:
- copy/paste data into sheets
- manually create folders for each client
- send a welcome email from scratch
- forget to log invalid / incomplete submissions

This is slow, error-prone and impossible to scale.

## What this workflow does

When a new lead submits a form (via webhook):

1. **Normalize & validate input**
   - required fields: name, surname, email, servicePack, weeklyWorkouts, mainGoal, contactTimePreference
   - email + weeklyWorkouts are strictly validated
   - each lead gets a stable `clientId`

2. **Happy path (valid leads)**
   - creates a client folder in Google Drive
   - appends a row to a Google Sheet (`Clients`) with:
     - personal data, plan, goal
     - folder URL
     - `status = WELCOME_SENT`
   - generates a short, personalized encouragement message using OpenAI
   - sends a **HTML welcome email** to the client
   - sends a **Telegram message** to the coach with:
     - all client details
     - goal
     - folder URL
     - AI message

3. **Error path (invalid leads)**
   - logs the raw payload + validation errors into a separate sheet (`InvalidLeads`)
   - sends an internal Telegram alert to the coach
   - returns a JSON error from the webhook with the validation errors

## Tech stack

- n8n Cloud
- Webhook trigger
- Function / IF / Merge nodes
- Google Sheets
- Google Drive
- OpenAI (Chat Completions / Responses API)
- Gmail
- Telegram

## HTTP responses

- `200 OK`  
    ```json
    { "status": "ok", "message": "Onboarding started.", "clientId": "..." }

- `400 Bad Request`
    ```json
    { "status": "error", "message": "Invalid data.", "errors": [ "…", "…" ] }

- `Example payload`
    ```json
    {
    "name": "Davide",
    "surname": "Musitelli",
    "email": "example@example.com",
    "servicePack": "6months",
    "weeklyWorkouts": 4,
    "mainGoal": "I'd like to gain weight, get stronger and bench 100kg.",
    "contactTimePreference": "18.15-20.15",
    "timestamp": "2025-12-03T19:12:13.614Z"
    }
    