# 500 Master Questions Bank: Volume 2
## 100 System Design (HLD & LLD) Interview Questions & Complete Answers

### Section 1: Distributed Systems Foundations (Q1–Q35)

#### QN 1: CAP theorem — why CA is mathematically impossible
- **Answer**: The CAP theorem states a distributed system can deliver only two of three desired characteristics: Consistency (C), Availability (A), and Partition Tolerance (P). Network partitions (P) are inevitable in distributed systems due to network failures. Therefore, you must choose between C and A during a partition. A purely CA system cannot exist over a network because if a partition occurs, it must either stop serving requests (losing A) to maintain consistency, or serve potentially stale requests (losing C) to maintain availability. 

#### QN 2: PACELC theorem with DynamoDB/MongoDB concrete examples
- **Answer**: PACELC extends CAP: in case of a Partition (P), trade off Availability (A) vs Consistency (C); Else (E), when running normally, trade off Latency (L) vs Consistency (C). 
DynamoDB: Typically PA/EL (prioritizes availability during partitions, and low latency over strong consistency normally, though it supports strong reads).
MongoDB: Typically PC/EC (prioritizes consistency; primary node election causes unavailability during partitions, and normally prioritizes consistency).

#### QN 3: Consistent hashing — key migration minimization proof
- **Answer**: In traditional hashing (`hash(key) % N`), adding/removing a server changes the modulo, causing almost all keys to remap. Consistent hashing maps both servers and keys to a circular ring (e.g., 0 to 2^32-1). Keys are assigned to the first server found moving clockwise. When a server is added/removed, only the keys mapping to that specific segment are reassigned. Migration is minimized to `K/N` where K is total keys and N is total servers.

#### QN 4: Virtual nodes (vnodes) — load distribution and variance reduction
- **Answer**: In standard consistent hashing, server distribution on the ring can be uneven, leading to hotspots. Virtual nodes map a single physical server to multiple points (vnodes) on the ring. This reduces variance in load distribution and allows heterogeneous clusters (more powerful servers get more vnodes). 

#### QN 5: HTTP/2 multiplexing vs HTTP/3 QUIC UDP stream isolation
- **Answer**: HTTP/2 uses TCP and multiplexes multiple streams over one connection. However, TCP suffers from Head-of-Line (HoL) blocking; if one packet is lost, all streams wait for retransmission. HTTP/3 uses QUIC (over UDP). QUIC manages streams independently. If a packet for Stream A is lost, only Stream A is blocked; Stream B continues seamlessly, solving TCP HoL blocking.

#### QN 6: gRPC vs REST — when to choose each
- **Answer**: gRPC (Protobuf, HTTP/2): Best for internal microservices, high-performance streaming, strict typed contracts. Binary payload is faster/smaller.
REST (JSON, HTTP/1.1 or 2): Best for public APIs, web clients, cases where human readability and wide ecosystem support are required. 

#### QN 7: Layer 4 vs Layer 7 load balancers
- **Answer**: Layer 4 (Transport): Forwards traffic based on IP/Port. Fast, low overhead, no payload inspection (e.g., AWS NLB). Good for TCP/UDP traffic.
Layer 7 (Application): Routes based on HTTP headers, URL paths, cookies. Slower but allows smart routing, SSL termination, rate limiting (e.g., AWS ALB, Nginx).

#### QN 8: Transactional Outbox Pattern for dual-write problem
- **Answer**: Writing to a DB and publishing an event to a message broker (dual-write) can fail partially. The Outbox pattern solves this by writing the state change AND an event record to an "outbox" table within the *same* database transaction. A separate async process (like Debezium/CDC) reads the outbox table and reliably forwards events to the broker.

#### QN 9: Saga Orchestration vs Choreography
- **Answer**: Both manage distributed transactions across microservices.
Choreography: Services publish events. Other services listen and react. Good for simple workflows, but hard to track (spaghetti architecture).
Orchestration: A central orchestrator explicitly calls services and handles rollbacks (compensating transactions). Best for complex workflows.

#### QN 10: Redis Redlock safety critique (Fencing Tokens)
- **Answer**: Redlock is a distributed lock algorithm using multiple Redis instances. Martin Kleppmann critiqued it because clocks can drift and GC pauses can cause a lock to expire while a client thinks it holds it. The solution is Fencing Tokens: the lock server returns an increasing token. The storage service rejects writes with older tokens.

#### QN 11: Cache strategies — Cache-Aside, Write-Through, Write-Behind
- **Answer**: Cache-Aside: App checks cache; if miss, reads DB, updates cache. (Good for read-heavy).
Write-Through: App writes to cache, which synchronously writes to DB. (Data is always consistent, but slower writes).
Write-Behind (Write-Back): App writes to cache, which asynchronously writes to DB. (Fast writes, risk of data loss on crash).

#### QN 12: Cache Stampede — Thundering Herd prevention (XFetch)
- **Answer**: Cache stampede occurs when a popular key expires, causing thousands of concurrent requests to hit the database. Prevention strategies include: 1) Locking (only one thread computes the value), 2) Probabilistic Early Expiration (XFetch - workers recompute before expiry based on a randomized probability), 3) Background refreshing.

