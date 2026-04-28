# EP-01 Wireframes — Sprint 1 Screens
### All screens [NEW] — greenfield development

---

## Screen A: Login (/auth/login)
**Security**: FLAG_SECURE enabled (prevents screenshots per EA5)
**API**: POST /api/v1/auth/register (send OTP)

```
┌──────────────────────────────────────┐
│           ░░ Status Bar ░░           │
├──────────────────────────────────────┤
│                                      │
│         [NEW] 🏦 App Logo            │  [A11Y: contentDescription="Tasheel Finance logo"]
│        "Tasheel Finance"             │  Typography: headlineLarge (32sp)
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 📱  Phone Number             │    │  [NEW] TasheelTextField
│  │     +966 5XX XXX XXXX        │    │  [A11Y: label="Phone number", 48dp target]
│  └──────────────────────────────┘    │  Validation: Saudi format, 10 digits
│                                      │
│  ┌──────────────────────────────┐    │
│  │      [ SEND OTP ]            │    │  [NEW] TasheelButton Primary
│  └──────────────────────────────┘    │  [A11Y: "Send verification code", 48dp]
│                                      │
│         ── or sign in with ──        │  Typography: bodySmall, Neutral-400
│                                      │
│          [ 👆 Biometric ]            │  [NEW] TasheelButton Text
│                                      │  [A11Y: "Sign in with fingerprint"]
│                                      │  Only shown if biometric enrolled
│                                      │
│  Error state:                        │
│  ┌──────────────────────────────┐    │
│  │ ⚠️ Invalid phone number      │    │  [NEW] Error text, color: Error (#E74C3C)
│  └──────────────────────────────┘    │
│                                      │
└──────────────────────────────────────┘
```

**TalkBack order**: Logo → Phone input → Send OTP → Divider → Biometric → Error (if shown)
**Dynamic text**: All text scales with system font size
**States**: Default | Phone focused | Validating | Error | Loading (button shows spinner)

---

## Screen B: OTP Verification (/auth/otp)
**Security**: FLAG_SECURE enabled (OTP visible, auth flow per EA5)
**API**: POST /api/v1/auth/verify-otp → POST /api/v1/auth/token

```
┌──────────────────────────────────────┐
│  ←  Verify Phone              ░░░░  │  [A11Y: back="Navigate back"]
├──────────────────────────────────────┤
│                                      │
│  "Enter the 6-digit code sent to"   │  Typography: bodyLarge
│  "+966 5XX XXX XX34"                 │  Masked phone, bodyMedium bold
│                                      │
│  ┌──┐ ┌──┐ ┌──┐ ┌──┐ ┌──┐ ┌──┐    │
│  │  │ │  │ │  │ │  │ │  │ │  │    │  [NEW] 6 individual digit inputs
│  └──┘ └──┘ └──┘ └──┘ └──┘ └──┘    │  [A11Y: "Digit 1 of 6", auto-advance]
│                                      │  48dp each, 8dp gap, auto-focus first
│                                      │
│  ⏱️ Code expires in 01:30           │  [NEW] Countdown timer
│                                      │  Typography: bodySmall, Neutral-400
│                                      │
│  "Didn't receive code?"             │
│  [ Resend Code ]                     │  [NEW] TasheelButton Text
│                                      │  Disabled during countdown
│                                      │  [A11Y: "Resend verification code"]
│                                      │
│  Error states:                       │
│  "Invalid code — X attempts left"   │  Error (#E74C3C)
│  "Too many attempts — try in 10min" │  Error, resend disabled
│  "Code expired — request new code"  │  Warning (#F39C12)
│                                      │
│  ┌──────────────────────────────┐    │
│  │      [ VERIFY ]              │    │  [NEW] TasheelButton Primary
│  └──────────────────────────────┘    │  Enabled when 6 digits entered
│                                      │
└──────────────────────────────────────┘
```

**TalkBack order**: Back → Instructions → Phone number → Digit inputs (1-6) → Timer → Resend → Error → Verify
**States**: Entering | Verifying (loading) | Error (invalid/expired/rate-limited) | Success (auto-navigate)

---

