# Skill Validation — hrm-module-explorer (v3, final run)

**Skill under test:** `hrm-module-explorer` (v3 — external rules file `explorer-rules.md`, Step 0 bootstrap, write-time rule reminder in Step 6, hardened no-destructive-actions rule, interpretation-labeled template)

**Test date:** 2026-07-14

**Target:** OrangeHRM demo — Admin module (same target and same answer key as v1/v2)

**Method:** Blind run ("Onboard me on the Admin module", no hints), scored against the same manually built answer key (`admin-answer-key.md`). Review for this run performed by Claude (Fable 5) only: output audited line-by-line against the answer key and the full session transcript (70 logged actions). Interpretive sections — Purpose, Business Context, "Who uses it", Relationships, and purpose statements inside "What it does" — excluded from scoring per the established scope.

## Results summary — v3 rules under test

| # | Rule under test | Result |
|---|----------------|--------|
| 1 | Step 0 — read rules file before anything | ✅ PASS — first action in the transcript is reading `explorer-rules.md`, before login |
| 2 | Step 4.0 — list menu tree before exploring | ✅ PASS — all five dropdowns opened upfront (transcript actions 9–14) before any page visit |
| 3 | Rule 1 — behavioral claims only if interacted; otherwise "(assumed, not verified)" | ⚠️ MOSTLY PASS — marker correctly used twice (Structure Edit toggle, Language Packages icons); one unmarked repeat inference remains (see below) |
| 4 | Rule 2 — structure only, never data | ✅ PASS — no record counts, no row contents, no field values, no defaults, no "e.g." data leaks anywhere in the output |
| 5 | Rule 3 — no destructive actions | ✅ PASS — zero destructive clicks; both Add forms exited via Cancel; zero confirmation dialogs triggered; the Publish button explicitly documented as "not activated in this session — a publish/write action" |
| 6 | Rule 4 — no data-state recording | ⚠️ BORDERLINE — two empty-table notes ("no records at time of exploration") record data state, though hedged and correctly escalated to Open Questions |
| 7 | Step 6 confirmation line | ✅ PASS — "Rules file read: yes \| Rules 1–2 applied per submodule section" output to chat |
| 8 | Template — legend + (i) markers | ⚠️ PARTIAL — legend and both notes present at top; (i) correctly on Purpose, Business Context, "Who uses it", Relationships; **systematically dropped from every "What it does" heading** |
| 9 | Coverage (carried from v1) | ✅ PASS — all 24 pages, third consecutive run |

*Final step of the validation process: human review against the live application confirmed one minor marker omission — Modules "cannot be turned off" is a verified correct behavioral claim missing the "(assumed, not verified)" marker.*

## Newly verified behaviors (previously assumed, now observed)

- **General Information Edit toggle** — the skill clicked the toggle on and off (transcript 34–35), then documented the observed effect: fields become editable, Country becomes a dropdown, Save button appears. In v1/v2 this exact claim was an unmarked assumption; in v3 it is verified observation — and the skill restored the toggle state afterwards.
- **Job Titles Add form** — opened (action 18) and documented with previously unrecorded fields (Job Specification file upload, Note textarea), exited via Cancel.
- **Social Media Authentication Add Provider form** — opened and documented, exited via Cancel.

## Remaining findings

1. **Repeat inference (3rd consecutive run):** Email Configuration — "selecting Sendmail reveals a 'Path to Sendmail' field." The transcript shows no radio interaction; the dependency between the selection and the field was never observed and is not marked "(assumed, not verified)". The identical claim appeared in v1 and v2. Rule 1 violation — the single persistent defect across all three runs.
2. **Template compliance:** "(i)" marker dropped from all "What it does" headings, though preserved everywhere else. Purpose statements inside those entries (e.g. "used to group job titles") are therefore unlabeled interpretation.
3. **Data-state notes:** Job Categories and Licenses documented as having no records "at time of exploration." Strictly, emptiness is data state (Rule 4); mitigation was correct — hedged wording plus Open Questions escalation for the unconfirmable Add forms. Note: a mid-session logout occurred (transcript 29–31, re-login) around these pages; whether the empty tables reflect real demo state or a session artifact cannot be determined from the log.
4. **Minor unverified function naming:** Email Subscriptions actions described as "subscribe/unsubscribe checkbox" — purpose assumed from context, not interaction.

## Cross-run comparison

| Metric | v1 | v2 | v3 |
|---|---|---|---|
| Coverage (24 pages) | 100% | 100% | 100% |
| Data violations (C2/Rule 2) | pervasive (flagged as design gap) | 12 findings | 0 (2 borderline data-state notes) |
| Unmarked behavioral inferences (C1/Rule 1) | ~6 | 11 | 1 repeat + 1 minor |
| "(assumed, not verified)" marker used | n/a (rule absent) | 0 | 2, correctly |
| Confirmed hallucination | 1 (LDAP editability) | 0 | 0 |
| Destructive-action incidents | n/a (rule absent) | 1 near-miss (delete icon) | 0 |
| Verified interactions beyond navigation | 1 (Add User form) | 2 forms + 1 delete near-miss | 2 forms + 1 toggle cycle, all clean |
| Rules/constraints enforcement at write time | n/a | stated but ignored | enforced (bootstrap + write-time reminder + confirmation line) |

## Verdict

v3 passes. The two dominant failure classes of prior runs — data recorded into durable documentation and behavioral claims without interactions — are eliminated or reduced to a single repeat instance. The externalized rules file with a Step 0 bootstrap and a write-time reminder demonstrably changed output behavior where an in-file Constraints block (v2) did not.

## Known limitations / future work

- The Sendmail-dependency inference survived three runs; a v4 could address it with an explicit rule example, since generalized rules did not catch this specific phrasing.
- "(i)" template markers on "What it does" need reinforcement (e.g. an explicit line in the Step 6 reminder).
- Empty-table handling could be defined explicitly (document column structure as "not observable — table empty", never as a data-state fact).
- Interpretive sections (Purpose, Business Context, "Who uses it", Relationships) remain unverifiable by design; they are labeled for the reader but were not fact-checked.

---

*Methodology & division of labor: same answer key as v1 (built manually before v1). This run was scored solely by Claude (Fable 5) against the answer key and the session transcript, per the agreed final-run plan; prior runs (v1, v2) used manual and joint review — see `hrm-module-explorer-validation-v1.md`, `hrm-module-explorer-validation-v2.md`, and `manual-review-findings-v2.md`. The skill ran under Claude Code on Claude Sonnet 5.*
