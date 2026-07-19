---
name: test-planner
description: Fetches a Jira user story, reads the module documentation if available, derives functional test cases from its acceptance criteria, classifies each as API or UI, writes a test plan to specs/{ISSUE-KEY}-test-plan.md, and after user confirmation spawns the test-updater-agent to produce a QMetry update/creation plan — new test cases are published after a further review, existing test updates are proposed for manual application. Trigger when the user provides a Jira story key or URL and asks for a test plan, test coverage, or "generate tests for OQ-N".
---

# Test Planner

## Step 1 — Parse Input

Extract the Jira issue key from the user message (bare key `OQ-5` or URL
`https://{your-instance}.atlassian.net/browse/OQ-5`). If no key is found, ask for one.

## Step 2 — Fetch the Story

Call `mcp__atlassian__getJiraIssue` with the extracted key. Extract:
- `summary` — story title
- `description` — full body including acceptance criteria

If the call fails or issue is not found, report the error and stop.   
If the story has no acceptance criteria, or the criteria are too vague to derive testable cases (e.g. "as per discussion", "TBD"), stop and ask the user to provide or clarify them before proceeding.

## Step 3 — Extract module and read documents

1.Extract the module name from the story title if it follows the `[Module Name]` prefix convention (e.g. `[Leave module] Employee can cancel a pending leave request` → module: Leave).
- Derive `module-slug` by lowercasing the module name and stripping the generic word "module" if present (e.g. `Leave Module` → `leave`; `Admin` → `admin`; multi-word names replace spaces with hyphens, e.g. `My Info` → `my-info`).
- If a matching file exists at `docs/{module-slug}.md`, read it in full to understand the module's structure, fields, and behavior before deriving test cases. 
- If no prefix is found or no matching file exists, proceed without it and note the gap in the Assumptions section of the test plan.  

2.Read `.claude/skills/test-planner/assets/test-classification-patterns.md` in full.
- Apply its Decision Algorithm when classifying each test case in Step 4. 
- If the file cannot be read, report the error and stop.

## Step 4 — Derive and Classify Test Cases

From the description and acceptance criteria, derive the minimum set of test cases that
covers every stated requirement. Rules:

- Functional tests only (no performance, security, exploratory)
- Classify each as **API** or **UI** using the patterns from Step 3
- One test case per distinct requirement/AC — do not invent edge cases
- Assign sequential IDs: TC-01, TC-02, …

For each test case produce:

| Field | Content |
|---|---|
| ID | TC-0N |
| Title | Imperative sentence, ≤ 10 words |
| Type | API or UI |
| Linked AC | AC reference, formatted as `{ISSUE-KEY}-AC#` (e.g. `OQ-12-AC1`) |
| Preconditions | Numbered list or "None" |
| Steps | Numbered action steps; add Expected Result on steps that verify a behavior — navigation/setup steps don't need one |
| Test Data | Concrete values or "N/A" |

## Step 5 — Write the Test Plan

Output path: `specs/{ISSUE-KEY}-test-plan.md`
Create the folder if it does not exist.

Structure:

**1. Header** — story title, Jira link, generation date

**2. Coverage Decisions** — 1–2 sentences: what is covered and what is excluded

**3. Assumptions** — 1–2 sentences on any inferred or missing information

**4. Test Cases**

Summary table — one row per test case:

| # | Title | Type | Linked AC | What it covers |
|---|---|---|---|---|
| 1 | `<Title>` | API/UI | `{ISSUE-KEY}-AC#` | `<one sentence: what risk or behaviour is verified>` |

API section with full detail cards using the field structure defined in Step 4 (omit if empty)

UI section with full detail cards using the field structure defined in Step 4 (omit if empty)

**5. Requirement Traceability Matrix** — table mapping each AC to its test case IDs

> ⚠️ **Before proceeding:** Present the written test plan to the user and ask: "Is this test plan ready to send for QMetry review? Reply yes to continue or tell me what to adjust." Wait for explicit confirmation before proceeding to Step 6. If the user requests changes, update the plan file and re-present it.

## Step 6 — Spawn Agent

After the required confirmation from Step 5 is received, spawn the `test-updater-agent` and pass it:
- The absolute path to the written test plan file