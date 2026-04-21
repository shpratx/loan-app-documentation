# Tasheel Finance — High Level Design (Baseline)

> Describes the existing system architecture before BridgeNow integration.

## 1. System Context

```
┌─────────────┐       ┌──────────────┐       ┌─────────────────────────────────┐
│  Mobile App  │──────▶│ API Gateway  │──────▶│       Backend Services (AKS)    │
│  (iOS/Android)│  HTTPS│ (APIM/Kong)  │       │                                 │
└─────────────┘       └──────────────┘       │  ┌─────────┐  ┌──────────────┐  │
                                              │  │ Auth Svc │  │ Product Svc  │  │
┌─────────────┐       ┌──────────────┐       │  └─────────┘  └──────────────┘  │
│ Back Office  │──────▶│ API Gateway  │──────▶│  ┌─────────┐  ┌──────────────┐  │
│   Portal     │  HTTPS│              │       │  │ App Svc  │  │  Offer Svc   │  │
└─────────────┘       └──────────────┘       │  └─────────┘  └──────────────┘  │
                                              │  ┌─────────┐  ┌──────────────┐  │
                                              │  │ Loan Svc │  │ Notif. Svc   │  │
                                              │  └─────────┘  └──────────────┘  │
                                              └────────┬──────────┬─────────────┘
                                                       │          │
                                              ┌────────▼──┐  ┌───▼────────┐
                                              │ SQL Server │  │   Redis     │
                                              │  (Primary) │  │  (Cache)    │
                                              └───────────┘  └────────────┘
```

## 2. Bounded Contexts

| Context | Responsibility | Key Aggregates |
|---|---|---|
| **Auth** | Identity, JWT issuance, token refresh, role-based access | User, Session |
| **Products** | Product catalogue, eligibility rules, pricing tiers | Product, PricingTier |
| **Applications** | Loan application lifecycle, state machine, document collection | LoanApplication |
| **Offers** | Offer generation post-assessment, acceptance workflow | Offer |
| **Loans** | Active loan management, repayment schedules, settlement, liability letters | Loan, Payment, AutoDebitSchedule |
| **Notifications** | SMS/push dispatch, template management | Notification, Template |

> Per **EA1** (Bounded Context Guidelines), each context owns its data and exposes contracts via REST + domain events.

## 3. Integration Points

| System | Protocol | Purpose |
|---|---|---|
| **SIMAH** (Credit Bureau) | Sync REST | Hard/soft credit searches during assessment |
| **CITC** | Sync REST | National ID & employment verification |
| **Geolocation** | Sync REST | Address verification |
| **Open Banking (AISP)** | Async redirect flow | Bank statement retrieval for affordability |
| **Payment Processor** | Sync REST + Webhooks | Disbursement, auto-debit collection, payment status callbacks |
| **SMS Gateway** | Async fire-and-forget | OTP delivery, status notifications |

> Per **EA6** (Integration Standards), all external calls use circuit breakers and structured logging.

## 4. Deployment Architecture

```
AKS Cluster
├── Namespace: tasheel-dev
├── Namespace: tasheel-staging
├── Namespace: tasheel-uat
└── Namespace: tasheel-prod
    ├── Deployment: auth-service          (2 replicas)
    ├── Deployment: product-service       (2 replicas)
    ├── Deployment: application-service   (3 replicas)
    ├── Deployment: offer-service         (2 replicas)
    ├── Deployment: loan-service          (3 replicas)
    ├── Deployment: notification-service  (2 replicas)
    ├── StatefulSet: redis-cache
    ├── Ingress: api-gateway
    └── ConfigMap/Secrets per service
```

- **SQL Server**: Azure SQL (managed), single database with schema-per-context separation
- **Redis**: Session cache, product catalogue cache, rate limiting
- **Message Broker**: Azure Service Bus (topic-per-event-type, per **EA8**)
- **Monitoring**: Application Insights + Prometheus/Grafana

## 5. Current Products

### Cash Finance
| Attribute | Value |
|---|---|
| Tenure | 12–60 months (variable) |
| APR | 15–35% p.a. (risk-based pricing) |
| Early Closure | Standard early closure fee applies |
| Disbursement | Direct to customer bank account |
| Repayment | Monthly auto-debit |

### Combo Finance
| Attribute | Value |
|---|---|
| Structure | Cash component + goods purchase component |
| Tenure | Aligned with Cash Finance tiers |
| Disbursement | Split: cash to account, goods value to merchant |
| Repayment | Single consolidated monthly auto-debit |

## 6. Cross-Cutting Concerns

- **Authentication**: JWT Bearer tokens, refresh token rotation (per **EA2**)
- **API Standards**: camelCase, RFC 7807 errors, pagination via `pageNumber`/`pageSize` (per **EA3**)
- **Audit**: All entities carry `CreatedAt`, `CreatedBy`, `UpdatedAt`, `UpdatedBy`, `IsDeleted`, `Version` (per **EA4**)
- **Observability**: Correlation IDs propagated via `X-Correlation-Id` header

## 7. Application State Machine

