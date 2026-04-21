# Tasheel Finance — Screen Wireframes

> Annotations marked `[BRIDGENOW: ...]` indicate elements that the BridgeNow integration will add or modify in the existing screens.

---

## A. Login Screen

```
┌──────────────────────────────────────┐
│           ░░ Status Bar ░░           │
├──────────────────────────────────────┤
│                                      │
│         [ Tasheel Logo ]             │
│        "Tasheel Finance"             │  ← Display style
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 📱  Phone or Email           │    │  ← TasheelTextField
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ 🔒  Password            👁   │    │  ← TasheelTextField + toggle
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │        [ LOG IN ]            │    │  ← TasheelButton Primary
│  └──────────────────────────────┘    │
│                                      │
│         ── or sign in with ──        │
│                                      │
│          [ 👆 Biometric ]            │  ← fingerprint icon, Text button
│                                      │
│       Forgot password?               │  ← Text link → Reset flow
│                                      │
│  [BRIDGENOW: Add "Register with     │
│   Nafath" button below biometric]    │
│                                      │
└──────────────────────────────────────┘
```

**Navigation:**
- LOG IN → Dashboard (if existing customer) or Product List (if new)
- Biometric → Dashboard (if enrolled)
- Forgot password → Password reset flow

---

## B. Product List

```
┌──────────────────────────────────────┐
│  ←  Our Products              🔔    │  ← App bar
├──────────────────────────────────────┤
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 🏦  Cash Finance              │    │  ← ProductCard
│  │                               │    │
│  │  SAR 5,000 – 250,000         │    │  ← Body
│  │  Rate: 20%  ·  3–60 months   │    │  ← Label
│  │                               │    │
│  │  [ Apply Now ]                │    │  ← TasheelButton Primary
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 💳  Combo Finance             │    │  ← ProductCard
│  │                               │    │
│  │  SAR 10,000 – 500,000        │    │
│  │  Rate: 18%  ·  6–60 months   │    │
│  │                               │    │
│  │  [ Apply Now ]                │    │  ← TasheelButton Primary
│  └──────────────────────────────┘    │
│                                      │
│  [BRIDGENOW: Add "Top-Up" badge     │
│   on eligible product cards]         │
│                                      │
│  [BRIDGENOW: Add "Bridge Finance"   │
│   product card — SAR 2K–50K,        │
│   7–30 days, bridge-specific rate]   │
│                                      │
├──────────────────────────────────────┤
│  🏠 Home   📋 Products   👤 Profile │  ← Bottom nav
└──────────────────────────────────────┘
```

**Navigation:**
- Apply Now (Cash) → Application Form Step 1 (productType=Cash)
- Apply Now (Combo) → Application Form Step 1 (productType=Combo)

---

## C. Application Form — Multi-Step Wizard

### Step 1: Personal Info

```
┌──────────────────────────────────────┐
│  ←  Apply – Personal Info     1/4   │  ← App bar + step indicator
├──────────────────────────────────────┤
│  ■■■■■■■■░░░░░░░░░░░░░░░░░░░░░░░░  │  ← Progress bar (25%)
│                                      │
│  ┌──────────────────────────────┐    │
│  │ Full Name                    │    │  ← TasheelTextField
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ Date of Birth        📅      │    │  ← Date picker
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ National ID (Iqama)          │    │  ← TasheelTextField
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ Address Line 1               │    │  ← TasheelTextField
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ City                         │    │  ← TasheelTextField
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ Region              ▼        │    │  ← Dropdown
│  └──────────────────────────────┘    │
│                                      │
│  [BRIDGENOW: Auto-populate fields   │
│   from Nafath/Absher verified data]  │
│                                      │
├──────────────────────────────────────┤
│           [ Next → ]                 │  ← TasheelButton Primary
└──────────────────────────────────────┘
```

### Step 2: Employment

```
┌──────────────────────────────────────┐
│  ←  Apply – Employment        2/4   │
├──────────────────────────────────────┤
│  ■■■■■■■■■■■■■■■■░░░░░░░░░░░░░░░░  │  ← Progress bar (50%)
│                                      │
│  ┌──────────────────────────────┐    │
│  │ Employment Type      ▼       │    │  ← Dropdown
│  │ (Government/Private/Military)│    │    (Salaried/Self-employed)
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ Employer Name                │    │  ← TasheelTextField
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ Employment Start Date  📅    │    │  ← Date picker
│  └──────────────────────────────┘    │
│                                      │
│  [BRIDGENOW: Add employer lookup     │
│   via GOSI integration]             │
│                                      │
├──────────────────────────────────────┤
│     [ ← Back ]      [ Next → ]      │
└──────────────────────────────────────┘
```

### Step 3: Income

