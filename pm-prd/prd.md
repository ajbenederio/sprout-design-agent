# Company Profile Unified Front-End PRD

## Document Header
- **Status:** Draft
- **Version:** v0.4
- **Owner:** TBD
- **Last updated:** 2026-05-18
- **Capability ID(s):** CAP-2026-015 (Primary)
- **Product Area:** Cross-Domain
- **PRD Link / Path:** outputs/pm-agent-outputs/company-profile-hr-payroll/payroll-company-profile/prd.md

### Revision History
| Date | Version | Author | Summary |
|---|---|---|---|
| 2026-05-15 | v0.1 | PM Agent | Initial draft for unified company profile front-end across HR and Payroll |
| 2026-05-18 | v0.2 | PM Agent | Merged Industry into Nature of Business/Industry; changed Sub-Industry to String; removed Source of Funds; added KYB/UBO/Authorized Signatory/Document Requirements to Phase 1 field model and scope; added housing/URL question to Open Questions |
| 2026-05-28 | v0.3 | PM Agent | Added AccountType field to Basic Company Information; added Account Structure — Related Companies section (Company record + Relationship record) to field model; updated Phase 1 scope, user stories, and functional requirements to include Epic 3 — Account Structure & Related Companies |
| 2026-06-01 | v0.4 | PM Agent | Replaced TIN/BRN exact-match lookup (Scenario A) with domain-scoped list — shows all companies under the same Sprout HR tenant, searchable by company name; updated Organization Record, Phase 1 scope, user stories, and functional requirements accordingly |

### Access Control & Audit Trail
- **Roles gaining or changing access:** HR Administrator, Payroll Administrator
- **Nature of access change:** Both personas can access a shared company profile workspace that exposes their relevant view and edit rights from a unified front-end.
- **Audit logging required:** Yes
- **Events to log:** Company profile record viewed, field-level updates submitted, changes approved or rejected, affected entity (company profile), actor identity, timestamp.

## Objective
To streamline the functionality by transitioning HR and Payroll-specific policies and rules into a unified shared module accessible across all Sprout products. This ensures greater flexibility, seamless integration, and consistent company-level governance across HR and Payroll.

## Executive Summary
The Company Profile Unified Front-End initiative creates a single, shared company profile workspace for HR and Payroll systems so HR Admins and Payroll Admins can manage employer data consistently without duplicate configuration or reconciliation overhead. Starting from the current separate HR and Payroll company profile experiences, this PRD defines a unified front-end that preserves role-appropriate access, keeps shared employer settings synchronized, and reduces the operational risk of inconsistent company data across modules.

## Problem Statement
Today, HR and Payroll systems maintain separate company profile experiences, causing duplicate data entry, mismatched company settings, and frequent reconciliation work between HR Admins and Payroll Admins. This fragmentation increases the risk of payroll errors and compliance gaps because employer-level details such as statutory contribution settings, entity metadata, and payroll mapping fields are shared across both domains but managed in different places. Without a shared front-end, administrators waste time resolving conflicting record versions, and teams lose confidence in the company-level data that drives downstream HR and payroll processes.

## Target Users & Personas
- **HR Administrator:** Manages workforce administration, employee records, company policies, and compliance reports. Needs a consistent company profile workspace that avoids duplicate edits and clearly shows what changes impact payroll.
- **Payroll Administrator:** Runs payroll cycles, validates statutory compliance, and reconciles upstream HR inputs. Needs a reliable view of company-level payroll settings and a way to ensure changes are reflected accurately in payroll without manual reconciliation.
- **Finance Business Partner (ENT only):** Benefits indirectly from fewer reconciliation exceptions and clearer company-level financial context when HR and Payroll share a single source of truth.

## Strategic Context
This initiative aligns with the company’s enterprise strategy by making HR and Payroll more connected and reducing the risk of data integrity issues that can undermine payroll accuracy. A unified company profile supports the strategic priorities of Payroll Accuracy & Compliance Reliability and Data Integrity Across Financial Modules by eliminating a key source of upstream mismatches. It also reinforces the platform differentiator of a connected HR and payroll experience, which is essential for enterprise customers that require consistent employer-level settings across multiple modules.

