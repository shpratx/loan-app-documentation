# Tasheel Finance — User Flow Diagrams

> Nodes marked `[BRIDGENOW]` indicate steps that the BridgeNow integration adds or modifies.

---

## 1. Cash Finance Application Flow (Happy Path)

```
┌─────────┐     ┌──────────────┐     ┌─────────────────┐
│  Login   │────▶│ Product List  │────▶│ Select "Cash    │
│  Screen  │     │              │     │  Finance"       │
└─────────┘     └──────────────┘     └────────┬────────┘
                                               │
                                               ▼
                ┌──────────────────────────────────────────────────┐
                │          Application Form (Wizard)               │
                │                                                  │
                │  Step 1          Step 2         Step 3           │
                │  Personal   ──▶  Employment ──▶ Income           │
                │  Info            Details        (editable for    │
                │                                 Cash Finance)    │
                │                                     │            │
                │                                     ▼            │
                │                                Step 4            │
                │                                Review &          │
                │                                Submit            │
                └─────────────────────────────────────┬────────────┘
                                                      │
                                                      ▼
                                            ┌─────────────────┐
                                            │   Assessment     │
                                            │   Engine         │
                                            │  ┌────────────┐  │
                                            │  │ CITC check │  │
                                            │  │ SIMAH check│  │
                                            │  │ DBR calc   │  │
                                            │  │ Geolocation│  │
                                            │  └────────────┘  │
                                            └────────┬────────┘
                                                     │
                                          ┌──────────┴──────────┐
                                          ▼                     ▼
                                   ┌────────────┐       ┌────────────┐
                                   │  Approved   │       │  Rejected  │
                                   └──────┬─────┘       └────────────┘
                                          │                    │
                                          ▼                    ▼
                                   ┌────────────┐       ┌────────────┐
                                   │   Offer    │       │ Rejection  │
                                   │   Screen   │       │ Screen     │
                                   └──────┬─────┘       └────────────┘
                                          │
                                ┌─────────┴─────────┐
                                ▼                   ▼
                         ┌────────────┐      ┌────────────┐
                         │  Accept    │      │  Decline   │
                         └──────┬─────┘      └──────┬─────┘
                                │                   │
                                ▼                   ▼
                         ┌────────────┐      ┌────────────┐
                         │   Card     │      │ Product    │
                         │ Collection │      │ List       │
                         └──────┬─────┘      └────────────┘
                                │
                                ▼
                         ┌────────────┐
                         │  Contract  │
                         │  Signing   │
                         │  (e-sign)  │
                         └──────┬─────┘
                                │
                                ▼
                         ┌────────────┐
                         │ Disbursement│
                         │ Processing │
                         │ (SMS conf) │
                         └──────┬─────┘
                                │
                                ▼
                         ┌────────────┐
                         │ Dashboard  │
                         │ (loan      │
                         │  visible)  │
                         └────────────┘
```

### BridgeNow Modifications to This Flow

```
[BRIDGENOW: New branch after Product List]

  Product List
      │
      ├──▶ Select "Cash Finance"    (existing)
      ├──▶ Select "Combo Finance"   (existing)
      └──▶ Select "Bridge Finance"  [BRIDGENOW: NEW product]
                │
                ▼
         Bridge-specific form       [BRIDGENOW: Shortened wizard]
         (fewer steps, salary       [BRIDGENOW: Expected salary date,
          date required)             bridge amount ≤ next salary]
                │
                ▼
         Fast-track assessment      [BRIDGENOW: Lighter checks,
                │                    no full SIMAH pull]
                ▼
         Bridge Offer               [BRIDGENOW: Short tenure 7–30 days]
                │
                ▼
         Accept → Disbursement      [BRIDGENOW: Skip card collection
                │                    if card already on file]
                ▼
         Dashboard

[BRIDGENOW: On Offer Screen — add amount adjustment slider]
[BRIDGENOW: On Review Step — add BridgeNow data-sharing consent]
[BRIDGENOW: On Dashboard — show Top-Up eligibility banner]
```

---

## 2. Back Office Flow