#### QN 13: Bloom Filters — false positive probability, space efficiency, use cases
- **Answer**: A space-efficient probabilistic data structure used to test set membership. Returns "possibly in set" or "definitely not in set". False positives are possible; false negatives are not. Space efficiency comes from using a bit array and `k` hash functions. Used in databases to avoid reading disk for non-existent keys (e.g., Cassandra).

#### QN 14: LSM-Trees vs B-Trees — write amplification trade-offs
- **Answer**: B-Trees (PostgreSQL): Update in place, excellent for reads, but suffer from write amplification due to random disk I/O and page splits.
LSM-Trees (Cassandra, RocksDB): Append-only structures in memory (MemTable) flushed to disk (SSTables). Excellent for high write throughput, but require background compaction and reads may need to check multiple SSTables.

#### QN 15: Write-Ahead Log (WAL) — crash recovery and durability
- **Answer**: A WAL is an append-only file where all database modifications are logged *before* being applied to the actual data files or in-memory structures. It guarantees Durability (D in ACID). On crash, the DB replays the WAL to restore state.

#### QN 16: Raft Consensus Algorithm — leader election and log replication
- **Answer**: Raft ensures distributed consensus via a strong leader. Leader Election: Nodes start as followers, become candidates if they don't hear from a leader, and request votes. Log Replication: The leader accepts writes, appends to its log, sends AppendEntries RPCs to followers, and commits only when a majority acknowledge.

#### QN 17: Paxos vs Raft — comparison and practical usage
- **Answer**: Paxos is the mathematical pioneer of consensus, but extremely complex to understand and implement correctly. Raft was designed specifically for understandability, dividing consensus into leader election, log replication, and safety. Most modern systems (etcd, Consul) use Raft.

#### QN 18: Distributed tracing with OpenTelemetry — trace IDs, span propagation
- **Answer**: Tracing tracks a request across microservices. A request is assigned a unique `TraceID`. As it hits each service, a `SpanID` is generated representing that unit of work. Context propagation passes these IDs via HTTP headers (e.g., W3C Trace Context) so logging backends (Jaeger) can reconstruct the tree.

#### QN 19: Split-Brain problem and quorum-based mitigation
- **Answer**: Split-brain occurs when a network partition causes two parts of a cluster to each elect a leader, leading to conflicting writes. Mitigated using Quorum (N/2 + 1). A leader must have the majority of votes. In a partition, only the side with the majority can elect a leader; the minority side halts.

#### QN 20: Vector Clocks vs Lamport Timestamps for causality tracking
- **Answer**: Lamport Timestamps provide a partial ordering of events (integer counters) but cannot detect concurrent events. Vector Clocks maintain an array of counters (one per node). If clock A < clock B for all elements, A happened before B. If some are greater and some lesser, the events are concurrent (conflict).

#### QN 21: Event Sourcing vs CQRS architectural patterns
- **Answer**: Event Sourcing: State is stored as a sequence of immutable events rather than current state. CQRS (Command Query Responsibility Segregation): Separates read and write models. They often pair together: Commands append events (Event Sourcing), which asynchronously update a separate read-optimized view (CQRS).

#### QN 22: Two-Phase Commit (2PC) vs Three-Phase Commit
- **Answer**: 2PC: Phase 1 (Prepare), Phase 2 (Commit/Abort). It is blocking; if the coordinator crashes after Prepare, resources are locked indefinitely. 3PC adds a "Pre-Commit" phase with timeouts to prevent indefinite blocking, but it cannot handle network partitions effectively.

#### QN 23: Circuit Breaker pattern — states and state transitions
- **Answer**: Protects systems from cascading failures. States:
- CLOSED: Normal operation, requests flow.
- OPEN: Failure threshold reached, requests fail fast immediately.
- HALF-OPEN: After a timeout, allows a few requests through to test if the downstream service has recovered.

#### QN 24: Rate Limiting algorithms — Token Bucket vs Leaky Bucket vs Sliding Window Counter
- **Answer**: Token Bucket: Tokens added at a constant rate; requests consume tokens. Allows bursts.
Leaky Bucket: Requests enter a queue; processed at a constant rate. Smooths traffic.
Sliding Window Counter: Tracks counts per timestamp in a rolling window, smoothing out boundary bursts seen in Fixed Window.

#### QN 25: Service Mesh (Istio/Envoy) — sidecar proxy benefits
- **Answer**: A service mesh handles inter-service communication via "sidecar" proxies deployed alongside application containers. Benefits: Decouples infrastructure logic (mTLS, retries, circuit breaking, tracing) from business logic.

#### QN 26: Message queue semantics — at-most-once, at-least-once, exactly-once
- **Answer**: At-most-once: Fire and forget; data might be lost. At-least-once: Retries until acknowledgment; requires idempotent consumers. Exactly-once: Hardest to achieve; guarantees processing once using idempotency keys, deduplication, and transactional coordination (e.g., Kafka Transactions).

#### QN 27: Kafka partitioning, consumer groups, and offset management
- **Answer**: Topics are split into Partitions for parallel scaling. A Consumer Group allows horizontal scaling of consumers; each partition is consumed by exactly one consumer in a group. Offsets track the consumer's position in a partition, committed back to Kafka (`__consumer_offsets`).

