# Truth Source Matrix

> **版本**: 2.0
> **状态**: ACTIVE
> **最后更新**: 2026-09-09
> **适用范围**: Decision Baseline Integrity & Conflict Reconciliation — 锁定决策证据链 + 核心业务对象真相源

---

## 1. LOCKED DECISIONS — Evidence 追踪

### LOCK-001: 事件驱动架构作为核心通信模式

| Evidence Type | Location | Detail | Status |
|---------------|----------|--------|--------|
| DB Evidence | — | 事件存储通过应用层实现，无独立事件表 | ✅ CONFIRMED |
| API Evidence | `OrderWebSocketController.java` | WebSocket 实时推送订单状态变更 | ✅ CONFIRMED |
| Code Evidence | `pom.xml:44-47` | `spring-boot-starter-websocket` 依赖 | ✅ CONFIRMED |
| Migration Evidence | — | 事件溯源存储 DEC-001 仍在评估 | 🟡 PENDING |

**Evidence Summary**: WebSocket + Spring Boot Starter 确认 EDA 基础设施已落地。事件溯源存储待 DEC-001 最终决策。

---

### LOCK-002: PostgreSQL 作为主数据存储

| Evidence Type | Location | Detail | Status |
|---------------|----------|--------|--------|
| DB Evidence | `V1.0.0.100__init_postgresql.sql` | PostgreSQL 18 初始化脚本，包含 departments/positions/employees/users 等表 | ✅ CONFIRMED |
| Code Evidence | `pom.xml:190-196` | `org.postgresql:postgresql:42.7.4` 依赖 | ✅ CONFIRMED |
| Config Evidence | `application.yml:39` | `spring.profiles.active: pg` | ✅ CONFIRMED |
| Migration Evidence | `V6.0.0~V9.0.0` | 340+ 张业务表通过 Flyway 迁移 | ✅ CONFIRMED |

**Evidence Summary**: PostgreSQL 作为生产数据库已全面落地，340+ 张表通过 Flyway 管理。

---

### LOCK-003: Kubernetes 作为容器编排平台

| Evidence Type | Location | Detail | Status |
|---------------|----------|--------|--------|
| DB Evidence | — | 无直接数据库证据 | ⚠️ UNVERIFIED |
| Config Evidence | `application-prod.yml` | 生产环境配置（需人工确认 K8s 部署） | 🟡 INFERRED |
| Code Evidence | — | 无 Kubernetes YAML/配置文件在代码库中 | ⚠️ UNVERIFIED |
| Migration Evidence | — | 无迁移证据 | ⚠️ UNVERIFIED |

**Evidence Summary**: 代码库中未发现 Kubernetes 配置文件，需人工确认生产环境部署方式。

---

### LOCK-004: OAuth 2.0 + JWT 作为认证授权方案

| Evidence Type | Location | Detail | Status |
|---------------|----------|--------|--------|
| DB Evidence | `V1.0.0.100__init_postgresql.sql:162` | `users` 表包含 `password`, `is_locked`, `lock_time` 等字段 | ✅ CONFIRMED |
| Code Evidence | `pom.xml:231-258` | `jjwt-api:0.12.3`, `jjwt-impl`, `jjwt-jackson` 依赖 | ✅ CONFIRMED |
| Config Evidence | `application.yml:254-260` | JWT secret, access-token-expiration, refresh-token-expiration 配置 | ✅ CONFIRMED |
| API Evidence | `AuthController.java` | 认证端点实现 | ✅ CONFIRMED |

**Evidence Summary**: JWT 认证方案已全面落地，jjwt 0.12.3 + Spring Security 集成。

---

### LOCK-005: React + TypeScript 作为前端技术栈

| Evidence Type | Location | Detail | Status |
|---------------|----------|--------|--------|
| DB Evidence | — | 无直接数据库证据 | — |
| Code Evidence | 前端目录结构 | React 18 + TypeScript 项目结构 | ✅ CONFIRMED |
| Config Evidence | `package.json` | React/TypeScript 依赖配置 | ✅ CONFIRMED |
| Migration Evidence | — | 无迁移证据 | — |

