# PROJECT-MASTER-MAP-CONSISTENCY-001 一致性校验报告

| 字段 | 值 |
|------|-----|
| 报告编号 | PROJECT-MASTER-MAP-CONSISTENCY-001 |
| 校验日期 | 2026-09-09 |
| 校验范围 | Project Master Map 全量文档 |
| 校验结果 | **10 项不一致** |

---

## 校验概览

本次校验对 Project Master Map 的统计数字、Upstream/Downstream 关系、Identity 追踪、Broken Chain 完整性、Foundation 对象定义及跨域所有权等维度进行一致性审查。

---

## 校验结果汇总

| # | 严重程度 | 类别 | 问题摘要 | 状态 |
|---|----------|------|----------|------|
| 1 | 中 | 统计数字不一致 | Pages: 200+ vs 170+ | 待处理 |
| 2 | 中 | 统计数字不一致 | Foundation Objects: 15 vs 19 | 待处理 |
| 3 | 中 | Upstream/Downstream 未闭合 | Warehouse → Inventory 未在 downstream 中明确 | 待处理 |
| 4 | 中 | Identity 追踪不一致 | productId Wrong Identity 在 Data Lineage 中未提及 | 待处理 |
| 5 | 高 | Identity 追踪不一致 | warehouseId Identity Loss 在 Data Lineage 中未追踪 | 待处理 |
| 6 | 低 | Broken Chain 重复 | BC-001/004/005 内容重叠 | 待处理 |
| 7 | 低 | Broken Chain 重复 | BC-003/030 内容重叠 | 待处理 |
| 8 | 中 | Missing Foundation 误判 | Warehouse/Unit/PaymentMethod 可能是 Embedded/Config | 待处理 |
| 9 | 中 | Cross-Domain Owner 不明确 | Order 的 Owner 在多端写入时需明确主数据所有权 | 待处理 |
| 10 | 中 | 统计口径 | Foundation 对象数量在不同文件中定义不一致 | 待处理 |

---

## 详细问题清单

### 问题 1：统计数字不一致 - Pages 数量

| 字段 | 内容 |
|------|------|
| 严重程度 | 中 |
| 类别 | 统计数字不一致 |
| 描述 | Pages 数量在不同位置分别标注为 200+ 和 170+，存在约 30 页的差异 |
| 影响 | 文档可信度降低，读者无法确认实际页面规模 |
| 建议 | 统一统计口径，明确是否包含附属文档/子页面 |

### 问题 2：统计数字不一致 - Foundation Objects 数量

| 字段 | 内容 |
|------|------|
| 严重程度 | 中 |
| 类别 | 统计数字不一致 |
| 描述 | Foundation Objects 数量分别标注为 15 和 19，差异为 4 个对象 |
| 影响 | 无法确定实际 Foundation 对象数量，影响架构完整性评估 |
| 建议 | 重新盘点并统一 Foundation 对象定义 |

### 问题 3：Upstream/Downstream 未闭合

| 字段 | 内容 |
|------|------|
| 严重程度 | 中 |
| 类别 | Upstream/Downstream 未闭合 |
| 描述 | Warehouse → Inventory 的下游关系未在 downstream 中明确标注 |
| 影响 | 数据流向追踪不完整，可能遗漏关键依赖 |
| 建议 | 补充 Warehouse 的 downstream 标注，确保 Inventory 出现在其下游列表中 |

### 问题 4：Identity 追踪不一致 - productId

| 字段 | 内容 |
|------|------|
| 严重程度 | 中 |
| 类别 | Identity 追踪不一致 |
| 描述 | productId 的 Wrong Identity 问题在 Data Lineage 中未被提及 |
| 影响 | 数据质量风险未在数据血缘中体现，可能遗漏治理措施 |
| 建议 | 在 Data Lineage 中补充 productId 的 Wrong Identity 标注及处理策略 |

### 问题 5：Identity 追踪不一致 - warehouseId

| 字段 | 内容 |
|------|------|
| 严重程度 | **高** |
| 类别 | Identity 追踪不一致 |
| 描述 | warehouseId 的 Identity Loss 问题在 Data Lineage 中未被追踪 |
| 影响 | 仓库维度数据可能在流转过程中丢失，影响库存准确性 |
| 建议 | **优先处理**：在 Data Lineage 中补充 warehouseId Identity Loss 追踪，并建立监控机制 |