#### QN 28: Database sharding strategies — range, hash, directory-based
- **Answer**: Range: Split by continuous ranges (e.g., dates); can cause hotspots.
Hash: `hash(key) % N`; distributes evenly, but hard to add nodes.
Directory: Uses a lookup table to map keys to shards; flexible but adds a single point of failure / bottleneck.

#### QN 29: Master-Replica vs Multi-Master replication trade-offs
- **Answer**: Master-Replica: Single master handles writes, replicas handle reads. Easy consistency, but write throughput is limited to one node.
Multi-Master: Multiple nodes accept writes. High availability and write scalability, but requires complex conflict resolution algorithms (CRDTs, vector clocks).

#### QN 30: Apache ZooKeeper vs etcd — distributed coordination
- **Answer**: Both provide strongly consistent, hierarchical key-value stores for metadata and leader election. ZooKeeper uses ZAB protocol, requires JVM, is older. etcd uses Raft, written in Go, simpler API (HTTP/gRPC), and is the backbone of Kubernetes.

#### QN 31: CDN pull vs push caching and edge computing
- **Answer**: Pull CDN: CDN requests assets from origin on first miss, caching for subsequent requests. Push CDN: Origin pushes assets to CDN proactively (good for predictable, large files). Edge computing runs serverless functions (e.g., Cloudflare Workers) at CDN nodes for ultra-low latency logic.

#### QN 32: WebSockets vs Server-Sent Events vs Long Polling
- **Answer**: Long Polling: Client requests, server holds connection open until data is ready. (High overhead). SSE: Server streams data to client over one HTTP connection (Unidirectional). WebSockets: Full-duplex bidirectional communication over a persistent TCP connection (Low overhead, real-time).

#### QN 33: API Gateway patterns — authentication, rate limiting, routing
- **Answer**: Sits between clients and microservices. Consolidates cross-cutting concerns: acts as a reverse proxy routing requests, terminates SSL, validates JWTs for auth, enforces rate limits, and performs protocol translation.

#### QN 34: Data consistency in microservices — eventual vs strong consistency
- **Answer**: Strong consistency ensures reads immediately reflect the latest write, usually requiring synchronous blocking (2PC). Eventual consistency allows temporary staleness but guarantees convergence over time via asynchronous events, offering higher availability and lower latency.

#### QN 35: Chaos Engineering principles — failure injection and recovery testing
- **Answer**: Proactively injecting failures (network latency, pod termination) into production/staging to verify system resilience. Principle: Automate experiments, minimize blast radius, and ensure the system degrades gracefully (e.g., Netflix Chaos Monkey).

---

### Section 2: High-Level Design Scenarios (Q36–Q70)

#### QN 36: Design Uber — real-time driver tracking at 250k writes/sec
- **Answer**: Use H3 hexagons for spatial indexing. Drivers push locations via WebSockets. API Gateway routes to Location Service. Use Redis geospatial indexes / TTL for active drivers. Push updates to Kafka for async processing (trip matching, analytics). Capacity: 250k writes/sec -> partition Kafka by region.

#### QN 37: Design Flash Sale — prevent inventory overselling
- **Answer**: Standard DB transactions are too slow and lock-heavy. Use Redis with Lua scripting for atomic decrements. Clients enter a queuing system (RabbitMQ/Kafka). Redis checks inventory: if > 0, decrement and issue a token. Token validates purchase at checkout. Token Bucket rate limiter prevents DDoS.

#### QN 38: Design Twitter/X News Feed — Hybrid Fanout model
- **Answer**: Push (Fanout-on-write): Compute feed for followers instantly on tweet creation. Fast reads, but fails for celebrities (Justin Bieber effect). Pull (Fanout-on-read): Fetch feeds on demand. Slower reads. Hybrid: Push for normal users; Pull for celebrities. Redis lists cache precomputed feeds.

#### QN 39: Design URL Shortener — Base62 with Snowflake ID
- **Answer**: Use Base62 encoding ([a-z, A-Z, 0-9]) on a unique integer ID. To generate distributed unique IDs, use Twitter Snowflake (Timestamp + Machine ID + Sequence). 62^7 gives 3.5 trillion URLs. Store mapping in Cassandra. Add Redis for caching hot links.

#### QN 40: Design Distributed Task Queue — visibility timeout, DLQ
- **Answer**: Producers push tasks. Consumers poll. When a consumer takes a task, it's hidden for a "visibility timeout" (prevents duplicate processing). If consumer fails, timeout expires, task reappears. If it fails N times, move to Dead Letter Queue (DLQ).

#### QN 41: Design YouTube/Netflix — video upload, transcoding pipeline, CDN delivery
- **Answer**: Client uploads video in chunks to object storage (S3). S3 triggers an event to a queue. Worker nodes pull tasks to transcode video into different resolutions (1080p, 720p) and formats (HLS, DASH). Output saved to S3. CDN caches video chunks for low-latency delivery. Metadata stored in RDBMS.

#### QN 42: Design WhatsApp — end-to-end messaging, message delivery receipts
- **Answer**: E2E encryption via Signal protocol. WebSockets for real-time duplex connections. Chat servers hold connections. Transient messages stored in memory/Cassandra until delivered. Delivery receipts: Receiver sends 'delivered' ACK via WebSocket, server routes ACK back to sender. 

