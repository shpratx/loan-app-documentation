# EP-02 / EP-03 User Flows — Sprint 2

---

## Flow 3: Application Submission (EP-02)

```
[Product Detail]
    │
    Tap "Apply Now"
    │
    ▼
[Personal Details — Step 1/4]
    │
    Fill name, ID, DOB, address
    │  (auto-save on each field blur → PUT /applications/{id})
    │
    Tap "Next"
    │
    ▼
[Employment Details — Step 2/4]
    │
    Fill employer, sector, start date
    │  (auto-save on blur)
    │
    Tap "Next"
    │
    ▼
[Income Details — Step 3/4]
    │
    ├── Income auto-populated from API? ── Yes ── Fields pre-filled (read-only)
    │                                        │
    │                                        No
    │                                        │
    │                                  Tap "Connect Bank"
    │                                        │
    │                                  [Open Banking Flow]
    │                                        │
    │                                  Bank authorized → income populated
    │
    Select salary date
    │  (auto-save on blur)
    │
    Tap "Next"
    │
    ▼
[Review — Step 4/4]
    │
    Review all data (personal, employment, income)
    │
    ├── Edit needed? ── Tap section ── [Jump to step] ── Edit ── Return to Review
    │
    Tick "I confirm details are accurate" checkbox
    │
    Tap "Submit"
    │
    POST /applications
    │
    ├── Success ──── [Application Status Screen]
    │
    ├── Validation error ──── Inline field errors ──── Fix ──── Re-submit
    │
    ├── Network error ──── [Error State] ──── Tap "Retry" ──── Re-submit
    │
    └── API error (500) ──── [Error State with correlation ID] ──── Tap "Retry"
```

### Decision Points — Flow 3
| Point | Condition | Path |
|-------|-----------|------|
| Income source | API returns salary data | Pre-fill fields (read-only) |
| Income source | No API data available | Show "Connect Bank" for Open Banking |
| Open Banking auth | User authorizes bank | Populate income fields |
| Open Banking auth | User cancels/fails | Manual income entry fallback |
| Confirm checkbox | Unchecked | Submit button disabled |
| Confirm checkbox | Checked | Submit button enabled |
| POST /applications | 201 Created | Navigate to Application Status |
| POST /applications | 422 Validation | Show inline errors per field |
| POST /applications | Network timeout | Show retry error state |

### Error Recovery — Flow 3
| Error | Screen | Recovery |
|-------|--------|----------|
| Field validation | Any step | Inline error below field, fix and re-blur to clear |
| Open Banking auth fail | Income (Step 3) | Retry bank connection or enter manually |
| Network error on save | Any step | Queued locally, retry on reconnect |
| Network error on submit | Review (Step 4) | Error state with "Retry" button |
| API 422 | Review (Step 4) | Scroll to first invalid field, show inline errors |
| API 500 | Review (Step 4) | Error state with correlation ID and "Retry" |
| Draft lost (edge case) | Any step | Restore from last auto-saved draft |

---

## Flow 4: Assessment & Decision (EP-03 F-03.1 / F-03.2)

```
[Application Status Screen]
    │
    Status: "Verifying"
    │
    ▼
CITC Check (server-side)
    │
    ├── ✅ Pass ──── Continue
    │
    └── ❌ Fail ──── Status → "Referred"
                        │
                        └── [Under Review Screen] ── poll for updates
    │
    ▼
Geolocation Check (server-side)
    │
    ├── ✅ Pass ──── Continue
    │
    └── ❌ Fail ──── Status → "Referred"
                        │
                        └── [Under Review Screen] ── poll for updates
    │
    ▼
SIMAH Credit Check (server-side)
    │
    ├── ✅ Pass ──── Continue
    │
    ├── ❌ Soft fail ──── Status → "Referred"
    │                        │
    │                        └── [Under Review Screen] ── poll for updates
    │
    └── ❌ Hard fail ──── Status → "Declined"
                            │
                            └── [Declined Screen]
    │
    ▼
Decision Engine
    │
    ├── Approved ──── Auto-navigate ──── [Offer Screen] (Flow 5)
    │
    ├── Referred ──── [Under Review Screen]
    │                        │
    │                   "Your application is under review"
    │                        │
    │                   Poll GET /applications/{id}/status every 30s
    │                        │
    │                   ├── Approved ──── [Offer Screen]
    │                   ├── Declined ──── [Declined Screen]
    │                   └── Still referred ──── Continue polling
    │
    └── Declined ──── [Declined Screen]
                            │
                       Reason displayed
                            │
                       Tap "Back to Products" ──── [Product List]
```