```
┌──────────────┐     ┌──────────────────┐     ┌─────────────────┐
│ Agent Login  │────▶│ Back Office      │────▶│ Create New      │
│ (web portal) │     │ Dashboard        │     │ Application     │
└──────────────┘     └──────────────────┘     └────────┬────────┘
                                                       │
                                                       ▼
                                              ┌─────────────────┐
                                              │ Select Product  │
                                              │ ▼ Cash Finance  │
                                              │   Combo Finance │
                                              │  [BRIDGENOW:    │
                                              │   Bridge Finance│
                                              │   added here]   │
                                              └────────┬────────┘
                                                       │
                                                       ▼
                                              ┌─────────────────┐
                                              │ Enter Customer  │
                                              │ Data            │
                                              │ - National ID   │
                                              │ - Name, Phone   │
                                              │ - Income        │
                                              │ - Amount/Tenure │
                                              │                 │
                                              │ [BRIDGENOW: If  │
                                              │  Bridge selected│
                                              │  show salary    │
                                              │  date + bridge  │
                                              │  amount cap]    │
                                              └────────┬────────┘
                                                       │
                                                       ▼
                                              ┌─────────────────┐
                                              │ Submit          │
                                              │ Application     │
                                              └────────┬────────┘
                                                       │
                                                       ▼
                                              ┌─────────────────┐
                                              │ Assessment      │
                                              │ Engine          │
                                              │ (auto-triggered)│
                                              └────────┬────────┘
                                                       │
                                            ┌──────────┴──────────┐
                                            ▼                     ▼
                                     ┌────────────┐       ┌────────────┐
                                     │ Approved → │       │ Rejected → │
                                     │ Offer gen  │       │ Status     │
                                     │ → notify   │       │ updated    │
                                     │ customer   │       │            │
                                     └────────────┘       └────────────┘
```

---

## 3. Settlement Flow

```
┌────────────┐     ┌────────────────┐     ┌──────────────────────┐
│ Dashboard  │────▶│ Loan Detail    │────▶│ Request Settlement   │
│ (tap loan) │     │ Screen         │     │ Figure               │
└────────────┘     └────────────────┘     └──────────┬───────────┘
                                                     │
                                                     ▼
                                          ┌──────────────────────┐
                                          │  Settlement Bottom   │
                                          │  Sheet               │
                                          │                      │
                                          │  Outstanding: SAR X  │
                                          │  Early Settle: SAR Y │
                                          │  Discount:     SAR Z │
                                          │  Valid until: <date> │
                                          │                      │
                                          │  [BRIDGENOW: Show    │
                                          │   bridge loan settle │
                                          │   as auto-deduct on  │
                                          │   salary credit date]│
                                          │                      │
                                          │  ┌────────────────┐  │
                                          │  │  [ Pay Now ]   │  │
                                          │  └────────────────┘  │
                                          │  ┌────────────────┐  │
                                          │  │  [ Cancel ]    │  │
                                          │  └────────────────┘  │
                                          └──────────┬───────────┘
                                                     │
                                              ┌──────┴──────┐
                                              ▼             ▼
                                       ┌───────────┐ ┌───────────┐
                                       │ Payment   │ │ Return to │
                                       │ Processing│ │ Loan      │
                                       │           │ │ Detail    │
                                       └─────┬─────┘ └───────────┘
                                             │
                                             ▼
                                       ┌───────────┐
                                       │ Success   │
                                       │ Screen    │
                                       │ (loan     │
                                       │ closed)   │
                                       └─────┬─────┘
                                             │
                                             ▼
                                       ┌───────────┐
                                       │ Dashboard │
                                       │ (status:  │
                                       │ Completed)│
                                       └───────────┘
```

---

## 4. Liability Letter Flow

```
┌────────────┐     ┌────────────────┐     ┌──────────────────────┐
│ Dashboard  │────▶│ Loan Detail    │────▶│ Download Liability   │
│            │     │                │     │ Letter               │
└────────────┘     └────────────────┘     └──────────┬───────────┘
                                                     │
                                                     ▼
                                          ┌──────────────────────┐
                                          │  PDF Generated       │
                                          │  - Loan reference    │
                                          │  - Outstanding amt   │
                                          │  - Payment history   │
                                          │  - Tasheel stamp     │
                                          │                      │
                                          │  [ Share ] [ Save ]  │
                                          └──────────────────────┘
```

---

## Flow Summary — Screen Inventory

| #  | Screen                  | Type       | API Endpoint(s)                          |
|----|-------------------------|------------|------------------------------------------|
| 1  | Login                   | Mobile     | POST /auth/login                         |
| 2  | Product List            | Mobile     | GET /api/products                        |
| 3  | Application Form (×4)   | Mobile     | POST /api/applications                   |
| 4  | Card Collection         | Mobile     | POST /api/cards/register                 |
| 5  | Offer Screen            | Mobile     | GET /api/offers/{id}, POST /api/offers/accept |
| 6  | Dashboard               | Mobile     | GET /api/loans                           |
| 7  | Loan Detail             | Mobile     | GET /api/loans/{id}                      |
| 8  | Settlement              | Mobile     | GET /api/loans/{id}/settlement           |
| 9  | Liability Letter        | Mobile     | GET /api/loans/{id}/liability-letter     |
| 10 | Back Office             | Web        | POST /api/backoffice/applications        |
