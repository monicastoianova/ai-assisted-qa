# AI-Assisted QA Portfolio

**Claude Code skills and agents covering application analysis, test planning, and defect reporting** — key stages of the Software Testing Life Cycle applied to OrangeHRM. Each component is validated against a specification I wrote and an expected outcome defined before running it — a hand-built reference set for the exploration skill, seeded test cases with predefined expected decisions for the planning pipeline, and for the bug-reporter, one firsthand-confirmed real bug plus three deliberately constructed test scenarios, each with an expected outcome defined in advance — and every claim is checked against raw session logs, not the agent's own chat summary.

**Run on:** Claude Code, Claude Sonnet 5

## Components

| Component | Type | What it does |
|---|---|---|
| hrm-module-explorer | Skill | Explores a live OrangeHRM module via Playwright MCP and writes business-level documentation |
| Test Planning Pipeline | Skill + 2 agents | Jira story → test plan → QMetry reconciliation → creates only genuinely missing test cases |
| bug-reporter | Agent | Reproduces a described defect via Playwright MCP and files a Jira Bug after explicit confirmation |

## How each was validated

- **hrm-module-explorer** — I manually clicked through the entire Admin module and recorded ground truth before running the skill. Three iterations, each scored claim-by-claim against that answer key and the session transcripts. v1 fabricated behavioral details it never verified — including one claim I proved false by testing it myself. Navigation coverage was 100% across all three runs; what changed was v3 reaching zero unsupported behavioral claims and zero destructive-action incidents.
- **Test Planning Pipeline** — I created a user story with 5 ACs and seeded QMetry with test cases each containing one deliberate trap: a mislabeled tag, a UI/API type mismatch, a content mismatch along with Type & Linked AC tags absence, and one requirement with no coverage at all. The pipeline's decisions were scored against a predicted outcome for every trap. 6 of 7 reproduced exactly as designed; one QMetry test case was created and verified manually.
- **bug-reporter** — 4 scenarios: a real bug I found and confirmed by hand, a deliberately false claim I knew was false, a genuinely open-outcome case, and an underspecified input. The agent filed exactly one Jira issue — for the real bug — and correctly refused to file anything for the rest.

## What the validation found

Beyond pass/fail, this process surfaced real problems in every component, each caught a different way — session transcript analysis, careful output comparison, and reading the agent's own explanation of why it refused to proceed:

- **hrm-module-explorer — a confirmed hallucination.** v1 stated as fact that "LDAP fields are only editable once enabled" — but the toggle was never clicked in that session (confirmed with session transcript). Manual verification against the live application proved that the LDAP fields are editable regardless of the Enable toggle state.
- **Test Planning Pipeline — a safety-language violation that didn't reach the mandated wording.** test-updater correctly detected a mislabeled tag, but described the fix as "correct the tag" instead of the required "flag for human review — verify before applying; may be mislabeled." The underlying judgment was right; the required phrasing wasn't reproduced, and logs confirmed no write occurred regardless.
- **bug-reporter — a background-task confirmation deadlock.** An agent asking for human confirmation from a background task cannot verify a relayed answer is genuine, and correctly refuses to proceed — a trust-boundary conflict between the execution model and the human-in-the-loop design.

Full findings, evidence, and recommendations are in each component's validation report under [`validation/`](validation/).

## Design principles

- **Human confirmation before every consequential write** — publishing a test case, filing a Jira issue. Proposed QMetry updates to existing test cases are never applied automatically — only shown to a human for manual action.
- **Destructive or irreversible actions always require explicit sign-off**, separate from the standard confirmation gate — a near-miss deletion during early skill validation is what made this rule necessary.
- **Never guess a defect or a document into existence** — every component has explicit stop conditions for missing input, failed reproduction, and low confidence.
- **Flag, don't fix** — suspected data conflicts are surfaced for human review, never silently auto-corrected.
- **Config in [`CLAUDE.md`](CLAUDE.md)**, not hardcoded — including a documented structure convention all skills and agents follow.

## Structure

```
agents/
  bug-reporter.md
  bug-reporter-README.md
  new-test-creator.md
  test-updater.md

assets/
  jira-priority-table.md

docs/
  admin.md
  my-info.md

skills/
  hrm-module-explorer/
    assets/
      explorer-rules.md
    README.md
    SKILL_v1.md
    SKILL_v2.md
    SKILL_v3.md
  test-planner/
    assets/
      test-classification-patterns.md
    README.md
    SKILL.md

validation/
  bug-reporter/
    admin-orgname-edit-toggle-no-revert/
      1-original-value.png
      2-after-edit-unsaved.png
      3-after-toggle-off-no-revert.png
    bug-reporter-validation-report.md
  hrm-module-explorer/
    admin-answer-key.md
    admin_v1.md
    admin_v2.md
    admin_v3.md
    hrm-module-explorer-validation-v1.md
    hrm-module-explorer-validation-v2.md
    hrm-module-explorer-validation-v3.md
    manual-AI-review-findings-v2.md
  test-planner/
    OQ-12-test-plan.md
    OQ-12-validation-report.md

CLAUDE.md
```
