# Application Baseline — BridgeNow Finance (Greenfield)
### kb-L3-bridgenow-baseline v0.1.0 (Sprint 1)
### Living document — updated at the end of each sprint with implemented capabilities.

---

## BL1: Product Inventory

| Product ID | Name | Type | Status | Parameters |
|---|---|---|---|---|
| (to be generated) | BridgeNow Finance | BridgeNow | ✅ Seeded | Tenure: 24mo fixed, Rate: 27% p.a. flat, Amount: SAR 4k-30k (1x income), Early Closure: 0%, Admin: 0.5%+VAT |
| (to be generated) | Cash Finance | Cash | ✅ Seeded | Tenure: 3-60mo, Rate: 15-35% (risk-based), Amount: SAR 5k-250k, Early Closure: 1%, Admin: 1.5% |
| (to be generated) | Combo Finance | Combo | ✅ Seeded | Tenure: 6-60mo, Rate: 18% (risk-based), Amount: SAR 10k-500k, Early Closure: 1.5%, Admin: 1% |

---

## BL2: Feature Inventory

| Feature ID | Feature Name | Status | Sprint | Module | Description |
|---|---|---|---|---|---|
| F-01.1 | Mobile App Scaffold & Design System | ✅ Implemented | 1 | Mobile | Kotlin/Compose, Hilt, Navigation, Material Design 3, 9 atom components |
| F-01.2 | API Scaffold & Health Endpoints | ✅ Implemented | 1 | Backend | .NET 8 Clean Architecture, MediatR, EF Core, Serilog, Swagger, CI |
| F-01.3 | Customer Registration & Authentication | ✅ Implemented | 1 | Auth | Phone+OTP, biometric, JWT (15min/24hr), EncryptedSharedPreferences |
| F-01.4 | Product Catalogue & BridgeNow Config | ✅ Implemented | 1 | Products | Product entity, seed data, list screen, BridgeNow landing page |
| F-02.1 | Application Form | 📋 Sprint 2 | 2 | Applications | 4-step wizard, auto-save, personal/employment data capture |
| F-02.2 | Income Verification & Review | 📋 Sprint 2 | 2 | Applications | API income, Open Banking, offer amount calc, review+submit |
| F-03.1 | External Verifications | 📋 Sprint 2 | 2 | Assessment | CITC, geolocation, SIMAH integrations |
| F-03.2 | Decision Engine | 📋 Sprint 2 | 2 | Assessment | DBR 33%, 1x income cap, min SAR 4k, exposure limits |
| F-03.3 | Offer, Contract & Acceptance | 📋 Sprint 2 | 2 | Offers | Offer generation, Tawarruq contract, e-signature, cooling period |
| F-03.4 | Card Collection & Disbursement | 📋 Sprint 2 | 2 | Cards/Loans | Hosted fields, dual auto-debit, 2nd SIMAH, disbursement |
| F-04.1 | Loan Dashboard & Schedule | 📋 Sprint 3 | 3 | Loans | Dashboard, loan detail, repayment schedule, payment history |
| F-04.2 | Auto-Debit & Payment Processing | 📋 Sprint 3 | 3 | Payments | Execute AD, retry, failure handling, LPF, billing cycle |
| F-04.3 | Early Settlement Flow | 📋 Sprint 3 | 3 | Loans | Settlement figure, pay+close, liability letter |
| F-05.1 | Product Switch | 📋 Sprint 3 | 3 | Applications | BridgeNow→Cash/Combo, abandon+recreate, DE re-run |
| F-06.1 | Transactional Notifications | 📋 Sprint 3 | 3 | Notifications | Push+SMS for lifecycle events |
| F-06.2 | Marketing Communication | 📋 Sprint 3 | 3 | Marketing | Landing content, SMS templates, CMS |
| F-07.1 | Back Office App Management | 📋 Sprint 4 | 4 | BackOffice | BO app creation, product switch, non-STP tag |
| F-07.2 | Staging Application Log | 📋 Sprint 4 | 4 | BackOffice | CITC/geo failure capture, agent pick-up |
| F-08.1 | Top-Up Eligibility | 📋 Sprint 4 | 4 | Loans | Top-up as Cash Finance, DBR combined exposure |
| F-08.2 | Reporting & Hardening | 📋 Sprint 4 | 4 | Operations | MIS, monitoring, feature flags, observability, security |

Legend: 🔨 = Building this sprint | 📋 = Planned | ✅ = Live

---

## BL3: Screen Inventory (Mobile)

| Screen | Route | Feature | Sprint | Status |
|---|---|---|---|---|
| Login | login | F-01.3 | 1 | ✅ Implemented |
| OTP Verification | otp/{phone} | F-01.3 | 1 | ✅ Implemented |
| Product List | products | F-01.4 | 1 | ✅ Implemented |
| BridgeNow Landing | products/{id} | F-01.4 | 1 | ✅ Implemented |
| Personal Details | apply/{productId}/personal | F-02.1 | 2 | ✅ Designed |
| Employment Details | apply/{productId}/employment | F-02.1 | 2 | ✅ Designed |
| Income & Salary | apply/{productId}/income | F-02.2 | 2 | ✅ Designed |
| Review & Submit | apply/{productId}/review | F-02.1, F-02.2 | 2 | ✅ Designed |
| Application Status | application/{id}/status | F-03.1, F-03.2 | 2 | ✅ Designed |
| Offer Display | application/{id}/offer | F-03.3 | 2 | ✅ Designed |
| Contract & Signature | application/{id}/contract | F-03.3 | 2 | ✅ Designed |
| Card Collection | application/{id}/card | F-03.4 | 2 | ✅ Designed |
| Disbursement Confirmation | application/{id}/disbursement | F-03.4 | 2 | ✅ Designed |
| Dashboard | /dashboard | F-04.1 | 3 | 📋 |
| Loan Detail | /loans/{loanId} | F-04.1 | 3 | 📋 |
| Settlement | /loans/{loanId}/settle | F-04.3 | 3 | 📋 |