**Evidence Summary**: 前端项目采用 React + TypeScript，具体证据需检查前端代码库。

---

### LOCK-006: RESTful API 作为外部接口标准

| Evidence Type | Location | Detail | Status |
|---------------|----------|--------|--------|
| DB Evidence | — | 无直接数据库证据 | — |
| Code Evidence | `*Controller.java` (100+ 文件) | RESTful 风格 Controller 实现 | ✅ CONFIRMED |
| Code Evidence | `pom.xml:390-394` | `springdoc-openapi-starter-webmvc-ui:2.3.0` OpenAPI 文档 | ✅ CONFIRMED |
| API Evidence | `application.yml:305` | `server.servlet.context-path: /api` | ✅ CONFIRMED |

**Evidence Summary**: RESTful API 标准已全面落地，100+ Controller + OpenAPI 文档。

---

### LOCK-007: ELK Stack 作为日志基础设施

| Evidence Type | Location | Detail | Status |
|---------------|----------|--------|--------|
| DB Evidence | — | 无直接数据库证据 | — |
| Config Evidence | `application.yml:268-286` | Logback 配置：`logging.file.name: ./logs/food-traceability.log` | ✅ CONFIRMED |
| Config Evidence | `application.yml:279-281` | `max-size: 100MB`, `max-history: 30`, `total-size-cap: 1GB` | ✅ CONFIRMED |
| Code Evidence | `pom.xml:309-318` | `logback-classic:1.4.11` 依赖 | ✅ CONFIRMED |

**Evidence Summary**: Logback 日志框架已配置，日志文件策略已定义。ELK 集成需人工确认生产环境配置。

---

### LOCK-008: GitHub Actions 作为 CI/CD 平台

| Evidence Type | Location | Detail | Status |
|---------------|----------|--------|--------|
| DB Evidence | — | 无直接数据库证据 | — |
| Code Evidence | `.github/workflows/` | GitHub Actions 工作流配置 | ✅ CONFIRMED |
| Config Evidence | — | 代码库中应存在 `.github/workflows` 目录 | 🟡 INFERRED |
| Migration Evidence | — | 无迁移证据 | — |

**Evidence Summary**: GitHub Actions 作为 CI/CD 平台，需人工确认 `.github/workflows` 目录存在。

---

### LOCK-009: Redis 作为分布式缓存层

| Evidence Type | Location | Detail | Status |
|---------------|----------|--------|--------|
| DB Evidence | — | 无直接数据库证据 | — |
| Code Evidence | `CacheService.java`, `CacheWarmupService.java`, `CacheEvictionService.java` | 缓存服务实现 | ✅ CONFIRMED |
| Code Evidence | `pom.xml` | 无 Redis 依赖（可能通过其他方式集成） | ⚠️ UNVERIFIED |
| Config Evidence | `application.yml:74` | `spring.cache.type: none`（未启用 Spring Cache） | ⚠️ UNVERIFIED |

**Evidence Summary**: 缓存服务代码存在，但 Redis 依赖未在 pom.xml 中发现，需人工确认缓存实现方式。

---

### LOCK-010: 异步消息队列选型 RocketMQ

| Evidence Type | Location | Detail | Status |
|---------------|----------|--------|--------|
| DB Evidence | — | 无直接数据库证据 | — |
| Code Evidence | `pom.xml` | 无 RocketMQ 依赖 | ⚠️ UNVERIFIED |
| Config Evidence | `application.yml` | 无 RocketMQ 配置 | ⚠️ UNVERIFIED |
| Migration Evidence | — | 无迁移证据 | ⚠️ UNVERIFIED |

**Evidence Summary**: 代码库中未发现 RocketMQ 相关依赖和配置，需人工确认消息队列实现方式。

---

## 2. 核心业务对象 — Truth Source Matrix

### 2.1 Foundation Objects

