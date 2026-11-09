# Product Roadmap

Current baseline: v1.0.0 — reusable job-search and screening instructions.

## Product principle

Build in this order: reliable discovery → structured tracking → controlled automation. Keep the system low-cost and practical. Do not automate a workflow until its manual version is repeatable and its data fields are stable.

Candidate hard gates remain authoritative across all versions:
- Shenzhen-based opportunities.
- Amazon / cross-border e-commerce operations roles, with Amazon apparel experience as a strong match.
- Fixed monthly base salary target: RMB 12,000 or above.
- Total compensation must never be substituted for fixed base salary.
- Unknown base salary is pending verification, not a pass.
- No HR message or application submission without explicit, job-specific user approval.

## Version plan

| Version | Outcome | Main deliverables | Exit criteria |
|---|---|---|---|
| v1.0.0 | Manual, repeatable screening | Core Skill, candidate profile, scoring rules, CSV tracker | Search report distinguishes verified, pending and excluded jobs |
| v1.1.0 | Platform-aware discovery | BOSS直聘 and 猎聘 source adapters; normalized job schema; verification checklist; deduplication rules | Each record has source URL, capture date, verification state and salary decomposition |
| v1.2.0 | Feishu job-management workspace | Bitable schema, linked tables, views, import templates, operating SOP | A job can be tracked from discovery through HR contact, application and interview |
| v2.0.0 | Scheduled monitoring agent | Daily run, change detection, duplicate matching, scoring, notifications, audit log and approval gates | Repeated runs are idempotent; alerts explain evidence; no unapproved external action |

## v1.1.0 — Platform adapters

Scope:
1. BOSS直聘: search by role and location; capture public/accessible job details; record recruiter/company metadata when visible.
2. 猎聘: equivalent search and capture workflow, with source-specific fields mapped into the common schema.
3. Normalize salary into base_salary_min, base_salary_max, total_comp_min, total_comp_max, salary_period and salary_notes.
4. Store original URL, source platform, source job ID (when visible), first-seen time, last-checked time and evidence notes.
5. Deduplicate cross-platform reposts using company + normalized role + location + responsibility similarity. Preserve every source URL.
6. Add freshness and evidence labels: current/uncertain/closed; verified/partial/pending.

Non-goals:
- Do not bypass login, CAPTCHA, rate limits, access controls or platform anti-automation mechanisms.
- Do not scrape private account pages or send messages automatically.
- Do not infer salary components from a headline range.

Acceptance:
- A sample batch can be normalized into one schema.
- Same job from two platforms is grouped without losing either source link.
- Fixed base salary is separately represented and unknown values remain null/pending.
- Every result can be traced to a source and checked date.

## v1.2.0 — Feishu Bitable workspace

Recommended tables:
1. Jobs — canonical job records and scoring.
2. Companies — company profile and due-diligence notes.
3. HR Contacts — recruiter/contact details voluntarily captured from accessible sources or user input.
4. Communications — date, channel, message draft/sent state, response and next step.
5. Applications — resume version, authorization, submission state and platform receipt.
6. Interviews — round, time, interviewer, preparation notes, outcome and follow-up.
7. Tasks — follow-ups, deadlines, reminders and owner.

Use linked-record fields for relationships; avoid duplicating company and contact details across job rows.

Integration choices:
- First release: CSV import/export and a manual operating SOP. This is the lowest-cost, least fragile path.
- Next: Feishu Open Platform custom app / Bitable API, after the user creates an app and grants only required scopes.
- Keep app credentials in a secret manager or environment variables; never commit app IDs, app secrets, access tokens or personal contact data to GitHub.
- Confirm the user's Feishu tenant, Base location and permissions before attempting writes.

Acceptance:
- Import templates create the required tables and field types.
- Views show today's follow-ups, jobs awaiting salary confirmation, priority jobs, applications in progress and upcoming interviews.
- Every application record links to a job and a confirmed resume version.
- Manual edits in Feishu are not overwritten by sync without conflict handling.

## v2.0.0 — Automated job-hunting agent

Daily pipeline:
1. Trigger on configured schedule (Asia/Shanghai).
2. Search enabled sources within permitted access.
3. Normalize and validate fields.
4. Match against existing jobs; update last-seen and changed fields.
5. Detect closed, stale or materially changed postings.
6. Apply hard gates, score and confidence labels.
7. Upsert records to Feishu (or produce an import file if API is not connected).
8. Send a concise digest with new, changed, high-priority and pending-verification jobs.
9. Create follow-up reminders only where user-approved.

Safety controls:
- Search and analysis may run automatically; messaging, resume sharing and applications require explicit user approval for each job/action.
- Never bypass platform controls or misrepresent the candidate.
- Store source URLs, timestamps and change history for audit.
- Use idempotency keys to prevent duplicate records and duplicate notifications.
- On source failure, report the failure; do not interpret missing results as zero jobs or closed postings.
- Provide pause/disable controls and a manual review queue.

Suggested alerts:
- New job passes fixed-base gate and score threshold.
- Base salary or job status changes.
- Follow-up due today.
- A priority job has not been checked recently.
- Source adapter fails or data becomes stale.

Acceptance:
- A dry run produces a preview without external writes.
- Re-running the same input does not create duplicate jobs.
- Salary hard gate and approval gates are covered by tests.
- Failures are visible and recoverable.
- User can pause monitoring and inspect the source evidence behind every alert.

## Dependencies and decisions

| Dependency | Needed by | Decision / user input |
|---|---|---|
| BOSS直聘 and 猎聘 access method | v1.1 | Public pages, permitted search, or user-provided links; no credential sharing required |
| Feishu tenant and Base location | v1.2 | Confirm which Feishu workspace/Base to use |
| Feishu app/API permissions | v1.2+ | User creates/authorizes app; grant minimum scopes |
| Notification channel and time | v2.0 | Choose Feishu message, email, or another connected channel; set local schedule |
| Hosting/scheduler | v2.0 | Start with available scheduled automation; external hosting only if required |

## Delivery sequence

1. Finish v1.1 data contract and platform-specific checklists.
2. Validate with a small manually collected batch before building integrations.
3. Build v1.2 Base tables and CSV templates; test import/export.
4. Connect Feishu API only after the manual schema is accepted.
5. Run v2.0 in dry-run mode, then enable read/notify automation.
6. Keep messaging and application actions approval-gated.
