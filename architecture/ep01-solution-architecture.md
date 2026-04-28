# BridgeNow Finance — Solution Architecture
### Sprint 1 (EP-01: Platform Foundation & Authentication)

---

## 1. System Context

```
┌─────────────────────────────────────────────────────────────────────┐
│                        EXTERNAL SYSTEMS                              │
├──────────┬──────────┬──────────┬──────────┬──────────┬─────────────┤
│  SIMAH   │   CITC   │  Geo     │  Open    │ Payment  │    SMS      │
│ (S2)     │ (S2)     │ (S2)     │ Banking  │Processor │  Gateway    │
│          │          │          │ (S2)     │ (S2)     │  (S1 mock)  │
└──────────┴──────────┴──────────┴──────────┴──────────┴─────────────┘
                                 │
┌────────────────────────────────▼────────────────────────────────────┐
│                    API GATEWAY (AKS Ingress)                         │
│              JWT validation, routing, rate limiting                  │
└────────┬───────────────────────────────────┬────────────────────────┘
         │                                   │
┌────────▼────────┐               ┌──────────▼──────────┐
│  auth-service   │               │ products-service    │
│  :5001          │               │ :5002               │
│  ┌────────────┐ │               │ ┌────────────┐      │
│  │ Register   │ │               │ │ List       │      │
│  │ Verify OTP │ │               │ │ Detail     │      │
│  │ Token      │ │               │ └────────────┘      │
│  │ Refresh    │ │               │        │            │
│  └────────────┘ │               │ ┌──────▼─────┐      │
│        │        │               │ │ Products   │      │
│ ┌──────▼─────┐  │               │ │ DB (SQL)   │      │
│ │ Users DB   │  │               │ └────────────┘      │
│ │ OTP Tokens │  │               └─────────────────────┘
│ │ Sessions   │  │
│ └────────────┘  │
│        │        │
│ ┌──────▼─────┐  │
│ │ SMS Gateway│  │  (mock in Sprint 1)
│ └────────────┘  │
└─────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│                    MOBILE APP (Android)                              │
│  Kotlin 2.0 + Jetpack Compose + Material Design 3                   │
│  Screens: Login → OTP → Product List → BridgeNow Landing            │
│  Hilt DI · Navigation Compose · Room (offline) · Retrofit           │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Bounded Contexts (Sprint 1)

| Context | Service | Responsibility | Entities | DB |
|---------|---------|---------------|----------|-----|
| **Auth** | auth-service :5001 | Registration, OTP, JWT, biometric | User, OtpToken, RefreshToken | auth_db |
| **Products** | products-service :5002 | Product catalogue, configuration | Product | products_db |

Future sprints add: Applications (:5003), Assessment (:5004), Offers (:5005), Loans (:5006), Notifications (:5007), BackOffice (:5008).

---

## 3. Technology Decisions

| Layer | Technology | Version | EA Reference |
|-------|-----------|---------|-------------|
| Mobile | Kotlin + Jetpack Compose | 2.0 / BOM 2024.02 | EA1, EA10 |
| Mobile DI | Hilt | 2.51 | EA10 |
| Mobile DB | Room | 2.6.1 | EA10 |
| Mobile Network | Retrofit + OkHttp | 2.9.0 / 4.12.0 | EA10 |
| Backend | C# / .NET 8 Web API | 12 / 8.0 | EA1 |
| Backend Pattern | Clean Architecture + CQRS (MediatR) | 12.4.1 | EA2 |
| Backend Validation | FluentValidation | 11.3.0 | EA2 |
| Backend Logging | Serilog | 8.0.2 | EA9 |
| Database | SQL Server (In-Memory for dev) | 2022 / 8.0.10 | EA4 |
| API Docs | Swashbuckle (OpenAPI 3.0) | 6.8.1 | EA3 |
| Auth | JWT RS256 (15min access, 24hr refresh) | — | EA5 |
| Container | Docker (Alpine) | — | EA6 |
| CI/CD | GitHub Actions | — | EA6 |
| Observability | OpenTelemetry | 1.9.0 | EA9 |

---

## 4. Security Architecture (Sprint 1)

| Aspect | Implementation | EA Reference |
|--------|---------------|-------------|
| Registration | Phone + 6-digit OTP (90s expiry, 3/10min rate limit) | EA5 |
| Authentication | JWT RS256 access token (15-min), refresh token (24hr, device-bound) | EA5 |
| Token Storage | EncryptedSharedPreferences (Android Keystore) | EA5, EA10 |
| Biometric | AndroidX BiometricPrompt, credential in Keystore | EA5 |
| Data at Rest | PII encrypted AES-256 (PhoneNumber, OTP hash) | EA4 |
| Data in Transit | TLS 1.2+ mandatory | EA5 |
| API Security | Idempotency-Key on writes, X-Correlation-Id, rate limiting | EA3 |
| Sensitive Screens | FLAG_SECURE on Login and OTP screens | EA5 |
| No PII in Logs | Serilog destructuring exclusions | EA9 |

---

## 5. Deployment Model (Sprint 1)

```
GitHub Actions CI Pipeline:
  lint → build → unit test → integration test → coverage ≥80%
  → SAST (CodeQL) → SCA (Snyk) → Docker build → Trivy scan → push

