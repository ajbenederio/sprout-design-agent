# Company Profile — Extended Fields: UI/UX Behaviour Specification

**Feature:** Extended Company Profile — KYB, UBO, Authorized Signatory, Document Requirements (Company Profile — Payroll Module)
**Capability ID:** CAP-2026-015
**Scope:** Phase 1 — Unified Shared Company Profile Workspace
**Date:** 2026-05-18
**Document type:** Design narrative and field specification — companion to the Company Profile Unified Front-End PRD

---

## Why This Page Works This Way

The KYB, UBO, Authorized Signatory, and Document Requirements fields are not a new module or page. They are new sub-sections added to the existing company profile page that HR and Payroll Admins already use. This is a deliberate consolidation: KYB and employer identity belong together because they describe the same entity — the company — from complementary angles. Separating them into a dedicated compliance portal would require administrators to maintain two canonical descriptions of the same entity in two places, defeating the purpose of a unified profile.

The "Same as UBO" option on the Authorized Signatory sub-section copies UBO values into AS fields at the moment the checkbox is checked. It is not a live synchronisation link. After the copy, UBO and AS are independent records. If the UBO's details change later, the Authorized Signatory record must be updated deliberately — the system will not propagate changes automatically. This prevents silent data drift in compliance-sensitive records: a reviewer can trust that the AS record reflects a deliberate state, not an automatic cascade from an upstream change.

Phase 1 supports exactly one UBO record and one Authorized Signatory record per company. This is a known simplification — standard KYB practice can require capturing multiple UBOs (typically all individuals owning ≥25% of shares). Multi-UBO and multi-AS support are explicitly deferred. The seven Certificate of Registration upload fields are all visible to all company types; which fields apply depends on the company's legal entity structure, and administrators are expected to upload only what is relevant. Conditional show/hide by company type is not implemented in Phase 1.

---

## Behaviour States Reference

### Group 1 — Company Profile Page

| State | What it means | How the user gets here | What can happen next |
|---|---|---|---|
| Company Profile — Loaded | The page displays all sections with their current saved values. New sub-sections are visible. No unsaved changes exist. | Admin navigates to the company profile page | Admin edits any field or uploads/removes a file → Company Profile — Editing |
| Company Profile — Editing | The admin has made at least one change that has not been saved. Save and Cancel actions are active. | Admin modifies any field value, checks or unchecks "Same as UBO," or uploads or removes a file | Admin clicks Save → Company Profile — Saving; Admin clicks Cancel (with unsaved changes) → Discard Confirmation |
| Company Profile — Saving | A save is in progress. All field values are being validated. Save and Cancel are disabled. | Admin clicks Save from Editing or Validation Failed | All validations pass → Company Profile — Saved; One or more fields fail → Company Profile — Validation Failed |
| Company Profile — Saved | All field values are persisted. A success confirmation is shown. The page returns to the Loaded state. | All validations pass during a save attempt | Admin makes further changes → Company Profile — Editing; Admin navigates away → no further page actions |
| Company Profile — Validation Failed | One or more fields failed validation. Inline error messages appear under each failing field. No data is persisted. Unsaved changes across all sections are preserved. | A save attempt is made while one or more fields contain invalid values | Admin corrects errors and clicks Save again → Company Profile — Saving; Admin clicks Cancel → Discard Confirmation |
| Discard Confirmation | A confirmation prompt asks the admin to confirm discarding unsaved changes. | Admin clicks Cancel while in Editing or Validation Failed state | Admin confirms discard → Changes Discarded; Admin dismisses the prompt → Company Profile — Editing (all unsaved changes preserved) |
| Changes Discarded | All unsaved changes are abandoned. The form reverts to the last saved state. | Admin confirms discard in the Discard Confirmation prompt | No further actions — page returns to Company Profile — Loaded |

### Group 2 — File Upload Fields

*Applies to: UBO Gov't ID, AS Gov't ID (image; PNG/JPG/JPEG; max 5MB) and all 7 Certificate of Registration document fields (PDF/PNG/JPG/JPEG; max 10MB).*

| State | What it means | How the user gets here | What can happen next |
|---|---|---|---|
| Upload Field — Empty | No file is attached. An upload control is shown. | Default state for all upload fields; or after a previously uploaded file is removed | Admin selects a file → Upload Field — Uploading |
| Upload Field — Uploading | A file has been selected and is transferring. An in-progress indicator is shown. The admin cannot select another file until this upload completes or fails. | Admin selects a file from their device | Transfer succeeds → Upload Field — Uploaded; Format not accepted → Upload Field — Wrong Format Error; File too large → Upload Field — File Too Large Error |
| Upload Field — Uploaded | A file is attached. For Gov't ID: filename or thumbnail shown inline. For Document Requirements: filename and download link shown. Replace and Remove controls are available. | File upload completes successfully | Admin clicks Replace → Upload Field — Uploading (new file); Admin clicks Remove → Upload Field — Empty; Admin saves → file is persisted |
| Upload Field — Wrong Format Error | Upload rejected; format not accepted. Error message shown. Field returns to its prior state (Empty or Uploaded). | Admin selects a file in an unsupported format | Admin selects a supported format → Upload Field — Uploading; Admin takes no action → field stays in prior state |
| Upload Field — File Too Large Error | Upload rejected; file exceeds size limit. Error message shown. Field returns to its prior state. | Admin selects a file exceeding the size limit | Admin selects a smaller file → Upload Field — Uploading; Admin takes no action → field stays in prior state |

### Group 3 — Authorized Signatory "Same as UBO"

| State | What it means | How the user gets here | What can happen next |
|---|---|---|---|
| AS — Independent Edit | "Same as UBO" checkbox is unchecked. All AS fields are individually editable. Values show last independently saved AS data, or blank if none exists. | Default state on page load; or after the admin manually unchecks "Same as UBO"; or after the admin edits any AS field while "Same as UBO" was checked (auto-uncheck) | Admin fills AS fields → Company Profile — Editing; Admin checks "Same as UBO" → AS — Copied from UBO |
| AS — Copied from UBO | "Same as UBO" checkbox is checked. AS fields were populated with current on-screen UBO values at the moment the checkbox was checked. No AS field has been edited since the copy. | Admin checks the "Same as UBO" checkbox | Admin edits any AS field → checkbox auto-unchecks immediately → AS — Independent Edit (edited and remaining copied values retained); Admin manually unchecks → AS — Independent Edit with last independently saved AS values or blank; Admin saves without editing → AS record saved with copied values; checkbox state (checked) persisted |