#### QN 43: Design Google Drive — file sync with chunk deduplication
- **Answer**: Files are broken into blocks (e.g., 4MB). Compute SHA-256 for each block. Deduplication: only upload missing hashes to Object Storage. Client uses Long Polling / WebSockets to listen for delta syncs. Metadata stored in CockroachDB for consistency.

#### QN 44: Design Web Crawler — politeness, frontier queue, distributed crawl
- **Answer**: URL Frontier (Redis/Kafka) holds URLs to visit. Fetchers download HTML. Parsers extract links. Bloom Filter checks if URL was seen. Politeness: group URLs by domain, enforce delays between requests to the same domain. Save content to Object Storage.

#### QN 45: Design Stock Exchange — matching engine, order book
- **Answer**: Strictly in-memory for microsecond latency. Orders processed sequentially via an event loop (LMAX Disruptor pattern). Order Book implemented using Red-Black Trees or Doubly Linked Lists for O(1) matching of FIFO price-time priority. State backed up via Event Sourcing (WAL).

#### QN 46: Design Notification System — multi-channel, deduplication
- **Answer**: Services send generic events to Kafka. Notification Service consumes, checks user preferences, generates templates, and queues provider-specific topics (iOS APNS, Android FCM, SMS, Email). Deduplication using Redis sets (EventID) to prevent double sending.

#### QN 47: Design Rate Limiter — distributed sliding window with Redis
- **Answer**: Use Redis Sorted Sets. Key: `user_id`. Score: Unix timestamp. Value: timestamp + random. On request: 1) `ZREMRANGEBYSCORE` to remove elements outside window. 2) `ZCARD` to get count. 3) If count < limit, `ZADD` new timestamp. Can optimize using Redis Lua scripts for atomicity.

#### QN 48: Design Search Autocomplete — trie vs inverted index
- **Answer**: In-memory Trie data structure. Each node stores the top-K terms prefixing there to optimize read (O(L) where L is prefix length). Update Trie offline via MapReduce processing logs, then swap Tries seamlessly. 

#### QN 49: Design Payment System — idempotency keys, two-phase payment
- **Answer**: Idempotency key from client ensures retries don't double charge. Two-phase: 1) Authorization (reserve funds), 2) Capture/Settle. Use async reconciliation jobs matching DB records against Payment Gateway settlement files to detect anomalies.

#### QN 50: Design Ride-Sharing Surge Pricing — geofencing and dynamic multiplier
- **Answer**: Divide map into H3 grid. Stream driver availability and rider requests to Flink/Spark Streaming. Calculate supply/demand ratio per hexagon over a 2-minute sliding window. Update surge multiplier in Redis.

#### QN 51: Design E-commerce Product Catalog — search index with Elasticsearch
- **Answer**: PIM (Product Info Management) stores canonical data in Postgres. CDC (Debezium) tracks Postgres WAL and pushes changes to Kafka. Kafka Connect sinks data to Elasticsearch for full-text search, facet filtering, and fuzzy matching.

#### QN 52: Design Live Streaming — RTMP ingest, HLS adaptive bitrate
- **Answer**: Broadcaster streams RTMP to Ingest Servers. Servers transcode streams on-the-fly into segments (2-10s) across different bitrates using FFmpeg. Segments pushed to S3 and CDN. Viewers use HLS player, pulling segments and dynamically adapting bitrate based on bandwidth.

#### QN 53: Design Chat System — message ordering, read receipts
- **Answer**: Order guaranteed by generating sequential IDs (Snowflake) per chat room. Read receipts work like delivery receipts: client sends 'read' event with MessageID, server broadcasts to room participants.

#### QN 54: Design Google Maps — road graph, Dijkstra/A* routing, ETA
- **Answer**: Map data modeled as a directed graph. Precompute optimal paths for major junctions using Contraction Hierarchies. Routing engines use A* algorithm on regions. ETA calculated using historical traffic data and real-time Kafka streams of user GPS speeds.

#### QN 55: Design Recommendation Engine — collaborative filtering
- **Answer**: User-Item interaction matrix. Offline batch processing (Spark/Hadoop) uses Matrix Factorization (ALS) to generate user embeddings and item embeddings. Store embeddings in a Vector Database (Milvus/Faiss). Real-time inference does Approximate Nearest Neighbor (ANN) search.

#### QN 56: Design Distributed Logging — ELK stack
- **Answer**: Apps write structured JSON logs to stdout. Fluentd/Filebeat daemonsets collect and push to Kafka buffer. Logstash consumes Kafka, parses/enriches, and indexes into Elasticsearch. Kibana visualizes. Set index retention policies (ILM) for cost control.

#### QN 57: Design Distributed Cache — Redis cluster, consistent hashing
- **Answer**: Implement a caching layer in front of DB. Use Consistent Hashing to shard keys across N Redis nodes. Implement replica nodes for HA. Handle hotspots by adding a local in-memory cache (Guava/Caffeine) on app servers for ultra-hot keys.

#### QN 58: Design Authentication System — JWT vs sessions
- **Answer**: Sessions: Stateful, stored in Redis. Easy to revoke, requires network hop. JWT: Stateless, cryptographically signed. Fast, but hard to revoke before expiry. Use short-lived JWTs (15m) + long-lived Refresh Tokens stored in DB.

