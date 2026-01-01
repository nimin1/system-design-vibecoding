# Real-World Design: Payment System

## High-Level Architecture

```mermaid
flowchart TB
    subgraph Clients
        Web["🌐 Web"]
        Mobile["📱 Mobile"]
        API["📡 Partner APIs"]
    end
    
    subgraph Gateway["Gateway Layer"]
        LB["Load Balancer"]
        APIGateway["API Gateway"]
        RateLimit["Rate Limiter"]
    end
    
    subgraph Core["Core Services"]
        PaymentSvc["Payment Service"]
        OrderSvc["Order Service"]
        WalletSvc["Wallet Service"]
        RiskSvc["Risk & Fraud"]
        LedgerSvc["Ledger Service"]
    end
    
    subgraph External["External Providers"]
        Stripe["Stripe"]
        PayPal["PayPal"]
        Bank["Bank APIs"]
    end
    
    subgraph Data["Data Layer"]
        PaymentDB[(Payment DB)]
        LedgerDB[(Ledger DB)]
        EventStore[(Event Store)]
        Cache[(Redis)]
    end
    
    subgraph Messaging
        Kafka["Apache Kafka"]
    end
    
    Web --> LB
    Mobile --> LB
    API --> LB
    LB --> APIGateway --> RateLimit
    
    RateLimit --> PaymentSvc
    RateLimit --> OrderSvc
    
    PaymentSvc --> RiskSvc
    PaymentSvc --> LedgerSvc
    PaymentSvc --> External
    
    PaymentSvc --> PaymentDB
    LedgerSvc --> LedgerDB
    PaymentSvc --> Kafka
```

## Payment Flow (Happy Path)

```mermaid
sequenceDiagram
    participant User
    participant API as Payment API
    participant Risk as Risk Engine
    participant PSP as Payment Provider
    participant Ledger
    participant Notify
    
    User->>API: POST /payments {amount: $100}
    API->>API: Create payment (PENDING)
    API->>Risk: Fraud check
    Risk-->>API: ✅ Approved
    
    API->>PSP: Charge card
    PSP-->>API: ✅ Success (txn_id: xyz)
    
    API->>Ledger: Record transaction
    API->>API: Update payment (COMPLETED)
    API-->>User: 200 OK {status: success}
    
    API--)Notify: Send receipt (async)
```

## Payment State Machine

```mermaid
stateDiagram-v2
    [*] --> Created: User initiates
    
    Created --> Pending: Submit for processing
    Pending --> Authorized: Card authorized
    Pending --> Failed: Authorization failed
    
    Authorized --> Captured: Capture funds
    Authorized --> Cancelled: User cancels
    
    Captured --> Settled: Batch settlement
    Captured --> Refunded: Issue refund
    
    Settled --> PartialRefund: Partial refund
    Settled --> FullRefund: Full refund
    
    Failed --> [*]
    Cancelled --> [*]
    FullRefund --> [*]
```

## Double-Entry Ledger

```mermaid
flowchart LR
    subgraph Transaction["Payment: $100 from User to Merchant"]
        direction TB
        Debit["DEBIT<br/>User Account: -$100"]
        Credit["CREDIT<br/>Merchant Account: +$100"]
    end
    
    subgraph Ledger["Ledger Entries"]
        direction TB
        E1["Entry 1: User, DEBIT, -$100"]
        E2["Entry 2: Merchant, CREDIT, +$100"]
        Sum["Sum = 0 ✅ (Always balanced)"]
    end
    
    Transaction --> Ledger
```

## Handling Failures with Retries

```mermaid
sequenceDiagram
    participant API as Payment API
    participant PSP as Payment Provider
    participant Queue as Retry Queue
    
    Note over API,Queue: Attempt 1
    API->>PSP: Charge card
    PSP--xAPI: ❌ Timeout
    
    API->>Queue: Schedule retry (5s delay)
    
    Note over API,Queue: Attempt 2
    Queue->>API: Trigger retry
    API->>PSP: Charge card (same idempotency key)
    PSP--xAPI: ❌ 500 Error
    
    API->>Queue: Schedule retry (30s delay, exponential backoff)
    
    Note over API,Queue: Attempt 3
    Queue->>API: Trigger retry
    API->>PSP: Charge card (same idempotency key)
    PSP-->>API: ✅ Success
```

## Idempotency Pattern

```mermaid
flowchart TB
    Request["POST /payments<br/>Idempotency-Key: pay_abc123"]
    
    subgraph IdempotencyCheck["Idempotency Handler"]
        Check{"Key exists?"}
        Process["Process payment"]
        Return["Return cached result"]
        Store["Store result with key"]
    end
    
    Request --> Check
    Check -->|"No"| Process
    Check -->|"Yes"| Return
    Process --> Store
```

## Reconciliation Process

```mermaid
flowchart TB
    subgraph Daily["Daily Reconciliation"]
        direction LR
        Internal["Internal Records"]
        External["PSP Reports"]
        Compare["Compare Transactions"]
    end
    
    Compare --> Match{"Match?"}
    Match -->|"Yes"| Archive["Archive ✅"]
    Match -->|"No"| Dispute["Flag for Review ⚠️"]
    
    Dispute --> Manual["Manual Investigation"]
    Manual --> Resolve["Resolve Discrepancy"]
```

## Webhook Handling

```mermaid
sequenceDiagram
    participant PSP as Stripe
    participant Webhook as Webhook Handler
    participant Queue as Job Queue
    participant Svc as Payment Service
    
    PSP->>Webhook: POST /webhooks/stripe
    Webhook->>Webhook: Verify signature
    Webhook->>Queue: Enqueue event
    Webhook-->>PSP: 200 OK (ACK quickly)
    
    Queue->>Svc: Process payment.succeeded
    Svc->>Svc: Update payment status
    Svc->>Svc: Send receipt
```

## Multi-Currency Support

```mermaid
flowchart TB
    subgraph UserPayment["User Pays"]
        USD["User: $100 USD"]
    end
    
    subgraph FX["FX Conversion"]
        Rate["Rate: 1 USD = 0.85 EUR<br/>(locked at transaction time)"]
    end
    
    subgraph MerchantReceives["Merchant Receives"]
        EUR["Merchant: €85 EUR"]
    end
    
    USD --> Rate --> EUR
    
    subgraph Ledger["Ledger Records"]
        L1["Debit: User -$100 USD"]
        L2["Credit: FX Reserve +$100 USD"]
        L3["Debit: FX Reserve -€85 EUR"]
        L4["Credit: Merchant +€85 EUR"]
    end
```