---

## 1. View the Extended Company Profile Page

### Flow Narrative

The admin navigates to the company profile page. The page loads in Company Profile — Loaded, displaying all existing sections alongside the new Phase 1 sub-sections.

**Section order on the page:**

1. **Basic Company Information** — existing fields (Company Logo, Company Name, Company Code, Company Type, Nature of Business/Industry) followed by four new optional fields: Trade Name, Business Registration Number, Date of Registration/Incorporation, Sub-Industry; and a new required field: Account Type.
2. **Related Companies** *(new; conditionally visible when Account Type ≠ Single Company — see Flow 9)*
3. **Incoming Relationships** *(new; conditionally visible when another company has linked this company as a related company — read-only; see Flow 9)*
4. **Registered Address** *(existing)*
5. **Other Address** *(existing)*
6. **Contact Information** *(existing)*
7. **Government Information** *(existing, including RDO)*
8. **Know Your Customer — Ultimate Beneficial Owner (UBO)** *(new)*
9. **Know Your Customer — Authorized Signatory** *(new)*
10. **Document Requirements — Certificate of Registration** *(new)*

**FinTech gating:**

The Know Your Customer — UBO, Know Your Customer — Authorized Signatory, and Document Requirements — Certificate of Registration sub-sections are visible only to company accounts with Embedded Finance (FinTech) products enabled. Accounts without an active Embedded Finance product do not see these three sub-sections. The Basic Company Information additions (Trade Name, BRN, Date of Registration, Sub-Industry) are not gated and are visible to all accounts.

The **Account Type** field and the **Related Companies** section are not gated by Embedded Finance product — they are visible on all company accounts. Access is role-gated: Account Type and Related Companies are editable only by HR Administrator and Super Admin roles. The Related Companies section is conditionally displayed: it appears only when Account Type is set to a value other than Single Company.

**Empty state for new sub-sections:**

If no KYB data has been entered, each new sub-section displays its fields in their default empty state. Each sub-section shows a brief contextual description under its header to orient administrators unfamiliar with KYB compliance terminology:

- Under **Know Your Customer — Ultimate Beneficial Owner (UBO):** "The Ultimate Beneficial Owner (UBO) is the individual who ultimately owns or controls this company. Provide UBO details to support Know Your Business (KYB) compliance requirements."
- Under **Know Your Customer — Authorized Signatory:** "The Authorized Signatory is the person authorized to sign documents on behalf of this company. If this is the same person as the UBO, check 'Same as UBO' above to copy their details automatically." A "Same as UBO" checkbox appears at the top of this sub-section above the field groups.
- Under **Document Requirements — Certificate of Registration:** "Upload the registration documents that apply to your company's legal entity type. You only need to upload documents relevant to your company structure."

No validation errors appear in the empty state — all new fields are optional.

**Existing data:**

All previously saved values in existing sections are displayed exactly as last saved. Existing fields are not affected by the addition of the new sub-sections.

### Screen States

| State | Trigger | Expected Behaviour |
|---|---|---|
| Company Profile — Loaded (new sub-sections empty) | Admin navigates to company profile; no KYB data has been saved | All new sub-sections visible with empty fields and contextual descriptions; existing sections show saved values |
| Company Profile — Loaded (KYB data present) | Admin navigates to company profile; KYB data was previously saved | New sub-sections show last saved values; upload fields show filenames or thumbnails; AS sub-section reflects last saved checkbox state |

### Field Specification

No user-editable fields are acted on in this flow. Display-only.

| Field (display) | Source | Display Format |
|---|---|---|
| All existing fields | Last saved company profile record | As last saved |
| Trade Name, BRN, Date of Registration, Sub-Industry | Last saved company profile record | Text value or blank |
| All UBO fields | Last saved UBO record | Text values or blank; Gov't ID shows filename or thumbnail if uploaded |
| All AS fields | Last saved AS record | Text values or blank; Gov't ID shows filename or thumbnail; "Same as UBO" checkbox reflects last saved state |
| Certificate of Registration uploads (7 fields) | Last saved document attachments | Filename and download link if uploaded; upload control if empty |

### Business Perspective

Surfacing all KYB and compliance fields on the existing company profile page positions the unified profile as the single canonical employer identity record. Administrators learn one navigation path for all employer-level data. Sub-section contextual descriptions reduce support load for non-compliance users encountering KYB terminology for the first time.

### User Experience Perspective

An administrator arriving at the page after the update should not be confused about what the new sub-sections are for. Contextual descriptions do the orientation work without requiring a tutorial. Existing data is unaffected and visible exactly where it was — the only change is new sub-sections appearing after Government Information. The admin can ignore the new sub-sections entirely without disrupting any existing workflow.

---

## 2. Edit Basic Company Information — New Fields

### Flow Narrative

The admin scrolls to the Basic Company Information section and sees the four new optional fields appended after the existing fields.

- **Trade Name:** Free-text input. No format restriction. Max 255 characters.
- **Business Registration Number:** Free-text input. Alphanumeric. Max 50 characters.
- **Date of Registration/Incorporation:** Date picker. MM/DD/YYYY. Must be a past date if a value is provided.
- **Sub-Industry:** Free-text input. No format restriction. Max 255 characters. Always editable regardless of whether Nature of Business/Industry has a value — Sub-Industry has no cascade dependency.

As soon as the admin edits any field on the page, the page enters Company Profile — Editing.

**Saving with valid values:** Admin clicks Save → Company Profile — Saving → all validations pass → Company Profile — Saved. All four values are persisted and displayed on reload.

**Saving with all four fields blank:** Valid. No validation error. Page enters Company Profile — Saved.

**Validation errors:**

- Trade Name > 255 characters: inline error — "Trade Name must not exceed 255 characters."
- BRN > 50 characters: inline error — "Business Registration Number must not exceed 50 characters."
- Date of Registration is a future date: inline error — "Date of Registration must be a past date."
- Sub-Industry > 255 characters: inline error — "Sub-Industry must not exceed 255 characters."

