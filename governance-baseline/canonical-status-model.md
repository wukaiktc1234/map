# Canonical Status Model — 统一状态定义

> **版本**: 1.0  
> **生成日期**: 2026-09-09  
> **状态**: CANONICAL BASELINE  
> **维护者**: 架构总控 / 项目治理负责人  
> **关键原则**: 统一状态定义 | 禁止状态混淆 | 明确状态转换规则

---

## 一、Canonical Status 定义

### 1.1 状态列表

| 状态 | 英文 | 定义 | 可执行性 | 说明 |
|------|------|------|----------|------|
| **FACT** | Verified Fact | 已验证的事实 | ✅ 可引用 | 代码/DB/API 证据支持的事实 |
| **LOCKED** | Locked Decision | 已锁定的决策 | ✅ 可执行 | 不可更改，除非架构委员会评审 |
| **OPEN** | Open Decision | 需要决策 | ❌ 需决策 | 等待 Product/Architecture Owner 确认 |
| **RECOMMENDED** | Recommended | 推荐方案 | ⚠️ 待确认 | 有推荐方案，需最终确认 |
| **CONFIRMED** | Confirmed | 已确认 | ✅ 可执行 | 已获最终确认，可进入工程化 |
| **ENGINEERING_READY** | Engineering Ready | 工程就绪 | ✅ 立即可执行 | 所有依赖已解决，可直接开发 |
| **BLOCKED** | Blocked | 被阻断 | ❌ 需等待 | 被其他未完成项阻断 |
| **INVALIDATED** | Invalidated | 已废止 | ⛔ 不执行 | 永久废止，不再复用 |

### 1.2 状态优先级

```
LOCKED > CONFIRMED > RECOMMENDED > OPEN
任何状态 > INVALIDATED
任何状态 > BLOCKED (临时阻断)
```

---

## 二、状态区分规则

### 2.1 VERIFIED ≠ LOCKED

| 状态 | 定义 | 适用对象 | 变更条件 |
|------|------|----------|----------|
| **VERIFIED** | 有证据支持 | 事实/决策 | 新证据可覆盖 |
| **LOCKED** | 不可更改 | 已锁定决策 | 架构委员会评审 |

**示例**:
- `LOCK-A001`: foods 为菜品唯一真相源 → **LOCKED** (不可更改)
- `FACT-001`: foods 表有 1000 条记录 → **VERIFIED** (可更新)

### 2.2 RECOMMENDED ≠ CONFIRMED

| 状态 | 定义 | 适用对象 | 可执行性 |
|------|------|----------|----------|
| **RECOMMENDED** | 推荐方案 | 推荐的解决方案 | ⚠️ 待确认 |
| **CONFIRMED** | 已确认 | 已获最终确认 | ✅ 可执行 |

**示例**:
- `DEC-004`: Customer/Member 关系 → **RECOMMENDED** (Option C)
- `DEC-001`: Warehouse 管理模式 → **CONFIRMED** (A_enum)

### 2.3 ENGINEERING_READY ≠ IMPLEMENTED

| 状态 | 定义 | 适用对象 | 可执行性 |
|------|------|----------|----------|
| **ENGINEERING_READY** | 工程就绪 | 待开发的任务 | ✅ 立即可执行 |
| **IMPLEMENTED** | 已实现 | 已完成的任务 | ✅ 已完成 |

**示例**:
- `ER-001`: foods 菜品真相源执行 → **ENGINEERING_READY** (待开发)
- `LOCK-A001`: foods 为菜品唯一真相源 → **IMPLEMENTED** (已实现)

---

## 三、状态转换规则

### 3.1 正向转换 (推进决策)

```
FACT → LOCKED: 事实被锁定为决策
OPEN → RECOMMENDED: 开放决策有推荐方案
RECOMMENDED → CONFIRMED: 推荐方案被确认
CONFIRMED → ENGINEERING_READY: 确认后可工程化
ENGINEERING_READY → IMPLEMENTED: 工程实施完成
```