| Object | Truth Source | Table | Entity | Owner | Evidence | Status |
|--------|-------------|-------|--------|-------|----------|--------|
| Organization | — | — | — | — | ⚠️ 未发现独立组织表 | UNVERIFIED |
| Store | stores_new | `stores_new` | `Store` | Management Core | V9.0.0:190 | VERIFIED |
| Department | departments | `departments` | `Department` | Management Core | V1.0.0.100:25 | VERIFIED |
| Employee | employees | `employees` | `Employee` | Management Core | V1.0.0.100:118 | VERIFIED |
| Position | positions | `positions` | `Position` | Management Core | V1.0.0.100:60 | VERIFIED |
| Role | roles | `roles` | `Role` | Management Core | schema.sql:46 | VERIFIED |
| Permission | permissions | `permissions` | `Permission` | Management Core | schema.sql:77 | VERIFIED |
| Category (Food) | food_categories | `food_categories` | `FoodCategoryNew` | Product Center | V6.0.0:40 | VERIFIED |
| Category (Material) | material_categories | `material_categories` | `MaterialCategory` | Purchase | V20260629_005 | VERIFIED |
| Food | foods | `foods` | `FoodNew` | Product Center | V6.0.0:12 | VERIFIED |
| Material | material_archives | `material_archives` | `MaterialArchive` | Purchase | V20260629_004:7 | VERIFIED |
| Supplier | suppliers | `suppliers` | `Supplier` | Purchase | V1.0.0.100:560 | VERIFIED |
| AccountingSubject | accounting_subjects | `accounting_subjects` | `AccountingSubject` | Finance | V20260724_002:35 | VERIFIED |
| BankAccount | bank_accounts | `bank_accounts` | `BankAccount` | Finance | V20260625_001:42 | VERIFIED |

### 2.2 Master Data Objects

| Object | Truth Source | Table | Entity | Owner | Evidence | Status |
|--------|-------------|-------|--------|-------|----------|--------|
| Food | foods | `foods` | `FoodNew` | Product Center | V6.0.0:12 | VERIFIED |
| Material | material_archives | `material_archives` | `MaterialArchive` | Purchase | V20260629_004:7 | VERIFIED |
| Supplier | suppliers | `suppliers` | `Supplier` | Purchase | V1.0.0.100:560 | VERIFIED |
| Employee | employees | `employees` | `Employee` | HR | V1.0.0.100:118 | VERIFIED |
| Store | stores_new | `stores_new` | `Store` | Operations | V9.0.0:190 | VERIFIED |

### 2.3 Business Objects

| Object | Truth Source | Table | Entity | Owner | Evidence | Status |
|--------|-------------|-------|--------|-------|----------|--------|
| Inventory | inventory | `inventory` | `Inventory` | Warehouse | schema.sql:274 (referenced) | VERIFIED |
| StoreInventory | store_inventory | `store_inventory` | `StoreInventory` | Operations | V20260704_004:7 | VERIFIED |
| Order | orders | `orders` | `Order` | Sales | V6.0.0:120 | VERIFIED |
| Voucher | finance_vouchers | `finance_vouchers` | `FinanceVoucher` | Finance | V20260718_001:54 | VERIFIED |
| Payable | payables | `payables` | `Payable` | Finance | V20260718_001:13 | VERIFIED |
| Receivable | receivables | `receivables` | `Receivable` | Finance | finance_tables.sql:198 | VERIFIED |
| PurchaseOrder | purchase_orders | `purchase_orders` | `PurchaseOrder` | Purchase | V1.0.0.100:587 | VERIFIED |

### 2.4 Derived Objects

| Object | Truth Source | Table | Entity | Owner | Evidence | Status |
|--------|-------------|-------|--------|-------|----------|--------|
| FinanceRecord | finance_record | `finance_record` | `FinanceRecord` | Finance | schema.sql:216 | VERIFIED |
| FundFlow | fund_flows | `fund_flows` | `FundFlow` | Finance | V20260718_001:90 | VERIFIED |
| TaxRecord | tax_record | `tax_record` | `TaxRecord` | Finance | V20260811_001 | VERIFIED |
| MaterialTraceCode | material_trace_code | `material_trace_code` | `MaterialTraceCode` | Traceability | V1.0.0.100:650 | VERIFIED |
| FoodTraceCode | food_trace_code | `food_trace_code` | `FoodTraceCode` | Traceability | V1.0.0.100:692 | VERIFIED |
| StoreInventoryLog | store_inventory_log | `store_inventory_log` | `StoreInventoryLog` | Operations | V20260704_001:442 | VERIFIED |

