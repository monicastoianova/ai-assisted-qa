# Admin module — answer key (manual clickthrough, 2026-07-12)

Convention: menu label = page heading unless noted otherwise; all headings verified.

Top bar items: User Management | Job | Organization | Qualifications | Nationalities | Corporate Branding | Configuration

## User Management (dropdown)
- Users — opens page headed "System Users" (URL:/admin/viewSystemUsers) — menu label ≠ page heading
    - Search/filter: Username | User Role (dropdown: Admin/ESS) | Employee Name | Status (dropdown: Enabled/Disabled)
    - Buttons: Reset, Search, + Add
    - Results table columns: Checkbox | Username (sort: Ascending/Descending) | User Role (sort: Ascending/Descending) | Employee Name (sort: Ascending/Descending) | Status (sort: Ascending/Descending)| Actions (Delete (trash icon) | Edit (pencil icon))
    - Edit(pencil icon) → opens "Edit User" form (URL:/admin/saveSystemUser/{id}):
        - Fields: User Role* (dropdown: Admin/ESS) | Employee Name* | Status* (dropdown: Enabled/Disabled) | Username*
        - Checkbox: "Change Password ?" → Yes — CLICKED: checking it reveals a password section:
            - Fields: Password* | Confirm Password*
            - Hint text below Password: "For a strong password, please use a hard to guess combination of text with upper and lower case characters, symbols and numbers"
            - NOT triggered: did not enter/save a password, did not test mismatch or weak password validation
        - Footnote: "* Required"
        - Buttons: Cancel, Save
    - Delete: NOT clicked — any agent claim about delete behavior = inference

## Job (dropdown) — Delete/Edit icons present on all tables, NOT clicked anywhere; claims about their behavior = inference
- Job Titles
    - Buttons: + Add
    - Table columns: Checkbox | Job Titles (sort: Ascending/Descending) | Job Description | Actions (Delete (trash icon) | Edit (pencil icon))
- Pay Grades
    - Buttons: + Add
    - Table columns: Checkbox | Name | Currency | Actions (Delete (trash icon) | Edit (pencil icon))
- Employment Status
    - Buttons: + Add
    - Table columns: Checkbox | Employment Status | Actions (Delete (trash icon) | Edit (pencil icon))
- Job Categories
    - Buttons: + Add
    - Table columns: Checkbox | Job Category | Actions (Delete (trash icon) | Edit (pencil icon))
- Work Shifts
    - Buttons: + Add
    - Table columns: Checkbox | Name | From | To | Hours Per Day | Actions (Delete (trash icon) | Edit (pencil icon))

## Organization (dropdown)
- General Information
    - Toggle: "Edit" — CLICKED (switched on): fields become editable; Number of Employees remains read-only; Save button visible
    - Fields: Organization Name* | Number of Employees (read-only) | Registration Number | Tax ID | Phone | Fax | Email | Address Street 1 | Address Street 2 | City | State/Province| Zip/Postal Code | Country (dropdown)| Notes
        - Country (dropdown): NOT clicked — any agent claim about its behavior = inference
    - Footnote: "* Required"
    - Buttons: Save
- Locations
    - Search: Name | City | Country (dropdown)
        - Country (dropdown): NOT clicked — any agent claim about its behavior = inference
    - Buttons: Reset, Search, + Add
    - Table columns: Checkbox | Name (sort: Ascending/Descending) | City (sort: Ascending/Descending) | Country (sort: Ascending/Descending) | Phone (sort: Ascending/Descending) | Number of Employees (sort: Ascending/Descending) | Actions (Delete (trash icon) | Edit (pencil icon))
        - Delete/Edit: NOT clicked — any agent claim about their behavior = inference
- Structure — opens page headed "Organization Structure" (URL:/admin/viewCompanyStructure) — menu label ≠ page heading
    - Toggle: "Edit"— CLICKED (switched on): fields become editable
    - Buttons: + Add
    - Organization Structure tree; Edit toggle enables Delete(trash icon)/Edit(pencil icon)/Add (+ icon) on nodes
        - Delete/Edit/Add: NOT clicked — any agent claim about their behavior = inference

## Qualifications (dropdown) — Delete/Edit icons present on all tables, NOT clicked anywhere; claims about their behavior = inference
- Skills
    - Buttons: + Add
    - Table columns: Checkbox | Name | Description | Actions (Delete (trash icon) | Edit (pencil icon))
- Education
    - Buttons: + Add
    - Table columns: Checkbox | Level | Actions (Delete (trash icon) | Edit (pencil icon))
- Licenses
    - Buttons: + Add
    - Table columns: Checkbox | Name | Actions (Delete (trash icon) | Edit (pencil icon))
- Languages
    - Buttons: + Add
    - Table columns: Checkbox | Name | Actions (Delete (trash icon) | Edit (pencil icon))
- Memberships
    - Buttons: + Add
    - Table columns: Checkbox | Membership | Actions (Delete (trash icon) | Edit (pencil icon))

## Nationalities (direct page — no dropdown)
- Buttons: + Add
- + Add → opens "Add Nationality" form (URL:/admin/saveNationality):
    - Fields: Name*
    - Footnote: "* Required"
    - Buttons: Cancel, Save
    - NOT triggered: did not save (empty, duplicate, or valid)
- Table: Checkbox | Nationality | Actions (Delete (trash icon) | Edit (pencil icon))
    - Delete/Edit: NOT clicked — any agent claim about their behavior = inference

