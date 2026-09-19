# 500 Master Questions Bank: Volume 5
## 75 Odoo Technical Lead & PostgreSQL Architect Interview Questions

This volume contains **75 deep technical questions and answers** tailored for Senior Odoo Developers, Technical Leads, and Python/PostgreSQL Systems Architects. It covers framework internals, WSGI/Gevent worker mechanics, ORM prefetching and cache graphs, PostgreSQL query planning and concurrency locks, OWL 2 component lifecycles, enterprise MCP/REST APIs, and zero-downtime version migrations.

---

### Section 1: Odoo WSGI, Gevent/Worker Architecture & Memory Limits (Q1 – Q15)

#### Q1: What is the exact execution lifecycle of an incoming HTTP request in Odoo?
- **Answer**:
  1. Reverse Proxy (Nginx) terminates TLS, applies rate limits, and proxies the HTTP request over Unix domain sockets to the Odoo WSGI master process.
  2. The WSGI master routes the socket to an idle worker process (or Gevent thread for longpolling/bus).
  3. `odoo.http.Root.dispatch()` initializes the `WebRequest` (either `JsonRequest` or `HttpRequest`), parses headers, cookies, and session tokens.
  4. Session validation fetches or creates `odoo.http.root.session_store` in `/tmp/odoo_sessions`.
  5. An ORM cursor (`Environment`) is spawned from the PostgreSQL connection pool for the targeted database.
  6. Routing resolution matches URL to the `@http.route()` controller method, checking authentication (`auth='user'`, `'public'`, or `'none'`).
  7. The controller executes ORM operations inside an explicit `try...finally` block.
  8. On success, `cr.commit()` is called; on exception, `cr.rollback()` is executed.
  9. The response is serialized (JSON or HTML), WSGI headers are appended, and the database connection is returned to the pool.

#### Q2: How do worker processes, memory limits (`limit_memory_soft` and `limit_memory_hard`), and CPU limits work together?
- **Answer**:
  - The master process monitors all child workers via POSIX signals (`SIGCHLD`, `SIGTERM`).
  - **`limit_memory_soft`** (default ~2048 MB): After a worker finishes handling an HTTP request, the master checks worker RSS memory. If $\text{RSS} > \text{limit\_memory\_soft}$, the worker is gracefully sent `SIGQUIT` to finish pending requests and cleanly restart, freeing leaked memory.
  - **`limit_memory_hard`** (default ~2560 MB): An absolute ceiling. If a single memory-hungry operation causes RSS to breach this ceiling mid-request, the master immediately terminates the process with `SIGKILL` to protect the host OS from an OOM-killer kernel crash.
  - **`limit_time_cpu` & `limit_time_real`**: Impose CPU time and wall-clock execution limits per request. Breaching them raises a `psutil` or signal-based exception, triggering transaction rollback and worker recycling.

#### Q3: How do you calculate the optimal number of Odoo workers and PostgreSQL max connections for a production server?
- **Answer**:
  - **Odoo Workers Formula**:
    $$\text{Workers} = (\text{CPU Cores} \times 2) + 1$$
    *(Example: On an 8-core server, allocate $8 \times 2 + 1 = 17$ workers: 16 HTTP workers + 1 Gevent longpolling worker).*
  - **RAM Sizing Formula**:
    $$\text{Total RAM Required} = \text{Workers} \times [(\text{RAM}_{\text{soft}} \times 0.8) + (\text{RAM}_{\text{hard}} \times 0.2)]$$
  - **PostgreSQL `max_connections` Formula**:
    $$\text{max\_connections} = (\text{Workers} \times \text{db\_maxconn}) + \text{Cron Workers} + \text{Admin Reserve (10)}$$
    *(Default `db_maxconn` = 64. For 16 workers, without PgBouncer, connections can spike to $16 \times 64 = 1024$, exhausting DB RAM. Recommended: Place PgBouncer in transaction mode between Odoo and Postgres, capping Postgres `max_connections` at 100–150).*

#### Q4: Why does Odoo separate standard HTTP workers from Gevent Longpolling workers?
- **Answer**: Standard HTTP workers operate in a synchronous, multi-process model (pre-forking). If a client initiates a long-lived WebSockets or longpolling HTTP connection (`/longpolling/poll`) that hangs open for 60 seconds waiting for bus notifications, a standard worker would be blocked from serving any other web requests during that time! Gevent uses greenlets (cooperative lightweight coroutines) over an event loop (`libev`), allowing a single Gevent process to maintain 5,000+ concurrent open idle notification connections using minimal RAM.

#### Q5: What is the Python GIL, and how does Odoo bypass it to utilize all multi-core CPUs?
- **Answer**: The Python Global Interpreter Lock (GIL) restricts execution of Python bytecode to a single OS thread per process. Odoo achieves true multi-core parallel processing not via Python multi-threading (`threading.Thread`), but via a **Multi-Process Pre-Forking Architecture** (`--workers > 0`). The master process binds to the listening port and forks independent OS child worker processes. Each worker has its own private Python interpreter, memory space, and GIL, enabling linear scaling across all physical CPU cores.

#### Q6: How does Odoo handle database connection pooling in multi-worker mode?
- **Answer**: In multi-worker mode, each worker process maintains its own independent thread-safe connection pool (`odoo.sql_db.ConnectionPool`).
  - Connections are not shared across OS worker processes.
  - When an HTTP request enters a worker, `self.env.cr` borrows a connection from the local pool.
  - If all local connections (`db_maxconn`) are active, subsequent requests in that worker block waiting for a connection to release.
  - Connections are validated with a lightweight ping (`SELECT 1`) before being handed to the ORM.

#### Q7: What causes "Cursor already closed" and "Connection already closed" exceptions, and how do you prevent them?
- **Answer**:
  - **Cause 1**: An unhandled exception or nested rollback explicitly closes the DB cursor, but downstream code tries to execute queries on `self.env.cr`.
  - **Cause 2**: A long-running compute job exceeds `limit_time_cpu` / `limit_time_real`. The master process kills the worker or drops the socket; when the Python thread attempts to flush changes, the DB connection has been terminated by the server.
  - **Cause 3**: Thread crossing: Spawning a `threading.Thread` and passing `self.env` directly into the background thread. Because `Environment` is thread-local and bound to the originating thread's cursor, the child thread crashes when the parent closes the request cursor.
  - **Fix**: In background threads, always initialize a new cursor and environment:
    ```python
    with odoo.registry(db_name).cursor() as new_cr:
        new_env = api.Environment(new_cr, odoo.SUPERUSER_ID, {})
        # execute operations safely
    ```