---

## BL4: API Inventory

| Method | Endpoint | Handler | Sprint | Status |
|---|---|---|---|---|
| POST | /api/v1/auth/register | RegisterCommand | 1 | ✅ |
| POST | /api/v1/auth/verify-otp | VerifyOtpCommand | 1 | ✅ |
| POST | /api/v1/auth/refresh | RefreshTokenCommand | 1 | ✅ |
| GET | /api/v1/products | GetProductsQuery | 1 | ✅ |
| GET | /api/v1/products/{id} | GetProductQuery | 1 | ✅ |
| GET | /health | HealthController | 1 | ✅ |
| GET | /health/ready | HealthController | 1 | ✅ |
| POST | /api/v1/applications | CreateApplicationCommand | 2 | ✅ Spec'd |
| GET | /api/v1/applications | ListApplicationsQuery | 2 | ✅ Spec'd |
| PUT | /api/v1/applications/{id} | UpdateApplicationCommand | 2 | ✅ Spec'd |
| GET | /api/v1/applications/{id} | GetApplicationQuery | 2 | ✅ Spec'd |
| POST | /api/v1/applications/{id}/submit | SubmitApplicationCommand | 2 | ✅ Spec'd |
| GET | /api/v1/applications/{id}/income | GetIncomeDataQuery | 2 | ✅ Spec'd |
| POST | /api/v1/applications/{id}/open-banking/initiate | InitiateOpenBankingCommand | 2 | ✅ Spec'd |
| POST | /api/v1/applications/{id}/open-banking/callback | OpenBankingCallbackCommand | 2 | ✅ Spec'd |
| POST | /api/v1/applications/{id}/assess | AssessApplicationCommand | 2 | ✅ Spec'd |
| POST | /api/v1/applications/{id}/offer/generate | GenerateOfferCommand | 2 | ✅ Spec'd |
| GET | /api/v1/applications/{id}/offer | GetOfferQuery | 2 | ✅ Spec'd |
| POST | /api/v1/applications/{id}/offer/accept | AcceptOfferCommand | 2 | ✅ Spec'd |
| POST | /api/v1/applications/{id}/offer/cancel | CancelOfferCommand | 2 | ✅ Spec'd |
| POST | /api/v1/applications/{id}/offer/decline | DeclineOfferCommand | 2 | ✅ Spec'd |
| GET | /api/v1/applications/{id}/contract | GetContractQuery | 2 | ✅ Spec'd |
| POST | /api/v1/applications/{id}/card | RegisterCardCommand | 2 | ✅ Spec'd |
| POST | /api/v1/applications/{id}/disburse | DisburseCommand | 2 | ✅ Spec'd |
| GET | /api/v1/applications/{id}/disbursement | GetDisbursementQuery | 2 | ✅ Spec'd |
| GET | /api/v1/loans | GetLoansQuery | 3 | 📋 |
| GET | /api/v1/loans/{id} | GetLoanQuery | 3 | 📋 |
| GET | /api/v1/loans/{id}/payments | GetPaymentsQuery | 3 | 📋 |
| GET | /api/v1/loans/{id}/settlement-figure | GetSettlementFigureQuery | 3 | 📋 |
| GET | /api/v1/loans/{id}/liability-letter | GetLiabilityLetterQuery | 3 | 📋 |
| POST | /api/v1/loans/{id}/settle | SettleLoanCommand | 3 | 📋 |
| POST | /api/v1/applications/{id}/switch | SwitchProductCommand | 3 | 📋 |
| GET | /api/v1/loans/{id}/topup-eligibility | GetTopUpEligibilityQuery | 4 | 📋 |
| POST | /api/v1/backoffice/applications | CreateBoApplicationCommand | 4 | 📋 |
| GET | /api/v1/backoffice/staging-log | GetStagingLogQuery | 4 | 📋 |

---

## BL5: Data Model (Tables)

### Sprint 1 Tables (✅ Implemented)

| Table | Key Columns | PII Encrypted | Sprint |
|---|---|---|---|
| Users | Id, PhoneNumber, Name, PasswordHash, BiometricEnabled, CreatedAt, UpdatedAt, IsDeleted | PhoneNumber (AES-256) | 1 |
| RefreshTokens | Id, UserId, Token, DeviceFingerprint, ExpiresAt, CreatedAt, IsRevoked | Token (AES-256) | 1 |
| OtpTokens | Id, PhoneNumber, OtpHash, ExpiresAt, AttemptCount, CreatedAt | PhoneNumber (AES-256) | 1 |
| Products | Id, Name, Type, MinAmount, MaxAmount, MinTenure, MaxTenure, BaseRate, EarlyClosureFeePercent, AdminFeePercent, IsActive | No | 1 |
| AuditLogs | Id, UserId, Action, Resource, ResourceId, Changes, Reason, Timestamp | No (append-only) | 1 |

### Sprint 2 Tables (📋 Planned)

| Table | Key Columns | PII Encrypted | Sprint |
|---|---|---|---|
| LoanApplications | Id, UserId, ProductId, Status, FullName, NationalId, DateOfBirth, Address, City, Region, EmployerName, EmploymentType, Income, GrossIncome, RequestedAmount, Tenure, ApprovedAmount, SalaryDate | NationalId, DateOfBirth, Income (AES-256) | 2 |
| Offers | Id, ApplicationId, Amount, Tenure, ProfitRate, AdminFee, MonthlyPayment, TotalAmount, ValidUntil | No | 2 |
| DebitCards | Id, ApplicationId, TokenReference, Last4Digits, SalaryDate, IsActive | TokenReference (encrypted) | 2 |
| AutoDebitSchedules | Id, LoanId, DebitCardId, ScheduledDate, Amount, Status | No | 2 |
| Contracts | Id, ApplicationId, OfferId, ContractType, SignedAt, CoolingPeriodEndsAt | No | 2 |

