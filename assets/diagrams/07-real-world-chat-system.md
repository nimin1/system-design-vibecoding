# Real-World Design: Chat System

## High-Level Architecture

```mermaid
flowchart TB
    subgraph Clients
        Web["🌐 Web Client"]
        Mobile["📱 Mobile App"]
    end
    
    subgraph Gateway["API Layer"]
        LB["Load Balancer"]
        WS["WebSocket Servers"]
        REST["REST API Servers"]
    end
    
    subgraph Core["Core Services"]
        Auth["Auth Service"]
        Chat["Chat Service"]
        Presence["Presence Service"]
        Push["Push Notification"]
    end
    
    subgraph Data["Data Layer"]
        Redis[(Redis<br/>Sessions/Presence)]
        Cassandra[(Cassandra<br/>Messages)]
        MySQL[(MySQL<br/>Users/Groups)]
    end
    
    subgraph Queue["Message Broker"]
        Kafka["Apache Kafka"]
    end
    
    Web --> LB
    Mobile --> LB
    LB --> WS
    LB --> REST
    
    WS --> Chat
    REST --> Auth
    Chat --> Kafka
    Kafka --> Presence
    Kafka --> Push
    
    Chat --> Cassandra
    Chat --> Redis
    Auth --> MySQL
    Presence --> Redis
```

## Message Flow

```mermaid
sequenceDiagram
    participant A as Alice
    participant WS1 as WS Server 1
    participant Kafka
    participant WS2 as WS Server 2
    participant B as Bob
    participant DB as Cassandra
    participant Push
    
    Note over A,B: Real-time messaging
    A->>WS1: Send message to Bob
    WS1->>Kafka: Publish message
    WS1->>DB: Store message
    
    alt Bob is online
        Kafka->>WS2: Deliver to Bob's WS
        WS2->>B: WebSocket push
        B-->>WS2: ACK (delivered)
    else Bob is offline
        Kafka->>Push: Trigger notification
        Push->>B: Push notification 📲
    end
```

## WebSocket Connection Management

```mermaid
flowchart TB
    subgraph Clients["Connected Clients"]
        C1["Alice → WS-1"]
        C2["Bob → WS-2"]
        C3["Carol → WS-1"]
        C4["Dave → WS-3"]
    end
    
    subgraph Registry["Connection Registry (Redis)"]
        R1["alice: {ws: ws-1, id: conn-123}"]
        R2["bob: {ws: ws-2, id: conn-456}"]
        R3["carol: {ws: ws-1, id: conn-789}"]
        R4["dave: {ws: ws-3, id: conn-012}"]
    end
    
    subgraph WSServers["WebSocket Server Pool"]
        WS1["WS Server 1<br/>alice, carol"]
        WS2["WS Server 2<br/>bob"]
        WS3["WS Server 3<br/>dave"]
    end
    
    C1 --> WS1
    C2 --> WS2
    C3 --> WS1
    C4 --> WS3
    
    WS1 --> Registry
    WS2 --> Registry
    WS3 --> Registry
```

## Group Chat Message Fan-Out

```mermaid
flowchart TB
    Sender["Alice sends to Group"]
    
    subgraph Group["Group: Engineering (1000 members)"]
        direction LR
        M1["Member 1-100"]
        M2["Member 101-200"]
        M3["Member 201-...]"]
    end
    
    subgraph Strategy["Fan-out Strategy"]
        direction TB
        Small["Small Group (<100)<br/>→ Push to all"]
        Large["Large Group (>100)<br/>→ Pull on demand"]
    end
    
    Sender --> Kafka["Kafka Topic: group-eng"]
    Kafka --> Workers["Fan-out Workers"]
    Workers -->|"Batch 1"| M1
    Workers -->|"Batch 2"| M2
    Workers -->|"Batch 3"| M3
```

## Message Storage Schema

```mermaid
erDiagram
    USERS {
        uuid user_id PK
        string username
        string email
        timestamp created_at
    }
    
    CONVERSATIONS {
        uuid conversation_id PK
        string type "dm|group"
        timestamp created_at
    }
    
    PARTICIPANTS {
        uuid conversation_id FK
        uuid user_id FK
        timestamp joined_at
    }
    
    MESSAGES {
        uuid message_id PK
        uuid conversation_id FK
        uuid sender_id FK
        text content
        timestamp sent_at
        string status "sent|delivered|read"
    }
    
    USERS ||--o{ PARTICIPANTS : joins
    CONVERSATIONS ||--o{ PARTICIPANTS : has
    CONVERSATIONS ||--o{ MESSAGES : contains
    USERS ||--o{ MESSAGES : sends
```

## Read Receipts Flow

```mermaid
sequenceDiagram
    participant A as Alice
    participant S as Server
    participant B as Bob
    
    A->>S: Send "Hello" (msg_id: 123)
    S-->>A: ✓ Sent
    S->>B: Deliver message
    
    Note over B: Message arrives
    B-->>S: ACK received (msg_id: 123)
    S-->>A: ✓✓ Delivered
    
    Note over B: Bob opens chat
    B->>S: Mark read (msg_id: 123)
    S-->>A: ✓✓ Read (blue)
```