```
                    ┌──────────┐
                    │  Draft   │ ← Application created
                    └────┬─────┘
                         │ submit
                    ┌────▼─────┐
                    │Submitted │
                    └────┬─────┘
                         │ assess (CITC + SIMAH + DBR)
                    ┌────▼─────┐         ┌──────────┐
                    │Verifying │────────▶│ Referred │ (CITC fail / SIMAH fail / below min)
                    └────┬─────┘         └──────────┘
                         │ pass
                    ┌────▼─────┐
                    │ Approved │
                    └────┬─────┘
                         │ generate offer
                 ┌───────▼────────┐
                 │ OfferGenerated │
                 └───────┬────────┘
                         │ accept
                 ┌───────▼────────┐
                 │ OfferAccepted  │
                 └───────┬────────┘
                         │ disburse
                  ┌──────▼──────┐
                  │  Disbursing │──── fail ──▶ retry (max 2) ──▶ Referred
                  └──────┬──────┘
                         │ success
                    ┌────▼─────┐
                    │  Active  │ ← Loan servicing
                    └────┬─────┘
                    ┌────▼─────┐
                    │ Settled  │ ← Full payment / early settlement
                    └────┬─────┘
                    ┌────▼─────┐
                    │  Closed  │ ← Terminal
                    └──────────┘

  Side states:
  ┌───────────┐
  │ Abandoned │ ← Customer switches product / times out
  └───────────┘
```

### Transition Rules

| From | To | Trigger | Validation |
|------|-----|---------|------------|
| Draft | Submitted | Customer submits | All required fields populated |
| Submitted | Verifying | System starts assessment | — |
| Verifying | Approved | All checks pass | CITC ✓, SIMAH score ≥ 500, DBR ≤ 33%, amount ≥ product min |
| Verifying | Referred | Any check fails | Reason recorded |
| Approved | OfferGenerated | System generates offer | Amount, rate, tenure calculated |
| OfferGenerated | OfferAccepted | Customer accepts | Within validity period |
| OfferAccepted | Disbursing | System initiates transfer | Card registered, 2nd SIMAH passed |
| Disbursing | Active | Funds confirmed | — |

> **Note**: In the dev mock, OfferAccepted transitions directly to Active (Disbursing is implicit). Production implements the full Disbursing state with retry logic.
| Active | Settled | Full balance paid | Outstanding = 0 |
| Settled | Closed | Post-settlement processing | CRA updated |
| Any | Abandoned | Customer/agent switches product | Original app preserved for audit |

## 8. Screen Navigation Flow

```
Login ──▶ Product List ──▶ Application Form (4 steps) ──▶ Assessment ──▶ Offer ──▶ Card Collection ──▶ Dashboard
  │              │                                                                                        │
  │              ├── Bottom Nav: Home / Products / Profile                                                │
  │              │                                                                                        │
  │              └── [BRIDGENOW: BridgeNow card added to product list]                                   │
  │                                                                                                       │
  └── Biometric ──▶ Dashboard ◀──────────────────────────────────────────────────────────────────────────┘
                        │
                        ├──▶ Loan Detail ──▶ Settlement Figure
                        │                  ──▶ Liability Letter
                        │                  ──▶ Top-Up Eligibility
                        │                  ──▶ Make Payment
                        │
                        └── Bottom Nav: Home / Products / Profile

Back Office (separate):
  Agent Login ──▶ Create Application (product dropdown + customer data) ──▶ Submit ──▶ Assessment
```

### Application Form Flow (4-Step Wizard)

```
Step 1: Personal Info ──▶ Step 2: Employment ──▶ Step 3: Income ──▶ Step 4: Review & Submit
     (Name, DOB, ID,         (Type, Employer,      (Gross, Net,        (Read-only summary,
      Address, City,          Start Date)            Other, Amount,      ✏️ Edit per section,
      Region)                                        Tenure)             T&C checkbox)
```

## 9. Data Flow: Loan Application Lifecycle

```
Customer                    Mobile App                 API                    External Systems
   │                           │                        │                          │
   │── Open app ──────────────▶│                        │                          │
   │                           │── GET /products ──────▶│                          │
   │◀── Product list ─────────│◀── Products ───────────│                          │
   │── Select Cash Finance ──▶│                        │                          │
   │── Fill 4-step form ─────▶│                        │                          │
   │                           │── POST /applications ─▶│                          │
   │                           │── POST /submit ───────▶│                          │
   │                           │── POST /assess ───────▶│── CITC verify ─────────▶│
   │                           │                        │◀── CITC result ─────────│
   │                           │                        │── SIMAH score ──────────▶│
   │                           │                        │◀── Score result ────────│
   │                           │                        │── DBR calculation        │
   │◀── Approved ─────────────│◀── Assessment result ──│                          │
   │                           │── POST /offer/generate▶│                          │
   │◀── Offer displayed ──────│◀── Offer ──────────────│                          │
   │── Accept offer ──────────▶│── POST /offer/accept ─▶│                          │
   │── Enter card details ────▶│── POST /card ─────────▶│── Tokenize card ───────▶│
   │                           │                        │◀── Token ───────────────│
   │                           │                        │── Disburse funds ──────▶│
   │◀── Loan active ──────────│◀── Loan created ──────│                          │
   │                           │                        │                          │
   │── View dashboard ────────▶│── GET /loans ─────────▶│                          │
   │── Request settlement ────▶│── GET /settlement ────▶│                          │
   │◀── Settlement figure ────│◀── Figure ─────────────│                          │
```