### Sprint 3 Tables (📋 Planned)

| Table | Key Columns | Sprint |
|---|---|---|
| Loans | Id, ApplicationId, OfferId, DisbursedAmount, OutstandingPrincipal, Status, DisbursedAt, NextPaymentDate | 3 |
| Payments | Id, LoanId, Amount, Type, Status, AttemptNumber, FailureReason, ProcessedAt | 3 |
| Notifications | Id, UserId, Type, Channel, TemplateId, Status, SentAt | 3 |

### Sprint 4 Tables (📋 Planned)

| Table | Key Columns | Sprint |
|---|---|---|
| StagingApplicationLog | Id, CustomerData (encrypted), FailureReason, FailureTimestamp, ApplicationData, Status, AssignedAgentId | 4 |

All tables include EA4 audit columns: CreatedAt, CreatedBy, UpdatedAt, UpdatedBy, IsDeleted, Version.

---

## BL6: Integration Inventory

| System | Protocol | Sprint | Status | Purpose |
|---|---|---|---|---|
| SMS Gateway | REST (fire-and-forget) | 1 | 🔨 Mock | OTP delivery |
| SIMAH (Credit Bureau) | REST (sync) | 2 | 📋 | Credit score + obligations |
| CITC (Employment) | REST (sync) | 2 | 📋 | Employment verification |
| Geolocation | REST (sync) | 2 | 📋 | Location verification |
| Open Banking (AISP) | REST + redirect | 2 | 📋 | Bank statement (unlisted companies) |
| Payment Processor | REST + webhooks | 2 | 📋 | Card tokenisation, auto-debit, disbursement |
| FCM (Push) | REST | 3 | 📋 | Push notifications |

---

## BL7: Known Limitations (Sprint 1)

| LIM ID | Description | Planned Resolution |
|---|---|---|
| LIM-01 | SMS gateway is mocked in Sprint 1 | Real integration in Sprint 2 |
| LIM-02 | In-memory database (no persistence across restarts) | SQL Server in staging/prod from Sprint 2 |
| LIM-03 | No application form — only product browsing | Sprint 2 delivers full application flow |
| LIM-04 | No loan servicing — no active loans yet | Sprint 3 delivers dashboard + payments |
| LIM-05 | No back office — no agent support | Sprint 4 delivers BO |
| LIM-06 | Web journey not available (app-only) | Deferred post-launch per CON-04 |
| LIM-07 | No feature flags — all features always on | Sprint 4 adds feature flag infrastructure |

---


---

## BL11: Design System Tokens (Sprint 1)

| Token | Value | Usage |
|-------|-------|-------|
| Primary | #4F6EF7 | Buttons, links, active states |
| Primary Hover | #3B5AE0 | Button hover |
| Primary Subtle | #EEF1FE | Hero banners, info banners, highlighted backgrounds |
| Neutral-100 | #EDEDF3 | Light backgrounds |
| Neutral-400 | #858AAD | Borders, muted text |
| Neutral-900 | #292C3D | Primary text |
| Success | #2ECC71 | Approved badges, success states |
| Warning | #F39C12 | Warning badges |
| Error | #E74C3C | Error states, declined badges |
| Success-subtle | #E8FAF0 | Status timeline success bg (Sprint 2) |
| Warning-subtle | #FEF3E2 | Status timeline warning bg (Sprint 2) |
| Error-subtle | #FDECEC | Status timeline error bg (Sprint 2) |
| Spacing | 4dp grid (4, 8, 12, 16, 20, 24, 32, 40) | All spacing |
| Radius-sm | 4dp | Buttons |
| Radius-md | 6dp | Cards |
| Radius-lg | 8dp | Inputs |
| Touch target | 48dp minimum | All interactive elements |
| Typography | Inter / system-ui | Font family |

Atom Components: TasheelButton (primary/secondary/text), TasheelTextField (default/focused/error/disabled), TasheelCard, StatusBadge, LoadingSkeleton, ErrorState

---

## BL12: Test Strategy

| Level | Framework | Target | Sprint 1 Scope |
|-------|-----------|--------|----------------|
| Unit (Backend) | xUnit + NSubstitute | ≥80% on Application handlers | Auth handlers, Product queries |
| Unit (Mobile) | JUnit 5 + MockK + Turbine | ≥80% on ViewModels | LoginViewModel, ProductListViewModel |
| Contract | Schema validation | All API response schemas | Auth + Product endpoints |
| Integration | WebApplicationFactory | Full request→response | Auth flow, product listing |
| UI (Mobile) | Compose UI Test | Screen rendering + interaction | Login screen, product list |
| Accessibility | TalkBack verification | All user-facing screens | Login, product list, landing page |

CI Pipeline: lint → build → unit test → integration test → coverage check (≥80%) → SAST (CodeQL) → SCA (Snyk) → Docker build → image scan (Trivy)

---

## BL13: API Standards

- Base URL: `/api/v1/{resource}`
- Response envelope: `{ "data": ..., "meta": { pageNumber, pageSize, totalCount } }`
- Errors: RFC 7807 ProblemDetails (type, title, status, detail, errors[])
- JSON: camelCase properties, ISO 8601 dates, enums as strings
- Headers: X-Correlation-Id (auto-generated), Idempotency-Key (on writes), Authorization (Bearer JWT)
- Pagination: pageNumber (1-based) + pageSize (max 100, default 20)
- Rate limiting: 100 read/min, 20 write/min per user

