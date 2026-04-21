# Solution Architecture — Tasheel Finance Platform

## Overview
Tasheel Finance is a digital lending platform serving Saudi Arabia, offering Cash Finance and Combo Finance products via mobile app and back-office channels.

## System Context

```
┌─────────────────────────────────────────────────────────────────────┐
│                        EXTERNAL SYSTEMS                              │
├──────────┬──────────┬──────────┬──────────┬──────────┬─────────────┤
│  SIMAH   │   CITC   │  Geo     │  Open    │ Payment  │    SMS      │
│ (Credit) │ (Employ) │ (Verify) │ Banking  │Processor │  Gateway    │
└────┬─────┴────┬─────┴────┬─────┴────┬─────┴────┬─────┴──────┬──────┘
     │          │          │          │          │            │
┌────▼──────────▼──────────▼──────────▼──────────▼────────────▼──────┐
│                    AZURE API MANAGEMENT                              │
│              (Rate limiting, JWT validation, routing)                │
└────────────────────────────────┬────────────────────────────────────┘
                                 │
┌────────────────────────────────▼────────────────────────────────────┐
│                    AZURE KUBERNETES SERVICE (AKS)                    │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │  LoanApp.Api (.NET 8 Web API)                                │   │
│  │  ├── ProductsController                                      │   │
│  │  ├── ApplicationsController (+ Assessment/DE)                │   │
│  │  ├── OffersController                                        │   │
│  │  ├── LoansController (Settlement, Liability Letter)          │   │
│  │  ├── PaymentsController                                      │   │
│  │  ├── CardsController                                         │   │
│  │  ├── BackOfficeController                                    │   │
│  │  └── HealthController                                        │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                         │                    │                       │
│  ┌──────────────────────▼──┐  ┌──────────────▼──────────────────┐   │
│  │   SQL Server 2022       │  │   Redis 7.2 (Cache)             │   │
│  │   - Products            │  │   - Product catalog (1hr TTL)   │   │
│  │   - Applications        │  │   - Session data (15min TTL)    │   │
│  │   - Offers              │  └─────────────────────────────────┘   │
│  │   - Loans               │                                        │
│  │   - Payments            │  ┌─────────────────────────────────┐   │
│  │   - DebitCards           │  │   Azure Service Bus             │   │
│  │   - AutoDebitSchedules  │  │   - application-events          │   │
│  │   - AuditLogs           │  │   - offer-events                │   │
│  └─────────────────────────┘  │   - loan-events                 │   │
│                                │   - notification-events         │   │
│                                └─────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│                    MOBILE APP (Android)                              │
│  Kotlin 2.0 + Jetpack Compose + Material Design 3                   │
│  MVVM + StateFlow + Room (offline) + Hilt DI                        │
│  Navigation Compose (single activity)                               │
└─────────────────────────────────────────────────────────────────────┘
```

## Bounded Contexts

| Context | Responsibility | Key Entities |
|---------|---------------|--------------|
| Auth | Authentication, session management | User, Session, Token |
| Products | Product catalog, configuration, eligibility | Product |
| Applications | Application lifecycle, assessment (DE) | LoanApplication, Assessment |
| Offers | Offer generation, acceptance | Offer |
| Loans | Active loan management, servicing | Loan, Payment, AutoDebitSchedule |
| Notifications | Customer communications | Notification, Template |

## Technology Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Backend language | C# 12 / .NET 8 | EA1 mandatory |
| Architecture | Clean Architecture + CQRS (MediatR) | EA2 — separation of concerns, testability |
| Database | SQL Server 2022 + EF Core 8 | EA1/EA4 — enterprise standard |
| Mobile | Kotlin 2.0 + Jetpack Compose | EA1 — Android-first market |
| API style | REST (OpenAPI 3.0) | EA3 — standard, tooling support |
| Messaging | Azure Service Bus | EA8 — async event-driven |
| Caching | Redis 7.2 | EA4 — product catalog, sessions |
| Observability | Azure Monitor + App Insights | EA9 — platform standard |
| CI/CD | Azure DevOps | EA6 — enterprise standard |
| Secrets | Azure Key Vault | EA5 — no secrets in code |

