# Decision Baseline Integrity & Conflict Reconciliation Report

> Generated: 2026-09-09
> Version: v2
> Status: ACTIVE

---

## Summary

| Metric | Count |
|--------|-------|
| Total Conflicts | 5 |
| DECISION_CONFLICT | 1 |
| INCONSISTENT | 1 |
| INSUFFICIENT | 1 |
| OVERGRANULAR | 1 |
| REDUNDANT | 6 (IMPLICIT-001~006) |

---

## Conflict Details

### CONFLICT-001: Amount/Money Storage Unit

| Field | Value |
|-------|-------|
| **ID** | CONFLICT-001 |
| **Severity** | CRITICAL |
| **Involved Rules** | LOCK-003 vs DEC-008 |
| **Category** | DECISION_CONFLICT |

**LOCK-003 Definition:**
- Amount/Money stored as `fen` (整数存储)
- Unit: fen (1/100 yuan)

**DEC-008 Definition:**
- Amount/Money stored as `Decimal(10,2)`
- Unit: yuan (元为单位)

**Analysis:**
These two rules directly contradict each other on the fundamental representation of monetary values in the system. LOCK-003 mandates integer-based fen storage, while DEC-008 mandates Decimal-based yuan storage. These are mutually exclusive — the database schema cannot simultaneously support both representations without conversion overhead and ambiguity.

**Impact:**
- Database schema ambiguity
- API contract inconsistency
- Precision loss risk at conversion boundaries
- Cross-service communication errors

**Resolution:** DEC-008 MUST be rejected or revised to align with LOCK-003. If the project adopts fen-based storage (as per LOCK-003), all decimal yuan representations must be converted to integer fen at the data layer.

---

### CONFLICT-002: Recon Inheritance Model

| Field | Value |
|-------|-------|
| **ID** | CONFLICT-002 |
| **Severity** | HIGH |
| **Involved Rules** | DEC-006 (recon) |
| **Category** | INCONSISTENT |

**Recommended Model (DEC-006):**
- Material → Food → Product inheritance hierarchy

**Actual Data Model:**
- Composition (组合关系), not inheritance (继承关系)

**Analysis:**
DEC-006 recommends a class inheritance model where Food extends Material, and Product extends Food. However, the actual data model uses composition — products are composed of materials and food items rather than inheriting from them. This mismatch leads to:
- Incorrect ORM mapping assumptions
- Overly rigid type hierarchies
- Inability to model products that contain both food and non-food materials

**Impact:**
- Schema design errors
- Incorrect polymorphic queries
- Violation of composition-over-inheritance principle

**Resolution:** DEC-006 MUST be revised to adopt the composition model. The inheritance chain should be replaced with explicit composition relationships and aggregation rules.

---

### CONFLICT-003: Data Ownership Granularity

| Field | Value |
|-------|-------|
| **ID** | CONFLICT-003 |
| **Severity** | MEDIUM |
| **Involved Rules** | DEC-009 |
| **Category** | INSUFFICIENT |

**Current Definition (DEC-009):**
- Only defines Truth Owner

**Missing Definitions:**
- Write Authority (写权限主体)
- Write Actor (写操作执行者)
- Event Consumer (事件消费方)

**Analysis:**
DEC-009 defines data ownership solely through the Truth Owner concept, which is insufficient for a production system. A robust data governance model requires:
- **Truth Owner**: The authoritative source of truth for a data entity
- **Write Authority**: The entity authorized to modify the data
- **Write Actor**: The actual component/service that performs the write operation
- **Event Consumer**: The downstream systems that consume change events

Without this distinction, audit trails become ambiguous and access control cannot be properly enforced.

**Impact:**
- Ambiguous audit trails
- Insufficient access control granularity
- Difficulty in compliance reporting

**Resolution:** DEC-009 MUST be supplemented with a role definition matrix covering Truth Owner, Write Authority, Write Actor, and Event Consumer for each data entity.

---

### CONFLICT-004: Order State Machine Dimensionality

| Field | Value |
|-------|-------|
| **ID** | CONFLICT-004 |
| **Severity** | HIGH |
| **Involved Rules** | DEC-007 (recon) |
| **Category** | OVERGRANULAR |

**Recommended Model (DEC-007):**
- Single complete state machine for order lifecycle

**Actual Data Model:**
- Business State and Payment State are orthogonal dimensions

**Analysis:**
DEC-007 proposes a monolithic order state machine. However, the actual system operates with two independent state dimensions:
- **Business State**: order processing stages (created, confirmed, shipped, delivered, etc.)
- **Payment State**: payment lifecycle (pending, paid, refunded, failed, etc.)

These dimensions evolve independently — an order can be in "shipped" business state while in "refunded" payment state. A single state machine cannot model this orthogonal behavior without combinatorial explosion.

**Impact:**
- State explosion if combined into single machine
- Loss of independent dimension management
- Incorrect state transition validation

**Resolution:** DEC-007 MUST be revised to adopt an ORTHOGONAL dimension model. Business State and Payment State should be managed as independent state machines with explicit cross-dimension validation rules.

---

### CONFLICT-005: IMPLICIT Rules Redundancy

| Field | Value |
|-------|-------|
| **ID** | CONFLICT-005 |
| **Severity** | LOW |
| **Involved Rules** | IMPLICIT-001 through IMPLICIT-006 |
| **Category** | REDUNDANT |

**Implicit Rules:**
- IMPLICIT-001
- IMPLICIT-002
- IMPLICIT-003
- IMPLICIT-004
- IMPLICIT-005
- IMPLICIT-006

**Analysis:**
All six IMPLICIT rules have been found to overlap significantly with existing DEC and LOCK rules. They either duplicate explicit decisions already codified or derive trivially from established baselines without adding independent governance value.

**Impact:**
- Maintenance overhead from redundant rule tracking
- Confusion during conflict resolution (which rule takes precedence?)
- Inflated rule count without proportional governance benefit

**Resolution:** All IMPLICIT-001 through IMPLICIT-006 rules MUST be marked as SUBSUMED by their corresponding DEC/LOCK counterparts. They should be archived and removed from active governance.

---

## Resolution Matrix

| Conflict ID | Target Rule | Action | Priority | Owner |
|-------------|-------------|--------|----------|-------|
| CONFLICT-001 | DEC-008 | REJECT / REVISE | P0 | TBD |
| CONFLICT-002 | DEC-006 | REVISE | P1 | TBD |
| CONFLICT-003 | DEC-009 | SUPPLEMENT | P2 | TBD |
| CONFLICT-004 | DEC-007 | REVISE | P1 | TBD |
| CONFLICT-005 | IMPLICIT-001~006 | SUBSUME | P3 | TBD |

---

## Next Steps

1. **Immediate (P0):** Resolve CONFLICT-001 — confirm fen-based storage across all money-related decisions
2. **Sprint 1 (P1):** Revise DEC-006 (composition model) and DEC-007 (orthogonal state dimensions)
3. **Sprint 2 (P2):** Supplement DEC-009 with full role definition matrix
4. **Backlog (P3):** Archive IMPLICIT-001~006 as SUBSUMED

---

*This report is a living document. Update as conflicts are resolved.*