## BL8: Application State Machine

```
Draft → Submitted → Verifying → Approved → OfferGenerated → OfferAccepted → Disbursing → Active → Settled → Closed
                                    ↓                                                        ↓
                                 Referred                                                 InArrears
                                    
Side states: Abandoned (product switch)
```

### Sprint 1 States: None (no applications yet — state machine implemented in Sprint 2)

### Transition Rules (planned for Sprint 2+):

| From | To | Trigger | Sprint |
|------|-----|---------|--------|
| — | Draft | Application created | 2 |
| Draft | Submitted | Customer submits | 2 |
| Submitted | Verifying | Assessment starts | 2 |
| Verifying | Approved | All checks pass | 2 |
| Verifying | Referred | Any check fails | 2 |
| Approved | OfferGenerated | Offer created | 2 |
| OfferGenerated | OfferAccepted | Customer accepts | 2 |
| OfferAccepted | Disbursing | Card registered + 2nd SIMAH | 2 |
| Disbursing | Active | Funds confirmed | 2 |
| Active | Settled | Full balance paid | 3 |
| Active | InArrears | Payment missed | 3 |
| Settled | Closed | Post-settlement | 3 |
| Any | Abandoned | Product switch | 3 |

---

## BL9: Architecture Decisions Record

| ADR | Decision | Sprint | KB Reference |
|-----|----------|--------|-------------|
| ADR-01 | Clean Architecture + CQRS (MediatR) for backend | 1 | EA2 |
| ADR-02 | Kotlin/Jetpack Compose for mobile | 1 | EA1, EA10 |
| ADR-03 | JWT RS256 with device-bound refresh tokens | 1 | EA5 |
| ADR-04 | In-memory DB for dev, SQL Server for prod | 1 | EA4 |
| ADR-05 | Design system before features | 1 | EA11 |
| ADR-06 | Assessment service backend-only — mobile polls for status | 2 | EA2, EA8, MS6 |
| ADR-07 | PCI-DSS SAQ A via payment processor hosted fields | 2 | PD9, EA5, PD10 |
| ADR-08 | Offer calculation server-side only — no business logic on mobile | 2 | PD4, PD5, EA5 |
| ADR-09 | Cooling-off period tracked server-side UTC — tamper-proof | 2 | PD5, SAMA |
| ADR-10 | Separate database per service (applications-db, assessment-db, offers-db) | 2 | EA2, EA4, MS3 |

---

## BL10: Sprint Delivery Tracker

| Sprint | Version | Epics | Features | Stories | Points | Status |
|--------|---------|-------|----------|---------|--------|--------|
| 1 | v0.1 | EP-01 | 4 | 14 | 57 | ✅ Implemented |
| 2 | v0.2 | EP-02, EP-03 | 6 | 23 | 86 | 📋 Planned |
| 3 | v0.3 | EP-04, EP-05, EP-06 | 6 | 22 | 72 | 📋 Planned |
| 4 | v1.0 | EP-07, EP-08 | 4 | 14 | 45 | 📋 Planned |
| **Total** | | **8** | **20** | **73** | **260** | |

---

## BL14: UX/UI Artifacts

### Sprint 1 Artifacts

| Artifact | File | Screens Covered | Status |
|----------|------|----------------|--------|
| Wireframes | ep01-wireframes.md | Login, OTP, Product List, BridgeNow Landing (4 screens) | ✅ Sprint 1 |
| User Flows | ep01-user-flows.md | Registration flow, returning user flow, error recovery | ✅ Sprint 1 |
| Design System | ep01-design-system.md | 9 components | ✅ Sprint 1 |

### Sprint 2 Artifacts

| Artifact | File | Screens Covered | Status |
|----------|------|----------------|--------|
| Wireframes | ep02-ep03-wireframes.md | 9 new + 1 modified (Personal, Employment, Income, Review, Status, Offer, Contract, Card, Disbursement) | ✅ Sprint 2 |
| User Flows | ep02-ep03-user-flows.md | 4 flows: Application Submission, Assessment & Decision, Offer & Contract, Card & Disbursement | ✅ Sprint 2 |
| Design System | ep02-ep03-design-system.md | 11 new components: StepperIndicator, VerticalTimeline, SummaryCard, OfferBreakdownCard, CountdownTimer, HostedFieldContainer, AutoSaveIndicator, DatePicker, TasheelDropdown, TasheelCheckbox, TasheelDialog | ✅ Sprint 2 |

### Component Inventory (Sprint 1 + Sprint 2 = 17 total)

| Component | Type | Sprint | Screens Used |
|-----------|------|--------|-------------|
| TasheelButton | Atom | 1 | All screens |
| TasheelTextField | Atom | 1 | Login, Personal, Employment, Income |
| TasheelCard | Atom | 1 | Products, Detail, Review, Offer, Disbursement |
| StatusBadge | Atom | 1 | Products, Status |
| LoadingSkeleton | Atom | 1 | All data-fetching screens |
| ErrorState | Molecule | 1 | All data-fetching screens |
| OtpDigitInput | Molecule | 1 | OTP |
| BottomNavBar | Organism | 1 | Products |
| HandleUiState | Molecule | 1 | All data-fetching screens |
| StepperIndicator | Molecule | 2 | Personal, Employment, Income, Review |
| VerticalTimeline | Molecule | 2 | Application Status |
| SummaryCard | Molecule | 2 | Review |
| OfferBreakdownCard | Molecule | 2 | Offer, Disbursement |
| CountdownTimer | Atom | 2 | Contract (cooling-off) |
| HostedFieldContainer | Organism | 2 | Card Collection |
| AutoSaveIndicator | Atom | 2 | Personal, Employment, Income |
| DatePicker | Atom | 2 | Personal (DOB), Income (salary date) |
| TasheelDropdown | Atom | 2 | Personal (Region), Employment (Type), Income (salary date) |
| TasheelCheckbox | Atom | 2 | Review (confirmation) |
| TasheelDialog | Molecule | 2 | Offer (decline confirm), Contract (cancel confirm) |

