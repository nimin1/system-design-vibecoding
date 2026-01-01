# Containers and Kubernetes

Modern infrastructure for deploying and managing applications at scale.

---

## What Are Containers?

Containers package an application with its dependencies into a standardized unit.

Unlike virtual machines, containers share the host OS kernel:

```
┌─────────────────────────────────────────────────────┐
│                    Virtual Machines                  │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐             │
│  │  App A  │  │  App B  │  │  App C  │             │
│  │  Guest  │  │  Guest  │  │  Guest  │             │
│  │   OS    │  │   OS    │  │   OS    │             │
│  └─────────┘  └─────────┘  └─────────┘             │
│  ┌─────────────────────────────────────┐           │
│  │            Hypervisor               │           │
│  └─────────────────────────────────────┘           │
│  ┌─────────────────────────────────────┐           │
│  │              Host OS                │           │
│  └─────────────────────────────────────┘           │
└─────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│                     Containers                       │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐             │
│  │  App A  │  │  App B  │  │  App C  │             │
│  │  Deps   │  │  Deps   │  │  Deps   │             │
│  └─────────┘  └─────────┘  └─────────┘             │
│  ┌─────────────────────────────────────┐           │
│  │         Container Runtime           │           │
│  └─────────────────────────────────────┘           │
│  ┌─────────────────────────────────────┐           │
│  │              Host OS                │           │
│  └─────────────────────────────────────┘           │
└─────────────────────────────────────────────────────┘
```

**Key benefits:**
- Lightweight (seconds to start vs. minutes for VMs)
- Consistent environment (dev = prod)
- Isolation (each container is separate)
- Portable (runs anywhere with container runtime)

---

## Docker Basics

Docker is the most common container platform.

### Images

A container image is a read-only template with the application and dependencies.

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```

Build: `docker build -t myapp:1.0 .`

### Containers

A running instance of an image.

```bash
docker run -d -p 3000:3000 myapp:1.0
```

### Registry

Where images are stored.

- Docker Hub (public default)
- AWS ECR
- Google Container Registry
- Self-hosted registries

---

## Why Kubernetes?

Containers are great for one application. What about hundreds?

**Challenges at scale:**
- Scheduling: which machine runs which container?
- Networking: how do containers find each other?
- Scaling: how to scale up/down?
- Health: what if a container crashes?
- Updates: how to deploy new versions?

**Kubernetes (K8s):** Container orchestration platform that handles all of this.

---

## Kubernetes Core Concepts

### Pods

Smallest deployable unit. One or more containers that share networking and storage.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
spec:
  containers:
  - name: myapp
    image: myapp:1.0
    ports:
    - containerPort: 3000
```

Usually don't create Pods directly - use Deployments.

### Deployments

Manage the desired state of Pods.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp:1.0
        ports:
        - containerPort: 3000
```

Kubernetes ensures 3 replicas are always running.

### Services

Stable network endpoint for Pods.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 3000
  type: ClusterIP
```

Pods come and go. Service provides stable DNS name and load balancing.

### Service Types

| Type | Description |
|------|-------------|
| ClusterIP | Internal only (default) |
| NodePort | Exposed on each node's IP |
| LoadBalancer | External load balancer (cloud) |
| Ingress | HTTP routing (via Ingress controller) |

### Namespaces

Logical separation within a cluster.

```
default namespace: general apps
prod namespace: production workloads
dev namespace: development
kube-system: Kubernetes internals
```

---

## Scaling

### Horizontal Pod Autoscaler (HPA)

Scale based on metrics.

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

CPU > 70%? Add pods. CPU < 70%? Remove pods.

### Cluster Autoscaler

Add/remove nodes based on pending pods.

Pods pending (no capacity) → add nodes.
Nodes underutilized → remove nodes.

---

## Configuration Management

### ConfigMaps

Non-sensitive configuration.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-config
data:
  database_host: "db.example.com"
  log_level: "info"
```

### Secrets

Sensitive configuration (base64 encoded, optionally encrypted at rest).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: myapp-secret
type: Opaque
data:
  database_password: cGFzc3dvcmQxMjM=
```

---

## Health Checks

### Liveness Probe

Is the container alive? Fail → restart container.

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 3000
  initialDelaySeconds: 10
  periodSeconds: 5
```

### Readiness Probe

Is the container ready for traffic? Fail → remove from service.

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 3000
  initialDelaySeconds: 5
  periodSeconds: 3
```

---

## Deployments and Updates