## Solution Overview
Build a unified Company Profile front-end that serves as the canonical workspace for shared employer data used by HR, Payroll, and downstream Sprout products. The solution will:
- Surface a single page for company profile data, consolidating HR, Payroll, and FinTech fields into one canonical profile page.
- Group fields into clear sections: Basic Company Information, Registered Address, Other Address, Contact Information, Government Information, KYB — Ultimate Beneficial Owner, KYB — Authorized Signatory, and Document Requirements — Certificate of Registration.
- Preserve role-specific views and edit permissions so HR Admins and Payroll Admins see the fields relevant to their responsibilities and can make changes without stepping on each other.
- Synchronize shared data changes to both HR and Payroll systems with validation and reconciliation checks.
- Highlight differences or pending updates that require review before they affect payroll processing.
- Maintain a full audit trail for company profile changes.

## Consolidated Company Profile Field Model
The single page will include the following consolidated fields and sections. Primary source-of-truth alignment should be maintained as follows:
- HR owns branding, company metadata, address, and contact profile fields.
- Payroll owns government compliance identifiers and tax-related registration fields.
- FinTech adds optional KYB and enterprise metadata fields that extend the shared profile where required.

### Basic Company Information
| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| CompanyLogo | No | HR / Payroll branding | Max file size 5MB; .png, .jpg, .jpeg | Uploadable logo for display purposes |
| CompanyName | Yes | HR / Payroll / FinTech | Max length 120; allowed characters alphanumeric + “-.,_+'&#@” | Legal name of the company |
| TradeName | No | FinTech | String | Alternate trading name |
| BusinessRegistrationNumber | No | FinTech | String | Official business registration number |
| DateOfRegistration | No | FinTech | String | Date of registration or incorporation |
| CompanyCode | Yes | HR internal reference | String | Internal company identifier |
| CompanyType | Yes | HR / Payroll / FinTech | Enum: Main, Conglomerate, SisterCompany | Classification of the company |
| Nature of Business / Industry | No | HR / Payroll / FinTech | Max length 120 | Combined field for industry or business type; merges legacy NatureOfBusiness and Industry fields |
| SubIndustry | No | HR | String; max 255 chars | Sub-industry classification; free-text input |
| AccountType | Yes (required on new account creation; editable after) | HR / Super Admin | Enum: Single Company, Group of Companies, Franchise Network, Managed Client Network, Shared Services Setup, Enterprise / Conglomerate, Partner Organization, Custom Structure | Classifies the company's organizational structure; drives Related Companies section visibility on the company profile page |

### Registered Address
| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| Address | Yes | HR / Payroll | Max length 255 | Registered business address line 1 |
| AddressOthers | No | HR / Payroll | Max length 255 | Additional address details, landmarks, floor/unit numbers |
| City | Yes | HR / Payroll | Max length 100 | City or municipality |
| Province | Yes | HR / Payroll | Max length 100 | Province or state |
| Region | Yes | HR / Payroll | Max length 100 | Administrative region, e.g., NCR |
| Country | Yes | HR / Payroll | Max length 50 | Country of registration |
| PostalCode | No | HR / Payroll | Max length 4 | ZIP or postal code |

### Other Address
| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| Other Address Line 1 | Yes | HR | String | Secondary or alternate address line 1 |
| Other Address Line 2 | No | HR | String | Secondary or alternate address line 2 |
| Other Address City | Yes | HR | String | City / municipality for secondary address |
| Other Address Province | Yes | HR | String | Province or state for secondary address |
| Other Address Region | No | HR | String | Administrative region for secondary address |
| Other Address Country | Yes | HR | String | Country for secondary address |
| Other Address Postal Code | No | HR | String | ZIP or postal code for secondary address |

### Contact Information
| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| ContactNumber | Yes | HR | Contact list JSON | Business phone; one per type; one primary per type |
| OtherContactNumber | No | HR | String | Alternate business phone |
| Fax | No | HR | String | Optional fax number |
| Email | Yes | HR | String | Primary contact email; stored in contact list |
| OtherEmailAddress | No | HR | String | Secondary email address |
| Website | No | HR | String | Company website URL |
| Notes | No | HR | Text | Internal HR notes about the company profile |