### Screen States

| State | Trigger | Expected Behaviour |
|---|---|---|
| Company Profile — Editing | Admin types in any new field or selects a date | Page enters Editing; Save and Cancel become active |
| Company Profile — Validation Failed (Trade Name) | Save attempted with Trade Name > 255 characters | Inline error under Trade Name: "Trade Name must not exceed 255 characters" |
| Company Profile — Validation Failed (BRN) | Save attempted with BRN > 50 characters | Inline error under BRN: "Business Registration Number must not exceed 50 characters" |
| Company Profile — Validation Failed (Date of Registration) | Save attempted with a future date selected | Inline error under Date of Registration: "Date of Registration must be a past date" |
| Company Profile — Saved | All validations pass | Success confirmation shown; all four values persisted and displayed on reload |

### Field Specification

| Field | Required | Validation Rule | Error Message |
|---|---|---|---|
| Trade Name | No | Optional; max 255 characters | "Trade Name must not exceed 255 characters" |
| Business Registration Number | No | Optional; max 50 characters; alphanumeric | "Business Registration Number must not exceed 50 characters" |
| Date of Registration/Incorporation | No | Optional; date picker; MM/DD/YYYY; if provided, must be a past date | "Date of Registration must be a past date" |
| Sub-Industry | No | Optional; max 255 characters; free-text | "Sub-Industry must not exceed 255 characters" |

### Business Perspective

Trade Name and Business Registration Number complete the legal identity picture needed for KYB verification workflows. Sub-Industry as a free-text field is a Phase 1 simplification pending a controlled options list — it avoids blocking implementation at the cost of data consistency across companies (different admins may enter "IT," "I.T.," or "Information Technology" for the same sub-industry). This is documented as technical debt to be addressed when a controlled list is available.

### User Experience Perspective

Appending the four new fields at the bottom of the existing Basic Company Information section means administrators find them in context — company identity details in one place, not across two sections. All four fields are optional with no visual prompt indicating the profile is incomplete if they are left blank, which avoids creating a false sense of urgency for data the admin may not have immediately at hand.

---

## 3. Complete UBO Information

### Flow Narrative

The admin scrolls to the Know Your Customer — Ultimate Beneficial Owner sub-section. A contextual description is shown under the header: "The Ultimate Beneficial Owner (UBO) is the individual who ultimately owns or controls this company. Provide UBO details to support Know Your Business (KYB) compliance requirements." Three field groups are presented: Personal Information, Contact Information, and Residential Address. All 14 fields are optional.

**Personal Information:** Full Name (text), Date of Birth (date picker), Nationality (text), Valid Government-issued ID type (dropdown), Valid Government-issued ID (image upload — see Flow 4).

**Contact Information:** Email (text), Mobile Number (text).

**Residential Address:** Address Line 1, Address Line 2, City/Town, Province, Region, Country (all text, max 100 chars), Zipcode (text, max 4 chars).

Any edit to any field on the page triggers Company Profile — Editing.

**Saving with values:** Admin clicks Save. If all field values on the page are valid, the page enters Company Profile — Saved. UBO values are persisted.

**Saving with all UBO fields blank:** Valid. No error. Page enters Company Profile — Saved.

**Partial completion:** The admin may fill some UBO fields and leave others blank. Only fields with values are validated; blank optional fields are not flagged.

**Validation errors (UBO-specific):**

- Date of Birth is a future date: "Date of Birth must be a valid past date."
- Date of Birth indicates person under 18: "UBO must be at least 18 years of age."
- Email value is not a valid email address: "Enter a valid email address."
- Mobile Number does not match PH format: "Enter a valid Philippine mobile number (e.g., 09XXXXXXXXX or +639XXXXXXXXX)."
- Any text field exceeds its character limit: "[Field name] must not exceed [N] characters."

### Screen States

| State | Trigger | Expected Behaviour |
|---|---|---|
| Company Profile — Editing | Admin enters a value in any UBO field | Page enters Editing state |
| Company Profile — Validation Failed (UBO DOB — future) | Save attempted with a future UBO Date of Birth | Inline error: "Date of Birth must be a valid past date" |
| Company Profile — Validation Failed (UBO DOB — under 18) | Save attempted with UBO DOB indicating person under 18 | Inline error: "UBO must be at least 18 years of age" |
| Company Profile — Validation Failed (UBO Email) | Save attempted with invalid email in UBO Email field | Inline error: "Enter a valid email address" |
| Company Profile — Validation Failed (UBO Mobile) | Save attempted with mobile not matching PH format | Inline error: "Enter a valid Philippine mobile number (e.g., 09XXXXXXXXX or +639XXXXXXXXX)" |
| Company Profile — Saved | All provided UBO values valid (or all UBO fields blank) and all other sections pass | UBO values persisted; success confirmation shown |

### Field Specification

| Field | Required | Validation Rule | Error Message |
|---|---|---|---|
| Full Name | No | Optional; if provided, max 255 characters | "Full Name must not exceed 255 characters" |
| Date of Birth | No | Optional; date picker; MM/DD/YYYY; if provided: must be a past date and indicate person ≥18 years old | "Date of Birth must be a valid past date" / "UBO must be at least 18 years of age" |
| Nationality | No | Optional; if provided, max 100 characters | "Nationality must not exceed 100 characters" |
| Valid Government-issued ID type | No | Optional; dropdown — values: Philippine Passport, SSS ID, GSIS ID, PhilHealth ID, Pag-IBIG ID, Driver's License, PRC ID, NBI Clearance, Voter's ID, Postal ID | N/A — dropdown selection; free-text not possible |
| Valid Government-issued ID (image) | No | Optional; covered in Flow 4 | See Flow 4 |
| Email | No | Optional; if provided, must be a valid email address | "Enter a valid email address" |
| Mobile Number | No | Optional; if provided, must match PH mobile format: 11 digits starting with 09, or international format starting with +639 | "Enter a valid Philippine mobile number (e.g., 09XXXXXXXXX or +639XXXXXXXXX)" |
| Address Line 1 | No | Optional; if provided, max 255 characters | "Address Line 1 must not exceed 255 characters" |
| Address Line 2 | No | Optional; if provided, max 255 characters | "Address Line 2 must not exceed 255 characters" |
| City/Town | No | Optional; if provided, max 100 characters | "City/Town must not exceed 100 characters" |
| Province | No | Optional; if provided, max 100 characters | "Province must not exceed 100 characters" |
| Region | No | Optional; if provided, max 100 characters | "Region must not exceed 100 characters" |
| Country | No | Optional; if provided, max 100 characters | "Country must not exceed 100 characters" |
| Zipcode | No | Optional; if provided, max 4 characters (PH postal code) | "Zipcode must not exceed 4 characters" |

