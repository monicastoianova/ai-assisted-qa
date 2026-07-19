# Pipeline Validation Report — OQ-12 Run

**Pipeline:** test-planner (skill) → test-updater-agent → new-test-creator (agent)

**Target:** OQ-12 — [Admin module] Admin can edit General Information (5 ACs)

**Method:** Seeded-fault test data — 6 QMetry test cases pre-crafted to trigger one specific pipeline behavior each, plus one AC with no coverage at all. Every agent decision was scored against a predefined expected outcome, then verified manually in QMetry and against the raw agent logs.

**Date:** 2026-07-17

---

## 1. Seeded Test Data (QMetry) and Expected Behaviors

| QMetry Case | Seeded Fault | Expected Pipeline Behavior |
|---|---|---|
| OQ-TC-18 | None — pre-seeded as full coverage of AC1 | Full/partial coverage → MATCH, or UPDATE closing the exact gap |
| OQ-TC-19 | Linked AC deliberately mislabeled (AC1, content matches AC2) | Detect conflict, flag for human review, never auto-correct |
| OQ-TC-20 | Missing pre-toggle Save-absent check | Partial coverage → UPDATE closing the exact gap |
| OQ-TC-21 | Type & Linked AC tags empty; title overlaps the AC4 scenario but steps test special characters, not empty value | Full content review despite missing tags; rejected on content, not tags |
| OQ-TC-22 | Checks only the required-field asterisk, not save-blocking | Partial coverage → UPDATE closing the exact gap |
| OQ-TC-23 | Type: API vs plan's UI (correct Linked AC) | Hard-stop Type mismatch; no further comparison; not modified |
| — (AC5) | No QMetry case exists | Clean DELEGATE → new case created after preview & confirmation |

## 2. Results per Stage

### Stage 1 — test-planner

| Check | Result |
|---|---|
| Test case derivation | ✅ 5 UI cases, one per AC, no invented edge cases |
| Linked AC format | ✅ All tagged `OQ-12-AC#` |
| API/UI classification | ✅ All UI — correct, no API signals in story |
| Traceability matrix | ✅ 1:1 AC-to-case mapping |
| Module doc grounding | ✅ `docs/admin.md` read; field list reflected in steps and Assumptions |
| Confirmation gate | ✅ Plan presented, explicit approval required before spawning updater |

**Verdict: Pass.**

### Stage 2 — test-updater-agent

| Plan TC | Decision | QMetry ID | Expected | Verdict |
|---|---|---|---|---|
| TC-01 | UPDATE | OQ-TC-18 | Full/partial coverage | ✅ Pass — resolved as partial: substantive assertions added (Edit toggle off, Save absent) |
| TC-02 | UPDATE | OQ-TC-19 | Conflict flagged, no auto-correct | ⚠️ Finding F1 |
| TC-03 | UPDATE | OQ-TC-20 | Partial, close gap | ✅ Pass* — gap identified exactly (bidirectional Save visibility) |
| TC-04 | UPDATE | OQ-TC-22 | Partial, close gap; decoys rejected | ✅ Pass* — see below |
| TC-05 | DELEGATE | — | Only DELEGATE row | ✅ Pass — 5 keyword combinations documented |

\* Precondition comparison accuracy — see Finding F2.

**TC-04 decoy handling (log-verified):** the "Organization Name" search returned all three AC4 candidates. Logs confirm OQ-TC-21 was fully fetched (steps + version details) and rejected on content grounds — special-character vs empty-value validation — not short-circuited on its missing tags. OQ-TC-23 was correctly identified as Type: API and excluded without modification. One UPDATE row was produced, with both rejected candidates named in the Reason column and explicitly marked "leave as-is" / "do not touch." No duplicate creation was triggered. This was the core trap of the run and it worked as designed.

**TC-01 note:** The plan's test cases are more detailed than the seeded QMetry cases, so TC-01 resolved to a genuine partial-coverage UPDATE (missing Edit-toggle-off and Save-absent assertions) rather than MATCH. Correct decision against the actual data — but it means the MATCH decision value remains unexercised in this run.

### Stage 3 — new-test-creator
Spawned for the single DELEGATE row only. Card content traced from the plan file (source-of-truth rule held — no re-derivation from ACs). Folder resolved (/Admin), priorities fetched from QMetry, priority asked before publish confirmation, explicit "yes" required before write. Published OQ-TC-24 with Description `Linked AC: OQ-12-AC5 | Type: UI` — exact spec format. Manually verified in QMetry: folder, priority, description, and all 3 steps (with test data and expected result) match the confirmed preview. Offer to auto-apply the 4 UPDATE rows was declined — see Finding F4. **Pass.**

## 3. Findings

