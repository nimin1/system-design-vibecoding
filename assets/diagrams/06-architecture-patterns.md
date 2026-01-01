# Architecture Patterns Diagrams

## Microservices Architecture

```mermaid
flowchart TB
    subgraph Clients["Clients"]
        Web["🌐 Web"]
        Mobile["📱 Mobile"]
    end
    
    Gateway["API Gateway"]
    
    subgraph Services["Microservices"]
        UserSvc["User Service"]
        OrderSvc["Order Service"]
        PaymentSvc["Payment Service"]
        InventorySvc["Inventory Service"]
        NotificationSvc["Notification Service"]
    end
    
    subgraph Data["Service Databases"]
        UserDB[(Users DB)]
        OrderDB[(Orders DB)]
        PaymentDB[(Payments DB)]
        InventoryDB[(Inventory DB)]
    end
    
    EventBus["Event Bus (Kafka)"]
    
    Web --> Gateway
    Mobile --> Gateway
    
    Gateway --> UserSvc
    Gateway --> OrderSvc
    Gateway --> PaymentSvc
    
    UserSvc --> UserDB
    OrderSvc --> OrderDB
    PaymentSvc --> PaymentDB
    InventorySvc --> InventoryDB
    
    OrderSvc --> EventBus
    EventBus --> PaymentSvc
    EventBus --> InventorySvc
    EventBus --> NotificationSvc
```

## Monolith vs Microservices

```mermaid
flowchart LR
    subgraph Monolith["Monolithic Architecture"]
        M1["Single Codebase"]
        M2["Single Database"]
        M3["Single Deployment"]
        M1 --> M2
        MNote["✓ Simple to start<br/>✓ Easy debugging<br/>✗ Scaling challenges<br/>✗ Tight coupling"]
    end
    
    subgraph Micro["Microservices Architecture"]
        direction TB
        S1["Service A"]
        S2["Service B"]
        S3["Service C"]
        D1[(DB A)]
        D2[(DB B)]
        D3[(DB C)]
        S1 --> D1
        S2 --> D2
        S3 --> D3
        MicroNote["✓ Independent scaling<br/>✓ Tech flexibility<br/>✗ Complexity<br/>✗ Network latency"]
    end
    
    style Monolith fill:#FFE4B5,stroke:#333
    style Micro fill:#90EE90,stroke:#333
```

## Event Sourcing

```mermaid
flowchart TB
    subgraph Commands["Commands"]
        C1["CreateOrder"]
        C2["AddItem"]
        C3["Checkout"]
    end
    
    EventStore[(Event Store)]
    
    subgraph Events["Events (Immutable)"]
        E1["OrderCreated {id: 1, user: alice}"]
        E2["ItemAdded {id: 1, product: shoes}"]
        E3["OrderCheckedOut {id: 1, total: $99}"]
    end
    
    subgraph ReadModel["Read Models"]
        View1["Order Summary View"]
        View2["User Orders View"]
        View3["Analytics View"]
    end
    
    Commands --> EventStore
    EventStore --> Events
    Events --> View1
    Events --> View2
    Events --> View3
```

## CQRS (Command Query Responsibility Segregation)

```mermaid
flowchart TB
    subgraph Write["Write Side (Commands)"]
        WClient["Client"]
        CommandAPI["Command API"]
        CommandHandler["Command Handler"]
        WriteDB[(Write DB<br/>Normalized)]
    end
    
    subgraph Read["Read Side (Queries)"]
        RClient["Client"]
        QueryAPI["Query API"]
        ReadDB[(Read DB<br/>Denormalized)]
    end
    
    EventBus["Event Bus"]
    
    WClient -->|"POST /orders"| CommandAPI
    CommandAPI --> CommandHandler
    CommandHandler --> WriteDB
    CommandHandler --> EventBus
    
    EventBus --> ReadDB
    
    RClient -->|"GET /orders"| QueryAPI
    QueryAPI --> ReadDB
    
    style Write fill:#FFB6C1,stroke:#333
    style Read fill:#90EE90,stroke:#333
```

