# Rooster Monitor — CLAUDE.md

## What this prototype is

A self-contained clinical operations coordinator dashboard for **Rooster** — OGP's critical result notification system deployed across 8 Singapore public health institutions (SGH, TTSH, NUH, CGH, KKH).

This is a **portfolio artefact** built by Aceson Aw as part of an OGP application. It is not a production codebase. It opens directly in any browser with no build step.

**Stack:** CDN React 18 + Babel standalone (in-browser JSX) + Tailwind CDN. Everything is in `index.html`.

---

## What Rooster actually does (ground your changes in this)

Source: https://reports.open.gov.sg/rooster/overview and `../rooster_audit.md`

- Routes **critical lab results** (require response ~within 1 hour) and **unexpected results** (e.g., incidental tumour findings — significant but not immediately life-threatening) to the right clinician via SMS
- Clinicians respond with a single digit: **1** (acknowledged), **2** (redirected), **3** (declined/unavailable)
- **Automatic escalation** fires if no acknowledgement — escalation is core routing logic, not an edge case
- Deployed across **8 PHIs** with a median delivery time of **7.5 minutes** and a **3.6/5 satisfaction score**
- Three actor types: lab/admin staff (trigger alerts), clinicians (receive and respond), hospital admins (configure routing rules)

---

## Design principles derived from the audit

These are the constraints that should govern any changes to this prototype:

### 1. The 7.5-min median is the wrong anchor — design for the tail
Colour-code and surface **outlier alerts** (P90+), not average performance. The 7.5-min threshold in the current prototype is used for stat card colouring — this is intentional. Do not remove it.

### 2. Escalation depth is semantically important
The dot markers (1/3, 2/3, 3/3) in each alert row represent escalation depth — this maps to Rooster's actual SMS escalation chain. Do not flatten these into a generic "status" field.

### 3. Critical vs unexpected result types must remain distinct
The taxonomy distinguishes urgency semantics. Do not collapse them into a single "alert type" category. Alert fatigue and under-triage are the failure modes on either side.

### 4. The 1/2/3 SMS reply codes are surfaced per alert
Each alert row shows the acknowledgement response code (1, 2, or 3). This is domain-accurate — clinicians respond with a digit, not free text.

### 5. Coordinator view, not clinician view
This dashboard is for the **clinical coordinator** monitoring the overall picture across hospitals — not for the clinician receiving the SMS. The clinician UX problem (3.6/5 satisfaction, context-poor acknowledgement) is a separate design surface not shown here.

---

## What this prototype shows

- Header: hospital cluster name, live clock, coordinator name, pulsing critical-count badge
- Ticker strip: scrolling unacknowledged critical alerts
- Stat cards: Active, Escalated, No Coverage, Resolved Today, Avg Response Time, Total
- Filter bar: hospital toggle (SGH/TTSH/NUH/CGH/KKH), status, priority, search, sort
- Alert table: 10 columns, 4 states (active/escalated/resolved/no-coverage), escalation depth indicators, SMS reply codes
- Click-to-expand: full routing timeline, response code, action buttons
- Footer: SMS gateway and HIS integration status

## What this prototype intentionally does NOT show

- Clinician-facing SMS ack UX (the 3.6/5 problem)
- Hospital admin routing configuration surface (the "high-stakes admin" problem)
- Rule simulation ("if this result came in now, who receives it?")
- Audit trail for configuration changes

These are called out in `../rooster_audit.md` as design opportunities — they are future scope, not gaps in this prototype.

---

## If you add features

- Stay within the single `index.html` file — no build step, no external files
- All new data should go in the `// ─── DATA ───` section at the top of the script
- Use the existing colour tokens (`accent-blue`, `accent-red`, `accent-orange`, `accent-green`) — do not introduce new colours without a clinical semantic reason
- Do not add features that contradict the audit findings above (e.g., do not add a feature that treats escalation as optional)
- If you add a new screen or panel, it should address one of the three design opportunities in `../rooster_audit.md` — not a generic dashboard feature
