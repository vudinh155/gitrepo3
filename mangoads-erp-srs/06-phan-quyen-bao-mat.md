# Phân Quyền và Bảo Mật

> **MangoAds Agency ERP - SRS v1.0**
> Phần 6: Role-Based Access Control & Security

---

## 1. Ma Trận Phân Quyền

### 1.1. Roles & Permissions Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    ROLE-BASED ACCESS CONTROL                           │
└─────────────────────────────────────────────────────────────────────────┘

┌──────────────┬──────────────────────────────────────────────────────────┐
│     ROLE     │                    PERMISSIONS                          │
├──────────────┼──────────────────────────────────────────────────────────┤
│              │                                                          │
│   DIRECTOR   │  ████████████████████████████████████████  FULL ACCESS  │
│              │                                                          │
├──────────────┼──────────────────────────────────────────────────────────┤
│              │                                                          │
│   FINANCE    │  ████████████████████████████░░░░░░░░░░░░  FINANCIAL    │
│              │                                                          │
├──────────────┼──────────────────────────────────────────────────────────┤
│              │                                                          │
│  ACCOUNTANT  │  ████████████████░░░░░░░░░░░░░░░░░░░░░░░░  CONTRACT     │
│              │                                                          │
├──────────────┼──────────────────────────────────────────────────────────┤
│              │                                                          │
│      PM      │  ██████████████████████░░░░░░░░░░░░░░░░░░  PROJECT      │
│              │                                                          │
├──────────────┼──────────────────────────────────────────────────────────┤
│              │                                                          │
│  ADS_TEAM    │  ████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  VIEW ONLY    │
│              │                                                          │
├──────────────┼──────────────────────────────────────────────────────────┤
│              │                                                          │
│   VIEWER     │  ████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  READ ONLY    │
│              │                                                          │
└──────────────┴──────────────────────────────────────────────────────────┘
```

### 1.2. Chi Tiết Quyền Theo Module

#### Module: Contracts

| Action | Director | Finance | Accountant | PM | Ads Team | Viewer |
|--------|:--------:|:-------:|:----------:|:--:|:--------:|:------:|
| View List | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| View Detail | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| Create | ✅ | ❌ | ✅ | ❌ | ❌ | ❌ |
| Edit | ✅ | ❌ | ✅ | ❌ | ❌ | ❌ |
| Delete | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ |
| Approve | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ |
| View Financial | ✅ | ✅ | ✅ | ❌ | ❌ | ❌ |

#### Module: Scopes

| Action | Director | Finance | Accountant | PM | Ads Team | Viewer |
|--------|:--------:|:-------:|:----------:|:--:|:--------:|:------:|
| View List | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| View Detail | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Create | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ |
| Edit | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ |
| Edit Budget | ✅ | ✅ | ❌ | ✅* | ❌ | ❌ |
| View Profit | ✅ | ✅ | ❌ | ✅ | ❌ | ❌ |

*PM chỉ được edit budget trong phạm vi contract

#### Module: Campaigns

| Action | Director | Finance | Accountant | PM | Ads Team | Viewer |
|--------|:--------:|:-------:|:----------:|:--:|:--------:|:------:|
| View List | ✅ | ✅ | ❌ | ✅ | ✅ | ✅ |
| View Detail | ✅ | ✅ | ❌ | ✅ | ✅ | ✅ |
| Import | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ |
| View Budget Status | ✅ | ✅ | ❌ | ✅ | ✅ | ❌ |

#### Module: Milestones & Invoices

| Action | Director | Finance | Accountant | PM | Ads Team | Viewer |
|--------|:--------:|:-------:|:----------:|:--:|:--------:|:------:|
| View Milestones | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| Create Milestone | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ |
| Edit Milestone | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ |
| Create Invoice | ✅ | ✅ | ✅ | ❌ | ❌ | ❌ |
| View Invoice | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| Mark Paid | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |

#### Module: Vendors

| Action | Director | Finance | Accountant | PM | Ads Team | Viewer |
|--------|:--------:|:-------:|:----------:|:--:|:--------:|:------:|
| View List | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| Create | ✅ | ✅ | ❌ | ✅ | ❌ | ❌ |
| Edit | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| Create Assignment | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ |
| Record Payment | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |

#### Module: Reports & Dashboard

| Action | Director | Finance | Accountant | PM | Ads Team | Viewer |
|--------|:--------:|:-------:|:----------:|:--:|:--------:|:------:|
| Overview Dashboard | ✅ | ✅ | ❌ | ✅ | ✅ | ✅ |
| Financial Dashboard | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| Cashflow Report | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| Profit Report | ✅ | ✅ | ❌ | ✅ | ❌ | ❌ |
| Export Reports | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |

---

## 2. Data Access Scope

### 2.1. Row-Level Security

```sql
-- Ví dụ: PM chỉ xem được contract mình phụ trách
CREATE POLICY pm_contract_access ON contracts
    FOR ALL
    TO pm_role
    USING (pm_id = current_user_id() OR current_user_role() = 'director');