### Rolling Update

Default strategy. Gradually replace old pods with new.

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1        # Extra pods during update
    maxUnavailable: 0  # Don't reduce capacity
```

### Blue-Green Deployment

Run two complete environments. Switch traffic.

```
v1 (blue) - serving traffic
v2 (green) - deployed, tested
Switch: green becomes active
Rollback: switch back to blue
```

### Canary Deployment

Route small percentage of traffic to new version.

```
95% → v1
5%  → v2
Monitor → if good, increase to 100%
```

---

## Networking

### Pod-to-Pod

Each pod gets an IP. Pods can communicate directly.

### Service Discovery

Internal DNS. Services accessible by name.

```
http://myapp-service.default.svc.cluster.local
or simply: http://myapp-service (same namespace)
```

### Ingress

HTTP(S) routing from outside the cluster.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myapp-service
            port:
              number: 80
```

---

## Managed Kubernetes

Running Kubernetes yourself is complex. Managed options:

| Provider | Service |
|----------|---------|
| AWS | EKS (Elastic Kubernetes Service) |
| GCP | GKE (Google Kubernetes Engine) |
| Azure | AKS (Azure Kubernetes Service) |
| DigitalOcean | DOKS |

Managed = control plane managed by provider. You manage workloads.

---

## Common Mistakes

**Not setting resource limits.** Container consumes all resources, affects others.

```yaml
resources:
  requests:
    memory: "256Mi"
    cpu: "100m"
  limits:
    memory: "512Mi"
    cpu: "500m"
```

**No health checks.** Dead containers keep receiving traffic.

**Storing state in containers.** Containers are ephemeral. Use external storage (databases, object storage).

**Hardcoding configuration.** Should use ConfigMaps/Secrets for environment-specific values.

**Not using namespaces.** Everything in default namespace becomes messy.

**Ignoring pod disruption budgets.** Deployments or node drains kill too many pods at once.

---

## When to Use Kubernetes

### Use When

- Many services to manage
- Need autoscaling
- Team has K8s expertise
- Already using containers

### Avoid When

- Single simple application
- Small team without K8s experience
- Simpler solutions work (ECS, serverless)
- You underestimate operational complexity

**Kubernetes is powerful but complex.** Start simple, adopt when needed.

---

## What An Experienced Senior Engineer Thinks About

**Is K8s necessary?** Often simpler solutions work. ECS, Fargate, Cloud Run, Heroku. Evaluate complexity vs. benefit.

**Platform teams.** K8s works best with dedicated platform team. Otherwise, operational burden on app teams.

**Multi-tenancy.** Multiple teams on one cluster. Namespaces, resource quotas, network policies for isolation.

**Stateful workloads.** Databases in K8s are tricky. Managed databases often better.

**Security.** RBAC, network policies, pod security policies, secret management. Security is complex.

---

## Vibe Engineering Guide

When prompting about Kubernetes:

**Less useful:**
> "Deploy my app to Kubernetes"

**More useful:**
> "I need to deploy a Node.js app to EKS:
> - 3 replicas minimum, autoscale to 10 based on CPU
> - Needs environment variables for database connection
> - Health endpoint at /health
> - Expose via ALB at api.myapp.com
>
> Show me the Deployment, Service, HPA, and Ingress YAML. What resource requests/limits should I start with?"

**For specific problems:**
> "My pods keep getting OOMKilled. Memory limit is 512Mi. Application sometimes uses more during spikes. Should I increase limits? Use HPA? Or is my app leaking memory?"

---

## Quick Check

<details>
<summary><b>What's the difference between containers and VMs?</b></summary>

VMs virtualize hardware, each with its own OS (heavy, slow to start). Containers share host OS kernel, only package app and dependencies (lightweight, seconds to start).

</details>

<details>
<summary><b>What's a Kubernetes Pod?</b></summary>

Smallest deployable unit. One or more containers that share networking and storage. Usually one main container per pod. Managed via Deployments.

</details>

<details>
<summary><b>What's the difference between liveness and readiness probes?</b></summary>

Liveness: Is it alive? Fails → restart container. Readiness: Is it ready for traffic? Fails → remove from service but don't restart.

</details>

<details>
<summary><b>When might you avoid Kubernetes?</b></summary>

Simple applications, small teams without K8s expertise, when simpler solutions work (ECS, serverless). K8s has significant operational complexity.

</details>

---

Next: [Level 7: Real-World Designs](../07-real-world-designs/README.md)