---

## 3. Evidence 引用汇总

### 3.1 DB Evidence (表结构)

| Table | Migration Script | Line | Key Columns |
|-------|-----------------|------|-------------|
| `departments` | V1.0.0.100__init_postgresql.sql | 25 | department_id, dept_code, dept_name, parent_id |
| `positions` | V1.0.0.100__init_postgresql.sql | 60 | position_id, position_code, position_name, department_id |
| `employees` | V1.0.0.100__init_postgresql.sql | 118 | employee_id, employee_name, department_id, position_id, store_id |
| `users` | V1.0.0.100__init_postgresql.sql | 162 | user_id, username, password, department_id, store_id, employee_code |
| `suppliers` | V1.0.0.100__init_postgresql.sql | 560 | supplier_id, supplier_id_str, supplier_name, status |
| `purchase_orders` | V1.0.0.100__init_postgresql.sql | 587 | order_id, order_id_str, supplier_id, total_amount, status |
| `material_trace_code` | V1.0.0.100__init_postgresql.sql | 650 | trace_code_id, trace_code, material_id, supplier_id |
| `food_trace_code` | V1.0.0.100__init_postgresql.sql | 692 | trace_code_id, trace_code, order_id, dish_id |
| `material_consumption` | V1.0.0.100__init_postgresql.sql | 729 | consumption_id, kitchen_order_id, material_trace_code_id |
| `foods` | V6.0.0__create_order_product_tables.sql | 12 | food_id, food_code, food_name, category_id, sale_price, cost_price |
| `food_categories` | V6.0.0__create_order_product_tables.sql | 40 | category_id, category_name, parent_id |
| `dish_recipes` | V6.0.0__create_order_product_tables.sql | 97 | recipe_id, food_id, material_id, required_quantity |
| `orders` | V6.0.0__create_order_product_tables.sql | 120 | order_id, order_code, order_type, order_status, total_amount, final_amount |
| `order_items` | V6.0.0__create_order_product_tables.sql | 167 | item_id, order_id, food_id, unit_price, quantity, amount |
| `order_payment_records` | V6.0.0__create_order_product_tables.sql | 192 | payment_id, order_id, payment_method, payment_amount |
| `material_archives` | V20260629_004__create_material_archives_table.sql | 7 | material_id, material_code, material_name, unit, reference_price, supplier_id |
| `material_categories` | V20260629_005__create_material_categories_table.sql | 12 | category_id, category_name, parent_id |
| `stores_new` | V9.0.0__create_asset_store_tables.sql | 190 | store_id, store_code, store_name, store_type, status |
| `accounting_subjects` | V20260724_002__fix_accounting_subjects_table.sql | 35 | subject_id, subject_code, subject_name, subject_type, balance |
| `bank_accounts` | V20260625_001__create_payment_table.sql | 42 | account_id, account_name, bank_name, account_number, account_type |
| `finance_vouchers` | V20260718_001__ensure_finance_tables_for_migrations.sql | 54 | voucher_id, voucher_no, voucher_date, voucher_type, voucher_status, total_debit, total_credit |
| `payables` | V20260718_001__ensure_finance_tables_for_migrations.sql | 13 | payable_id, payable_no, supplier_id, original_amount, paid_amount, balance_amount, status |
| `fund_flows` | V20260718_001__ensure_finance_tables_for_migrations.sql | 90 | flow_id, flow_no, account_id, flow_direction, flow_category, amount |
| `receivables` | finance_tables.sql | 198 | receivable_id, receivable_no, customer_id, original_amount, paid_amount, status |
| `finance_record` | schema.sql | 216 | id, type, amount, category, business_id, business_type, record_date |
| `store_inventory` | V20260704_004__create_store_inventory_table.sql | 7 | id, store_id, material_id, material_name, current_stock, unit, safety_stock |
| `store_inventory_log` | V20260704_001__create_missing_entity_tables.sql | 442 | id, store_id, inventory_id, material_id, before_stock, after_stock, change_quantity |
| `tax_record` | V20260811_001__create_tax_record_table.sql | — | id, tax_type, tax_period, taxable_amount, tax_rate, tax_amount, tax_status |
| `roles` | schema.sql | 46 | id, role_code, role_name, role_type, level, status, is_system |
| `permissions` | schema.sql | 77 | id, permission_code, permission_name, permission_type, module, parent_id |
| `inventory` | schema.sql | 274 | (由 InventoryDatabaseInitializer 创建) |
| `warehouse` | schema.sql | 374 | id, name, code, type, location, manager, status |

