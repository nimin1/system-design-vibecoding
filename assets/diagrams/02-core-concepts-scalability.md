# Scalability Concepts Diagrams

## Horizontal vs Vertical Scaling

```mermaid
flowchart TB
    subgraph Vertical["Vertical Scaling (Scale Up)"]
        direction TB
        V1["Small Server<br/>2 CPU, 4GB RAM"]
        V2["Medium Server<br/>8 CPU, 32GB RAM"]
        V3["Large Server<br/>64 CPU, 256GB RAM"]
        V1 -->|"Upgrade"| V2
        V2 -->|"Upgrade"| V3
        VNote["✓ Simple<br/>✓ No code changes<br/>✗ Hardware limits<br/>✗ Single point of failure"]
    end
    
    subgraph Horizontal["Horizontal Scaling (Scale Out)"]
        direction TB
        LB["Load Balancer"]
        H1["Server 1"]
        H2["Server 2"]
        H3["Server 3"]
        H4["Server N..."]
        LB --> H1
        LB --> H2
        LB --> H3
        LB --> H4
        HNote["✓ Near infinite scale<br/>✓ Fault tolerance<br/>✗ Complexity<br/>✗ State management"]
    end
    
    style Vertical fill:#FFE4B5,stroke:#333
    style Horizontal fill:#90EE90,stroke:#333
```

## Stateless Architecture

```mermaid
flowchart TB
    subgraph Stateful["❌ Stateful (Bad)"]
        direction LR
        C1["Client"] --> S1["Server 1<br/>Session: client1"]
        Note1["Client stuck to one server"]
    end
    
    subgraph Stateless["✅ Stateless (Good)"]
        direction TB
        C2["Client<br/>(carries JWT)"]
        LB["Load Balancer"]
        SS1["Server 1"]
        SS2["Server 2"]
        SS3["Server 3"]
        Store[(Session Store<br/>Redis)]
        
        C2 --> LB
        LB --> SS1
        LB --> SS2
        LB --> SS3
        SS1 --> Store
        SS2 --> Store
        SS3 --> Store
        Note2["Any server can handle any request"]
    end
    
    style Stateful fill:#FFB6C1,stroke:#333
    style Stateless fill:#90EE90,stroke:#333
```

## Throughput vs Latency

```mermaid
flowchart LR
    subgraph Latency["Latency (Response Time)"]
        L1["Request"] -->|"100ms"| L2["Response"]
        LNote["Time for ONE request"]
    end
    
    subgraph Throughput["Throughput (Requests/sec)"]
        direction TB
        R1["→ Request 1"]
        R2["→ Request 2"]
        R3["→ Request 3"]
        R4["→ Request N"]
        Server["Server<br/>1000 RPS"]
        R1 --> Server
        R2 --> Server
        R3 --> Server
        R4 --> Server
        TNote["Requests per second"]
    end
```

## The Numbers Every Developer Should Know

```mermaid
flowchart TB
    subgraph Latencies["Latency Comparison"]
        direction LR
        L1["L1 Cache: 0.5ns"]
        L2["L2 Cache: 7ns"]
        L3["RAM: 100ns"]
        L4["SSD Read: 150μs"]
        L5["HDD Seek: 10ms"]
        L6["Network Roundtrip: 500μs"]
        L7["Cross-Region: 150ms"]
    end
    
    subgraph Scale["Scale Reference"]
        direction LR
        S1["1ms = 1,000μs = 1,000,000ns"]
        S2["RAM is 10,000x slower than L1"]
        S3["SSD is 1,500x slower than RAM"]
        S4["Network is 5x slower than SSD"]
    end
```

## Load Testing Metrics

```mermaid
flowchart TB
    subgraph Metrics["Key Metrics"]
        P50["P50 (Median)<br/>50% of requests faster"]
        P95["P95<br/>95% of requests faster"]
        P99["P99<br/>99% of requests faster"]
        P999["P99.9<br/>99.9% of requests faster"]
    end
    
    subgraph Example["Example Distribution"]
        direction LR
        E1["P50: 100ms"]
        E2["P95: 250ms"]
        E3["P99: 500ms"]
        E4["P99.9: 2000ms"]
    end
    
    subgraph WhyItMatters["Why P99 Matters"]
        W1["1M requests/day"]
        W2["P50 = 100ms ← Most users see this"]
        W3["P99 = 500ms ← 10,000 users/day see this"]
        W4["P99.9 = 2s ← 1,000 users/day see this"]
    end
```

## Back-of-Envelope Calculations

```mermaid
flowchart TB
    subgraph Powers["Powers of 2"]
        direction LR
        P1["2^10 = 1 KB"]
        P2["2^20 = 1 MB"]
        P3["2^30 = 1 GB"]
        P4["2^40 = 1 TB"]
    end
    
    subgraph Traffic["Traffic Estimation"]
        direction TB
        DAU["100M DAU"]
        Writes["Each user: 1 post/day"]
        Total["= 100M posts/day"]
        PerSec["= 100M / 86400 ≈ 1157 writes/sec"]
        Peak["Peak: 3x average = 3500 writes/sec"]
    end
    
    subgraph Storage["Storage Estimation"]
        direction TB
        Posts["100M posts/day"]
        Size["Average: 500 bytes/post"]
        Daily["Daily: 50GB"]
        Yearly["Yearly: 18TB"]
        FiveYr["5 Years: 90TB"]
    end
```

## Availability Nines

```mermaid
flowchart TB
    subgraph Nines["Availability Levels"]
        direction TB
        N2["99% (2 nines)<br/>3.65 days/year downtime"]
        N3["99.9% (3 nines)<br/>8.76 hours/year downtime"]
        N4["99.99% (4 nines)<br/>52.6 minutes/year downtime"]
        N5["99.999% (5 nines)<br/>5.26 minutes/year downtime"]
    end
    
    subgraph Formula["How to Achieve"]
        direction TB
        Redundancy["Redundancy: Multiple servers"]
        Failover["Failover: Automatic switching"]
        Monitoring["Monitoring: Quick detection"]
        Testing["Testing: Chaos engineering"]
    end
    
    style N5 fill:#90EE90,stroke:#333
```
