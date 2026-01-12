# MangoAds Agency ERP - Software Requirements Specification

```
 __  __                          _       _         _____ ____  ____
|  \/  | __ _ _ __   __ _  ___  / \   __| |___    | ____|  _ \|  _ \
| |\/| |/ _` | '_ \ / _` |/ _ \/ _ \ / _` / __|   |  _| | |_) | |_) |
| |  | | (_| | | | | (_| | (_) / ___ \ (_| \__ \   | |___|  _ <|  __/
|_|  |_|\__,_|_| |_|\__, |\___/_/   \_\__,_|___/   |_____|_| \_\_|
                    |___/
     Unified Agency Financial & Delivery System
```

> **Phiên bản:** 1.0
> **Ngày tạo:** Tháng 01/2026
> **Tác giả:** MangoAds Tech Team

---

## Giới thiệu

Đây là tài liệu **Software Requirements Specification (SRS)** cho hệ thống **MangoAds Agency ERP** - một hệ thống quản lý tài chính, hợp đồng, KPI và dòng tiền cho agency quảng cáo số.

## Tên Hệ Thống

**Unified Agency Financial & Delivery System**
*(Hệ thống điều hành tài chính – dự án – ads – outsource cho agency)*

## Triết Lý Thiết Kế

```
+----------------------------------------------------------+
|                                                          |
|   KHÔNG QUẢN ADS. KHÔNG QUẢN DEV.                        |
|                                                          |
|   Hệ thống chỉ quản:                                     |
|   - TIỀN                                                 |
|   - KPI                                                  |
|   - NGHIỆM THU                                           |
|   - DÒNG TIỀN                                            |
|   - LỜI LỖ                                               |
|   - RỦI RO                                               |
|                                                          |
|   Ads team, Dev team được tự do tác nghiệp               |
|   trong phạm vi tài chính đã khóa.                       |
|                                                          |
+----------------------------------------------------------+
```

## Cấu Trúc Tài Liệu

| File | Nội dung |
|------|----------|
| [01-tong-quan-he-thong.md](./01-tong-quan-he-thong.md) | Tổng quan, mục tiêu, phạm vi hệ thống |
| [02-yeu-cau-chuc-nang.md](./02-yeu-cau-chuc-nang.md) | Chi tiết các chức năng của hệ thống |
| [03-mo-hinh-du-lieu.md](./03-mo-hinh-du-lieu.md) | ERD, Schema, Database Design |
| [04-luong-nghiep-vu.md](./04-luong-nghiep-vu.md) | Các luồng nghiệp vụ chính |
| [05-tich-hop-api.md](./05-tich-hop-api.md) | Tích hợp BigQuery, Meta, TikTok, Google |
| [06-phan-quyen-bao-mat.md](./06-phan-quyen-bao-mat.md) | Phân quyền và bảo mật |

## Kiến Trúc Tổng Quan

```
Contract (Hợp đồng tổng)
 └── Scope (Channel / Service Package)
        ├── Campaigns (nếu là Ads)
        ├── Web / App / Hosting (nếu là Project)
        └── Outsource Assignments
              └── Vendor Payments
        └── Milestones (nghiệm thu – thu tiền)
```

## Đối Tượng Sử Dụng

| Vai trò | Mô tả |
|---------|-------|
| Director | Quản lý toàn bộ hệ thống |
| Finance | Quản lý dòng tiền, thu chi |
| Accountant | Quản lý hợp đồng, thanh toán |
| PM (Project Manager) | Quản lý scope, ngân sách, KPI |
| Ads Team | Xem ngân sách, thực thi campaign |

## Công Nghệ Đề Xuất

| Thành phần | Công nghệ |
|------------|-----------|
| Backend | Node.js / Python |
| Database | PostgreSQL / MongoDB |
| Data Platform | BigQuery |
| API | RESTful / GraphQL |
| Frontend | React / Vue.js |
| BI Dashboard | Metabase / Looker Studio |

---

## Liên Hệ

- **Product Owner:** MangoAds Management
- **Tech Lead:** MangoAds Tech Team
- **Email:** tech@mangoads.vn

---

*MangoAds - Digital Ads Agency*
*"Data-Driven Marketing Solutions"*