#### Q8: How does Odoo's Cron scheduler execute jobs, and how do you ensure cron high availability?
- **Answer**:
  - The master process designates dedicated cron worker threads/processes.
  - Cron workers periodically query `ir_cron` table using `SELECT FOR UPDATE SKIP LOCKED` on runnable rows (`nextcall <= now()`, `active = True`).
  - `SKIP LOCKED` ensures that in a multi-server clustered environment, multiple cron workers never execute the same scheduled action concurrently.
  - **HA Setup**: Configure dedicated background Odoo instances with `--max-cron-threads=4` and `--workers=0` dedicated strictly to cron processing, while user-facing instances run `--max-cron-threads=0` to ensure cron execution never degrades web UI latency.

#### Q9: What happens when an Odoo server receives a `SIGHUP`, `SIGTERM`, or `SIGINT` signal?
- **Answer**:
  - **`SIGHUP`**: Triggers a reload of configuration files and re-opens log file descriptors without dropping active client connections (used for log rotation via `logrotate`).
  - **`SIGTERM` / `SIGINT`**: Graceful shutdown. Master signals all workers to stop accepting new connections, allows in-flight HTTP requests to complete within a timeout window, flushes open file handlers, closes DB pools, and terminates cleanly.
  - **`SIGKILL`**: Immediate hard termination without cleanup (risks orphaned lock files or socket hang).

#### Q10: How do you configure Nginx as an SSL-terminating reverse proxy for Odoo with WebSocket / Longpolling support?
- **Answer**:
  - Define two upstream blocks:
    ```nginx
    upstream odoo_http { server 127.0.0.1:8069; }
    upstream odoo_chat { server 127.0.0.1:8072; }
    ```
  - Direct standard traffic to `odoo_http`:
    ```nginx
    location / {
        proxy_pass http://odoo_http;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Real-IP $remote_addr;
    }
    ```
  - Direct `/websocket` or `/longpolling` to `odoo_chat`:
    ```nginx
    location /websocket {
        proxy_pass http://odoo_chat;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
    ```

#### Q11: How do you detect and fix memory leaks in custom Odoo modules?
- **Answer**:
  - **Detection**: Run Odoo with `tracemalloc` or attach `objgraph` / `py-spy` to a live worker process showing memory growth:
    ```python
    import objgraph
    objgraph.show_most_common_types(limit=20)
    ```
  - **Common Culprits**:
    1. Global dictionary caches that store recordsets or environments (`self.env`) without LRU eviction.
    2. Circular references involving `__del__` methods preventing garbage collection.
    3. Storing large binary file payloads directly in Python memory instead of streaming to `ir.attachment` filestore.

#### Q12: Why should `os._exit()` never be used inside an Odoo controller or method?
- **Answer**: `os._exit()` immediately terminates the process at the C level without calling Python cleanup handlers, without executing `finally` blocks, and without returning database connections to the connection pool. This leaves orphaned open transactions and unreleased row locks on PostgreSQL until the TCP keepalive timeout kills the backend session. Always raise standard exceptions or use `sys.exit()`.

#### Q13: How does the Odoo Session Store work, and how do you share sessions across a horizontal cluster?
- **Answer**:
  - By default, Odoo saves session cookies as serialized files on disk in `~/.local/share/Odoo/sessions`.
  - In a multi-server load-balanced cluster: If Request 1 hits Server A and Request 2 hits Server B, Server B does not have the session file, logging the user out.
  - **Solutions**:
    1. Enable Sticky Sessions (Session Affinity via cookie hash) on the load balancer (HAProxy/AWS ALB).
    2. Mount a shared NFS/EFS drive for the Odoo sessions directory.
    3. Patch `odoo.http.root.session_store` to use a centralized **Redis cluster** for distributed session management.

#### Q14: How does Odoo implement multi-database routing from a single WSGI instance?
- **Answer**: Odoo inspects the HTTP Host header using the `dbfilter` regex configuration setting:
  - If `dbfilter = ^%h$`, a request to `client1.odoo.com` extracts `client1` as the database name.
  - The request sets `httprequest.session.db = 'client1'`.
  - The ORM dynamically connects to the corresponding PostgreSQL database without needing separate Odoo server processes per tenant.

#### Q15: What is the purpose of `odoo-bin --dev=all` and why is it dangerous in production?
- **Answer**: `--dev=all` activates:
  - `reload`: Restarts Python workers automatically on source code modification.
  - `qweb`: Disables QWeb template caching, recompiling XML on every HTTP request.
  - `werkzeug`: Activates the interactive Werkzeug web debugger.
  - `xml`: Reads views directly from XML files instead of the database.
  - **Production Risk**: Catastrophic security vulnerability! The Werkzeug interactive web debugger allows unauthenticated remote code execution (arbitrary Python commands) directly from the browser on any 500 error page!

---

### Section 2: ORM Internals, Prefetching & Cache Invalidation Graph (Q16 – Q30)

#### Q16: How does Odoo's ORM prefetching mechanism eliminate the $N+1$ query problem?
- **Answer**:
  - When an ORM search is executed (`partners = env['res.partner'].search(...)`), Odoo fetches only the requested primary keys (`ids`) into the recordset.
  - The recordset attaches a shared prefetch set (`partners._prefetch_ids = tuple(ids)`).
  - When code accesses a field on the first record (`partners[0].name`), the ORM does not issue `SELECT name FROM res_partner WHERE id = 1`.
  - Instead, it inspects `_prefetch_ids` and executes a single batched SQL query:
    ```sql
    SELECT id, name, email, phone FROM res_partner WHERE id IN (1, 2, 3, ... 100);
    ```
  - It populates the local `env.cache` for all 100 records in a single database round-trip.