### Government Information
| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| RDO | No | Payroll | Max length 5 | Revenue District Office code for BIR reports |
| TIN | Yes | Payroll | Max length 13 | Tax Identification Number |
| SSSNumber | Yes | Payroll | Max length 15 | SSS Employer ID |
| PhilHealthNumber | Yes | Payroll | Max length 20 | PhilHealth Employer Number |
| PagibigNumber | Yes | Payroll | Max length 20 | Pag-IBIG Employer ID |

### KYB — Ultimate Beneficial Owner (UBO)

*New sub-section added to the existing company profile page. All fields optional.*

#### Personal Information
| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| UBO Full Name | No | FinTech / KYB | String; max 255 chars | Full legal name of the UBO |
| UBO Date of Birth | No | FinTech / KYB | Date; MM/DD/YYYY; if provided, must be a valid past date and indicate person ≥18 years old | Date of birth of the UBO |
| UBO Nationality | No | FinTech / KYB | String; max 100 chars | Nationality of the UBO |
| UBO Gov't ID Type | No | FinTech / KYB | Dropdown; PH government-issued ID types | Type of government-issued ID presented |
| UBO Gov't ID | No | FinTech / KYB | Image; PNG, JPG, JPEG; max 5MB | Scanned or photographed government-issued ID |

#### Contact Information
| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| UBO Email | No | FinTech / KYB | Valid email format (RFC 5322) if provided | Contact email of the UBO |
| UBO Mobile Number | No | FinTech / KYB | PH format: 11 digits starting with 09, or +639XXXXXXXXX if provided | Mobile number of the UBO |

#### Residential Address
| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| UBO Address Line 1 | No | FinTech / KYB | String; max 255 chars | Primary address line |
| UBO Address Line 2 | No | FinTech / KYB | String; max 255 chars | Secondary address detail |
| UBO City / Town | No | FinTech / KYB | String; max 100 chars | City or municipality |
| UBO Province | No | FinTech / KYB | String; max 100 chars | Province |
| UBO Region | No | FinTech / KYB | String; max 100 chars | PH administrative region |
| UBO Country | No | FinTech / KYB | String; max 100 chars | Country |
| UBO Zipcode | No | FinTech / KYB | String; max 4 chars (PH postal code) | Postal code |

---

### KYB — Authorized Signatory

*New sub-section added to the existing company profile page. Identical field structure to UBO. All fields optional. A "Same as UBO" checkbox auto-populates AS fields with current UBO values at the time of selection; subsequent UBO changes do not propagate to the AS record.*

#### Personal Information
| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| AS Full Name | No | FinTech / KYB | String; max 255 chars | Full legal name of the Authorized Signatory |
| AS Date of Birth | No | FinTech / KYB | Date; MM/DD/YYYY; if provided, must be a valid past date and indicate person ≥18 years old | Date of birth |
| AS Nationality | No | FinTech / KYB | String; max 100 chars | Nationality |
| AS Gov't ID Type | No | FinTech / KYB | Dropdown; PH government-issued ID types | Type of government-issued ID |
| AS Gov't ID | No | FinTech / KYB | Image; PNG, JPG, JPEG; max 5MB | Scanned or photographed government-issued ID |

#### Contact Information
| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| AS Email | No | FinTech / KYB | Valid email format (RFC 5322) if provided | Contact email |
| AS Mobile Number | No | FinTech / KYB | PH format: 11 digits starting with 09, or +639XXXXXXXXX if provided | Mobile number |

#### Residential Address
| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| AS Address Line 1 | No | FinTech / KYB | String; max 255 chars | Primary address line |
| AS Address Line 2 | No | FinTech / KYB | String; max 255 chars | Secondary address detail |
| AS City / Town | No | FinTech / KYB | String; max 100 chars | City or municipality |
| AS Province | No | FinTech / KYB | String; max 100 chars | Province |
| AS Region | No | FinTech / KYB | String; max 100 chars | PH administrative region |
| AS Country | No | FinTech / KYB | String; max 100 chars | Country |
| AS Zipcode | No | FinTech / KYB | String; max 4 chars (PH postal code) | Postal code |