### 问题 6：Broken Chain 重复 - BC-001/004/005

| 字段 | 内容 |
|------|------|
| 严重程度 | 低 |
| 类别 | Broken Chain 重复 |
| 描述 | BC-001、BC-004、BC-005 三个 Broken Chain 记录存在内容重叠 |
| 影响 | 问题追踪冗余，可能导致修复工作重复 |
| 建议 | 合并相关记录，保留最完整的描述，删除重复项 |

### 问题 7：Broken Chain 重复 - BC-003/030

| 字段 | 内容 |
|------|------|
| 严重程度 | 低 |
| 类别 | Broken Chain 重复 |
| 描述 | BC-003 和 BC-030 两个 Broken Chain 记录存在内容重叠 |
| 影响 | 问题追踪冗余 |
| 建议 | 合并相关记录，统一编号 |

### 问题 8：Missing Foundation 误判

| 字段 | 内容 |
|------|------|
| 严重程度 | 中 |
| 类别 | Missing Foundation 误判 |
| 描述 | Warehouse、Unit、PaymentMethod 可能被误判为 Missing Foundation，实际应为 Embedded/Config 类型 |
| 影响 | Foundation 对象分类不准确，影响架构分层理解 |
| 建议 | 重新评估这三个对象的定位，明确其为 Embedded 还是 Config 类型，并更新分类 |

### 问题 9：Cross-Domain Owner 不明确

| 字段 | 内容 |
|------|------|
| 严重程度 | 中 |
| 类别 | Cross-Domain Owner 不明确 |
| 描述 | Order 在多端写入场景下，主数据所有权归属不明确 |
| 影响 | 数据冲突时无法确定权威来源，可能导致数据不一致 |
| 建议 | 明确 Order 的主数据所有权规则，定义多端写入时的优先级和冲突解决策略 |

### 问题 10：统计口径不一致

| 字段 | 内容 |
|------|------|
| 严重程度 | 中 |
| 类别 | 统计口径 |
| 描述 | Foundation 对象数量在不同文件中定义不一致（与问题 2 关联） |
| 影响 | 文档间数据不一致，降低整体可信度 |
| 建议 | 建立统一的统计口径定义，确保所有引用同一指标的文件保持一致 |

---

## 严重程度分布

| 严重程度 | 数量 | 占比 |
|----------|------|------|
| 高 | 1 | 10% |
| 中 | 7 | 70% |
| 低 | 2 | 20% |
| **合计** | **10** | **100%** |

---

## 建议处理优先级

| 优先级 | 问题编号 | 原因 |
|--------|----------|------|
| P0 - 立即处理 | #5 | 高严重度，Identity Loss 影响数据准确性 |
| P1 - 尽快处理 | #1, #2, #10 | 统计数字不一致影响文档可信度 |
| P1 - 尽快处理 | #3 | Upstream/Downstream 未闭合影响数据流追踪 |
| P1 - 尽快处理 | #9 | Cross-Domain Owner 不明确影响数据治理 |
| P2 - 计划处理 | #4 | Identity 追踪缺失，需补充但非紧急 |
| P2 - 计划处理 | #8 | Foundation 分类误判，需重新评估 |
| P3 - 有空处理 | #6, #7 | Broken Chain 重复，低优先级清理 |

---

## 结论

本次校验发现 **10 项不一致**，其中高严重度 1 项、中严重度 7 项、低严重度 2 项。

**核心发现：**
- 统计数字在不同位置存在明显差异（Pages 差 30+，Foundation Objects 差 4 个），需统一口径
- Identity 追踪存在遗漏，特别是 warehouseId 的 Identity Loss 问题需优先处理
- Broken Chain 记录存在重复，需合并清理

**建议行动：**
1. **立即** 修复 warehouseId Identity Loss 追踪缺失（问题 #5）
2. **本周内** 统一所有统计数字，确保文档一致性
3. **两周内** 完善 Upstream/Downstream 关系和 Cross-Domain Owner 定义
4. **一个月内** 清理 Broken Chain 重复记录，完成 Foundation 分类重新评估

---

*报告生成时间：2026-09-09*
