# Luồng Nghiệp Vụ

> **MangoAds Agency ERP - SRS v1.0**
> Phần 4: Các luồng nghiệp vụ chính

---

## 1. Luồng Tạo Hợp Đồng Mới

### 1.1. Sequence Diagram

```
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│ Account │    │  System │    │   PM    │    │ Finance │    │Director │
└────┬────┘    └────┬────┘    └────┬────┘    └────┬────┘    └────┬────┘
     │              │              │              │              │
     │ Tạo client   │              │              │              │
     │─────────────>│              │              │              │
     │              │              │              │              │
     │ Tạo contract │              │              │              │
     │─────────────>│              │              │              │
     │              │              │              │              │
     │              │ Validate     │              │              │
     │              │─────────────>│              │              │
     │              │              │              │              │
     │              │ Gán PM       │              │              │
     │              │─────────────>│              │              │
     │              │              │              │              │
     │              │              │ Tạo scopes   │              │
     │              │              │─────────────>│              │
     │              │              │              │              │
     │              │              │ Set budget   │              │
     │              │              │─────────────>│              │
     │              │              │              │              │
     │              │              │              │ Review       │
     │              │              │              │─────────────>│
     │              │              │              │              │
     │              │              │              │   Approve    │
     │              │              │              │<─────────────│
     │              │              │              │              │
     │              │ Activate     │              │              │
     │              │<─────────────│──────────────│──────────────│
     │              │              │              │              │
```

### 1.2. Flowchart

```
┌─────────────────┐
│     START       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Kiểm tra client │
│ đã tồn tại?     │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
   YES       NO
    │         │
    │         ▼
    │    ┌─────────────────┐
    │    │  Tạo client mới │
    │    └────────┬────────┘
    │             │
    └──────┬──────┘
           │
           ▼
┌─────────────────┐
│  Nhập thông tin │
│  hợp đồng       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Validate data  │
│  (BR-CON-001-3) │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
  PASS      FAIL
    │         │
    │         ▼
    │    ┌─────────────────┐
    │    │  Hiển thị lỗi   │
    │    │  Yêu cầu sửa    │
    │    └────────┬────────┘
    │             │
    │             └────────┐
    │                      │
    ▼                      │
┌─────────────────┐        │
│  Lưu contract   │        │
│  Status: Draft  │        │
└────────┬────────┘        │
         │                 │
         ▼                 │
┌─────────────────┐        │
│  Gán PM phụ     │        │
│  trách          │        │
└────────┬────────┘        │
         │                 │
         ▼                 │
┌─────────────────┐        │
│  PM tạo scopes  │<───────┘
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Tổng scope ≤    │
│ Contract value? │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
   YES       NO
    │         │
    │         ▼
    │    ┌─────────────────┐
    │    │  Alert: Vượt    │
    │    │  ngân sách      │
    │    └────────┬────────┘
    │             │
    ▼             │
┌─────────────────┐
│  PM tạo        │
│  milestones    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Director review │
│  & approve      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Activate        │
│ contract        │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│      END        │
└─────────────────┘
```

---

## 2. Luồng Import Campaign và Sync Data

### 2.1. Sequence Diagram

```
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│   PM    │    │  System │    │BigQuery │    │Ads Platform│
└────┬────┘    └────┬────┘    └────┬────┘    └────┬────┘
     │              │              │              │
     │ Upload Excel │              │              │
     │─────────────>│              │              │
     │              │              │              │
     │              │ Validate     │              │
     │              │ campaigns    │              │
     │              │              │              │
     │              │ Parse naming │              │
     │              │ convention   │              │
     │              │              │              │
     │              │ Check budget │              │
     │              │              │              │
     │<─────────────│              │              │
     │ Kết quả      │              │              │
     │ validation   │              │              │
     │              │              │              │
     │              │              │ Scheduled    │
     │              │              │ sync         │
     │              │              │<─────────────│
     │              │              │              │
     │              │ Pull data    │              │
     │              │─────────────>│              │
     │              │              │              │
     │              │ Map campaign │              │
     │              │ → scope      │              │
     │              │              │              │
     │              │ Update KPI   │              │
     │              │              │              │
     │              │ Check        │              │
     │              │ milestones   │              │
     │              │              │              │
```

### 2.2. Campaign Naming Parse Flow