---

### Document Requirements — Certificate of Registration

*New sub-section added to the existing company profile page. All uploads optional. Users upload only the documents applicable to their company's legal entity type.*

| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| SEC Certificate | No | FinTech / KYB | Binary; PDF, PNG, JPG, JPEG; max 10MB | For corporations and partnerships |
| DTI Certificate | No | FinTech / KYB | Binary; PDF, PNG, JPG, JPEG; max 10MB | For sole proprietorships |
| CDA Certificate | No | FinTech / KYB | Binary; PDF, PNG, JPG, JPEG; max 10MB | For cooperatives |
| Articles of Incorporation / Partnership | No | FinTech / KYB | Binary; PDF, PNG, JPG, JPEG; max 10MB | Corporate or partnership formation document |
| General Information Sheet (GIS) | No | FinTech / KYB | Binary; PDF, PNG, JPG, JPEG; max 10MB | For corporations |
| Mayor's / Business Permit | No | FinTech / KYB | Binary; PDF, PNG, JPG, JPEG; max 10MB | Local government business permit |
| Board Resolution or Secretary's Certificate | No | FinTech / KYB | Binary; PDF, PNG, JPG, JPEG; max 10MB | Corporate authorization document |

### Account Structure — Related Companies

*New section added to the company profile page. Account Type is visible and editable by HR Administrator and Super Admin roles only. The Related Companies section is conditionally visible when Account Type ≠ Single Company. Neither is gated by Embedded Finance product.*

#### Company Record

When a related company does not yet exist in the system, a lightweight company profile is created (Scenario B) covering Basic Company Information, Business / Registration Details, and Address / Contact Details only. KYB / compliance sections are deferred to the new company's own profile after creation.

| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| Company Name | Yes | HR / Super Admin | String; max 255 chars | Legal name of the related company |
| Account Type | Yes (required on creation) | HR / Super Admin | Enum: same 8 values as main company | Independent classification — not inherited from the current company's Account Type |
| Country | Yes | HR / Super Admin | Searchable dropdown | Country of the related company |
| TIN / Registration No. | No | HR / Super Admin | String | Tax identification or registration number; displayed as secondary info on the company record |
| Address | No | HR / Super Admin | Multi-field; LocationIQ autocomplete (existing integration) | Address Line 1, City/Municipality, Province/State, Country, Postal Code |
| Website URL | No | HR / Super Admin | String | Website URL; "Same as [Current Company]" checkbox available on creation — one-time copy, not a live sync |

#### Relationship Record

| Field Name | Required | Source / Usage | Validation | Description |
|---|---|---|---|---|
| Related Company | Yes | HR / Super Admin | Reference to Company record | The company being linked to the current company |
| Relationship Type | Yes | HR / Super Admin | Enum: 13 options; filtered by Account Type | How the related org connects to the current company; reads as "[Related Org] [Relationship Type] [Current Company]" |
| Effectivity Date | Yes | HR / Super Admin | Date; MM/DD/YYYY; past or future | Date the relationship takes effect |
| End Date | No | HR / Super Admin | Date; MM/DD/YYYY; if provided, must be ≥ Effectivity Date | Date the relationship formally ends; blank if open-ended |
| Notes / Details | No | HR / Super Admin | Multi-line text | Additional context; used for incorrect or lapsed relationships |
| Status | Yes | HR / Super Admin | Enum: Draft, Pending, Active, Suspended, Inactive, Ended | Current state of the relationship link; defaults to Pending on creation |

**Relationship Type options (13):** Parent Company, Subsidiary Of, Satellite Of, Affiliate Of, Franchise Of, Joint Venture Of, Manages, Managed By, Partner Of, Shared Services With, Client Of, Supports, Other