-- Ads Team chỉ xem được scope được assign
CREATE POLICY ads_scope_access ON scopes
    FOR SELECT
    TO ads_team_role
    USING (
        scope_id IN (
            SELECT scope_id FROM scope_assignments
            WHERE user_id = current_user_id()
        )
    );
```

### 2.2. Field-Level Security

| Field | Director | Finance | Accountant | PM | Ads Team |
|-------|:--------:|:-------:|:----------:|:--:|:--------:|
| contract.total_value | ✅ | ✅ | ✅ | ❌ | ❌ |
| scope.revenue | ✅ | ✅ | ❌ | ✅ | ❌ |
| scope.profit | ✅ | ✅ | ❌ | ✅ | ❌ |
| vendor.bank_account | ✅ | ✅ | ❌ | ❌ | ❌ |
| client.tax_code | ✅ | ✅ | ✅ | ❌ | ❌ |

---

## 3. Permission Implementation

### 3.1. Permission Schema

```sql
CREATE TABLE permissions (
    permission_id UUID PRIMARY KEY,
    permission_code VARCHAR(100) UNIQUE NOT NULL,
    permission_name VARCHAR(255) NOT NULL,
    module VARCHAR(50) NOT NULL,
    description TEXT
);

CREATE TABLE role_permissions (
    role VARCHAR(50) NOT NULL,
    permission_id UUID NOT NULL REFERENCES permissions(permission_id),
    PRIMARY KEY (role, permission_id)
);

-- Sample permissions
INSERT INTO permissions (permission_id, permission_code, permission_name, module) VALUES
(gen_random_uuid(), 'contract.view', 'View Contracts', 'contract'),
(gen_random_uuid(), 'contract.create', 'Create Contract', 'contract'),
(gen_random_uuid(), 'contract.edit', 'Edit Contract', 'contract'),
(gen_random_uuid(), 'contract.delete', 'Delete Contract', 'contract'),
(gen_random_uuid(), 'contract.approve', 'Approve Contract', 'contract'),
(gen_random_uuid(), 'scope.view', 'View Scopes', 'scope'),
(gen_random_uuid(), 'scope.create', 'Create Scope', 'scope'),
(gen_random_uuid(), 'scope.edit_budget', 'Edit Scope Budget', 'scope'),
(gen_random_uuid(), 'scope.view_profit', 'View Scope Profit', 'scope'),
-- ... more permissions
```

### 3.2. Permission Check Middleware

```python
# permission_middleware.py

from functools import wraps
from flask import g, abort

def require_permission(permission_code):
    def decorator(f):
        @wraps(f)
        def decorated_function(*args, **kwargs):
            user = g.current_user

            if not user:
                abort(401, "Unauthorized")

            if not has_permission(user.role, permission_code):
                abort(403, f"Permission denied: {permission_code}")

            return f(*args, **kwargs)
        return decorated_function
    return decorator

def has_permission(role, permission_code):
    """
    Check if role has permission
    """
    # Director has all permissions
    if role == 'director':
        return True

    # Check database
    result = db.session.query(RolePermission).join(Permission).filter(
        RolePermission.role == role,
        Permission.permission_code == permission_code
    ).first()

    return result is not None

# Usage in route
@app.route('/api/v1/contracts', methods=['POST'])
@require_permission('contract.create')
def create_contract():
    # ... create contract logic
    pass
```

---

## 4. Audit & Logging

### 4.1. Audit Log Structure

```sql
CREATE TABLE audit_logs (
    log_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    user_id UUID REFERENCES users(user_id),
    user_email VARCHAR(255),
    user_role VARCHAR(50),
    action VARCHAR(50) NOT NULL,
    entity_type VARCHAR(50) NOT NULL,
    entity_id UUID NOT NULL,
    entity_name VARCHAR(255),
    old_values JSONB,
    new_values JSONB,
    ip_address VARCHAR(50),
    user_agent TEXT,
    request_id VARCHAR(100),
    status VARCHAR(20) DEFAULT 'success'
);

CREATE INDEX idx_audit_timestamp ON audit_logs(timestamp);
CREATE INDEX idx_audit_user ON audit_logs(user_id);
CREATE INDEX idx_audit_entity ON audit_logs(entity_type, entity_id);
```

### 4.2. Audit Events

| Event Category | Events |
|----------------|--------|
| **Authentication** | login, logout, failed_login, password_change |
| **Contract** | contract.create, contract.update, contract.delete, contract.approve |
| **Scope** | scope.create, scope.update, scope.budget_change |
| **Financial** | invoice.create, payment.record, budget.warning |
| **User** | user.create, user.update, user.role_change, user.deactivate |
| **Export** | report.export, data.export |

### 4.3. Sensitive Data Masking

```python
# audit_service.py

