# EP-02 / EP-03 Wireframes — Sprint 2 Screens
### 9 screens [NEW] + 1 screen [MODIFIED]

---

## Screen E: Personal Details (/apply/{productId}/personal)
**Security**: FLAG_SECURE enabled (PII screen)
**API**: POST /api/v1/applications/{productId}/personal (auto-save), GET /api/v1/applications/{productId}/personal (prefill)
**Story**: US-015

```
┌──────────────────────────────────────┐
│  ←  Apply for BridgeNow       ░░░░  │  [A11Y: back="Back to product detail"]
├──────────────────────────────────────┤
│                                      │
│  ● ─── ○ ─── ○ ─── ○                │  [NEW] Stepper: Step 1 of 4
│  Personal  Employ  Income  Review    │  [A11Y: "Step 1 of 4, Personal Details"]
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 👤  Full Name                │    │  [NEW] TasheelTextField
│  │     e.g. Mohammed Al-Rashid  │    │  [A11Y: label="Full name", 48dp target]
│  └──────────────────────────────┘    │  Validation: required, min 2 chars
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 🆔  National ID              │    │  [NEW] TasheelTextField (masked)
│  │     1XXX XXXX XX             │    │  [A11Y: label="National ID number", 48dp]
│  └──────────────────────────────┘    │  Validation: 10 digits, masked display
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 📅  Date of Birth            │    │  [NEW] TasheelDatePicker
│  │     DD / MM / YYYY           │    │  [A11Y: label="Date of birth", 48dp]
│  └──────────────────────────────┘    │  Opens date picker dialog
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 🏠  Address                  │    │  [NEW] TasheelTextField
│  │     Street address           │    │  [A11Y: label="Street address", 48dp]
│  └──────────────────────────────┘    │  Validation: required
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 🏙️  City                     │    │  [NEW] TasheelTextField
│  │     e.g. Riyadh              │    │  [A11Y: label="City", 48dp target]
│  └──────────────────────────────┘    │  Validation: required
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 📍  Region              ▼   │    │  [NEW] TasheelDropdown
│  │     Select region            │    │  [A11Y: label="Region", role=dropdown]
│  └──────────────────────────────┘    │  Options: Saudi regions list
│                                      │
│  ☁️ Auto-saved                       │  [NEW] Auto-save indicator
│                                      │  Typography: bodySmall, Neutral-400
│                                      │  [A11Y: liveRegion="polite"]
│                                      │
│  ┌──────────────────────────────┐    │
│  │        [ NEXT ]              │    │  [NEW] TasheelButton Primary
│  └──────────────────────────────┘    │  [A11Y: "Next, go to employment details"]
│                                      │  Disabled until all required fields valid
│                                      │  48dp height, full width
│                                      │
│  Error state:                        │
│  ┌──────────────────────────────┐    │
│  │ ⚠️ Please fill required fields│    │  Error text, color: Error (#E74C3C)
│  └──────────────────────────────┘    │
│                                      │
└──────────────────────────────────────┘
```

**TalkBack order**: Back → Stepper → Full Name → National ID → Date of Birth → Address → City → Region → Auto-save status → Next → Error (if shown)
**Dynamic text**: All text scales with system font size
**States**: Default | Field focused | Validating | Error (per-field + form) | Loading (auto-save spinner) | Success (auto-save confirmed)

---

## Screen F: Employment Details (/apply/{productId}/employment)
**Security**: Standard (no FLAG_SECURE)
**API**: POST /api/v1/applications/{productId}/employment (save), GET /api/v1/employers (employer list)
**Story**: US-016