## Screen C: Product List (/products)
**API**: GET /api/v1/products

```
┌──────────────────────────────────────┐
│  ☰  Our Products              🔔    │  [A11Y: menu="Open menu", bell="Notifications"]
├──────────────────────────────────────┤
│                                      │
│  ┌──────────────────────────────┐    │
│  │ ⭐ BridgeNow Finance         │    │  [NEW] TasheelCard (highlighted)
│  │    "Easy Salary Advance"     │    │  Border: Primary (#4F6EF7)
│  │                               │    │
│  │  SAR 4,000 – 30,000          │    │  Typography: bodyMedium
│  │  24 months · 27% p.a.        │    │  Typography: bodySmall, Neutral-400
│  │  ✨ No Early Closure Charges  │    │  Badge: Success (#2ECC71)
│  │                               │    │
│  │  [ Apply Now ]                │    │  TasheelButton Primary, 48dp
│  └──────────────────────────────┘    │  [A11Y: "Apply for BridgeNow Finance"]
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 🏦 Cash Finance              │    │  [NEW] TasheelCard
│  │                               │    │  Border: Neutral-200
│  │  SAR 5,000 – 250,000         │    │
│  │  3–60 months · From 15% p.a. │    │
│  │                               │    │
│  │  [ Apply Now ]                │    │  TasheelButton Primary
│  └──────────────────────────────┘    │  [A11Y: "Apply for Cash Finance"]
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 💳 Combo Finance             │    │  [NEW] TasheelCard
│  │                               │    │
│  │  SAR 10,000 – 500,000        │    │
│  │  6–60 months · From 18% p.a. │    │
│  │                               │    │
│  │  [ Apply Now ]                │    │  TasheelButton Primary
│  └──────────────────────────────┘    │  [A11Y: "Apply for Combo Finance"]
│                                      │
│  Loading: [Skeleton] x3 cards       │  LoadingSkeleton component
│  Error: [ErrorState + Retry]        │  ErrorState component
│  Empty: "No products available"     │
│                                      │
├──────────────────────────────────────┤
│  🏠 Home   📋 Products   👤 Profile │  [NEW] Bottom nav, 48dp targets
└──────────────────────────────────────┘  [A11Y: each tab labeled]
```

**TalkBack order**: Menu → Title → Notifications → BridgeNow card → Cash card → Combo card → Bottom nav tabs
**BridgeNow card**: Visually prominent (primary border, star icon, badge)

---

## Screen D: BridgeNow Landing (/products/bridgenow)
**API**: GET /api/v1/products/{id}

```
┌──────────────────────────────────────┐
│  ←  BridgeNow Finance         ░░░░  │  [A11Y: back="Back to products"]
├──────────────────────────────────────┤
│                                      │
│  ┌──────────────────────────────┐    │
│  │                               │    │  [NEW] Hero banner
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
│  │ Loan Amount  │ 1x Income     │    │  [NEW] Key-value card
│  │              │ (max SAR 30k) │    │
│  │ Tenure       │ 24 months     │    │
│  │ Profit Rate  │ 27% p.a.     │    │
│  │ Early Close  │ FREE ✅       │    │  Badge: Success
│  │ Admin Fee    │ 0.5% + VAT   │    │
│  │ Min Amount   │ SAR 4,000    │    │
│  └──────────────────────────────┘    │
│                                      │
│  ℹ️ "Your offer amount will be      │  [NEW] Info banner
│     calculated based on your         │  Background: Primary-subtle
│     verified income"                 │  Typography: bodySmall
│                                      │
│  ┌──────────────────────────────┐    │
│  │     [ Apply for BridgeNow ]  │    │  TasheelButton Primary, full width
│  └──────────────────────────────┘    │  [A11Y: "Start BridgeNow application"]
│                                      │  48dp height
│                                      │
│  Loading: [Skeleton] hero + card    │
│  Error: [ErrorState + Retry]        │
│                                      │
└──────────────────────────────────────┘
```

**TalkBack order**: Back → Hero text → Positioning → Product details (each row) → Info banner → Apply button
**Dynamic text**: Hero scales, card rows wrap on large text