## Corporate Branding (direct page — no dropdown)
- Color pickers: Primary Color* | Primary Font Color* | Primary Gradient Color 1* | Secondary Color* | Secondary Font Color* | Primary Gradient Color 2*
    - Color pickers: NOT clicked — any agent claim about picker behavior = inference
- File uploads (Browse + "No file selected"):
    - Client Logo — hint: "Accepts jpg, .png, .gif, .svg up to 1MB. Recommended dimensions: 50px X 50px"
    - Client Banner — hint: "Accepts jpg, .png, .gif, .svg up to 1MB. Recommended dimensions: 182px X 50px"
    - Login Banner — hint: "Accepts jpg, .png, .gif, .svg up to 1MB. Recommended dimensions: 340px X 65px"
    - NOT triggered: no file uploaded — claims about upload validation = inference
- Toggle: Social Media Images (on by default)
    - NOT clicked — claim about its effect = inference
- Footnote: "* Required"
- Buttons: Reset to Default, Preview, Publish
    - NOT clicked — any agent claim about their behavior = inference

## Configuration (dropdown)
- Email Configuration
    - Fields: Mail Sent As* | Sending Method (radio: SECURE SMTP / SMTP / Sendmail — Sendmail selected) | Path to Sendmail (text)
      — NOT triggered: sending method not switched — claims about method-dependent field changes = inference.
    - Toggle: Send Test Mail
      — NOT clicked — claim about its effect = inference
    - Footnote: "* Required"
    - Buttons: Reset, Save
      — Reset/Save: NOT clicked — claim about their effect = inference
- Email Subscriptions
    - Table: Notification Type | Subscribers | Actions (add-subscriber icon | toggle)
    - Rows: Leave Applications | Leave Approvals | Leave Assignments | Leave Cancellations | Leave Rejections
    - Icons/toggles: NOT clicked — claims about their behavior = inference
- Localization
    - Fields: Language (dropdown) | Date Format (dropdown)
    - Buttons: Save
    - Dropdowns: NOT clicked (visible values only) — claims about available options = inference
- Language Packages
    - Buttons: + Add
    - Table: Checkbox | Language Packages (sort: Ascending/Descending) | Actions (Upload (up-arrow icon) | Translate (translate icon) | Download (down-arrow icon) | Delete (trash icon))
    - Icons: NOT clicked — claims about their behavior = inference
- Modules — opens page headed "Module Configuration" (URL:/admin/viewModules) — menu label ≠ page heading
    - Toggles: Admin Module (disabled) | Pim Module (disabled) | Leave Module | Time Module | Recruitment Module | Performance Module | Directory Module | Maintenance Module | Mobile | Claim Module | Buzz
    - Toggles: NOT clicked — claims about toggle effects = inference
    - Buttons: Save
- Social Media Authentication — opens page headed "Provider List" (URL:/admin/openIdProvider) — menu label ≠ page heading
    - Buttons: + Add
    - Table: Checkbox | Name | Actions
        - Table empty at time of viewing — Actions icons not observable
- Register OAuth Client — opens page headed "OAuth Client List" (URL:/admin/registerOAuthClient) — menu label ≠ page heading
    - Buttons: + Add
    - Table: Checkbox | Name | Redirect URI | Status | Actions (Delete (trash icon (disabled)) | Edit (pencil icon(disabled)))
- LDAP Configuration
    - Toggle: Enable — NOT clicked
    - Sections: Server Settings | Bind Settings | User Lookup Settings | Data Mapping | Additional Settings
        - Server Settings: Host* (hint: LDAP Server IP or Hostname without the protocol (without ldap:// or ldaps://)) | Port* (hint:If SSL use port 636 by default) | Encryption (dropdown: TLS/SSL) | LDAP Implementation (dropdown: Open LDAP v3/ MS Active Directory)
        - Bind Settings: Bind Anonymously (toggle - NOT clicked) | Distinguished Name* | Password*
        - User Lookup Settings: Base Distinguished Name* | Search Scope (dropdown: Subtree/One level; hint: Subtree option will allow searching base directory and sub directories. One level will only search within the base directory) | User Name Attribute* (hint: Attribute field to use when loading the username. Ex: cn, SMA account name) | User Search Filter* (hint: Attribute field to use when searching user objects. Ex: objectClass=person) | User Unique ID Attribute (hint: Attribute field to use as a unique immutable identifier for user objects. This is used to track username changes. Ex: entryUUID, objectGUID)
    - Data Mapping: Field in OrangeHRM (fields: First Name*, Middle Name, Last Name*, User Status, Work Email, Employee Id) | Field in LDAP Directory | Use this field as the employee / user mapping field (toggle: Work Email/Employee Id (NOT clicked))
    - Additional Settings: Merge LDAP Users With Existing System Users (toggle: NOT clicked) | Sync Interval (in Hours)*
    - Warning: Before activating the LDAP service, make sure that all LDAP settings are functioning properly since incorrect configuration may result in corrupted data. As a precaution, we recommend you to create a backup of your database before continuing.
    - Buttons: Test Connection, Save
    - NOT triggered: nothing entered/saved/tested — claims about validation or connection behavior = inference

## Behaviors deliberately NOT triggered anywhere:
- no form submissions, no deletes, no saves
- any agent claim about post-save/post-delete behavior = INFERENCE