## Security Architecture

- **Authentication**: OAuth 2.0 + PKCE (mobile), JWT RS256 (15-min expiry)
- **Authorization**: Role-based (Customer, Agent, Admin, System)
- **Data at rest**: AES-256 for PII columns (NationalId, DOB, Phone, Income)
- **Data in transit**: TLS 1.2+ mandatory, certificate pinning on mobile
- **Card data**: PCI-DSS SAQ A — tokenisation via payment processor, raw PAN never stored
- **Audit**: All state transitions and PII access logged (async via Service Bus)

## Current Products

| Product | Tenure | Rate | Amount | Early Closure Fee | Admin Fee |
|---------|--------|------|--------|-------------------|-----------|
| Cash Finance | 3-60 months (variable) | 20% p.a. (risk-based in prod, fixed in dev mock) | SAR 5,000 - 250,000 | 1.0% | 1.5% |
| Combo Finance | 6-60 months (variable) | 18% p.a. (risk-based in prod, fixed in dev mock) | SAR 10,000 - 500,000 | 1.5% | 1.0% |

## Deployment Model

- **Environments**: dev → staging → prod (AKS namespaces)
- **Deployment**: Trunk-based, PR to main, auto-deploy to dev, manual gate to prod
- **Scaling**: HPA (min 2, max 10 replicas), CPU/memory based
- **DR**: Active-passive across Azure paired regions

## Application Data Model

### LoanApplication Entity (core)

| Field | Type | PII | Notes |
|-------|------|-----|-------|
| Id | GUID | No | PK |
| UserId | GUID | No | FK → Users |
| ProductId | GUID | No | FK → Products |
| Status | Enum | No | Draft→Submitted→Verifying→Approved→OfferGenerated→OfferAccepted→Active→Settled→Closed + Abandoned/Referred |
| FullName | string | Yes | Applicant legal name |
| NationalId | string | Yes | [ENCRYPTED] 10-digit Iqama |
| DateOfBirth | date | Yes | [ENCRYPTED] |
| Address | string | No | Street address |
| City | string | No | |
| Region | string | No | Central/Western/Eastern |
| EmployerName | string | No | |
| EmploymentType | enum | No | Government/Private/Military/Self-employed |
| EmploymentStartDate | date | No | |
| Income (Net) | decimal | Yes | [ENCRYPTED] Net monthly SAR |
| GrossIncome | decimal | Yes | [ENCRYPTED] Gross monthly SAR |
| OtherIncome | decimal | No | Default 0 |
| RequestedAmount | decimal | No | |
| Tenure | int | No | Months |
| ApprovedAmount | decimal | No | Nullable, set by DE |
| SalaryDate | int | No | Day of month (1-28) |

### Supporting Entities

- **Product**: Id, Name, Type, MinAmount, MaxAmount, MinTenure, MaxTenure, BaseRate, EarlyClosureFeePercent, AdminFeePercent
- **Offer**: Id, ApplicationId, Amount, Tenure, ProfitRate, AdminFee, MonthlyPayment, TotalAmount, ValidUntil
- **Loan**: Id, ApplicationId, OfferId, DisbursedAmount, OutstandingPrincipal, Status, DisbursedAt
- **Payment**: Id, LoanId, Amount, Type (AutoDebit/Manual/Settlement), Status (Initiated/Submitted/Settled/Failed/Returned), AttemptNumber
- **DebitCard**: Id, ApplicationId, TokenReference, Last4Digits, SalaryDate, IsActive
- **AutoDebitSchedule**: Id, LoanId, DebitCardId, ScheduledDate, Amount, Status
- **AuditLog**: Id, UserId, Action, Resource, ResourceId, Changes, Reason, Timestamp

## Screen Inventory (Mobile App)