```
┌─────────────────────────────────────────────────────────┐
│           CAMPAIGN NAMING CONVENTION PARSER             │
└─────────────────────────────────────────────────────────┘

Input: "Kewpie-KWP2026-FB01-FB-Lead-Office-P1"

                    │
                    ▼
┌─────────────────────────────────────────────────────────┐
│                 SPLIT BY "-"                            │
└─────────────────────────────────────────────────────────┘

["Kewpie", "KWP2026", "FB01", "FB", "Lead", "Office", "P1"]
    │         │        │      │      │        │       │
    ▼         ▼        ▼      ▼      ▼        ▼       ▼

┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐
│ Client │ │Contract│ │ Scope  │ │Channel │ │Objective│ │Segment │ │ Phase  │
│Kewpie  │ │KWP2026 │ │  FB01  │ │   FB   │ │  Lead  │ │ Office │ │   P1   │
└────────┘ └────────┘ └────────┘ └────────┘ └────────┘ └────────┘ └────────┘
    │         │           │
    ▼         ▼           ▼
┌─────────────────────────────────────────────────────────┐
│                 DATABASE LOOKUP                         │
│                                                         │
│  SELECT scope_id FROM scopes s                          │
│  JOIN contracts c ON s.contract_id = c.contract_id      │
│  JOIN clients cl ON c.client_id = cl.client_id          │
│  WHERE cl.client_code = 'Kewpie'                        │
│    AND c.contract_code = 'KWP2026'                      │
│    AND s.scope_code = 'FB01'                            │
└─────────────────────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────────────┐
│               LINK CAMPAIGN TO SCOPE                    │
│                                                         │
│  INSERT INTO campaigns (campaign_name, scope_id, ...)   │
└─────────────────────────────────────────────────────────┘
```

---

## 3. Luồng Kiểm Soát Ngân Sách

### 3.1. Budget Control Flow

```
┌─────────────────┐
│   DAILY JOB     │
│   Check Budget  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Lấy tất cả      │
│ active scopes   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Với mỗi scope:  │
│ Tính % spent    │
└────────┬────────┘
         │
         ▼
    ┌────────────────────────────────────────┐
    │                                        │
    │    spent_pct = total_spend / budget    │
    │                                        │
    └───────────────────┬────────────────────┘
                        │
         ┌──────────────┼──────────────┐
         │              │              │
    < 80%          80-95%          > 95%
         │              │              │
         ▼              ▼              ▼
┌─────────────┐ ┌─────────────┐ ┌─────────────┐
│   Normal    │ │   WARNING   │ │  CRITICAL   │
│   No action │ │   Notify PM │ │  Notify PM  │
└─────────────┘ └─────────────┘ │  + Finance  │
                               │  + Auto     │
                               │    pause?   │
                               └─────────────┘
```

### 3.2. Profit Protection Flow

```
┌─────────────────┐
│ PM yêu cầu      │
│ tăng budget     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Tính profit     │
│ hiện tại        │
└────────┬────────┘
         │
    ┌────────────────────────────────────────────────────┐
    │                                                    │
    │  profit = (kpi_achieved × unit_price)              │
    │           - total_spend                            │
    │           - vendor_cost                            │
    │                                                    │
    └───────────────────────┬────────────────────────────┘
                            │
              ┌─────────────┴─────────────┐
              │                           │
         profit > 0                  profit ≤ 0
              │                           │
              ▼                           ▼
┌─────────────────────┐     ┌─────────────────────┐
│  Cho phép tăng      │     │  BLOCK              │
│  budget             │     │  Không cho tăng     │
│                     │     │  budget             │
│  Validate:          │     │                     │
│  new_budget ≤       │     │  Alert:             │
│  scope.budget       │     │  "Scope đang lỗ,    │
└─────────────────────┘     │   không thể tăng    │
                            │   ngân sách"        │
                            └─────────────────────┘
```

---

## 4. Luồng Nghiệm Thu và Thu Tiền

### 4.1. Milestone Achievement Flow

```
┌─────────────────┐
│   DAILY JOB     │
│Check Milestones │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Lấy milestones  │
│ status=pending  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Với mỗi         │
│ milestone:      │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────────────┐
│                                         │
│  SELECT SUM(kpi_value) as kpi_achieved  │
│  FROM campaign_data cd                  │
│  JOIN campaigns c ON ...                │
│  WHERE c.scope_id = milestone.scope_id  │
│                                         │
└───────────────────┬─────────────────────┘
                    │
         ┌──────────┴──────────┐
         │                     │
   kpi_achieved ≥        kpi_achieved <
   kpi_target            kpi_target
         │                     │
         ▼                     ▼
┌─────────────────┐    ┌─────────────────┐
│  Update status  │    │  Check pace     │
│  = 'achieved'   │    │                 │
│                 │    │  If behind:     │
│  Set achieved   │    │  Alert PM       │
│  _date = today  │    │                 │
└────────┬────────┘    └─────────────────┘
         │
         ▼
┌─────────────────┐
│ Notify Finance: │
│ "Milestone đạt  │
│  cần xuất hóa   │
│  đơn"           │
└─────────────────┘
```

### 4.2. Invoice Flow