#### Q17: What breaks the ORM prefetch set and re-introduces $N+1$ query overhead?
- **Answer**:
  1. **Iterating and converting to singletons without maintaining prefetch**: Splitting records into detached environments (`p.with_user(other_user)`).
  2. **Accessing One2many relational sub-fields in a loop**: Looping over `order.order_line` for 500 orders without prefetching the lines triggers 500 individual `SELECT` statements.
  3. **Manually slicing or filtering using Python list comprehensions** instead of ORM domains.
  4. **Fix**: Use `mapped()` or explicit batch prefetching:
    ```python
    # BAD: 100 queries
    for order in orders:
        for line in order.order_line:
            total += line.price_subtotal

    # GOOD: 1 query using mapped prefetch
    lines = orders.mapped('order_line')
    total = sum(lines.mapped('price_subtotal'))
    ```

#### Q18: Explain the structure and lifecycle of the ORM Cache (`self.env.cache`).
- **Answer**:
  - `env.cache` is an in-memory dictionary local to the current `Environment` cursor, structured as:
    ```python
    cache[field][record_id] = value
    ```
  - When a field is read, the ORM checks `env.cache`. If present, it returns immediately with zero SQL queries.
  - When a field is written (`record.write({'name': 'New'})`), the ORM updates `env.cache`, logs the dirty field, and determines all dependent computed fields to invalidate across the dependency graph.
  - The cache lives strictly for the duration of the database transaction cursor and is discarded upon `cr.commit()` or `cr.rollback()`.

#### Q19: How does the Computed Field Dependency Graph work with `@api.depends`?
- **Answer**:
  - During server startup, the ORM inspects all models and builds a global **Trigger Graph / Dependency Tree**.
  - If Model A has a stored compute field `total` with `@api.depends('line_ids.price_subtotal')`:
    - The ORM registers a trigger: "Any modification to `line_ids.price_subtotal` marks `Model A.total` as dirty".
  - When a line price changes, `env.add_to_compute(field, records)` queues the parent record.
  - Before committing or returning field values, the ORM resolves and recomputes all dirty fields in topological order.
  - **Bug Alert**: If a developer omits a dependency from `@api.depends`, the trigger is never registered; database values become permanently desynchronized!

#### Q20: What is the difference between `@api.depends`, `@api.onchange`, and `@api.constrains`?
- **Answer**:
  - **`@api.depends`**: ORM-level. Executes on the backend for both UI actions and background API/cron scripts. Can be stored in the database (`store=True`).
  - **`@api.onchange`**: Web-client UI level only. Executes in the browser when a user changes a field value in an active form view. Does not execute during RPC API calls, backend scripts, or imports unless explicitly triggered via `onchange()` controller simulation.
  - **`@api.constrains`**: Python validation rule executed after `create()` and `write()`. If the condition fails, raises `ValidationError`, triggering an immediate database rollback.

#### Q21: What is the operational difference between `browse()`, `search()`, and `search_read()`?
- **Answer**:
  - **`browse(ids)`**: Takes IDs and returns an empty recordset without querying the DB; queries are executed lazily only when fields are accessed.
  - **`search(domain)`**: Executes `SELECT id FROM table WHERE ...` and returns a recordset initialized with prefetch IDs.
  - **`search_read(domain, fields)`**: Executes a single SQL query fetching only the requested column list, directly returning a Python list of dictionaries:
    ```sql
    SELECT id, name, email FROM res_partner WHERE active = true LIMIT 50;
    ```
    Bypasses recordset instantiation and QWeb overhead; $5\times$ faster for API endpoints and web client lists.

#### Q22: When should you bypass the ORM and use `self.env.cr.execute()`?
- **Answer**: Use raw SQL only when:
  1. Performing bulk operations on $> 100,000$ records (e.g., mass price updates or large-scale data migrations) where ORM instantiation causes massive memory bloat.
  2. Executing complex analytical aggregations, recursive CTEs, or window functions (`OVER (PARTITION BY ...)`) not supported by standard ORM domains.
  - **Mandatory Safety Rule**: If updating stored fields via raw SQL, you must manually invalidate the ORM cache:
    ```python
    self.env['res.partner'].invalidate_model(['name', 'credit_limit'])
    ```
    Failure to invalidate causes other concurrent threads to serve stale cached data.

#### Q23: How do `flush_all()`, `invalidate_all()`, and `invalidate_recordset()` function?
- **Answer**:
  - **`flush_all()` (or `records.flush_recordset()`)**: Writes all dirty, in-memory modified fields from `env.cache` to the PostgreSQL database buffers via pending `UPDATE` queries. Essential before executing raw SQL reads on recently modified models.
  - **`invalidate_all()`**: Wipes the entire in-memory cache for all models in the environment, forcing subsequent reads to query PostgreSQL.
  - **`invalidate_recordset(fnames)`**: Precision invalidation: purges cache entries strictly for the specified records and field names, preserving unaffected cache entries.

#### Q24: What is the difference between Model Inheritance styles: `_inherit` vs `_inherits`?
- **Answer**:
  - **`_inherit` (Classical Extension)**: Extends an existing model directly in the same database table (adds columns to the existing table), or creates a new child table sharing the parent's schema if `_name` differs.
  - **`_inherits` (Delegation Inheritance / Polymorphic Composition)**: Uses database composition. Embeds a foreign key linking to the parent table.
    - Example: `product.product` delegates to `product.template` via `product_tmpl_id`.
    - Accessing fields on the child transparently reads/writes to the parent record without duplicating database columns.

#### Q25: Why is `@tools.ormcache` used and what are its critical pitfalls?
- **Answer**:
  - `@tools.ormcache` is an LRU in-memory caching decorator that persists across HTTP requests and workers for pure deterministic functions (e.g., resolving system parameters or currency conversion rates).
  - **Structure**: `@tools.ormcache('param1', 'param2')`.
  - **Pitfall**: It does not automatically invalidate when underlying database records change! The developer must explicitly author an invalidation trigger on `write()` and `unlink()` using:
    ```python
    self.env.registry.clear_cache()
    ```
    If forgotten, the server serves stale cached data indefinitely until server restart.