---

## BL15: API Specifications — Per Service (Sprint 1)

**Principle**: One OpenAPI spec per microservice (per EA2 Microservices-First Architecture).

| Service | Spec File | Port (dev) | Endpoints | Status |
|---------|-----------|-----------|-----------|--------|
| auth-service | ep01-auth-service-api.yaml | 5001 | 5 (3 auth + 2 health) | ✅ Sprint 1 |
| products-service | ep01-products-service-api.yaml | 5002 | 4 (2 products + 2 health) | ✅ Sprint 1 |
| applications-service | ep02-applications-service-api.yaml | 5003 | 10 (8 app + 2 health) | ✅ Sprint 2 |
| assessment-service | ep02-assessment-service-api.yaml | 5004 | 3 (1 assess + 2 health) | ✅ Sprint 2 |
| offers-service | ep02-offers-service-api.yaml | 5005 | 11 (9 offer/card/disburse + 2 health) | ✅ Sprint 2 |
| loans-service | — | 5006 | — | 📋 Sprint 3 |
| notifications-service | — | 5007 | — | 📋 Sprint 3 |
| backoffice-service | — | 5008 | — | 📋 Sprint 4 |

### Auth Service Endpoints (ep01-auth-service-api.yaml)

| Method | Path | Auth | Sensitivity |
|--------|------|------|-------------|
| POST | /auth/register | Public | Confidential |
| POST | /auth/verify-otp | Public | Restricted |
| POST | /auth/refresh | Bearer | Restricted |
| GET | /health | Public | Public |
| GET | /health/ready | Public | Public |

### Products Service Endpoints (ep01-products-service-api.yaml)

| Method | Path | Auth | Sensitivity |
|--------|------|------|-------------|
| GET | /products | Public | Public |
| GET | /products/{id} | Public | Public |
| GET | /health | Public | Public |
| GET | /health/ready | Public | Public |

### Standards: RFC 7807, camelCase, X-Correlation-Id, Idempotency-Key on writes, Bearer JWT

| Artifact | File | Endpoints | Status |
|----------|------|-----------|--------|
| OpenAPI 3.0 Spec | ep01-api-spec.yaml | 7 endpoints (3 auth + 2 products + 2 health) | ✅ Sprint 1 |

### Endpoint Summary

| Method | Path | Auth | Sensitivity | Schema |
|--------|------|------|-------------|--------|
| POST | /auth/register | Public | Confidential | → OTP sent confirmation |
| POST | /auth/verify-otp | Public | Restricted | → TokenResponse |
| POST | /auth/refresh | Bearer | Restricted | → TokenResponse |
| GET | /products | Public | Public | → Product[] |
| GET | /products/{id} | Public | Public | → Product |
| GET | /health | Public | Public | → status |
| GET | /health/ready | Public | Public | → status + checks |

### Schemas: TokenResponse, Product, ProblemDetails
### Error Responses: 400 (ValidationError), 401 (Unauthorized), 404 (NotFound), 429 (RateLimited), 500 (InternalError)
### Standards: RFC 7807 errors, camelCase JSON, X-Correlation-Id, Idempotency-Key on writes, Bearer JWT (RS256, 15min)

---

## BL16: Architecture Documents

### Sprint 1

| Document | File | Content | Status |
|----------|------|---------|--------|
| Solution Architecture | ep01-solution-architecture.md | System context, bounded contexts, tech stack, security, deployment, data, ADRs | ✅ Sprint 1 |
| Integration Architecture | ep01-integration-architecture.md | Integration diagram, SMS gateway, event topics, circuit breakers, API gateway, observability | ✅ Sprint 1 |
| Auth Service API Spec | ep01-auth-service-api.yaml | 5 endpoints (register, verify-otp, refresh, health×2) | ✅ Sprint 1 |
| Products Service API Spec | ep01-products-service-api.yaml | 4 endpoints (list, detail, health×2) | ✅ Sprint 1 |
| UX Wireframes | ep01-wireframes.md + .html | 4 screens (Login, OTP, Product List, BridgeNow Landing) | ✅ Sprint 1 |
| User Flows | ep01-user-flows.md | Registration flow, returning user flow, error recovery | ✅ Sprint 1 |
| Design System | ep01-design-system.md | 9 components, tokens, typography, spacing | ✅ Sprint 1 |

### Sprint 2