### Business Perspective

UBO data is a standard KYB requirement for financial services onboarding in the Philippines. Capturing it in the company profile ensures that FinTech-enabled workflows can reference verified UBO identity data without requiring separate onboarding forms. Making all fields optional in Phase 1 reduces friction for existing companies that may not have this data ready at initial setup.

### User Experience Perspective

Grouping the 14 UBO fields into three labeled groups mirrors the mental model administrators already use for employee records — the same grouping convention (Personal, Contact, Address) applies to both contexts. The optional-field treatment means the sub-section does not create a blocking experience: an administrator can complete the rest of the company profile without being forced to gather KYB documentation first.

---

## 4. Upload Government-Issued ID

*Behaviour is identical for UBO Gov't ID and AS Gov't ID fields.*

### Flow Narrative

The admin locates the Valid Government-issued ID field within the UBO or AS sub-section. The field is in Upload Field — Empty state.

**Initiating an upload:** Admin clicks the upload control and selects a file. The field enters Upload Field — Uploading. An in-progress indicator is shown. The admin cannot select another file for this field while the upload is in progress.

**Successful upload:** The field enters Upload Field — Uploaded. The filename or a thumbnail of the image is displayed inline. Two controls appear: Replace and Remove.

**Replacing an uploaded file:** Admin clicks Replace. The field enters Upload Field — Uploading with the new file. If the new upload succeeds, the field returns to Upload Field — Uploaded showing the new file. If it fails, the field returns to Upload Field — Uploaded showing the original file, with the error message for the failed attempt shown.

**Removing an uploaded file:** Admin clicks Remove. A confirmation dialog appears: "Remove this file? You can re-upload it at any time." with "Remove" and "Cancel" actions. If the admin confirms, the file is removed and the field returns to Upload Field — Empty, triggering Company Profile — Editing. If the admin cancels the dialog, no change occurs and the field remains in Upload Field — Uploaded.

**Wrong format rejection:** If the selected file is not PNG, JPG, or JPEG, the upload is rejected immediately. Field returns to prior state. Error: "Accepted formats: PNG, JPG, JPEG."

**File too large rejection:** If the file exceeds 5MB, the upload is rejected. Field returns to prior state. Error: "File size must not exceed 5MB."

**Gov't ID type and image — independence:** The Gov't ID type dropdown and the Gov't ID image upload are independent optional fields. Selecting an ID type does not require uploading an image, and uploading an image does not require selecting an ID type first.

### Screen States

| State | Trigger | Expected Behaviour |
|---|---|---|
| Upload Field — Empty | No file uploaded; or admin removes existing file | Upload control shown; no filename or thumbnail displayed |
| Upload Field — Uploading | Admin selects a file | In-progress indicator shown; upload control disabled |
| Upload Field — Uploaded | Upload completes successfully | Filename or thumbnail shown inline; Replace and Remove controls visible |
| Upload Field — Wrong Format Error | Admin selects a file that is not PNG, JPG, or JPEG | Error: "Accepted formats: PNG, JPG, JPEG"; field returns to prior state |
| Upload Field — File Too Large Error | Admin selects a file larger than 5MB | Error: "File size must not exceed 5MB"; field returns to prior state |

### Field Specification

| Field | Required | Validation Rule | Error Message |
|---|---|---|---|
| Valid Government-issued ID type | No | Optional; dropdown selection from fixed list | N/A — dropdown only |
| Valid Government-issued ID (image) | No | Optional; if provided: PNG, JPG, or JPEG; max 5MB | "Accepted formats: PNG, JPG, JPEG" / "File size must not exceed 5MB" |

### Business Perspective

Capturing a government-issued ID image alongside UBO and AS personal data provides documentary evidence for KYB identity verification. Keeping ID type and image upload independent avoids a sequential dependency that could stall data entry — an administrator can record the ID type before the image is available, or upload the image without yet knowing the document category to assign.

### User Experience Perspective

Inline display of the filename or thumbnail after a successful upload gives the admin immediate confirmation that the correct file was attached without requiring navigation to a separate document viewer. Replace and Remove controls are visible in the uploaded state — not hidden behind a hover — so administrators can locate them without exploration.

---

## 5. Complete Authorized Signatory — Independent

### Flow Narrative

The admin scrolls to the Know Your Customer — Authorized Signatory sub-section. A contextual description is shown under the header: "The Authorized Signatory is the person authorized to sign documents on behalf of this company. If this is the same person as the UBO, check 'Same as UBO' above to copy their details automatically." The "Same as UBO" checkbox at the top of the sub-section is unchecked (AS — Independent Edit state).

The AS sub-section contains the same three field groups as UBO — Personal Information, Contact Information, Residential Address — with identical field names, types, and validation rules, with one exception in the DOB under-18 error message.

**Filling AS fields independently:** Admin fills any combination of fields. Page enters Company Profile — Editing. Save and Cancel become active.

**Saving with all AS fields blank:** Valid. No error. Page enters Company Profile — Saved.

**AS data is stored independently from UBO:** The AS record is separate. Saving AS values does not overwrite UBO values, and vice versa. UBO Full Name and AS Full Name are separate fields on separate records.

**Validation errors (AS-specific):**

- Date of Birth is a future date: "Date of Birth must be a valid past date."
- Date of Birth indicates person under 18: "Authorized Signatory must be at least 18 years of age." *(differs from UBO error message)*
- Email invalid: "Enter a valid email address."
- Mobile invalid: "Enter a valid Philippine mobile number (e.g., 09XXXXXXXXX or +639XXXXXXXXX)."

### Screen States