**Relationship record lifecycle:** Records are never hard-deleted. Status = Ended (with End Date) for formally concluded relationships. Status = Inactive for incorrect entries, with context in Notes / Details.

#### Incoming Relationships

When another company links this organization as a related org, those links are visible on this organization's own company profile as a read-only **Incoming Relationships** section. Visible to HR Administrator and Super Admin. Displays: Main Company Name, Relationship Type, Effectivity Date, Status. No edit controls — relationship records can only be managed from the originating company's profile. Visible regardless of this organization's own Account Type, including when Account Type = Single Company. Section is hidden if no incoming relationships exist.

---

## Phased Scope
### Phase 1 — Unified Shared Company Profile Workspace
- Create a shared company profile workspace accessible from both HR and Payroll front-ends for a single company account.
- Migrate the most important shared fields into the unified workspace: company name, legal entity details, employer registration numbers, company address, payroll calendar parameters, statutory employer contribution settings, and primary payroll mappings.
- Implement role-specific field visibility and edit permissions for HR Admin and Payroll Admin.
- Enforce validation rules for required fields and field formats before allowing save.
- Add read-only indicators for payroll-only and HR-only fields when viewed by the other persona.
- Add audit logging for all profile changes, with special attention to TIN, SSS, PhilHealth, and Pag-IBIG numbers.
- Add new sub-sections to the company profile page for KYB compliance: Ultimate Beneficial Owner (UBO) and Authorized Signatory, each capturing personal information, contact information, and residential address. All fields in both sub-sections are optional.
- Support a "Same as UBO" option on the Authorized Signatory sub-section that copies current UBO values into AS fields at the time of selection; subsequent UBO changes do not propagate automatically to the AS record.
- Support image uploads for UBO and Authorized Signatory Government-issued ID fields (PNG, JPG, JPEG; max 5MB) and file uploads for the seven Certificate of Registration document fields (PDF, PNG, JPG, JPEG; max 10MB).
- Phase 1 supports a single UBO record and a single Authorized Signatory record per company. Multi-UBO and multi-AS support (applicable when a company has multiple beneficial owners each holding ≥25% of shares) is a known Phase 1 simplification and deferred to a future phase.
- Add **Account Type** field to Basic Company Information — required on new account creation, always editable by HR Administrator and Super Admin. Account Type drives the conditional visibility of the Related Companies section: Single Company (and blank on existing accounts) shows no additional section; all other values reveal the Related Companies section.
- Add **Related Companies** section — conditionally visible when Account Type ≠ Single Company. The Add Company entry point shows a **domain-scoped list** of all companies under the same Sprout HR tenant, searchable by company name (Scenario A — select an existing company from the list). A "Create New Company" option is available alongside the list and also when the search returns no results (Scenario B — create via lightweight form then link; accessible directly without requiring a search first). Only organizations within the same Sprout HR tenant are shown in the list. Scenario B's lightweight form covers Basic Company Information (including Account Type, independent of the current company's), Business / Registration Details, and Address / Contact Details only. KYB / compliance sections are deferred to the new company's own profile.
- Add **Relationship record** model — each linked organization generates a distinct Relationship record (separate from the Company record) with Relationship Type (13 options, filtered by Account Type), Effectivity Date (required), End Date (optional, must be ≥ Effectivity Date), Notes / Details (optional), and Status (Draft / Pending / Active / Suspended / Inactive / Ended; defaults to Pending on creation). Relationship records are never deleted. Status = Inactive handles incorrect entries; Status = Ended handles formal conclusions.
- Add **Incoming Relationships** section — read-only section visible on any company profile where another company has created a relationship record linking to it. Displays: Main Company Name, Relationship Type, Effectivity Date, Status. No edit controls. Visible regardless of the company's own Account Type. Hidden when no incoming relationships exist.

### Phase 2 — Extended Company Profile Consolidation
- Enable creation and management of multiple companies in one account.
- Expand the unified workspace to include additional module-specific company settings such as payroll cost center defaults, statutory branch mappings, and HR reporting identifiers.
- Add reconciliation status indicators that surface misalignments between HR and Payroll company settings in real time.
- Support guided migration for existing company profile data from the separate HR and Payroll systems into the unified model.

