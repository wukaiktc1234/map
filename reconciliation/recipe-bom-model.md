# Phase 16: Recipe / BOM 数据模型分析

## 1. 业务背景

餐饮ERP系统中，Recipe/BOM（配方/物料清单）是连接菜品与原料的核心模型。它定义了：
- 菜品由哪些原料构成
- 每种原料的用量标准
- 成本核算的基础

---

## 2. Recipe/BOM 层次模型

### 2.1 四层结构

```
┌─────────────────────────────────────────────────────────────┐
│                    Food/Product（菜品/产品）                  │
│                    客户最终购买的成品                         │
├─────────────────────────────────────────────────────────────┤
│                    Recipe（配方）                             │
│                    标准制作流程和原料清单                     │
├─────────────────────────────────────────────────────────────┤
│                    Recipe Component（配方组件）               │
│                    单个原料的用量定义                         │
├─────────────────────────────────────────────────────────────┤
│                    Canonical Item（标准物料）                 │
│                    原料的标准化表示                           │
└─────────────────────────────────────────────────────────────┘
```

### 2.2 层次定义

| 层级 | 名称 | 职责 | 粒度 |
|------|------|------|------|
| L1 | Food/Product | 最终出品，客户可购买 | 单个菜品 |
| L2 | Recipe | 标准制作方案 | 单个菜品的配方 |
| L3 | Recipe Component | 单个原料用量定义 | 单个原料 |
| L4 | Canonical Item | 原料标准化表示 | 单个物料 |

---

## 3. 能力需求分析

### 3.1 必需能力（当前系统必须支持）

| 能力 | 优先级 | 业务价值 | 实现复杂度 |
|------|--------|----------|------------|
| quantity | P0 | 标准用量 | 低 |
| unit | P0 | 计量单位 | 低 |
| estimated_cost | P0 | 标准成本 | 低 |
| actual_cost | P0 | 实际成本 | 低 |
| is_required | P0 | 是否必需原料 | 低 |
| allow_variance | P1 | 允许差异 | 低 |

### 3.2 未来能力（当前系统可选支持）

| 能力 | 优先级 | 业务价值 | 实现复杂度 |
|------|--------|----------|------------|
| yield | P2 | 产出率 | 中 |
| waste | P2 | 损耗率 | 中 |
| substitution | P2 | 替代材料 | 中 |
| effective_date | P2 | 生效日期 | 中 |
| store_scope | P3 | 门店范围 | 高 |
| version | P3 | 版本管理 | 高 |

---

## 4. 真实业务案例

### 案例1：宫保鸡丁（基础配方）

**业务场景**：标准菜品配方

```yaml
dish: 宫保鸡丁
recipe:
  - ingredient: 鸡胸肉
    quantity: 200
    unit: g
    estimated_cost: 6.00
    actual_cost: 5.80
    is_required: true
    allow_variance: 10  # 允许10%差异
    
  - ingredient: 花生米
    quantity: 50
    unit: g
    estimated_cost: 2.00
    actual_cost: 2.10
    is_required: true
    allow_variance: 5
    
  - ingredient: 干辣椒
    quantity: 15
    unit: g
    estimated_cost: 0.50
    actual_cost: 0.45
    is_required: true
    allow_variance: 20
    
  - ingredient: 花椒
    quantity: 5
    unit: g
    estimated_cost: 0.30
    actual_cost: 0.28
    is_required: true
    allow_variance: 20
    
  - ingredient: 酱油
    quantity: 30
    unit: ml
    estimated_cost: 0.60
    actual_cost: 0.55
    is_required: true
    allow_variance: 10

total_estimated_cost: 9.40
total_actual_cost: 9.18
variance: -2.3%
```

**必需能力覆盖**：quantity, unit, estimated_cost, actual_cost, is_required, allow_variance

---

### 案例2：红烧肉（含产出率）

**业务场景**：需要计算产出率的菜品

```yaml
dish: 红烧肉
recipe:
  - ingredient: 五花肉
    quantity: 500
    unit: g
    estimated_cost: 15.00
    actual_cost: 14.50
    is_required: true
    yield: 0.8  # 产出率80%（烹饪后重量减少）
    
  - ingredient: 生姜
    quantity: 20
    unit: g
    estimated_cost: 0.20
    actual_cost: 0.18
    is_required: true
    
  - ingredient: 大蒜
    quantity: 30
    unit: g
    estimated_cost: 0.30
    actual_cost: 0.28
    is_required: true

# 成本计算考虑产出率
actual_cost_per_serving: (14.50 / 0.8) + 0.18 + 0.28 = 18.58
```

