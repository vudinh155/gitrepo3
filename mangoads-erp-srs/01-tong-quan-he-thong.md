# Tổng Quan Hệ Thống

> **MangoAds Agency ERP - SRS v1.0**
> Phần 1: Tổng quan, Mục tiêu, Phạm vi

---

## 1. Giới Thiệu

### 1.1. Mục Đích Tài Liệu

Tài liệu này mô tả chi tiết các yêu cầu phần mềm cho hệ thống **Unified Agency Financial & Delivery System** của MangoAds. Đây là hệ thống ERP chuyên biệt cho agency quảng cáo số, quản lý toàn bộ vòng đời từ hợp đồng đến thu tiền.

### 1.2. Phạm Vi Hệ Thống

Hệ thống bao gồm:
- Quản lý hợp đồng và khách hàng
- Quản lý scope dịch vụ (Ads, Web, App, Hosting)
- Quản lý campaign và ngân sách
- Quản lý KPI và nghiệm thu
- Quản lý dòng tiền và thanh toán
- Quản lý nhà thầu/outsource
- Dashboard phân tích lợi nhuận

### 1.3. Đối Tượng Tài Liệu

- Product Owner
- Development Team
- QA Team
- Stakeholders

---

## 2. Mô Tả Tổng Quan

### 2.1. Bối Cảnh Nghiệp Vụ

MangoAds là agency quảng cáo số cung cấp đa dịch vụ:
- **Performance Ads:** Facebook, TikTok, Google
- **Tech Services:** Website, Mobile App
- **Infrastructure:** Hosting, Domain

**Thách thức hiện tại:**
- Không có công cụ tổng hợp quản lý tài chính
- Khó theo dõi lời/lỗ theo từng dịch vụ
- Dòng tiền không được dự báo chính xác
- KPI và nghiệm thu không được liên kết tự động

### 2.2. Giải Pháp

Xây dựng hệ thống ERP tích hợp:

```
┌─────────────────────────────────────────────────────────────┐
│                    MANGOADS AGENCY ERP                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │  CONTRACT   │──│    SCOPE    │──│  CAMPAIGN   │         │
│  │  MANAGEMENT │  │  MANAGEMENT │  │  MANAGEMENT │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
│         │                │                │                 │
│         ▼                ▼                ▼                 │
│  ┌─────────────────────────────────────────────────┐       │
│  │              FINANCIAL ENGINE                    │       │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐           │       │
│  │  │ BUDGET  │ │  KPI    │ │ PROFIT  │           │       │
│  │  │ CONTROL │ │ TRACKING│ │ PROTECT │           │       │
│  │  └─────────┘ └─────────┘ └─────────┘           │       │
│  └─────────────────────────────────────────────────┘       │
│         │                │                │                 │
│         ▼                ▼                ▼                 │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │  MILESTONE  │  │  CASHFLOW   │  │  DASHBOARD  │         │
│  │  & INVOICE  │  │  FORECAST   │  │  & REPORT   │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 3. Các Bên Liên Quan (Stakeholders)

### 3.1. Người Dùng Nội Bộ

| Vai trò | Trách nhiệm | Tương tác với hệ thống |
|---------|-------------|------------------------|
| **Director** | Ra quyết định chiến lược | Xem báo cáo tổng hợp, phê duyệt |
| **Finance** | Quản lý dòng tiền | Theo dõi thu chi, dự báo |
| **Accountant** | Quản lý hợp đồng | Tạo hợp đồng, xuất hóa đơn |
| **PM** | Quản lý dự án | Phân bổ ngân sách, theo dõi KPI |
| **Ads Team** | Thực thi campaign | Xem ngân sách còn lại |

### 3.2. Hệ Thống Bên Ngoài

| Hệ thống | Mục đích tích hợp |
|----------|-------------------|
| **Meta Ads** | Lấy data chi tiêu, KPI |
| **TikTok Ads** | Lấy data chi tiêu, KPI |
| **Google Ads** | Lấy data chi tiêu, KPI |
| **BigQuery** | Data warehouse |
| **Kế toán** | Đồng bộ hóa đơn |

---

## 4. Mục Tiêu Kinh Doanh

### 4.1. Mục Tiêu Chính

| # | Mục tiêu | Chỉ số đo lường |
|---|----------|-----------------|
| 1 | Kiểm soát lời/lỗ theo scope | Margin accuracy > 95% |
| 2 | Dự báo dòng tiền chính xác | Forecast variance < 10% |
| 3 | Tự động hóa nghiệm thu | Time-to-invoice giảm 50% |
| 4 | Ngăn chặn chạy lố ngân sách | Budget overrun = 0% |

### 4.2. Lợi Ích Kỳ Vọng

**Cho Management:**
- Biết chính xác lời/lỗ từng khách hàng, từng dịch vụ
- Dự báo được dòng tiền 3-6 tháng

**Cho Finance:**
- Tự động nhắc thu tiền theo milestone
- Biết khi nào cần bơm tiền ads

**Cho PM:**
- Phân bổ ngân sách rõ ràng
- Không bị vượt budget

**Cho Ads Team:**
- Biết giới hạn ngân sách để không chạy lố
- Tự do tác nghiệp trong phạm vi cho phép

---

## 5. Giả Định và Ràng Buộc

### 5.1. Giả Định

1. Tất cả campaign ads được đặt tên theo chuẩn naming convention
2. Data từ ads platform được sync vào BigQuery định kỳ
3. Mỗi hợp đồng có ít nhất 1 scope
4. Mỗi scope có ít nhất 1 milestone

### 5.2. Ràng Buộc

1. **Kỹ thuật:**
   - Hệ thống phải tích hợp được với BigQuery
   - Response time < 2 giây cho các truy vấn thông thường

2. **Nghiệp vụ:**
   - Tuân thủ quy trình kế toán hiện hành
   - Bảo mật thông tin tài chính khách hàng

3. **Tài nguyên:**
   - Team phát triển: 3-5 người
   - Timeline MVP: 3 tháng

---

## 6. Phạm Vi Hệ Thống

### 6.1. Trong Phạm Vi (In Scope)

| Module | Chức năng |
|--------|-----------|
| Contract | CRUD hợp đồng, khách hàng |
| Scope | CRUD scope, phân bổ ngân sách |
| Campaign | Import campaign, mapping data |
| KPI | Tracking KPI theo scope |
| Milestone | Quản lý nghiệm thu, xuất invoice |
| Cashflow | Dự báo dòng tiền |
| Vendor | Quản lý nhà thầu, thanh toán |
| Report | Dashboard, báo cáo |

### 6.2. Ngoài Phạm Vi (Out of Scope)

| Chức năng | Lý do |
|-----------|-------|
| Quản lý chi tiết campaign ads | Dùng native platform |
| Quản lý dev task | Dùng Jira/Trello |
| CRM đầy đủ | Chỉ quản lý khách hàng liên quan hợp đồng |
| HR/Payroll | Hệ thống riêng |

---

## 7. Định Nghĩa Thuật Ngữ

| Thuật ngữ | Định nghĩa |
|-----------|------------|
| **Contract** | Hợp đồng tổng với khách hàng, bao gồm nhiều scope |
| **Scope** | Gói dịch vụ trong hợp đồng (FB Ads, Web, App...) |
| **Campaign** | Chiến dịch quảng cáo thuộc 1 scope |
| **Milestone** | Mốc nghiệm thu để thu tiền |
| **KPI** | Chỉ số hiệu quả (Lead, Click, Conversion...) |
| **Margin** | Tỷ lệ lợi nhuận = (Revenue - Cost) / Revenue |
| **Spend** | Chi tiêu thực tế trên ads platform |
| **Vendor** | Nhà thầu/đối tác outsource |

---

## 8. Tài Liệu Tham Khảo

- Meta Marketing API Documentation
- TikTok Marketing API Documentation
- Google Ads API Documentation
- BigQuery Documentation

---

**Tiếp theo:** [02 - Yêu Cầu Chức Năng](./02-yeu-cau-chuc-nang.md)

---

*MangoAds Agency ERP - SRS v1.0*