### Decision Points — Flow 4
| Point | Condition | Path |
|-------|-----------|------|
| CITC check | Identity verified | Continue to geolocation |
| CITC check | Identity not verified | Referred (manual review) |
| Geolocation check | Location within Saudi Arabia | Continue to SIMAH |
| Geolocation check | Location outside / VPN detected | Referred (manual review) |
| SIMAH check | Credit score above threshold | Continue to Decision Engine |
| SIMAH check | Score borderline | Referred (manual review) |
| SIMAH check | Score below minimum | Declined |
| Decision Engine | All rules pass | Approved → Offer screen |
| Decision Engine | Some rules fail (non-critical) | Referred → Under Review |
| Decision Engine | Critical rules fail | Declined → Declined screen |

### Error Recovery — Flow 4
| Error | Screen | Recovery |
|-------|--------|----------|
| Status poll network error | Application Status | Retry poll after 10s backoff |
| Status poll timeout | Application Status | Show "Checking status…" spinner, retry |
| CITC service unavailable | Application Status | Queue for retry server-side, user sees "Verifying" |
| SIMAH service unavailable | Application Status | Queue for retry server-side, user sees "Verifying" |
| Unexpected status value | Application Status | Show generic "Processing" with support contact |

---

## Flow 5: Offer Acceptance & Contract (EP-03 F-03.3)

```
[Offer Screen]
    │
    Review loan terms (PD5 PCCI):
      - Loan amount, APR, total repayable
      - Monthly instalment, number of payments
      - Fees breakdown
    │
    ├── Tap "Decline Offer" ──── Confirm dialog ──── [Product List]
    │
    └── Tap "Accept Offer"
            │
            ▼
      [Contract Screen]
            │
            Scroll through full contract text
            │
            Type full name in signature field
            │
            Tap "Sign Contract"
            │
            POST /applications/{id}/contract
            │
            ▼
      [Cooling-Off Screen]
            │
            24-hour countdown timer displayed
            │
            ├── During cooling-off (< 24h):
            │       │
            │       Tap "Cancel" ──── Confirm dialog
            │       │                      │
            │       │               "Are you sure? This will void the offer."
            │       │                      │
            │       │               ├── Yes ──── Offer voided (no charges) ──── [Product List]
            │       │               └── No ──── Return to Cooling-Off screen
            │       │
            │       Timer ticking…
            │
            └── After 24 hours:
                    │
                    Auto-proceed ──── [Card Collection Screen] (Flow 6)
```

### Decision Points — Flow 5
| Point | Condition | Path |
|-------|-----------|------|
| Offer response | User taps "Accept" | Navigate to Contract screen |
| Offer response | User taps "Decline" | Confirm → back to Product List |
| Contract signed | Name entered + signed | Start 24h cooling-off |
| Cooling-off cancel | User cancels within 24h | Void offer, no charges, back to Products |
| Cooling-off expiry | 24 hours elapsed | Auto-proceed to Card Collection |
| Contract POST | Success (201) | Show Cooling-Off screen |
| Contract POST | Failure | Error state with "Retry" |

### Error Recovery — Flow 5
| Error | Screen | Recovery |
|-------|--------|----------|
| Contract POST failure | Contract | Error state with "Retry" button |
| Network error during sign | Contract | Queued locally, retry on reconnect |
| Cooling-off timer sync error | Cooling-Off | Re-fetch server time on app foreground |
| Cancel request fails | Cooling-Off | Retry cancel, show error if persistent |
| Offer expired (server-side) | Offer | "Offer no longer available" → Product List |

---

## Flow 6: Card Collection & Disbursement (EP-03 F-03.4)