#### Q26: How do `sudo()`, `with_user()`, and `with_context()` affect the ORM Environment?
- **Answer**: They return a new recordset instance bound to a modified copy of the `Environment`:
  - **`sudo(flag=True)`**: Switches the user context to `SUPERUSER_ID` (User ID 1), completely bypassing all `ir.rule` security record rules and `ir.model.access` ACLs.
  - **`with_user(user)`**: Evaluates all subsequent queries and security rules under the specified user's identity.
  - **`with_context(**kwargs)`**: Passes auxiliary metadata flags down the execution chain (e.g., `active_test=False` to query archived records, or `tracking_disable=True` to prevent chatter log overhead).

#### Q27: How does Odoo implement `ir.rule` (Record Rules) at the SQL level?
- **Answer**: When an ORM domain is parsed, the ORM fetches all active `ir.rule` records matching the user's security groups for that model. It converts the record rule domains into raw SQL AST clauses and injects them via `AND` into the `WHERE` clause:
  ```sql
  -- Original: SELECT id FROM sale_order
  -- Injected:
  SELECT id FROM sale_order 
  WHERE (company_id IN (1, 2)) 
    AND (user_id = 42 OR state != 'draft');
  ```
  Because the injection occurs at the SQL AST level, security rules are enforced natively by PostgreSQL.

#### Q28: What is the risk of using `default=fields.Date.today()` vs `default=fields.Date.context_today`?
- **Answer**:
  - `fields.Date.today()` evaluates the system date in UTC. For a user in Tokyo or San Francisco, this can result in records being saved with yesterday's or tomorrow's date!
  - `fields.Date.context_today(self)` evaluates the current date using the user's localized timezone extracted from `self.env.context.get('tz')`, guaranteeing localized calendar accuracy.

#### Q29: How do you implement a Hierarchical Tree Structure using `_parent_store = True`?
- **Answer**:
  - Used for fast recursive parent-child lookups (e.g., product categories, chart of accounts).
  - Requires integer columns: `parent_path = fields.Char(index=True)`.
  - Storing paths like `1/4/19/` allows querying all recursive descendants of Category 4 with a blazing-fast SQL prefix index match:
    ```sql
    WHERE parent_path LIKE '1/4/%'
    ```
    Replaces slow recursive CTE loops with a single indexed B-Tree range query.

#### Q30: What is the difference between `read_group()` and `_read_group()`?
- **Answer**:
  - `read_group()` is the legacy API (pre-v16) returning a list of dictionaries with messy auto-generated keys.
  - `_read_group()` is the modern ORM method introduced in v16+. It returns typed tuples of aggregated values, supports direct grouping by date granularities (`order_date:month`), and generates cleaner, optimized `GROUP BY` SQL statements without unnecessary joins.

---

### Section 3: PostgreSQL Concurrency, Row-Level Locking & Tuning (Q31 – Q45)

#### Q31: How do you prevent race conditions and concurrent update collisions in high-concurrency environments?
- **Answer**: Use explicit PostgreSQL row-level locks via the ORM:
  ```python
  self.env.cr.execute(
      "SELECT id FROM stock_quant WHERE id = %s FOR UPDATE", 
      [quant_id]
  )
  ```
  - **`FOR UPDATE`**: Locks the target row until the transaction commits. Concurrent transactions attempting to modify the same row block and wait.
  - **`FOR UPDATE NOWAIT`**: If the row is locked by another transaction, immediately raises a serialization error instead of blocking, allowing the application to retry.
  - **`FOR UPDATE SKIP LOCKED`**: Skips locked rows entirely. Ideal for queue consumer workers processing independent tasks without contention.

#### Q32: What causes PostgreSQL Deadlocks in Odoo, and how do you prevent them?
- **Answer**:
  - **Cause**: Transaction 1 locks Row A and attempts to lock Row B. Transaction 2 locks Row B and attempts to lock Row A. Neither can proceed; PostgreSQL detects cycle and aborts one transaction.
  - **Prevention Rules**:
    1. **Strict Lock Ordering**: Always lock related records in a consistent, deterministic order (e.g., sort primary keys ascending before acquiring locks: `quant_ids = sorted(quants.ids)`).
    2. Keep database transactions as short as possible; never make external HTTP API calls while holding open DB locks!
    3. Use optimistic locking where appropriate via version counters.

#### Q33: How do you interpret a PostgreSQL `EXPLAIN (ANALYZE, BUFFERS)` output to optimize a slow Odoo query?
- **Answer**:
  - Look for three critical red flags:
    1. **`Seq Scan` on Large Tables**: Indicates missing indexes on filtered columns (`WHERE` clauses).
    2. **High `Buffers: shared read`**: Data is being read from slow physical disk rather than PostgreSQL `shared_buffers` RAM cache.
    3. **Disparity between `rows=X` (Estimated) and `actual rows=Y`**: Indicates stale planner statistics. Requires running `ANALYZE table_name`.
    4. **`Sort Method: external merge Disk`**: Query sort exceeded `work_mem`, spilling temporary sort files to disk. Increase `work_mem` for the session.

#### Q34: What is the difference between B-Tree, GIN, and GiST indexes, and when do you use each in Odoo?
- **Answer**:
  - **B-Tree (Default)**: Best for equality (`=`), range lookups (`<`, `>`, `BETWEEN`), and sorting (`ORDER BY`). Used on foreign keys (`partner_id`, `company_id`) and dates.
  - **GIN (Generalized Inverted Index)**: Best for composite data structures containing multiple elements: JSONB columns, arrays, and full-text search (`tsvector`).
  - **GiST (Generalized Search Tree)**: Best for geometric, spatial data (PostGIS coordinates) and overlapping ranges (`tsrange`, `daterange`).

#### Q35: What is Table and Index Bloat, and why does `VACUUM FULL` cause production outages?
- **Answer**:
  - PostgreSQL uses Multi-Version Concurrency Control (MVCC). When an `UPDATE` or `DELETE` occurs, old row versions (dead tuples) remain on disk until cleaned. If autovacuum cannot keep up with write volume, tables and indexes accumulate dead space (**Bloat**).
  - **`VACUUM FULL` Danger**: Acquires an **`ACCESS EXCLUSIVE` lock** on the entire table, blocking all reads and writes! On a 50GB table, this locks the ERP for hours, causing a total outage.
  - **Production Solution**: Use **`pg_repack`**. It rebuilds tables and indexes online in the background without holding exclusive table locks, allowing uninterrupted production traffic.