**必需能力覆盖**：quantity, unit, estimated_cost, actual_cost, is_required, yield

---

### 案例3：宫保鸡丁（含替代材料）

**业务场景**：原料短缺时的替代方案

```yaml
dish: 宫保鸡丁
recipe:
  - ingredient: 鸡胸肉
    quantity: 200
    unit: g
    is_required: true
    substitutions:
      - item: 鸡腿肉
        ratio: 1.0  # 1:1替代
        note: "口感略有不同"
        
      - item: 鸡里脊
        ratio: 0.9  # 用90%即可
        note: "更嫩，可减量"
        
  - ingredient: 花生米
    quantity: 50
    unit: g
    is_required: true
    substitutions:
      - item: 腰果
        ratio: 1.0
        note: "成本更高"
```

**必需能力覆盖**：quantity, unit, is_required, substitution

---

### 案例4：季节性菜品（含生效日期）

**业务场景**：菜品配方随季节调整

```yaml
dish: 凉拌黄瓜
recipes:
  - effective_date: 2024-01-01
    expiry_date: 2024-05-31
    recipe:
      - ingredient: 黄瓜
        quantity: 300
        unit: g
        is_required: true
      - ingredient: 醋
        quantity: 20
        unit: ml
        is_required: true
        
  - effective_date: 2024-06-01
    expiry_date: 2024-09-30
    recipe:
      - ingredient: 黄瓜
        quantity: 350  # 夏季黄瓜更水，用量增加
        unit: g
        is_required: true
      - ingredient: 醋
        quantity: 25  # 夏季口味偏酸
        unit: ml
        is_required: true
```

**必需能力覆盖**：quantity, unit, is_required, effective_date

---

### 案例5：多门店配方（含门店范围）

**业务场景**：不同门店使用不同配方

```yaml
dish: 麻婆豆腐
recipes:
  - store_scope: "all"
    recipe:
      - ingredient: 豆腐
        quantity: 300
        unit: g
        is_required: true
      - ingredient: 肉末
        quantity: 50
        unit: g
        is_required: true
        
  - store_scope: "sichuan_region"
    recipe:
      - ingredient: 豆腐
        quantity: 300
        unit: g
        is_required: true
      - ingredient: 肉末
        quantity: 80  # 四川门店肉末更多
        unit: g
        is_required: true
      - ingredient: 花椒
        quantity: 10  # 四川门店花椒更多
        unit: g
        is_required: true
```

**必需能力覆盖**：quantity, unit, is_required, store_scope

---

### 案例6：配方版本管理（含版本）

**业务场景**：配方迭代优化

```yaml
dish: 宫保鸡丁
versions:
  - version: 1.0
    effective_date: 2024-01-01
    status: "archived"
    recipe:
      - ingredient: 鸡胸肉
        quantity: 200
        unit: g
      - ingredient: 花生米
        quantity: 50
        unit: g
        
  - version: 1.1
    effective_date: 2024-03-01
    status: "archived"
    changes: "调整花生米用量"
    recipe:
      - ingredient: 鸡胸肉
        quantity: 200
        unit: g
      - ingredient: 花生米
        quantity: 60  # 增加花生米用量
        unit: g
        
  - version: 2.0
    effective_date: 2024-06-01
    status: "active"
    changes: "增加鸡胸肉用量，减少花生米"
    recipe:
      - ingredient: 鸡胸肉
        quantity: 220  # 增加鸡胸肉
        unit: g
      - ingredient: 花生米
        quantity: 55  # 略减花生米
        unit: g
```

**必需能力覆盖**：quantity, unit, version, effective_date

---

## 5. 当前系统现实

### 5.1 现有表结构