```
┌──────────────────────────────────────┐
│  ←  Apply for BridgeNow       ░░░░  │  [A11Y: back="Back to personal details"]
├──────────────────────────────────────┤
│                                      │
│  ○ ─── ● ─── ○ ─── ○                │  Stepper: Step 2 of 4
│  Personal  Employ  Income  Review    │  [A11Y: "Step 2 of 4, Employment Details"]
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 🏢  Employer Name            │    │  [NEW] TasheelTextField (autocomplete)
│  │     e.g. Saudi Aramco        │    │  [A11Y: label="Employer name", 48dp]
│  └──────────────────────────────┘    │  Validation: required
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 💼  Employment Type     ▼   │    │  [NEW] TasheelDropdown
│  │     Select type              │    │  [A11Y: label="Employment type", role=dropdown]
│  └──────────────────────────────┘    │  Options: Employed / Self-Employed / Retired
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 📅  Employment Start Date    │    │  [NEW] TasheelDatePicker
│  │     MM / YYYY                │    │  [A11Y: label="Employment start date", 48dp]
│  └──────────────────────────────┘    │  Opens date picker dialog
│                                      │
│  ┌──────────────────────────────┐    │
│  │ ℹ️ Employment will be         │    │  [NEW] Info banner
│  │   verified via CITC          │    │  Background: Primary-subtle (#EEF1FE)
│  └──────────────────────────────┘    │  Typography: bodySmall, Neutral-600
│                                      │  [A11Y: liveRegion="polite"]
│                                      │
│  ┌─────────────┐ ┌──────────────┐    │
│  │   [ BACK ]   │ │   [ NEXT ]   │    │  TasheelButton Outlined / Primary
│  └─────────────┘ └──────────────┘    │  [A11Y: "Back to personal" / "Next, income"]
│                                      │  48dp height each
│                                      │
│  Error state:                        │
│  ┌──────────────────────────────┐    │
│  │ ⚠️ Please fill required fields│    │  Error text, color: Error (#E74C3C)
│  └──────────────────────────────┘    │
│                                      │
└──────────────────────────────────────┘
```

**TalkBack order**: Back → Stepper → Employer Name → Employment Type → Start Date → CITC info → Back button → Next button → Error (if shown)
**Dynamic text**: All text scales with system font size
**States**: Default | Field focused | Validating | Error (per-field + form) | Loading (save spinner) | Success (navigates to step 3)

---

## Screen G: Income & Salary (/apply/{productId}/income)
**Security**: FLAG_SECURE enabled (income data)
**API**: GET /api/v1/applications/{productId}/income (CITC/API prefill), POST /api/v1/applications/{productId}/income (save), POST /api/v1/open-banking/connect (Open Banking)
**Story**: US-017, US-020, US-021

```
┌──────────────────────────────────────┐
│  ←  Apply for BridgeNow       ░░░░  │  [A11Y: back="Back to employment details"]
├──────────────────────────────────────┤
│                                      │
│  ○ ─── ○ ─── ● ─── ○                │  Stepper: Step 3 of 4
│  Personal  Employ  Income  Review    │  [A11Y: "Step 3 of 4, Income and Salary"]
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 💰  Monthly Income           │    │  [NEW] TasheelTextField
│  │     SAR 12,000               │    │  [A11Y: label="Monthly income in SAR"]
│  │     🔒 Verified via API      │    │  Read-only if API-populated
│  └──────────────────────────────┘    │  Editable if manual entry
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 💵  Gross Income             │    │  [NEW] TasheelTextField
│  │     SAR 14,500               │    │  [A11Y: label="Gross income in SAR"]
│  │     🔒 Verified via API      │    │  Read-only if API-populated
│  └──────────────────────────────┘    │  Editable if manual entry
│                                      │
│  ┌──────────────────────────────┐    │
│  │ Income Source:                │    │  [NEW] Income source indicator
│  │  ● API  ○ Open Banking       │    │  [A11Y: "Income source: API verified"]
│  │  ○ Manual                    │    │  Read-only indicator, not selectable
│  └──────────────────────────────┘    │  Badge: Success if API/OB, Warning if Manual
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 🏦 [ Connect Bank Account ] │    │  [NEW] TasheelButton Outlined
│  └──────────────────────────────┘    │  [A11Y: "Connect bank account via Open Banking"]
│                                      │  Shown for unlisted companies
│                                      │  48dp height, full width
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 📅  Salary Date              │    │  [NEW] TasheelDropdown (1–28)
│  │     Select day (1–28)   ▼   │    │  [A11Y: label="Salary date, day of month"]
│  └──────────────────────────────┘    │  Validation: required, 1–28
│                                      │
│  ┌─────────────┐ ┌──────────────┐    │
│  │   [ BACK ]   │ │   [ NEXT ]   │    │  TasheelButton Outlined / Primary
│  └─────────────┘ └──────────────┘    │  [A11Y: "Back to employment" / "Next, review"]
│                                      │  48dp height each
│                                      │
│  Error state:                        │
│  ┌──────────────────────────────┐    │
│  │ ⚠️ Unable to fetch income     │    │  Error text, color: Error (#E74C3C)
│  │   [ Retry ] or enter manually│    │  Retry + manual fallback
│  └──────────────────────────────┘    │
│                                      │
└──────────────────────────────────────┘
```

