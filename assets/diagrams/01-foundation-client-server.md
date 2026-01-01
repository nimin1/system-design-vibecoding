# Client-Server Architecture Diagrams

## Basic Client-Server Model

```mermaid
flowchart LR
    subgraph Client["🖥️ Client"]
        Browser["Web Browser"]
        MobileApp["Mobile App"]
    end
    
    subgraph Server["🖧 Server"]
        WebServer["Web Server"]
        AppLogic["Application Logic"]
        DB[(Database)]
    end
    
    Browser -->|"HTTP Request"| WebServer
    MobileApp -->|"HTTP Request"| WebServer
    WebServer --> AppLogic
    AppLogic --> DB
    DB --> AppLogic
    AppLogic --> WebServer
    WebServer -->|"HTTP Response"| Browser
    WebServer -->|"HTTP Response"| MobileApp
```

## Request-Response Cycle

```mermaid
sequenceDiagram
    participant C as Client
    participant S as Server
    participant D as Database
    
    C->>S: 1. HTTP Request (GET /users/123)
    S->>D: 2. Query database
    D-->>S: 3. Return data
    S-->>C: 4. HTTP Response (200 OK + JSON)
```

## Three-Tier Architecture

```mermaid
flowchart TB
    subgraph Presentation["Presentation Tier"]
        Web["Web Browser"]
        Mobile["Mobile App"]
        Desktop["Desktop App"]
    end
    
    subgraph Logic["Application Tier"]
        API["API Gateway"]
        Services["Business Logic"]
        Auth["Auth Service"]
    end
    
    subgraph Data["Data Tier"]
        Primary[(Primary DB)]
        Cache[("Redis Cache")]
        Search[("Search Index")]
    end
    
    Web --> API
    Mobile --> API
    Desktop --> API
    API --> Services
    Services --> Auth
    Services --> Primary
    Services --> Cache
    Services --> Search
```

## Stateless vs Stateful Servers

### Stateless Server
```mermaid
flowchart LR
    C1[Client 1] --> LB[Load Balancer]
    C2[Client 2] --> LB
    C3[Client 3] --> LB
    
    LB --> S1[Server 1]
    LB --> S2[Server 2]
    LB --> S3[Server 3]
    
    S1 --> Store[(Shared<br/>Session Store)]
    S2 --> Store
    S3 --> Store
    
    style Store fill:#f9f,stroke:#333
```

### Stateful Server (Not Recommended)
```mermaid
flowchart LR
    C1[Client 1] --> S1[Server 1<br/>Session: C1]
    C2[Client 2] --> S2[Server 2<br/>Session: C2]
    C3[Client 3] --> S3[Server 3<br/>Session: C3]
    
    style S1 fill:#faa,stroke:#333
    style S2 fill:#faa,stroke:#333
    style S3 fill:#faa,stroke:#333
```
