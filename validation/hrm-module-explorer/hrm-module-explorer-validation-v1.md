# Skill Validation — hrm-module-explorer

**Skill under test:** `hrm-module-explorer` (v1)

**Test date:** 2026-07-12

**Target:** OrangeHRM demo — Admin module (module not previously explored by this skill)

**Method:** Blind run ("Onboard me on the Admin module", no hints) scored against a manually built answer key (`admin-answer-key.md`), created by hand *before* the run. Behavioral claims verified against the session transcript (tool-call log) and, where needed, against the live application.

## What was tested

The skill's own design guarantees, as stated in its SKILL.md definition:

1. **Coverage** — "Have I visited every submenu item?" (Step 5)
2. **Observed vs. inferred** — "Is each description based on observed UI, not inference?" (Step 5)
3. **Confidence gating** — below ~80% confidence: don't guess, flag in Open Questions (Step 5)

## Results summary

| # | Check | Skill rule under test | Result |
|---|-------|-------------------|--------|
| 1 | Submenu coverage (7 top-bar items, 24 pages) | Self-reflection catches missed submenus | ✅ PASS — 100% coverage, incl. commonly-skipped Configuration pages |
| 2 | Low-confidence flagging | Flag instead of guess | ✅ PASS ×2 — Open Questions correctly flags unopened Add forms and unidentified Language Packages icons |
| 3 | Observed-only descriptions | No inference presented as fact | ❌ FAIL — ~6 behavioral claims made without corresponding interactions (see below) |
| 4 | Factual accuracy | No false claims | ❌ FAIL — 1 confirmed hallucination (LDAP, see below) |
| 5 | Data/structure separation | (no rule exists — design gap) | ❌ GAP — volatile demo data (record counts, org names) written into durable documentation |

*Rules are taken from the skill's Step 5 definition; verification relied on independent evidence (answer key, session transcript, live application), not the skill's self-assessment.*

## Confirmed hallucination (most serious finding)

**Claim in generated doc:** LDAP Configuration — "fields only editable once enabled."

**Evidence chain:**
1. Session transcript shows the skill's last interactions were navigation to the LDAP page (clicks #47–48); the Enable toggle was **never clicked**.
2. Manual verification against the live application: the LDAP fields are editable **regardless** of the Enable toggle state.
3. Conclusion: the skill observed a static page with an off-state toggle, pattern-matched the *expected* behavior of such UIs, and wrote the expectation as observed fact — which turned out to be false.

This is the exact failure mode the observed-vs-inferred checkpoint exists to prevent, demonstrated end-to-end: no interaction → asserted behavior → assertion false.

## Inference presented as fact (unverified behavioral claims)

Transcript analysis: the skill performed **48 clicks, of which 47 were navigation** and 1 opened a form. It flipped no toggles, switched no radios, clicked no Edit/Delete icons. Nevertheless, the generated doc asserts:

| Claim | Why it's inference |
|-------|-------------------|
| "Sendmail path shown when selected" (Email Configuration) | Radio never switched; saw only one state |
| "Fields only editable once enabled" (LDAP) | Toggle never clicked — falsified (see above) |
| "Admin and PIM are always-on" (Modules) | Observed: disabled toggles. "Always-on" is an interpretation of why |
| "Number of Employees (system-calculated)" (General Info) | Observed: read-only field. Mechanism is a guess |
| "Controls which notifications are active and who is subscribed" (Email Subscriptions) | Icons/toggles never clicked |
| Relationships to Other Modules (entire section) | Never navigated to PIM or toggled a module; cross-module effects unobserved |

**Quantified finding:** ~6 dynamic-behavior claims vs. 1 interaction that could actually reveal behavior (the other 47 clicks were pure navigation). Behavioral claims outran performed interactions ~6:1.

## Design gaps (not bugs — missing rules)

1. **No data/structure separation.** The doc bakes in session-volatile demo data (4 locations, 21 skills, 194 nationalities, 9 language packages, "Kantor Pondok" org unit names, empty-table observations). The shared demo resets and is concurrently modified; these "facts" may be false within hours of generation.
2. **No upfront menu enumeration.** The skill asks "have I visited every submenu?" but contains no instruction to record the full menu tree *before* exploring — the checkpoint question has no list to check against. (Coverage passed this run, but by diligence, not by design.)
3. **Terminology fidelity.** Toggles/switches reported as "checkboxes" — verified via DevTools DOM inspection: OrangeHRM implements its switches as a hidden <input type="checkbox"> styled as a toggle. The skill sees only the accessibility role "checkbox", not the underlying HTML — so this was faithful reporting of the accessibility tree, not an error. v2 should describe elements as users see them.

## What worked well

- Complete coverage including easy-to-overlook pages (Corporate Branding, LDAP, OAuth) — the common skip-failure did not occur.
- The confidence gate fired correctly twice: unopened Add forms and unlabeled icons were flagged in Open Questions instead of guessed. Notably, where the skill *did* hedge (Language Packages icons: "translate/preview/download/delete-style… not individually labeled"), its guess was partially wrong (no "preview" icon exists; the four are Upload/Translate/Download/Delete) — demonstrating that the hedge was warranted and the mechanism has real value when it fires.
- Went one level deeper than required on System Users — opened the Add User form (verified in transcript, click #3) and correctly documented its Password/Confirm Password fields.

## Recommended fixes for v2

1. **Interaction-claim rule (fixes the core failure):** "Document what IS on the page. Describe what an element DOES only if you performed the interaction in this session. Otherwise write it as an assumption: '(assumed, not verified)' or move it to Open Questions."
2. **Data volatility rule:** "Record structure (menus, fields, buttons, labels), not data (record counts, record contents). The demo instance is shared and resets; data observations are valid only for the session."
3. **Enumerate-then-explore:** "Before visiting any page, open every dropdown and record the complete menu tree. The Step 5 coverage check compares visited pages against this list."

Each fix targets a finding documented above; after applying them, re-run the same blind test against the same answer key to measure the delta.

---

*Methodology & division of labor: I built and verified the answer key manually (full clickthrough before the run). The skill ran blind under Claude Code on Claude Sonnet 5. The claim-by-claim comparison of its output against my answer key was performed by Claude (Fable 5) in a separate session, using the session transcript's tool-call log as ground truth for what the skill actually did. I reviewed every finding and spot-verified claims against the live application — which is how the LDAP claim was falsified and several comparison errors were caught and corrected.*