**TalkBack order**: Back → Stepper → Monthly Income → Gross Income → Income Source → Connect Bank Account → Salary Date → Back button → Next button → Error (if shown)
**Dynamic text**: All text scales with system font size
**States**: Default | Loading (fetching income from API) | API-populated (read-only fields) | Manual entry (editable fields) | Open Banking connecting | Error (API fail + retry) | Success (navigates to step 4)

---

## Screen H: Review & Submit (/apply/{productId}/review)
**Security**: FLAG_SECURE enabled (PII + financial data)
**API**: GET /api/v1/applications/{productId}/summary, GET /api/v1/applications/{productId}/offer-preview, POST /api/v1/applications/{productId}/submit
**Story**: US-018, US-019, US-022

```
┌──────────────────────────────────────┐
│  ←  Apply for BridgeNow       ░░░░  │  [A11Y: back="Back to income details"]
├──────────────────────────────────────┤
│                                      │
│  ○ ─── ○ ─── ○ ─── ●                │  Stepper: Step 4 of 4
│  Personal  Employ  Income  Review    │  [A11Y: "Step 4 of 4, Review and Submit"]
│                                      │
│  Personal Details            [Edit]  │  [NEW] TasheelCard summary
│  ┌──────────────────────────────┐    │  [A11Y: Edit="Edit personal details"]
│  │ Name:    Mohammed Al-Rashid  │    │
│  │ ID:      1XXX XXXX XX       │    │  National ID masked
│  │ DOB:     15/03/1990         │    │
│  └──────────────────────────────┘    │
│                                      │
│  Employment                  [Edit]  │  [NEW] TasheelCard summary
│  ┌──────────────────────────────┐    │  [A11Y: Edit="Edit employment details"]
│  │ Employer: Saudi Aramco      │    │
│  │ Type:     Employed          │    │
│  └──────────────────────────────┘    │
│                                      │
│  Income                      [Edit]  │  [NEW] TasheelCard summary
│  ┌──────────────────────────────┐    │  [A11Y: Edit="Edit income details"]
│  │ Monthly:  SAR 12,000        │    │
│  │ Gross:    SAR 14,500        │    │
│  │ Source:   API Verified ✅    │    │
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 💰 Estimated offer:          │    │  [NEW] Offer preview card
│  │    SAR XX,XXX               │    │  Background: Primary-subtle (#EEF1FE)
│  │    (calculated server-side)  │    │  Typography: headlineMedium, Primary
│  └──────────────────────────────┘    │  [A11Y: "Estimated offer amount, SAR"]
│                                      │  Read-only, not editable
│                                      │
│  ┌──────────────────────────────┐    │
│  │ ☐ I confirm the information │    │  [NEW] TasheelCheckbox
│  │   is accurate                │    │  [A11Y: "Confirm information is accurate"]
│  └──────────────────────────────┘    │  Required before submit, 48dp target
│                                      │
│  ┌──────────────────────────────┐    │
│  │      [ SUBMIT APPLICATION ]  │    │  [NEW] TasheelButton Primary
│  └──────────────────────────────┘    │  [A11Y: "Submit loan application"]
│                                      │  Disabled until checkbox checked
│                                      │  48dp height, full width
│                                      │
│  Error state:                        │
│  ┌──────────────────────────────┐    │
│  │ ⚠️ Submission failed. Retry? │    │  Error text, color: Error (#E74C3C)
│  └──────────────────────────────┘    │
│                                      │
└──────────────────────────────────────┘
```

