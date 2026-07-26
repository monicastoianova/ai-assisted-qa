# bug-reporter

Reproduces a described OrangeHRM defect via Playwright MCP and files a Jira Bug issue after explicit confirmation.

## What it does

1. Parses the user's description — stops and asks if the module/page or trigger action is missing.
2. Reproduces the described steps live via Playwright MCP.
3. Stops if the symptom doesn't actually occur — never files a report for something that didn't happen.
4. Assesses confidence in the Expected Result; stops and asks if it's genuinely unclear.
5. Compares Expected vs. Actual — stops if they match, since that means it isn't a bug.
6. Prints findings, confirms with the user, then creates the Jira issue (current sprint or backlog).

## Design principles

- **Never guess a defect into existence.** Three separate stop conditions exist specifically to prevent filing a bug that isn't real: reproduction failure, low confidence, and Expected=Actual.
- **Human confirms before any write.** The report is always shown and approved before Jira is touched.
- **Destructive actions require explicit sign-off**, separate from the report-confirmation gate.
- **Config lives in CLAUDE.md**, not hardcoded — Jira instance, project key, sprint field ID.

## Conventions

- **Environment string format:** `<Base URL> | <Device type> <WxH>` — fixed format every filed issue uses, derived from viewport at reproduction time.
- **Screenshot path:** `validation/bug-reporter/{distinctive-slug}/`, collision-safe via numeric suffix.
- **Scripted stop wording:** every stop condition (missing detail, reproduction failure, low confidence, Expected=Actual match) has exact required phrasing, not left to the model's improvisation.

## Prerequisites

- Claude Code with Playwright MCP and Atlassian (Jira) MCP configured
- `CLAUDE.md` populated with Jira Instance, Project Key, and Sprint Field ID
- Target app for test execution: OrangeHRM demo instance

## Validation

Validated with 4 scenarios: a missing-detail case, a real firsthand-confirmed bug, a genuinely open-outcome case, and a deliberately false claim. Full results, two structural findings (orchestrator handoff drift, a background-task confirmation deadlock), and recommendations: **bug-reporter validation report** (`validation/bug-reporter/bug-reporter-validation-report.md`)
