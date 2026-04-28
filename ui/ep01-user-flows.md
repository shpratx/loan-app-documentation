# EP-01 User Flows — Sprint 1

---

## Flow 1: New User Registration & Product Discovery

```
[App Launch]
    │
    ▼
[Login Screen] ──── Has account? ──── Yes ──── [Enter Phone] ──── [OTP Screen] ──── Verify ──── [Product List]
    │                                                                    │
    │                                                              OTP Failed?
    │                                                                    │
    │                                                          ┌─────────┼──────────┐
    │                                                          │         │          │
    │                                                     Invalid    Expired    Rate Limited
    │                                                     "X tries   "Request   "Wait 10min"
    │                                                      left"     new code"
    │
    ├── Biometric enrolled? ── Yes ── [Biometric Prompt] ── Success ── [Product List]
    │                                        │
    │                                   Failed/Cancel ── [Fall back to OTP flow]
    │
    ▼
[Product List]
    │
    ├── Tap BridgeNow card ──── [BridgeNow Landing] ──── Tap "Apply" ──── [Application Form] (Sprint 2)
    │
    ├── Tap Cash Finance ──── [Application Form for Cash] (Sprint 2)
    │
    └── Tap Combo Finance ──── [Application Form for Combo] (Sprint 2)
```

### Decision Points
| Point | Condition | Path |
|-------|-----------|------|
| Has account? | Phone exists in Users table | OTP flow |
| Biometric enrolled? | BiometricEnabled = true | Biometric prompt |
| OTP valid? | Hash matches + not expired + attempts < 5 | Product List |
| OTP expired? | ExpiresAt < now | "Request new code" |
| Rate limited? | 3 OTPs in 10 minutes | "Wait 10 minutes" |

### Error Recovery
| Error | Screen | Recovery |
|-------|--------|----------|
| Invalid phone format | Login | Inline validation, re-enter |
| OTP wrong | OTP | Show attempts remaining, re-enter |
| OTP expired | OTP | Tap "Resend Code" |
| Rate limited | OTP | Wait timer, then resend |
| Biometric failed | Login | Fall back to OTP |
| Network error | Any | ErrorState with "Retry" button |
| API 500 | Any | ErrorState with correlation ID |

---

## Flow 2: Returning User Quick Access

```
[App Launch] ──── Token valid? ──── Yes ──── [Product List] (skip login)
                       │
                       No (expired)
                       │
                  Refresh token valid?
                       │
                  ┌────┼────┐
                 Yes        No
                  │          │
            [Silent refresh] [Login Screen]
                  │
            [Product List]
```

---

## Screen Map (Sprint 1)

| Screen | Route | Entry Points | Exit Points |
|--------|-------|-------------|-------------|
| Login | /auth/login | App launch (no valid session) | → OTP (send), → Product List (biometric) |
| OTP | /auth/otp | Login (send OTP) | → Product List (success), → Login (back) |
| Product List | /products | OTP (success), biometric (success), token refresh | → BridgeNow Landing, → App Form (Sprint 2) |
| BridgeNow Landing | /products/bridgenow | Product List (tap BridgeNow) | → App Form (Sprint 2), → Product List (back) |