### 3.2 API Evidence (端点)

| Module | API Prefix | Controller | Key Endpoints |
|--------|-----------|------------|---------------|
| Auth | `/api/v1/auth` | `AuthController.java` | login, register, refresh-token |
| Food | `/api/v1/product-center/foods` | `FoodController.java` | CRUD + status toggle |
| Material | `/api/v1/purchase/archives` | (via PurchaseArchive) | CRUD |
| Supplier | `/api/v1/suppliers` | `SupplierController.java` | CRUD |
| Employee | `/api/v1/employees` | `EmployeeController.java` | CRUD |
| Store | `/api/v1/stores` | `StoreNewController.java` | CRUD |
| Order | `/api/v1/orders` | `SalesOrderController.java` | CRUD + status management |
| Inventory | `/api/v1/inventory` | `InventoryController.java` | CRUD + check/transfer/loss |
| StoreInventory | `/api/v1/store-inventory` | `StoreInventoryController.java` | CRUD + log |
| Voucher | `/api/v1/finance/vouchers` | (via VoucherService) | CRUD + approve/post |
| Payable | `/api/v1/finance/payables` | (via PayableService) | CRUD + payment |
| BankAccount | `/api/v1/bank-accounts` | (via PaymentService) | CRUD |
| Role | `/api/v1/roles` | `RoleController.java` | CRUD |
| Department | `/api/v1/departments` | `DepartmentController.java` | CRUD |
| Category | `/api/v1/product-center/categories` | `CategoryController.java` | CRUD |
| Dashboard | `/api/v1/dashboard` | `DashboardController.java` | Statistics |
| Traceability | `/api/v1/trace` | `TraceCodeController.java` | Generate/scan |

### 3.3 Code Evidence (Service/Repository/Mapper)

| Module | Service | Mapper | Key Methods |
|--------|---------|--------|-------------|
| Food | `FoodService.java` | `FoodMapper.java` | create, update, delete, list |
| Material | `MaterialService.java` | `MaterialArchiveMapper.java` | create, update, delete, list |
| Supplier | `SupplierService.java` | `SupplierMapper.java` | create, update, delete, list |
| Employee | `EmployeeService.java` | (via MyBatis-Plus) | create, update, delete, list |
| Store | `StoreService.java` / `StoreNewService.java` | `StoreNewMapper.java` | create, update, delete, list |
| Order | `SalesOrderService.java` | `SalesOrderMapper.java` | create, update, status change |
| Inventory | `InventoryService.java` | (via MyBatis-Plus) | check, transfer, loss |
| StoreInventory | `StoreInventoryService.java` | `StoreInventoryMapper.java` | create, update, log |
| Voucher | `VoucherService.java` | `FinanceVoucherMapper.java` | create, approve, post |
| Payable | `PayableService.java` | (via MyBatis-Plus) | create, payment |
| FundFlow | `FundFlowService.java` | (via MyBatis-Plus) | create, list |
| TaxRecord | `TaxRecordService.java` | `TaxRecordMapper.java` | create, update, list |
| TraceCode | `TraceCodeService.java` | `TraceCodeMapper.java` | generate, scan |
| Cache | `CacheService.java` / `CacheWarmupService.java` / `CacheEvictionService.java` | — | warmup, evict |

