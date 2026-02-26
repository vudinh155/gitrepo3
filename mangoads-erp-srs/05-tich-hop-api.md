# Tích Hợp và API

> **MangoAds Agency ERP - SRS v1.0**
> Phần 5: Tích hợp BigQuery, Ads Platforms, API Design

---

## 1. Kiến Trúc Tích Hợp

### 1.1. Tổng Quan Kiến Trúc

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         MANGOADS ERP ARCHITECTURE                       │
└─────────────────────────────────────────────────────────────────────────┘

                        ┌─────────────────────┐
                        │    ADS PLATFORMS    │
                        │                     │
                        │ ┌─────┐ ┌─────┐    │
                        │ │Meta │ │TikTok│    │
                        │ └──┬──┘ └──┬──┘    │
                        │    │       │        │
                        │ ┌──┴───────┴──┐    │
                        │ │   Google    │    │
                        │ └──────┬──────┘    │
                        └────────┼───────────┘
                                 │
                        [APIs / Data Export]
                                 │
                                 ▼
                        ┌─────────────────────┐
                        │     BIGQUERY        │
                        │   (Data Warehouse)  │
                        │                     │
                        │  - ads_meta         │
                        │  - ads_tiktok       │
                        │  - ads_google       │
                        │  - ads_unified      │
                        └────────┬────────────┘
                                 │
                        [Scheduled Query / ETL]
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                          MANGOADS ERP                                   │
│                                                                         │
│  ┌───────────────┐    ┌───────────────┐    ┌───────────────┐           │
│  │   API Layer   │◄──►│  Business     │◄──►│   Database    │           │
│  │   (REST)      │    │  Logic        │    │  (PostgreSQL) │           │
│  └───────────────┘    └───────────────┘    └───────────────┘           │
│         ▲                                                               │
│         │                                                               │
└─────────┼───────────────────────────────────────────────────────────────┘
          │
          │
┌─────────┴───────────────────────────────────────────────────────────────┐
│                           FRONTEND                                      │
│                                                                         │
│  ┌───────────────┐    ┌───────────────┐    ┌───────────────┐           │
│  │  Dashboard    │    │  Management   │    │   Reports     │           │
│  │  (Realtime)   │    │  (CRUD)       │    │  (BI/Charts)  │           │
│  └───────────────┘    └───────────────┘    └───────────────┘           │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Tích Hợp BigQuery

### 2.1. Schema BigQuery

#### Table: ads_unified

```sql
-- BigQuery table structure
CREATE TABLE `project.dataset.ads_unified` (
    date DATE NOT NULL,
    platform STRING NOT NULL,           -- 'meta', 'tiktok', 'google'
    account_id STRING,
    account_name STRING,
    campaign_id STRING,
    campaign_name STRING NOT NULL,
    adset_id STRING,
    adset_name STRING,
    ad_id STRING,
    ad_name STRING,
    spend FLOAT64 DEFAULT 0,
    impressions INT64 DEFAULT 0,
    clicks INT64 DEFAULT 0,
    reach INT64,
    frequency FLOAT64,
    -- KPI metrics
    conversions INT64 DEFAULT 0,
    leads INT64 DEFAULT 0,
    purchases INT64 DEFAULT 0,
    add_to_cart INT64,
    -- Calculated
    cpc FLOAT64,
    cpm FLOAT64,
    ctr FLOAT64,
    conversion_rate FLOAT64,
    -- Metadata
    synced_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP()
);
```

### 2.2. ETL Pipeline