AKS Cluster:
  ├── Namespace: bridgenow-dev
  │   ├── auth-service (2 replicas, :5001)
  │   ├── products-service (2 replicas, :5002)
  │   └── ingress (API Gateway)
  └── Namespace: bridgenow-staging (same topology)

Docker: Alpine base, non-root, <200MB, HEALTHCHECK
```

---

## 6. Data Architecture (Sprint 1)

### auth_db
| Table | Key Columns | Encrypted |
|-------|------------|-----------|
| Users | Id, PhoneNumber, Name, PasswordHash, BiometricEnabled | PhoneNumber |
| OtpTokens | Id, PhoneNumber, OtpHash, ExpiresAt, AttemptCount | PhoneNumber |
| RefreshTokens | Id, UserId, Token, DeviceFingerprint, ExpiresAt, IsRevoked | Token |
| AuditLogs | Id, UserId, Action, Resource, ResourceId, Timestamp | — |

### products_db
| Table | Key Columns | Encrypted |
|-------|------------|-----------|
| Products | Id, Name, Type, MinAmount, MaxAmount, Tenure, Rate, Fees, IsActive | — |

All tables: EA4 audit columns (CreatedAt, CreatedBy, UpdatedAt, UpdatedBy, IsDeleted, Version).

---

## 7. ADR Summary

| ADR | Decision | Rationale |
|-----|----------|-----------|
| ADR-01 | Microservices-first (one service per bounded context) | Independent deployment, scaling, team ownership per EA2 |
| ADR-02 | Clean Architecture + CQRS per service | Testability, separation of concerns per EA2 |
| ADR-03 | Kotlin/Compose for mobile | Modern UI, accessibility, per EA1/EA10 |
| ADR-04 | JWT RS256 device-bound | Short-lived tokens, device binding per EA5 |
| ADR-05 | Design system before features | Unblocks all UI work per EA11 |
| ADR-06 | Separate API spec per service | API-first, independent evolution per EA2/EA3 |

---

## 8. API Standards (per EA3)

All services follow these standards:
- **Response envelope**: `{ "data": ... }` for success, RFC 7807 ProblemDetails for errors
- **JSON conventions**: camelCase properties, ISO 8601 dates, enums as strings
- **Headers**: X-Correlation-Id (auto-generated), Idempotency-Key (on POST/PUT/PATCH)
- **Pagination**: pageNumber (1-based) + pageSize (max 100, default 20)
- **Rate limiting**: 100 read/min, 20 write/min per user (429 with Retry-After)
- **Versioning**: URL path `/api/v1/`, breaking changes require new version
- **Error codes**: 400 (validation), 401 (auth), 404 (not found), 422 (business rule), 429 (rate limit), 500 (server)
- **Documentation**: OpenAPI 3.0.3 spec per service, Swagger UI in dev

---

# Sprint 2 Additions (EP-02: Application & Data Capture, EP-03: Assessment, Offer & Disbursement)

## 2a. Bounded Contexts [EP-02/EP-03]

| Context | Service | Port | Responsibility | Key Entities | DB |
|---------|---------|------|---------------|-------------|-----|
| Auth | auth-service | 5001 | Registration, OTP, JWT | User, OtpToken, RefreshToken | auth-db |
| Products | products-service | 5002 | Product catalogue, config | Product | products-db |
| **Applications** [EP-02] | applications-service | 5003 | Application lifecycle, data capture, income verification | LoanApplication | applications-db |
| **Assessment** [EP-03] | assessment-service | 5004 | CITC, geolocation, SIMAH, decision engine | AssessmentResult | assessment-db |
| **Offers** [EP-03] | offers-service | 5005 | Offer generation, contracts, card collection, disbursement | Offer, Contract, DebitCard, Disbursement | offers-db |

### Inter-Service Dependencies [EP-02/EP-03]
- applications-service → auth-service (JWT validation via API Gateway)
- applications-service → products-service (product config for eligibility)
- applications-service → assessment-service (triggers assessment after submit — ADR-06)
- offers-service → assessment-service (reads assessment result for offer generation)
- offers-service → applications-service (updates application status on disbursement)

## 4a. Security Architecture [EP-02/EP-03]

| Aspect | Implementation | EA/PD Reference |
|--------|---------------|-----------------|
| **PII Encryption** [EP-02] | NationalId, DOB, Income encrypted AES-256 at rest in applications-db | EA5, PD14 |
| **Card Data** [EP-03] | PCI-DSS SAQ A — card PAN never touches BridgeNow; hosted fields SDK only; tokenised reference stored encrypted | EA5, PD9, ADR-07 |
| **3D Secure** [EP-03] | SCA via payment processor 3DS 2.0 for card verification | PD10 |
| **Contract Signing** [EP-03] | E-signature captured (typed name); timestamp + device fingerprint stored; FLAG_SECURE on contract screen | EA5, PD5 |
| **Cooling-Off** [EP-03] | 24hr SAMA cooling-off enforced server-side UTC; client timer informational only | ADR-09, SAMA |
| **FLAG_SECURE** [EP-02/EP-03] | Enabled on: Personal Details, Income, Review, Offer, Contract, Card Collection screens | EA5 |
| **Terms Consent** [EP-02] | termsAccepted boolean + timestamp captured at submission; audit-logged | PD12, SAMA |

## 5a. Deployment Model [EP-02/EP-03]

```
┌─────────────────────────────────────────────────────────────────┐
│                    AKS Cluster (bridgenow-dev)                   │
│                                                                   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │ auth-service  │  │ products-svc │  │ applications │  Sprint 1│
│  │    :5001      │  │    :5002     │  │    :5003     │  + new   │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
│  ┌──────────────┐  ┌──────────────┐                             │
│  │ assessment   │  │ offers-svc   │  Sprint 2 new               │
│  │    :5004     │  │    :5005     │                              │
│  └──────────────┘  └──────────────┘                             │
│                                                                   │
│  Each service: 2 replicas, 256Mi-512Mi, health probes            │
│  Docker Alpine, non-root, <200MB, readiness + liveness           │
└─────────────────────────────────────────────────────────────────┘
```

## 6a. Data Architecture [EP-02/EP-03]

### applications-db [EP-02]

| Table | Key Columns | Encrypted |
|-------|-------------|-----------|
| LoanApplications | Id, UserId(FK→Users), ProductId(FK→Products), Status(enum 10 states), FullName, NationalId, DOB, Address, City, Region, EmployerName, EmploymentType, EmploymentStartDate, MonthlyIncome, GrossIncome, IncomeSource, SalaryDate, RequestedAmount, ApprovedAmount, Tenure, SubmittedAt, TermsAcceptedAt | NationalId, DOB, MonthlyIncome, GrossIncome (AES-256) |

### assessment-db [EP-03]

| Table | Key Columns | Encrypted |
|-------|-------------|-----------|
| AssessmentResults | Id, ApplicationId(FK), CitcStatus, CitcVerifiedAt, GeoStatus, GeoVerifiedAt, SimahScore, SimahDbr, SimahCheckedAt, DeDecision, DeReason, ApprovedAmount, DecidedAt | None (no PII — scores and decisions only) |

### offers-db [EP-03]

| Table | Key Columns | Encrypted |
|-------|-------------|-----------|
| Offers | Id, ApplicationId(FK), Amount, Tenure, ProfitRate, AdminFeePercent, AdminFeeAmount, VatAmount, MonthlyPayment, TotalRepayment, NetDisbursement, TotalCostOfCredit, ValidUntil, Status, KeySellingPoint | None |
| Contracts | Id, ApplicationId(FK), OfferId(FK), ContractType(Tawarruq), SignatureType, SignatureData, SignedAt, CoolingPeriodEndsAt, Status | SignatureData (AES-256) |
| DebitCards | Id, ApplicationId(FK), TokenReference, Last4Digits, CardBrand, Is3dsVerified, SalaryDate, PrimaryDebitDate, SecondaryDebitDate, IsActive | TokenReference (AES-256) |
| Disbursements | Id, ApplicationId(FK), GrossAmount, AdminFee, VatOnAdminFee, NetAmount, Status, BankReference, DisbursedAt, FirstPaymentDate, MonthlyPayment | None |

All tables include EA4 audit columns: CreatedAt, CreatedBy, UpdatedAt, UpdatedBy, IsDeleted, Version.

### Modified Table [EP-03]

| Table | Change | Backward Compatible |
|-------|--------|-------------------|
| Users (auth-db) | Add NationalId (string, encrypted, nullable) for SIMAH lookup | ✅ Yes — nullable, no default needed |

## 7a. ADR Summary [EP-02/EP-03]

| ADR | Decision | Rationale | KB Reference |
|-----|----------|-----------|-------------|
| ADR-06 | Assessment service is backend-only — mobile does not call it directly | Simplifies mobile (2 API clients not 3); assessment logic fully server-side; mobile polls for status | EA2, EA8, MS6 |
| ADR-07 | PCI-DSS SAQ A via payment processor hosted fields | Card data never touches BridgeNow; lowest PCI scope; token stored encrypted | PD9, EA5, PD10 |
| ADR-08 | Offer calculation is server-side only | No business logic leakage to mobile; offer tampering impossible | PD4, PD5, EA5 |
| ADR-09 | Cooling-off period tracked server-side with UTC timestamps | Tamper-proof; mobile timer informational only; disbursement enforced server-side | PD5, SAMA |
| ADR-10 | Separate database per service | No cross-service joins; services communicate via REST; eventual consistency | EA2, EA4, MS3 |
