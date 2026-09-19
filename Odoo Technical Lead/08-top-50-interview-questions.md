# Track 1: Odoo Technical Lead
## Chapter 8: Top 50+ Senior & Lead Odoo Interview Questions & Ideal Answers

> 🧭 **Navigation**: [⬅️ Previous: 07: Technical Leadership](07-technical-leadership.md) | [🏠 Odoo Technical Lead Track Index](README.md) | End of Track ⇥

---

Here is a curated collection of deep, scenario-based interview questions asked in Senior Odoo Developer and Technical Lead interviews.

---

### Section 1: Core Engine & ORM Internals (Q1 – Q10)

#### Q1: What is the difference between `workers=0` and `workers > 0` in Odoo?
- **Answer**: `workers=0` runs in multi-threaded mode (one master process with threads). Because of Python's GIL, it cannot utilize multiple CPU cores for computation; it is used primarily in local development. `workers > 0` activates the pre-fork multi-processing model, where a master process spawns HTTP workers and a dedicated Gevent longpolling worker. Each worker process has its own Python interpreter and memory space, allowing full utilization of multi-core servers.

#### Q2: How does the Odoo ORM avoid the N+1 query problem during recordset iteration?
- **Answer**: Odoo uses an automatic **prefetching engine**. When a field is read on the first record of a recordset, the ORM doesn't just query for that single record; it inspects the prefetch context and fetches that field for all records in the recordset using a single `SELECT ... WHERE id IN (...)` query. Subsequent loop iterations read directly from `env.cache`.

#### Q3: What happens behind the scenes during `Environment` instantiation?
- **Answer**: `Environment(cr, uid, context)` binds a PostgreSQL database cursor (`cr`), an authenticated user ID (`uid`), and an execution context dictionary (`context`). It also manages `env.cache` (in-memory cache for record field values) and tracks dirty records pending database commit.

#### Q4: What is the difference between `_inherit` and `_inherits`?
- **Answer**: `_inherit` provides classical inheritance or prototype inheritance. If `_name` is omitted, it modifies the existing model in-place (adding columns to the same SQL table). `_inherits` implements delegation inheritance (composition). It creates a separate SQL table with a foreign key pointing to the parent table and transparently proxies field reads and writes between them (e.g. `res.users` delegating to `res.partner`).

#### Q5: When should a computed field have `store=True` vs `store=False`?
- **Answer**: Use `store=True` when the field needs to be searched, filtered, grouped, or sorted in database queries, or when the computation is expensive and inputs rarely change. Use `store=False` (default) when the field depends on dynamic context (e.g., current date/time, user currency), when values change constantly, or to save storage space.

#### Q6: How does Odoo's computed field dependency graph work?
- **Answer**: At startup, Odoo analyzes all `@api.depends` decorators to construct a global directed dependency graph. When a field is written to via `write()`, the ORM traverses the graph to locate all dependent computed fields, invalidates their entries in `env.cache`, and either schedules an immediate or deferred recomputation before flush.

#### Q7: Why should you avoid calling `cr.commit()` inside business methods?
- **Answer**: Calling `cr.commit()` manually breaks transaction atomicity. If an unhandled exception occurs later in the method, Odoo cannot rollback previously committed statements, leaving the database in an inconsistent state. It also corrupts automated unit tests (`TransactionCase`), which rely on rolling back the transaction after each test.

#### Q8: What does `env.flush()` do, and when is it necessary?
- **Answer**: `env.flush()` forces all pending in-memory ORM changes (new records, modified fields) to be written to PostgreSQL via SQL `INSERT` and `UPDATE` statements. It is necessary before executing raw SQL queries (`cr.execute()`) so that the raw query reads the most up-to-date state.

#### Q9: What is the difference between `search_read()` and `read()`?
- **Answer**: `read()` takes a list of known record IDs and retrieves specified field values. `search_read()` combines `search()` and `read()` into a single optimized operation: it filters records using a domain and directly returns the dictionary list of requested fields, avoiding unnecessary recordset object construction in Python.

#### Q10: How does Odoo implement the Identity Map pattern?
- **Answer**: Within a single transaction and environment, accessing the same record ID multiple times returns references to the identical in-memory cache data. This ensures that modifications made to a record are immediately visible across all references in the same request without requiring database refetches.

---

### Section 2: Performance Tuning & Database (Q11 – Q20)

#### Q11: How did you achieve a 10× performance improvement in Odoo POS?
- **Answer**: We identified that downloading 50,000+ products on every POS session initialization consumed 45+ seconds. We introduced an **IndexedDB client-side caching layer** to store the product catalog in the browser. Subsequent session openings loaded the catalog locally in under 1 second, while an asynchronous delta-sync worker fetched only products modified (`write_date > last_sync`) since the previous session. We also pruned payload fields and enabled Gzip/Brotli compression.