| Screen | Route | Wireframe | Description |
|--------|-------|-----------|-------------|
| Login | /auth/login | A | Phone/email + password + biometric |
| Product List | /products | B | Cash + Combo cards, bottom nav |
| Application Form | /apply/{productId} | C1-C4 | 4-step wizard: Personal Info → Employment → Income → Review & Submit |
| Card Collection | /apply/{appId}/card | D | Card details + salary date + single auto-debit + authorization |
| Offer | /offer/{appId} | E | Amount hero + terms + accept/decline + 48hr validity |
| Dashboard | /dashboard | F | Welcome message + active loans + bottom nav |
| Loan Detail | /loan/{loanId} | G | Balance + details + payment schedule + settlement/liability/topup |
| Back Office | /backoffice | H | Product dropdown + customer data + income + submit |

### Application Form Steps (Wireframe C)

| Step | Fields | Validation |
|------|--------|------------|
| 1. Personal Info | Full Name, DOB, National ID, Address, City, Region | All required except Address |
| 2. Employment | Employment Type (dropdown), Employer Name, Start Date | Type + Employer required |
| 3. Income | Gross Income, Net Income, Other Income, Requested Amount, Tenure | Gross + Net required, Amount within product limits |
| 4. Review & Submit | Read-only summary with ✏️ Edit per section, T&C checkbox | Checkbox must be checked |

## API Endpoint Inventory

| Method | Endpoint | Handler | Auth | Description |
|--------|----------|---------|------|-------------|
| POST | /api/v1/auth/token | AuthHandler | Public | Login |
| GET | /api/v1/products | GetProductsQuery | Public | List products |
| GET | /api/v1/products/{id} | GetProductQuery | Public | Product detail |
| POST | /api/v1/applications | CreateApplicationCommand | Bearer | Create draft application (all personal/employment/income fields) |
| GET | /api/v1/applications/{id} | GetApplicationQuery | Bearer | Get application |
| POST | /api/v1/applications/{id}/submit | SubmitApplicationCommand | Bearer | Submit for assessment |
| POST | /api/v1/applications/{id}/assess | AssessApplicationCommand | Bearer | Decision engine (CITC + SIMAH + DBR) |
| POST | /api/v1/applications/{id}/card | RegisterCardCommand | Bearer | Register debit card + auto-debit |
| POST | /api/v1/applications/{id}/offer/generate | GenerateOfferCommand | Bearer | Generate offer from assessment |
| GET | /api/v1/applications/{id}/offer | GetOfferQuery | Bearer | Get offer |
| POST | /api/v1/applications/{id}/offer/accept | AcceptOfferCommand | Bearer | Accept offer, create loan |
| GET | /api/v1/loans | GetLoansQuery | Bearer | List active loans |
| GET | /api/v1/loans/{id} | GetLoanQuery | Bearer | Loan detail |
| GET | /api/v1/loans/{id}/settlement-figure | GetSettlementFigureQuery | Bearer | Settlement calculation |
| GET | /api/v1/loans/{id}/liability-letter | GetLiabilityLetterQuery | Bearer | Generate liability letter |
| GET | /api/v1/loans/{id}/topup-eligibility | GetTopUpEligibilityQuery | Bearer | Check top-up eligibility |
| GET | /api/v1/loans/{id}/payments | GetPaymentsQuery | Bearer | Payment history |
| POST | /api/v1/loans/{id}/payments | CreatePaymentCommand | Bearer | Make payment |
| POST | /api/v1/backoffice/applications | CreateBoApplicationCommand | Bearer (Agent) | Agent creates application |
| GET | /api/v1/audit | GetAuditQuery | Bearer (Admin) | Audit log |
| GET | /health | HealthCheck | Public | Liveness |
| GET | /health/ready | ReadinessCheck | Public | Readiness |

## BridgeNow Enhancement Points

The following components will be modified for BridgeNow:
1. **Product table** — new BridgeNow product type (EP-01)
2. **AssessApplicationCommand** — new DE rule branch (EP-01)
3. **Disbursement/Settlement/Liability Letter** — zero-fee overrides (EP-01)
4. **Mobile app** — new landing page + STP journey (EP-02)
5. **CardCollectionScreen** — dual auto-debit (EP-02)
6. **BackOfficeController** — BridgeNow option + product switch (EP-03)
7. **Payment failure handling** — dual-date retry (EP-04)
8. **TopUp eligibility** — BridgeNow as source product (EP-05)
9. **Reporting** — BridgeNow filter + STP monitoring (EP-06)