SENSITIVE_FIELDS = [
    'password',
    'bank_account',
    'tax_code',
    'total_value',  # Mask for certain roles
]

def mask_sensitive_data(data: dict, user_role: str) -> dict:
    """
    Mask sensitive fields in audit logs based on user role
    """
    masked = data.copy()

    for field in SENSITIVE_FIELDS:
        if field in masked:
            if field == 'password':
                masked[field] = '********'
            elif field in ['bank_account']:
                # Show last 4 digits only
                masked[field] = '****' + str(masked[field])[-4:]
            elif field == 'total_value' and user_role not in ['director', 'finance']:
                masked[field] = '[HIDDEN]'

    return masked
```

---

## 5. Security Policies

### 5.1. Password Policy

```yaml
password_policy:
  min_length: 12
  require_uppercase: true
  require_lowercase: true
  require_numbers: true
  require_special: true
  max_age_days: 90
  history_count: 5  # Cannot reuse last 5 passwords
  lockout_attempts: 5
  lockout_duration_minutes: 30
```

### 5.2. Session Policy

```yaml
session_policy:
  access_token_lifetime: 24h
  refresh_token_lifetime: 7d
  max_concurrent_sessions: 3
  idle_timeout: 30m
  require_mfa_for_roles:
    - director
    - finance
```

### 5.3. API Security

```yaml
api_security:
  rate_limiting:
    enabled: true
    requests_per_minute: 60
    burst_limit: 100

  cors:
    allowed_origins:
      - "https://app.mangoads.vn"
      - "https://admin.mangoads.vn"
    allowed_methods:
      - GET
      - POST
      - PUT
      - DELETE
    allowed_headers:
      - Authorization
      - Content-Type

  headers:
    X-Content-Type-Options: nosniff
    X-Frame-Options: DENY
    X-XSS-Protection: "1; mode=block"
    Strict-Transport-Security: "max-age=31536000; includeSubDomains"
```

---

## 6. Data Protection

### 6.1. Encryption

| Data Type | Encryption Method |
|-----------|-------------------|
| Passwords | bcrypt (cost 12) |
| API Keys | AES-256-GCM |
| Sensitive fields | AES-256-CBC |
| Data in transit | TLS 1.3 |
| Database backups | AES-256 |

### 6.2. Data Retention

```yaml
data_retention:
  audit_logs: 2 years
  deleted_contracts: 7 years (soft delete)
  campaign_data: 3 years
  user_sessions: 30 days
  export_files: 7 days
```

### 6.3. GDPR Compliance

```yaml
gdpr:
  data_subject_rights:
    - access
    - rectification
    - erasure
    - portability
    - restriction

  consent_tracking: true

  data_processing_log: true

  dpo_contact: "dpo@mangoads.vn"
```

---

## 7. Incident Response

### 7.1. Security Alerts

| Alert Level | Trigger | Response Time | Notification |
|-------------|---------|---------------|--------------|
| CRITICAL | Data breach, System compromise | Immediate | SMS + Email + Slack |
| HIGH | Multiple failed logins, Unusual access | 15 minutes | Email + Slack |
| MEDIUM | Permission violation, Policy breach | 1 hour | Email |
| LOW | Minor policy deviation | 24 hours | Dashboard |

### 7.2. Incident Response Flow

```
┌─────────────────┐
│  DETECT         │
│  (Automated/    │
│   Manual)       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  ANALYZE        │
│  - Scope        │
│  - Impact       │
│  - Root cause   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  CONTAIN        │
│  - Block access │
│  - Isolate      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  ERADICATE      │
│  - Fix vuln     │
│  - Patch        │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  RECOVER        │
│  - Restore      │
│  - Verify       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  LEARN          │
│  - Document     │
│  - Improve      │
└─────────────────┘
```

---

## 8. Compliance Checklist

### 8.1. Pre-Launch Security Checklist

- [ ] All endpoints require authentication
- [ ] Role-based access control implemented
- [ ] Sensitive data encrypted at rest
- [ ] TLS enabled for all connections
- [ ] SQL injection prevention
- [ ] XSS prevention
- [ ] CSRF tokens implemented
- [ ] Rate limiting configured
- [ ] Audit logging enabled
- [ ] Password policy enforced
- [ ] Session management secure
- [ ] Error messages don't leak info
- [ ] Security headers configured
- [ ] Backup encryption verified
- [ ] Penetration testing completed

---

**Kết thúc tài liệu SRS**

---

*MangoAds Agency ERP - SRS v1.0*
*Hoàn thành: Tháng 01/2026*
