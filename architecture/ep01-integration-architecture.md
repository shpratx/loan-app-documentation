# BridgeNow Finance — Integration Architecture
### Sprint 1 (EP-01: Platform Foundation & Authentication)

---

## 1. Integration Diagram (Sprint 1)

```
┌──────────────┐     HTTPS/REST      ┌──────────────┐
│  Mobile App  │ ──────────────────▶  │ API Gateway  │
│  (Android)   │                      │ (AKS Ingress)│
└──────────────┘                      └──────┬───────┘
                                             │
                              ┌──────────────┼──────────────┐
                              │              │              │
                       ┌──────▼──────┐ ┌─────▼──────┐      │
                       │auth-service │ │products-svc│      │
                       │   :5001     │ │   :5002    │      │
                       └──────┬──────┘ └────────────┘      │
                              │                             │
                       ┌──────▼──────┐                      │
                       │ SMS Gateway │  (mock Sprint 1)     │
                       │ (OTP send)  │                      │
                       └─────────────┘                      │
                                                            │
         Sprint 2+:  SIMAH, CITC, Geo, Open Banking, Payment Processor
```

---

## 2. External System Integrations (Sprint 1)

### SMS Gateway (OTP Delivery)

| Aspect | Detail |
|--------|--------|
| Protocol | REST (fire-and-forget) |
| Auth | API key in header |
| Purpose | Send 6-digit OTP to customer phone |
| Sprint 1 | **Mock** — logs to console, returns success |
| Production | Real SMS provider (Twilio/equivalent) |
| Timeout | 10 seconds |
| Retry | 2 retries with exponential backoff (1s, 2s) |
| Circuit Breaker | 10 failures / 60s → 30s break |
| Fallback | Queue for retry, return success to user (OTP still generated) |
| Data | Phone number (PII — not logged) |

```
auth-service → SMS Gateway
  Request:  POST /sms/send { to: "+966...", body: "Your code: 482931" }
  Response: 200 { messageId: "...", status: "queued" }
  Timeout:  10s
  Retry:    2x (1s, 2s backoff)
```

---

## 3. Inter-Service Communication (Sprint 1)

Sprint 1 has **no service-to-service calls** — auth-service and products-service are independent.

Future sprints will add:
| From | To | Method | Purpose | Sprint |
|------|-----|--------|---------|--------|
| applications-service | auth-service | REST | Validate JWT | 2 |
| assessment-service | SIMAH | REST | Credit check | 2 |
| assessment-service | CITC | REST | Employment check | 2 |
| offers-service | applications-service | Event | Application approved | 2 |
| loans-service | payment-processor | REST + Webhook | Disbursement, auto-debit | 3 |
| notifications-service | SMS Gateway | REST | Transactional SMS | 3 |
| notifications-service | FCM | REST | Push notifications | 3 |

---

## 4. Event Topics (Planned)

Per EA8, async communication via Azure Service Bus.
**Note:** Sprint 2 section 4a below provides the detailed implementation with PascalCase event names per EA8 canonical schema.

| Topic | Events (planned) | Publisher | Subscriber | Sprint |
|-------|--------|-----------|------------|--------|
| auth-events | UserRegistered, UserAuthenticated | auth-service | audit-service | 1 |
| application-events | See section 4a | applications-service | See section 4a | 2 |
| offer-events | See section 4a | offers-service | See section 4a | 2 |
| loan-events | LoanDisbursed, PaymentDue, PaymentReceived, LoanSettled | loans-service | notifications-service | 3 |

### Event Envelope (per EA8)
```json
{
  "eventId": "uuid",
  "eventType": "user.registered",
  "source": "auth-service",
  "timestamp": "2026-04-27T10:00:00Z",
  "correlationId": "uuid",
  "data": { },
  "metadata": { "version": "1.0", "userId": "uuid" }
}
```

---

## 5. Circuit Breaker Configuration

| Integration | Timeout | Retries | Breaker Threshold | Break Duration | Fallback |
|-------------|---------|---------|-------------------|----------------|----------|
| SMS Gateway | 10s | 2 (1s, 2s) | 10 failures / 60s | 30s | Queue for retry |

