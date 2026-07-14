# Review Findings — hrm-module-explorer v2 output (joint audit)

**Reviewers:** Monica (manual line-by-line review against the live application) and Claude Fable 5 (independent audit of the output against the answer key and the full session transcript, 59 interactions).

**Date:** 2026-07-13

**Provenance labels:** **[Manual]** = found by Monica's review · **[Fable 5]** = found only by the AI audit (missed by manual review) · **[Both]** = independently found, or manual finding reclassified by transcript evidence.

**Legend:** C1–C4 = Constraints 1–4 in SKILL.md.

*Volatile data* = claim was accurate at run time (confirmed in transcript snapshots) but no longer true at review time — recording it violates C2.

| # | Found by | Location in output | Claim | Finding | Classification |
|---|----------|--------------------|-------|---------|----------------|
| 1 | [Both] | Job Titles | Job Description "(some rows show 'Show More')" | No such rows at review time; transcript shows 14 "Show More" occurrences at run time | C2 violation (volatile data) |
| 2 | [Manual] | Pay Grades | "each associated with one or more currencies" | Only single-currency examples visible; generalization from one opened grade (Grade 3, transcript click 15) | C1 violation (overgeneralization stated as fact) |
| 3 | [Manual] | Employment Status | "Observed values: Full-Time Contract, … Part-Time Internship" | Data list recorded into durable docs | C2 violation |
| 4 | [Manual] | General Information | "Toggle 'Edit' switch to make fields editable" | Toggle never clicked (no transcript interaction) — behavior assumed, no "(assumed, not verified)" marker | C1 violation |
| 5 | [Manual] | Locations | Buttons | Search/Reset buttons present on the page but missing from the doc | Completeness gap |
| 6a | [Manual] | Structure | "child nodes expandable via a toggle button" | Nodes are expandable regardless of the Edit toggle state (manual check) | C1 violation (unverified/misleading behavior claim) |
| 6b | [Manual] | Structure | "(observed: Administration, Engineering, …)" node names | Data recorded into durable docs | C2 violation |
| 6c | [Manual] | Structure | "'Edit' toggle switch to modify the tree" | Toggle never clicked — assumed, unmarked | C1 violation |
| 7 | [Manual] | Nationalities | "near-complete world list of 199 entries out of the box" | Record count recorded; "out of the box" infers defaults from current state | C2 violation + C1 ("default" inferred) |
| 8 | [Manual] | Corporate Branding | "(color pickers)" | Pickers never clicked; control type assumed from appearance | C1 violation (minor) |
| 9 | [Manual] | Email Subscriptions | Row list "Leave Applications … Leave Rejections" | Row contents recorded; these are fixed system notification types (not user data), but C2 prohibits recording any row contents regardless — structure is the table itself, not what it contains | C2 violation |
| 10 | [Both] | Localization | "Date Format dropdown (observed default: yyyy-dd-mm)" | Live app shows yyyy-mm-dd at review time; transcript snapshot shows `yyyy-dd-mm ( 2026-13-07 )` at run time. "Default" also infers from current state | C2 violation (volatile data) + C1 ("default" inferred) |
| 11 | [Manual] | Language Packages | "Observed packages: Chinese (Simplified), …" (9 named) | List contents recorded into durable docs | C2 violation |
| 12 | [Both] | Social Media Authentication | "Add Provider form: Name*, Provider URL*, Client ID*, Client Secret*, Cancel/Save" | Verified in transcript (click 54: Add provider button) — form actually opened | ✅ Observed — no violation |
| 13 | [Manual] | Register OAuth Client | Pre-registered client name/URI/status recorded | Record contents recorded into durable docs | C2 violation |
| 14a | [Manual] | LDAP Configuration | Field defaults recorded (Host `localhost`, Port `389`, `cn`, `objectClass=person`, Sync Interval `1`) — only for some fields | Current values recorded as "defaults"; volatile and inferred | C2 violation + C1 ("default" inferred) |
| 14b | [Both] | LDAP — Bind Settings | "'Bind Anonymously' toggle (checked by default)" | Unchecked at review time; transcript snapshot shows `checkbox [checked]` at run time. "By default" is an inference | C2 violation (volatile data) + C1 ("default" inferred) |
| 14c | [Manual] | LDAP — Data Mapping | "each with a checkbox to use that field as the employee/user mapping key" | False: only Work Email and Employee Id carry that control, and it is a toggle, not a checkbox | C1 violation (false overgeneralization) + C4 violation |
| 15 | [Manual] | Relationships to Other Modules | Entire section | Cross-module effects never observed — no navigation to PIM, no module toggled; same issue as v1 | C1 violation / interpretation unlabeled (template gap, carried to v3) |
| 15b | [Both] | Purpose, Business Context, "Who uses it" (all entries); "What it does" purpose/function statements (e.g., "used for reporting/classification", "controlling which items appear in the sidebar", "Controls which notification types are active") | Entire interpretive sections plus function-statement wrapping within "What it does" | Content originates from domain knowledge and extrapolation, not observable UI; unverifiable against the structural answer key and unlabeled as interpretation | Template epistemology gap (excluded from scoring; labeling recommended for v3) |
| 16 | [Manual] | Modules | "Checkbox toggle per module", "disabled checkboxes" | Elements render as switches/toggles to the user | C4 violation |
| 17 | [Fable 5] | Transcript clicks 16–17 (Pay Grades) | Click described by the skill as "Edit icon for Grade 3" triggered a delete confirmation ("Are you Sure?"); skill clicked "No, Cancel" | A delete control was activated on the shared demo — either mistargeted or misidentified; the confirmation dialog was the only safeguard. No data destroyed | C3 near-miss + element mistargeting |
| 18 | [Fable 5] | Users — Add User form | "Employee Name* (typeahead)" | DevTools DOM inspection confirms the field is an autocomplete component (class oxd-autocomplete-text-input). The skill's claim was correct, though it could not verify this from the accessibility tree alone — the accessible name "Type for hints..." visible in the snapshot suggests autocomplete behavior but does not confirm it conclusively.| ✅ Observed — no violation |
| 19 | [Fable 5] | Users — table columns | Checkbox column omitted from the documented column list | Present in answer key and on the page | Completeness gap |
| 20 | [Fable 5] | Job Categories, Education, Licenses, Memberships | Demo record values leaked as examples: "(e.g., Professionals, Sales Workers)", "(e.g., Bachelor's Degree, MBA)", "(e.g., PMP Certification, CCNA)", "(e.g., IEEE Membership, CIMA)" | Actual table contents recorded into durable docs disguised as illustrative examples | C2 violation (via "e.g.") |
| 21 | [Fable 5] | General Information | "Number of Employees (read-only, system-derived)" | "System-derived" is a mechanism guess (repeat of v1's "system-calculated"); only read-only state is observable | C1 violation |
| 22 | [Fable 5] | Email Configuration | "Sendmail shows a read-only 'Path to Sendmail'" | Radio never switched; "read-only" and the shows-when-selected dependency both unverified — **repeat of the v1 inference** | C1 violation |
| 23 | [Fable 5] | Localization | "Language dropdown (observed default: English (United States))" | Same class as #10: current value recorded as default | C2 violation + C1 ("default" inferred) |


## Summary

23 findings (1 explicitly verified-correct). By constraint: **C2 — 12** (three proven accurate-at-run-time by the transcript, demonstrating why the rule exists), **C1 — 11** (unmarked assumptions and inferred defaults, incl. one false overgeneralization and two repeats of v1 inferences), **C4 — 2**, **C3 — 1 near-miss** (with element mistargeting), **completeness — 2**, **template design gap — 1** (covers interpretive sections and "What it does" function statements).

Manual review found 15 of 23; finding #18 reclassified to verified-correct after DevTools check of the correct field; the AI transcript/output audit added 8 (findings 17–23), of which the C3 near-miss (#17) and the "e.g." data leak (#20) are the most significant. Constraints were present in SKILL.md v2 but not enforced during documentation writing — see `hrm-module-explorer-validation-v2.md` for the consolidated verdict and v3 recommendations.