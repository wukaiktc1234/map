# Business Item Model Reconciliation

## 项目名称

**Canonical Identity, Type, Role, Capability, Relationship & Context Reconciliation**

## 分析目标

在正式确认 DEC-006 (Product / Food / Material) 之前，解决：

> "一个东西是什么"与"这个东西在某个业务上下文中做什么"之间的边界。

最终回答：

> "一个东西的唯一身份、它是什么、它能做什么、它在什么场景下做什么、它与其他东西是什么关系"——这五件事应该如何在系统里分别表达。

## 核心原则

1. **不得为了迁就现有数据库表结构定义最终业务模型**
2. **不得因为发现"多重角色"，就自动决定建立 Item 表**
3. **不得把 Type、Role、Capability、Relationship 混成同一个枚举**
4. **不得把当前实现直接等同于目标业务模型**
5. **长期业务正确性 > 当前最小迁移成本**
6. **Recommendation ≠ Decision. Current Reality ≠ Target Model.**

## 分析阶段

| Phase | 内容 | 文件 |
|-------|------|------|
| 1 | 概念分类 | concept-taxonomy.md |
| 2 | Identity 解析规则 | identity-resolution-rules.md |
| 3-5 | Type / Role / Capability 深度分析 | identity-type-role-capability.md |
| 6 | Ingredient / Recipe Component 分析 | ingredient-recipe-relationship-analysis.md |
| 7-9 | Stockable / Purchasable / Sellable 分析 | stockable-capability-analysis.md, purchasable-capability-analysis.md, sellable-capability-analysis.md |
| 10-12 | Product / Food / Material 语义分析 | product-semantic-analysis.md, food-semantic-analysis.md, material-semantic-analysis.md |
| 13-14 | 多角色测试矩阵 + 上下文测试 | counterexample-validation.md, store-context-analysis.md |
| 15-16 | Unit/UOM + Recipe/BOM 模型 | unit-context-analysis.md, recipe-bom-model.md |
| 17-19 | Inventory / Procurement / 关系模型 | inventory-object-model.md, procurement-object-model.md, relationship-model.md |
| 20 | 当前→目标概念映射 | current-to-target-semantic-mapping.md |
| 21 | 模型压力点分析 | model-stress-points.md |
| 22 | 推荐模型 + DEC-006 升级 | candidate-semantic-models.md, dec-006-refined-decision-pack.md |

## 文件清单

| # | 文件 | 用途 |
|---|------|------|
| 1 | `concept-taxonomy.md` | 16个概念的分类定义 |
| 2 | `identity-resolution-rules.md` | Canonical Identity 粒度规则 |
| 3 | `identity-type-role-capability.md` | Type / Role / Capability 严格区分 |
| 4 | `relationship-model.md` | 跨域关系模型 |
| 5 | `product-semantic-analysis.md` | Product 语义深度分析 |
| 6 | `food-semantic-analysis.md` | Food 语义深度分析 |
| 7 | `material-semantic-analysis.md` | Material 语义深度分析 |
| 8 | `stockable-capability-analysis.md` | Stockable 能力分析 |
| 9 | `purchasable-capability-analysis.md` | Purchasable 能力分析 |
| 10 | `sellable-capability-analysis.md` | Sellable 能力分析 |
| 11 | `ingredient-recipe-relationship-analysis.md` | Ingredient/Recipe 关系分析 |
| 12 | `unit-context-analysis.md` | Unit/UOM 分层分析 |
| 13 | `store-context-analysis.md` | 门店上下文分析 |
| 14 | `counterexample-validation.md` | 多角色测试矩阵 |
| 15 | `candidate-semantic-models.md` | 候选语义模型比较 |
| 16 | `current-to-target-semantic-mapping.md` | 当前→目标概念映射 |
| 17 | `model-stress-points.md` | 模型压力点分析 |
| 18 | `dec-006-refined-decision-pack.md` | DEC-006 升级决策包 |
| 19 | `business-item-model-reconciliation-registry.yaml` | 概念注册表 |
| 20 | `business-item-model-reconciliation-summary.md` | 调和总结 |

## 核心结论摘要

### 概念分层模型

```
L1: Canonical Identity    "这是什么东西？"
L2: Type / Classification "它属于什么类别？"
L3: Capability            "它能做什么？"
L4: Role                  "它在某场景中做什么？"
L5: Profile               "它在某场景中的配置是什么？"
L6: Relationship          "它与其他东西是什么关系？"
```

### Identity Resolution Rule

Canonical Identity = **Brand** + **ProductFamily** + **Specification**

### Type / Role / Capability 区分

| 维度 | Type | Role | Capability |
|------|------|------|------------|
| 回答 | "它是什么类别" | "它在场景中做什么" | "它能做什么" |
| 唯一性 | 一个对象一个 Type | 一个对象多个 Role | 一个对象多个 Capability |
| 生命周期 | 创建时确定 | 可动态添加/移除 | 创建时声明 |
| 数据位置 | 主数据 | 配置表 | 能力声明表 |

### 候选模型结论

**推荐 Model C: Hybrid Domain Model**（加权得分 4.65/5.0）

### DEC-006 升级

从 "Product / Food / Material 谁废弃谁保留" 升级为：
"Canonical Business Item / Product / Food / Material / Stock / Recipe 的目标业务语义边界"

## 下一步行动

1. **Product Owner 审阅** 本调和结果
2. **Architecture Owner 审阅** 推荐模型
3. **Decision Workshop** 确认 DEC-006-REFINED
4. **进入工程准备** 基于确认的语义模型

## 状态

**RECOMMENDATION, NOT CONFIRMED**

等待 Product Owner / Architecture Owner Review

---

**日期**：2026-09-09
**作者**：AI 架构总控
**任务**：BUSINESS-ITEM-MODEL-002
