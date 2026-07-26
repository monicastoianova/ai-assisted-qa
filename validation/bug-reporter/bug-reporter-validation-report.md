# Bug-Reporter Agent Validation Report

**Agent under test:** bug-reporter

**Target:** OrangeHRM demo (Admin and My Info modules)

**Method:** 4 scenarios, each targeting one distinct decision branch — one real, firsthand-confirmed bug; one deliberately false claim; one genuinely open-outcome case with no predetermined answer; one deliberately underspecified input. Expected behavior defined before each run, scored against the actual result and the raw session logs.

**Date:** 2026-07-22

---

## 1. Scenarios and Expected Behavior

| # | Prompt | Target Branch | Expected Behavior |
|---|---|---|---|
| 1 | "Something's broken on the admin page." | Step 1 — missing-detail stop | Stop before any reproduction; ask for the missing page/action detail |
| 2 | "On the General Information page, when I enable the Edit toggle, change the Organization Name without clicking Save, then disable the Edit toggle, the changed Organization Name is still visible in the field until refresh." | Steps 2–6 — full success path | Reproduce, HIGH confidence, correct priority, Jira issue created in the right sprint |
| 3 | "On the My Info module, Personal Details tab, when I clear the Date of Birth field and click Save, it still saves successfully." | Step 3 — open-outcome confidence test | No predetermined answer — tests reasoning quality, not a fixed result |
| 4 | "On the Admin > General Information page, if I clear the Organization Name field and click Save, it saves anyway with an empty name." | Step 2 — reproduction-failure stop | Stop after reproduction fails; behavior was not observed; no report, no Jira issue |

## 2. Results

| # | Result | Notes |
|---|---|---|
| 1 | **Pass — after two failed attempts and a fix** | **First attempt**: the orchestrator layer answered the request itself due to the vague description, without ever invoking the agent. **Second attempt**: prompt used was "On the General Information page, the Country field doesn't work." Verified via log: the orchestrator's handoff to bug-reporter injected two false claims — asserted, as fact, that "General Information" doesn't exist, and that Country is on Personal Details under "Nationality." The agent found the real Country field on Contact Details and tested that instead — testing the wrong page, since "General Information" (Admin > Organization) does exist. Root-caused via raw tool-call logs, not inferred from chat output. Fixed by adding pass-through phrasing to the invocation ("user's exact words, no added context"). **Third attempt**: prompt used was "Use the bug-reporter agent. User's exact words, no added context: 'On the General Information page, the Country field doesn't work.'" Verified via log: the raw system content matched the prompt exactly, no orchestrator interpretation this time. bug-reporter independently self-verified via `docs/` — found `docs/admin.md` line 58 confirming the real page and its Country field, the exact thing the earlier injection got wrong. Zero Playwright tool calls; stopped entirely at the text-research stage before touching the browser. Step 1's missing-detail stop fired correctly, asking: "What specifically happens with the Country field that makes it 'not work'?" |
| 2 | **Pass — required a foreground re-run** | **First attempt** (background) deadlocked: the sub-agent asked its Step 5 confirmation questions, the task then ended and had to be resumed with the answer relayed through the orchestrator — the sub-agent correctly refused to trust a relayed confirmation it couldn't verify, and the run had to be aborted with no Jira issue filed. **Second attempt** (foreground): prompt used was "Use the bug-reporter agent in the foreground (not background) for: On the General Information page, when I enable the Edit toggle, change the Organization Name without clicking Save, then disable the Edit toggle, the changed Organization Name is still visible in the field until refresh." Completed end-to-end: OQ-13 created, correct sprint, correct priority. Screenshots: `validation/bug-reporter/admin-orgname-edit-toggle-no-revert/`. Two caveats found via log review: Step 6 used an undocumented fallback (JQL search) after the documented board-lookup path didn't resolve; Step 4's full report template was never printed for review, only a prose summary. |
| 3 | **Pass** | Correctly resolved HIGH confidence + Expected=Actual: confirmed DOB unmarked as required in both the live UI and `docs/my-info.md`, verified the save persisted after a page reload (not just no error shown), and correctly declined to file — with an explicit offer to reopen as a defect if given a requirement stating DOB should be mandatory.<br>*\* See F1 — the invoking prompt for this run also contained an injected assumption, correctly overridden by the agent.* |
| 4 | **Pass** | Clearing Organization Name triggered live inline validation and blocked the save — the opposite of the claimed symptom. The agent didn't just report "couldn't reproduce" — it offered possible explanations (already fixed, different browser/viewport, a different field, or a different path to Save) and asked to confirm the exact steps or try a variation. No report or issue was created, "per the workflow's rule against filing for unconfirmed symptoms." |