**TalkBack order**: Back → Stepper → Personal card (Name, ID, DOB) → Edit personal → Employment card → Edit employment → Income card → Edit income → Offer preview → Checkbox → Submit → Error (if shown)
**Dynamic text**: All text scales with system font size
**States**: Default | Loading (fetching summary + offer preview) | Error (submit fail + retry) | Success (navigates to application status)

---

## Screen I: Application Status (/application/{id}/status)
**Security**: Standard (no FLAG_SECURE)
**API**: GET /api/v1/applications/{id}/status (poll / pull-to-refresh)
**Story**: US-023, US-024, US-025, US-026, US-027, US-028

```
┌──────────────────────────────────────┐
│  ←  Application Status        ░░░░  │  [A11Y: back="Back to products"]
├──────────────────────────────────────┤
│                                      │
│  ↻ Pull to refresh                   │  [A11Y: "Pull down to refresh status"]
│                                      │
│  Application #BN-20260427-001       │  Typography: bodySmall, Neutral-400
│                                      │
│  Verification Progress               │  Typography: titleMedium (16sp)
│                                      │
│  ✅ ── Employment Verification       │  [NEW] Vertical timeline stepper
│  │    (CITC)                         │  Status: Complete
│  │    Verified · 10:30 AM           │  Timestamp shown when complete
│  │                                   │  [A11Y: "Employment verification, complete"]
│  │                                   │
│  ⏳ ── Location Verification         │  Status: In Progress
│  │    Verifying...                   │  [A11Y: "Location verification, in progress"]
│  │                                   │
│  ○ ── Credit Check (SIMAH)          │  Status: Pending
│  │    Waiting                        │  [A11Y: "Credit check, pending"]
│  │                                   │
│  ○ ── Decision                       │  Status: Pending
│       Waiting                        │  [A11Y: "Decision, pending"]
│                                      │
│  ─────────────────────────────────   │
│                                      │
│  Decision states (mutually exclusive):│
│                                      │
│  Approved:                           │
│  ┌──────────────────────────────┐    │
│  │ ✅ Congratulations!           │    │  Background: Success-subtle
│  │   Your application has been  │    │  Auto-navigates to Offer screen
│  │   approved.                  │    │  [A11Y: liveRegion="assertive"]
│  └──────────────────────────────┘    │
│                                      │
│  Referred:                           │
│  ┌──────────────────────────────┐    │
│  │ ⚠️ Your application is under │    │  Background: Warning-subtle (#FEF3E2)
│  │   review                     │    │  [A11Y: liveRegion="assertive"]
│  └──────────────────────────────┘    │
│                                      │
│  Declined:                           │
│  ┌──────────────────────────────┐    │
│  │ ❌ Unfortunately your         │    │  Background: Error-subtle (#FDECEC)
│  │   application was not        │    │  [A11Y: liveRegion="assertive"]
│  │   approved.                  │    │
│  │                               │    │
│  │   Reason: [decline reason]   │    │  Typography: bodySmall, Neutral-600
│  │                               │    │
│  │   [ Back to Products ]       │    │  TasheelButton Outlined
│  └──────────────────────────────┘    │  [A11Y: "Return to product list"]
│                                      │
│  Loading: [Skeleton] timeline       │  LoadingSkeleton component
│  Error: [ErrorState + Retry]        │  ErrorState component
│                                      │
└──────────────────────────────────────┘
```

**TalkBack order**: Back → Application ID → Title → Step 1 (Employment + status + timestamp) → Step 2 (Location + status) → Step 3 (Credit + status) → Step 4 (Decision + status) → Decision banner (if shown)
**Dynamic text**: All text scales with system font size
**States**: Default (polling) | Loading (skeleton) | In-progress (steps updating) | Approved (auto-navigate to offer) | Referred (under review banner) | Declined (reason + back button) | Error (network fail + retry)

---

## Screen J: Offer Display (/application/{id}/offer)
**Security**: FLAG_SECURE enabled (financial data)
**API**: GET /api/v1/applications/{id}/offer, POST /api/v1/applications/{id}/offer/accept, POST /api/v1/applications/{id}/offer/decline
**Story**: US-029, US-030

