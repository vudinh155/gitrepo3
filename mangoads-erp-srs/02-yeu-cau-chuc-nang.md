# Yêu Cầu Chức Năng

> **MangoAds Agency ERP - SRS v1.0**
> Phần 2: Chi tiết các chức năng của hệ thống

---

## 1. Module Quản Lý Hợp Đồng (Contract Management)

### 1.1. Mô Tả

Quản lý cấp pháp lý và tổng giá trị hợp đồng với khách hàng.

### 1.2. Yêu Cầu Chức Năng

#### FR-CON-001: Tạo Hợp Đồng Mới

| Thuộc tính | Mô tả |
|------------|-------|
| **Input** | Thông tin khách hàng, thời gian, giá trị |
| **Output** | Hợp đồng được tạo với mã duy nhất |
| **Actor** | Accountant, PM |

**Các trường thông tin:**

| Trường | Kiểu dữ liệu | Bắt buộc | Mô tả |
|--------|--------------|----------|-------|
| contract_code | String | Có | Mã hợp đồng (auto-generate) |
| client_id | FK | Có | Khách hàng |
| contract_name | String | Có | Tên hợp đồng |
| start_date | Date | Có | Ngày bắt đầu |
| end_date | Date | Có | Ngày kết thúc |
| total_value | Decimal | Có | Tổng giá trị hợp đồng |
| currency | Enum | Có | VND, USD |
| total_kpi | Integer | Không | Tổng KPI cam kết |
| margin_target | Decimal | Không | Tỷ lệ margin mục tiêu (%) |
| status | Enum | Có | Draft, Active, Completed, Cancelled |
| notes | Text | Không | Ghi chú |

**Business Rules:**
- `BR-CON-001`: end_date phải lớn hơn start_date
- `BR-CON-002`: total_value phải > 0
- `BR-CON-003`: Mã hợp đồng format: `{CLIENT}-{YEAR}{MONTH}-{SEQ}`

#### FR-CON-002: Cập Nhật Hợp Đồng

| Thuộc tính | Mô tả |
|------------|-------|
| **Điều kiện** | Hợp đồng chưa hoàn thành |
| **Actor** | Accountant |

**Business Rules:**
- `BR-CON-004`: Không thể giảm total_value nhỏ hơn tổng scope đã tạo
- `BR-CON-005`: Thay đổi giá trị cần ghi log audit

#### FR-CON-003: Xem Danh Sách Hợp Đồng

**Filters:**
- Theo khách hàng
- Theo trạng thái
- Theo thời gian
- Theo PM phụ trách

**Sorting:**
- Theo ngày tạo
- Theo giá trị
- Theo deadline

---

## 2. Module Quản Lý Scope (Scope Management)

### 2.1. Mô Tả

Mỗi hợp đồng chia thành nhiều scope độc lập về KPI, ngân sách, nghiệm thu.

### 2.2. Loại Scope

| Scope Type | Service | Ví dụ Code |
|------------|---------|------------|
| ADS_FACEBOOK | Facebook/Instagram Ads | FB01 |
| ADS_TIKTOK | TikTok Ads | TT01 |
| ADS_GOOGLE | Google Ads | GG01 |
| WEB | Website Development | WEB01 |
| APP | Mobile App Development | APP01 |
| HOSTING | Hosting & Domain | HOST01 |
| OTHER | Dịch vụ khác | OTH01 |

### 2.3. Yêu Cầu Chức Năng

#### FR-SCP-001: Tạo Scope Mới

**Các trường thông tin:**

| Trường | Kiểu dữ liệu | Bắt buộc | Mô tả |
|--------|--------------|----------|-------|
| scope_code | String | Có | Mã scope (auto-generate) |
| contract_id | FK | Có | Hợp đồng cha |
| scope_type | Enum | Có | Loại dịch vụ |
| scope_name | String | Có | Tên scope |
| channel | String | Có | Kênh (FB, TikTok, Google...) |
| budget | Decimal | Có | Ngân sách scope |
| revenue | Decimal | Có | Doanh thu dự kiến |
| kpi_target | Integer | Có | KPI cam kết |
| kpi_unit | Enum | Có | Lead, Click, Conversion, Impression |
| unit_price | Decimal | Có | Giá mỗi đơn vị KPI |
| cost_estimate | Decimal | Không | Chi phí dự kiến |
| margin_target | Decimal | Không | Margin mục tiêu (%) |
| start_date | Date | Có | Ngày bắt đầu |
| end_date | Date | Có | Ngày kết thúc |
| attributes | JSON | Không | Thuộc tính mở rộng |

**Attributes JSON Structure:**
```json
{
  "product": "Sản phẩm A",
  "region": "HCM, HN",
  "audience": "18-35, Female",
  "tech_stack": "React, Node.js",
  "objective": "Lead Generation"
}
```

**Business Rules:**
- `BR-SCP-001`: Tổng budget các scope ≤ total_value của contract
- `BR-SCP-002`: Scope date range phải nằm trong contract date range
- `BR-SCP-003`: revenue ≥ budget (để đảm bảo margin)