```sql
-- dish_recipe 表
CREATE TABLE dish_recipe (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    dish_id BIGINT NOT NULL,
    ingredient_id BIGINT,
    ingredient_name VARCHAR(100),
    quantity DECIMAL(10,2),
    unit VARCHAR(20),
    estimated_cost DECIMAL(10,2),
    actual_cost DECIMAL(10,2),
    allow_variance TINYINT DEFAULT 0,
    is_required TINYINT DEFAULT 1
);

-- material_consumption 表
CREATE TABLE material_consumption (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    order_id BIGINT,
    dish_id BIGINT,
    material_id BIGINT,
    quantity DECIMAL(10,2),
    unit VARCHAR(20),
    cost DECIMAL(10,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- inventory_deduction 表
CREATE TABLE inventory_deduction (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    order_id BIGINT,
    material_id BIGINT,
    quantity DECIMAL(10,2),
    unit VARCHAR(20),
    deducted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 5.2 能力覆盖分析

| 能力 | 当前支持 | 说明 |
|------|----------|------|
| quantity | ✅ | dish_recipe.quantity |
| unit | ✅ | dish_recipe.unit |
| estimated_cost | ✅ | dish_recipe.estimated_cost |
| actual_cost | ✅ | dish_recipe.actual_cost |
| is_required | ✅ | dish_recipe.is_required |
| allow_variance | ✅ | dish_recipe.allow_variance |
| yield | ❌ | 无产出率字段 |
| waste | ❌ | 无损耗率字段 |
| substitution | ❌ | 无替代材料表 |
| effective_date | ❌ | 无生效日期字段 |
| store_scope | ❌ | 无门店范围字段 |
| version | ❌ | 无版本管理 |

### 5.3 数据一致性问题

**问题1**：ingredient_id 与 ingredient_name 并存

```sql
-- dish_recipe 中同时存在
ingredient_id BIGINT,        -- 引用 material_archives.id
ingredient_name VARCHAR(100) -- 自由文本