```
┌──────────────────────────────────────┐
│  ←  Your Offer                ░░░░  │  [A11Y: back="Back to application status"]
├──────────────────────────────────────┤
│                                      │
│  ┌──────────────────────────────┐    │
│  │                               │    │  [NEW] Hero banner
│  │   ✨ No Early Closure         │    │  Background: Primary-subtle (#EEF1FE)
│  │      Charges                  │    │  Typography: headlineMedium (24sp)
│  │                               │    │  Color: Primary (#4F6EF7)
│  └──────────────────────────────┘    │
│                                      │
│  Offer Details (PD5 PCCI)           │  Typography: titleMedium (16sp)
│  ┌──────────────────────────────┐    │  [NEW] TasheelCard — PD22 compliant
│  │                               │    │  All costs equal prominence (PD22)
│  │ Approved Amount  SAR XX,XXX  │    │  Typography: bodyMedium
│  │ ─────────────────────────── │    │
│  │ Tenure           24 months   │    │  Typography: bodyMedium
│  │ ─────────────────────────── │    │
│  │ Profit Rate      27% p.a.   │    │  Typography: bodyMedium
│  │ ─────────────────────────── │    │
│  │ Monthly Payment  SAR X,XXX  │    │  Typography: bodyMedium
│  │ ─────────────────────────── │    │
│  │ Total Repayment  SAR XX,XXX │    │  Typography: bodyMedium
│  │ ─────────────────────────── │    │
│  │ Admin Fee        SAR XXX    │    │  Typography: bodyMedium
│  │                  (0.5%+VAT) │    │  Typography: bodySmall, Neutral-400
│  │ ─────────────────────────── │    │
│  │ Total Cost of    SAR X,XXX  │    │  Typography: bodyMedium
│  │ Credit                       │    │
│  │                               │    │
│  └──────────────────────────────┘    │  [A11Y: each row announced individually]
│                                      │
│  ┌──────────────────────────────┐    │
│  │     [ ACCEPT OFFER ]        │    │  [NEW] TasheelButton Primary
│  └──────────────────────────────┘    │  [A11Y: "Accept loan offer"]
│                                      │  48dp height, full width
│                                      │
│       [ Decline Offer ]              │  [NEW] TasheelButton Text
│                                      │  [A11Y: "Decline loan offer"]
│                                      │  Typography: bodyMedium, Error color
│                                      │
│  Error state:                        │
│  ┌──────────────────────────────┐    │
│  │ ⚠️ Unable to load offer       │    │  Error text, color: Error (#E74C3C)
│  │   [ Retry ]                  │    │
│  └──────────────────────────────┘    │
│                                      │
└──────────────────────────────────────┘
```

**TalkBack order**: Back → Hero banner → Title → Approved Amount → Tenure → Profit Rate → Monthly Payment → Total Repayment → Admin Fee → Total Cost of Credit → Accept Offer → Decline Offer → Error (if shown)
**Dynamic text**: All text scales with system font size; card rows wrap on large text
**States**: Default | Loading (skeleton card) | Error (load fail + retry) | Accept loading (spinner) | Decline confirmation dialog | Success (navigates to contract)

---

## Screen K: Contract & Signature (/application/{id}/contract)
**Security**: FLAG_SECURE enabled (legal document)
**API**: GET /api/v1/applications/{id}/contract, POST /api/v1/applications/{id}/contract/sign, POST /api/v1/applications/{id}/contract/cancel
**Story**: US-031, US-032