#### Q12: How do you identify the slowest queries in an Odoo production database?
- **Answer**: By enabling the PostgreSQL extension `pg_stat_statements`. We query `pg_stat_statements` ordered by `total_exec_time` and `mean_exec_time`. We then inspect the worst offenders using `EXPLAIN (ANALYZE, BUFFERS)` to diagnose missing indexes, sequential table scans, or inefficient nested loops.

#### Q13: What is the difference between an Index Scan and a Bitmap Index Scan?
- **Answer**: An **Index Scan** traverses the B-Tree index and fetches the corresponding table row from heap pages one-by-one; it is optimal for returning few rows. A **Bitmap Index Scan** builds an in-memory bitmap of matched table pages from the index and then reads the heap pages sequentially; it is optimal when retrieving a moderate percentage of rows or combining multiple indexes (BitmapAnd/BitmapOr).

#### Q14: When should you use a Partial Index in Odoo?
- **Answer**: When queries predominantly target a small subset of rows with specific states. For example, in `sale.order` with millions of rows, 90% are historical and confirmed. A partial index `CREATE INDEX idx_sale_pending ON sale_order (company_id) WHERE state IN ('draft', 'sent')` consumes a fraction of disk space and accelerates draft order lookups without index bloat.

#### Q15: How does `read_group()` prevent N+1 aggregation queries?
- **Answer**: Instead of looping through thousands of child records in Python and summing attributes, `read_group()` compiles the aggregation into a single SQL `SELECT groupby_field, SUM(metric) FROM ... GROUP BY groupby_field` executed natively by PostgreSQL.

#### Q16: What is a table vacuum in PostgreSQL, and why is `autovacuum` tuning vital for Odoo?
- **Answer**: PostgreSQL uses Multi-Version Concurrency Control (MVCC), creating new row versions on `UPDATE` and marking old rows as dead tuples. `VACUUM` reclaims storage occupied by dead tuples. If `autovacuum` settings are too conservative on high-write ERP tables (like `stock_quant` or `mail_message`), dead tuples cause table bloat, slowing down sequential and index scans.

#### Q17: Why use PgBouncer with Odoo?
- **Answer**: Odoo creates dedicated database connections per worker. With many workers and background cron jobs, PostgreSQL experiences connection thrashing and high RAM consumption. PgBouncer acts as a lightweight proxy operating in **Transaction Pooling Mode**, multiplexing hundreds of Odoo connections across a small pool of persistent PostgreSQL connections.

#### Q18: What is the risk of using Python `mapped()` on large recordsets?
- **Answer**: If the mapped relation traverses multiple levels (e.g. `orders.mapped('order_line.product_id.categ_id')`) without proper prefetching, it can trigger massive cascading memory allocations and database queries. In large datasets, using direct SQL or `search_read` with targeted joins is much faster.

#### Q19: How do you optimize an import of 500,000 partner records?
- **Answer**: Batch imports in chunks (e.g., 5,000 records). Disable tracking chatter during import (`context={'tracking_disable': True}`). Temporarily defer foreign key validation or trigger recomputations until the bulk insertion finishes. Use `create([vals_list])` for batch inserts rather than individual row insertions.

#### Q20: What causes database deadlocks in Odoo, and how do you resolve them?
- **Answer**: Deadlocks occur when concurrent transactions attempt to lock the same database rows in different orders. The solution is **deterministic lock ordering**: always sort record IDs (e.g. `records.sorted(lambda r: r.id)`) before acquiring row locks or updating stock quantities, and use `SELECT ... FOR UPDATE SKIP LOCKED` for task queues.

---

### Section 3: Security & Multi-Company (Q21 – Q30)

#### Q21: What is the difference between `ir.model.access` and `ir.rule`?
- **Answer**: `ir.model.access` controls model-level (table-level) permissions (Read, Write, Create, Unlink) based on user groups. `ir.rule` controls row-level security by injecting dynamic SQL `WHERE` domain filters into queries to restrict which individual records a user can view or edit.

#### Q22: How does Odoo combine global rules and group-specific record rules?
- **Answer**: All global rules (`global=True`) are joined together with `AND` operators. All group-specific rules for the user's groups are joined with `OR` operators. The two resulting domains are then joined with `AND`: `(Global1 AND Global2) AND (GroupRuleA OR GroupRuleB)`.

#### Q23: What are the security risks of `sudo()`, and how do you mitigate them?
- **Answer**: `sudo()` bypasses all access rights and record rules by elevating the user to Superuser (`uid=2`). If invoked on user-supplied parameters without verification, it creates severe privilege escalation vulnerabilities. Mitigate by using `with_user(restricted_user)` instead, validating caller authorization before calling `sudo()`, and strictly scoping the fields accessed.