#### FR-SCP-002: Dashboard Scope

**Hiển thị:**
- Budget allocated vs Budget spent
- KPI target vs KPI achieved
- Revenue vs Cost
- Profit & Margin thực tế
- Remaining days

---

## 3. Module Quản Lý Campaign (Campaign Management)

### 3.1. Mô Tả

Áp dụng cho scope loại ADS. PM phân bổ ngân sách và KPI cho từng campaign.

### 3.2. Yêu Cầu Chức Năng

#### FR-CAM-001: Import Campaign từ Excel

**Template Excel:**

| Cột | Mô tả | Bắt buộc |
|-----|-------|----------|
| campaign_name | Tên campaign theo naming convention | Có |
| budget | Ngân sách phân bổ | Có |
| kpi_target | KPI mục tiêu | Có |
| start_date | Ngày bắt đầu | Có |
| end_date | Ngày kết thúc | Có |
| pricing_model | CPC, CPM, CPA | Có |
| break_even_cost | Chi phí hòa vốn | Không |

**Validation khi import:**
- `VAL-CAM-001`: Tổng budget campaigns ≤ budget scope
- `VAL-CAM-002`: Tổng KPI campaigns ≥ KPI scope
- `VAL-CAM-003`: Campaign name phải theo naming convention

#### FR-CAM-002: Campaign Naming Convention

**Format chuẩn:**
```
<Client>-<Contract>-<Scope>-<Channel>-<Objective>-<Segment>-<Phase>
```

**Ví dụ:**
```
Kewpie-KWP2026-FB01-FB-Lead-Office-P1
VinMart-VM2026-TT01-TK-Awareness-Young-P2
```

**Parsing Logic:**
```
campaign_name = "Kewpie-KWP2026-FB01-FB-Lead-Office-P1"

parsed = {
  client: "Kewpie",
  contract: "KWP2026",
  scope: "FB01",
  channel: "FB",
  objective: "Lead",
  segment: "Office",
  phase: "P1"
}
```

#### FR-CAM-003: Auto Mapping từ BigQuery

**Flow:**
1. BigQuery chứa data: `date, platform, campaign, spend, kpi`
2. System parse campaign name
3. Map về: `campaign → scope → contract → milestone`

---

## 4. Module Quản Lý KPI (KPI Tracking)

### 4.1. Yêu Cầu Chức Năng

#### FR-KPI-001: Tracking KPI theo Scope

**Metrics tracked:**

| Metric | Mô tả | Nguồn |
|--------|-------|-------|
| kpi_target | KPI cam kết | Scope |
| kpi_achieved | KPI đạt được | BigQuery |
| kpi_remaining | KPI còn thiếu | Calculated |
| kpi_pace | Tốc độ đạt KPI | Calculated |

**Calculation:**
```
kpi_remaining = kpi_target - kpi_achieved
days_remaining = end_date - today
kpi_pace = kpi_achieved / days_elapsed
required_pace = kpi_remaining / days_remaining
```

#### FR-KPI-002: Alert KPI

| Alert Type | Điều kiện | Action |
|------------|-----------|--------|
| WARNING | kpi_pace < required_pace * 0.8 | Notify PM |
| CRITICAL | kpi_pace < required_pace * 0.5 | Notify PM + Director |

---

## 5. Module Kiểm Soát Ngân Sách (Budget Control)

### 5.1. Auto Budget Control

#### FR-BUD-001: Cảnh Báo Ngân Sách

| Threshold | Action |
|-----------|--------|
| 80% budget | Warning notification |
| 95% budget | Critical notification |
| 100% budget | Auto pause (nếu có API) |

#### FR-BUD-002: Profit Protection Engine

**Formula:**
```
Profit = (KPI × Unit_Price) - Spend - Vendor_Cost

if Profit < 0:
    Block tăng ngân sách
    Alert PM + Finance
```

**Business Rules:**
- `BR-BUD-001`: Không cho phép tăng budget nếu profit < 0
- `BR-BUD-002`: Mỗi thay đổi budget cần approval từ PM

---

## 6. Module Milestone & Invoice (Payment Milestone)

### 6.1. Mô Tả

Milestone gắn vào **Scope**, không gắn vào Contract. Mỗi scope có nhiều phase nghiệm thu.

### 6.2. Yêu Cầu Chức Năng

#### FR-MIL-001: Tạo Milestone

**Các trường thông tin:**

| Trường | Kiểu dữ liệu | Bắt buộc | Mô tả |
|--------|--------------|----------|-------|
| milestone_code | String | Có | Mã milestone |
| scope_id | FK | Có | Scope cha |
| phase | String | Có | Tên phase (P1, P2...) |
| kpi_target | Integer | Có | KPI cần đạt để nghiệm thu |
| amount | Decimal | Có | Số tiền thu |
| due_date | Date | Có | Deadline nghiệm thu |
| status | Enum | Có | Pending, Achieved, Invoiced, Paid |

**Business Rules:**
- `BR-MIL-001`: Tổng amount các milestone = revenue của scope
- `BR-MIL-002`: Tổng KPI các milestone = kpi_target của scope

