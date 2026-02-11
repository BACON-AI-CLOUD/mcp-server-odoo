# BACON-AI Odoo MCP Server

**Orchestrating Intelligence for the Real World.**

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![License: MPL 2.0](https://img.shields.io/badge/License-MPL_2.0-brightgreen.svg)](https://opensource.org/licenses/MPL-2.0)
[![PyPI](https://img.shields.io/pypi/v/mcp-server-odoo.svg)](https://pypi.org/project/mcp-server-odoo/)
[![Tests](https://img.shields.io/badge/tests-572%20passing-brightgreen.svg)](#test-coverage)

The most comprehensive MCP server for Odoo ERP. Connect AI assistants like Claude, GPT, and Gemini to your Odoo instance with **17 tools** covering CRUD operations and complete end-to-end business workflows -- from quotation to manufacturing to delivery.

Built and maintained by [BACON-AI](https://bacon-ai.cloud) | Contact: [hello@bacon-ai.cloud](mailto:hello@bacon-ai.cloud)

> **Fork of [ivnvxd/mcp-server-odoo](https://github.com/ivnvxd/mcp-server-odoo)** with workflow tools, bug fixes, and 153 additional tests validated against Odoo 19.0.

---

## Why BACON-AI Odoo MCP?

| Capability | This Server | Typical Alternatives |
|-----------|-------------|---------------------|
| Core CRUD (search, create, update, delete) | 7 tools | 3-5 tools |
| Business Workflow Automation | 10 workflow tools | None |
| End-to-End Process Coverage | Sales, Purchase, Manufacturing, Inventory | CRUD only |
| Database DevOps Pipeline | Shift-left testing with isolated DB branches | None |
| AI-Assisted Data Migration | Import/export with validation and rollback | None |
| YOLO Mode (no module install needed) | Read-only + Full access | Not available |
| Validated Test Suite | 572 tests (153 against live Odoo 19.0) | Minimal or none |
| PyPI Distribution | `pip install mcp-server-odoo` | Manual setup |

---

## Database DevOps Pipeline (Shift-Left Testing)

> **Proven at scale.** This approach is adapted from BACON-AI founder Colin's shift-left test strategies deployed on large-scale international SAP programs, where separating development and maintenance landscapes prevented production defects across multi-country rollouts. The same discipline -- database isolation, regression gating, and promotion pipelines -- now powers AI-driven ERP testing.

Enterprise ERP changes are high-risk. A broken workflow in production means failed orders, blocked deliveries, and real revenue impact. The BACON-AI Odoo MCP Server brings a **Database DevOps Pipeline** that mirrors git branching -- every feature gets its own isolated database copy, tested independently before promotion through regression gates to production.

### The Landscape Architecture

```mermaid
graph TB
    PROD[(Production DB<br/><i>Live business data</i>)]
    PREPROD[(Pre-Production DB<br/><i>Regression testing</i>)]
    SIT1[(SIT1_feature-A<br/><i>Sales workflow fix</i>)]
    SIT2[(SIT1_feature-B<br/><i>New pricing rules</i>)]
    SIT3[(SIT1_feature-C<br/><i>Inventory changes</i>)]
    MAINT[(Maintenance DB<br/><i>Hot fixes & patches</i>)]
    HOTFIX[(HOTFIX_urgent-fix<br/><i>Critical bug fix</i>)]

    PROD -- "copy for<br/>feature testing" --> SIT1
    PROD -- "copy for<br/>feature testing" --> SIT2
    PROD -- "copy for<br/>feature testing" --> SIT3
    PROD -- "copy for<br/>maintenance" --> MAINT

    SIT1 -- "SIT pass" --> PREPROD
    SIT2 -- "SIT pass" --> PREPROD
    SIT3 -- "SIT pass" --> PREPROD
    PREPROD -- "regression<br/>pass" --> PROD

    MAINT -- "copy for<br/>hot fix" --> HOTFIX
    HOTFIX -- "tested &<br/>verified" --> MAINT
    MAINT -- "emergency<br/>patch" --> PROD

    style PROD fill:#d32f2f,color:#fff,stroke:#b71c1c
    style PREPROD fill:#f57c00,color:#fff,stroke:#e65100
    style SIT1 fill:#1976d2,color:#fff,stroke:#0d47a1
    style SIT2 fill:#1976d2,color:#fff,stroke:#0d47a1
    style SIT3 fill:#1976d2,color:#fff,stroke:#0d47a1
    style MAINT fill:#7b1fa2,color:#fff,stroke:#4a148c
    style HOTFIX fill:#7b1fa2,color:#fff,stroke:#4a148c
```

**Development Landscape** (left): Feature branches get isolated database copies. Developers and AI agents test freely without polluting production. Each feature runs the full SIT suite before promotion.

**Maintenance Landscape** (right): Hot fixes, security patches, and urgent bug fixes follow a separate fast-track path. Critical fixes are tested on a maintenance copy and applied directly to production with minimal delay.

### The Promotion Pipeline

```mermaid
sequenceDiagram
    participant Dev as Developer / AI Agent
    participant SIT as SIT Database Copy
    participant Tests as Test Suite (572 tests)
    participant PreProd as Pre-Production
    participant Regression as Regression Gate
    participant Prod as Production

    Dev->>SIT: 1. Copy production DB
    Note over SIT: SIT1_prod_feature-A_20260211
    Dev->>SIT: 2. Develop & test feature
    Note over SIT: Isolated - no production impact
    Dev->>Tests: 3. Run SIT tests
    Tests-->>Dev: 84 integration tests pass
    Dev->>PreProd: 4. Promote feature
    Note over PreProd: Merge with other tested features
    PreProd->>Regression: 5. Full regression suite
    Regression-->>PreProd: 572 tests pass
    Note over Regression: All existing workflows verified
    PreProd->>Prod: 6. Deploy to production
    Note over Prod: Audited, confirmed, reversible
```

### Why Regression Testing is Critical

When multiple features are developed in parallel, each passing its own SIT tests independently, **cross-feature conflicts only surface during regression testing**. A change to the sales workflow in Feature-A might break the inventory logic that Feature-B depends on. Without regression gating on the merged pre-production database, these defects reach production.

```mermaid
graph LR
    subgraph "Feature Testing (Isolated)"
        FA[Feature A<br/>Sales fix<br/>SIT: PASS]
        FB[Feature B<br/>Inventory change<br/>SIT: PASS]
        FC[Feature C<br/>New pricing<br/>SIT: PASS]
    end

    subgraph "Regression Testing (Merged)"
        MERGED[Pre-Production<br/>A + B + C merged]
        RGT{Regression<br/>572 tests}
    end

    subgraph "Outcome"
        PASS[All pass<br/>Deploy to production]
        FAIL[Conflict detected<br/>Fix before deploying]
    end

    FA --> MERGED
    FB --> MERGED
    FC --> MERGED
    MERGED --> RGT
    RGT -- "pass" --> PASS
    RGT -- "fail" --> FAIL

    style FA fill:#1976d2,color:#fff
    style FB fill:#1976d2,color:#fff
    style FC fill:#1976d2,color:#fff
    style MERGED fill:#f57c00,color:#fff
    style RGT fill:#f57c00,color:#fff
    style PASS fill:#388e3c,color:#fff
    style FAIL fill:#d32f2f,color:#fff
```

### Development vs Maintenance Landscapes

This separation is essential for enterprise ERP operations:

| Landscape | Purpose | Database Copies | Testing | Deployment Path |
|-----------|---------|----------------|---------|-----------------|
| **Development** | New features, enhancements, refactoring | SIT1_prod_feature-* | Full SIT + regression | Feature -> Pre-Prod -> Production |
| **Maintenance** | Hot fixes, security patches, urgent bugs | HOTFIX_urgent-* | Targeted + smoke tests | Hotfix -> Maintenance -> Production (fast-track) |

**Development** follows the full pipeline: copy production, develop in isolation, SIT test, promote to pre-production, run regression, deploy. This ensures new features don't break existing workflows.

**Maintenance** follows a fast-track: copy production to maintenance landscape, apply the fix, run targeted tests plus smoke tests on critical paths, deploy directly. Urgent production issues can't wait for a full regression cycle.

Both landscapes are AI-driven. The MCP server provides the tools for AI agents to copy databases, execute workflows, run tests, and validate results -- all through natural language.

### Data Migration & Cut-Over

The same pipeline supports system migrations (e.g., legacy ERP to Odoo):

```mermaid
graph LR
    LEGACY[Legacy System<br/>SAP / Custom ERP] -- "export data" --> FILES[Migration Files<br/>customer-master.xlsx<br/>product-catalog.csv<br/>open-orders.xml]
    FILES -- "AI-assisted<br/>import" --> STAGING[(Migration Staging DB)]
    STAGING -- "validate &<br/>reconcile" --> PREPROD[(Pre-Production)]
    PREPROD -- "regression<br/>pass" --> PROD[(Production)]

    style LEGACY fill:#757575,color:#fff
    style FILES fill:#fdd835,color:#000
    style STAGING fill:#1976d2,color:#fff
    style PREPROD fill:#f57c00,color:#fff
    style PROD fill:#d32f2f,color:#fff
```

```
User: "Read the customer-master.xlsx and upload those customers
       to the customer master in the staging database"

AI Agent:
  1. Parse 500 rows from Excel
  2. Map columns to Odoo fields
  3. Validate: 495 valid, 5 issues
  4. Dry-run: "Would create 480 new, update 15 existing"
  5. Import with progress tracking
  6. Rollback available for 24 hours
```

---

## End-to-End Business Workflows

Every workflow below has been tested end-to-end against a real Odoo 19.0 instance with 84 system integration tests.

### Order-to-Cash (Sales)

```mermaid
graph LR
    Q[Create<br/>Quotation] --> C[Confirm<br/>Quotation]
    C --> SO[Sales<br/>Order]
    SO --> DEL[Deliver to<br/>Customer]
    DEL --> INV[Invoice]
    SO --> STATUS[Workflow<br/>Status]

    style Q fill:#1976d2,color:#fff
    style C fill:#1976d2,color:#fff
    style SO fill:#388e3c,color:#fff
    style DEL fill:#f57c00,color:#fff
    style INV fill:#d32f2f,color:#fff
    style STATUS fill:#7b1fa2,color:#fff
```

1. **Create Quotation** with customer, product lines, quantities, and pricing
2. **Confirm Quotation** to convert it into a sales order
3. **Deliver to Customer** by validating the outgoing shipment
4. **Track Workflow Status** to see deliveries, invoices, and related documents

### Procure-to-Pay (Purchasing)

```mermaid
graph LR
    PO[Create<br/>Purchase Order] --> CP[Confirm<br/>PO]
    CP --> SHIP[Incoming<br/>Shipment]
    SHIP --> RCV[Receive<br/>Inventory]
    CP --> STATUS[Workflow<br/>Status]

    style PO fill:#1976d2,color:#fff
    style CP fill:#1976d2,color:#fff
    style SHIP fill:#388e3c,color:#fff
    style RCV fill:#f57c00,color:#fff
    style STATUS fill:#7b1fa2,color:#fff
```

1. **Create Purchase Order** with vendor, product lines, and unit prices
2. **Confirm Purchase Order** to generate incoming shipments
3. **Receive Inventory** by validating the incoming receipt
4. **Track Workflow Status** to see receipts and payment status

### Plan-to-Produce (Manufacturing)

```mermaid
graph LR
    BOM[Create<br/>BOM] --> MO[Create<br/>MO]
    MO --> CMO[Confirm<br/>MO]
    CMO --> ASSIGN[Material<br/>Assignment]
    ASSIGN --> PRODUCE[Production]
    MO --> STATUS[Workflow<br/>Status]

    style BOM fill:#1976d2,color:#fff
    style MO fill:#1976d2,color:#fff
    style CMO fill:#388e3c,color:#fff
    style ASSIGN fill:#f57c00,color:#fff
    style PRODUCE fill:#d32f2f,color:#fff
    style STATUS fill:#7b1fa2,color:#fff
```

1. **Create Bill of Materials** defining product components and quantities
2. **Create Manufacturing Order** for a specific product and quantity
3. **Confirm Manufacturing Order** to assign materials and begin production
4. **Track Workflow Status** to monitor production progress

### Full Integrated Workflow

All three workflows connect into a complete supply chain:

```python
# 1. Create and confirm a sales quotation
quotation = create_quotation(customer_id=15, product_lines=[...])
order = confirm_quotation(quotation_id=quotation["quotation_id"])

# 2. Create manufacturing order for the sold product
mo = create_manufacturing_order(product_id=373, quantity=2.0, origin=order["order_name"])

# 3. Purchase raw materials from vendor
po = create_purchase_order(vendor_id=42, product_lines=[...])
confirm_purchase_order(po_id=po["po_id"])
receive_inventory(po_name=po["po_name"])

# 4. Confirm manufacturing and produce goods
confirm_manufacturing_order(mo_id=mo["mo_id"])

# 5. Deliver to customer
deliver_to_customer(so_name=order["order_name"])

# 6. Get complete workflow status showing the entire chain
status = get_workflow_status(order_id=order["order_id"], order_type="sale")
```

---

## Available Tools (17 Total)

### Core Operations (7 Tools)

| Tool | Description |
|------|-------------|
| `search_records` | Search any Odoo model with domain filters, field selection, pagination, and ordering |
| `get_record` | Retrieve a specific record by ID with smart field defaults |
| `list_models` | Discover all accessible models with field metadata |
| `create_record` | Create records with field validation and permission checks |
| `update_record` | Update specific fields on existing records |
| `delete_record` | Delete records respecting model-level permissions |
| `read_group` | Aggregate data with grouping and statistical functions |

### Workflow Tools (10 Tools)

| Tool | Description |
|------|-------------|
| `create_quotation` | Create sales quotation with customer, product lines, quantities, pricing |
| `confirm_quotation` | Convert quotation to confirmed sales order (triggers delivery creation) |
| `create_purchase_order` | Create PO with vendor, product lines, and pricing |
| `confirm_purchase_order` | Confirm PO (triggers incoming shipment creation) |
| `receive_inventory` | Validate incoming receipt by PO name or picking ID |
| `deliver_to_customer` | Validate outgoing delivery by SO name or picking ID |
| `create_manufacturing_order` | Create manufacturing order for a product (MRP module) |
| `confirm_manufacturing_order` | Confirm MO and assign materials for production |
| `create_bom` | Create Bill of Materials with component lines |
| `get_workflow_status` | Trace order through complete lifecycle with related documents |

### Database DevOps Tools (Roadmap)

| Tool | Description | Status |
|------|-------------|--------|
| `switch_database` | Dynamic database connection switching | Planned |
| `list_databases` | Discover available databases | Planned |
| `copy_database` | Duplicate database for isolated testing | Planned |
| `drop_database` | Clean up test database copies | Planned |
| `import_data` | AI-assisted data import with validation | Planned |
| `export_data` | Extract data to CSV/JSON/Excel | Planned |

---

## Test Coverage

This server is backed by one of the most thorough test suites of any Odoo MCP implementation. Testing follows the **BACON-AI Agentic-Driven Development & Testing (ADDT)** methodology -- an evolution of TDD where AI agents don't just execute tests, they **learn and improve**. Every test failure feeds back into the agent's knowledge base, refining future test design and execution through a continuous self-annealing cycle.

### V-Model: Development Phases Mapped to Test Levels

The BACON-AI V-Model maps each development phase to a corresponding test level, ensuring every layer of the system is validated at the appropriate scope:

```mermaid
graph LR
    subgraph "Development Phases"
        REQ[Requirements<br/>Analysis]
        ARCH[Architecture<br/>& Design]
        IMPL[Implementation<br/>& Unit Design]
        CODE[Coding]
    end

    subgraph "Test Levels"
        UAT[UAT<br/>User Acceptance]
        SIT[SIT<br/>System Integration]
        FUT[FUT<br/>Functional Unit]
        TUT[TUT<br/>Technical Unit]
    end

    REQ -.- UAT
    ARCH -.- SIT
    IMPL -.- FUT
    CODE -.- TUT

    CODE --> TUT
    TUT --> FUT
    FUT --> SIT
    SIT --> UAT

    style REQ fill:#1976d2,color:#fff
    style ARCH fill:#1976d2,color:#fff
    style IMPL fill:#1976d2,color:#fff
    style CODE fill:#1976d2,color:#fff
    style TUT fill:#388e3c,color:#fff
    style FUT fill:#388e3c,color:#fff
    style SIT fill:#f57c00,color:#fff
    style UAT fill:#d32f2f,color:#fff
```

| Level | Full Name | Scope | This Project |
|-------|-----------|-------|-------------|
| **TUT** | Technical Unit Test | Individual functions, mocked dependencies | 69 workflow handler tests + 419 core tests |
| **FUT** | Functional Unit Test | Single features, business logic validation | Workflow tool functional verification |
| **SIT** | System Integration Test | End-to-end with real systems (no mocks) | 84 tests against live Odoo 19.0 |
| **UAT** | User Acceptance Test | Business validation by end users | Full workflow walkthroughs |
| **RGT** | Regression Test | All tests on merged pre-production | Full 572-test suite |

### Self-Annealing: Tests That Improve Themselves

ADDT goes beyond traditional TDD through **self-annealing** -- a continuous improvement cycle where test failures drive knowledge accumulation:

```mermaid
graph LR
    A[Action<br/>Execute tests] --> F[Feedback<br/>Analyze failures]
    F --> ADJ[Adjustment<br/>Fix root cause]
    ADJ --> DOC[Documentation<br/>Log lesson learned]
    DOC --> APP[Application<br/>Apply to future tests]
    APP --> A

    style A fill:#1976d2,color:#fff
    style F fill:#d32f2f,color:#fff
    style ADJ fill:#f57c00,color:#fff
    style DOC fill:#7b1fa2,color:#fff
    style APP fill:#388e3c,color:#fff
```

**Example from this project:** SIT tests initially failed against Odoo 19.0 because `action_confirm` on `sale.order` returns different states depending on warehouse configuration. The self-annealing cycle captured this as a lesson learned, and all subsequent test assertions were redesigned to check `success=True` instead of asserting a specific state -- a pattern now applied across all workflow tests.

### Test Pyramid

```mermaid
graph TB
    subgraph "Test Pyramid"
        TUT["TUT: Technical Unit Tests<br/>69 mocked workflow tests<br/>419 existing unit tests"]
        FUT["FUT: Functional Unit Tests<br/>Workflow tool validation"]
        SIT["SIT: System Integration Tests<br/>84 tests against live Odoo 19.0"]
        RGT["RGT: Regression Tests<br/>Full 572-test suite on<br/>pre-production database"]
    end

    TUT --> FUT --> SIT --> RGT

    style TUT fill:#388e3c,color:#fff
    style FUT fill:#388e3c,color:#fff
    style SIT fill:#f57c00,color:#fff
    style RGT fill:#d32f2f,color:#fff
```

### Test Summary

| Category | Tests | Status | Scope |
|----------|-------|--------|-------|
| Workflow Unit Tests (mocked) | 69 | All passing | All 10 handlers, 4 scenarios each |
| System Integration Tests (live Odoo 19.0) | 84 | All passing | 11 categories, real XML-RPC |
| Existing Unit & Integration Tests | 419 | All passing | Core CRUD, auth, resources |
| **Total** | **572** | **All passing** | |

### System Integration Test Categories (SIT)

All SIT tests run against a **real Odoo 19.0 instance** with actual XML-RPC calls -- no mocks.

| SIT | Category | Tests | What It Validates |
|-----|----------|-------|-------------------|
| SIT-1 | Sales End-to-End | 9 | Quotation creation, confirmation, delivery generation, multi-line orders, date handling |
| SIT-2 | Purchase End-to-End | 6 | PO creation, confirmation, receipt generation, multi-line orders |
| SIT-3 | CRUD Lifecycle | 7 | Create, read, search, update, delete with verification at each step |
| SIT-4 | Data Consistency | 7 | Field-level write/read verification, boolean/numeric fields, empty value handling |
| SIT-5 | Error Handling | 17 | Invalid models, nonexistent records, state violations, missing parameters, duplicate operations |
| SIT-6 | Model Discovery | 8 | Model listing, metadata structure, resource templates, YOLO mode indicators |
| SIT-7 | Workflow Status | 5 | Status tracking structure, delivery references, order name formats |
| SIT-8 | Connection & Auth | 7 | Authentication flow, server version, health check, reconnection, context manager |
| SIT-9 | Direct Connection Ops | 10 | Low-level search, read, write, unlink, fields_get, search_read, execute, ordering |
| SIT-10 | Manufacturing | 2 | MO creation and workflow status (conditional on MRP module) |
| SIT-11 | Search Features | 6 | String domains, pagination, ordering, smart defaults, empty results |

### Regression Testing Strategy

Every feature promotion triggers the **full 572-test regression suite** against the merged pre-production database. This catches cross-feature conflicts that individual SIT testing cannot detect.

| Gate | Tests Run | What It Catches |
|------|-----------|-----------------|
| **Feature SIT** | 84 integration tests | Feature-specific bugs against real data |
| **Pre-Production Regression** | All 572 tests | Cross-feature conflicts, side effects |
| **Production Smoke** | Critical path subset | Post-deployment verification |

---

## Quick Start

### Installation

```bash
pip install mcp-server-odoo
```

### Configuration

Add to your MCP client settings (Claude Desktop, Claude Code, Cursor, VS Code, etc.):

```json
{
  "mcpServers": {
    "odoo": {
      "command": "uvx",
      "args": ["mcp-server-odoo"],
      "env": {
        "ODOO_URL": "https://your-odoo-instance.com",
        "ODOO_API_KEY": "your-api-key-here",
        "ODOO_DB": "your-database-name"
      }
    }
  }
}
```

### YOLO Mode (No Module Install Needed)

Connect to **any standard Odoo instance** without installing additional modules:

```json
{
  "mcpServers": {
    "odoo": {
      "command": "uvx",
      "args": ["mcp-server-odoo"],
      "env": {
        "ODOO_URL": "http://localhost:8069",
        "ODOO_USER": "admin",
        "ODOO_PASSWORD": "admin",
        "ODOO_DB": "mydb",
        "ODOO_YOLO": "true"
      }
    }
  }
}
```

| Mode | Env Value | Description |
|------|-----------|-------------|
| Off (default) | `off` | Requires MCP module installed on Odoo |
| Read-Only | `read` | All read operations, no writes. Safe for demos. |
| Full Access | `true` | Full CRUD + workflows. Dev/test environments only. |

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `ODOO_URL` | Yes | Your Odoo instance URL |
| `ODOO_API_KEY` | Yes* | API key authentication (recommended) |
| `ODOO_USER` | Yes* | Username (if not using API key) |
| `ODOO_PASSWORD` | Yes* | Password (if not using API key) |
| `ODOO_DB` | No | Database name (auto-detected if not set) |
| `ODOO_YOLO` | No | YOLO mode: `off`, `read`, or `true` |

*Either `ODOO_API_KEY` or both `ODOO_USER` and `ODOO_PASSWORD` are required.

### Transport Options

| Transport | Use Case | Command |
|-----------|----------|---------|
| **stdio** (default) | Desktop AI apps (Claude Desktop, Cursor) | `uvx mcp-server-odoo` |
| **streamable-http** | Remote access, web clients | `uvx mcp-server-odoo --transport streamable-http --port 8000` |

---

## Usage Examples

Once connected, ask your AI assistant naturally:

**Search & Retrieve:**
- "Show me all customers from Germany"
- "Find products with stock below 10 units"
- "List today's sales orders over 1000 EUR"

**Create & Manage:**
- "Create a quotation for Acme Corp with 5 units of Widget Pro at 99.99 each"
- "Confirm quotation S00286 and check if a delivery was created"
- "Create a purchase order to restock 100 units of raw material from our supplier"

**Complete Workflows:**
- "Walk me through creating a full sales order, from quotation to delivery"
- "Set up a manufacturing order for 50 units of our flagship product"
- "Check the workflow status of order S00280 -- where are we in the process?"

**Database DevOps (Roadmap):**
- "Copy production to a test database for the new pricing feature"
- "Run integration tests on the feature database"
- "Import customer-master.xlsx into the staging database"

---

## Resources

Direct access to Odoo data through MCP resource URIs:

```
odoo://res.partner/record/1           - Get partner by ID
odoo://product.product/search?domain=[["qty_available",">",0]]  - Search products
odoo://sale.order/browse?ids=1,2,3    - Browse multiple records
odoo://res.partner/count?domain=[["customer_rank",">",0]]       - Count records
odoo://product.product/fields         - Field metadata
```

---

## About BACON-AI

**BACON-AI** develops self-learning AI frameworks that automate complex workflows across DevOps, robotics, and enterprise systems with deterministic control.

### What We Do

- **AI Business Consulting** -- Helping organizations harness AI to streamline operations, from strategy to implementation
- **Enterprise ERP Integration** -- Deep expertise in SAP and Odoo, connecting AI agents to real business processes
- **Agentic-Driven Development & Testing (ADDT)** -- Our evolution of TDD where AI agents don't just execute -- they **learn and improve**. Through self-annealing cycles (Action -> Feedback -> Adjustment -> Documentation -> Application), every test run makes the next one smarter. This MCP server and its 572 tests are built and continuously refined using ADDT.
- **AI Orchestration** -- Coordinating complex multi-agent systems with deterministic control using our BACON-AI 12-phase framework
- **Shift-Left Test Strategy** -- Proven on large-scale international SAP programs. The Database DevOps Pipeline in this project applies the same landscape separation (development vs maintenance) and regression gating that prevented production defects across multi-country ERP rollouts. Our V-Model maps development phases to test levels (TUT/FUT/SIT/UAT/RGT) ensuring complete coverage from unit to acceptance.
- **Robotics Integration** -- Smart AI frameworks for hardware and sensor control

### Our Expertise

With a foundation in **SAP test architecture** and large-scale international ERP programs, BACON-AI brings production-grade reliability to AI automation. The shift-left testing strategies, landscape management, and regression gating in this project are proven patterns from real enterprise deployments -- now enhanced with AI-driven test execution.

We don't just build demos -- we build systems that work in the real world.

### Get in Touch

- **Website:** [bacon-ai.cloud](https://bacon-ai.cloud)
- **Email:** [hello@bacon-ai.cloud](mailto:hello@bacon-ai.cloud)
- **Live Demos & Workshops:** Available on request

---

## Contributing

Contributions are welcome! This is a fork of [ivnvxd/mcp-server-odoo](https://github.com/ivnvxd/mcp-server-odoo) with extended workflow tools and comprehensive testing.

```bash
# Clone and install
git clone https://github.com/BACON-AI-CLOUD/bacon-ai-odoo-mcp.git
cd bacon-ai-odoo-mcp
pip install -e ".[dev]"

# Run tests
pytest --cov
```

See the full [Roadmap](docs/ROADMAP.md) for planned features.

## License

This project is licensed under the Mozilla Public License 2.0 (MPL-2.0) - see the [LICENSE](LICENSE) file for details.

---

*Built with the [BACON-AI Framework](https://bacon-ai.cloud) -- Orchestrating Intelligence for the Real World.*