#### Q24: How does Odoo implement multi-company data isolation?
- **Answer**: Through global record rules on transactional models that include `['|', ('company_id', '=', False), ('company_id', 'in', company_ids)]`. `company_ids` is dynamically populated from the active companies selected in the user's web session.

#### Q25: How do inter-company transactions work between two subsidiaries?
- **Answer**: When Company A validates a Sales Order targeting Company B, automated hooks intercept the event, switch the environment context to Company B (`with_company(company_b)`), and create a corresponding Purchase Order with linked line items, ensuring both legal entities maintain balanced ledgers.

#### Q26: How does currency conversion work in Odoo?
- **Answer**: Via `res.currency._convert(amount, to_currency, company, date)`. The method looks up historical conversion rates from `res.currency.rate` for the given company where `name <= date` and applies the rate multiplier, properly handling currency decimal precisions.

#### Q27: What is Polish prefix notation in Odoo domains?
- **Answer**: An expression syntax where operators precede their operands (e.g. `['|', ('a', '=', 1), ('b', '=', 2)]`). This allows Odoo's domain parser to recursively build abstract syntax trees that translate directly into PostgreSQL `WHERE` conditions without ambiguity.

#### Q28: Can a non-stored computed field be used in a search domain or record rule?
- **Answer**: Only if the model defines a `search` method on the field definition (e.g. `search='_search_my_field'`). The search method must return a valid SQL domain that PostgreSQL can evaluate on stored database columns.

#### Q29: How do you prevent field-level unauthorized reads or writes?
- **Answer**: Use the `groups` attribute on the field definition (e.g., `margin = fields.Float(groups="sales_team.group_sale_manager")`). Odoo's ORM strips this field from queries and payloads for users outside that security group.

#### Q30: How do you safely test security rules in automated unit tests?
- **Answer**: Create restricted test users belonging only to specific test groups, and instantiate the model using `.with_user(test_user)`. Verify that unauthorized operations raise `odoo.exceptions.AccessError` using `with self.assertRaises(AccessError):`.

---

### Section 4: Frontend OWL & QWeb (Q31 – Q38)

#### Q31: What is the reactivity model in OWL?
- **Answer**: OWL uses ES6 Proxies via `useState()` or `reactive()`. When state properties are mutated, the proxy intercepts the mutation, flags the component as dirty, and schedules a batched virtual DOM render and reconciliation cycle on the next animation frame.

#### Q32: What is the purpose of `onWillStart` in an OWL component?
- **Answer**: `onWillStart` is an asynchronous lifecycle hook executed before the component's initial render. It is the designated place to perform asynchronous operations such as fetching data from the backend via `useService("orm")`.

#### Q33: How do you extend an existing Odoo core OWL component?
- **Answer**: By importing `patch` from `@web/core/utils/patch` and monkey-patching the component prototype (e.g. `patch(FormController.prototype, { ... })`). Custom methods should call `super.methodName(...arguments)` to maintain default framework behaviors.

#### Q34: How do OWL services work?
- **Answer**: Services are shared, singleton abstractions registered in the `services` registry (e.g., `orm`, `action`, `notification`, `router`). Components consume them using the `useService("service_name")` hook in their `setup()` method.

#### Q35: What is the difference between client-side and server-side QWeb?
- **Answer**: Server-side QWeb executes in Python using `lxml` to render HTML templates on the backend (used for Website pages, portal, and PDF reports). Client-side QWeb executes in the browser using JavaScript to compile OWL component templates into virtual DOM rendering functions.

#### Q36: How do you solve header/footer overlapping in `wkhtmltopdf` reports?
- **Answer**: Set `thead { display: table-header-group; }` and `tr { page-break-inside: avoid; }` in report CSS. Ensure the paperformat's `margin_top` and `header_spacing` in Odoo configuration accurately match the pixel height of the custom QWeb header template.

#### Q37: How do you debug a broken QWeb PDF report without downloading PDFs repeatedly?
- **Answer**: Change the URL path from `/report/pdf/<report_name>/<doc_ids>` to `/report/html/<report_name>/<doc_ids>`. This renders the report as raw HTML directly in Chrome/Firefox, allowing live inspection of DOM elements, margins, and CSS styles via DevTools.

#### Q38: How do you pass data from a parent OWL component to a child component?
- **Answer**: Via `props`. The parent passes data in the QWeb template (`<ChildComponent item="state.selectedItem" />`), and the child declares expected prop types in `static props = { item: { type: Object } }`. Props in OWL are unidirectional and read-only.

---

### Section 5: APIs, Integrations & MCP (Q39 – Q44)