```
┌──────────────────────────────────────┐
│  ←  Apply – Income            3/4   │
├──────────────────────────────────────┤
│  ■■■■■■■■■■■■■■■■■■■■■■■■░░░░░░░░  │  ← Progress bar (75%)
│                                      │
│  ┌──────────────────────────────┐    │
│  │ Gross Monthly Income (SAR)   │    │  ← TasheelTextField (number)
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ Net Monthly Income (SAR)     │    │  ← TasheelTextField (number)
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ Other Income (SAR)           │    │  ← TasheelTextField (number)
│  └──────────────────────────────┘    │
│                                      │
│  ℹ️  For Cash Finance, income        │
│     fields are editable.             │
│     For Combo, values are fetched    │
│     from employer payroll.           │
│                                      │
│  [BRIDGENOW: Add SIMAH-sourced      │
│   existing obligations summary       │
│   showing current DBR before apply]  │
│                                      │
├──────────────────────────────────────┤
│     [ ← Back ]      [ Next → ]      │
└──────────────────────────────────────┘
```

### Step 4: Review & Submit

```
┌──────────────────────────────────────┐
│  ←  Apply – Review            4/4   │
├──────────────────────────────────────┤
│  ■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■  │  ← Progress bar (100%)
│                                      │
│  Personal Info                  ✏️   │  ← Section header + edit
│  Name: <name>                        │
│  DOB: <date>  ·  ID: <id>           │
│  Address: <address>                  │
│  ─────────────────────────────────   │
│  Employment                     ✏️   │
│  Type: Government                    │
│  Employer: <employer>                │
│  Since: <date>                       │
│  ─────────────────────────────────   │
│  Income                         ✏️   │
│  Gross: SAR XX,XXX                   │
│  Net: SAR XX,XXX                     │
│  Other: SAR X,XXX                    │
│  ─────────────────────────────────   │
│                                      │
│  ☑ I agree to the Terms &           │
│    Conditions and authorize          │
│    credit checks.                    │
│                                      │
│  [BRIDGENOW: Add consent checkbox   │
│   for BridgeNow data sharing]       │
│                                      │
├──────────────────────────────────────┤
│          [ Submit Application ]      │  ← TasheelButton Primary
└──────────────────────────────────────┘
```

**Navigation:**
- Next → advances step; Back → returns to previous step
- ✏️ on Review → jumps to that step for editing
- Submit → triggers assessment → Offer Screen (if approved) or Rejection screen

---

## D. Card Collection

```
┌──────────────────────────────────────┐
│  ←  Card & Debit Setup              │
├──────────────────────────────────────┤
│                                      │
│  "Register your debit card for       │
│   automatic repayments"              │  ← Body
│                                      │
│  ┌──────────────────────────────┐    │
│  │ Card Number                  │    │  ← TasheelTextField (masked)
│  │ XXXX  XXXX  XXXX  ____      │    │
│  └──────────────────────────────┘    │
│  ┌─────────────┐ ┌─────────────┐    │
│  │ Expiry MM/YY│ │ CVV  •••    │    │  ← Side-by-side fields
│  └─────────────┘ └─────────────┘    │
│                                      │
│  ─────────────────────────────────   │
│                                      │
│  ┌──────────────────────────────┐    │
│  │ Salary Credit Date    📅 ▼  │    │  ← Date picker (day of month)
│  └──────────────────────────────┘    │
│                                      │
│  ☑ I authorize Tasheel Finance       │
│    to auto-debit monthly payments    │
│    from this card on the selected    │
│    date.                             │  ← Single confirmation checkbox
│                                      │
│  [BRIDGENOW: Add option to link     │
│   multiple cards for split debit]    │
│                                      │
├──────────────────────────────────────┤
│        [ Confirm & Continue ]        │  ← TasheelButton Primary
└──────────────────────────────────────┘
```

**Navigation:**
- Confirm → Offer Screen (card registered, offer generated)

---

## E. Offer Screen

```
┌──────────────────────────────────────┐
│  ←  Your Offer                       │
├──────────────────────────────────────┤
│                                      │
│          🎉 Congratulations!         │  ← Headline
│                                      │
│        SAR 150,000                   │  ← Display style (hero)
│        Loan Amount                   │  ← Label
│                                      │
│  ┌──────────────────────────────┐    │
│  │  Tenure         │ 36 months  │    │
│  │  Annual Rate    │ 20.00%     │    │
│  │  Monthly Payment│ SAR 5,577  │    │
│  │  Total Payable  │ SAR 200,772│    │
│  └──────────────────────────────┘    │  ← Card with key-value rows
│                                      │
│  ℹ️  This offer is valid for 48hrs   │  ← Info banner
│                                      │
│  [BRIDGENOW: Add "Adjust Amount"    │
│   slider to let user reduce amount   │
│   and see recalculated terms]        │
│                                      │
│  ┌──────────────────────────────┐    │
│  │       [ Accept Offer ]       │    │  ← TasheelButton Primary
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │       [ Decline ]            │    │  ← TasheelButton Secondary
│  └──────────────────────────────┘    │
│                                      │
└──────────────────────────────────────┘
```

**Navigation:**
- Accept → Contract signing → Disbursement → Dashboard
- Decline → Confirmation dialog → Product List

---