### 3.2 逆向转换 (回退决策)

```
任何状态 → BLOCKED: 被其他决策阻断
任何状态 → INVALIDATED: 被废止
```

### 3.3 状态转换图

```mermaid
stateDiagram-v2
    [*] --> FACT: 代码/DB/API 验证
    FACT --> LOCKED: 架构委员会评审
    FACT --> OPEN: 需要决策
    OPEN --> RECOMMENDED: 推荐方案
    RECOMMENDED --> CONFIRMED: 最终确认
    CONFIRMED --> ENGINEERING_READY: 所有依赖解决
    ENGINEERING_READY --> IMPLEMENTED: 工程实施
    
    FACT --> BLOCKED: 被阻断
    OPEN --> BLOCKED: 被阻断
    RECOMMENDED --> BLOCKED: 被阻断
    CONFIRMED --> BLOCKED: 被阻断
    ENGINEERING_READY --> BLOCKED: 被阻断
    
    FACT --> INVALIDATED: 废止
    OPEN --> INVALIDATED: 废止
    RECOMMENDED --> INVALIDATED: 废止
    CONFIRMED --> INVALIDATED: 废止
    ENGINEERING_READY --> INVALIDATED: 废止
    
    BLOCKED --> FACT: 阻断解除
    BLOCKED --> OPEN: 阻断解除
    BLOCKED --> RECOMMENDED: 阻断解除
    BLOCKED --> CONFIRMED: 阻断解除
    BLOCKED --> ENGINEERING_READY: 阻断解除
```

---

## 四、状态使用规范

### 4.1 LOCK 状态

| LOCK 状态 | 定义 | 适用对象 |
|-----------|------|----------|
| VERIFIED | 有证据支持 | 基线 A/B 的 LOCK |
| PARTIAL | 部分验证 | LOCK-A003 |
| UNVERIFIED | 无证据支持 | 基线 C/D 的 LOCK |
| CONFLICT | 与代码冲突 | 基线 C/D 的 LOCK |

### 4.2 DEC 状态

| DEC 状态 | 定义 | 适用对象 |
|----------|------|----------|
| CONFIRMED | 已确认 | V1 DEC-001~009, 011, 013, 014 |
| OPEN | 需决策 | DEC-004, DEC-006, DEC-010, DEC-012 |
| RECOMMENDED | 推荐方案 | DEC-004 (Option C), DEC-006 (Option C) |
| SUPERSEDED | 被取代 | DEC-006 (旧), DEC-V2-008 |
| INVALIDATED | 已废止 | DEC-008, DEC-V2-001~007 |

### 4.3 SR 状态

| SR 状态 | 定义 | 适用对象 |
|---------|------|----------|
| ENGINEERING_READY | 可立即执行 | 12 个 SR |
| WAITING_PRODUCT_DECISION | 等待 PO | 3 个 SR |
| WAITING_ARCHITECTURE_DECISION | 等待架构师 | 4 个 SR |
| BLOCKED | 被阻断 | 8 个 SR |
| INVALIDATED | 已废止 | 3 个 SR |

---

## 五、状态计算规则

### 5.1 统计规则

| 统计项 | 规则 | 示例 |
|--------|------|------|
| VERIFIED | 有代码/DB/API 证据 | LOCK-A001~009 |
| PARTIAL | 部分证据 | LOCK-A003 |
| OPEN | 需决策 | DEC-004, DEC-006 |
| CONFIRMED | 已确认 | DEC-001~009, 011, 013, 014 |
| ENGINEERING_READY | 可立即执行 | ER-001~012 |
| BLOCKED | 被阻断 | 8 个 SR |
| INVALIDATED | 已废止 | 基线 C/D, DEC-008, IMPLICIT-001~006 |

### 5.2 禁止的表述