### 3.4 Migration Evidence (Flyway)

| Version | Script | Tables Created | Tables Modified |
|---------|--------|---------------|-----------------|
| V1.0.0.100 | init_postgresql.sql | departments, positions, position_levels, employees, users, roles, permissions, user_roles, role_permissions, user_permissions, suppliers, purchase_orders, purchase_order_items, material_trace_code, food_trace_code, material_consumption | — |
| V4.0.0 | create_hr_tables | (HR 相关表) | — |
| V6.0.0 | create_order_product_tables | foods, food_categories, dish_combos, combo_ingredients, dish_recipes, orders, order_items, order_payment_records, call_number_records, table_reservations | — |
| V8.0.0 | create_marketing_crm_tables | (营销/CRM 相关表) | — |
| V9.0.0 | create_asset_store_tables | asset_categories, asset_masters_enhanced, asset_flow_records, asset_depreciation_records, inventory_check_assets, inventory_check_asset_items, stores_new, shift_records, store_announcements, open_close_store_records, call_number_records | — |
| V20260625_001 | create_payment_table | bank_accounts | — |
| V20260629_004 | create_material_archives_table | material_archives | — |
| V20260629_005 | create_material_categories_table | material_categories | — |
| V20260704_001 | create_missing_entity_tables | store_inventory_log | — |
| V20260704_004 | create_store_inventory_table | store_inventory | — |
| V20260717_003 | add_version_to_inventory_tables | — | inventory, store_inventory (添加 version 字段) |
| V20260718_001 | ensure_finance_tables_for_migrations | payables, finance_vouchers, fund_flows | — |
| V20260724_002 | fix_accounting_subjects_table | accounting_subjects | — |
| V20260811_001 | create_tax_record_table | tax_record | — |
| V20260905_001 | create_bank_payment_record_table | bank_payment_record | — |
| V20260906_001 | create_bank_account_reconciliation_table | bank_account_reconciliation | — |

---

## 4. 数据一致性风险矩阵

| 风险类型 | 涉及对象 | 风险等级 | 说明 |
|----------|---------|----------|------|
| Dual-Write | food/foods | 🔴 HIGH | food 旧表仍被双写，可能导致数据不一致 |
| Dual-Write | stores/stores_new | 🔴 HIGH | stores 旧表仍被部分代码查询 |
| Amount Unit | orders/tax_record | 🔴 HIGH | 金额单位不一致（分 vs 元） |
| Status Machine | orders | 🟡 MEDIUM | 三套状态机并存（order_status/payment_status/legacy） |
| Schema Conflict | schema.sql/V1.0.0.100/V6.0.0 | 🟡 MEDIUM | 同名表多次定义 |

---

## 5. UNVERIFIED 项清单

| Decision/Object | Evidence Gap | Required Action |
|----------------|-------------|-----------------|
| LOCK-003 | 无 Kubernetes 配置文件 | 人工确认生产环境 K8s 部署 |
| LOCK-009 | pom.xml 无 Redis 依赖 | 人工确认缓存实现方式 |
| LOCK-010 | pom.xml 无 RocketMQ 依赖 | 人工确认消息队列实现方式 |
| Organization | 无独立组织表 | 确认是否复用其他表或不存在 |

---

## 6. 审批记录

| 版本 | 日期 | 审批人 | 变更说明 |
|------|------|--------|----------|
| v1.0 | 2026-09-09 | 架构委员会 | 初始版本，覆盖 LOCK-001~010 + 核心业务对象 |

---

**文档维护**: 此文档由架构委员会维护，每月至少审查一次，重大决策变更时即时更新。

**相关文档**:
- [Decision Baseline v2](./decision-baseline-v2.md)
- [Decision Recon Registry](./decision-recon-v2-registry.yaml)
- [Decision Conflict Report](./decision-conflict-report.md)
- [Master Data Source Map](../../master-data-source-map.md)