```
┌──────────────────────────────────────┐
│  ←  Contract                  ░░░░  │  [A11Y: back="Back to offer"]
├──────────────────────────────────────┤
│                                      │
│  Tawarruq Finance Agreement         │  Typography: titleLarge (20sp)
│                                      │
│  ┌──────────────────────────────┐    │
│  │                               │    │  [NEW] Scrollable contract area
│  │  Contract text...             │    │  Scrollable container
│  │                               │    │
│  │  Key terms (highlighted):     │    │  Background: Warning-subtle on key terms
│  │  ┌────────────────────────┐  │    │
│  │  │ Amount:   SAR XX,XXX  │  │    │  Highlighted term
│  │  │ Rate:     27% p.a.    │  │    │  Highlighted term
│  │  │ Tenure:   24 months   │  │    │  Highlighted term
│  │  │ Monthly:  SAR X,XXX   │  │    │  Highlighted term
│  │  │ Total:    SAR XX,XXX  │  │    │  Highlighted term
│  │  └────────────────────────┘  │    │
│  │                               │    │
│  │  ...remaining contract text   │    │  [A11Y: "Scrollable contract document"]
│  │                               │    │
│  └──────────────────────────────┘    │
│                                      │
│  E-Signature                         │  Typography: titleMedium (16sp)
│  ┌──────────────────────────────┐    │
│  │ ✍️  Type your full name       │    │  [NEW] TasheelTextField
│  │     Mohammed Al-Rashid       │    │  [A11Y: label="Type full name to sign"]
│  └──────────────────────────────┘    │  Validation: must match applicant name
│                                      │
│  ┌──────────────────────────────┐    │
│  │     [ SIGN CONTRACT ]        │    │  [NEW] TasheelButton Primary
│  └──────────────────────────────┘    │  [A11Y: "Sign contract"]
│                                      │  48dp height, full width
│                                      │  Disabled until name typed
│                                      │
│  ─── After signing: ───────────────  │
│                                      │
│  ┌──────────────────────────────┐    │
│  │ ⏱️ Cooling-off period         │    │  [NEW] Countdown timer card
│  │                               │    │  Background: Warning-subtle (#FEF3E2)
│  │   23 : 45 : 12               │    │  Typography: headlineLarge (32sp)
│  │   HH   MM   SS               │    │  Typography: bodySmall, Neutral-400
│  │                               │    │  [A11Y: liveRegion="polite",
│  │   remaining                   │    │   "23 hours 45 minutes remaining"]
│  └──────────────────────────────┘    │  Updates every second
│                                      │
│       [ Cancel Application ]         │  [NEW] TasheelButton Text
│                                      │  [A11Y: "Cancel application during cooling off"]
│                                      │  Typography: bodyMedium, Error color
│                                      │  Only shown during cooling-off period
│                                      │
│  Error state:                        │
│  ┌──────────────────────────────┐    │
│  │ ⚠️ Signing failed. Retry?    │    │  Error text, color: Error (#E74C3C)
│  └──────────────────────────────┘    │
│                                      │
└──────────────────────────────────────┘
```

**TalkBack order**: Back → Title → Contract text (scrollable) → Key terms → E-signature input → Sign button → Countdown timer (after signing) → Cancel button (during cooling-off) → Error (if shown)
**Dynamic text**: All text scales with system font size; contract text scrollable
**States**: Default (unsigned) | Loading (fetching contract) | Signing (spinner) | Signed (countdown visible, cancel available) | Cooling-off expired (auto-navigate to card collection) | Cancelled (navigate back to products) | Error (sign fail + retry)

---

## Screen L: Card Collection (/application/{id}/card)
**Security**: FLAG_SECURE enabled (card data)
**API**: POST /api/v1/applications/{id}/card (tokenized via hosted fields), POST /api/v1/applications/{id}/3ds-challenge
**Story**: US-033, US-034, US-035