#### QN 59: Design Healthcare Records System — HIPAA compliance
- **Answer**: Granular RBAC. All PHI (Protected Health Info) encrypted at rest (AES-256) and in transit (TLS 1.3). Keys managed by HSM/KMS. All access attempts logged to immutable storage (WORM) for audit trails.

#### QN 60: Design Hotel Booking System — inventory management
- **Answer**: Prevent double booking using pessimistic locking (SELECT FOR UPDATE) on the room table during checkout. Alternatively, use saga pattern for microservices. Hotels overbook by ~5% based on historical cancellation models; handle edge cases with upgrades/relocations.

#### QN 61: Design Online Code Judge (LeetCode) — sandbox isolation
- **Answer**: Code submitted to queue. Worker pulls task, spins up a Docker container (sandbox) with seccomp profiles to disable networking and limit syscalls. Run code against hidden test cases. Monitor process using `cgroups` for CPU/Memory limits.

#### QN 62: Design Dropbox — delta sync, CRDTs
- **Answer**: (See Q43 for block sync). For collaborative editing, use CRDTs (Conflict-free Replicated Data Types) or OT (Operational Transformation) to merge concurrent document edits without locking.

#### QN 63: Design Gmail — email threading, labels
- **Answer**: Use a graph or parent-ID field to thread replies. Store metadata in a scalable NoSQL store (Cassandra) to handle massive scale. Search implemented via inverted indexes on email text.

#### QN 64: Design Distributed ID Generator
- **Answer**: Twitter Snowflake: 64-bit integer. 1 bit sign, 41 bits timestamp (millisecond precision), 10 bits machine ID, 12 bits sequence number. Highly available, sortable by time. 

#### QN 65: Design Metrics System — Prometheus
- **Answer**: Time-series data. Apps expose `/metrics` endpoint. Prometheus servers pull metrics periodically. Store in TSDB. Grafana queries via PromQL. For high cardinality, use VictoriaMetrics or Thanos for long-term storage.

#### QN 66: Design A/B Testing Platform
- **Answer**: Hashing user_id + experiment_name maps user to a bucket (0-99). Buckets tied to variants. Emits telemetry events to Kafka. Data pipeline calculates metrics and statistical significance (p-value) using Chi-Square/T-test.

#### QN 67: Design Feature Flag System
- **Answer**: Admin dashboard writes rules to DB. Sync to distributed cache/edge nodes. SDKs poll configs periodically or receive updates via SSE. Enables progressive delivery (10% rollout) and instant kill switches.

#### QN 68: Design Event-Driven Architecture — schema registry
- **Answer**: Producers write Avro/Protobuf messages to Kafka. A Schema Registry enforces compatibility rules (forward/backward) to ensure producers don't break consumers when schemas evolve.

#### QN 69: Design Blockchain Transaction Ledger
- **Answer**: Blocks contain transactions, a timestamp, and previous block hash. Immutability guaranteed by cryptographic hashes. Merkle trees used to efficiently verify if a transaction exists in a block without downloading the full block.

#### QN 70: Design Content Moderation System
- **Answer**: ML models evaluate text/images for NSFW content immediately. If score > threshold, auto-reject. If score < lower_threshold, auto-approve. If in between, route to manual human review queue.

---

### Section 3: Low-Level Design & Machine Coding (Q71–Q100)

#### QN 71: LRU Cache — O(1) with HashMap + Doubly Linked List
- **Answer**: Use a Hash Map for O(1) lookups and a Doubly Linked List to maintain access order (head is most recently used, tail is least).
```python
class Node:
    def __init__(self, key, val):
        self.key, self.val = key, val
        self.prev = self.next = None

class LRUCache:
    def __init__(self, capacity: int):
        self.cap, self.cache = capacity, {}
        self.head, self.tail = Node(0,0), Node(0,0)
        self.head.next, self.tail.prev = self.tail, self.head

    def _remove(self, node):
        node.prev.next, node.next.prev = node.next, node.prev

    def _insert(self, node):
        node.next, node.prev = self.head.next, self.head
        self.head.next.prev = self.head.next = node

    def get(self, key: int) -> int:
        if key in self.cache:
            self._remove(self.cache[key])
            self._insert(self.cache[key])
            return self.cache[key].val
        return -1

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self._remove(self.cache[key])
        self.cache[key] = Node(key, value)
        self._insert(self.cache[key])
        if len(self.cache) > self.cap:
            lru = self.tail.prev
            self._remove(lru)
            del self.cache[lru.key]
```

#### QN 72: LFU Cache
- **Answer**: LFU Cache implementation using two HashMaps: one for keys to values, and another tracking frequency lists.
```python
import collections

class LFUCache:
    def __init__(self, capacity: int):
        self.cap = capacity
        self.key_to_val_freq = {}  # key: (val, freq)
        self.freq_to_keys = collections.defaultdict(collections.OrderedDict)
        self.min_freq = 0

    def get(self, key: int) -> int:
        if key not in self.key_to_val_freq:
            return -1
        val, freq = self.key_to_val_freq[key]
        del self.freq_to_keys[freq][key]
        if not self.freq_to_keys[freq] and self.min_freq == freq:
            self.min_freq += 1
        self.key_to_val_freq[key] = (val, freq + 1)
        self.freq_to_keys[freq + 1][key] = None
        return val

    def put(self, key: int, value: int) -> None:
        if self.cap == 0: return
        if key in self.key_to_val_freq:
            self.key_to_val_freq[key] = (value, self.key_to_val_freq[key][1])
            self.get(key)
            return
        if len(self.key_to_val_freq) >= self.cap:
            k, _ = self.freq_to_keys[self.min_freq].popitem(last=False)
            del self.key_to_val_freq[k]
        self.key_to_val_freq[key] = (value, 1)
        self.freq_to_keys[1][key] = None
        self.min_freq = 1
```