| # | Severity | Finding | Evidence | Disposition |
|---|---|---|---|---|
| F1 | Medium | **Linked AC conflict wording violates the no-auto-correct rule.** Row 2's Reason says "correct the tag" — an instruction to fix — instead of the mandated flag: "…verify before applying; may be mislabeled." Logs confirm no QMetry write occurred (UPDATE rows are manual-apply), so impact is phrasing-only, but the human-review safeguard language was not reproduced. | Update Plan table row 2; agent log | Fix: make the flag phrase mandatory and verbatim in Step 3 — the agent must output exactly "Linked AC tag conflicts with a strong step-level match — verify before applying; may be mislabeled," not a paraphrase like "correct the tag." This removes the option to describe the conflict in the agent's own words, which is what let "correct the tag" slip through. |
| F2 | Medium | **Precondition comparison over-reported "matches," missing real start-state gaps.** Rows 3–4 state "precondition matches," but the plan requires a starting toggle state (Edit off for TC-03, Edit on for TC-04) that the QMetry preconditions don't establish — OQ-TC-20 and OQ-TC-22 both have no toggle state in the preconditions. The Reason column calls precondition "matches" for both, but the QMetry preconditions have no toggle state recorded, so no such match could have existed to check. | Manual QMetry review vs plan file | Fix: change Step 3's comparison rule so the agent quotes the actual precondition text it read (or "no precondition set") before judging match/gap — e.g. instead of "precondition matches," the Reason would read "plan requires Edit toggle enabled; QMetry precondition has no toggle state — gap." This forces the comparison to happen against real text rather than a general impression. |
| F3 | Low | **No link to the created test case.** `create-qmetry-test-case` returns only `{id, key, versionNo, summary}` — no URL — so the agent could not surface one. Tooling gap, not a prompt defect. | Agent log, MCP response | Workaround: construct URL from instance base + project key + case key in Step 5, or accept manual lookup |
| F4 | Low | **Scope drift in the post-table menu.** The orchestrator offered "Apply the 4 UPDATE proposals to QMetry" — but the design specifies UPDATE rows are proposed for manual application only. Declined; no writes occurred. | Run transcript | Fix: constrain post-table options to DELEGATE handoff + report only |

## 4. Design Behaviors Verified in This Run

- Type mismatch = hard stop, no partial-match attempt (OQ-TC-23, log-verified)
- Missing Type/Linked AC tags ≠ rejection; full content review performed (OQ-TC-21, log-verified)
- Multi-candidate fan-out resolved to a single UPDATE without duplicate test creation (TC-04)
- Update-first policy held: 4 updates proposed, only 1 genuinely new case created
- Both human gates enforced: plan confirmation before updater spawn; priority + explicit "yes" before QMetry write
- Source-of-truth chain held: created case content identical to plan file TC-05, not re-derived
- `{ISSUE-KEY}-AC#` Linked AC convention applied end-to-end, including the published case's Description field

## 5. Pre-Run Design Review

All three skill/agent definitions were rewritten and critically reviewed line-by-line before execution — restructured from mixed XML/Phase formats to a consistent Step-based convention, with every cross-file field reference, tool call, and handoff contract checked for consistency. This review also surfaced the need for a documented Skill/Agent Structure Convention, since added to CLAUDE.md, to prevent this class of format drift on future skills/agents.

### Gaps Found and Fixed Before Execution

Documenting these is itself part of the report: they demonstrate why pre-execution design review matters as much as post-execution scoring — several of these gaps would have caused silent failures or wrong QMetry writes if they'd reached a live run undetected.

| # | Gap Found | Fix Applied |
|---|---|---|
| 1 | Field naming inconsistent across files (`Type` vs `Classification`) | Standardized to `Type` everywhere; matches QMetry convention |
| 2 | Linked AC derived by test-planner but never compared by test-updater | Added Linked AC as a comparison dimension, including a specific "conflict with strong match" flag for cases where the tag differs but the content otherwise aligns |
| 3 | test-updater's Step 3 never fetched QMetry's description/precondition fields — the exact fields carrying Type/Linked AC | Added explicit `get-test-case-version-details` calls with `fields: "precondition,description"` |
| 4 | new-test-creator re-derived test case content from acceptance criteria independently of test-planner's output — risk of drift between the two agents' interpretations of the same requirement | Redesigned so new-test-creator pulls the test-planner test case directly from the shared test plan file; if the referenced test case can't be found, it stops and asks rather than silently falling back to AC-derivation |
| 5 | test-updater never explicitly spawned new-test-creator with required arguments | Added explicit Step 6 spawn instruction (file path + Jira key) |
| 6 | Missing stop conditions (vague/absent AC, unreadable pattern file, unresolvable QMetry folder) | Added explicit stop-and-ask conditions at each dependency point |
| 7 | Step template rendering broken in Step 5 (table nested inside a numbered list swallowed content) | Restructured as standalone tables outside list nesting |

### Fixes Applied Immediately Before This Run

- Added `MATCH` as a third decision value (Step 3 + Step 5 table) to separate "no changes needed" from UPDATE
- Aligned the DELEGATE token between test-updater output and new-test-creator's row filter

## 6. Conclusion

**Result:** 6 of 7 seeded behaviors reproduced exactly as designed.

- **1 partial pass (F1):** OQ-TC-19's conflict was correctly detected and flagged, but the Reason wording didn't match the required "flag for human review, never auto-correct" language.
- **1 gap caught only by manual review (F2):** the precondition-match claim on rows 3–4 didn't hold up against the actual QMetry data. No incorrect data reached QMetry regardless — UPDATE rows are manual-apply by design, so this is exactly the class of error the human-in-the-loop step exists to catch.
- **Next iteration:** apply the F1/F2 prompt fixes, then re-run against re-seeded data that includes a true MATCH case.

---

*Division of labor: pipeline design, all fixes, and the QMetry trap data were built and reviewed jointly by me and Claude across multiple sessions before this run. The manual precondition-accuracy finding was identified by me through direct comparison against live QMetry data during the test plan review. Transcript recovery and tool-call verification were performed by Claude (Sonnet 5) against the recovered subagent log. The pipeline ran under Claude Code on Claude Sonnet 5.*