-- 可能导致：
-- 同一原料使用不同ID或名称
-- 无法保证数据一致性
```

**问题2**：无菜品版本管理

```sql
-- 当菜品配方修改时，直接更新原记录
-- 无法追溯历史配方
-- 无法比较不同版本的成本差异
```

**问题3**：无门店级别配方

```sql
-- 所有门店使用相同配方
-- 无法支持区域口味差异
-- 无法支持季节性调整
```

---

## 6. 能力优先级分析

### 6.1 必需能力（当前系统必须支持）

| 能力 | 业务价值 | 实现复杂度 | 建议 |
|------|----------|------------|------|
| quantity | 标准用量 | 低 | 保持现有字段 |
| unit | 计量单位 | 低 | 结合 Phase 15 Unit Foundation |
| estimated_cost | 标准成本 | 低 | 保持现有字段 |
| actual_cost | 实际成本 | 低 | 保持现有字段 |
| is_required | 是否必需原料 | 低 | 保持现有字段 |
| allow_variance | 允许差异 | 低 | 保持现有字段 |

**结论**：当前系统已完全支持必需能力

---

### 6.2 未来能力（按业务需求排序）

| 能力 | 优先级 | 业务价值 | 实现复杂度 | 建议 |
|------|--------|----------|------------|------|
| yield | P2 | 成本核算准确性 | 中 | 第二阶段实现 |
| waste | P2 | 损耗控制 | 中 | 第二阶段实现 |
| substitution | P2 | 原料灵活性 | 中 | 第二阶段实现 |
| effective_date | P2 | 配方时效性 | 中 | 第二阶段实现 |
| store_scope | P3 | 区域差异化 | 高 | 第三阶段实现 |
| version | P3 | 配方迭代 | 高 | 第三阶段实现 |

---

## 7. 建议的 Recipe/BOM 数据模型

### 7.1 核心表设计（必需能力）

```sql
-- 菜品配方主表
CREATE TABLE recipe (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    dish_id BIGINT NOT NULL,
    version INT DEFAULT 1,
    status ENUM('draft', 'active', 'archived') DEFAULT 'draft',
    effective_date DATE,
    created_by BIGINT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- 配方组件表
CREATE TABLE recipe_component (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    recipe_id BIGINT NOT NULL,
    item_id BIGINT NOT NULL,              -- 引用 material_archives.id
    quantity DECIMAL(10,2) NOT NULL,
    uom_code VARCHAR(20) NOT NULL,        -- 引用 uom_master.code
    estimated_cost DECIMAL(10,2),
    actual_cost DECIMAL(10,2),
    allow_variance DECIMAL(5,2) DEFAULT 0,
    is_required TINYINT DEFAULT 1,
    sort_order INT DEFAULT 0,
    status TINYINT DEFAULT 1,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 7.2 扩展表设计（未来能力）

```sql
-- 配方组件扩展（产出率、损耗率）
CREATE TABLE recipe_component_ext (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    component_id BIGINT NOT NULL,
    yield_rate DECIMAL(5,4) DEFAULT 1.0000,  -- 产出率
    waste_rate DECIMAL(5,4) DEFAULT 0.0000,  -- 损耗率
    notes TEXT
);

-- 替代材料表
CREATE TABLE recipe_substitution (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    component_id BIGINT NOT NULL,
    substitute_item_id BIGINT NOT NULL,
    ratio DECIMAL(5,4) DEFAULT 1.0000,       -- 替代比例
    priority INT DEFAULT 0,                  -- 优先级
    notes TEXT,
    status TINYINT DEFAULT 1
);

-- 门店配方范围表
CREATE TABLE recipe_store_scope (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    recipe_id BIGINT NOT NULL,
    store_id BIGINT,                         -- NULL 表示全部门店
    region VARCHAR(50),
    status TINYINT DEFAULT 1
);
```

---

## 8. 迁移策略

### 8.1 渐进式迁移

```mermaid
graph LR
    A[阶段1: 重构dish_recipe] --> B[阶段2: 添加yield/waste]
    B --> C[阶段3: 添加substitution]
    C --> D[阶段4: 添加store_scope/version]
```

### 8.2 数据迁移规则

```yaml
migration_rules:
  # 阶段1: 重构 dish_recipe → recipe + recipe_component
  phase1:
    - step: "创建新表结构"
    - step: "迁移现有数据"
    - step: "验证数据一致性"
    - step: "更新业务逻辑"
    
  # 阶段2: 添加 yield/waste
  phase2:
    - step: "添加 recipe_component_ext 表"
    - step: "初始化默认值"
    - step: "更新成本计算逻辑"
    
  # 阶段3: 添加 substitution
  phase3:
    - step: "创建 recipe_substitution 表"
    - step: "逐步添加替代材料数据"
    
  # 阶段4: 添加 store_scope/version
  phase4:
    - step: "创建 recipe_store_scope 表"
    - step: "添加版本管理逻辑"
```

---

## 9. 业务验证

### 9.1 成本核算验证

**场景**：宫保鸡丁成本计算

```yaml
ingredients:
  - name: 鸡胸肉
    quantity: 200g
    actual_cost: 5.80
    
  - name: 花生米
    quantity: 50g
    actual_cost: 2.10
    
  - name: 干辣椒
    quantity: 15g
    actual_cost: 0.45
    
  - name: 花椒
    quantity: 5g
    actual_cost: 0.28
    
  - name: 酱油
    quantity: 30ml
    actual_cost: 0.55

total_cost: 9.18
selling_price: 28.00
gross_margin: (28.00 - 9.18) / 28.00 = 67.2%
```

### 9.2 库存扣减验证

**场景**：订单包含宫保鸡丁

```yaml
order:
  - dish: 宫保鸡丁
    quantity: 2
    
inventory_deduction:
  - material: 鸡胸肉
    quantity: 200g × 2 = 400g
    
  - material: 花生米
    quantity: 50g × 2 = 100g
    
  - material: 干辣椒
    quantity: 15g × 2 = 30g
    
  - material: 花椒
    quantity: 5g × 2 = 10g
    
  - material: 酱油
    quantity: 30ml × 2 = 60ml
```

### 9.3 替代材料验证

**场景**：鸡胸肉缺货，使用鸡腿肉替代

```yaml
recipe:
  - ingredient: 鸡胸肉
    quantity: 200g
    substitutions:
      - item: 鸡腿肉
        ratio: 1.0
        
substitution_usage:
  original_ingredient: 鸡胸肉
  substitute_ingredient: 鸡腿肉
  quantity: 200g × 1.0 = 200g
  cost_adjustment: 鸡腿肉成本可能不同
```

---

## 10. 总结

### 10.1 核心结论

1. **必需能力已支持**：quantity, unit, estimated_cost, actual_cost, is_required, allow_variance 已实现
2. **未来能力按需实现**：yield, waste, substitution, effective_date, store_scope, version 按优先级逐步实现
3. **渐进式迁移**：分4个阶段实施，降低风险

### 10.2 关键数据

| 维度 | 结论 |
|------|------|
| 当前能力覆盖 | 6/12（50%） |
| 必需能力覆盖 | 6/6（100%） |
| 迁移阶段数 | 4阶段 |
| 预计迁移周期 | 3-6个月 |

### 10.3 风险提示

| 风险 | 影响 | 缓解措施 |
|------|------|----------|
| 数据迁移失败 | 业务中断 | 分阶段迁移，保留回滚能力 |
| 性能下降 | 用户体验 | 添加索引，优化查询 |
| 业务逻辑复杂度增加 | 开发成本 | 渐进式实现，避免过度设计 |