#### QN 73: Parking Lot
- **Answer**: Thread-safe Parking Lot simulation using enums for spot types.
```python
from enum import Enum
import threading

class SpotType(Enum):
    COMPACT = 1
    LARGE = 2

class ParkingSpot:
    def __init__(self, id: int, spot_type: SpotType):
        self.id = id
        self.spot_type = spot_type
        self.is_free = True

class ParkingLot:
    def __init__(self, spots):
        self.spots = spots # List of ParkingSpot
        self.lock = threading.Lock()

    def park(self, spot_type: SpotType) -> bool:
        with self.lock:
            for spot in self.spots:
                if spot.is_free and spot.spot_type == spot_type:
                    spot.is_free = False
                    return True
            return False

    def unpark(self, spot_id: int):
        with self.lock:
            for spot in self.spots:
                if spot.id == spot_id:
                    spot.is_free = True
                    break
```

#### QN 74: Elevator System
- **Answer**: Elevator utilizing LOOK algorithm via priority queues to serve requests.
```python
import heapq

class Elevator:
    def __init__(self):
        self.current_floor = 0
        self.up_heap = []
        self.down_heap = []
        
    def request(self, floor):
        if floor >= self.current_floor:
            heapq.heappush(self.up_heap, floor)
        else:
            heapq.heappush(self.down_heap, -floor)
            
    def step(self):
        if self.up_heap:
            self.current_floor = heapq.heappop(self.up_heap)
        elif self.down_heap:
            self.current_floor = -heapq.heappop(self.down_heap)
        return self.current_floor
```

#### QN 75: BookMyShow
- **Answer**: Seat booking with timeout-based locking to prevent race conditions during checkout.
```python
from enum import Enum
import time

class SeatStatus(Enum):
    AVAILABLE = 1
    LOCKED = 2
    BOOKED = 3

class Seat:
    def __init__(self, id):
        self.id = id
        self.status = SeatStatus.AVAILABLE
        self.locked_until = 0

    def lock_seat(self, timeout_sec=600):
        if self.status == SeatStatus.AVAILABLE or \
          (self.status == SeatStatus.LOCKED and time.time() > self.locked_until):
            self.status = SeatStatus.LOCKED
            self.locked_until = time.time() + timeout_sec
            return True
        return False
        
    def confirm(self):
        if self.status == SeatStatus.LOCKED and time.time() <= self.locked_until:
            self.status = SeatStatus.BOOKED
            return True
        return False
```

#### QN 76: Splitwise
- **Answer**: Net balance calculation using Min-Cash-Flow with Max-Heaps.
```python
import heapq

def simplify_debts(transactions):
    balances = {}
    for giver, receiver, amount in transactions:
        balances[giver] = balances.get(giver, 0) - amount
        balances[receiver] = balances.get(receiver, 0) + amount
        
    givers = [(-v, k) for k, v in balances.items() if v < 0]
    receivers = [(-v, k) for k, v in balances.items() if v > 0]
    heapq.heapify(givers)
    heapq.heapify(receivers)
    
    result = []
    while givers and receivers:
        g_val, g_id = heapq.heappop(givers)
        r_val, r_id = heapq.heappop(receivers)
        
        settled = min(-g_val, -r_val)
        result.append((g_id, r_id, settled))
        
        if -g_val > settled:
            heapq.heappush(givers, (g_val + settled, g_id))
        if -r_val > settled:
            heapq.heappush(receivers, (r_val + settled, r_id))
            
    return result
```

#### QN 77: In-Memory KV Store with Transactions
- **Answer**: Nested transactions using a stack of dictionary updates.
```python
class KVStore:
    def __init__(self):
        self.store = {}
        self.tx_stack = []

    def begin(self):
        self.tx_stack.append({})

    def commit(self):
        if not self.tx_stack: raise Exception("No active transaction")
        tx_diff = self.tx_stack.pop()
        if self.tx_stack:
            self.tx_stack[-1].update(tx_diff)
        else:
            self.store.update(tx_diff)

    def rollback(self):
        if not self.tx_stack: raise Exception("No active transaction")
        self.tx_stack.pop()

    def set(self, key, value):
        if self.tx_stack:
            self.tx_stack[-1][key] = value
        else:
            self.store[key] = value

    def get(self, key):
        for tx in reversed(self.tx_stack):
            if key in tx: return tx[key]
        return self.store.get(key)
```