Sprint 2+ will add: SIMAH (30s, 3 retries, 5/30s→60s), CITC (30s, 3, 3/30s→30s), Geolocation (30s, 3, 5/30s→30s), Payment Processor (30s, 2, 5/30s→60s).

---

## 6. API Gateway Configuration (Sprint 1)

```yaml
# AKS Ingress routing
routes:
  - path: /api/v1/auth/*
    service: auth-service:5001
    rate_limit: { read: 100/min, write: 20/min }
    
  - path: /api/v1/products/*
    service: products-service:5002
    rate_limit: { read: 100/min }
    
  - path: /health
    service: "{service}/health"  # per-service health

security:
  jwt_validation: true
  public_paths:
    - /api/v1/auth/register
    - /api/v1/auth/verify-otp
    - /api/v1/auth/token
    - /api/v1/products
    - /api/v1/products/*
    - /health
    - /health/ready

headers:
  add:
    - X-Correlation-Id  # auto-generate if missing
  propagate:
    - X-Correlation-Id
    - Authorization
```

---

## 7. Observability (Sprint 1)

| Aspect | Implementation | EA Reference |
|--------|---------------|-------------|
| Logging | Serilog structured JSON, correlation ID on every entry | EA9 |
| Tracing | OpenTelemetry → OTLP (ASP.NET Core + HTTP + EF Core instrumentation) | EA9 |
| Metrics | OpenTelemetry (request rate, error rate, latency percentiles) | EA9 |
| Health | /health (liveness), /health/ready (readiness with DB check) per service | EA9 |
| Alerting | P1: service down. P2: error rate >1% for 15min. P3: latency p95 >500ms | EA9 |

---

## Cross-References

| Section | EA | MS | Alignment |
|---------|----|----|-----------|
| System Context | EA2 | MS2 | Bounded contexts as services |
| Security | EA5 | MS9 | JWT, mTLS (future), zero trust |
| Data | EA4 | MS5 | Database per service |
| Integration | EA8 | MS3 | REST sync, events async |
| Circuit Breaker | EA7 | MS6 | Polly, exponential backoff |
| Deployment | EA6 | MS10 | AKS, rolling update, HPA |
| Observability | EA9 | MS8 | Logging, tracing, metrics |
| API Gateway | EA3 | MS4 | Routing, JWT validation, rate limiting |

---

# Sprint 2 Additions (EP-02/EP-03)

## 1a. Integration Diagram [EP-02/EP-03]

```
┌──────────┐     ┌─────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  Mobile   │────▶│ API Gateway │────▶│ applications-svc │────▶│ assessment-svc   │
│   App     │     │  (JWT, CORS │     │     :5003        │     │     :5004        │
└──────────┘     │  Rate Limit)│     └────────┬─────────┘     └───┬──────┬───┬───┘
                  └─────────────┘              │                   │      │   │
                        │                      │                   │      │   │
                        ▼                      ▼                   ▼      ▼   ▼
                  ┌──────────────┐     ┌──────────────┐     ┌─────┐ ┌───┐ ┌───┐
                  │ offers-svc   │     │ auth-svc     │     │SIMAH│ │CIT│ │GEO│
                  │    :5005     │     │   :5001      │     │     │ │C  │ │   │
                  └──┬───┬───┬──┘     └──────────────┘     └─────┘ └───┘ └───┘
                     │   │   │
                     ▼   ▼   ▼
               ┌─────┐ ┌───┐ ┌──────────┐
               │Pay  │ │3DS│ │Disburse  │
               │Proc │ │   │ │(Bank Xfr)│
               └─────┘ └───┘ └──────────┘
```

## 2a. External System Integrations [EP-02/EP-03]

### CITC Employment API [EP-03, US-023]