#### Q36: How do you configure PgBouncer connection pooling for Odoo?
- **Answer**:
  - Configure PgBouncer in **`pool_mode = transaction`**.
  - This allows 1,000 Odoo worker processes to share 50 physical PostgreSQL connections, because a server connection is borrowed only for the duration of an active transaction (`BEGIN` to `COMMIT`) and returned immediately.
  - **Caveat**: In transaction pooling, session-level features are disabled:
    - Avoid `LISTEN / NOTIFY` (use separate direct connection for bus longpolling).
    - Avoid prepared statements (`prepare_threshold = 0`).
    - Avoid session-level `SET` commands.

#### Q37: What is the purpose of PostgreSQL Partial Indexes in Odoo?
- **Answer**: A partial index indexes only a subset of rows satisfying a condition:
  ```sql
  CREATE INDEX idx_sale_order_unconfirmed 
  ON sale_order (date_order) 
  WHERE state IN ('draft', 'sent');
  ```
  - **Benefit**: Instead of indexing 5 million historical confirmed orders, the index covers only the 2,000 active draft orders.
  - Slashes index size by 99%, keeps the index entirely inside RAM cache, and accelerates query speeds by $20\times$.

#### Q38: How do you handle PostgreSQL `work_mem`, `shared_buffers`, and `maintenance_work_mem` sizing?
- **Answer**:
  - **`shared_buffers`**: Set to $25\%$ of total system RAM (e.g., 16 GB on a 64 GB dedicated DB server).
  - **`effective_cache_size`**: Set to $50\% - 75\%$ of total system RAM (hints to planner how much OS file cache exists).
  - **`work_mem`**: Allocated per sort/hash operation per query:
    $$\text{work\_mem} = \frac{(\text{Total RAM} - \text{shared\_buffers})}{(\text{max\_connections} \times 3)}$$
    *(Typically 16MB–64MB. Sizing too high risks OOM crashes under concurrency).*
  - **`maintenance_work_mem`**: Set to 1–2 GB to accelerate `VACUUM`, `CREATE INDEX`, and schema alterations.

#### Q39: What is the cause of "Transaction Rollback: could not serialize access due to concurrent update"?
- **Answer**: Occurs when running under `REPEATABLE READ` or `SERIALIZABLE` isolation levels, or during concurrent updates to the same row in `READ COMMITTED` mode when foreign key constraint validation conflicts.
  - **Fix**: Wrap high-contention business logic in an automated retry decorator with exponential backoff:
    ```python
    for attempt in range(MAX_RETRIES):
        try:
            with self.env.cr.savepoint():
                # execute transactional write
                break
        except psycopg2.OperationalError as e:
            if e.pgcode == psycopg2.errorcodes.SERIALIZATION_FAILURE and attempt < MAX_RETRIES - 1:
                time.sleep(0.1 * (2 ** attempt))
                continue
            raise
    ```

#### Q40: How do PostgreSQL Savepoints (`cr.savepoint()`) work inside Odoo transactions?
- **Answer**:
  - A savepoint creates an intermediate rollback marker inside an open database transaction:
    ```sql
    SAVEPOINT odoo_sp_1;
    ```
  - If a sub-operation fails, the code can issue `ROLLBACK TO SAVEPOINT odoo_sp_1`, undoing errors in the sub-block without rolling back the entire outer transaction.
  - **Crucial Usage**: Wrapping individual loop iterations (e.g., importing 10,000 rows) in savepoints allows recording errors for individual failed rows while committing valid rows.

#### Q41: Why should you avoid `COUNT(*)` on massive PostgreSQL tables, and what is the Odoo alternative?
- **Answer**:
  - In PostgreSQL MVCC, `COUNT(*)` must scan the entire table visibility map or index to verify row visibility for the current transaction snapshot, taking seconds or minutes on 50-million-row tables.
  - **Odoo Alternative**:
    - For pagination in web views: Odoo caps counts or reads estimated rows from system catalogs:
      ```sql
      SELECT reltuples::bigint FROM pg_class WHERE relname = 'table_name';
      ```
    - Provides instantaneous ($< 1\text{ms}$) estimate without table scanning.

#### Q42: How does Odoo handle database migrations when adding a non-null column to a 10-million row table?
- **Answer**:
  - In PostgreSQL 11+, adding a column with a constant default (`ALTER TABLE t ADD COLUMN c INT DEFAULT 0 NOT NULL;`) is instantaneous because the default is stored in system metadata without rewriting table tuples.
  - However, in Odoo ORM, declaring `field = fields.Integer(required=True, default=0)` triggers both a column addition and a subsequent mass `UPDATE` query, locking the table!
  - **Technical Lead Pattern**:
    1. Add field with `required=False`.
    2. Populate values in chunks via background script (`UPDATE ... WHERE id BETWEEN X AND Y`).
    3. Set `required=True` in code after backfill is complete.

#### Q43: What is the difference between synchronous and asynchronous replication in PostgreSQL?
- **Answer**:
  - **Synchronous Replication**: The primary database waits for at least one standby replica to write the WAL log to disk before returning `COMMIT` success to Odoo. Guarantees zero data loss (RPO = 0), but increases write latency and halts writes if the replica dies.
  - **Asynchronous Replication**: The primary commits locally and streams WAL records in the background. Ultra-fast write latency, but risks minor data loss (seconds) if the primary server suffers sudden hardware destruction before streaming the latest log.

#### Q44: How do you set up Read-Replicas for Odoo reporting queries?
- **Answer**:
  - Standard Odoo ORM connects to a single primary database connection.
  - To route read-heavy analytical dashboards or external BI tools (Tableau/Metabase) away from the transactional OLTP primary:
    1. Configure streaming replication to a read-only standby node.
    2. Point BI tools directly to the replica port.
    3. Inside custom Odoo controller methods, use a dedicated secondary database cursor connecting to the read replica for heavy exports.