| State | Trigger | Expected Behaviour |
|---|---|---|
| AS — Independent Edit | Page loads with checkbox unchecked; or admin unchecks "Same as UBO" | All AS fields editable; checkbox unchecked; fields show last independently saved AS values or blank |
| Company Profile — Validation Failed (AS DOB — future) | Save attempted with future AS Date of Birth | Inline error: "Date of Birth must be a valid past date" |
| Company Profile — Validation Failed (AS DOB — under 18) | Save attempted with AS DOB indicating person under 18 | Inline error: "Authorized Signatory must be at least 18 years of age" |
| Company Profile — Validation Failed (AS Email) | Save attempted with invalid email in AS Email | Inline error: "Enter a valid email address" |
| Company Profile — Validation Failed (AS Mobile) | Save attempted with invalid mobile in AS Mobile Number | Inline error: "Enter a valid Philippine mobile number (e.g., 09XXXXXXXXX or +639XXXXXXXXX)" |
| Company Profile — Saved | All AS values valid (or all blank) and all other sections pass | AS values persisted independently from UBO; success confirmation shown |

### Field Specification

*Identical to Flow 3 (UBO) with one exception:*

| Field | Required | Validation Rule | Error Message |
|---|---|---|---|
| Date of Birth | No | Optional; date picker; MM/DD/YYYY; if provided: must be a past date; person must be ≥18 years old | "Date of Birth must be a valid past date" / "Authorized Signatory must be at least 18 years of age" |

*All other AS fields have identical validation rules and error messages as their UBO counterparts in Flow 3.*

### Business Perspective

Storing UBO and AS as independent records reflects the real-world possibility that a company's ultimate beneficial owner and its authorised signatory are different people. Keeping them separate at the data level ensures changes to one do not overwrite the other — which matters for compliance documentation where each role may need to be auditable independently.

### User Experience Perspective

The AS sub-section mirrors the UBO sub-section field-for-field, reducing cognitive load: an administrator who has filled in UBO data knows exactly what to expect from the AS form. The "Same as UBO" checkbox at the top provides the shortcut for the common case where both roles are the same person — but the default state is independent editing, which is the more defensible compliance default.

---

## 6. Use "Same as UBO"

### Flow Narrative

The admin opens the AS sub-section. The "Same as UBO" checkbox is visible at the top, above the Personal Information group. Current state: AS — Independent Edit.

**Checking "Same as UBO" — UBO has values:**

Admin checks the checkbox. All AS fields immediately populate with the current UBO field values as they appear on screen — including any in-progress edits the admin has made to UBO fields in the current session that have not yet been saved. The sub-section enters AS — Copied from UBO. The page enters Company Profile — Editing.

Note: because the copy includes in-progress UBO edits, if a UBO field contains an invalid value at the time the checkbox is checked (e.g., a future DOB), that invalid value is copied to the corresponding AS field. Both the UBO and AS fields will then fail validation on save. The admin corrects the UBO field value; the AS field should be updated to match.

AS fields are individually editable after population. If the admin edits any AS field, the "Same as UBO" checkbox auto-unchecks immediately — the sub-section transitions to AS — Independent Edit with the edited value retained in the edited field and all other AS fields retaining their copied values. The visual state change of the checkbox is the signal that AS data has diverged from UBO. No additional notification is shown.

**Checking "Same as UBO" — UBO is entirely blank:**

If no UBO data has been entered (saved or in-progress), checking "Same as UBO" copies blank values into all AS fields. An informational note appears within the AS sub-section: "No UBO information found. Fill in the UBO section above and re-check to copy values." The checkbox remains checked. All AS fields are blank and editable. Page enters Company Profile — Editing.

**Saving with "Same as UBO" still checked (no AS fields edited):**

Admin saves without editing any AS field. The AS record is saved with the copied UBO values. The checkbox state (checked) is persisted. On the next page load, the checkbox remains checked and AS fields show the saved copied values.

**Saving after auto-uncheck (one or more AS fields were edited):**

Admin edits one or more AS fields (checkbox auto-unchecked), then saves. The AS record is saved with the current AS field values. The checkbox state (unchecked) is persisted. On the next page load, the checkbox is unchecked and AS fields show the saved values.

**Unchecking "Same as UBO":**

Admin unchecks the checkbox. The sub-section returns to AS — Independent Edit. AS fields revert to the last values that were saved independently for AS — that is, the AS record's state before "Same as UBO" was ever checked. If no independent AS save exists, all AS fields revert to blank.

UBO-copied values are not automatically discarded until a save occurs in the independent state.

**UBO changes after "Same as UBO" was saved:**

If the admin saves the AS record with "Same as UBO" checked and later updates UBO fields, the AS record is NOT automatically updated. To sync AS to new UBO values, the admin must uncheck and recheck "Same as UBO," or update AS fields manually.

### Screen States

| State | Trigger | Expected Behaviour |
|---|---|---|
| AS — Independent Edit | Page loads; or admin manually unchecks "Same as UBO" | Checkbox unchecked; all AS fields editable; fields show last independently saved AS values or blank |
| AS — Copied from UBO (UBO has values) | Admin checks "Same as UBO" and UBO fields have values (saved or in-progress) | Checkbox checked; AS fields populated with current on-screen UBO values; fields remain individually editable; invalid UBO values copied as-is and will fail validation on save |
| AS — Copied from UBO (UBO is blank) | Admin checks "Same as UBO" and no UBO values have been entered | Checkbox checked; all AS fields blank; fields editable; informational note shown |
| AS — Independent Edit (auto-uncheck on field edit) | Admin edits any AS field while "Same as UBO" is checked | Checkbox auto-unchecks immediately; edited field shows new value; all other AS fields retain their current copied values; no notification shown |
| AS — Independent Edit (manual uncheck) | Admin manually unchecks "Same as UBO" | Checkbox unchecked; AS fields revert to last independently saved AS values, or blank if no independent save exists |

### Field Specification

*AS field validation is identical to Flow 5. No additional validation applies to "Same as UBO"-populated fields — copied values are subject to the same validation rules as manually entered values.*

### Business Perspective

The "Same as UBO" option exists because many companies — particularly SMEs in the Philippines — have the same individual serving as both UBO and Authorised Signatory. Without this shortcut, those administrators must enter identical personal, contact, and address data twice. The one-time copy model rather than a live sync preserves compliance data integrity: if the UBO changes, the AS record is not silently overwritten, which matters when the AS record may be referenced in signed documents or compliance filings.

