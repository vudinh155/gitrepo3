# Mô Hình Dữ Liệu

> **MangoAds Agency ERP - SRS v1.0**
> Phần 3: ERD, Database Schema

---

## 1. Entity Relationship Diagram (ERD)

### 1.1. ERD Tổng Quan

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           MANGOADS ERP - ERD                                │
└─────────────────────────────────────────────────────────────────────────────┘

┌──────────────┐       ┌──────────────┐       ┌──────────────┐
│   CLIENT     │       │   CONTRACT   │       │    SCOPE     │
├──────────────┤       ├──────────────┤       ├──────────────┤
│ PK client_id │◄──────│ FK client_id │       │ FK contract_id│◄──┐
│    name      │   1:N │ PK contract_id│◄──────│ PK scope_id  │   │
│    email     │       │    code      │   1:N │    code      │   │
│    phone     │       │    name      │       │    type      │   │
│    address   │       │    value     │       │    channel   │   │
│    tax_code  │       │    start_date│       │    budget    │   │
└──────────────┘       │    end_date  │       │    revenue   │   │
                       │    margin_tgt│       │    kpi_target│   │
                       │    status    │       │    status    │   │
                       └──────────────┘       └──────────────┘   │
                                                     │           │
                              ┌───────────────────────┼───────────┘
                              │                       │
                              ▼                       ▼
                       ┌──────────────┐       ┌──────────────┐
                       │   CAMPAIGN   │       │  MILESTONE   │
                       ├──────────────┤       ├──────────────┤
                       │ FK scope_id  │       │ FK scope_id  │
                       │ PK campaign_id│       │ PK milestone_id│
                       │    name      │       │    phase     │
                       │    budget    │       │    kpi_target│
                       │    kpi_target│       │    amount    │
                       │    start_date│       │    due_date  │
                       │    end_date  │       │    status    │
                       │    status    │       └──────────────┘
                       └──────────────┘              │
                              │                      │
                              ▼                      ▼
                       ┌──────────────┐       ┌──────────────┐
                       │ CAMPAIGN_DATA│       │   INVOICE    │
                       ├──────────────┤       ├──────────────┤
                       │ FK campaign_id│       │ FK milestone_id│
                       │    date      │       │ PK invoice_id│
                       │    spend     │       │    number    │
                       │    kpi_value │       │    amount    │
                       │    platform  │       │    issue_date│
                       └──────────────┘       │    due_date  │
                                              │    status    │
                                              └──────────────┘

┌──────────────┐       ┌──────────────┐       ┌──────────────┐
│    VENDOR    │       │VENDOR_ASSIGN │       │VENDOR_PAYMENT│
├──────────────┤       ├──────────────┤       ├──────────────┤
│ PK vendor_id │◄──────│ FK vendor_id │       │FK assignment_id│
│    name      │   1:N │ FK scope_id  │◄──────│ PK payment_id│
│    email     │       │PK assignment_id│  1:N │    amount    │
│    phone     │       │    role      │       │    due_date  │
│    tax_code  │       │    cost      │       │    paid_date │
│    bank_info │       │    terms     │       │    status    │
└──────────────┘       └──────────────┘       └──────────────┘

┌──────────────┐       ┌──────────────┐
│     USER     │       │  AUDIT_LOG   │
├──────────────┤       ├──────────────┤
│ PK user_id   │       │ PK log_id    │
│    email     │       │ FK user_id   │
│    name      │       │    entity    │
│    role      │       │    entity_id │
│    password  │       │    action    │
│    status    │       │    old_value │
└──────────────┘       │    new_value │
                       │    timestamp │
                       └──────────────┘
```

---

## 2. Database Schema (PostgreSQL)

### 2.1. Table: clients

```sql
CREATE TABLE clients (
    client_id       UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_code     VARCHAR(50) UNIQUE NOT NULL,
    client_name     VARCHAR(255) NOT NULL,
    email           VARCHAR(255),
    phone           VARCHAR(50),
    address         TEXT,
    tax_code        VARCHAR(50),
    contact_person  VARCHAR(255),
    industry        VARCHAR(100),
    notes           TEXT,
    status          VARCHAR(20) DEFAULT 'active',
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_by      UUID REFERENCES users(user_id),

    CONSTRAINT chk_client_status CHECK (status IN ('active', 'inactive'))
);