#### Q45: How do you trace and eliminate slow queries using `pg_stat_statements`?
- **Answer**:
  - Enable `pg_stat_statements` in `postgresql.conf`.
  - Execute top queries sorted by cumulative execution time:
    ```sql
    SELECT query, calls, total_exec_time, mean_exec_time, rows
    FROM pg_stat_statements
    ORDER BY total_exec_time DESC LIMIT 10;
    ```
  - Identifies the highest-impact optimization targets across the entire enterprise deployment.

---

### Section 4: OWL 2 (Odoo Web Library), Component Lifecycle & Reactive State (Q46 – Q60)

#### Q46: What are the fundamental architectural differences between OWL 1 and OWL 2?
- **Answer**:
  - **OWL 1**: Class-based component model using getters/setters, relying on internal virtual DOM diffing algorithms.
  - **OWL 2**: Complete modern overhaul:
    1. **Hook-Based Architecture**: Introduces React-like hooks (`useState`, `useRef`, `useSubEnv`, `useService`).
    2. **Fine-Grained Reactivity via JavaScript Proxies**: Mutating a property inside `useState` triggers targeted DOM patching directly without heavy full-tree virtual DOM reconciliation.
    3. **Performance**: $3\times$ faster rendering and 50% smaller JavaScript bundle size.

#### Q47: Walk through the complete lifecycle hooks of an OWL 2 Component.
- **Answer**:
  1. **`setup()`**: Component constructor. Register hooks, declare reactive state (`useState`), inject services (`useService`).
  2. **`onWillStart()`**: Asynchronous hook. Fetch initial RPC data before initial template rendering.
  3. **`onWillRender()`**: Executes immediately before every template render cycle.
  4. **`onRendered()`**: Executes immediately after DOM nodes are generated.
  5. **`onMounted()`**: Executes after DOM elements are physically inserted into the browser document. Safe to attach third-party JS libraries (Chart.js) or DOM event listeners.
  6. **`onWillPatch()` & `onPatched()`**: Execute before and after reactive state updates trigger DOM re-rendering.
  7. **`onWillUnmount()`**: Cleanup hook. Disconnect event listeners, clear `setInterval` timers, destroy external widget instances to prevent memory leaks.

#### Q48: How do you monkey-patch an existing core OWL Component without modifying source code?
- **Answer**: Use the OWL `patch()` utility:
  ```javascript
  import { patch } from "@web/core/utils/patch";
  import { Orderline } from "@point_of_sale/app/generic_components/orderline/orderline";

  patch(Orderline.prototype, {
      setup() {
          super.setup();
          // custom initialization
      },
      get customerDiscount() {
          return this.props.line.discount + "% (VIP Applied)";
      }
  });
  ```
  - Modifies component prototypes cleanly at runtime, preserving upgrade compatibility.

#### Q49: How does OWL's Reactive State (`useState`) detect mutations under the hood?
- **Answer**: `useState()` wraps the target JavaScript object in a **JavaScript `Proxy`**. When code mutates a property (`state.counter++`), the Proxy's `set()` trap intercepts the assignment, marks the component as dirty, and schedules an asynchronous rendering job on the microtask queue (`Promise.resolve().then(...)`), batching multiple state modifications into a single DOM repaint.

#### Q50: How do you design an offline-first Point-of-Sale (POS) architecture capable of handling 100,000 products?
- **Answer**:
  - **Storage**: Standard browser `localStorage` caps at 5MB (crashes on 10k products). Use **IndexedDB** (supports gigabytes of client-side structured storage).
  - **Initial Sync**: Chunked background worker downloads products in batches of 5,000, storing records in an IndexedDB object store with indexes on `barcode` and `name`.
  - **Offline Transacting**: When offline, cart operations query IndexedDB via Web Workers without blocking the main UI thread.
  - **Order Queuing & Conflict Resolution**: Offline orders are saved in an `outbox` IndexedDB store with unique UUIDs. When network connectivity restores, a background synchronization worker drains the queue to the backend `/pos/sync_orders` endpoint with idempotent validation.

#### Q51: How does the OWL Service Registry (`registry.category("services")`) operate?
- **Answer**:
  - Services are singleton utility providers (e.g., `notification`, `rpc`, `orm`, `dialog`, `action`).
  - Registered via:
    ```javascript
    registry.category("services").add("myService", {
        dependencies: ["rpc", "notification"],
        start(env, { rpc, notification }) {
            return {
                fetchAlerts: async () => rpc("/get/alerts")
            };
        }
    });
    ```
  - Any OWL component can inject and use the service via:
    ```javascript
    const myService = useService("myService");
    ```

#### Q52: What is the purpose of `useSubEnv` and how does it prevent context pollution?
- **Answer**: `useSubEnv(newContext)` creates an isolated child environment for the component and its descendants without mutating the global parent environment. Used extensively in modals, wizards, and nested views to provide scoped configuration parameters without side effects on the parent view.

#### Q53: How do QWeb template inheritance directives (`t-inherit`, `expr`, `position`) work in OWL?
- **Answer**:
  - OWL compiles XML templates into JavaScript rendering functions.
  - **Inheritance Syntax**:
    ```xml
    <t t-name="my_module.CustomOrderWidget" t-inherit="point_of_sale.OrderWidget" t-inherit-mode="extension">
        <xpath expr="//div[@class='order-info']" position="inside">
            <span class="loyalty-badge"><t t-esc="state.loyaltyPoints"/></span>
        </xpath>
    </t>
    ```
  - `t-inherit-mode="extension"` modifies the existing template in place.
  - `t-inherit-mode="primary"` creates a brand-new distinct template inheriting the parent's base structure.

#### Q54: What causes "Cannot read properties of undefined (reading 'call')" in OWL RPC calls?
- **Answer**:
  - In Odoo 16+, the legacy `this.rpc()` API on components was completely removed in favor of the `orm` and `rpc` services.
  - **Fix**: Inject the service via hook:
    ```javascript
    const orm = useService("orm");
    const result = await orm.searchRead("res.partner", [["customer_rank", ">", 0]], ["name"]);
    ```