```
┌─────────────────────────────────────────────────────────────────────┐
│                         ETL PIPELINE                                │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  META ADS   │     │ TIKTOK ADS  │     │ GOOGLE ADS  │
│   API       │     │    API      │     │    API      │
└──────┬──────┘     └──────┬──────┘     └──────┬──────┘
       │                   │                   │
       ▼                   ▼                   ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ads_meta_raw │     │ads_tiktok_  │     │ads_google_  │
│             │     │raw          │     │raw          │
└──────┬──────┘     └──────┬──────┘     └──────┬──────┘
       │                   │                   │
       └───────────────────┼───────────────────┘
                           │
                           ▼
              ┌─────────────────────────┐
              │   TRANSFORM & UNIFY     │
              │                         │
              │  - Standardize columns  │
              │  - Map campaign names   │
              │  - Calculate metrics    │
              └───────────┬─────────────┘
                          │
                          ▼
              ┌─────────────────────────┐
              │     ads_unified         │
              └───────────┬─────────────┘
                          │
                          ▼
              ┌─────────────────────────┐
              │   SYNC TO ERP           │
              │   (campaign_data table) │
              └─────────────────────────┘
```

### 2.3. Scheduled Query

```sql
-- Daily sync query (chạy 6AM hàng ngày)
INSERT INTO `project.dataset.ads_unified`
SELECT
    DATE(date_start) as date,
    'meta' as platform,
    account_id,
    account_name,
    campaign_id,
    campaign_name,
    adset_id,
    adset_name,
    ad_id,
    ad_name,
    CAST(spend as FLOAT64) as spend,
    CAST(impressions as INT64) as impressions,
    CAST(clicks as INT64) as clicks,
    CAST(reach as INT64) as reach,
    CAST(frequency as FLOAT64) as frequency,
    CAST(COALESCE(actions_lead, 0) as INT64) as leads,
    CAST(COALESCE(actions_purchase, 0) as INT64) as purchases,
    SAFE_DIVIDE(spend, clicks) as cpc,
    SAFE_DIVIDE(spend, impressions) * 1000 as cpm,
    SAFE_DIVIDE(clicks, impressions) * 100 as ctr,
    CURRENT_TIMESTAMP() as synced_at
FROM `project.dataset.ads_meta_raw`
WHERE DATE(date_start) = DATE_SUB(CURRENT_DATE(), INTERVAL 1 DAY)
  AND campaign_name IS NOT NULL;
```

---

## 3. Sync Service

### 3.1. Campaign Data Sync

```python
# sync_service.py (pseudo-code)

from google.cloud import bigquery
import psycopg2

class CampaignSyncService:
    def __init__(self):
        self.bq_client = bigquery.Client()
        self.db_conn = psycopg2.connect(...)

    def sync_campaign_data(self, date: str):
        """
        Sync campaign data từ BigQuery về PostgreSQL
        """
        # 1. Query BigQuery
        query = f"""
        SELECT
            date,
            platform,
            campaign_name,
            SUM(spend) as spend,
            SUM(impressions) as impressions,
            SUM(clicks) as clicks,
            SUM(leads) as kpi_value
        FROM `project.dataset.ads_unified`
        WHERE date = '{date}'
        GROUP BY date, platform, campaign_name
        """

        results = self.bq_client.query(query).result()

        # 2. Parse campaign name và map với scope
        for row in results:
            parsed = self.parse_campaign_name(row.campaign_name)
            campaign_id = self.get_or_create_campaign(
                campaign_name=row.campaign_name,
                parsed=parsed
            )

            # 3. Insert/Update campaign_data
            self.upsert_campaign_data(
                campaign_id=campaign_id,
                date=row.date,
                platform=row.platform,
                spend=row.spend,
                kpi_value=row.kpi_value
            )

        # 4. Trigger milestone check
        self.check_milestones()

    def parse_campaign_name(self, name: str) -> dict:
        """
        Parse: Kewpie-KWP2026-FB01-FB-Lead-Office-P1
        """
        parts = name.split('-')
        if len(parts) >= 7:
            return {
                'client': parts[0],
                'contract': parts[1],
                'scope': parts[2],
                'channel': parts[3],
                'objective': parts[4],
                'segment': parts[5],
                'phase': parts[6]
            }
        return None

    def check_milestones(self):
        """
        Check và update milestone status
        """
        cursor = self.db_conn.cursor()
        cursor.execute("""
            SELECT check_milestone_achievement();
        """)
        self.db_conn.commit()
```

