# Microservices, Design Patterns, and Spring Cloud Ecosystem Guide

## 1) Why Microservices?

Microservices = an architectural style that breaks a system into small, independently deployable services.

**Benefits:**

* Independent deploys → faster release cycles
* Small, focused teams own services → better autonomy
* Technology heterogeneity (polyglot)
* Scalability by service
* Fault isolation

**Tradeoffs:**

* Operational complexity
* Distributed systems problems
* More effort for cross-service transactions, debugging, testing
* Needs automation: CI/CD, infra-as-code, container orchestration

**When not to use:**

* Small teams or small app
* Lack of SRE/DevOps capabilities
* Unclear domain boundaries

---

## 2) Real-world Microservices Examples

* **E-commerce:** Product Catalog, Cart, Pricing, Checkout, Orders, Inventory, Notification
* **Streaming:** Catalog, Recommendation, Playback, Billing, User Profile, Search
* **Ride-hailing:** Rider service, Driver service, Matching, Pricing, Maps, Payments
* **Food delivery:** Restaurant, Menu, Order, Rider Logistics, Payments, Ratings

---

## 3) Major Microservices Design Patterns

### A. API Gateway

* Single entry point for clients
* Handles authentication, routing, rate limiting, aggregation
* **Examples:** Spring Cloud Gateway, Kong, NGINX, AWS API Gateway

### B. Service Discovery

* Dynamically find service instances at runtime
* **Approaches:**

  * Client-side discovery (client queries registry)
  * Server-side discovery (gateway/load-balancer queries registry)
* **Real example:** `order-service` finds `inventory-service` instance via registry

### C. Circuit Breaker

* Prevent repeated calls to failing service
* **Real example:** If `payment-service` is down, checkout uses fallback
* **Implementations:** Resilience4j, Hystrix

### D. Bulkhead

* Isolate resources to prevent cascading failures
* **Real example:** Thread pools per downstream dependency

### E. Saga (Distributed transactions)

* Manages distributed transactions via compensating actions
* **Types:** Choreography (event-driven), Orchestration (central coordinator)
* **Real example:** Order creation → reserve inventory → charge payment → confirm order

### F. Event Sourcing & CQRS

* **Event Sourcing:** Persist state changes as events
* **CQRS:** Separate models for writes (commands) and reads (queries)
* **Real example:** Banking ledger stores transactions as events

### G. Strangler Fig Pattern

* Gradually replace monolith modules with microservices
* **Real example:** Replace monolith checkout with `checkout-service`

### H. Sidecar

* Helper process alongside service instance (proxies, logging)
* **Real example:** Istio/Envoy sidecar for telemetry and mTLS

### I. Observability Patterns

* Correlation IDs, centralized logging, metrics, distributed tracing (Zipkin/OpenTelemetry)

---

## 4) Typical Microservices Platform Components

* Service registry/discovery (Eureka, Consul)
* API Gateway (Spring Cloud Gateway, Kong)
* Config server (Spring Cloud Config)
* Resilience (Circuit breakers, retries)
* Message broker (Kafka, RabbitMQ)
* Monitoring/tracing (Prometheus, Grafana, Zipkin)
* CI/CD, container orchestration (Kubernetes)
* Security (OAuth2/JWT, centralized auth)

---

## 5) Spring Cloud, API Gateway, Eureka, Service Discovery

* **Spring Cloud:** Framework for building distributed Spring apps (config, discovery, gateways, circuit breakers, messaging)
* **API Gateway:** Pattern/component; single entry point for routing, auth, aggregation
* **Service Discovery:** Pattern for locating services dynamically
* **Netflix Eureka:** Implementation of service discovery (registry)

---

## 6) How They Work Together

### Client-side discovery

1. Eureka Server keeps registry
2. Services register with Eureka
3. Client queries registry for service instances
4. Client calls chosen instance directly

### Server-side discovery with API Gateway

1. Clients call API Gateway
2. Gateway queries registry for service instances
3. Gateway forwards request to healthy instance

---

## 7) Client-side vs Server-side Discovery

| Aspect               | Client-side                     | Server-side                 |
| -------------------- | ------------------------------- | --------------------------- |
| Who queries registry | Client                          | Load balancer/Gateway       |
| Example stack        | Eureka + Ribbon                 | API Gateway + Eureka/Consul |
| Pros                 | Fine-grained load-balancing     | Simpler clients             |
| Cons                 | Discovery logic in every client | Gateway is critical path    |

---

## 8) Code Snippets

### Eureka Server

```java
@EnableEurekaServer
@SpringBootApplication
public class EurekaServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}
```

application.yml:

```yaml
server:
  port: 8761
eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
```

### Eureka Client (Product Service)

application.yml:

```yaml
spring:
  application:
    name: product-service
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

`@EnableEurekaClient` in main class

### Spring Cloud Gateway

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: product_route
        uri: lb://product-service
        predicates:
        - Path=/products/**
```

`lb://product-service` uses load-balanced discovery

---

## 9) Alternatives

* **Kubernetes native service discovery**: use K8s DNS + Ingress/Service mesh
* **Consul**: service registry + KV store + health checks
* **Zookeeper / Etcd**: strong consistency, used for coordination
* **Service mesh**: Istio/Linkerd with Envoy sidecars

---

## 10) Microservices Checklist

1. Domain decomposition
2. Automate CI/CD
3. Choose discovery strategy
4. Use API Gateway
5. Add resilience (Circuit breakers, retries)
6. Async messaging where possible
7. Centralized config (Spring Cloud Config / Vault)
8. Observability from day 1
9. Centralized security (OAuth2/JWT)
10. Plan for distributed transactions (Saga)

---

## 11) Real-world E-commerce Example

* Client → API Gateway → Order Service → Inventory & Pricing
* Event-driven: OrderCreated → Payment Service → Shipment Service
* Compensating transactions via Saga if payment fails
* Observability: tracing with Zipkin/OpenTelemetry

---

## 12) Quick Interview Points

* API Gateway ≠ Service Discovery
* Eureka vs K8s discovery: use K8s on Kubernetes
* Client-side vs server-side discovery: tradeoffs in complexity vs control

---

## 13) TL;DR

* **Spring Cloud**: framework
* **API Gateway**: edge router / pattern
* **Service Discovery**: pattern
* **Netflix Eureka**: registry implementation

They complement each other but are not the same.