CREATE INDEX idx_clients_code ON clients(client_code);
CREATE INDEX idx_clients_status ON clients(status);
```

### 2.2. Table: contracts

```sql
CREATE TABLE contracts (
    contract_id     UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    contract_code   VARCHAR(50) UNIQUE NOT NULL,
    contract_name   VARCHAR(255) NOT NULL,
    client_id       UUID NOT NULL REFERENCES clients(client_id),
    start_date      DATE NOT NULL,
    end_date        DATE NOT NULL,
    total_value     DECIMAL(18,2) NOT NULL,
    currency        VARCHAR(3) DEFAULT 'VND',
    total_kpi       INTEGER,
    kpi_unit        VARCHAR(50),
    margin_target   DECIMAL(5,2),
    pm_id           UUID REFERENCES users(user_id),
    status          VARCHAR(20) DEFAULT 'draft',
    notes           TEXT,
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_by      UUID REFERENCES users(user_id),

    CONSTRAINT chk_contract_dates CHECK (end_date > start_date),
    CONSTRAINT chk_contract_value CHECK (total_value > 0),
    CONSTRAINT chk_contract_status CHECK (
        status IN ('draft', 'active', 'completed', 'cancelled')
    ),
    CONSTRAINT chk_contract_currency CHECK (currency IN ('VND', 'USD'))
);

CREATE INDEX idx_contracts_client ON contracts(client_id);
CREATE INDEX idx_contracts_status ON contracts(status);
CREATE INDEX idx_contracts_dates ON contracts(start_date, end_date);
```

### 2.3. Table: scopes

```sql
CREATE TABLE scopes (
    scope_id        UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    scope_code      VARCHAR(50) NOT NULL,
    scope_name      VARCHAR(255) NOT NULL,
    contract_id     UUID NOT NULL REFERENCES contracts(contract_id),
    scope_type      VARCHAR(50) NOT NULL,
    channel         VARCHAR(50),
    budget          DECIMAL(18,2) NOT NULL,
    revenue         DECIMAL(18,2) NOT NULL,
    kpi_target      INTEGER NOT NULL,
    kpi_unit        VARCHAR(50) NOT NULL,
    unit_price      DECIMAL(18,4),
    cost_estimate   DECIMAL(18,2),
    margin_target   DECIMAL(5,2),
    start_date      DATE NOT NULL,
    end_date        DATE NOT NULL,
    attributes      JSONB,
    status          VARCHAR(20) DEFAULT 'active',
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_by      UUID REFERENCES users(user_id),

    CONSTRAINT chk_scope_type CHECK (
        scope_type IN ('ADS_FACEBOOK', 'ADS_TIKTOK', 'ADS_GOOGLE',
                       'WEB', 'APP', 'HOSTING', 'OTHER')
    ),
    CONSTRAINT chk_scope_budget CHECK (budget > 0),
    CONSTRAINT chk_scope_revenue CHECK (revenue >= budget),
    CONSTRAINT chk_scope_kpi CHECK (kpi_target > 0),
    CONSTRAINT chk_scope_dates CHECK (end_date > start_date),
    CONSTRAINT chk_scope_status CHECK (status IN ('active', 'paused', 'completed')),
    CONSTRAINT uq_scope_code_contract UNIQUE (contract_id, scope_code)
);