```
┌──────────────────────────────────────┐
│  ←  Card Details              ░░░░  │  [A11Y: back="Back to contract"]
├──────────────────────────────────────┤
│                                      │
│  Add your debit card for             │  Typography: titleMedium (16sp)
│  automatic repayments                │  Typography: bodyMedium, Neutral-600
│                                      │
│  ┌──────────────────────────────┐    │
│  │  Secured by [Processor] 🔒  │    │  [NEW] Hosted fields iframe
│  │                               │    │  Border: Neutral-200
│  │  ┌────────────────────────┐  │    │
│  │  │ 💳  Card Number (PAN)  │  │    │  Hosted field — PCI-DSS compliant
│  │  │     XXXX XXXX XXXX XXXX│  │    │  [A11Y: label="Card number", 48dp]
│  │  └────────────────────────┘  │    │
│  │                               │    │
│  │  ┌──────────┐ ┌──────────┐  │    │
│  │  │ Expiry   │ │ CVV      │  │    │  Hosted fields side-by-side
│  │  │ MM / YY  │ │ •••      │  │    │  [A11Y: "Expiry date" / "CVV"]
│  │  └──────────┘ └──────────┘  │    │  48dp targets each
│  │                               │    │
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 🔒 Card details are processed│    │  [NEW] PCI-DSS note
│  │   securely and never stored  │    │  Typography: bodySmall, Neutral-400
│  │   by BridgeNow               │    │  [A11Y: liveRegion="polite"]
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 📅  Salary Date              │    │  TasheelDropdown (1–28)
│  │     Select day (1–28)   ▼   │    │  [A11Y: label="Salary date, day of month"]
│  └──────────────────────────────┘    │  Shown only if not already captured
│                                      │
│  Auto-Debit Schedule                 │  Typography: titleSmall (14sp)
│  ┌──────────────────────────────┐    │
│  │ Primary:    25th of month    │    │  [NEW] Dual auto-debit summary
│  │ Secondary:  28th of month    │    │  [A11Y: "Primary debit date 25th,
│  │             (Primary + 3)    │    │   Secondary debit date 28th"]
│  └──────────────────────────────┘    │  Typography: bodyMedium
│                                      │
│  ┌──────────────────────────────┐    │
│  │     [ SUBMIT CARD ]          │    │  [NEW] TasheelButton Primary
│  └──────────────────────────────┘    │  [A11Y: "Submit card for auto-debit"]
│                                      │  48dp height, full width
│                                      │
│  ─── 3DS Challenge (if triggered): ─ │
│  ┌──────────────────────────────┐    │
│  │                               │    │  [NEW] Inline WebView / redirect
│  │   3D Secure Verification     │    │  [A11Y: "3D Secure verification in progress"]
│  │   [Bank challenge page]      │    │  Hosted in WebView or external redirect
│  │                               │    │
│  └──────────────────────────────┘    │
│                                      │
│  Error state:                        │
│  ┌──────────────────────────────┐    │
│  │ ⚠️ Card verification failed   │    │  Error text, color: Error (#E74C3C)
│  │   [ Try Again ]              │    │
│  └──────────────────────────────┘    │
│                                      │
└──────────────────────────────────────┘
```

**TalkBack order**: Back → Title → Description → Processor badge → Card Number → Expiry → CVV → PCI note → Salary Date (if shown) → Auto-debit summary → Submit Card → 3DS challenge (if shown) → Error (if shown)
**Dynamic text**: All text scales with system font size
**States**: Default | Loading (hosted fields initializing) | Fields ready | Submitting (spinner) | 3DS challenge (WebView) | 3DS success (auto-navigate to disbursement) | 3DS fail (retry) | Error (card fail + retry)

---

## Screen M: Disbursement Confirmation (/application/{id}/disbursement)
**Security**: Standard (no FLAG_SECURE)
**API**: GET /api/v1/applications/{id}/disbursement
**Story**: US-037

```
┌──────────────────────────────────────┐
│  ←  Disbursement              ░░░░  │  [A11Y: back="Back to card details"]
├──────────────────────────────────────┤
│                                      │
│                                      │
│            ✅                         │  [NEW] Success illustration/icon
│                                      │  64dp, color: Success (#2ECC71)
│   "Your loan has been disbursed!"   │  Typography: headlineMedium (24sp)
│                                      │  [A11Y: liveRegion="assertive",
│                                      │   "Success, your loan has been disbursed"]
│                                      │
│  Disbursement Breakdown             │  Typography: titleMedium (16sp)
│  ┌──────────────────────────────┐    │
│  │                               │    │  [NEW] TasheelCard breakdown
│  │ Approved Amount  SAR XX,XXX  │    │  Typography: bodyMedium
│  │ ─────────────────────────── │    │
│  │ Admin Fee        SAR XXX    │    │  Typography: bodyMedium
│  │ ─────────────────────────── │    │
│  │ VAT (15%)        SAR XX     │    │  Typography: bodyMedium
│  │ ═══════════════════════════ │    │  Divider: bold
│  │ Net Amount       SAR XX,XXX │    │  Typography: titleMedium, Primary
│  │ Received                     │    │  Bold, prominent
│  │                               │    │
│  └──────────────────────────────┘    │  [A11Y: each row announced individually]
│                                      │
│  Repayment Info                      │  Typography: titleMedium (16sp)
│  ┌──────────────────────────────┐    │
│  │ First Payment   15 Jun 2026 │    │  [NEW] TasheelCard
│  │ ─────────────────────────── │    │
│  │ Monthly Amount  SAR X,XXX   │    │  Typography: bodyMedium
│  └──────────────────────────────┘    │  [A11Y: each row announced individually]
│                                      │
│  ┌──────────────────────────────┐    │
│  │    [ GO TO DASHBOARD ]       │    │  [NEW] TasheelButton Primary
│  └──────────────────────────────┘    │  [A11Y: "Go to dashboard"]
│                                      │  48dp height, full width
│                                      │  Sprint 3 placeholder — navigates to
│                                      │  products list until dashboard built
│                                      │
│  Loading: [Skeleton] icon + cards   │  LoadingSkeleton component
│  Error: [ErrorState + Retry]        │  ErrorState component
│                                      │
└──────────────────────────────────────┘
```

