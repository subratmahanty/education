# Materialized View (MV) + FAST Refresh + Oracle CONTEXT Index

This document summarizes our complete discussion on **Materialized Views, FAST refresh behavior, partitioning, PCT (Partition Change Tracking), CONTEXT index behavior, and operational best practices**. It is written as a **reference guide** you can share with your team.

---

## 1. Materialized View Basics

- A **Materialized View (MV)** stores the *physical result* of a query.
- Unlike a normal view, data is **precomputed and stored**.
- Used for:
  - Reporting
  - Heavy aggregation
  - Search optimization

---

## 2. FAST Refresh – How It Really Works

### Key Principles

- FAST refresh is **delta-based**, not query-based
- Oracle **never re-runs the full SELECT**
- Changes are applied using **Materialized View Logs (MV logs)**

### Refresh Types

| Refresh Type | Behavior |
|-------------|----------|
| FAST | Applies only INSERT / UPDATE / DELETE deltas |
| COMPLETE | Truncate + rebuild entire MV |
| FORCE | Tries FAST, falls back to COMPLETE |

### Triggering FAST Refresh

- `REFRESH FAST ON DEMAND` (most common)
- Scheduled via `DBMS_SCHEDULER`
- `REFRESH FAST ON COMMIT` (automatic but risky for OLTP)

FAST refresh **does not trigger automatically on DML** unless `ON COMMIT` is used.

---

## 3. FAST Refresh with WHERE Conditions

### Example
```sql
WHERE status IN ('OPEN','ACTIVE','INPROGRESS')
```

### Behavior on Status Change

| Old Status | New Status | MV Action |
|-----------|-----------|-----------|
| OPEN | ACTIVE | UPDATE |
| ACTIVE | INPROGRESS | UPDATE |
| OPEN | CLOSE | DELETE |
| CLOSE | OPEN | INSERT |

Oracle evaluates:

- Qualified before?
- Qualified after?

And decides INSERT / UPDATE / DELETE accordingly.

---

## 4. FAST Refresh with JOIN (Parent–Child)

### Scenario

- Child rows included only if parent status is OPEN / ACTIVE / INPROGRESS

```sql
FROM child c
JOIN parent p ON p.parent_id = c.parent_id
WHERE p.status IN ('OPEN','ACTIVE','INPROGRESS')
```

### What Happens if Parent Status → CLOSE?

✅ FAST refresh **deletes all dependent child rows from MV**

### Why MV Logs Are Required on BOTH Tables

- FAST refresh replays **row-level deltas only**
- Oracle does **not re-run joins**
- Parent log: detects eligibility change
- Child log: identifies which MV rows to change

Without child MV log, Oracle cannot identify affected MV rows.

---

## 5. CONTEXT Index on MV

### Key Facts

- CONTEXT index is **not auto-synced** after FAST refresh
- FAST refresh updates MV data only

### Sync Options

#### Scheduled Sync (Recommended)
```sql
EXEC CTX_DDL.SYNC_INDEX('MV_TXT_IDX');
```

- Indexes **delta rows only**
- Non-blocking

#### SYNC (ON COMMIT)
```sql
PARAMETERS ('SYNC (ON COMMIT)')
```

- Delta-only indexing
- Runs at commit time
- Can slow refresh significantly

---

## 6. Blocking vs Non-Blocking Operations

| Operation | Blocks Queries? |
|---------|----------------|
| SELECT with CONTAINS | ❌ No |
| FAST refresh | ❌ No |
| CTX_DDL.SYNC_INDEX | ❌ No |
| COMPLETE refresh | ⚠️ Yes |
| ALTER INDEX REBUILD | ⚠️ Yes |

**FAST refresh + CONTEXT search is non-blocking.**

---

## 7. Partition Operations & MVs

### DROP PARTITION on Base Table

- Does **NOT** write row-level deletes to MV logs
- Breaks FAST refresh
- MV becomes `UNUSABLE`

### Important Rule

> If an MV is **not partitioned**, dropping a base-table partition **requires COMPLETE refresh**, even if logically no MV rows are affected.

---

## 8. PCT (Partition Change Tracking)

### What Is PCT?

**PCT allows Oracle to track partition-level changes and refresh only affected MV partitions.**

It prevents unnecessary COMPLETE refresh after partition DDL.

### Requirements for PCT

- Base table is partitioned
- MV is partitioned
- Partition key aligns
- MV query is PCT-supported (usually single-table)

### Check PCT Status
```sql
SELECT mview_name, pct_refresh FROM user_mviews;
```

---

## 9. Partitioned Materialized View

### Example (List Partition by Status)
```sql
CREATE MATERIALIZED VIEW mv_orders
PARTITION BY LIST (status) (
  PARTITION mv_open VALUES ('OPEN'),
  PARTITION mv_active VALUES ('ACTIVE'),
  PARTITION mv_inprogress VALUES ('INPROGRESS')
)
REFRESH FAST
AS
SELECT order_id, status, amount
FROM orders
WHERE status IN ('OPEN','ACTIVE','INPROGRESS');
```

### Benefits

- Faster refresh
- Partition-level maintenance
- Enables PCT

---

## 10. DROP PARTITION vs EXCHANGE PARTITION

| Operation | FAST Refresh Safe? |
|---------|-------------------|
| DELETE FROM partition | ✅ |
| EXCHANGE PARTITION | ✅ |
| DROP PARTITION | ❌ (unless PCT) |
| TRUNCATE TABLE | ❌ |

**Best practice:** Use `EXCHANGE PARTITION` instead of `DROP PARTITION`.

---

## 11. Patching & Refresh Requirements

### VM / OS / Hypervisor Patching

- ❌ COMPLETE refresh not required
- MV and CONTEXT index remain valid

### Oracle DB Patching

- Usually safe
- Check `USER_MVIEWS.STALENESS`

COMPLETE refresh required only if:
- MV logs lost
- Objects invalidated
- Structural changes occurred

---

## 12. MV + CONTEXT vs Elasticsearch (At Scale)

| Aspect | MV + CONTEXT | Elasticsearch |
|------|-------------|---------------|
| Consistency | Strong (ACID) | Eventual |
| Scale | 100–300M rows | Billions |
| Regex | Limited | Full |
| Ops complexity | Low | High |
| Search UX | Moderate | Rich |

**Common enterprise pattern:**
- Oracle = system of record
- Elasticsearch = exploratory / fuzzy search

---

## 13. Key One-Line Rules (Cheat Sheet)

- FAST refresh is **delta-based, not query-based**
- JOIN MVs require MV logs on **all base tables**
- CONTEXT index sync is **separate from MV refresh**
- COMPLETE refresh breaks CONTEXT index
- DROP PARTITION breaks FAST refresh unless PCT is enabled
- Non-partitioned MV + DROP PARTITION = COMPLETE refresh
- Use EXCHANGE PARTITION for MV-safe purging

---

## 14. Recommended Architecture Pattern

```
Base Tables (partitioned)
   ↓
Denormalized or PCT-safe MV (FAST refresh)
   ↓
Oracle CONTEXT index
   ↓
Scheduled SYNC_INDEX
```

This gives:
- Predictable refresh
- Non-blocking search
- No COMPLETE refresh in steady state

---

## Final Takeaway

> **Design MVs for how data changes, not just how it is queried.**

If you do that, FAST refresh + CONTEXT indexing scales safely even in 24×7 enterprise systems.