CREATE INDEX idx_scopes_contract ON scopes(contract_id);
CREATE INDEX idx_scopes_type ON scopes(scope_type);
CREATE INDEX idx_scopes_status ON scopes(status);
```

### 2.4. Table: campaigns

```sql
CREATE TABLE campaigns (
    campaign_id     UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    campaign_name   VARCHAR(500) NOT NULL,
    scope_id        UUID NOT NULL REFERENCES scopes(scope_id),
    budget          DECIMAL(18,2) NOT NULL,
    kpi_target      INTEGER NOT NULL,
    start_date      DATE NOT NULL,
    end_date        DATE NOT NULL,
    pricing_model   VARCHAR(20),
    break_even_cost DECIMAL(18,4),
    profit_target   DECIMAL(18,2),
    -- Parsed from naming convention
    parsed_client   VARCHAR(100),
    parsed_contract VARCHAR(100),
    parsed_scope    VARCHAR(100),
    parsed_channel  VARCHAR(50),
    parsed_objective VARCHAR(100),
    parsed_segment  VARCHAR(100),
    parsed_phase    VARCHAR(50),
    status          VARCHAR(20) DEFAULT 'active',
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT chk_campaign_budget CHECK (budget > 0),
    CONSTRAINT chk_campaign_kpi CHECK (kpi_target > 0),
    CONSTRAINT chk_campaign_pricing CHECK (
        pricing_model IN ('CPC', 'CPM', 'CPA', 'CPL', 'FIXED')
    ),
    CONSTRAINT chk_campaign_status CHECK (
        status IN ('active', 'paused', 'completed')
    )
);

CREATE INDEX idx_campaigns_scope ON campaigns(scope_id);
CREATE INDEX idx_campaigns_name ON campaigns(campaign_name);
CREATE INDEX idx_campaigns_status ON campaigns(status);
```

### 2.5. Table: campaign_data (Daily data from BigQuery)

```sql
CREATE TABLE campaign_data (
    data_id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    campaign_id     UUID REFERENCES campaigns(campaign_id),
    campaign_name   VARCHAR(500) NOT NULL,
    date            DATE NOT NULL,
    platform        VARCHAR(50) NOT NULL,
    spend           DECIMAL(18,2) DEFAULT 0,
    impressions     INTEGER DEFAULT 0,
    clicks          INTEGER DEFAULT 0,
    kpi_value       INTEGER DEFAULT 0,
    kpi_type        VARCHAR(50),
    cpc             DECIMAL(18,4),
    cpm             DECIMAL(18,4),
    ctr             DECIMAL(8,4),
    synced_at       TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT uq_campaign_data_date UNIQUE (campaign_name, date, platform)
);

CREATE INDEX idx_campaign_data_campaign ON campaign_data(campaign_id);
CREATE INDEX idx_campaign_data_date ON campaign_data(date);
CREATE INDEX idx_campaign_data_platform ON campaign_data(platform);
```

### 2.6. Table: milestones

```sql
CREATE TABLE milestones (
    milestone_id    UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    milestone_code  VARCHAR(50) NOT NULL,
    scope_id        UUID NOT NULL REFERENCES scopes(scope_id),
    phase           VARCHAR(50) NOT NULL,
    description     TEXT,
    kpi_target      INTEGER NOT NULL,
    amount          DECIMAL(18,2) NOT NULL,
    due_date        DATE NOT NULL,
    achieved_date   DATE,
    status          VARCHAR(20) DEFAULT 'pending',
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT chk_milestone_kpi CHECK (kpi_target > 0),
    CONSTRAINT chk_milestone_amount CHECK (amount > 0),
    CONSTRAINT chk_milestone_status CHECK (
        status IN ('pending', 'achieved', 'invoiced', 'paid', 'cancelled')
    ),
    CONSTRAINT uq_milestone_code_scope UNIQUE (scope_id, milestone_code)
);

CREATE INDEX idx_milestones_scope ON milestones(scope_id);
CREATE INDEX idx_milestones_status ON milestones(status);
CREATE INDEX idx_milestones_due_date ON milestones(due_date);
```

### 2.7. Table: invoices

```sql
CREATE TABLE invoices (
    invoice_id      UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    invoice_number  VARCHAR(50) UNIQUE NOT NULL,
    milestone_id    UUID NOT NULL REFERENCES milestones(milestone_id),
    amount          DECIMAL(18,2) NOT NULL,
    tax_amount      DECIMAL(18,2) DEFAULT 0,
    total_amount    DECIMAL(18,2) NOT NULL,
    issue_date      DATE NOT NULL,
    due_date        DATE NOT NULL,
    paid_date       DATE,
    status          VARCHAR(20) DEFAULT 'draft',
    notes           TEXT,
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_by      UUID REFERENCES users(user_id),

    CONSTRAINT chk_invoice_amount CHECK (amount > 0),
    CONSTRAINT chk_invoice_status CHECK (
        status IN ('draft', 'sent', 'paid', 'overdue', 'cancelled')
    )
);

