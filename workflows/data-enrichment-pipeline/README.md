# Data Enrichment Pipeline

This workflow enriches existing records (e.g. leads, accounts, users) by fetching additional information from external APIs or internal services, then updates the source with the new data.

It’s built to run in batches and produce a simple summary of what happened.

---

## Use cases

- Enrich leads with company data, industry, size, website, etc.  
- Score records (e.g. lead scoring) based on rules or external signals.  
- Clean up and normalize existing data (domains, names, categories).  
- Periodically refresh a dataset stored in Google Sheets or a database.

---

## Workflow overview

The typical execution:

1. **Trigger – Manual or Scheduled**
   - Can be launched manually or via a Cron node (e.g. every night).

2. **Read source records**
   - Google Sheets, database, CSV, or an API.
   - Selects all rows or only rows that match a condition (e.g. “not yet enriched”).

3. **Loop / Split in batches**
   - Iterates over records one by one or in small chunks.
   - Each item represents one entity to be enriched.

4. **Enrichment – HTTP Request / internal API**
   - Calls an external service or internal API, passing a key (e.g. email, domain).
   - Receives additional fields, such as:
     - company name
     - website
     - industry
     - location
     - enrichment score, etc.

5. **Combine & normalize (Function node)**
   - Merges original and enriched data.
   - Handles missing or partial information.
   - Optionally computes custom scores or tags.

6. **Update source**
   - Writes back to the original storage:
     - update existing rows (Sheets/DB)
     - set “enriched = true”
     - add columns for new fields

7. **Summary**
   - At the end, generates a summary:
     - number of records processed
     - number successfully enriched
     - number failed / skipped
   - Optionally sends this summary via email / chat.

---

## Inputs

### Source data

The workflow expects a list of records with at least one **key field**:

- For leads: `email` or `domain`
- For companies: `domain` or `companyName`
- For users: `userId`, `email`, etc.

Example row (Google Sheets):

- `email`: `john@example.com`  
- `company`: `Example Inc`  
- `enriched`: `FALSE`  

You can adapt the schema to your own storage.

---

## Outputs

1. **Updated records**
   - Source entries updated with new fields:
     - e.g. `industry`, `companySize`, `country`, `score`, `enriched = TRUE`

2. **Summary object**
   - Example structure:
     ```json
     {
       "total": 50,
       "success": 43,
       "failed": 7,
       "timestamp": "2025-01-01T10:30:00Z"
     }
     ```

3. **Notification (optional)**
   - A message or email summarizing the run.

---

## Setup & configuration

1. **Import the workflow**
   - In n8n, import `data-enrichment-pipeline.json`.

2. **Configure the source**
   - Point the “Read” node to your actual data:
     - Google Sheets
     - Database (MySQL, Postgres, etc.)
     - API endpoint

3. **Define the enrichment step**
   - Configure the HTTP Request node (or similar) with:
     - base URL
     - API key (if required)
     - query parameters / body (e.g. `email`, `domain`)

4. **Map fields**
   - In the Function / Set nodes, map:
     - source fields → enrichment request
     - enrichment response → output fields

5. **Configure update**
   - In the “Update” node, set:
     - how to identify records (e.g. row index, primary key)
     - which columns/fields to update

6. **Add schedule (optional)**
   - Add a Cron node to run daily, weekly, etc.

---

## Customization ideas

- Add a “dry run” mode that logs what would be updated without actually writing.
- Implement multiple enrichment sources and merge their results.
- Add retry logic for failed API calls.
- Store failures in a separate sheet / table for manual review.

---

## Notes

This pipeline is meant as a **generic template**:
- input → enrichment → update + summary  
- it can be adapted to any domain where records need to be progressively improved over time  
- the exact fields and enrichment API are intentionally left flexible so you can plug in whatever service you use.