**TalkBack order**: Back → Success icon → Success message → Breakdown title → Approved Amount → Admin Fee → VAT → Net Amount → Repayment title → First Payment → Monthly Amount → Go to Dashboard → Error (if shown)
**Dynamic text**: All text scales with system font size
**States**: Default | Loading (skeleton) | Error (load fail + retry) | Success (breakdown displayed)

---

## Screen D [MODIFIED]: BridgeNow Landing (/products/{id})
**Security**: Standard (no FLAG_SECURE)
**API**: GET /api/v1/products/{id}
**Modification**: Apply Now button now navigates to Screen E (/apply/{productId}/personal) instead of placeholder

```
┌──────────────────────────────────────┐
│  ←  BridgeNow Finance         ░░░░  │  [A11Y: back="Back to products"]
├──────────────────────────────────────┤
│                                      │
│  ┌──────────────────────────────┐    │
│  │                               │    │  Hero banner (unchanged)
│  │   ✨ No Early Closure         │    │  Background: Primary-subtle (#EEF1FE)
│  │      Charges                  │    │  Typography: headlineMedium (24sp)
│  │                               │    │  Color: Primary (#4F6EF7)
│  │   "Easy Salary Advance for   │    │  Typography: bodyLarge
│  │    your immediate needs"      │    │  Color: Neutral-600
│  │                               │    │
│  └──────────────────────────────┘    │
│                                      │
│  Product Details                     │  Typography: titleMedium (16sp)
│  ┌──────────────────────────────┐    │
│  │ Loan Amount  │ 1x Income     │    │  Key-value card (unchanged)
│  │              │ (max SAR 30k) │    │
│  │ Tenure       │ 24 months     │    │
│  │ Profit Rate  │ 27% p.a.     │    │
│  │ Early Close  │ FREE ✅       │    │  Badge: Success
│  │ Admin Fee    │ 0.5% + VAT   │    │
│  │ Min Amount   │ SAR 4,000    │    │
│  └──────────────────────────────┘    │
│                                      │
│  ℹ️ "Your offer amount will be      │  Info banner (unchanged)
│     calculated based on your         │  Background: Primary-subtle
│     verified income"                 │  Typography: bodySmall
│                                      │
│  ┌──────────────────────────────┐    │
│  │     [ Apply for BridgeNow ]  │    │  [MODIFIED] TasheelButton Primary
│  └──────────────────────────────┘    │  NOW navigates to: /apply/{productId}/personal
│                                      │  [A11Y: "Start BridgeNow application"]
│                                      │  48dp height, full width
│                                      │
│  Loading: [Skeleton] hero + card    │
│  Error: [ErrorState + Retry]        │
│                                      │
└──────────────────────────────────────┘
```

**TalkBack order**: Back → Hero text → Positioning → Product details (each row) → Info banner → Apply button
**Dynamic text**: Hero scales, card rows wrap on large text
**States**: Default | Loading (skeleton) | Error (load fail + retry) | Success (product displayed)
**Change summary**: Only the Apply button navigation target changed — from placeholder to `/apply/{productId}/personal` (Screen E)