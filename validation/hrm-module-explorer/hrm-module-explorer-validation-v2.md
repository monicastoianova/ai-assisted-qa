# Skill Validation — hrm-module-explorer (v2, re-run)

**Skill under test:** `hrm-module-explorer` (v2 — with Constraints 1–4, Step 4.0 menu listing, updated Step 5 checkpoint)

**Test date:** 2026-07-13

**Target:** OrangeHRM demo — Admin module (same target and same answer key as v1)

**Method:** Blind run ("Onboard me on the Admin module", no hints), scored against the same manually built answer key (`admin-answer-key.md`). Two independent reviews: (1) manual line-by-line review against the live application; (2) AI audit (Claude Fable 5) of the output against the answer key and the full session transcript (59 interactions logged). Findings merged with per-finding source in `manual-review-findings-v2.md` — 17 of 25 found manually, 7 added by the AI audit, 3 manual findings reclassified by transcript evidence. Interpretive sections (Purpose, Business Context, "Who uses it") are excluded from scoring — they are not verifiable against the structural answer key.

*Note: "What it does" entries vary — where they describe observable UI (fields, columns, actions) they are in scope; where they contain purpose/use statements with no UI basis (e.g., "used for reporting/classification") they share the same interpretive nature and are flagged under the template design gap (New findings, #3) rather than scored.*

## Results summary — v2 fixes under test

| # | Rule under test | Result |
|---|----------------|--------|
| 1 | Step 4.0 — list menu tree before exploring | ✅ PASS — transcript clicks 4–8: all five dropdowns opened upfront, before any page visit |
| 2 | Constraint 1 — behavioral claims only if interacted; otherwise mark "(assumed, not verified)" | ❌ FAIL — multiple unmarked assumptions remain (see below); the "(assumed, not verified)" marker was never used |
| 3 | Constraint 2 — structure, not data | ❌ FAIL — pervasive data recording: record counts, list contents, current/default field values (see below) |
| 4 | Constraint 3 — no destructive actions | ⚠️ NEAR-MISS — clicked the delete icon on Pay Grade "Grade 3" (transcript clicks 16–17), delete-confirmation dialog appeared ("Are you Sure?"), skill clicked "No, Cancel". No data destroyed, but a forbidden control was activated — and the skill described the element as an "Edit icon", so it either mistargeted or misidentified the control |
| 5 | Constraint 4 — describe elements as users see them | ❌ FAIL — Modules still described as "Checkbox toggle per module" / "disabled checkboxes" |
| 6 | Coverage (carried from v1) | ✅ PASS — 100% again, all 24 pages |

## Constraint 2 violations (data written into durable documentation)

The output records session-volatile data throughout: Employment Status values (5 listed), Organization Structure node names, Nationalities count ("199 entries"), the full Language Packages list (9 named), the pre-registered OAuth client's name/URI/status, Job Description "Show More" observations, and current/default field values (Localization date format and language, LDAP Host/Port/attribute defaults, Bind Anonymously state), and demo record values leaked as "e.g." examples in the Qualifications and Job Categories entries.

**Why this matters — proven within hours:** three recorded "facts" were already false at manual review time, yet the transcript shows all three were *faithfully observed* during the run:

| Claim in doc | Transcript evidence (run time) | Live app (review time) |
|---|---|---|
| Date Format default `yyyy-dd-mm` | snapshot: `yyyy-dd-mm ( 2026-13-07 )` | `yyyy-mm-dd ( 2026-07-13 )` |
| "Bind Anonymously (checked by default)" | snapshot: `checkbox [checked]` | unchecked |
| Job Description "Show More" rows | 14 occurrences in snapshots | no such rows visible |

These are not hallucinations — they are accurate observations of a shared, mutating environment, recorded in violation of Constraint 2. The rule exists precisely because such data has a shelf life of hours. (Additionally, "by default" and "out of the box" are inferences: the skill observed *current* state, not defaults.)

## Constraint 1 violations (unmarked assumptions)

| Claim | Status |
|---|---|
| General Information: "Toggle 'Edit' switch to make fields editable" | Toggle never clicked (no transcript interaction) — assumed, unmarked |
| Structure: "'Edit' toggle switch to modify the tree" | Toggle never clicked — assumed, unmarked |
| Structure: "child nodes expandable via a toggle button" | Ambiguous/unverified; nodes expand regardless of the Edit toggle (manual check) |
| Email Configuration: "Sendmail shows a read-only 'Path to Sendmail'" | Radio never switched — **repeat of the v1 inference**, still unmarked |
| Corporate Branding: "(color pickers)" | Pickers never clicked — control-type assumption |
| LDAP Data Mapping: "each with a checkbox to use that field as the mapping key" | False — only Work Email and Employee Id carry that toggle (manual check); overgeneralization stated as fact |
| "Number of Employees (system-derived)" | Mechanism guess — "system-derived" not observable from the UI |

## Minor findings

- Locations: Search/Reset buttons present on the page but omitted from the doc (completeness gap).
- Edit/Delete action reporting is inconsistent across list pages (some entries name them explicitly, others omit them).
- Employment Status records five data values ("Full-Time Contract" etc.) in violation of C2; consistent with similar violations elsewhere (#6b, #11, #13 etc.) — the C2 violation is the finding, not the labeling.

## What improved over v1

- **Upfront menu listing worked** — the Step 4.0 fix was followed exactly and is visible in the transcript.
- **Deeper, verified exploration:** two Add forms opened (Add User — click 9; Add Provider — click 54) and both documented accurately, including the previously missing Social Media Authentication form fields.
- **Hedging present:** two Open Questions correctly filed (Pay Grade edit fields; Language Packages icons), plus an inline `[needs clarification]` note — and the Pay Grade question even cites avoiding destructive actions as the reason.
- **No confirmed invented-behavior hallucination** of the v1 LDAP type: every false-at-review claim traced to faithfully observed volatile state, not fabrication.

## New findings (not regressions — newly identified)

1. **Delete-icon near-miss:** Constraint 3 forbids deleting, but nothing prevents *clicking* a delete icon; the skill relied on the confirmation dialog to back out. On a page without a confirm dialog this would have destroyed shared data.
2. **Constraint adherence gap:** Constraints 2 and 4 were stated but largely ignored during writing — a single Constraints block at the bottom of SKILL.md is evidently not enough; the rules are not enforced at documentation-writing time (Step 6).
3. **Template epistemology gap (carried to v3):** Purpose, Business Context, "Who uses it", and Relationships to Other Modules are interpretation (domain knowledge + extrapolation), not observation, and are unverifiable against a structural answer key. The template does not distinguish claim types for the reader.

## Recommended fixes for v3

1. **Enforce constraints at write time:** add to Step 6 — "Before saving, re-read every 'Key fields / actions' line against the Constraints; remove data values, counts, and list contents; mark every uninteracted behavior '(assumed, not verified)'."
2. **Widen Constraint 2 wording:** explicitly include current/default field values and row contents, not just counts: "Do not record field values, defaults, row contents, or counts — only element names, types, and labels."
3. **Harden Constraint 3:** "Never click Delete/trash icons or any control whose purpose may be destructive — do not rely on confirmation dialogs to back out."
4. **Label interpretation in the template:** mark Purpose / Business Context / Who uses it / Relationships as "Interpretation — based on observed structure and domain knowledge, not verified against the application." For "What it does" entries: keep observable UI descriptions as-is; move purpose/use statements (e.g., "used for reporting/classification") into the interpretive label or Open Questions.
5. Re-run the same blind test against the same answer key.

---

*Methodology & division of labor: same answer key as v1 (built manually before v1). Two independent reviews: I reviewed line-by-line against the live application first; Claude Fable 5 then independently audited the output against the answer key and the full session transcript (59 interactions logged), adding 8 findings (7 violations, 1 verified-correct) and reclassifying 3 manual findings as volatile-data violations (accurate at run time, stale at review time). The delete near-miss was surfaced from the click log. The skill ran under Claude Code on Claude Sonnet 5.*