| Aspect | Detail |
|--------|--------|
| Protocol | REST (synchronous) |
| Auth | API key + mTLS |
| Purpose | Verify employment status and employer name |
| Endpoint | POST /api/v1/employment/verify |
| Request | { nationalId, employerName } |
| Response | { verified: bool, employerName, status, verifiedAt } |
| Timeout | 30s |
| Retry | 3 retries, exponential backoff (1s, 2s, 4s) |
| Circuit Breaker | 3 failures → open 60s |
| Fallback | Application held in 'Pending Verification', logged to staging |
| SLA | 99.5% availability, <5s p95 latency |

### Geolocation API [EP-03, US-024]

| Aspect | Detail |
|--------|--------|
| Protocol | REST (synchronous) |
| Auth | API key |
| Purpose | Verify applicant location within Saudi Arabia |
| Endpoint | POST /api/v1/geolocation/verify |
| Request | { latitude, longitude, declaredRegion } |
| Response | { verified: bool, country, region, verifiedAt } |
| Timeout | 10s |
| Retry | 3 retries, exponential backoff (1s, 2s, 4s) |
| Circuit Breaker | 3 failures → open 60s |
| Fallback | Application held, logged to staging |

### SIMAH Credit Bureau [EP-03, US-025, US-036]

| Aspect | Detail |
|--------|--------|
| Protocol | REST (synchronous) |
| Auth | OAuth2 client credentials + mTLS |
| Purpose | Credit score + existing obligations for DBR calculation |
| Usage | Initial check (post-submit) + 2nd check (pre-disbursement) |
| Endpoint | POST /api/v1/credit/inquiry |
| Request | { nationalId, consentReference } |
| Response | { score (0-999), dbr (%), obligations[], checkedAt } |
| Timeout | 30s |
| Retry | 3 retries, exponential backoff (2s, 4s, 8s) |
| Circuit Breaker | 3 failures → open 120s |
| Fallback | Application held in 'Pending Credit Check' |
| SLA | 99.9% availability, <3s p95 latency |
| Regulatory | SAMA-mandated, explicit customer consent required |

### Open Banking AISP [EP-02, US-021]

| Aspect | Detail |
|--------|--------|
| Protocol | REST + OAuth2 redirect (PSD2/SAMA Open Banking) |
| Auth | OAuth2 authorization code flow with PKCE |
| Purpose | Bank statement retrieval for unlisted company employees |
| Flow | 1. Initiate → redirectUrl 2. User authorizes at bank 3. Callback with consent 4. Fetch transactions |
| Consent Duration | 90 days |
| Timeout | N/A (redirect flow — user-driven) |
| Fallback | Manual income declaration accepted |
| Data Retrieved | 3-12 months transactions, categorised income/expenditure |

### Payment Processor — Hosted Fields [EP-03, US-033]

| Aspect | Detail |
|--------|--------|
| Protocol | JavaScript SDK (iframe) |
| Auth | Publishable API key (client-side) |
| Purpose | PCI-DSS SAQ A compliant card capture |
| Flow | SDK renders PAN/Expiry/CVV fields in iframe → tokenises → returns token to BridgeNow |
| PCI Scope | SAQ A — card data never touches BridgeNow servers or mobile storage |
| Fallback | If SDK fails to load → error state, retry |

### Payment Processor — 3D Secure [EP-03, US-034]

| Aspect | Detail |
|--------|--------|
| Protocol | REST + redirect/challenge |
| Auth | Secret API key (server-side) |
| Purpose | SCA verification for card authentication (PSD2/SAMA) |
| Flow | 1. Server initiates 3DS 2. Challenge presented (OTP/biometric) 3. Result callback |
| Fallback | If 3DS not supported by issuer → risk-based auth |

### Payment Processor — Disbursement [EP-03, US-037]

| Aspect | Detail |
|--------|--------|
| Protocol | REST async + webhook callback |
| Auth | Secret API key + HMAC webhook signature |
| Purpose | Bank transfer of net loan amount to applicant account |
| Endpoint | POST /api/v1/transfers |
| Request | { amount, currency: SAR, recipientAccount, reference } |
| Webhook | POST /webhooks/disbursement { transferId, status, bankReference } |
| Timeout | 60s for initiation; webhook within 5 minutes |
| Circuit Breaker | 2 failures → hold application |
| Fallback | Operations alerted, manual disbursement |