### 3.2. Cron Job Schedule

```yaml
# cron_jobs.yaml

jobs:
  - name: sync_campaign_data
    schedule: "0 6 * * *"  # 6AM daily
    command: python sync_service.py sync

  - name: check_budgets
    schedule: "0 */4 * * *"  # Every 4 hours
    command: python budget_service.py check

  - name: check_milestones
    schedule: "0 7 * * *"  # 7AM daily
    command: python milestone_service.py check

  - name: generate_cashflow_forecast
    schedule: "0 8 * * 1"  # 8AM every Monday
    command: python cashflow_service.py forecast
```

---

## 4. REST API Design

### 4.1. API Endpoints

#### Contracts API

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/contracts` | Danh sách hợp đồng |
| GET | `/api/v1/contracts/{id}` | Chi tiết hợp đồng |
| POST | `/api/v1/contracts` | Tạo hợp đồng |
| PUT | `/api/v1/contracts/{id}` | Cập nhật hợp đồng |
| DELETE | `/api/v1/contracts/{id}` | Xóa hợp đồng |
| GET | `/api/v1/contracts/{id}/scopes` | Danh sách scope |
| GET | `/api/v1/contracts/{id}/summary` | Tổng hợp hợp đồng |

#### Scopes API

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/scopes` | Danh sách scope |
| GET | `/api/v1/scopes/{id}` | Chi tiết scope |
| POST | `/api/v1/scopes` | Tạo scope |
| PUT | `/api/v1/scopes/{id}` | Cập nhật scope |
| GET | `/api/v1/scopes/{id}/campaigns` | Danh sách campaign |
| GET | `/api/v1/scopes/{id}/milestones` | Danh sách milestone |
| GET | `/api/v1/scopes/{id}/profit` | Lợi nhuận scope |

#### Campaigns API

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/campaigns` | Danh sách campaign |
| POST | `/api/v1/campaigns/import` | Import từ Excel |
| GET | `/api/v1/campaigns/{id}/data` | Data campaign |

#### Milestones API

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/milestones` | Danh sách milestone |
| POST | `/api/v1/milestones` | Tạo milestone |
| PUT | `/api/v1/milestones/{id}` | Cập nhật milestone |
| POST | `/api/v1/milestones/{id}/invoice` | Xuất hóa đơn |

#### Dashboard API

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/dashboard/overview` | Tổng quan |
| GET | `/api/v1/dashboard/cashflow` | Dòng tiền |
| GET | `/api/v1/dashboard/profit` | Lợi nhuận |
| GET | `/api/v1/dashboard/alerts` | Cảnh báo |

### 4.2. API Request/Response Examples

#### Create Contract

**Request:**
```http
POST /api/v1/contracts
Content-Type: application/json
Authorization: Bearer {token}