### Phase 3 — Shared Company Profile Governance
- Introduce approval workflows for high-risk company profile changes (for example, statutory employer contribution updates).
- Enable version history and rollback for company profile records.
- Surface cross-module impact analysis when a shared field is updated.

## Success Metrics
- Shared company profile data duplication reduced by 100% for the fields migrated into the unified workspace.
- Company profile reconciliation incidents reported by HR and Payroll Admins reduced by at least 50% in the first quarter after launch.
- At least 80% of HR and Payroll Admins use the unified company profile workspace for shared employer settings within 12 weeks of availability.
- 100% of shared company profile fields are consistent across HR and Payroll modules as measured by automated synchronization checks.

## User Stories & Requirements
### Functional Requirements
- Create/Update Company Profile — Only users with appropriate roles can create or update the company profile.
- Validation Enforcement — All required fields must pass their respective validation rules before saving.
- Contact Info Rules — The system must prevent duplicate contact entries and prevent multiple entries marked as Primary for the same type.
- Upload Logo — Users can upload a logo image within file size and format restrictions.
- Audit Trail — Track changes for fields like TIN, SSS, PhilHealth, and Pag-IBIG Numbers.
- Single Company Support — Phase 1 must support a single company only (one company account per tenant).
- Account Type Classification — Account Type must be selected when creating a new company account; always editable after by HR Administrator and Super Admin.
- Related Companies — When Account Type ≠ Single Company, administrators can link related companies via two paths: Scenario A (domain-scoped list showing all companies under the same Sprout HR tenant, searchable by company name, to find and link an existing org) or Scenario B (create a new org via lightweight form — accessible directly without searching the list first, or when the list search returns no results). Relationship records are never deleted.
- Incoming Relationships — Any company linked by another company can view those links as read-only in an Incoming Relationships section on their own company profile, visible regardless of their own Account Type.
- Relationship Type Filtering — Available Relationship Type options are filtered based on the current company's Account Type per the defined mapping.
- Relationship Record Integrity — Status = Inactive for incorrect entries; Status = Ended for formally concluded relationships.
- Multiple Company Support — Phase 2 must enable creation and management of multiple companies in one account.

### User Stories
1. **HR Admin manages shared employer data in one place.**
   - As an HR Admin, I want to update company metadata and employer settings in a unified company profile workspace so I do not have to enter the same information in separate HR and Payroll systems.
   - Acceptance criteria:
     - The unified workspace displays shared company profile fields in a single interface.
     - Changes to shared fields update the canonical company profile and are reflected in both HR and Payroll modules.

2. **Payroll Admin verifies payroll-relevant company settings.**
   - As a Payroll Admin, I want to review payroll-specific employer settings and see whether they are aligned with HR profile data so I can trust payroll inputs.
   - Acceptance criteria:
     - Payroll Admins can access the unified company profile workspace with a payroll-focused view.
     - Fields relevant only to payroll are clearly labeled and editable by Payroll Admins.

3. **System prevents conflicting edits across domains.**
   - As an administrator, I want the system to show when a shared field is being edited or differs from the current payroll view so I can avoid inconsistent company profile data.
   - Acceptance criteria:
     - The workspace surfaces reconciliation indicators for shared fields.
     - If the same shared field exists in HR and Payroll modules, the unified front-end displays the canonical value and any pending updates.

5. **HR Admin or Super Admin classifies account structure and links related companies.**
   - As an HR Admin or Super Admin, I want to classify my company's account structure type and record the companies it is connected to, so that the system reflects the real-world organizational structure of our client accounts.
   - Acceptance criteria:
     - Account Type dropdown is present in Basic Company Information; required on new account creation; always editable after by HR Administrator and Super Admin.
     - Related Companies section is visible when Account Type ≠ Single Company.
     - Add Company entry point shows a domain-scoped list of companies under the same Sprout HR tenant, searchable by company name (Scenario A), and a "Create New Company" option available alongside the list and when the search returns no results (Scenario B — accessible directly without requiring a search first).
     - Each linked company has a Relationship record with Relationship Type, Effectivity Date, End Date, Notes / Details, and Status (defaulting to Pending on creation).
     - Relationship records are never deleted; Status = Ended for formal conclusions, Status = Inactive for incorrect entries.
     - Companies linked by other companies can view those incoming links as read-only in an Incoming Relationships section on their own company profile.

