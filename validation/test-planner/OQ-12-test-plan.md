# Test Plan — OQ-12

**Story:** [Admin module] Admin can edit General Information
**Generated:** 2026-07-17

## Coverage Decisions

Covers all 5 acceptance criteria: default read-only state, field editability behind the Edit toggle, conditional visibility of the Save button, required-field validation on Organization Name, and discard-on-toggle-off behavior. Excludes performance, security, and validation of fields other than Organization Name (no other field is called out as required in the AC).

## Assumptions

`docs/admin.md` was found and read; it confirms the General Information page's field list (Organization Name, Registration Number, Tax ID, Phone, Fax, Email, Address fields, Country, Notes) and that Country becomes a dropdown in edit mode. All ACs describe UI behavior only (toggle, field state, button visibility, inline validation) with no API/HTTP signals, so all 5 test cases classify as UI per the Decision Algorithm.

## Test Cases

| # | Title | Type | Linked AC | What it covers |
|---|---|---|---|---|
| 1 | Verify fields are read-only on page load | UI | OQ-12-AC1 | Confirms no field is editable and no Save button shows before Edit is enabled |
| 2 | Verify Edit toggle makes all fields editable | UI | OQ-12-AC2 | Confirms enabling Edit switches every field (including Country to a dropdown) into an editable state |
| 3 | Verify Save button appears only when Edit is enabled | UI | OQ-12-AC3 | Confirms Save button visibility is tied to the Edit toggle state, in both directions |
| 4 | Verify Organization Name cannot be saved empty | UI | OQ-12-AC4 | Confirms required-field validation blocks save when Organization Name is cleared |
| 5 | Verify unsaved changes are discarded on Edit toggle-off | UI | OQ-12-AC5 | Confirms toggling Edit off without saving reverts fields to their last saved values |

### UI

**TC-01 — Verify fields are read-only on page load**
- Type: UI
- Linked AC: OQ-12-AC1
- Preconditions:
  1. Logged in as Admin (`Admin` / `admin123`).
  2. Navigate to Admin > Organization > General Information.
- Steps:
  1. Load the General Information page.
  2. Verify Organization Name, Registration Number, Tax ID, Phone, Fax, Email, Address Street 1/2, City, State/Province, Zip/Postal Code, Country, and Notes are rendered as read-only text, the Edit toggle is off, and no Save button is visible.
     - Expected Result: All fields display as non-editable text; Edit toggle is off; Save button is absent.
- Test Data: N/A

**TC-02 — Verify Edit toggle makes all fields editable**
- Type: UI
- Linked AC: OQ-12-AC2
- Preconditions:
  1. Logged in as Admin.
  2. On the General Information page with Edit toggle off.
- Steps:
  1. Enable the Edit toggle.
     - Expected Result: All fields (Organization Name, Registration Number, Tax ID, Phone, Fax, Email, Address fields, Notes) become editable text inputs, and Country becomes a dropdown.
- Test Data: N/A

**TC-03 — Verify Save button appears only when Edit is enabled**
- Type: UI
- Linked AC: OQ-12-AC3
- Preconditions:
  1. Logged in as Admin.
  2. On the General Information page with Edit toggle off.
- Steps:
  1. Verify the Save button is not visible while Edit is off.
     - Expected Result: Save button is absent.
  2. Enable the Edit toggle.
     - Expected Result: Save button becomes visible.
  3. Disable the Edit toggle.
     - Expected Result: Save button disappears again.
- Test Data: N/A

**TC-04 — Verify Organization Name cannot be saved empty**
- Type: UI
- Linked AC: OQ-12-AC4
- Preconditions:
  1. Logged in as Admin.
  2. On the General Information page with Edit toggle enabled.
- Steps:
  1. Clear the Organization Name field.
  2. Click Save.
     - Expected Result: A validation error is shown for Organization Name; the form is not saved; the previously saved Organization Name remains unchanged.
- Test Data: Organization Name = "" (empty)

**TC-05 — Verify unsaved changes are discarded on Edit toggle-off**
- Type: UI
- Linked AC: OQ-12-AC5
- Preconditions:
  1. Logged in as Admin.
  2. On the General Information page with Edit toggle off; note the currently saved Organization Name value.
- Steps:
  1. Enable the Edit toggle.
  2. Change the Organization Name field to a new, unsaved value.
  3. Disable the Edit toggle without clicking Save.
     - Expected Result: Organization Name reverts to its last saved value; the unsaved change is discarded.
- Test Data: Temporary unsaved value = "Test Org Temp"

## Requirement Traceability Matrix

| AC | Test Case(s) |
|---|---|
| OQ-12-AC1 | TC-01 |
| OQ-12-AC2 | TC-02 |
| OQ-12-AC3 | TC-03 |
| OQ-12-AC4 | TC-04 |
| OQ-12-AC5 | TC-05 |

## QMetry Update Plan

| # | Test Case Title | Decision | QMetry ID (if MATCH/UPDATE) | Reason |
|---|---|---|---|---|
| 1 | Verify fields are read-only on page load | UPDATE | OQ-TC-18 "Verify General Information fields are read-only on page load" | Type (UI) & Linked AC (OQ-12-AC1) match, precondition matches. Steps only assert fields are read-only and Organization Name isn't clickable — add assertions that the Edit toggle is off and no Save button is visible. |
| 2 | Verify Edit toggle makes all fields editable | UPDATE | OQ-TC-19 "Verify Edit toggle enables field editing" | Linked AC tag is OQ-12-AC1 but this scenario maps to OQ-12-AC2 per the plan — correct the tag. Steps only exercise the Organization Name field; extend to cover all fields (Registration Number, Tax ID, Phone, Fax, Email, Address fields, Notes) and the Country field converting to a dropdown. |
| 3 | Verify Save button appears only when Edit is enabled | UPDATE | OQ-TC-20 "Verify Save button visibility on General Information page" | Type (UI) & Linked AC (OQ-12-AC3) match, precondition matches. Only verifies Save appears after enabling Edit — add steps verifying Save is absent before Edit is enabled and disappears again after disabling Edit (bidirectional coverage). |
| 4 | Verify Organization Name cannot be saved empty | UPDATE | OQ-TC-22 "Verify Organization Name field shows required indicator" | Type (UI) & Linked AC (OQ-12-AC4) match, precondition matches. Currently only checks the required-field asterisk — add steps to clear the field, click Save, verify a validation error blocks save and the previously saved value is retained; add test data Organization Name = "" (empty). Related OQ-TC-21 (special-character validation, different test data — leave as-is) and OQ-TC-23 (Type: API — Type mismatch vs. plan's UI, do not touch) are separate cases and out of scope for this update. |
| 5 | Verify unsaved changes are discarded on Edit toggle-off | DELEGATE | — | Searched "unsaved changes discard", "toggle", "revert", "unsaved", "cancel edit" (5 keyword combinations) — no existing QMetry test covers reverting an unsaved Organization Name change on Edit toggle-off. New UI test needed. See TC-05.