```
┌─────────────────┐
│  Finance nhận   │
│  notification   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Review         │
│  milestone      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Tạo invoice    │
│  - invoice_no   │
│  - amount       │
│  - due_date     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Update         │
│  milestone      │
│  status =       │
│  'invoiced'     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Gửi invoice    │
│  cho client     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Track payment  │
│                 │
│  If overdue:    │
│  Send reminder  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Client thanh   │
│  toán           │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Update:        │
│  - invoice.     │
│    status=paid  │
│  - milestone.   │
│    status=paid  │
└─────────────────┘
```

---

## 5. Luồng Quản Lý Vendor

### 5.1. Vendor Assignment Flow

```
┌─────────────────┐
│  PM cần outsource│
│  cho scope      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Kiểm tra vendor │
│ đã tồn tại?     │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
   YES       NO
    │         │
    │         ▼
    │    ┌─────────────────┐
    │    │  Tạo vendor mới │
    │    │  (thông tin,    │
    │    │   bank info)    │
    │    └────────┬────────┘
    │             │
    └──────┬──────┘
           │
           ▼
┌─────────────────┐
│ Tạo assignment  │
│ - vendor_id     │
│ - scope_id      │
│ - role          │
│ - cost          │
│ - payment_terms │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Tạo payment     │
│ schedule        │
│                 │
│ Ví dụ:          │
│ 30% ký HĐ      │
│ 40% UAT         │
│ 30% go-live     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ System tự động  │
│ cộng vendor_cost│
│ vào scope cost  │
└─────────────────┘
```

### 5.2. Vendor Payment Flow

```
┌─────────────────┐
│   DAILY JOB     │
│ Check payments  │
│ due soon        │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Lấy payments    │
│ due trong 7 ngày│
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Notify Finance: │
│ "Cần thanh toán │
│  vendor XYZ     │
│  trong 7 ngày"  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Finance review │
│  & approve      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Thực hiện      │
│  chuyển khoản   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Update payment │
│  status = paid  │
│  paid_date =    │
│  today          │
└─────────────────┘
```

---

## 6. Luồng Dự Báo Dòng Tiền

### 6.1. Cashflow Forecast Flow

```
┌─────────────────┐
│  Finance yêu    │
│  cầu forecast   │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────────────────────────────┐
│                 CALCULATE CASH IN                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1. Milestones đã đạt, chưa thu:                        │
│     SELECT SUM(amount) WHERE status IN ('achieved',     │
│     'invoiced') GROUP BY month                          │
│                                                         │
│  2. Milestones sắp đạt (dựa trên KPI pace):             │
│     Estimate based on current KPI pace                  │
│                                                         │
└───────────────────────────┬─────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│                CALCULATE CASH OUT                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1. Vendor payments pending:                            │
│     SELECT SUM(amount) WHERE status = 'pending'         │
│     GROUP BY month                                      │
│                                                         │
│  2. Estimated ads spend:                                │
│     (remaining_budget / remaining_days) × 30 days       │
│                                                         │
│  3. Fixed costs (hosting, tools):                       │
│     From monthly allocation                             │
│                                                         │
└───────────────────────────┬─────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│                 GENERATE REPORT                         │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Month    | Cash In  | Cash Out | Net      | Balance   │
│  ─────────|─────────|──────────|──────────|───────────│
│  Jan 2026 | 500M    | 350M     | +150M    | 150M      │
│  Feb 2026 | 300M    | 400M     | -100M    | 50M       │
│  Mar 2026 | 600M    | 350M     | +250M    | 300M      │
│                                                         │
│  ⚠️ Alert: Feb 2026 có cash flow âm                     │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 7. State Diagrams

### 7.1. Contract States

```
                    ┌─────────┐
                    │  DRAFT  │
                    └────┬────┘
                         │
                    [Director approve]
                         │
                         ▼
                    ┌─────────┐
        ┌──────────│ ACTIVE  │──────────┐
        │          └────┬────┘          │
        │               │               │
   [Cancel]        [All scopes         [Contract
        │           completed]          expired]
        │               │               │
        ▼               ▼               │
   ┌─────────┐    ┌─────────┐          │
   │CANCELLED│    │COMPLETED│◄─────────┘
   └─────────┘    └─────────┘
```

### 7.2. Milestone States

```
                    ┌─────────┐
                    │ PENDING │
                    └────┬────┘
                         │
                    [KPI achieved]
                         │
                         ▼
                    ┌─────────┐
                    │ACHIEVED │
                    └────┬────┘
                         │
                    [Invoice created]
                         │
                         ▼
                    ┌─────────┐
                    │INVOICED │
                    └────┬────┘
                         │
                    [Payment received]
                         │
                         ▼
                    ┌─────────┐
                    │  PAID   │
                    └─────────┘

        (Có thể CANCELLED từ bất kỳ state nào)
```

---

**Tiếp theo:** [05 - Tích Hợp và API](./05-tich-hop-api.md)

---

*MangoAds Agency ERP - SRS v1.0*
