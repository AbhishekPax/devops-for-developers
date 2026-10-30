# Task: Why Caching Matters and the Cache-Aside Pattern

## Objective
Explain the importance of caching in distributed web systems and demonstrate the Cache-Aside (Lazy Loading) pattern applied to optimize a slow API endpoint (e.g., `GET /api/products/:id`).

---

## 1. Why Caching Matters
- **Latency Reduction:** In-memory stores like Redis provide sub-millisecond retrieval speeds compared to multi-millisecond disk or relational database roundtrips.
- **Database Offloading:** Prevents expensive SQL joins, aggregations, and high connection concurrency from saturating the primary relational database (RDS).
- **Cost & Scalability:** Improves throughput across peak loads without over-provisioning database instance hardware.

---

## 2. Cache-Aside (Lazy Loading) Architecture & Flow
In the Cache-Aside pattern, the application code directly orchestrates reads and writes between the cache and the primary datastore.
