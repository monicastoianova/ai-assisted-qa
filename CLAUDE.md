# CLAUDE.md

Project configuration for the AI-assisted QA portfolio. This file is read at the start of every session and is the single source of truth for shared config — no skill or agent hardcodes the Jira instance, project key, or field ID.

## Behavioral Guidelines

- State assumptions before implementing. Ask when uncertain.
- Write minimum code that solves the problem — no speculative features.
- Touch only what was asked. Match existing style.

## MCP Servers

Configured in `.mcp.json`:

| Server | Purpose |
|--------|---------|
| `playwright` | Browser automation |
| `atlassian` | Jira read/write |
| `jira-qmetry-mcp` | QMetry test management |

## Skills

Skills live in `.claude/skills/`. Invoke with `/skill-name` or natural language.

**Rule:** Every new skill file added to `.claude/skills/` must have a corresponding entry in this section before the work is considered done. The entry must include the skill name, a description, and a `**Trigger:**` line.

### `hrm-module-explorer`
Explores an OrangeHRM module via Playwright MCP and writes business documentation to `docs/{module}.md`.
**Trigger:** "help me understand the X module", "what does the X module do", "explain X in OrangeHRM", "onboard me on X".

### `test-planner`
Fetches a Jira user story, classifies derived test cases as API or UI, and writes a test plan to `validation/test-planner/{KEY}-test-plan.md`.
**Trigger:** user provides a Jira story key or URL and asks for a test plan or test coverage.

## Agents

Agents live in `.claude/agents/`. Some are user-invoked directly (e.g. bug-reporter); others are spawned automatically by another skill or agent (e.g. test-updater, new-test-creator).

**Rule:** Every new agent file added to `.claude/agents/` must have a corresponding entry in this section before the work is considered done. The entry must include the agent name, a description, and a `**Trigger:**` line.

### `test-updater`
Spawned by test-planner after a test plan is written. Searches QMetry for test cases matching the plan titles, compares against the test plan (Type, Linked AC, preconditions, steps, expected results, test data), and outputs MATCH / UPDATE / DELEGATE decision table. Never creates test cases.
**Trigger:** spawned programmatically by the test-planner skill with a test plan file path.

### `new-test-creator`
Generates structured test cases, previews them as cards, and publishes to QMetry after explicit user confirmation.
**Trigger:** spawned by test-updater agent with DELEGATE rows.

### `bug-reporter`
Reproduces a described OrangeHRM defect via Playwright MCP, generates a structured bug report, and creates a Jira Bug issue.
**Trigger:** user describes a bug they observed ("when I do X, Y happens instead of Z", "this feature is broken", etc.) and wants a report and Jira issue.

## OrangeHRM Demo

- **URL:** `https://opensource-demo.orangehrmlive.com`
- **Credentials:** `Admin` / `admin123`

## Explored OrangeHRM Modules

| Module | Doc |
|--------|-----|
| My Info | `docs/my-info.md` |
| Admin | `docs/admin.md` |

## Project Configuration

- QMetry Project ID: {your-project-id}
- QMetry/Jira Project Key: {your-project-key}
- Jira Instance: {your-instance}.atlassian.net
- Jira Sprint Field ID: {your-sprint-field-id}
- OrangeHRM Demo: https://opensource-demo.orangehrmlive.com
- Skills Path: .claude/skills
- Agents Path: .claude/agents
- Docs Path: docs

## Skill/Agent Structure Convention

All skills and agents follow this structure:
- Frontmatter: `name`, `description` (with Trigger). Agents also specify `model: claude-sonnet-5` unless justified otherwise; skills do not.
- `## Step 0 — Load Context` (if external config/assets are needed)
- `## Step N — <Verb Phrase>` for each sequential step
- `## Constraints` — only for safety-critical or easy-to-violate rules that benefit from a second, end-of-file reinforcement (e.g. destructive-action bans, publish-confirmation gates). A short numbered list restating the rule plainly. Most rules belong inline at their step and need no Constraints entry.
- No XML tags. No "Phase" numbering. Numbered lists use `1. ` with a space.
