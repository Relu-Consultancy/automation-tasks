# 📌 Automation System – Scenario Documentation

## 🧠 Overview

This system is built using **Make (Integromat)** to automate content discovery, processing, and engagement workflows.
It follows a **state-driven architecture**, ensuring idempotency, scalability, and no duplicate processing.

---

## ⚙️ Core Design Principles

* **Idempotency First**
  Every scenario uses an `idempotency_key` to prevent duplicate execution.

* **State Machine Driven**
  Airtable fields like `status` (`new`, `queued`, `in_brief`, etc.) control flow.

* **Single Source of Truth**
  Airtable acts as the central data layer.

* **No Re-fetching After Processing**
  Always pass forward bundles instead of re-querying Airtable.

* **Error Handling via Error Routes**
  Failures are logged using Make’s **error handler**, not routers.

---

## 🔁 Scenario Architecture Pattern

Each scenario follows this structure:

1. **Fetch Data**

   * Airtable search (filtered by status)

2. **Transform / Prepare**

   * Clean data (e.g., remove URL params)
   * Generate variables

3. **Idempotency Check**

   * Airtable lookup using unique key

4. **Router (Decision Node)**

   * Skip if already processed
   * Continue if new

5. **Action Execution**

   * PhantomBuster / HTTP / Email / Processing

6. **Logging**

   * Create Event Log (`status = ok / failed`)

7. **State Update**

   * Update Airtable records to next stage

---

## 📂 Implemented Scenarios

### ✅ Scenario 2 – Topic Radar Processing

* Fetch topics
* Rotate keywords
* Launch Phantom agents
* Update processing state

---

### ✅ Scenario 3 – Prospect Radar (Commenters Export)

* Extract post URLs
* Clean URLs (remove tracking params)
* Idempotency check using:

  ```
  prospect_radar|YYYY-MM-DD|post_url
  ```
* Launch Phantom for commenters

---

### ✅ Scenario 4 – Signal Processing

* Process extracted data
* Normalize and store signals
* Prepare for downstream usage

---

### ✅ Scenario 5 – Daily Brief Email

* Aggregate signals (`status = new`)
* Send email via Gmail
* Log event: `daily_brief_send`
* Bulk update signals → `status = in_brief`

⚠️ Critical:
Without this update, signals repeat in every email.

---

### ✅ Scenario 6 – Engagement / Actions

* Handle user-triggered actions
* Maintain state consistency

---

### ✅ Scenario 7 – Queue Management

* Manage content queues
* Respect limits (e.g., max daily actions)

---

### ✅ Scenario 8 – Supporting Automations

* Utility workflows
* Data sync / maintenance logic

---

## 🚨 Error Handling

* Implemented via **Error Handler Routes**
* Logs include:

  * `status = failed`
  * `error.message`
* Failed executions do NOT update state

---

## 🔑 Key Rules

* Never update status unless action succeeds
* Never re-fetch data after aggregation
* Always use consistent idempotency keys
* Always log events (success + failure)

---

## 🚀 Outcome

* No duplicate processing
* Clean state transitions
* Scalable automation pipeline
* Reliable daily workflows

---