### User Experience Perspective

Placing the "Same as UBO" checkbox at the top of the AS sub-section — before the field groups — means the admin sees the shortcut before they begin filling in fields, not after typing everything in. Immediate field population on check gives the admin clear confirmation that the copy occurred. The ability to edit individual copied fields afterward means the option is non-destructive: it is a starting point, not a lock.

---

## 7. Upload Certificate of Registration Documents

### Flow Narrative

The admin scrolls to the Document Requirements — Certificate of Registration sub-section. A contextual description is shown under the header: "Upload the registration documents that apply to your company's legal entity type. You only need to upload documents relevant to your company structure."

Seven upload fields are presented, each in Upload Field — Empty by default:

1. SEC Certificate (for corporations/partnerships)
2. DTI Certificate (for sole proprietorships)
3. CDA Certificate (for cooperatives)
4. Articles of Incorporation / Partnership
5. General Information Sheet (GIS) for corporations
6. Mayor's / Business Permit
7. Board Resolution or Secretary's Certificate

Each field label includes a parenthetical indicating which entity type it applies to.

**Uploading a document:** Admin clicks the upload control for a field and selects a file. Field enters Upload Field — Uploading. On success, field enters Upload Field — Uploaded. Unlike Gov't ID fields, successful document uploads display a filename and a download link — not a thumbnail.

**Replace and Remove:** Replace initiates a new upload for that field — behaviour identical to Flow 4. Remove shows a confirmation dialog: "Remove this file? You can re-upload it at any time." with "Remove" and "Cancel" actions. If the admin confirms, the file is removed and the field returns to Upload Field — Empty, triggering Company Profile — Editing. If the admin cancels the dialog, no change occurs and the field remains in Upload Field — Uploaded.

**Wrong format rejection:** Accepted formats: PDF, PNG, JPG, JPEG. If rejected: "Accepted formats: PDF, PNG, JPG, JPEG." Field returns to prior state.

**File too large rejection:** Max 10MB. If rejected: "File size must not exceed 10MB." Field returns to prior state.

**All seven fields left blank:** Valid. All are optional. Page saves without error.

**Multiple uploads:** Each of the seven fields is independent. Any combination may be uploaded. Each file is stored separately and accessible via its own download link.

### Screen States

| State | Trigger | Expected Behaviour |
|---|---|---|
| Upload Field — Empty (document) | No document uploaded; or document was removed | Upload control shown; no filename or link displayed |
| Upload Field — Uploading (document) | Admin selects a file | In-progress indicator shown; upload control disabled for this field |
| Upload Field — Uploaded (document) | Upload completes successfully | Filename and download link displayed; Replace and Remove controls visible |
| Upload Field — Wrong Format Error (document) | Admin selects a file that is not PDF, PNG, JPG, or JPEG | Error: "Accepted formats: PDF, PNG, JPG, JPEG"; field returns to prior state |
| Upload Field — File Too Large Error (document) | Admin selects a file larger than 10MB | Error: "File size must not exceed 10MB"; field returns to prior state |

### Field Specification

| Field | Required | Validation Rule | Error Message |
|---|---|---|---|
| SEC Certificate | No | Optional; if provided: PDF, PNG, JPG, or JPEG; max 10MB | "Accepted formats: PDF, PNG, JPG, JPEG" / "File size must not exceed 10MB" |
| DTI Certificate | No | Optional; if provided: PDF, PNG, JPG, or JPEG; max 10MB | "Accepted formats: PDF, PNG, JPG, JPEG" / "File size must not exceed 10MB" |
| CDA Certificate | No | Optional; if provided: PDF, PNG, JPG, or JPEG; max 10MB | "Accepted formats: PDF, PNG, JPG, JPEG" / "File size must not exceed 10MB" |
| Articles of Incorporation / Partnership | No | Optional; if provided: PDF, PNG, JPG, or JPEG; max 10MB | "Accepted formats: PDF, PNG, JPG, JPEG" / "File size must not exceed 10MB" |
| General Information Sheet (GIS) | No | Optional; if provided: PDF, PNG, JPG, or JPEG; max 10MB | "Accepted formats: PDF, PNG, JPG, JPEG" / "File size must not exceed 10MB" |
| Mayor's / Business Permit | No | Optional; if provided: PDF, PNG, JPG, or JPEG; max 10MB | "Accepted formats: PDF, PNG, JPG, JPEG" / "File size must not exceed 10MB" |
| Board Resolution or Secretary's Certificate | No | Optional; if provided: PDF, PNG, JPG, or JPEG; max 10MB | "Accepted formats: PDF, PNG, JPG, JPEG" / "File size must not exceed 10MB" |

### Business Perspective

Showing all seven certificate fields to all company types — without hiding fields by legal entity — avoids requiring the system to maintain and enforce which documents are mandatory for which structure. Companies with ambiguous or evolving legal structures may legitimately upload across categories. Optional visibility for all is the lowest-friction Phase 1 approach; conditional logic by company type is deferred.

### User Experience Perspective

Parenthetical entity-type labels on each field (e.g., "for corporations/partnerships") provide contextual guidance without requiring a help modal or external reference. Administrators can scan the list and identify relevant fields. The download link on an uploaded document lets the admin verify the correct file was attached without navigating away from the page.

---

## 8. Save or Cancel Company Profile Changes

### Flow Narrative

**Saving:**

The admin is in Company Profile — Editing and clicks Save. The page enters Company Profile — Saving. Save and Cancel are disabled during processing. All field values across all page sections are validated simultaneously — there is a single Save action for the entire company profile page, with no per-section save buttons.

**Save succeeds:** All validations pass. The page enters Company Profile — Saved. A success confirmation is shown: "Company profile saved." All changes across all sections are persisted in a single operation. The page returns to Company Profile — Loaded.

**Save fails — one section has errors:** One or more fields fail validation. The page enters Company Profile — Validation Failed. Inline error messages appear under each failing field. No data is persisted — the entire save is rolled back. All unsaved changes across all sections (including sections with no errors) are preserved. The admin corrects the failing fields and clicks Save again.

**Save fails — multiple sections have errors:** Errors in multiple sections are shown simultaneously. All inline error messages appear at once. The page scrolls to the first failing field and sets focus to it. The admin can see the full scope of corrections needed without re-triggering the save.