{
    "client_id": "uuid-client-123",
    "contract_name": "Kewpie Digital Campaign 2026",
    "start_date": "2026-01-01",
    "end_date": "2026-12-31",
    "total_value": 500000000,
    "currency": "VND",
    "total_kpi": 10000,
    "kpi_unit": "Lead",
    "margin_target": 25.00,
    "pm_id": "uuid-pm-456"
}
```

**Response:**
```json
{
    "success": true,
    "data": {
        "contract_id": "uuid-contract-789",
        "contract_code": "KEWPIE-202601-001",
        "contract_name": "Kewpie Digital Campaign 2026",
        "client": {
            "client_id": "uuid-client-123",
            "client_name": "Kewpie Vietnam"
        },
        "start_date": "2026-01-01",
        "end_date": "2026-12-31",
        "total_value": 500000000,
        "currency": "VND",
        "status": "draft",
        "created_at": "2026-01-01T10:00:00Z"
    }
}
```

#### Get Scope Summary

**Request:**
```http
GET /api/v1/scopes/uuid-scope-123/profit
Authorization: Bearer {token}
```

**Response:**
```json
{
    "success": true,
    "data": {
        "scope_id": "uuid-scope-123",
        "scope_code": "FB01",
        "scope_name": "Facebook Ads Campaign",
        "financials": {
            "budget": 100000000,
            "revenue": 120000000,
            "ads_spend": 75000000,
            "vendor_cost": 10000000,
            "total_cost": 85000000,
            "profit": 35000000,
            "margin_pct": 29.17
        },
        "kpi": {
            "target": 5000,
            "achieved": 3500,
            "remaining": 1500,
            "achievement_pct": 70.00
        },
        "budget_status": {
            "spent_pct": 75.00,
            "remaining": 25000000,
            "days_remaining": 45,
            "daily_spend_rate": 1666666.67
        }
    }
}
```

#### Cashflow Forecast

**Request:**
```http
GET /api/v1/dashboard/cashflow?months=3
Authorization: Bearer {token}
```

**Response:**
```json
{
    "success": true,
    "data": {
        "forecast_period": {
            "start": "2026-01-01",
            "end": "2026-03-31"
        },
        "monthly": [
            {
                "month": "2026-01",
                "cash_in": 500000000,
                "cash_out": 350000000,
                "net": 150000000,
                "balance": 150000000,
                "details": {
                    "cash_in": {
                        "milestones": 500000000
                    },
                    "cash_out": {
                        "ads_spend": 250000000,
                        "vendor_payments": 80000000,
                        "other": 20000000
                    }
                }
            },
            {
                "month": "2026-02",
                "cash_in": 300000000,
                "cash_out": 400000000,
                "net": -100000000,
                "balance": 50000000,
                "alert": "Negative cashflow"
            }
        ],
        "alerts": [
            {
                "type": "WARNING",
                "message": "Feb 2026 có dòng tiền âm -100M",
                "suggested_action": "Đẩy nhanh thu tiền milestone"
            }
        ]
    }
}
```

---

## 5. Webhook & Notifications

### 5.1. Webhook Events

| Event | Trigger | Payload |
|-------|---------|---------|
| `milestone.achieved` | KPI đạt | milestone_id, scope_id, amount |
| `budget.warning` | Spend > 80% | scope_id, spent_pct |
| `budget.critical` | Spend > 95% | scope_id, spent_pct |
| `profit.negative` | Profit < 0 | scope_id, profit |
| `payment.due` | Payment due soon | vendor_id, amount, due_date |
| `invoice.overdue` | Invoice quá hạn | invoice_id, days_overdue |

### 5.2. Notification Channels

```yaml
notifications:
  channels:
    - type: email
      enabled: true
      recipients:
        milestone.achieved: ["finance@mangoads.vn"]
        budget.warning: ["pm@mangoads.vn"]
        budget.critical: ["pm@mangoads.vn", "director@mangoads.vn"]

    - type: slack
      enabled: true
      webhook_url: "https://hooks.slack.com/..."
      channel: "#alerts"

    - type: in_app
      enabled: true
```

---

## 6. Security

### 6.1. Authentication

- **Method:** JWT Token
- **Token Lifetime:** 24 hours
- **Refresh Token:** 7 days

### 6.2. API Rate Limiting

| Tier | Requests/minute |
|------|-----------------|
| Standard | 60 |
| Premium | 300 |
| Internal | Unlimited |

### 6.3. Data Encryption

- **Transit:** TLS 1.3
- **At Rest:** AES-256
- **Sensitive Fields:** Hashed (bcrypt)

---

**Tiếp theo:** [06 - Phân Quyền và Bảo Mật](./06-phan-quyen-bao-mat.md)

---

*MangoAds Agency ERP - SRS v1.0*