#### Q55: How do you prevent memory leaks when mounting third-party charting libraries in OWL?
- **Answer**: Always capture the third-party instance and destroy it inside `onWillUnmount()`:
  ```javascript
  onMounted(() => {
      this.chart = new Chart(this.canvasRef.el, { ... });
  });
  onWillUnmount(() => {
      if (this.chart) {
          this.chart.destroy();
          this.chart = null;
      }
  });
  ```
  Failing to destroy third-party chart/editor instances leaves detached DOM nodes and event listeners in memory, causing browser tab crashes.

#### Q56: What is the difference between `props` and `state` in an OWL Component?
- **Answer**:
  - **`props`**: Immutable data passed from parent component down to child component. Child cannot directly mutate props.
  - **`state`**: Mutable reactive internal data owned and managed by the component itself. Mutations via `useState` trigger automatic local re-rendering.

#### Q57: How do you optimize OWL list view rendering for 5,000 rows?
- **Answer**:
  - Never render 5,000 physical DOM nodes simultaneously! The browser DOM tree will freeze.
  - Use **Virtual Scrolling / Windowing**: Calculate viewport height and row height; render only the visible ~30 rows in the DOM. As the user scrolls, recycling existing DOM elements and dynamically swapping row data via CSS `transform: translateY(...)`.

#### Q58: How does the Odoo Asset Bundle pipeline work (`web.assets_backend`)?
- **Answer**:
  - Assets are declared in `__manifest__.py` under the `'assets'` dictionary.
  - Odoo compiles SCSS/SASS into CSS, concatenates JavaScript modules, runs minification, and computes a sha512 content hash.
  - Bundles are saved as attachments in `ir.attachment` and cached aggressively at reverse proxy / browser CDN levels with 1-year immutable cache headers.

#### Q59: How do you write automated unit tests for OWL Components using QUnit/Hoot?
- **Answer**:
  - Mount component into a mocked test fixture using `mount()` helper.
  - Mock backend RPC services using `makeTestEnv()`.
  - Simulate user clicks (`await click(target)`), and assert DOM mutations:
    ```javascript
    QUnit.test("Counter increments on click", async function (assert) {
        const fixture = await mount(MyComponent);
        await click(fixture, ".btn-increment");
        assert.strictEqual(fixture.querySelector(".count").textContent, "1");
    });
    ```

#### Q60: How does OWL handle Keyboard Shortcuts via `useHotkey`?
- **Answer**:
  - Injects keyboard event listeners bound to component lifecycle:
    ```javascript
    useHotkey("control+enter", () => this.submitOrder());
    ```
  - Automatically unbinds listeners when component unmounts, preventing ghost keypress triggers across screens.

---

### Section 5: Enterprise Integrations, REST/MCP Servers, Security & Migrations (Q61 – Q75)

#### Q61: How do you architect an enterprise RESTful API / Model Context Protocol (MCP) server on top of Odoo?
- **Answer**:
  - Build a cleanly decoupled controller layer exposing OpenAPI / JSON-RPC / MCP specs.
  - Authentication: Enforce Bearer JWT tokens verified against public RSA keys, mapping token claims to specific service users (`res.users`).
  - Architecture: Wrap ORM calls inside standardized serialization and pagination schemas (`offset`, `limit`, `total_count`).
  - Rate Limiting: Redis token-bucket rate limiter at reverse proxy or middleware layer.
  - MCP Server: Expose core Odoo business logic as discoverable MCP tools (`create_sales_order`, `get_inventory_levels`) enabling LLM agents to safely interact with enterprise ERP workflows.

#### Q62: What is the exact step-by-step strategy for migrating an enterprise database across multiple major versions (e.g., v14 to v18)?
- **Answer**:
  1. **Audit & Deprecation Review**: Identify all third-party and custom modules. Map removed/renamed fields and models across target versions.
  2. **OpenUpgrade Pipeline**: Execute sequential migrations: $v14 \to v15 \to v16 \to v17 \to v18$. Never attempt to skip versions!
  3. **Pre-Migration Scripts**: Drop orphaned database constraints, fix corrupted foreign keys, and clean up duplicate XML IDs in `ir.model.data`.
  4. **Migration Scripts Execution**: Run OpenUpgrade schema transformation scripts in PostgreSQL.
  5. **Post-Migration Scripts**: Re-link foreign keys, recompute migrated fields, and update `ir.module.module` state.
  6. **Golden Financial Reconciliation**: Verify Trial Balance, AR/AP aging balances, and Stock Valuation match pre-migration figures to the exact penny.

#### Q63: What are SQL Injection vulnerabilities in Odoo and how do you prevent them?
- **Answer**:
  - **Vulnerability**: Concatenating raw user input directly into SQL strings:
    ```python
    # VULNERABLE: Catastrophic SQL Injection
    self.env.cr.execute(f"SELECT id FROM res_partner WHERE name = '{user_input}'")
    ```
  - **Prevention**: Always pass parameters as a tuple/list to the psycopg2 cursor query parameterized engine:
    ```python
    # SECURE: Parameterized execution
    self.env.cr.execute("SELECT id FROM res_partner WHERE name = %s", [user_input])
    ```
  - Never sanitize via regex; always rely on database-driver parameter binding.

#### Q64: How do Multi-Company Security Rules work and how do you write bulletproof company domains?
- **Answer**:
  - In multi-company environments, users can have access to multiple active companies (`allowed_company_ids`).
  - Standard Record Rule pattern on models with `company_id`:
    ```python
    ['|', ('company_id', '=', False), ('company_id', 'in', company_ids)]
    ```
  - `company_id = False`: Allows shared global records accessible to all subsidiaries.
  - `company_id in company_ids`: Restricts private subsidiary records strictly to users actively logged into that company.

#### Q65: What is the difference between `auth='user'`, `auth='public'`, `auth='none'`, and `auth='api_key'` in Odoo controllers?
- **Answer**:
  - **`auth='none'`**: Operates without a database or session context (`self.env` is not initialized). Used strictly for database selectors or system health checks (`/web/health`).
  - **`auth='public'`**: Falls back to the special `public` user if unauthenticated. Used for e-commerce catalog browsing.
  - **`auth='user'`**: Enforces valid user login session; unauthenticated requests are redirected to `/web/login`.
  - **`auth='api_key'`**: Authenticates requests via HTTP Bearer API keys (`res.users.apikeys`) without creating persistent browser cookies.