**Validation timing:** Validation errors are shown only after the admin clicks Save. Once a field has shown an error, that field is re-validated on blur — when the admin leaves the field — so the error clears as soon as the value is corrected. Blur-time validation does not trigger before the first save attempt.

**Cancelling with unsaved changes:**

Admin clicks Cancel. The Discard Confirmation prompt appears — title: "Discard unsaved changes?" / body: "You have unsaved changes. If you leave now, your changes will be lost." — with confirm action "Discard changes" and dismiss action "Keep editing."

- **Admin confirms discard:** All unsaved changes are abandoned. The page returns to Company Profile — Loaded showing last saved values.
- **Admin dismisses the prompt:** The prompt closes. The admin returns to Company Profile — Editing with all unsaved changes intact.

**Cancelling without unsaved changes:** If the admin is in Company Profile — Loaded and clicks Cancel or navigates away, no Discard Confirmation is shown.

**Navigating away with unsaved changes:** If the admin attempts to navigate away from the page while in Company Profile — Editing, the Discard Confirmation prompt appears. Behaviour is identical to the Cancel flow above.

### Screen States

| State | Trigger | Expected Behaviour |
|---|---|---|
| Company Profile — Saving | Admin clicks Save from Editing or Validation Failed | Page processes and validates all fields simultaneously; Save and Cancel disabled |
| Company Profile — Saved | All validations pass | Success confirmation shown; all changes persisted; page returns to Loaded |
| Company Profile — Validation Failed (single section) | One or more fields in one section fail | Inline errors shown under failing fields; all unsaved changes preserved across all sections |
| Company Profile — Validation Failed (multiple sections) | Fields in more than one section fail | All inline errors shown simultaneously across sections; page scrolls to first failing field and sets focus to it |
| Discard Confirmation | Admin clicks Cancel with unsaved changes; or navigates away with unsaved changes | Prompt shown — "Discard unsaved changes?" / "You have unsaved changes. If you leave now, your changes will be lost." — with "Discard changes" and "Keep editing" actions |
| Changes Discarded | Admin confirms discard | All unsaved changes abandoned; page returns to Loaded with last saved values |
| Company Profile — Editing (after dismiss) | Admin dismisses Discard Confirmation | Prompt closes; admin returns to Editing state with all unsaved changes intact |

### Field Specification

No fields specific to this flow. Save and Cancel apply across all fields on the page as specified in Flows 2–7.

### Business Perspective

A single Save action across the entire page ensures company profile data is saved as a consistent snapshot. Partial saves — where Basic Company Info is persisted but UBO data fails — would create an inconsistent profile state that is difficult to reconcile and could generate misleading compliance records. Rolling back the entire save on any validation failure keeps the profile in a known-good state at all times.

### User Experience Perspective

The Discard Confirmation prompt protects administrators from accidentally losing work on a long form with many optional fields — a real risk when the admin has filled in a detailed UBO or AS record and inadvertently clicks Cancel or a navigation link. Showing all validation errors simultaneously rather than one at a time respects the admin's time: they can see the full scope of corrections needed in one pass and address everything before re-saving.

---

## Appendix A: Adaptive UI Copy Reference

| Scenario | Expected copy |
|---|---|
| Save success confirmation | "Company profile saved." |
| Discard Confirmation prompt title | "Discard unsaved changes?" |
| Discard Confirmation body | "You have unsaved changes. If you leave now, your changes will be lost." |
| Discard Confirmation — confirm action label | "Discard changes" |
| Discard Confirmation — dismiss action label | "Keep editing" |
| UBO sub-section contextual description | "The Ultimate Beneficial Owner (UBO) is the individual who ultimately owns or controls this company. Provide UBO details to support Know Your Business (KYB) compliance requirements." |
| AS sub-section contextual description | "The Authorized Signatory is the person authorized to sign documents on behalf of this company. If this is the same person as the UBO, check 'Same as UBO' above to copy their details automatically." |
| Document Requirements sub-section contextual description | "Upload the registration documents that apply to your company's legal entity type. You only need to upload documents relevant to your company structure." |
| "Same as UBO" — blank UBO informational note | "No UBO information found. Fill in the UBO section above and re-check to copy values." |
| Remove file confirmation — title | "Remove this file?" |
| Remove file confirmation — body | "You can re-upload it at any time." |
| Remove file confirmation — confirm action label | "Remove" |
| Remove file confirmation — dismiss action label | "Cancel" |
| Account Type — required error | "Account Type is required." |
| Related Companies — empty state prompt | "No related companies added yet. Click '+ Add Company' to link your first company." |
| Relationship Type — persistent helper line | "Describes how [Related Company Name] relates to [Current Company Name]. The related company is always the subject." |
| Relationship Type — dynamic inline preview | "[Related Company Name] is a [Relationship Type] of [Current Company Name]." |
| Relationship Type — Manages tooltip | "[Related Company] has management authority over [Current Company]." |
| Relationship Type — Managed By tooltip | "[Current Company] has management authority over [Related Company]." |
| Add Company — create option label | "Create New Company" |
| Scenario B — return context banner | "You created [Org Name]. Now define its relationship to [Current Company Name]." |
| Website URL — Same as parent checkbox label | "Same as [Current Company Name]" |
| Website URL — Same as parent, no URL on parent | "No website URL found for [Current Company Name]. Add a URL to the current company profile first." |
| Effectivity Date — required error | "Effectivity Date is required." |
| End Date — before Effectivity Date error | "End Date must be on or after Effectivity Date." |
| Add Company — search field placeholder | "Search by company name..." |
| Add Company — no results message | "No company found. You can create a new company instead." |
| TIN/BRN duplicate warning (Scenario B) | "A company with this TIN/BRN already exists. Do you want to link the existing company instead?" |
| Incoming Relationships — section header | "Incoming Relationships" |

---

## Appendix B: Open Unknowns

1. **Housing / URL for the unified company profile** — Where does this page live? HR and Payroll use different URLs. Recommendation is a neutral shared admin/settings URL accessible from both modules. To be discussed during alignment with Design and Engineering teams. Decision required before implementation begins. *Owner: PM + Engineering.*