CREATE INDEX idx_invoices_milestone ON invoices(milestone_id);
CREATE INDEX idx_invoices_status ON invoices(status);
CREATE INDEX idx_invoices_due_date ON invoices(due_date);
```

### 2.8. Table: vendors

```sql
CREATE TABLE vendors (
    vendor_id       UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    vendor_code     VARCHAR(50) UNIQUE NOT NULL,
    vendor_name     VARCHAR(255) NOT NULL,
    email           VARCHAR(255),
    phone           VARCHAR(50),
    address         TEXT,
    tax_code        VARCHAR(50),
    bank_name       VARCHAR(255),
    bank_account    VARCHAR(50),
    bank_branch     VARCHAR(255),
    service_type    VARCHAR(100),
    status          VARCHAR(20) DEFAULT 'active',
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT chk_vendor_status CHECK (status IN ('active', 'inactive'))
);

CREATE INDEX idx_vendors_code ON vendors(vendor_code);
CREATE INDEX idx_vendors_status ON vendors(status);
```

### 2.9. Table: vendor_assignments

```sql
CREATE TABLE vendor_assignments (
    assignment_id   UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    vendor_id       UUID NOT NULL REFERENCES vendors(vendor_id),
    scope_id        UUID NOT NULL REFERENCES scopes(scope_id),
    role            VARCHAR(100) NOT NULL,
    description     TEXT,
    cost            DECIMAL(18,2) NOT NULL,
    payment_terms   VARCHAR(255),
    start_date      DATE,
    end_date        DATE,
    status          VARCHAR(20) DEFAULT 'active',
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT chk_assignment_cost CHECK (cost > 0),
    CONSTRAINT chk_assignment_status CHECK (
        status IN ('active', 'completed', 'cancelled')
    )
);

CREATE INDEX idx_vendor_assignments_vendor ON vendor_assignments(vendor_id);
CREATE INDEX idx_vendor_assignments_scope ON vendor_assignments(scope_id);
```

### 2.10. Table: vendor_payments

```sql
CREATE TABLE vendor_payments (
    payment_id      UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    assignment_id   UUID NOT NULL REFERENCES vendor_assignments(assignment_id),
    amount          DECIMAL(18,2) NOT NULL,
    due_date        DATE NOT NULL,
    paid_date       DATE,
    payment_method  VARCHAR(50),
    reference       VARCHAR(255),
    status          VARCHAR(20) DEFAULT 'pending',
    notes           TEXT,
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT chk_vendor_payment_amount CHECK (amount > 0),
    CONSTRAINT chk_vendor_payment_status CHECK (
        status IN ('pending', 'paid', 'overdue', 'cancelled')
    )
);

