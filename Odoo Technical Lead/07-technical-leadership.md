# Track 1: Odoo Technical Lead
## Chapter 7: Technical Leadership, Architecture Decisions & Team Excellence

### 1. Code Review Checklist for Odoo PRs
A robust 10-point mandatory checklist for Odoo development teams:
1. ✅ No `cr.commit()` or `cr.rollback()` in business logic.
2. ✅ No hardcoded IDs (use `ref()` or `env.ref()`).
3. ✅ Security: all new models have `ir.model.access` CSV.
4. ✅ Performance: no `search()` inside loops (N+1 check).
5. ✅ Migrations: pre/post migrate scripts exist for column/table changes.
6. ✅ Tests: new business logic has `TransactionCase` coverage.
7. ✅ Translations: user-visible strings wrapped in `_()`.
8. ✅ API stability: no breaking changes to public methods.
9. ✅ Demo data: new module has demo records for testing.
10. ✅ Manifest: version bumped, dependencies properly listed.

### 2. Architecture Decision Records (ADR)
- **When to use ADR:** Any decision with significant tradeoffs, future impact, or external dependencies.

```markdown
# ADR-001: Use Redis for Session Caching vs PostgreSQL
Date: 2025-09-01
Status: Accepted
Context: 500 concurrent users, session lookup adds 50ms latency with PostgreSQL
Decision: Use Redis with TTL-based session expiry
Consequences: +10x session lookup speed, -complexity (new Redis dependency)
```

### 3. Technical Estimation Techniques
- **Planning Poker:** Fibonacci (1, 2, 3, 5, 8, 13, 21). Useful for complexity.
- **T-shirt sizing:** XS (< 2h), S (2-4h), M (4-8h), L (1-2d), XL (3-5d).
- **Real example: Stripe integration estimate**
  - Payment provider model: M (5h)
  - Webhook receiver: M (6h) 
  - Frontend payment form: L (8h)
  - Testing & QA: M (4h)
  - Total: 23h → Sprint 3
- **Buffer rule:** Add 30% buffer for integration unknowns and code review iterations.

### 4. Post-Mortem & Incident Response
- **Blameless post-mortem culture:** Focus on the process and system, not the person.
- **5-Whys root cause analysis:**

```text
Incident: POS offline sync failure (Black Friday, 14:30)
Impact: 3 retail stores unable to process cash payments for 47 minutes

Root Cause (5 Whys):
  Why: POS couldn't sync with Odoo server
  Why: Odoo server returned 503 errors
  Why: All 8 workers busy with background MRP compute
  Why: MRP recompute triggered by a scheduled action set to run at 14:00
  Why: Scheduled action timing was not coordinated with peak business hours

Action items:
  - Move heavy scheduled actions to off-peak hours (02:00 AM)
  - Add dedicated POS health check endpoint with worker bypass
  - Add POS monitoring alert for > 30s sync delay
```

> 📖 **Blog:** https://sre.google/sre-book/postmortem-culture/
> 💡 **Interview Tip:** Bring a real incident story — interviewers love post-mortems and learning how you handle pressure and failure.