| Document | File | Content | Status |
|----------|------|---------|--------|
| Validation | greenfield-ep02-ep03-validation.json | EP-02/EP-03 validation: PASS, 23 stories, 98 ACs, 6 warnings | ✅ Sprint 2 |
| Discovery | greenfield-ep02-ep03-discovery.json | Change impact: 3 new services, 9 screens, 24 APIs, 7 integrations, 5 ADRs | ✅ Sprint 2 |
| Solution Architecture | ep01-solution-architecture.md (appended) | +5 bounded contexts, +6 tables, +5 ADRs, +7 security items, deployment model | ✅ Sprint 2 |
| Integration Architecture | ep01-integration-architecture.md (appended) | +7 integrations, +9 events, +7 inter-service calls, +6 circuit breakers, gateway routes | ✅ Sprint 2 |
| UX Wireframes | ep02-ep03-wireframes.md + .html | 9 new + 1 modified screen (E–M), loading/error/success states, FLAG_SECURE, A11Y | ✅ Sprint 2 |
| User Flows | ep02-ep03-user-flows.md | 4 flows: Application, Assessment, Offer/Contract, Card/Disbursement | ✅ Sprint 2 |
| Design System | ep02-ep03-design-system.md | 11 new components, 3 new color tokens, 5 new dimension tokens | ✅ Sprint 2 |
| Applications API Spec | ep02-applications-service-api.yaml | 10 endpoints (8 app + 2 health), port 5003 | ✅ Sprint 2 |
| Assessment API Spec | ep02-assessment-service-api.yaml | 3 endpoints (1 assess + 2 health), port 5004 | ✅ Sprint 2 |
| Offers API Spec | ep02-offers-service-api.yaml | 11 endpoints (9 offer/card/disburse + 2 health), port 5005 | ✅ Sprint 2 |
| LLD | ep01-lld.md (appended) | 6 entities, 6 tables, 18 handlers, 13 state transitions, 6 ViewModels, 16 test classes | ✅ Sprint 2 |

---

## BL17: Low-Level Design

### Sprint 1

| Artifact | File | Content | Status |
|----------|------|---------|--------|
| LLD | ep01-lld.md | Domain model, DB schemas (5 tables), CQRS handlers (6), mobile architecture (4 ViewModels, 2 repos, 2 APIs), DI modules, testing plan, seed data | ✅ Sprint 1 |

### Sprint 2 (appended to ep01-lld.md)

| Section | Content | Status |
|---------|---------|--------|
| 1a. Domain Model | 6 new entities (LoanApplication, AssessmentResult, Offer, Contract, DebitCard, Disbursement), 9 enums, Mermaid class diagram | ✅ Sprint 2 |
| 2a. Database Schemas | 6 new tables + 1 ALTER (Users.NationalId), all with EA4 audit columns, PII encrypted, indexes defined | ✅ Sprint 2 |
| 3a. CQRS Handlers | 18 handlers (8 applications-svc, 1 assessment-svc, 9 offers-svc), DE pseudocode, offer calculation | ✅ Sprint 2 |
| 4a. Mobile Architecture | 6 new ViewModels, 2 new repositories, 2 new Retrofit APIs (15 endpoints) | ✅ Sprint 2 |
| 10a. State Machine | 13 transitions covering Draft→Active lifecycle, cooling-off cancel, 2nd SIMAH decline | ✅ Sprint 2 |
| 6a. Testing Plan | 10 backend test classes, 6 mobile test classes | ✅ Sprint 2 |

### Handler Inventory

| Service | Handler | Type | Endpoint |
|---------|---------|------|----------|
| auth-service | RegisterCommand | Command | POST /auth/register |
| auth-service | VerifyOtpCommand | Command | POST /auth/verify-otp |
| auth-service | RefreshTokenCommand | Command | POST /auth/refresh |
| products-service | GetProductsQuery | Query | GET /products |
| products-service | GetProductQuery | Query | GET /products/{id} |

### Mobile ViewModel Inventory

| ViewModel | Screen | State Type |
|-----------|--------|-----------|
| LoginViewModel | Login | LoginUiState (phone, phoneError, isLoading, errorMessage) |
| OtpViewModel | OTP | OtpUiState (otp, countdown, isLoading, isVerified, errorMessage, errorType) |
| ProductListViewModel | Product List | UiState<List<Product>> |
| ProductDetailViewModel | BridgeNow Landing | UiState<Product> |

---

## BL18: Test Artifacts (Sprint 1)

### Automated Tests (BDD/Cucumber)

| Feature File | Scenarios | Tags | Coverage |
|-------------|-----------|------|----------|
| auth-registration.feature | 10 | @regression @sprint1 | US-005 to US-008, US-014 |
| products-catalogue.feature | 8 | @regression @sprint1 | US-009 to US-012 |
| health-endpoints.feature | 4 | @smoke @sprint1 | US-004 |
| api-standards.feature | 6 | @regression @sprint1 | US-013 |
| security.feature | 6 | @regression @security | US-007, US-008, US-014 |
| **Total** | **34** | | **14/14 stories covered** |

Run: `cd tests/automated && npm install && npm test`

### Manual Test Cases (XRay Format)

| Document | Test Cases | Coverage |
|----------|-----------|----------|
| ep01-manual-test-cases.md | 30 (TC-001 to TC-030) | 51/51 ACs covered |
| ep01-test-matrix.md | Story → AC → TC mapping | 100% coverage |
| ep01-regression-pack.md | 30 regression tests | Critical: 13, High: 13, Medium: 4 |

### Regression Pack (Sprint 1 Baseline)

- **Automated**: 34 scenarios (run in CI on every deployment)
- **Manual**: 30 test cases (~3.5 hours, 1 tester)
- **Smoke**: 5 critical tests (~23 min)
- **Growth**: New tests added each sprint, existing tests never removed

---

## BL19: Implementation Code

### Sprint 1

| Service | Path | Port | Source Files | Test Files | Status |
|---------|------|------|-------------|-----------|--------|
| auth-service | code/auth-service/ | 5001 | 31 .cs files | 4 .cs files (unit + contract) | ✅ Audited |
| products-service | code/products-service/ | 5002 | 17 .cs files | 3 .cs files (unit + contract) | ✅ Audited |

### Sprint 2