CREATE INDEX idx_vendor_payments_assignment ON vendor_payments(assignment_id);
CREATE INDEX idx_vendor_payments_status ON vendor_payments(status);
CREATE INDEX idx_vendor_payments_due_date ON vendor_payments(due_date);
```

### 2.11. Table: users

```sql
CREATE TABLE users (
    user_id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email           VARCHAR(255) UNIQUE NOT NULL,
    password_hash   VARCHAR(255) NOT NULL,
    full_name       VARCHAR(255) NOT NULL,
    role            VARCHAR(50) NOT NULL,
    department      VARCHAR(100),
    phone           VARCHAR(50),
    status          VARCHAR(20) DEFAULT 'active',
    last_login      TIMESTAMP,
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT chk_user_role CHECK (
        role IN ('director', 'finance', 'accountant', 'pm', 'ads_team', 'viewer')
    ),
    CONSTRAINT chk_user_status CHECK (status IN ('active', 'inactive'))
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_role ON users(role);
```

### 2.12. Table: audit_logs

```sql
CREATE TABLE audit_logs (
    log_id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         UUID REFERENCES users(user_id),
    entity_type     VARCHAR(50) NOT NULL,
    entity_id       UUID NOT NULL,
    action          VARCHAR(50) NOT NULL,
    old_values      JSONB,
    new_values      JSONB,
    ip_address      VARCHAR(50),
    user_agent      TEXT,
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT chk_audit_action CHECK (
        action IN ('CREATE', 'UPDATE', 'DELETE', 'STATUS_CHANGE')
    )
);

CREATE INDEX idx_audit_logs_entity ON audit_logs(entity_type, entity_id);
CREATE INDEX idx_audit_logs_user ON audit_logs(user_id);
CREATE INDEX idx_audit_logs_created ON audit_logs(created_at);
```

---

## 3. Views

### 3.1. View: scope_summary

```sql
CREATE VIEW v_scope_summary AS
SELECT
    s.scope_id,
    s.scope_code,
    s.scope_name,
    s.scope_type,
    s.channel,
    c.contract_code,
    cl.client_name,
    s.budget,
    s.revenue,
    s.kpi_target,
    COALESCE(SUM(cd.spend), 0) AS total_spend,
    COALESCE(SUM(cd.kpi_value), 0) AS kpi_achieved,
    s.budget - COALESCE(SUM(cd.spend), 0) AS budget_remaining,
    s.kpi_target - COALESCE(SUM(cd.kpi_value), 0) AS kpi_remaining,
    s.revenue - COALESCE(SUM(cd.spend), 0) - COALESCE(va.vendor_cost, 0) AS profit,
    CASE
        WHEN s.revenue > 0
        THEN (s.revenue - COALESCE(SUM(cd.spend), 0) - COALESCE(va.vendor_cost, 0)) / s.revenue * 100
        ELSE 0
    END AS margin_pct
FROM scopes s
JOIN contracts c ON s.contract_id = c.contract_id
JOIN clients cl ON c.client_id = cl.client_id
LEFT JOIN campaigns cam ON cam.scope_id = s.scope_id
LEFT JOIN campaign_data cd ON cd.campaign_id = cam.campaign_id
LEFT JOIN (
    SELECT scope_id, SUM(cost) AS vendor_cost
    FROM vendor_assignments
    GROUP BY scope_id
) va ON va.scope_id = s.scope_id
GROUP BY s.scope_id, s.scope_code, s.scope_name, s.scope_type, s.channel,
         c.contract_code, cl.client_name, s.budget, s.revenue, s.kpi_target, va.vendor_cost;
```

### 3.2. View: cashflow_forecast

```sql
CREATE VIEW v_cashflow_forecast AS
-- Cash In (từ milestones)
SELECT
    DATE_TRUNC('month', m.due_date) AS month,
    'CASH_IN' AS flow_type,
    SUM(m.amount) AS amount,
    'Milestone payments' AS description
FROM milestones m
WHERE m.status IN ('pending', 'achieved')
GROUP BY DATE_TRUNC('month', m.due_date)

UNION ALL

-- Cash Out (từ vendor payments)
SELECT
    DATE_TRUNC('month', vp.due_date) AS month,
    'CASH_OUT' AS flow_type,
    SUM(vp.amount) AS amount,
    'Vendor payments' AS description
FROM vendor_payments vp
WHERE vp.status = 'pending'
GROUP BY DATE_TRUNC('month', vp.due_date)

UNION ALL

-- Cash Out (từ ads spend dự kiến)
SELECT
    DATE_TRUNC('month', CURRENT_DATE + INTERVAL '1 month' * generate_series(0,2)) AS month,
    'CASH_OUT' AS flow_type,
    SUM(s.budget - COALESCE(spent.total, 0)) / 3 AS amount,
    'Estimated ads spend' AS description
FROM scopes s
LEFT JOIN (
    SELECT cam.scope_id, SUM(cd.spend) AS total
    FROM campaigns cam
    JOIN campaign_data cd ON cam.campaign_id = cd.campaign_id
    GROUP BY cam.scope_id
) spent ON spent.scope_id = s.scope_id
WHERE s.status = 'active'
GROUP BY 1;
```

---

## 4. Stored Procedures

### 4.1. Procedure: Check Milestone Achievement

```sql
CREATE OR REPLACE FUNCTION check_milestone_achievement()
RETURNS void AS $$
DECLARE
    rec RECORD;
BEGIN
    FOR rec IN
        SELECT
            m.milestone_id,
            m.kpi_target,
            COALESCE(SUM(cd.kpi_value), 0) AS kpi_achieved
        FROM milestones m
        JOIN scopes s ON m.scope_id = s.scope_id
        LEFT JOIN campaigns cam ON cam.scope_id = s.scope_id
        LEFT JOIN campaign_data cd ON cd.campaign_id = cam.campaign_id
        WHERE m.status = 'pending'
        GROUP BY m.milestone_id, m.kpi_target
    LOOP
        IF rec.kpi_achieved >= rec.kpi_target THEN
            UPDATE milestones
            SET status = 'achieved',
                achieved_date = CURRENT_DATE,
                updated_at = CURRENT_TIMESTAMP
            WHERE milestone_id = rec.milestone_id;
        END IF;
    END LOOP;
END;
$$ LANGUAGE plpgsql;
```

### 4.2. Procedure: Calculate Scope Profit

```sql
CREATE OR REPLACE FUNCTION calculate_scope_profit(p_scope_id UUID)
RETURNS TABLE (
    revenue DECIMAL,
    ads_cost DECIMAL,
    vendor_cost DECIMAL,
    total_cost DECIMAL,
    profit DECIMAL,
    margin_pct DECIMAL
) AS $$
BEGIN
    RETURN QUERY
    SELECT
        s.revenue,
        COALESCE(SUM(cd.spend), 0) AS ads_cost,
        COALESCE(va.vendor_cost, 0) AS vendor_cost,
        COALESCE(SUM(cd.spend), 0) + COALESCE(va.vendor_cost, 0) AS total_cost,
        s.revenue - COALESCE(SUM(cd.spend), 0) - COALESCE(va.vendor_cost, 0) AS profit,
        CASE
            WHEN s.revenue > 0
            THEN (s.revenue - COALESCE(SUM(cd.spend), 0) - COALESCE(va.vendor_cost, 0)) / s.revenue * 100
            ELSE 0
        END AS margin_pct
    FROM scopes s
    LEFT JOIN campaigns cam ON cam.scope_id = s.scope_id
    LEFT JOIN campaign_data cd ON cd.campaign_id = cam.campaign_id
    LEFT JOIN (
        SELECT scope_id, SUM(cost) AS vendor_cost
        FROM vendor_assignments
        GROUP BY scope_id
    ) va ON va.scope_id = s.scope_id
    WHERE s.scope_id = p_scope_id
    GROUP BY s.scope_id, s.revenue, va.vendor_cost;
END;
$$ LANGUAGE plpgsql;
```

---

## 5. Data Dictionary

### 5.1. Enums Summary

| Enum Name | Values |
|-----------|--------|
| contract_status | draft, active, completed, cancelled |
| scope_type | ADS_FACEBOOK, ADS_TIKTOK, ADS_GOOGLE, WEB, APP, HOSTING, OTHER |
| scope_status | active, paused, completed |
| campaign_status | active, paused, completed |
| milestone_status | pending, achieved, invoiced, paid, cancelled |
| invoice_status | draft, sent, paid, overdue, cancelled |
| payment_status | pending, paid, overdue, cancelled |
| user_role | director, finance, accountant, pm, ads_team, viewer |

### 5.2. Key Relationships

| Parent | Child | Relationship | On Delete |
|--------|-------|--------------|-----------|
| clients | contracts | 1:N | RESTRICT |
| contracts | scopes | 1:N | CASCADE |
| scopes | campaigns | 1:N | CASCADE |
| scopes | milestones | 1:N | CASCADE |
| scopes | vendor_assignments | 1:N | CASCADE |
| campaigns | campaign_data | 1:N | CASCADE |
| milestones | invoices | 1:1 | RESTRICT |
| vendors | vendor_assignments | 1:N | RESTRICT |
| vendor_assignments | vendor_payments | 1:N | CASCADE |

---

**Tiếp theo:** [04 - Luồng Nghiệp Vụ](./04-luong-nghiep-vu.md)

---

*MangoAds Agency ERP - SRS v1.0*
