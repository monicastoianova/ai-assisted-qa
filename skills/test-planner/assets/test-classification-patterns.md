# Test Classification Patterns

## Classification Lookup Table
| # | Pattern in User Story | Example Fragment | → Classification |
|---|---|---|---|
| 1 | Verb is **"returns"**, **"responds with"**, **"exposes"** + data/field name | *"API returns error details when entitlement is negative"* | **API** |
| 2 | Explicit **HTTP method** (GET, POST, PUT, PATCH, DELETE) | *"a PUT request updates the employee's nationality"* | **API** |
| 3 | Explicit **endpoint path** (`/api/`, `/v2/`) | *"POST to /api/v2/leave/leave-entitlements"* | **API** |
| 4 | **HTTP status code** as expected outcome | *"system returns HTTP 422 with a structured error"* | **API** |
| 5 | **Security / enumeration / injection** testing of a backend endpoint | *"verify the password reset endpoint does not reveal valid usernames"* | **API** |
| 6 | **Contract / schema** validation | *"response body matches the agreed JSON schema"* | **API** |
| 7 | Verb is **"navigate"**, **"click"**, **"fill"**, **"select"**, **"submit"** a form or button | *"user clicks Submit and sees a success notification"* | **UI** |
| 8 | **Visual / display** outcome expected | *"dashboard loads all widgets"*, *"dropdown opens and closes correctly"* | **UI** |
| 9 | **Page / screen / tab** mentioned as context | *"on the Admin panel, all menu items are visible"* | **UI** |
| 10 | **Role-based UI access** or **permission** checked through the browser | *"ESS user sees only their own timesheet"* | **UI** |
| 11 | **Form validation** or **inline error message** | *"required field shows a validation error"* | **UI** |
| 12 | **Export / download** action triggered by user | *"KPI list is exported to CSV via the UI button"* | **UI** |


## Decision Algorithm
1. If the story explicitly says "via API" or "via UI" → classify accordingly and stop.
2. Check rows 1–6 (API signals) and rows 7–12 (UI signals) against the story text.
3. If API signals matched and no UI signals matched → classify as API.
4. If UI signals matched and no API signals matched → classify as UI.
5. If BOTH API and UI signals matched, classify as UI. (Rationale: when a story spans both layers, the UI is the user-facing verification surface and the more common default for this classifier — see step 6.)
6. If no signals matched at all → default to UI (the vast majority of functional tests are UI).