#### Q66: How do you prevent Cross-Site Request Forgery (CSRF) in custom HTTP controllers?
- **Answer**:
  - For standard HTML web forms: Set `csrf=True` (default) on `@http.route()`.
  - The controller generates and validates a hidden `csrf_token` embedded in the form matching the user's session token.
  - For external machine-to-machine REST/JSON APIs: Set `csrf=False`, `type='json'`, and enforce stateless API key or Bearer JWT token verification in HTTP headers.

#### Q67: How do you handle file storage performance using Odoo Filestore vs Database BLOBs?
- **Answer**:
  - Storing binary PDF/image attachments directly as base64 strings in PostgreSQL tables rapidly explodes database dump sizes into hundreds of gigabytes, crippling backup/restore times and exhausting database RAM.
  - **Odoo Filestore**: Odoo stores binary files on the host filesystem under `~/.local/share/Odoo/filestore/[db_name]/`, naming files by their SHA1 content hash.
  - The database stores only the lightweight SHA1 hash pointer in `ir.attachment.store_fname`. Provides native deduplication and fast OS-level caching.

#### Q68: How do you implement Zero-Downtime Blue/Green Deployments with Odoo?
- **Answer**:
  - Maintain two identical production environments: **Blue (Active)** and **Green (Idle)**.
  - Both connect to the same PostgreSQL database cluster.
  - When releasing a new version without schema alterations:
    1. Deploy new code and restart Green.
    2. Run smoke tests on Green.
    3. Shift Nginx traffic from Blue to Green instantaneously.
  - **Handling Schema Changes**: Run schema changes backward-compatibly (add columns as nullable; never rename or delete active columns during live traffic).

#### Q69: What is the functional and technical role of `ir.attachment` URL storage (S3 / Cloud Storage)?
- **Answer**:
  - In a Kubernetes / autoscaling multi-pod deployment, local container filesystems are ephemeral.
  - Custom module overrides `ir.attachment._get_path()` or overrides `create()` / `_file_read()` to stream attachment payloads directly to/from **Amazon S3 / Google Cloud Storage** via signed URLs.
  - Keeps Odoo app containers completely stateless.

#### Q70: How do you debug Odoo container deadlocks and performance using `py-spy` and `pg_activity`?
- **Answer**:
  - **`pg_activity`**: Terminal-based PostgreSQL monitoring tool showing real-time active queries, lock contention (`Waiting` state), and blocking query PIDs.
  - **`py-spy`**: Zero-overhead sampling profiler for Python programs:
    ```bash
    py-spy dump --pid <worker_pid>
    py-spy record -o profile.svg --pid <worker_pid> --duration 60
    ```
  - Dumps live C-level and Python-level stack traces without pausing or restarting the production worker, pinpointing infinite loops or stuck database socket reads.

#### Q71: How do you handle Rate Limiting and DoS protection for public-facing Odoo routes?
- **Answer**:
  - Public routes like `/web/login`, `/web/reset_password`, and `/api/v1/*` are prime targets for brute-force attacks.
  - Enforce rate limiting at Nginx using `limit_req_zone`:
    ```nginx
    limit_req_zone $binary_remote_addr zone=login_limit:10m rate=5r/m;
    location = /web/login {
        limit_req zone=login_limit burst=3 nodelay;
        proxy_pass http://odoo_http;
    }
    ```
  - Inside Odoo: Use `ir.config_parameter` to lock user accounts after 5 consecutive failed login attempts.

#### Q72: What is the difference between `ir.model.data` (XML ID / External ID) and database Primary Keys?
- **Answer**:
  - Database primary keys (`id = 42`) are ephemeral sequence integers that change across database environments, migrations, and instances.
  - **XML IDs (`module.external_id`)**: Permanent, globally unique human-readable string identifiers (e.g., `base.group_user`, `sale.action_orders`).
  - Odoo uses `ir.model.data` mapping tables to link XML IDs to current physical database IDs, ensuring safe module upgrades without duplicate record creation.

#### Q73: How do you design an Event-Driven Webhook system in Odoo?
- **Answer**:
  - Avoid firing synchronous external HTTP webhooks inside standard ORM transactions! If the external webhook endpoint is slow or times out, the internal Odoo transaction hangs, holding database row locks.
  - **Best Practice Pattern**:
    1. Write the webhook event payload to a local database queue table (`webhook_event`) in the primary ACID transaction.
    2. A background worker (or external queue worker like Celery / Kafka producer) reads pending events via `SELECT FOR UPDATE SKIP LOCKED`.
    3. Asynchronously transmits HTTP payloads with automated retry policies and exponential backoff.

#### Q74: How do you detect and resolve circular dependency errors in module manifests?
- **Answer**:
  - Occurs when Module A depends on Module B (`'depends': ['module_b']`), and Module B depends on Module A (`'depends': ['module_a']`).
  - Odoo's topological sorting algorithm raises a `CircularDependencyError` during module graph resolution.
  - **Fix**: Decouple the shared logic into a third base bridge module (Module C) that both Module A and Module B depend upon.

#### Q75: How do you lead a high-performing Odoo development team as a Technical Lead?
- **Answer**:
  1. **Strict Code Quality Gates**: Mandate `flake8`, `black`, `eslint`, and `odoo-lint` in pre-commit hooks and CI/CD pipelines.
  2. **Automated Testing Mandate**: Every custom feature must include Python server test cases (`TransactionCase`) and OWL frontend tests, targeting $\ge 80\%$ code coverage.
  3. **Architectural Code Reviews**: Review PRs focusing on database query efficiency, prefetching preservation, index usage, and upgrade-safe extension patterns.
  4. **Continuous Upgradability**: Enforce strict separation between enterprise core modules and custom repositories; prohibit modifying core files directly.
  5. **Disaster Recovery & Post-Mortems**: Lead blameless post-mortems on production incidents, instrument continuous APM monitoring, and maintain actionable runbooks for database failovers and rollback procedures.