| Service | Path | Port | Source Files | Test Files | Unit Tests | Contract Tests | Status |
|---------|------|------|-------------|-----------|-----------|---------------|--------|
| applications-service | code/applications-service/ | 5003 | 26 .cs | 5 .cs | 12 | 4 | ✅ Generated |
| assessment-service | code/assessment-service/ | 5004 | 23 .cs | 2 .cs | 6 | 3 | ✅ Generated |
| offers-service | code/offers-service/ | 5005 | 49 .cs | 6 .cs | 14 | 5 | ✅ Generated |
| **Sprint 2 Total** | | | **98 .cs** | **13 .cs** | **32** | **12** | |

### Auth Service Structure
```
auth-service/
├── src/AuthService.Domain/        (entities, interfaces — incl. GetByIdAsync)
├── src/AuthService.Application/   (commands, 3 validators, behaviours, DTOs)
├── src/AuthService.Infrastructure/ (EF Core, repositories, JWT, SMS mock)
├── src/AuthService.Api/           (controllers, middleware, Program.cs)
├── tests/AuthService.UnitTests/   (15 handler tests)
└── tests/AuthService.ContractTests/ (7 API contract tests)
```

### Products Service Structure
```
products-service/
├── src/ProductsService.Domain/        (entities, enums, interfaces)
├── src/ProductsService.Application/   (queries, DTOs, behaviours)
├── src/ProductsService.Infrastructure/ (EF Core, repositories, seed data w/ IgnoreQueryFilters)
├── src/ProductsService.Api/           (controllers, middleware, Program.cs)
├── tests/ProductsService.UnitTests/   (7 handler tests)
└── tests/ProductsService.ContractTests/ (5 API contract tests)
```

### Test Coverage (34 total tests)
- **Auth unit tests** (15): RegisterCommand (3), VerifyOtpCommand (6 — incl. new user, null OTP), RefreshTokenCommand (6 — incl. invalid token, user not found)
- **Auth contract tests** (7): Register, invalid phone, verify-otp invalid, verify-otp wrong code, refresh invalid, refresh empty, correlation ID
- **Products unit tests** (7): GetProductsQuery (4), GetProductQuery (3 — incl. full DTO mapping)
- **Products contract tests** (5): Response schemas (13 fields incl. tenure), seed data, 404 format

### Audit Fixes Applied
1. **Logic bug fixed**: RefreshTokenCommandHandler now uses `GetByIdAsync` (was passing GUID as phone to `GetByPhoneAsync`)
2. **Missing validator added**: `RefreshTokenCommandValidator` (RefreshToken + DeviceFingerprint not-empty)
3. **Interface extended**: `IUserRepository.GetByIdAsync` + `UserRepository` implementation
4. **DataSeeder hardened**: Uses `IgnoreQueryFilters().Any()` to prevent duplicate seeding
5. **Contract test schema**: Added `minTenureMonths`/`maxTenureMonths` to required fields check
6. **Test isolation**: Auth contract tests use unique phone numbers per test (no rate-limit collisions)
7. **NuGet aligned**: All packages consistent across services (FluentValidation 11.11.0, EF Core 8.0.11, Serilog 8.0.3)
8. **Dead code removed**: Unused `System.Text.Json` using in ExceptionMiddleware

---

## BL20: Mobile App Code

### Sprint 1

| Metric | Value |
|--------|-------|
| Kotlin Source | 53 |
| Kotlin Tests | 4 (25 test cases) |
| Screens | 4 (Login, OTP, ProductList, ProductDetail) |
| Components | 9 shared |

### Sprint 2

| Metric | Value |
|--------|-------|
| New Kotlin Source | 42 (95 total) |
| New Kotlin Tests | 6 (10 total, 47 test cases) |
| New Screens | 8 (ApplicationForm 4-step, ApplicationStatus, Offer, Contract, CardCollection, Disbursement) |
| New Components | 3 (StepperIndicator, VerticalTimeline, CountdownTimer — 12 total) |
| New DTOs | 12 |
| New API Interfaces | 2 (TasheelApplicationsApi, TasheelOffersApi) |
| New Domain Models | 7 (LoanApplication, IncomeData, Offer, Contract, DebitCard, Disbursement, OpenBankingRedirect) |
| New Repositories | 2 (ApplicationRepository, OfferRepository) |
| Modified Files | 6 (Screen.kt, NavGraph.kt, NetworkModule.kt, RepositoryModule.kt, build.gradle.kts, strings.xml) |
| Total Strings | 98 (53 Sprint 1 + 45 Sprint 2) |

### Architecture (MVVM + Clean Architecture)
```
mobile-app/
├── settings.gradle.kts, build.gradle.kts, gradle/libs.versions.toml
├── app/build.gradle.kts, proguard-rules.pro
├── app/src/main/
│   ├── AndroidManifest.xml, res/values/strings.xml
│   └── java/com/tasheel/app/
│       ├── TasheelApp.kt (@HiltAndroidApp)
│       ├── MainActivity.kt (@AndroidEntryPoint)
│       ├── core/ (UiState, Constants, di/NetworkModule, DatabaseModule, RepositoryModule, SecurityModule)
│       ├── data/
│       │   ├── api/ (AuthApi, ProductsApi, dto/8 DTOs, interceptor/AuthInterceptor, CorrelationIdInterceptor)
│       │   ├── db/ (ProductCacheEntity, ProductCacheDao, TasheelDatabase)
│       │   └── repository/ (AuthRepositoryImpl, ProductRepositoryImpl)
│       ├── domain/
│       │   ├── model/ (Product, OtpResult, AuthToken)
│       │   └── repository/ (AuthRepository, ProductRepository interfaces)
│       └── ui/
│           ├── theme/ (Color, Type, Dimens, Theme — light+dark)
│           ├── navigation/ (Screen sealed class, NavGraph)
│           ├── components/ (9 shared composables)
│           └── features/ (login/, otp/, products/)
└── app/src/test/ (4 ViewModel test files)
```