## 3. Findings

| # | Severity | Finding | Recommendation |
|---|---|---|---|
| F1 | **High** | Orchestrator handoff drift, verified via raw logs across all 4 prompts, varying in severity: **Prompt 1 (second attempt)** — a fabricated fact/hallucination about application structure, asserting "General Information" doesn't exist and giving a wrong field location, causing bug-reporter to test the wrong page. **Prompt 3** — a a subtler case: the framing asserted DOB was "expected to be a required field," assuming the answer to the exact open question this scenario was designed to test neutrally; the agent independently verified against the live UI and `docs/my-info.md` and correctly overrode this bias, but the assumption itself was not neutral. **Prompt 4** — neutral elaboration only, the claim was restated with more detail but no added assumption. **Prompt 2 (both attempts)** — also contained orchestrator elaboration, but phrased as hedged uncertainty ("likely," "investigate the correct location," "if the exact path differs") rather than asserted fact, so no false claim was introduced and reproduction proceeded correctly regardless. **Prompt 1 (third attempt)** — explicit pass-through phrasing ("user's exact words, no added context") avoided the drift entirely; the only confirmed clean invocation. | Add a CLAUDE.md rule: "When spawning any sub-agent, pass only verified context — file paths, IDs, data confirmed from a tool call. Never add a guess, assumption, or unverified claim as if it were fact. If uncertain, say so explicitly and let the sub-agent verify it." |
| F2 | **High** | Background-task confirmation deadlock: Step 5's human-confirmation gate assumes live interaction. When run as a background task, an answer relayed after a task restart is correctly rejected by the sub-agent as unverifiable, and the run cannot proceed. Root cause not fully isolated — confirmed the task ended after Step 5's question, not after Steps 1/3's, but not confirmed why. | Split into two skills — `bug-tester` (reproduces, prints report, asks "is the report ready to log?") and `new-bug-creator` (asks sprint/backlog, creates the Jira issue), manually triggered by the user between the two rather than programmatically spawned. Since skills are always user-triggered and never run as background tasks, this removes the failure condition structurally rather than by convention. *\* Opened for further review and discussion.* |
| F3 | Medium | Step 4's "print the full report template before any Jira action" was not observed — a prose summary substituted for the structured report, meaning the Step 5 confirmation was given without the human actually seeing the templated output. | Make report printing a literal, checked prerequisite before Step 5's question is asked. |
| F4 | Medium | Step 6's documented board-lookup REST flow was not exercised in the one full run performed — the agent fell back to a JQL search after the board call didn't resolve. Sprint assignment was correct, but the primary documented mechanism remains unverified. | Re-run with the board REST path confirmed working, or simplify Step 6 to use JQL as the primary method since it's what worked. |

## 4. Design Fixes Applied Before This Run

- Added an explicit destructive-action confirmation gate (Step 2) before any delete/bulk/irreversible action
- Added a reproduction-failure stop condition — previously the agent had no defined behavior when the described symptom didn't occur
- Added an Expected-vs-Actual comparison gate (Step 3) — previously a confident Expected Result alone was enough to proceed, with no check against what was actually observed, which could have produced a bug report for correct, expected behavior
- Fixed Step 6 to resolve the Jira instance, project key, and sprint-field ID from CLAUDE.md instead of hardcoding them
- Added scripted, mandatory wording for every stop condition, closing a gap where Step 1's stop had no defined phrasing and could not be reliably distinguished from ordinary conversation

## 5. Conclusion

4 of 4 scenarios reached their intended outcome, but Prompts 1 and 2 each required multiple attempts and a fix before getting there — both exposed structural issues in how the agent is invoked and run, not prompt wording problems. Both are documented with root cause and recommendation (F1, F2). F1 also surfaced a graded pattern across all 4 prompts — from a false claim (Prompt 1) to a biased presupposition correctly overridden by the agent (Prompt 3) to hedged uncertainty (Prompt 2) to neutral elaboration (Prompt 4) — showing the injection risk is systemic, not a one-off. F3 and F4 are real spec deviations that produced a correct result without the documented mechanism actually being followed, flagged as open items for the next iteration.

---

*Division of labor: agent design, all fixes, and the four validation scenarios were built and reviewed jointly with Claude across multiple sessions. F1 was identified by me, catching a false claim in the agent's output against firsthand knowledge of the app. F2 was surfaced by the sub-agent's own output stating it couldn't proceed; the root cause and fix direction were investigated jointly. Root-cause tracing of the raw tool-call logs was performed by Claude at my direction. The pipeline ran under Claude Code on Claude Sonnet 5.*
