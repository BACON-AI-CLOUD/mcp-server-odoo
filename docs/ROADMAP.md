# BACON-AI Odoo MCP Server - Roadmap

**Maintained by:** [BACON-AI](https://bacon-ai.cloud) | [hello@bacon-ai.cloud](mailto:hello@bacon-ai.cloud)

---

## Current State (v0.3.0 - February 2026)

- 17 MCP tools (7 core CRUD + 10 workflow)
- YOLO mode (no module install required)
- 572 tests passing (153 against live Odoo 19.0)
- Single database, static configuration
- PyPI distribution (`pip install mcp-server-odoo`)

---

## Phase 1: Dynamic Database Authentication (Next)

**Problem:** The MCP server is statically configured with one database at startup. Users cannot switch databases or query multiple Odoo instances without restarting.

**Solution:** Multi-database credential management with interactive authentication.

### Features

#### 1.1 Dynamic Database Switching
- `switch_database` tool: Change active database without restart
- `list_databases` tool: Discover all available databases on the Odoo server
- Connection pooling: Cache authenticated connections for fast switching
- Session-aware: Remember which database the user is working with

#### 1.2 Credential Store
- Encrypted per-database credential storage (`~/.config/bacon-ai-odoo/credentials.json`)
- Auto-prompt flow: "No credentials found for 'demo'. Please provide username and password."
- "Save for next time?" confirmation
- Per-database access levels: read-only, full, admin
- Credential verification on save (test connection before storing)

#### 1.3 Multi-Instance Support
- Configure multiple Odoo servers (not just databases)
- Named connections: "production", "staging", "dev"
- Quick-switch between environments

### User Experience

```
User: "Show me the top 10 customers from the demo database"

MCP Server:
  1. Check credential store for "demo" → not found
  2. Prompt: "No saved credentials for database 'demo'.
     Username?" → admin
     "Password?" → ****
  3. Test connection → success
  4. "Save credentials for 'demo' for next time?" → Yes
  5. Execute query and return results

Next time:
  1. Check credential store for "demo" → found
  2. Connect automatically
  3. Execute query
```

---

## Phase 2: Database DevOps Pipeline

**Problem:** Testing against production data creates dummy transactions. Features developed in isolation cannot be regression-tested together before production deployment.

**Solution:** GitFlow-inspired database lifecycle management that mirrors branch-based development.

### Architecture

```
Production DB (main)
    │
    ├── Pre-Production DB (develop) ← regression testing gate
    │   ├── SIT1_prod_feature-sales-report     ← feature branch copy
    │   ├── SIT1_prod_feature-new-workflow      ← feature branch copy
    │   └── SIT1_prod_feature-inventory-fix     ← feature branch copy
    │
    └── Migration staging (release/v2.0) ← cut-over from legacy
```

### Features

#### 2.1 Database Copy for Testing
- `copy_database` tool: Duplicate any database for isolated testing
- Naming convention: `SIT1_<source>_<feature>_<YYYYMMDD>`
- Auto-cleanup: List and remove stale test databases
- Size awareness: Warn if production database is very large before copying

```
User: "Copy production to a test database for the new pricing feature"

MCP Server:
  1. Calls Odoo duplicate_database("production", "SIT1_production_pricing_20260211")
  2. Switches active connection to new database
  3. "Database copied. You're now working on SIT1_production_pricing_20260211.
     All changes are isolated from production."
```

#### 2.2 Database Promotion Pipeline
- `promote_database` tool: Apply tested changes from feature DB to next stage
- Pipeline stages: Feature → Pre-Production → Production
- Gate checks: "Have SIT tests passed on this database?"
- Audit trail: Log all promotions with who/when/what

```
Feature Database (tested)
    → Pre-Production (regression test)
        → Production (controlled deployment)
```

#### 2.3 Automated SIT on Copied Databases
- `run_sit_tests` tool: Execute the full SIT suite against any database
- Compare results across databases (feature vs production baseline)
- Regression detection: Flag tests that pass on production but fail on feature branch
- Generate test reports with evidence

#### 2.4 Database Cleanup
- `drop_database` tool: Remove test databases when no longer needed
- `list_test_databases` tool: Show all SIT copies with creation date and size
- Auto-cleanup policy: Remove databases older than N days
- Protection: Never allow dropping production or pre-production

### Database Promotion Workflow

```
Step 1: Developer starts feature
  "Copy production to SIT1_prod_feature-A"
  → Isolated copy with real data

Step 2: Develop and test
  Run workflows, create test data, verify behavior
  → All changes contained in SIT1 copy

Step 3: Run SIT tests
  "Run integration tests on SIT1_prod_feature-A"
  → 84 SIT tests execute against feature database
  → Generate pass/fail report

Step 4: Promote to pre-production
  "Promote feature-A to pre-production"
  → Apply operations to pre-prod database
  → Run regression tests

Step 5: Regression gate
  "Run full regression on pre-production"
  → All 572 tests against merged pre-prod
  → Catch conflicts between features

Step 6: Production deployment
  "Deploy to production"
  → Controlled, audited, confirmed
  → Rollback plan documented
```

---

## Phase 3: Data Import/Export & Migration Tools

**Problem:** Setting up new database instances, migrating from legacy systems, and managing cut-over requires manual data loading. AI assistants could automate this but need structured import/export tools.

**Solution:** AI-assisted data migration tools with validation, dry-run, and rollback capabilities.

### Features

#### 3.1 Data Import
- `import_data` tool: Load records from CSV, XML, or Excel files
- Field mapping: AI-assisted column-to-field mapping
- Validation: Check required fields, data types, foreign key references
- Dry-run mode: Show what would be created/updated without executing
- Batch processing: Handle large files with progress reporting
- Rollback: Track imported record IDs for easy reversal

```
User: "Read the customer-master.xlsx spreadsheet and upload those
       customers to the customer master"

MCP Server:
  1. Read Excel file → parse 500 rows
  2. Map columns: "Company Name" → name, "Email" → email, etc.
  3. Validate: 3 rows missing required fields, 2 duplicate emails
  4. "Found 500 customers. 495 valid, 5 issues. Run dry-run first?"
  5. Dry-run: "Would create 480 new, update 15 existing"
  6. "Proceed with import?" → Yes
  7. Import with progress: 100/500... 250/500... 500/500
  8. "Imported 495 customers. 5 skipped (see error report).
     Rollback available for 24 hours."
```

#### 3.2 Data Export
- `export_data` tool: Extract records to CSV/JSON/Excel
- Filtered exports: Use domain filters to select records
- Related data: Follow relationships (e.g., customer + their orders)
- Scheduled exports: Set up recurring data extracts

#### 3.3 Migration Toolkit
- `compare_databases` tool: Diff two databases (e.g., legacy vs Odoo)
- `migration_plan` tool: Generate import sequence respecting dependencies
- `validate_migration` tool: Post-migration data integrity checks
- Cut-over checklists: Guided migration workflow

### Migration Use Cases

| Scenario | Tools Used |
|----------|-----------|
| Legacy system cut-over | import_data, validate_migration, compare_databases |
| New instance setup | import_data (master data: customers, products, vendors) |
| Database consolidation | export_data + import_data across instances |
| Periodic data sync | export_data + import_data with scheduling |
| Disaster recovery | export_data for backup, import_data for restore |

---

## Phase 4: Production Safety & Audit

**Problem:** Production database access is necessary but dangerous. Need tiered safety controls and complete audit trails.

**Solution:** Production access modes with confirmation gates, audit logging, and rollback capabilities.

### Features

#### 4.1 Tiered Access Modes

| Mode | Operations Allowed | Safety Controls |
|------|-------------------|-----------------|
| **Observer** | Read, search, count, export | No confirmation needed |
| **Operator** | + Create, update (business data) | Confirmation per operation |
| **Importer** | + Bulk import, data loading | Dry-run required, rollback available |
| **Admin** | + Delete, database operations | Dual confirmation, audit log |

#### 4.2 Audit Trail
- Log every write operation to production
- Who, when, what model, what changed
- Searchable audit history
- Export audit logs for compliance

#### 4.3 Rollback Support
- Track all changes made in a session
- "Undo last operation" capability
- Session-level rollback (undo everything since last checkpoint)
- Time-limited rollback window (24 hours)

#### 4.4 Confirmation Gates
- Natural language confirmation: "This will create 500 customer records in PRODUCTION. Type 'confirm' to proceed."
- Preview mode: Show exactly what will change before executing
- Rate limiting: Slow down bulk operations in production

---

## Phase 5: Advanced Features

### 5.1 Docker Support
- Official Docker image for easy deployment
- Docker Compose with Odoo + MCP server
- CI/CD pipeline integration

### 5.2 MCP Resources & Prompts
- Expose Odoo dashboards as MCP resources
- Pre-built prompts for common business queries
- Template-based report generation

### 5.3 Real-Time Notifications
- Subscribe to Odoo events (new order, inventory change)
- Push notifications to AI assistant
- Webhook integration

### 5.4 Multi-Tenant Support
- Manage multiple Odoo instances from one MCP server
- Tenant isolation and security
- Cross-instance reporting

---

## Competitive Advantage

This roadmap positions BACON-AI Odoo MCP as the only server suitable for:

| Capability | BACON-AI | All Others |
|-----------|----------|------------|
| Business Workflow Automation | 10 tools | None |
| Dynamic Multi-Database | Planned (Phase 1) | None |
| Database DevOps Pipeline | Planned (Phase 2) | None |
| AI-Assisted Data Migration | Planned (Phase 3) | None |
| Production Safety Controls | Planned (Phase 4) | None |
| Enterprise Test Coverage | 572 tests | Minimal |

No other Odoo MCP server addresses the enterprise database lifecycle. This is the gap between "demo tool" and "production-grade platform."

---

*Built with the [BACON-AI Framework](https://bacon-ai.cloud) -- Orchestrating Intelligence for the Real World.*