### Technology Stack (Pinned Versions)
| Technology | Version | Purpose |
|-----------|---------|---------|
| Kotlin | 2.0.0 | Language |
| Compose BOM | 2024.02.00 | UI framework |
| Hilt | 2.51 | DI |
| Navigation Compose | 2.7.7 | Navigation |
| Room | 2.6.1 | Local cache |
| Retrofit | 2.9.0 | HTTP client |
| OkHttp | 4.12.0 | HTTP transport |
| JUnit 5 | 5.10.2 | Testing |
| MockK | 1.13.10 | Mocking |
| Turbine | 1.1.0 | Flow testing |
| Truth | 1.4.2 | Assertions |

### Design System Alignment
| Token | Value | Source |
|-------|-------|--------|
| Primary | #4F6EF7 | ep01-design-system.md |
| Neutral-900 | #292C3D | ep01-design-system.md |
| Border-input | #858AAD | ep01-design-system.md |
| Radius-sm/md/lg | 4/6/8dp | ep01-design-system.md |
| Touch target | 48dp | MC3, UC3 |
| Spacing grid | 4dp base | KS12 |

### Test Coverage (25 tests)
- **LoginViewModelTest** (6): initial state, phone change, short phone error, valid OTP send, repo failure, loading state
- **OtpViewModelTest** (10): initial state, OTP change, clear errors, short OTP guard, verify success, verify invalid, verify expired, verify rate-limited, resend success, resend failure
- **ProductListViewModelTest** (6): success, empty, error, repository order, init call, reload
- **ProductDetailViewModelTest** (3): found, not found, reload

### KB Standards Applied
| KB | Standards | Applied To |
|----|----------|-----------|
| kb-L0-ui-coding-standards | UC1-UC12 | Component architecture, accessibility, error/loading/empty states, design tokens |
| kb-L0-mobile-coding-standards | MC1-MC13 | MVVM, UiState, 48dp targets, offline-first, i18n, security |
| kb-L1-kotlin-compose-standards | KS1-KS17 | Project structure, screen/ViewModel patterns, Hilt DI, Room, Retrofit, testing |
| kb-L1-kotlin-compose-scaffold | KF1-KF15 | Build config, version catalog, CI pipeline, ProGuard, security module |

### Audit Fixes Applied
1. **CorrelationIdInterceptor**: Added `@Inject constructor()` for Hilt injection
2. **AuthInterceptor**: Removed duplicate `X-Correlation-Id` header (CorrelationIdInterceptor handles it)
3. **NetworkModule**: Uses `BuildConfig.AUTH_BASE_URL` and `BuildConfig.PRODUCTS_BASE_URL` (was single `BASE_URL`)
4. **All screens**: Changed `collectAsState()` to `collectAsStateWithLifecycle()` for lifecycle awareness
5. **All tests**: Rewritten to match actual ViewModel APIs (LoginUiState, OtpUiState, UiState<T>)

### Wireframe Alignment Fixes
6. **LoginScreen**: Added +966 prefix via `leadingContent`, conditional biometric (`isBiometricAvailable`), divider "or sign in with" text, phone digit filter (9 chars max), subtitle text
7. **OtpScreen**: Added TopAppBar with back navigation (`onBack`), verify disabled when <6 digits, countdown in mm:ss format, "Didn't receive code?" helper text, masked phone "+966 •••••XXXX"
8. **ProductListScreen**: Added TopAppBar "Our Products" with ☰ menu and 🔔 notification icons, BridgeNow badge shows `keySellingPoint`, structured card data (amount range, tenure, rate with "p.a."), empty state, case-insensitive type comparison, product-type icons (⭐🏦💳)
9. **ProductDetailScreen**: Hero shows product name as headline with `keySellingPoint` as subtitle, "Product Details" section title, early close "FREE ✅" when 0%, values formatted with "p.a." / "+ VAT", loan amount "1x Income (max SAR 30k)" for BridgeNow, info banner "Your offer amount will be calculated based on your verified income", dynamic "Apply Now" button text
10. **NavGraph**: Wired `onBack` to OtpScreen
11. **TasheelTextField**: Added `leadingContent` parameter for prefix display
12. **strings.xml**: Added 10 new strings (login_subtitle, login_divider, phone_prefix, otp_didnt_receive, products_empty, product_details_title, nav_home/products/profile, navigate_back) — total 39 strings

---

## BL21: Mock Server & Web Preview (Sprint 1 — Dev Tooling)

| File | Purpose |
|------|---------|
| code/mock-server/server.js | Node.js mock API: auth-service (:5001) + products-service (:5002) |
| code/mock-server/index.html | Web preview of all 4 EP-01 screens with live API calls |

### Mock Server Features
- **Auth endpoints**: POST /auth/register (OTP generation, rate limiting 3/10min), POST /auth/verify-otp (hash comparison, 5 attempt max, 90s expiry), POST /auth/refresh (token rotation)
- **Products endpoints**: GET /products (3 seed products, BridgeNow first, optional ?type= filter), GET /products/{id} (404 for unknown)
- **Standards**: RFC 7807 ProblemDetails, X-Correlation-Id echo, `{data:...}` envelope
- **Dev helper**: OTP code returned in `_dev.otpCode` field and displayed in UI console

### Web Preview Features
- Side-by-side layout: phone frame (390×844) + API console
- All 4 screens with actual design tokens (#4F6EF7, #858AAD, etc.)
- Live API calls to mock server with request/response logging
- OTP auto-advance, countdown timer, error states, loading skeletons

### Run Commands
```bash
cd code/mock-server
node server.js &          # Starts auth :5001 + products :5002
open index.html           # Opens web preview in browser
```