#### Q39: What is the Model Context Protocol (MCP) and how does it apply to Odoo?
- **Answer**: MCP is an open standard enabling AI assistants to securely interface with enterprise tools. In Odoo, an MCP server exposes curated ERP operations (like checking inventory, looking up sales orders, or logging CRM activities) as typed tools. It enforces model-level permission controls and token authentication so LLMs can interact with business data safely.

#### Q40: Why must payment gateway integrations rely on Webhooks rather than browser return redirects?
- **Answer**: Browser redirects depend on the customer's browser staying open and maintaining internet connectivity after charging the card. Webhooks provide direct, reliable server-to-server notifications from the payment gateway to Odoo, ensuring transactions reach `done` state even if the customer closes their browser tab.

#### Q41: How do you implement idempotency in external API endpoints?
- **Answer**: Require clients to transmit a unique `Idempotency-Key` header with mutation requests. Before executing logic, check a cache/database table for that key. If found, return the cached previous response; if not found, execute the transaction atomically, store the key and result, and return the response.

#### Q42: How do you prevent prompt injection when building AI tools for Odoo?
- **Answer**: Never let an LLM generate arbitrary Python code (`eval()`) or raw domain strings executed directly by `search()`. Instead, expose strongly-typed tool schemas with discrete parameters (e.g. `min_price: float`, `category: str`), and construct parameterized ORM domains strictly on the backend.

#### Q43: What is the difference between `type='http'` and `type='json'` in `@http.route`?
- **Answer**: `type='http'` handles standard HTTP requests, allowing full control over HTTP headers, status codes, and raw responses. `type='json'` expects a JSON-RPC 2.0 payload (`{"jsonrpc": "2.0", "params": {...}}`), automatically parses the JSON body, and wraps the Python return value in a JSON-RPC response object.

#### Q44: How do you handle network timeouts when calling third-party APIs from Odoo?
- **Answer**: Always specify explicit timeouts in requests (`requests.post(url, timeout=(3.05, 10))`). Offload external API calls to background queues (like Odoo scheduled actions or Celery) rather than blocking synchronous HTTP user workers. Implement exponential backoff for retries.

---

### Section 6: DevOps, Migrations & Leadership (Q45 – Q50+)

#### Q45: What are the key stages of an Odoo version upgrade (e.g. v14 to v18)?
- **Answer**: 
  1. Code audit and deprecation replacement.
  2. Database pre-migration cleanup on staging.
  3. Schema and data transformation scripts (`pre-migration.py` and `post-migration.py`).
  4. Business validation (Trial Balance, Stock Valuation balance check).
  5. Maintenance window cutover with automated rollback checkpoints.

#### Q46: How do you size workers and memory limits in `odoo.conf`?
- **Answer**: Workers = `(2 * CPU cores) + 1`. Memory soft limit = `2048 MB` (2 GB per worker). Memory hard limit = `2560 MB` (2.5 GB). Max real time = `240s`. Total server RAM required $\approx \text{Workers} \times 1.5\text{ GB} + \text{PostgreSQL RAM}$.

#### Q47: How do you handle a production situation where Odoo workers are repeatedly hitting memory limits?
- **Answer**: Inspect server logs to locate which endpoints or cron jobs trigger worker recycling. Profile the responsible code for memory leaks (e.g., loading massive recordsets into memory without batching). If an export or report requires high memory, configure a dedicated long-running cron/task runner worker with higher memory limits rather than inflating memory across all interactive HTTP workers.

#### Q48: How do you lead a team of 8 Odoo developers to maintain high code quality?
- **Answer**: 
  - Establish Architectural Decision Records (ADRs) for major features.
  - Implement automated CI pipelines with `flake8`, `odoo-lint`, and unit test coverage.
  - Enforce peer code reviews using a strict checklist (security, N+1 queries, prefetch integrity).
  - Conduct weekly architectural design reviews and technical mentoring sessions.

#### Q49: What is your rollback strategy if a production deployment fails?
- **Answer**: Automated snapshot rollback: We maintain a pre-deployment PostgreSQL base backup (`pg_dump`) and filestore snapshot. If health check probes or golden business tests fail within the maintenance window, traffic is routed to a maintenance page, the previous Docker container image is redeployed, the database snapshot is restored, and DNS/reverse proxy traffic is switched back.

#### Q50: Why are XML IDs (`ir.model.data`) critical for enterprise modules?
- **Answer**: XML IDs provide stable, unique string identifiers (e.g. `base.main_company`) that decouple code and migrations from auto-incrementing database integer IDs. Hardcoding integer IDs (`id == 1`) creates catastrophic bugs across staging and production instances where IDs differ.

---

> 🧭 **Navigation**: [⬅️ Previous: 07: Technical Leadership](07-technical-leadership.md) | [🏠 Odoo Technical Lead Track Index](README.md) | End of Track ⇥