#### QN 78: Rate Limiter Token Bucket
- **Answer**: Thread-safe token bucket implementation.
```python
import time
import threading

class TokenBucket:
    def __init__(self, capacity, refill_rate):
        self.capacity = capacity
        self.tokens = capacity
        self.refill_rate = refill_rate
        self.last_refill = time.time()
        self.lock = threading.Lock()

    def allow_request(self):
        with self.lock:
            now = time.time()
            elapsed = now - self.last_refill
            self.tokens = min(self.capacity, self.tokens + elapsed * self.refill_rate)
            self.last_refill = now
            
            if self.tokens >= 1:
                self.tokens -= 1
                return True
            return False
```

#### QN 79: Pub-Sub Message Queue
- **Answer**: Simple topic-based pub-sub broker.
```python
class Broker:
    def __init__(self):
        self.topics = {}
        
    def subscribe(self, topic, callback):
        if topic not in self.topics:
            self.topics[topic] = []
        self.topics[topic].append(callback)
        
    def publish(self, topic, message):
        if topic in self.topics:
            for callback in self.topics[topic]:
                callback(message)

# Usage
# def my_sub(msg): print("Got:", msg)
# b = Broker()
# b.subscribe("news", my_sub)
# b.publish("news", "Hello!")
```

#### QN 80: Vending Machine
- **Answer**: State pattern implementation for state transitions.
```python
class State:
    def insert_coin(self, machine): pass
    def dispense(self, machine): pass

class IdleState(State):
    def insert_coin(self, machine):
        print("Coin inserted.")
        machine.state = HasMoneyState()

class HasMoneyState(State):
    def dispense(self, machine):
        print("Item dispensed.")
        machine.state = IdleState()

class VendingMachine:
    def __init__(self):
        self.state = IdleState()
        
    def insert(self): self.state.insert_coin(self)
    def dispense(self): self.state.dispense(self)
```

#### QN 81: Chess Game
- **Answer**: Object-oriented encapsulation of moves.
```python
class Piece:
    def can_move(self, start, end, board): pass

class Pawn(Piece):
    def can_move(self, start, end, board):
        return start[0] + 1 == end[0] and start[1] == end[1]

class Board:
    def __init__(self):
        self.grid = [[None]*8 for _ in range(8)]
        self.grid[1][0] = Pawn()
```

#### QN 82: Snake and Ladder Game
- **Answer**: Game loop iterating over player turns.
```python
import random
class SnakeLadder:
    def __init__(self, snakes, ladders):
        self.snakes = snakes
        self.ladders = ladders
        self.positions = [0, 0] # 2 players
        
    def play_turn(self, player_id):
        roll = random.randint(1, 6)
        self.positions[player_id] += roll
        curr = self.positions[player_id]
        if curr in self.snakes: self.positions[player_id] = self.snakes[curr]
        elif curr in self.ladders: self.positions[player_id] = self.ladders[curr]
```

#### QN 83: Library Management System
- **Answer**: Simple state tracking for library books.
```python
class Book:
    def __init__(self, id, title):
        self.id = id
        self.title = title
        self.is_checked_out = False

class Library:
    def __init__(self): self.catalog = {}
    
    def checkout(self, book_id):
        if book_id in self.catalog and not self.catalog[book_id].is_checked_out:
            self.catalog[book_id].is_checked_out = True
            return True
        return False
```

#### QN 84: ATM Machine
- **Answer**: Chain of responsibility for dispensing notes.
```python
class Dispenser:
    def __init__(self, note_value, next_dispenser=None):
        self.note_value = note_value
        self.next = next_dispenser
        
    def dispense(self, amount):
        count = amount // self.note_value
        remainder = amount % self.note_value
        if count > 0: print(f"Dispensing {count} notes of {self.note_value}")
        if remainder > 0 and self.next: self.next.dispense(remainder)
```

#### QN 85: File System
- **Answer**: Composite pattern representing directories and files.
```python
class FileSystemNode:
    def __init__(self, name): self.name = name

class File(FileSystemNode):
    def __init__(self, name, size):
        super().__init__(name)
        self.size = size

class Directory(FileSystemNode):
    def __init__(self, name):
        super().__init__(name)
        self.children = []
        
    def add(self, node): self.children.append(node)
```

#### QN 86: Task Scheduler
- **Answer**: Priority queue for time-based scheduling.
```python
import heapq, time
class Scheduler:
    def __init__(self): self.tasks = []
    
    def schedule(self, task, run_at):
        heapq.heappush(self.tasks, (run_at, task))
        
    def run_pending(self):
        now = time.time()
        while self.tasks and self.tasks[0][0] <= now:
            _, task = heapq.heappop(self.tasks)
            task()
```

#### QN 87: Thread Pool
- **Answer**: ThreadPool executing tasks from a shared queue.
```python
from queue import Queue
from threading import Thread

class Worker(Thread):
    def __init__(self, queue):
        super().__init__(daemon=True)
        self.queue = queue
        self.start()
        
    def run(self):
        while True:
            func, args = self.queue.get()
            try: func(*args)
            finally: self.queue.task_done()

class ThreadPool:
    def __init__(self, num_threads):
        self.queue = Queue()
        for _ in range(num_threads): Worker(self.queue)
        
    def submit(self, func, *args): self.queue.put((func, args))
```

