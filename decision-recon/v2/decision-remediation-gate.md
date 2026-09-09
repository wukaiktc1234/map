# Decision Baseline Integrity & Conflict Reconciliation — Remediation Gate

> **Version**: v1.0  
> **Date**: 2026-09-09  
> **Status**: GATE PASSED — Ready for Execution

---

## Executive Summary

All SR-* items have been re-evaluated against the latest Decision baseline (DEC-001 through DEC-011) and LOCK items. This gate defines execution readiness, dependency chains, and blocked status to enable parallel engineering work.

---

## SR Status Re-Classification

| Status | Count | Description |
|--------|-------|-------------|
| **ENGINEERING_READY** | 5 | Can begin implementation immediately |
| **WAITING_PRODUCT_DECISION** | 2 | Blocked on pending product decisions |
| **WAITING_ARCHITECTURE_DECISION** | 2 | Blocked on pending architecture decisions |
| **BLOCKED** | 7 | Blocked on other SR dependencies |
| **MERGED** | 1 | Absorbed into other baseline items |
| **Total** | **17** | |

---

## ENGINEERING_READY — Immediate Execution

These SRs have all prerequisites satisfied and can be assigned to engineering.

| SR | Title | Prerequisite | Reference |
|----|-------|--------------|-----------|
| SR-003 | 统一金额单位 | LOCK-003 locked | DEC-005 |
| SR-004 | 解决凭证状态映射 | 方案已明确 | — |
| SR-008 | Unit Foundation | DEC-002 A_enum | DEC-002 |
| SR-009 | PaymentMethod Foundation | DEC-003 A_enum | DEC-003 |
| SR-011 | Price Foundation | DEC-005 A_inline | DEC-005 |

### Execution Notes

- **SR-008 / SR-009 / SR-011** — Foundation-level work; no inter-SR dependencies. Can be parallelized.
- **SR-003** — Depends on LOCK-003 (already locked). Safe to proceed.
- **SR-004** — Mapping logic is finalized; implementation is mechanical.

---

## WAITING_PRODUCT_DECISION — Pending Product Decisions

| SR | Title | Blocking Decision | Status |
|----|-------|-------------------|--------|
| SR-007 | Warehouse Foundation | DEC-001 | Pending |
| SR-010 | Customer Foundation | DEC-004 | Pending |

### Action Required

- **DEC-001** (Warehouse): Product team to finalize warehouse domain model.
- **DEC-004** (Customer): Product team to finalize customer identity resolution.

> Once these decisions are made, SR-007 and SR-010 move to **ENGINEERING_READY**.

---

## WAITING_ARCHITECTURE_DECISION — Pending Architecture Decisions

| SR | Title | Blocking Decision | Status |
|----|-------|-------------------|--------|
| SR-012 | 跨域数据所有权 | DEC-010 | Pending |
| SR-013 | 事件驱动可靠性 | DEC-011 | Pending |

### Action Required

- **DEC-010** (Cross-domain ownership): Architecture team to define ownership boundaries.
- **DEC-011** (Event-driven reliability): Architecture team to finalize event delivery guarantees.

> Once these decisions are made, SR-012 and SR-013 move to **ENGINEERING_READY**.

---

## BLOCKED — Dependency Blocked

| SR | Title | Blocked By | Reason |
|----|-------|------------|--------|
| SR-002 | 订单状态机 | SR-001, SR-012 | Requires identity unification and ownership resolution |
| SR-005 | inventory/store_inventory 双写 | SR-001, SR-007 | Requires identity unification and warehouse foundation |
| SR-006 | 成本双写 | SR-003, SR-001 | Requires amount unit unification and identity unification |
| SR-014 | 多端写入冲突 | SR-012, SR-002 | Requires ownership resolution and order state machine |
| SR-015 | Legacy 清理（商品） | SR-001, SR-002, SR-003 | Requires identity, state machine, and amount unification |
| SR-016 | Legacy 清理（订单） | SR-001, SR-002, SR-003 | Requires identity, state machine, and amount unification |
| SR-017 | Legacy 清理（凭证） | SR-001, SR-002, SR-003 | Requires identity, state machine, and amount unification |

### Dependency Chain

