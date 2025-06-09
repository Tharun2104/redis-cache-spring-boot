# Redis Caching in Spring Boot – Quick Notes & Interview Prep

---

## 📌 Why Use Caching?

- **Definition**: Temporarily storing frequently accessed data in a fast storage layer to avoid repeated, expensive operations (e.g., database calls).
- **Use-Case**: High-traffic scenarios (e.g., Black Friday sales) where millions of requests hit a product endpoint, causing DB bottlenecks.
- **Benefits**:
  - Reduces database load
  - Improves application response time
  - Enhances scalability under heavy traffic

**Interview Tip**  
> Explain how caching fits into the CAP theorem trade-offs (e.g., eventual consistency when using TTL).

---

## 💾 What Is Redis?

- **Redis** = Remote Dictionary Server  
- **Type**: In-memory key-value data store  
- **Primary Uses**:
  - Caching layer
  - Session store
  - Pub/Sub messaging
  - Optional persistence (RDB snapshots / AOF logs)  [oai_citation:3‡RedisTranscripts.txt](file-service://file-WzPgv6J5iiKoXsD68x6jp3)
- **Key Advantage**: Sub-millisecond latency via in-memory operations

**Expert Insight**  
> Use Redis hashes for storing related fields together to reduce round-trips.
---

## ⚙️ Java Config: RedisCacheManager

- Use `@Configuration` to define a bean for Redis caching.
- Set a **Time-To-Live (TTL)** to control how long data stays in the cache (e.g., 10 minutes).
- Disable caching of `null` values to avoid storing meaningless responses.
- Use **Jackson2JsonRedisSerializer** to handle object serialization (e.g., for `ProductDTO`).
- Return a `RedisCacheManager` with these defaults applied.

**📝 Tip**: TTL should match your data freshness needs. Stale cache can cause bugs in real-time apps.

---

## 🏷️ Spring Caching Annotations Summary

| Annotation   | Meaning                                | Best Used For                      |
|--------------|-----------------------------------------|-------------------------------------|
| `@Cacheable` | Checks cache before method executes     | `GET` requests for reading data     |
| `@CachePut`  | Updates/creates new cache entry         | `POST` or `PUT` to add/update data |
| `@CacheEvict`| Removes entry from cache                | `DELETE` operations                |

**🔍 Example**:  
Use `@Cacheable("productCache")` to store product details after fetching from DB.

**💡 Insight**: Use SpEL (`#id`, `#result.id`) in keys for precise cache control.

---

## 🧪 Integration Testing with Testcontainers

- Use `@Testcontainers` to spin up a real Redis container.
- `@BeforeEach` clears cache to ensure test isolation.
- Use `@SpyBean` to check DB hits and verify if caching works.
- Test scenario:
  - First request hits DB and caches result.
  - Second request should use the cache.

**✅ Best Practice**:  
Avoid using mocked Redis. Real containers catch serialization & TTL issues.

---

## 🧠 Common Interview Questions

1. What’s the difference between `@Cacheable`, `@CachePut`, and `@CacheEvict`?
2. Why is setting TTL important in caching?
3. How does Redis handle persistence (RDB vs. AOF)?
4. What strategies can you use for cache invalidation?
5. Which Redis data structures are best for storing grouped values?

---

## 💡 Expert Insights

- Always serialize cached objects to JSON using Jackson.
- Disable null value caching to avoid unnecessary cache clutter.
- Use TTL to prevent stale data issues.
- Integration test with real Redis to reflect production behavior.
- Monitor cache hit/miss ratios to optimize performance.

---

## 🐳 Quick Install with Docker

```bash
docker pull redis:7.4.2
docker run -d --name redis -p 6379:6379 redis:7.4.2
```


> Based on: [YouTube Tutorial](https://www.youtube.com/watch?v=j65P_-yOX8g)