#### QN 88: Observer Pattern
- **Answer**: Simple event publisher mapping.
```python
class Subject:
    def __init__(self): self.observers = []
    def attach(self, obs): self.observers.append(obs)
    def notify(self): 
        for obs in self.observers: obs.update()

class Observer:
    def update(self): print("State changed!")
```

#### QN 89: Strategy Pattern
- **Answer**: Encapsulated algorithms swapped at runtime.
```python
class PaymentStrategy:
    def pay(self, amount): pass

class CreditCard(PaymentStrategy):
    def pay(self, amount): print(f"Paid {amount} with CC")

class ShoppingCart:
    def checkout(self, amount, strategy):
        strategy.pay(amount)
```

#### QN 90: Decorator Pattern
- **Answer**: Dynamic behavior addition using wrappers.
```python
class Coffee:
    def cost(self): return 5
    
class MilkDecorator(Coffee):
    def __init__(self, coffee): self.coffee = coffee
    def cost(self): return self.coffee.cost() + 2

# Usage: cost = MilkDecorator(Coffee()).cost()
```

#### QN 91: Factory Pattern
- **Answer**: Centralized object creation logic.
```python
class ShapeFactory:
    @staticmethod
    def get_shape(shape_type):
        if shape_type == "circle": return "Circle()"
        elif shape_type == "square": return "Square()"
        return None
```

#### QN 92: Singleton Pattern
- **Answer**: Thread-safe instance restriction.
```python
import threading
class Singleton:
    _instance = None
    _lock = threading.Lock()
    
    def __new__(cls):
        if cls._instance is None:
            with cls._lock:
                if cls._instance is None:
                    cls._instance = super().__new__(cls)
        return cls._instance
```

#### QN 93: Builder Pattern
- **Answer**: Fluent interface for object construction.
```python
class RequestBuilder:
    def __init__(self): self.req = {}
    def set_url(self, url): 
        self.req['url'] = url; return self
    def set_method(self, method): 
        self.req['method'] = method; return self
    def build(self): return self.req
```

#### QN 94: Command Pattern
- **Answer**: Encapsulate action as an object for undo/redo.
```python
class Command:
    def execute(self): pass
    def undo(self): pass

class EditorCommand(Command):
    def __init__(self, editor, text):
        self.editor = editor; self.text = text
    def execute(self): self.editor.append(self.text)
    def undo(self): self.editor = self.editor[:-len(self.text)]
```

#### QN 95: Trie Implementation
- **Answer**: Prefix tree for autocomplete.
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self): self.root = TrieNode()
    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children: node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True
```

#### QN 96: LRU + TTL Cache
- **Answer**: Lazy evaluation TTL on LRU access.
```python
import time
class TTLCache:
    def __init__(self, ttl):
        self.ttl = ttl
        self.cache = {}
        
    def put(self, key, val):
        self.cache[key] = (val, time.time() + self.ttl)
        
    def get(self, key):
        if key in self.cache:
            val, exp = self.cache[key]
            if time.time() < exp: return val
            else: del self.cache[key]
        return None
```

#### QN 97: Consistent Hash Ring
- **Answer**: Binary search implementation of a ring.
```python
import bisect, hashlib
class HashRing:
    def __init__(self): self.ring = []; self.nodes = {}
    
    def add_node(self, node):
        h = int(hashlib.md5(node.encode()).hexdigest(), 16)
        bisect.insort(self.ring, h)
        self.nodes[h] = node
        
    def get_node(self, key):
        if not self.ring: return None
        h = int(hashlib.md5(key.encode()).hexdigest(), 16)
        idx = bisect.bisect(self.ring, h)
        if idx == len(self.ring): idx = 0
        return self.nodes[self.ring[idx]]
```

#### QN 98: Bloom Filter
- **Answer**: Multiple hashes for probabilistic presence.
```python
class BloomFilter:
    def __init__(self, size):
        self.bit_array = [False] * size
        self.size = size
        
    def add(self, item):
        self.bit_array[hash(item) % self.size] = True
        self.bit_array[hash(item + "salt") % self.size] = True
        
    def check(self, item):
        return (self.bit_array[hash(item) % self.size] and 
                self.bit_array[hash(item + "salt") % self.size])
```

#### QN 99: Min-Heap
- **Answer**: Sift-up logic for a simple array-backed heap.
```python
class MinHeap:
    def __init__(self): self.heap = []
    
    def push(self, val):
        self.heap.append(val)
        self._sift_up(len(self.heap) - 1)
        
    def _sift_up(self, idx):
        parent = (idx - 1) // 2
        if parent >= 0 and self.heap[parent] > self.heap[idx]:
            self.heap[parent], self.heap[idx] = self.heap[idx], self.heap[parent]
            self._sift_up(parent)
```

#### QN 100: Graph Implementation
- **Answer**: Adjacency list and simple BFS traversal.
```python
from collections import defaultdict, deque

class Graph:
    def __init__(self): self.graph = defaultdict(list)
    
    def add_edge(self, u, v): self.graph[u].append(v)
    
    def bfs(self, start):
        visited, queue = set([start]), deque([start])
        res = []
        while queue:
            vertex = queue.popleft()
            res.append(vertex)
            for neighbor in self.graph[vertex]:
                if neighbor not in visited:
                    visited.add(neighbor)
                    queue.append(neighbor)
        return res
```

---
*End of Volume 2.*
