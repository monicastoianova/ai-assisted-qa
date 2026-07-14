# hrm-module-explorer

A Claude Code skill that autonomously explores any OrangeHRM module via Playwright and produces business-level documentation saved to `docs/{module}.md`.

**Type:** Skill (user-triggered)

**Model:** Claude Sonnet 5

**Position in pipeline:** Step 1 — Explore

---

## What it does

Given a prompt like "onboard me on the Admin module", the skill:

1. Reads its rules file (`assets/explorer-rules.md`) before doing anything else
2. Logs into the OrangeHRM demo
3. Opens every dropdown to record the complete menu tree before exploring
4. Visits every page in the module; may open Add forms to document their fields — always exiting via Cancel without saving (Edit/Delete forms are not activated per safety rules)
5. Runs a self-reflection checkpoint by re-opening the module navigation and comparing it live against the menu tree recorded — catching any missed pages or mid-session UI changes
6. Writes structured documentation following the template — with `(i)` markers on interpretive sections and "(assumed, not verified)" on unverified behavioral claims
7. Outputs a chat confirmation that the rules file was read and applied — making rule adherence verifiable without opening the session log

---

## Validation — 3 runs, same answer key

This skill was validated against the Admin module across three iterations. Before the first run, a manual answer key was built by clicking through all 24 Admin pages and recording exact labels, fields, and columns. Each run was scored claim-by-claim against this key using session transcripts as evidence of what the skill actually clicked.

### What was tested
The skill's own stated guarantees (Step 5 self-reflection):
- Full coverage of all submenus
- Observed-only descriptions (no inference stated as fact)
- Confidence gating — hedge below ~80%, don't guess

### Results across runs

| | v1 | v2 | v3 |
|---|---|---|---|
| Coverage (24 pages) | 100% | 100% | 100% |
| Data violations | Throughout | 12 | 0 |
| Unmarked behavioral inferences | ~6 | 11 | 1 (repeat) |
| Confirmed hallucination | 1 | 0 | 0 |
| Destructive-action incidents | — | 1 near-miss | 0 |
| Rules enforced at write time | No rule | Present, ignored | Enforced |

### Key findings

**v1:** Coverage was perfect, but the skill made ~6 behavioral claims with only 1 non-navigation interaction. One confirmed hallucination: LDAP "fields only editable once enabled" — the toggle was never clicked, and the claim was proven false by testing. Session transcript was the decisive evidence.

**v2:** Added constraints inside the skill file. Data violations reduced but not eliminated. Constraints were not enforced at write time. A delete icon was clicked on the shared demo — the confirmation dialog saved it, but relying on dialogs to back out is itself a violation. Three "wrong" findings were reclassified by transcript analysis as accurate observations of volatile demo state — proving why the data rule exists.

**v3:** Rules moved to an external file (`assets/explorer-rules.md`), read as the very first action before login, and re-applied at each write step. Data violations: 0. Destructive clicks: 0. The Edit toggle was clicked, observed, and restored — a v1/v2 inference became verified observation. One inference survived all three runs (Sendmail field dependency). Documented as a known limitation.

### One surviving inference
"Selecting Sendmail reveals a 'Path to Sendmail' field" — present in v1, v2, and v3. No radio interaction in any transcript. Documented in the v3 report as a known limitation; a future rule could add a specific example to catch this phrasing.

---

## Files

| File | Description |
|------|-------------|
| `SKILL_v1.md` | Original course version — baseline for validation |
| `SKILL_v2.md` | Added constraints section inside the skill |
| `SKILL_v3.md` | Current version — externalized rules, Step 0 bootstrap, write-time enforcement |
| `assets/explorer-rules.md` | Rules file read by the skill at runtime |

Full validation artifacts are in [`/validation/hrm-module-explorer/`](../../validation/hrm-module-explorer/).

---

## Human review note

The v3 output was reviewed by me against the live application after the AI scoring pass. One minor finding: Modules "cannot be turned off" was documented without the "(assumed, not verified)" marker — verified by clicking, claim is correct. Human review remains the final step of the validation process.