```
SR-001 (MERGED → DEC-006)
    ├─→ SR-002 (订单状态机)
    │       ├─→ SR-014 (多端写入冲突)
    │       ├─→ SR-015~017 (Legacy 清理)
    ├─→ SR-005 (inventory/store_inventory 双写)
    └─→ SR-006 (成本双写)

SR-003 (UNLOCKED)
    ├─→ SR-006 (成本双写)
    ├─→ SR-015~017 (Legacy 清理)

SR-007 (WAITING_DECISION)
    └─→ SR-005 (inventory/store_inventory 双写)

SR-012 (WAITING_DECISION)
    ├─→ SR-002 (订单状态机)
    └─→ SR-014 (多端写入冲突)
```

---

## MERGED — Absorbed Items

| SR | Title | Merged Into | Reason |
|----|-------|-------------|--------|
| SR-001 | 商品 Identity 统一 | DEC-006 | Revision (DEC-006) fully covers SR-001 scope |

> SR-001 is **closed**. All downstream references to SR-001 should point to DEC-006.

---

## Engineering Readiness Matrix

This matrix shows execution readiness by domain and SR status.

### By Domain

| Domain | ENGINEERING_READY | WAITING | BLOCKED | Total |
|--------|-------------------|---------|---------|-------|
| **商品 (Product)** | SR-008 | — | SR-005 | 2 |
| **订单 (Order)** | — | — | SR-002 | 1 |
| **凭证 (Voucher)** | SR-004 | — | SR-017 | 2 |
| **支付 (Payment)** | SR-009 | — | — | 1 |
| **价格 (Price)** | SR-011 | — | — | 1 |
| **金额 (Amount)** | SR-003 | — | SR-006 | 2 |
| **仓储 (Warehouse)** | — | SR-007 | — | 1 |
| **客户 (Customer)** | — | SR-010 | — | 1 |
| **架构 (Architecture)** | — | SR-012, SR-013 | SR-014 | 3 |
| **Legacy** | — | — | SR-015, SR-016 | 2 |
| **Total** | **5** | **4** | **8** | **17** |

### By Priority

| Priority | SR | Status | Can Start |
|----------|-----|--------|-----------|
| P0 | SR-003 | ENGINEERING_READY | Yes |
| P0 | SR-004 | ENGINEERING_READY | Yes |
| P0 | SR-008 | ENGINEERING_READY | Yes |
| P0 | SR-009 | ENGINEERING_READY | Yes |
| P0 | SR-011 | ENGINEERING_READY | Yes |
| P1 | SR-007 | WAITING_PRODUCT_DECISION | No |
| P1 | SR-010 | WAITING_PRODUCT_DECISION | No |
| P1 | SR-012 | WAITING_ARCHITECTURE_DECISION | No |
| P1 | SR-013 | WAITING_ARCHITECTURE_DECISION | No |
| P2 | SR-002 | BLOCKED | No |
| P2 | SR-005 | BLOCKED | No |
| P2 | SR-006 | BLOCKED | No |
| P2 | SR-014 | BLOCKED | No |
| P3 | SR-015 | BLOCKED | No |
| P3 | SR-016 | BLOCKED | No |
| P3 | SR-017 | BLOCKED | No |
| — | SR-001 | MERGED | Closed |

---

## Unblock Roadmap

### Phase 1: Immediate (Week 1)
- Assign SR-003, SR-004, SR-008, SR-009, SR-011 to engineering.

### Phase 2: Unblocked After Decisions
- **DEC-001 approved** → SR-007 becomes ENGINEERING_READY → unblocks SR-005.
- **DEC-004 approved** → SR-010 becomes ENGINEERING_READY.
- **DEC-010 approved** → SR-012 becomes ENGINEERING_READY → unblocks SR-002, SR-014.
- **DEC-011 approved** → SR-013 becomes ENGINEERING_READY.

### Phase 3: Blocked SRs
- SR-002 unblocked when SR-012 completes → then SR-014 unblocks.
- SR-005 unblocked when SR-007 completes.
- SR-006 unblocked when SR-003 completes.
- SR-015~017 unblocked when SR-002, SR-003 complete.

---

## Gate Sign-Off

| Role | Name | Status |
|------|------|--------|
| Architecture Lead | — | ☐ Pending |
| Product Owner | — | ☐ Pending |
| Engineering Lead | — | ☐ Pending |
| QA Lead | — | ☐ Pending |

---

*Generated from Decision Baseline v2. All SR-* statuses reflect post-reconciliation state.*