2. ~~**Validation error timing — blur or save only**~~ — **Resolved:** Errors are shown on save first. Once an error has been shown on a field, that field re-validates on blur so the error clears when the admin corrects it. Blur validation does not trigger before the first save attempt. Flow 8 updated accordingly.

3. ~~**Scroll to first error on validation failure**~~ — **Resolved:** Yes — on validation failure, the page scrolls to the first failing field and sets focus to it. Flow 8 updated accordingly.

4. ~~**"Same as UBO" — unsaved UBO edits not included in copy**~~ — **Resolved:** "Same as UBO" copies current on-screen UBO values including in-progress unsaved edits. Flow 6 updated accordingly.

5. ~~**"Same as UBO" — warning when UBO is blank**~~ — **Resolved:** An informational note appears within the AS sub-section when the admin checks "Same as UBO" with no UBO data present: "No UBO information found. Fill in the UBO section above and re-check to copy values." This is informational, not an error — no blocking behaviour. Flow 6 updated accordingly.

6. ~~**Gov't ID type and image — dependency**~~ — **Resolved:** Gov't ID type (dropdown) and Gov't ID image (upload) remain independent optional fields. No sequential dependency required. Flow 4 confirmed accordingly.

7. ~~**Remove action — confirmation dialog**~~ — **Resolved:** A confirmation dialog appears before removal. Dialog: "Remove this file? You can re-upload it at any time." — confirm action "Remove," dismiss action "Cancel." Flows 4 and 7 updated accordingly.

8. ~~**Single UBO / Single AS — UI messaging**~~ — **Resolved:** The Phase 1 single-UBO/single-AS limitation is not communicated on the page. The "Why This Page Works This Way" section of this spec documents the Phase 1 simplification for the build team. Engineering will model UBO and AS data as a list/array from day one to avoid a migration when multi-UBO/AS ships in Phase 2.

9. **Access control — which roles can edit KYB fields** — Pending alignment with the Embedded Finance team to determine which persona(s) handle KYB/UBO/AS data. *Owner: PM + Embedded Finance team.*

10. ~~**FinTech product gating — KYB sub-sections visibility**~~ — **Resolved:** KYB sub-sections (UBO, Authorized Signatory, Document Requirements) are visible only to accounts with Embedded Finance (FinTech) products enabled. Flow 1 and PRD updated accordingly.

11. **Audit logging — follow-on ticket required** — Audit logging for company profile changes is a Phase 1 PRD requirement but is not yet implemented. A dedicated audit logging implementation ticket is needed. PM noted; ticket creation pending. *Owner: PM.*

12. **Data privacy — Legal/Compliance sign-off required** — UBO and AS sub-sections collect sensitive PII subject to the PH Data Privacy Act (RA 10173). PM will coordinate requirements with Legal/Compliance before go-live. *Owner: PM + Legal/Compliance.*

13. **Related Companies — editing an existing relationship record** — How does the user edit or update an existing relationship row in the table: edit button per row, expandable row, or slide-out drawer? What fields are editable after a relationship is saved? *Owner: Product Designer.*

14. **Related Companies — empty state design** — What does the Related Companies section look like before any companies have been added? *Owner: Product Designer.*

15. **Account Type change with existing relationships** — If Account Type is changed to Single Company after relationships have already been saved, what happens? Are records preserved but hidden, or is a warning/confirmation shown before the section collapses? *Owner: Product Designer.*

16. **Relationship Type mismatch after Account Type change** — If a saved relationship uses a Relationship Type that is no longer available under the new Account Type (e.g., Subsidiary Of saved, then Account Type changed to Franchise Network), what does the system show? Does the saved value persist as-is or show a warning? *Owner: Product Designer.*

17. **Scenario B — return flow context** — After the user saves a new company via the lightweight form and is returned to the original company profile, what UX pattern communicates the context? A banner, breadcrumb, or other pattern? *Owner: Product Designer — Ticket 2 (`tickets/add-account-type-scenario-b.md`).*

18. **LocationIQ address autocomplete interaction** — How many characters trigger suggestions? How many results are shown? What is the fallback UX if LocationIQ is unavailable? *Owner: Product Designer + Engineering.*

19. **Website URL — "Same as parent" checkbox when parent has no URL** — What happens when the checkbox is checked but the current company has no Website URL saved? Is it disabled, hidden, or does it show a helper message? *Owner: Product Designer — Ticket 2 (`tickets/add-account-type-scenario-b.md`).*

20. **Related Companies table — default Status filter** — Should the table show all relationship statuses by default (including Inactive and Ended), or should those be filtered out by default with an option to reveal them? *Owner: Product Designer.*

21. **Related Companies table — scale and pagination** — Is there a filter or search on the table for companies with many linked orgs? At what row count does pagination activate? *Owner: Product Designer + Engineering.*

22. **Linked org's own company status** — Relationship Status describes the state of the link, not the state of the related company itself. A related org may have an Active relationship but be deactivated as a company entity. How should the table visually distinguish rows where the linked company is itself inactive or deactivated? *Owner: Product Designer.*

23. **Add Company — domain-scoped list and search interaction** — How is the domain-scoped list presented when "+ Add Company" is clicked — as a modal, slide-out panel, or inline expansion? How does the list load and paginate for clients with 400+ organizations? How is the search field positioned relative to the list? How is the "no results" state communicated, and how is the "Create New Company" escape offered from within the no-results state? *Owner: Product Designer — Ticket 1 (`tickets/add-account-type.md`).*

24. **Incoming Relationships section design** — Where on the company profile page does Incoming Relationships appear — after the Related Companies section, or in a separate position? What does the section look like for a Single Company account that has no Related Companies section of its own? What is the hidden state when no incoming relationships exist? *Owner: Product Designer — Ticket 1 (`tickets/add-account-type.md`).*

25. **Scenario B — entry point presentation in no results state** — How is the "Create New Company" option presented when the domain-scoped list search returns no results — inline below the message, as a button, or another pattern? *Owner: Product Designer — Ticket 2 (`tickets/add-account-type-scenario-b.md`).*

26. **Scenario B — sub-flow navigation and cancellation** — How does the user know they are in a sub-flow that will return them to the original company profile? Is there a back/cancel option that discards the new org creation and returns to the original profile without creating any record? *Owner: Product Designer — Ticket 2 (`tickets/add-account-type-scenario-b.md`).*