| 禁止表述 | 正确表述 | 说明 |
|----------|----------|------|
| 10/10 VERIFIED | 9/10 VERIFIED, 1/10 PARTIAL | LOCK-A003 是 PARTIAL |
| VERIFIED ≈ LOCKED | VERIFIED ≠ LOCKED | VERIFIED 可更新，LOCKED 不可更改 |
| RECOMMENDED ≈ CONFIRMED | RECOMMENDED ≠ CONFIRMED | RECOMMENDED 待确认，CONFIRMED 已确认 |
| ENGINEERING_READY ≈ IMPLEMENTED | ENGINEERING_READY ≠ IMPLEMENTED | ENGINEERING_READY 待开发，IMPLEMENTED 已完成 |

---

## 六、状态应用示例

### 6.1 LOCK-A001: foods 为菜品唯一真相源

```
初始状态: OPEN (需决策)
↓ 代码验证
当前状态: VERIFIED (有证据支持)
↓ 架构委员会评审
最终状态: LOCKED (不可更改)
```

### 6.2 DEC-004: Customer/Member 关系

```
初始状态: OPEN (需决策)
↓ 推荐方案
当前状态: RECOMMENDED (Option C)
↓ Product Owner 确认
最终状态: CONFIRMED (已确认)
↓ 所有依赖解决
工程状态: ENGINEERING_READY (可立即执行)
```

### 6.3 ER-001: foods 菜品真相源执行

```
初始状态: BLOCKED (等待 DEC-004)
↓ DEC-004 确认
当前状态: ENGINEERING_READY (可立即执行)
↓ 工程实施
最终状态: IMPLEMENTED (已完成)
```

---

## 七、状态治理规则

### 7.1 状态变更权限

| 状态变更 | 权限 | 说明 |
|----------|------|------|
| OPEN → RECOMMENDED | 任何人 | 推荐方案 |
| RECOMMENDED → CONFIRMED | Product/Architecture Owner | 最终确认 |
| CONFIRMED → ENGINEERING_READY | 架构总控 | 依赖检查 |
| ENGINEERING_READY → IMPLEMENTED | 开发团队 | 工程实施 |
| 任何状态 → BLOCKED | 任何人 | 发现阻断 |
| 任何状态 → INVALIDATED | 架构总控 | 废止决策 |

### 7.2 状态变更记录

每次状态变更必须记录：
- 变更时间
- 变更人
- 变更原因
- 变更前状态
- 变更后状态

---

## 八、统计摘要

| 状态 | 数量 | 占比 | 说明 |
|------|------|------|------|
| VERIFIED | 9 | 6% | 基线 A 的 LOCK |
| PARTIAL | 1 | 1% | LOCK-A003 |
| LOCKED | 14 | 9% | 基线 A + B 的 LOCK |
| CONFIRMED | 9 | 6% | V1 DEC |
| OPEN | 3 | 2% | DEC-004, DEC-006, DEC-010, DEC-012 |
| RECOMMENDED | 2 | 1% | DEC-004, DEC-006 |
| ENGINEERING_READY | 12 | 8% | ER-001~012 |
| BLOCKED | 8 | 5% | 8 个 SR |
| INVALIDATED | 31 | 21% | 基线 C/D, DEC-008, IMPLICIT |
| **总计** | **151** | **100%** | |

---

## 九、下一步

1. **停止地图制作**: 本文档为最终治理基线，不再生成新的地图文件
2. **等待确认**: Product Owner / Architecture Owner 正式确认 Decision
3. **进入治理流程**: 
   ```
   FACT BASELINE → PRODUCT DECISIONS / ARCHITECTURE DECISIONS
                          ↓
                   REQUIREMENT BASELINE
                          ↓
                   SYSTEMIC REMEDIATION
                          ↓
                   ENGINEERING CARDS
   ```

---

**文档状态**: ✅ CANONICAL BASELINE  
**下一步**: 等待 Product Owner / Architecture Owner 正式确认 Decision