## API Gateway Pattern

```mermaid
flowchart TB
    subgraph Clients
        Web["Web"]
        Mobile["Mobile"]
        Partner["Partner API"]
    end
    
    subgraph Gateway["API Gateway"]
        Auth["Authentication"]
        RateLimit["Rate Limiting"]
        Route["Routing"]
        Transform["Response Transform"]
        Cache["Caching"]
    end
    
    subgraph Backend["Backend Services"]
        S1["User Service"]
        S2["Product Service"]
        S3["Order Service"]
    end
    
    Web --> Gateway
    Mobile --> Gateway
    Partner --> Gateway
    
    Gateway --> Auth
    Auth --> RateLimit
    RateLimit --> Route
    Route --> S1
    Route --> S2
    Route --> S3
    
    style Gateway fill:#ffd93d,stroke:#333
```

## Strangler Fig Pattern (Migration)

```mermaid
flowchart TB
    subgraph Phase1["Phase 1: Initial State"]
        P1Clients["Clients"] --> P1Mono["Monolith"]
    end
    
    subgraph Phase2["Phase 2: Add Facade"]
        P2Clients["Clients"] --> P2Facade["Facade/Proxy"]
        P2Facade --> P2Mono["Monolith"]
    end
    
    subgraph Phase3["Phase 3: Extract Services"]
        P3Clients["Clients"] --> P3Facade["Facade"]
        P3Facade --> P3Service["New Service ✨"]
        P3Facade --> P3Mono["Monolith<br/>(shrinking)"]
    end
    
    subgraph Phase4["Phase 4: Complete"]
        P4Clients["Clients"] --> P4Gateway["API Gateway"]
        P4Gateway --> P4S1["Service 1"]
        P4Gateway --> P4S2["Service 2"]
        P4Gateway --> P4S3["Service 3"]
    end
    
    Phase1 --> Phase2
    Phase2 --> Phase3
    Phase3 --> Phase4
```

## Sidecar Pattern

```mermaid
flowchart TB
    subgraph Pod1["Pod 1"]
        App1["Application<br/>(Business Logic)"]
        Sidecar1["Sidecar<br/>(Logging, Proxy)"]
        App1 <--> Sidecar1
    end
    
    subgraph Pod2["Pod 2"]
        App2["Application"]
        Sidecar2["Sidecar"]
        App2 <--> Sidecar2
    end
    
    Sidecar1 <-->|"Service Mesh"| Sidecar2
    
    subgraph SidecarFeatures["Sidecar Features"]
        F1["📊 Logging"]
        F2["📈 Metrics"]
        F3["🔐 mTLS"]
        F4["🔄 Retries"]
        F5["⚡ Circuit Breaking"]
    end
```

## Backend for Frontend (BFF)

```mermaid
flowchart TB
    subgraph Clients
        Web["🌐 Web"]
        iOS["📱 iOS"]
        Android["🤖 Android"]
    end
    
    subgraph BFFs["BFF Layer"]
        WebBFF["Web BFF"]
        iOSBFF["iOS BFF"]
        AndroidBFF["Android BFF"]
    end
    
    subgraph Services["Core Services"]
        UserSvc["User Service"]
        ProductSvc["Product Service"]
        OrderSvc["Order Service"]
    end
    
    Web --> WebBFF
    iOS --> iOSBFF
    Android --> AndroidBFF
    
    WebBFF --> UserSvc
    WebBFF --> ProductSvc
    iOSBFF --> UserSvc
    iOSBFF --> ProductSvc
    AndroidBFF --> UserSvc
    AndroidBFF --> OrderSvc
    
    style WebBFF fill:#4ecdc4,stroke:#333
    style iOSBFF fill:#ff6b6b,stroke:#333
    style AndroidBFF fill:#45b7d1,stroke:#333
```
