# AI Document Processor

This workflow turns unstructured text (emails, reports, notes, etc.) into **structured, actionable output** using an AI model (e.g. OpenAI).

It can summarize content, extract key points, classify the document, and optionally send the result via email or store it in a database / spreadsheet.

---

## Use cases

- Summarizing long email threads or support tickets  
- Turning meeting notes into clear summaries and bullet points  
- Classifying incoming documents (e.g. “Sales / Support / Internal”)  
- Generating concise reports from raw text dumps

---

## Workflow overview

Typical execution steps:

1. **Trigger – Webhook or Manual**
   - Receives an input payload with raw text (e.g. `content` field).
   - Can also be triggered manually from n8n for testing.

2. **Pre-processing (optional)**
   - Clean up whitespace, strip HTML, limit content length if needed.
   - Combine multiple fields into a single `content` string if they come separately.

3. **AI Processing – OpenAI (or similar)**
   - Sends the text to a language model with a carefully designed prompt:
     - Summarize the content
     - Extract 3–7 key bullet points
     - Classify the document into one of a predefined set of categories
   - The AI node returns structured JSON.

4. **Normalize AI response (Function node)**
   - Ensures the output has a stable shape, for example:
     ```json
     {
       "summary": "...",
       "keyPoints": ["...", "..."],
       "category": "Support",
       "originalLength": 2345
     }
     ```

5. **Output stage**
   - Option A: store in Google Sheets / DB as a new row/record
   - Option B: generate a PDF with the summary and key points
   - Option C: send an email with the processed result to a recipient

6. **(Optional) Logging & error handling**
   - Logs AI errors or timeouts for debugging.

---

## Inputs

### Webhook payload

Expected structure (can be adapted):

- `content` – string, the full text of the document / email / notes  
- `title` (optional) – string, a title or subject  
- `meta` (optional) – any additional metadata you want to carry along

Example:

```json
{
  "title": "Weekly sales meeting notes",
  "content": "We discussed Q3 targets, major accounts, and pipeline risks...",
  "meta": {
    "source": "meeting-notes",
    "author": "John Doe"
  }
}