#### FR-MIL-002: Auto Check Milestone Achievement

**Flow:**
1. System check KPI từ BigQuery daily
2. Nếu `kpi_achieved >= milestone.kpi_target`
3. Update status = "Achieved"
4. Notify Finance để xuất invoice

#### FR-MIL-003: Xuất Invoice

**Trigger:** Milestone status = "Achieved"
**Output:** Invoice PDF với thông tin:
- Thông tin khách hàng
- Scope & Phase
- KPI đạt được
- Số tiền
- Hạn thanh toán

---

## 7. Module Quản Lý Dòng Tiền (Cashflow Management)

### 7.1. Dòng Tiền Ra (Cash Out)

#### FR-CSH-001: Tracking Chi Tiêu

**Nguồn chi:**
- Ads spend (từ BigQuery)
- Vendor payments
- Hosting/Infra

#### FR-CSH-002: Dự Báo Chi Tiêu

**Input:**
- Budget còn lại các scope
- Vendor payment schedule
- Historical spend rate

**Output:**
- Dự báo chi tiêu 30/60/90 ngày

### 7.2. Dòng Tiền Vào (Cash In)

#### FR-CSH-003: Dự Báo Thu Tiền

**Input:**
- Milestone sắp đạt
- KPI pace hiện tại

**Output:**
- Dự báo thu tiền theo milestone
- Timeline thu tiền

#### FR-CSH-004: Cashflow Dashboard

**Hiển thị:**
```
+----------------------------------------------------------+
|                    CASHFLOW FORECAST                      |
+----------------------------------------------------------+
|  Month    |  Cash In   |  Cash Out  |  Net     |  Balance |
+----------------------------------------------------------+
|  Jan 2026 |  500M      |  350M      |  +150M   |  150M    |
|  Feb 2026 |  300M      |  400M      |  -100M   |  50M     |
|  Mar 2026 |  600M      |  350M      |  +250M   |  300M    |
+----------------------------------------------------------+
```

---

## 8. Module Quản Lý Nhà Thầu (Vendor Management)

### 8.1. Yêu Cầu Chức Năng

#### FR-VEN-001: Tạo Vendor Assignment

**Các trường thông tin:**

| Trường | Kiểu dữ liệu | Bắt buộc | Mô tả |
|--------|--------------|----------|-------|
| vendor_id | FK | Có | Nhà thầu |
| scope_id | FK | Có | Scope liên quan |
| role | String | Có | Vai trò (Design, Dev, Content...) |
| cost | Decimal | Có | Chi phí |
| payment_terms | String | Có | Điều khoản thanh toán |

#### FR-VEN-002: Payment Schedule

**Các trường:**
- payment_date
- amount
- status (Pending, Paid)

**Business Rules:**
- `BR-VEN-001`: Vendor cost được tính vào chi phí scope
- `BR-VEN-002`: Alert khi payment due date đến

---

## 9. Module Báo Cáo (Reporting)

### 9.1. Dashboard Tổng Quan

#### FR-RPT-001: Contract Dashboard

| Widget | Mô tả |
|--------|-------|
| Active Contracts | Số hợp đồng đang chạy |
| Total Revenue | Tổng doanh thu |
| Total Cost | Tổng chi phí |
| Overall Margin | Margin tổng |

#### FR-RPT-002: Scope P&L Report

```
+----------------------------------------------------------+
|                    P&L BY SCOPE                           |
+----------------------------------------------------------+
|  Scope   |  Revenue  |  Cost     |  Profit  |  Margin    |
+----------------------------------------------------------+
|  FB01    |  100M     |  70M      |  30M     |  30%       |
|  TT01    |  80M      |  65M      |  15M     |  18.75%    |
|  WEB01   |  50M      |  55M      |  -5M     |  -10%      |
+----------------------------------------------------------+
|  TOTAL   |  230M     |  190M     |  40M     |  17.4%     |
+----------------------------------------------------------+
```

#### FR-RPT-003: Client Profitability Report

- Lợi nhuận theo khách hàng
- Top khách hàng sinh lời
- Khách hàng cần review

---

## 10. Yêu Cầu Phi Chức Năng

### 10.1. Performance

| Yêu cầu | Chỉ số |
|---------|--------|
| NFR-001 | Response time < 2 giây |
| NFR-002 | Support 100 concurrent users |
| NFR-003 | Data sync từ BigQuery < 5 phút |

### 10.2. Security

| Yêu cầu | Mô tả |
|---------|-------|
| NFR-004 | Encrypt sensitive data (AES-256) |
| NFR-005 | Role-based access control |
| NFR-006 | Audit log cho mọi thay đổi |

### 10.3. Availability

| Yêu cầu | Chỉ số |
|---------|--------|
| NFR-007 | Uptime 99.5% |
| NFR-008 | Backup daily |

---

**Tiếp theo:** [03 - Mô Hình Dữ Liệu](./03-mo-hinh-du-lieu.md)

---

*MangoAds Agency ERP - SRS v1.0*
