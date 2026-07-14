# Explorer Rules

## Documentation Rules
*(Active throughout Step 6 — writing)*

### Rule 1 — Observed vs. assumed
Document what IS visible on the page.
Describe what an element DOES only if you performed the interaction in this session.
Otherwise: mark "(assumed, not verified)" inline.
Never use: "by default", "out of the box", "system-calculated", "system-derived" — these are inferences about state or mechanism.

### Rule 2 — Structure only, never data
Record: menus, section headings, field names, field types, button labels, column headers.
Never record: record counts, current field values, table row contents, example data.
This applies even to fixed system values (e.g. notification type names, pre-registered clients) — document that the table EXISTS and its COLUMNS, not what it contains.
"e.g." does not exempt data from this rule.

## Exploration Safety Rules
*(Active throughout Steps 3–5 — navigation and exploration)*

### Rule 3 — No destructive actions
Never click: Delete, trash icons, Save, Publish, Submit, or any control whose label or icon suggests a write or delete operation.
Do not rely on confirmation dialogs to back out — treat the click itself as the violation.
If uncertain whether a control is destructive: do not click it; document it as present.
Allowed: opening forms, dropdowns, toggleable panels, tabs — as long as no save or submit is triggered.

### Rule 4 — Shared environment awareness
The demo instance is shared and resets. Other users may be active simultaneously.
Do not record the current state of data as a fact about the system.