4. **Audit trail captures company profile changes.**
   - As a Compliance Reviewer, I want every company profile change to be logged with actor, timestamp, and field-level details so I can trace who changed employer settings.
   - Acceptance criteria:
     - All updates to shared company profile fields generate audit events.
     - Audit events include actor identity, affected field, old value, new value, and timestamp.

## Dependencies & Risks
### Dependencies
- Current HR and Payroll company profile data models and field definitions.
- Existing HR and Payroll profile front-end flows, including the designs and data structure documented in the provided Atlassian/Wiki, spreadsheet, and Figma artifacts.
- Role-based access control infrastructure for HR and Payroll personas.
- Audit logging and change-tracking infrastructure. Note: audit logging for company profile fields is not yet implemented. A dedicated implementation ticket is required before Phase 1 can be considered complete on this requirement.
- Synchronization layer or business logic that persists shared field changes to both HR and Payroll systems.
- **PH Data Privacy Act (RA 10173) compliance:** UBO and Authorized Signatory sub-sections collect sensitive personal information (full name, date of birth, nationality, government ID images). Data handling must comply with NPC requirements — including lawful basis for collection, data subject consent, purpose limitation, and retention policy. Legal or Compliance review is required before these sub-sections go live.

### Risks
- **Incomplete field alignment:** The unified front-end may omit fields currently required by HR or Payroll, causing administrators to continue using separate systems.
- **Ownership ambiguity:** Unclear product ownership of shared company profile fields may lead to conflicting requirements from HR and Payroll stakeholders.
- **Data migration complexity:** Existing HR and Payroll company profile records may diverge, making migration into a single canonical workspace risky without a clear reconciliation plan.
- **User confusion:** If role-specific views are not clearly differentiated, HR and Payroll Admins may not understand which fields they are responsible for.
- **Audit logging implementation gap:** Phase 1 scope requires audit logging for all company profile changes, but audit logging for company profile is not yet implemented in the current system. The KYB/UBO/AS ticket explicitly excludes audit logging as out of scope. Without a follow-on implementation ticket, Phase 1 will ship without audit coverage for the new fields — contradicting the Phase 1 requirement.
- **Data privacy exposure:** UBO and AS sub-sections collect highly sensitive PII. Shipping without NPC-compliant data handling (consent, purpose limitation, retention) creates regulatory risk under RA 10173. Legal/Compliance sign-off is required before launch.

## Open Questions
- Which company profile fields are currently managed exclusively by HR, exclusively by Payroll, and shared across both?
- What is the expected ownership model for the unified company profile: HR-led, Payroll-led, or co-owned?
- Should payroll-only employer settings remain editable by Payroll Admin only, or should HR Admins have a read-only view for awareness?
- What is the exact migration path for existing company profile data from the separate HR and Payroll systems?
- Are there custom client-specific company profile fields that must be supported in phase 1?
- **Where should the unified Company Profile be housed?** HR and Payroll run on different URLs. Options: (1) neutral shared admin/settings URL accessible from both modules — recommended, avoids either module appearing to own a cross-domain resource; (2) hosted in one module with the other deep-linking — simpler but creates a module ownership perception problem. To be discussed during alignment with Design and Engineering teams.
- **KYB sub-sections visibility (resolved):** The Know Your Customer — UBO, Know Your Customer — Authorized Signatory, and Document Requirements — Certificate of Registration sub-sections are visible only to accounts with Embedded Finance (FinTech) products enabled. Basic Company Information additions (Trade Name, BRN, Date of Registration, Sub-Industry) are not gated.
- **Access control for KYB fields:** Pending alignment with the Embedded Finance team to determine which persona(s) are responsible for managing KYB/UBO/AS data.

---
