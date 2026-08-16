# System Design Roadmap — Zero to Interview-Ready

## How to use this roadmap
Go phase by phase. Don't skip Phase 1 even if you're impatient to get to "design Twitter" — most people who fail system design interviews don't fail because they don't know what a load balancer is, they fail because they can't reason from first principles when the interviewer changes a constraint mid-interview. Concepts first, patterns second, full designs last.

Rough timeline if you study focused: **6-10 weeks**, faster if you already code professionally.

---

## Phase 1: Foundations (Week 1-2)
Goal: understand the vocabulary and the physics of distributed systems — what's actually hard and why.

1. **Client-server basics** — how a request actually travels (DNS → TCP handshake → HTTP → response). Don't skip DNS resolution and TCP/IP basics even if they feel "too basic" — interviewers will occasionally probe here to check you're not just parroting buzzwords.
2. **Vertical vs horizontal scaling** — and *why* horizontal scaling is usually preferred at scale despite being harder.
3. **Latency vs throughput** — the classic trade-off. Know real numbers: memory access ~100ns, SSD read ~100µs, network round trip same datacenter ~0.5ms, cross-continent ~150ms. These numbers let you reason about design choices instead of guessing.
4. **CAP theorem** — Consistency, Availability, Partition tolerance — pick 2 of 3 (really: partition tolerance is mandatory in distributed systems, so it's CP vs AP). Understand this deeply, not as a slogan — interviewers ask you to *apply* it, not recite it.
5. **ACID vs BASE** — transactional guarantees vs eventual consistency.
6. **Consistency models** — strong, eventual, causal. Know what "eventual consistency" actually means in practice (e.g., DNS propagation, S3 historically).

**Checkpoint:** Can you explain to a non-technical friend why Instagram likes might not update instantly for everyone, but your bank balance always does?

---

## Phase 2: Building Blocks (Week 2-4)
Goal: know each core component — what problem it solves, its trade-offs, and when it breaks.

### Networking & delivery
- Load balancers (L4 vs L7, round robin, least connections, consistent hashing for sticky routing)
- Reverse proxies vs forward proxies
- CDNs — caching static/dynamic content at the edge

### Databases
- SQL vs NoSQL — and *why*, not just "NoSQL is faster" (it's not, inherently)
- Types of NoSQL: key-value (Redis, DynamoDB), document (MongoDB), wide-column (Cassandra), graph (Neo4j) — know one real use case for each
- Indexing — how B-trees work, why indexes speed reads but slow writes
- Replication — leader-follower, multi-leader, quorum reads/writes
- Sharding/partitioning — range-based, hash-based, directory-based; the "hot shard" problem
- Database transactions across shards (2-phase commit, sagas) — advanced but commonly probed

### Caching
- Cache-aside, write-through, write-back strategies
- Cache invalidation (the "two hard things in CS" joke exists for a reason)
- Where to cache: client, CDN, app layer, DB layer
- Eviction policies: LRU, LFU

### Messaging & async
- Message queues (SQS, RabbitMQ) vs pub-sub (Kafka, SNS)
- When to go async vs sync
- At-least-once vs exactly-once vs at-most-once delivery — and why exactly-once is nearly a myth in distributed systems

### Storage
- Block storage vs object storage vs file storage
- Blob storage patterns (S3-style) for media/large files

**Checkpoint:** For each building block, you should be able to answer: "What breaks if this goes down? What's the failure mode?"

---

## Phase 3: Cross-Cutting Concerns (Week 4-5)
These show up in *every* real design and separate junior from senior answers.

1. **Scalability patterns** — stateless services, database read replicas, sharding, microservices vs monolith trade-offs
2. **Reliability & fault tolerance** — redundancy, failover, circuit breakers, retries with exponential backoff + jitter, timeouts
3. **Availability math** — the "nines" (99.9% = ~8.7 hrs downtime/year), how redundant components combine
4. **Consistency patterns in practice** — idempotency keys, distributed locks, leader election (Raft/Paxos at a conceptual level — you don't need to implement Raft, but know what problem it solves)
5. **Rate limiting** — token bucket, leaky bucket, sliding window — and where to enforce it (gateway vs service)
6. **API design** — REST vs GraphQL vs gRPC, pagination strategies, versioning
7. **Security basics** — authn vs authz, OAuth flow at a high level, encryption in transit/at rest
8. **Monitoring & observability** — metrics, logging, tracing, why you mention this even briefly in every interview answer (shows production maturity)

**Checkpoint:** You should be able to sketch a back-of-envelope capacity estimate: given X users, Y requests/user/day, estimate QPS, storage growth/year, bandwidth. This is a near-guaranteed interview step.

---

## Phase 4: The Interview Framework (Week 5-6)
Before doing full designs, internalize a repeatable *process* — this matters more than knowing any single answer.

**A reliable framework (roughly 45-min interview):**
1. **Clarify requirements** (5 min) — functional (what must it do) and non-functional (scale, latency, consistency needs). Ask about read/write ratio, expected users, growth.
2. **Capacity estimation** (3-5 min) — back-of-envelope QPS, storage, bandwidth.
3. **High-level design** (10 min) — draw boxes: client, LB, services, DB, cache, queue. Get something end-to-end on the board before going deep anywhere.
4. **Deep dive** (15-20 min) — the interviewer usually steers you into 1-2 components. This is where Phase 2/3 knowledge pays off. Discuss trade-offs out loud — there's rarely one right answer, and articulating *why* you chose X over Y is the actual signal being evaluated.
5. **Bottlenecks & scaling** (5 min) — what breaks first as traffic grows 10x/100x, how you'd address it.
6. **Wrap-up** — mention monitoring, security, edge cases if time allows.

**Golden rule:** talk out loud constantly. Silence is the biggest interview killer — narrate your thinking even when unsure.

---

## Phase 5: Practice Designs (Week 6-9)
Go roughly in this order — easier, more classic problems first, building toward more novel/open-ended ones.

**Tier 1 (classic, do these first):**
- URL shortener (great first design — small but touches hashing, DB, caching)
- Rate limiter
- Pastebin / key-value store

**Tier 2 (moderate):**
- Chat application (WhatsApp-style) — websockets, message ordering, delivery guarantees
- News feed (Twitter/Instagram feed) — fan-out on write vs fan-out on read
- Notification system

**Tier 3 (classic "big" interview questions):**
- Web crawler
- Distributed cache (design Redis/Memcached)
- Ride-sharing (Uber) — geospatial indexing, matching
- Video streaming (YouTube/Netflix) — encoding, CDN, storage

**Tier 4 (advanced / senior-level):**
- Distributed task scheduler
- Search autocomplete / typeahead
- Distributed file storage (design S3/GFS)
- Payment system (idempotency, exactly-once semantics matter a lot here)
- Design a distributed message queue (design Kafka)

**How to practice each one:** don't read a solution first. Attempt the full framework from Phase 4 yourself, timeboxed to ~40 min, *then* compare against a reference design and note the gaps in your reasoning — not just the gaps in your knowledge of components.

---

## Phase 6: Conceptual Depth (ongoing, alongside Phase 5)
This is what separates "memorized the pattern" from "actually strong." Spend time understanding, not just designing:
- Read how real systems work: Kafka's design doc, Dynamo paper (Amazon), Google's Spanner/Bigtable papers (skim, don't need full academic rigor)
- Understand *why* certain famous outages happened (postmortems from major companies are gold — they teach failure modes textbooks don't)
- Get comfortable saying "it depends" and then actually explaining what it depends on

---

## Quick reference: recurring trade-offs to always have ready
- SQL vs NoSQL
- Strong vs eventual consistency
- Latency vs consistency (and vs availability)
- Push vs pull (fan-out on write vs read)
- Normalization vs denormalization
- Synchronous vs asynchronous processing
- Monolith vs microservices

If you can discuss each of these with a concrete example of when you'd choose one over the other, you're in strong shape for most interviews.