```
[Card Collection Screen]
    │
    Enter card details via hosted fields (PCI-compliant iframe)
      - Card number, expiry, CVV
    │
    Tap "Submit"
    │
    ▼
[3DS Challenge]
    │
    ├── OTP challenge ──── Enter OTP ──── Verify
    │
    └── Biometric challenge ──── Authenticate
            │
            ├── ✅ 3DS Success ──── Card verified
            │
            └── ❌ 3DS Failure ──── "Verification failed" ──── Retry or use different card
    │
    ▼
[Salary Date Selection]
    │
    Select salary date
    │
    Dual auto-debit schedule calculated and displayed
    │
    Tap "Confirm"
    │
    ▼
2nd SIMAH Check (automatic, server-side)
    │
    ├── ✅ Pass ──── Disbursement triggered
    │       │
    │       ├── ✅ Disbursement success
    │       │       │
    │       │       ▼
    │       │  [Confirmation Screen]
    │       │       - Net amount disbursed
    │       │       - Fees charged
    │       │       - First payment date & amount
    │       │       - Tap "Done" ──── [Dashboard]
    │       │
    │       └── ❌ Disbursement failure
    │               │
    │               Operations team alerted (server-side)
    │               │
    │               [Error Screen]
    │               "We're processing your loan. We'll notify you shortly."
    │               │
    │               Push notification when resolved
    │
    └── ❌ Fail ──── Application declined (credit profile changed)
            │
            [Declined Screen]
            "Your credit profile has changed since your application."
            │
            Tap "Back to Products" ──── [Product List]
```

### Decision Points — Flow 6
| Point | Condition | Path |
|-------|-----------|------|
| 3DS challenge | OTP/biometric succeeds | Card verified, continue |
| 3DS challenge | OTP/biometric fails | Retry or enter different card |
| 3DS challenge | User cancels | Return to Card Collection |
| 2nd SIMAH check | Credit profile unchanged | Proceed to disbursement |
| 2nd SIMAH check | Credit profile deteriorated | Application declined |
| Disbursement | Transfer succeeds | Confirmation screen |
| Disbursement | Transfer fails | Ops alerted, customer notified async |

### Error Recovery — Flow 6
| Error | Screen | Recovery |
|-------|--------|----------|
| Invalid card details | Card Collection | Inline validation via hosted fields |
| 3DS timeout | 3DS Challenge | "Verification timed out" → Retry |
| 3DS failure | 3DS Challenge | Retry or use different card |
| 2nd SIMAH unavailable | Processing | Queue server-side, user sees "Processing" |
| Disbursement failure | Processing | Ops alerted, push notification to user |
| Network error | Any | Error state with "Retry" button |
| Hosted fields load error | Card Collection | Retry iframe load, fallback error message |

---

## Screen Map (Sprint 2)

| Screen | Route | Entry Points | Exit Points | Notes |
|--------|-------|-------------|-------------|-------|
| E. Personal Details (Step 1) | /apply/{productId}/personal | Product Detail ("Apply Now") | → Employment (Next), → Product Detail (Back) | FLAG_SECURE |
| F. Employment Details (Step 2) | /apply/{productId}/employment | Personal Details (Next) | → Income (Next), → Personal Details (Back) | |
| G. Income Details (Step 3) | /apply/{productId}/income | Employment Details (Next) | → Review (Next), → Employment (Back), → Open Banking redirect | FLAG_SECURE |
| H. Review (Step 4) | /apply/{productId}/review | Income Details (Next) | → Application Status (Submit), → Any step (Edit) | |
| I. Application Status | /application/{id}/status | Review (Submit success) | → Offer (Approved) | States: Verifying, Referred ("Under Review"), Declined |
| J. Offer | /application/{id}/offer | Status (Approved) | → Contract (Accept), → Product List (Decline via dialog) | FLAG_SECURE, PD22 |
| K. Contract & Cooling-Off | /application/{id}/contract | Offer (Accept) | → Card Collection (24h elapsed), → Product List (Cancel via dialog) | FLAG_SECURE. States: Contract view, Signing, Cooling-off countdown |
| L. Card Collection | /application/{id}/card | Contract (cooling-off elapsed) | → Disbursement (success) | FLAG_SECURE. States: Card entry, 3DS challenge (inline), Salary date, Processing |
| M. Disbursement Confirmation | /application/{id}/disbursement | Card Collection (disbursement success) | → Dashboard (Sprint 3) | |