## 3a. Inter-Service Communication [EP-02/EP-03]

| From | To | Method | Purpose | Auth | Sprint |
|------|----|--------|---------|------|--------|
| applications-svc | assessment-svc | REST POST | Trigger assessment after submit | Service JWT (mTLS) | 2 |
| applications-svc | products-svc | REST GET | Fetch product config for eligibility | Service JWT | 2 |
| offers-svc | assessment-svc | REST GET | Read assessment result for offer calc | Service JWT | 2 |
| offers-svc | applications-svc | REST PUT | Update application status on disbursement | Service JWT | 2 |
| assessment-svc | SIMAH | REST POST | Credit inquiry | OAuth2 + mTLS | 2 |
| assessment-svc | CITC | REST POST | Employment verification | API key + mTLS | 2 |
| assessment-svc | Geolocation | REST POST | Location verification | API key | 2 |

## 4a. Event Topics [EP-02/EP-03]

| Topic | Event | Publisher | Subscriber | Sprint |
|-------|-------|-----------|------------|--------|
| application-events | ApplicationCreated | applications-svc | audit-log, notifications | 2 |
| application-events | ApplicationSubmitted | applications-svc | assessment-svc, audit-log | 2 |
| application-events | ApplicationStatusChanged | applications-svc | notifications, audit-log | 2 |
| offer-events | OfferGenerated | offers-svc | applications-svc, notifications | 2 |
| offer-events | OfferAccepted | offers-svc | applications-svc, audit-log | 2 |
| offer-events | OfferCancelled | offers-svc | applications-svc, audit-log | 2 |
| offer-events | ContractSigned | offers-svc | audit-log, notifications | 2 |
| offer-events | DisbursementCompleted | offers-svc | applications-svc, notifications, audit-log | 2 |
| offer-events | DisbursementFailed | offers-svc | applications-svc, operations-alert | 2 |

### Event Envelope (per EA8)
```json
{
  "eventId": "uuid",
  "eventType": "ApplicationSubmitted",
  "timestamp": "2026-04-27T12:00:00Z",
  "correlationId": "uuid",
  "source": "applications-service",
  "version": "1.0",
  "data": { }
}
```

## 5a. Circuit Breaker Configuration [EP-02/EP-03]

| Integration | Timeout | Retries | Breaker Threshold | Break Duration | Fallback |
|-------------|---------|---------|-------------------|----------------|----------|
| SMS Gateway | 10s | 2 | 5 failures/60s | 30s | Log + continue |
| **CITC** [EP-03] | 30s | 3 | 3 failures/60s | 60s | Hold in Pending Verification |
| **Geolocation** [EP-03] | 10s | 3 | 3 failures/60s | 60s | Hold, log to staging |
| **SIMAH** [EP-03] | 30s | 3 | 3 failures/120s | 120s | Hold in Pending Credit Check |
| **Payment Processor** [EP-03] | 60s | 2 | 2 failures/60s | 60s | Hold, alert operations |
| **Open Banking** [EP-02] | N/A | N/A | N/A | N/A | Manual income declaration |

## 6a. API Gateway Configuration [EP-02/EP-03]

```yaml
# Sprint 2 additions to API Gateway routes
routes:
  - path: /api/v1/applications/**
    service: applications-service:5003
    auth: jwt_required
    rate_limit: { read: 100/min, write: 20/min }
  - path: /api/v1/applications/*/assess
    service: assessment-service:5004
    auth: service_jwt  # Internal only — not exposed to mobile (ADR-06)
  - path: /api/v1/applications/*/offer/**
    service: offers-service:5005
    auth: jwt_required
    rate_limit: { read: 100/min, write: 20/min }
  - path: /api/v1/applications/*/card
    service: offers-service:5005
    auth: jwt_required
    sca_required: true
  - path: /api/v1/applications/*/disburse
    service: offers-service:5005
    auth: jwt_required
    sca_required: true
  - path: /webhooks/disbursement
    service: offers-service:5005
    auth: hmac_signature  # Payment processor webhook
```