## F. Dashboard

```
┌──────────────────────────────────────┐
│  ☰  Dashboard                  🔔   │  ← App bar
├──────────────────────────────────────┤
│                                      │
│  Welcome back, <name>                │  ← Headline
│                                      │
│  [BRIDGENOW: Add "Top-Up Eligible"  │
│   banner if customer qualifies]      │
│                                      │
│  Active Loans                        │  ← Title
│                                      │
│  ┌──────────────────────────────┐    │
│  │ Cash Finance        [Active] │    │  ← ProductCard + StatusBadge
│  │                               │    │
│  │ Balance: SAR 120,450          │    │  ← Display style
│  │ Next Payment: SAR 5,577       │    │  ← Body
│  │ Due: 15 May 2026              │    │  ← Label
│  │                          →    │    │  ← Tap to view detail
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │ Combo Finance    [Completed] │    │
│  │                               │    │
│  │ Balance: SAR 0                │    │
│  │ Fully paid                    │    │
│  │                          →    │    │
│  └──────────────────────────────┘    │
│                                      │
│  [BRIDGENOW: Add "Bridge Loan"      │
│   quick-apply floating action        │
│   button for eligible customers]     │
│                                      │
├──────────────────────────────────────┤
│  🏠 Home   📋 Products   👤 Profile │  ← Bottom nav
└──────────────────────────────────────┘
```

**Navigation:**
- Loan card tap → Loan Detail
- Products tab → Product List
- 🔔 → Notifications

---

## G. Loan Detail

```
┌──────────────────────────────────────┐
│  ←  Cash Finance            [Active]│  ← App bar + StatusBadge
├──────────────────────────────────────┤
│                                      │
│        SAR 120,450                   │  ← Display (outstanding balance)
│        Outstanding Balance           │  ← Label
│                                      │
│  ┌──────────────────────────────┐    │
│  │ Original Amount │ SAR 150,000│    │
│  │ Tenure          │ 36 months  │    │
│  │ Rate            │ 20.00%     │    │
│  │ Monthly Payment │ SAR 5,577  │    │
│  │ Payments Made   │ 6 of 36   │    │
│  └──────────────────────────────┘    │
│                                      │
│  Payment Schedule                    │  ← Title
│  ┌──────────────────────────────┐    │
│  │ #  │ Date       │ Amount    │ ✓  │
│  │ 1  │ 15 Nov 25  │ 5,577    │ ✅ │
│  │ 2  │ 15 Dec 25  │ 5,577    │ ✅ │
│  │ 3  │ 15 Jan 26  │ 5,577    │ ✅ │
│  │ ...│            │          │    │
│  │ 7  │ 15 May 26  │ 5,577    │ ⏳ │  ← Next due
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │  [ Request Settlement Figure ]│   │  ← TasheelButton Primary
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │  [ Download Liability Letter ]│   │  ← TasheelButton Secondary
│  └──────────────────────────────┘    │
│                                      │
│  [BRIDGENOW: Add "Apply for Top-Up" │
│   button if eligible]               │
│                                      │
│  [BRIDGENOW: Add "Request Bridge    │
│   Loan" shortcut]                    │
│                                      │
└──────────────────────────────────────┘
```

**Navigation:**
- Request Settlement Figure → Settlement bottom sheet (amount + pay button)
- Download Liability Letter → PDF viewer / share sheet
- ← → Dashboard

---

## H. Back Office — Application Creation

```
┌──────────────────────────────────────┐
│  Tasheel Back Office          Agent ▼│  ← Web app bar
├──────────────────────────────────────┤
│                                      │
│  Create New Application              │  ← Headline
│                                      │
│  ┌──────────────────────────────┐    │
│  │ Product Type          ▼      │    │  ← Dropdown
│  │ (Cash Finance / Combo Finance)│   │
│  └──────────────────────────────┘    │
│                                      │
│  Customer Information                │  ← Title
│  ┌──────────────────────────────┐    │
│  │ National ID                  │    │
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ Full Name                    │    │
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ Phone Number                 │    │
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ Email                        │    │
│  └──────────────────────────────┘    │
│                                      │
│  Income Details                      │  ← Title
│  ┌──────────────────────────────┐    │
│  │ Gross Income (SAR)           │    │
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ Net Income (SAR)             │    │
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ Requested Amount (SAR)       │    │
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │ Requested Tenure (months) ▼  │    │
│  └──────────────────────────────┘    │
│                                      │
│  [BRIDGENOW: Add "Bridge Finance"   │
│   to product dropdown + show         │
│   bridge-specific fields when        │
│   selected (expected salary date,    │
│   bridge amount cap)]               │
│                                      │
│  ┌──────────────────────────────┐    │
│  │     [ Submit Application ]   │    │  ← TasheelButton Primary
│  └──────────────────────────────┘    │
│                                      │
└──────────────────────────────────────┘
```

**Navigation:**
- Submit → Assessment triggered → Application list with status
- Product dropdown change → form fields adjust per product rules
