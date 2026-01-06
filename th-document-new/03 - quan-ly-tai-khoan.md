# TỔNG HỢP MODULE 2: QUẢN LÝ TÀI KHOẢN

> **Tương ứng SOW:** Module 2: Quản lý tài khoản
> **Tham chiếu:** B.1 - Module 2 trong yêu cầu từ khách hàng

---

## Mục lục

1. [Tổng quan Module](#1-tổng-quan-module)
2. [Ma trận phân chia trách nhiệm](#2-ma-trận-phân-chia-trách-nhiệm)
3. [Chi tiết từng chức năng](#3-chi-tiết-từng-chức-năng)

---

## 1. TỔNG QUAN MODULE

### 1.1. Module Quản lý Tài khoản

Module này là trung tâm quản lý thông tin cá nhân của khách hàng trên ứng dụng TH Milk. Đây là nơi khách hàng xem và quản lý tất cả thông tin liên quan đến tài khoản của mình.

#### Backend Loyalty

| Vai trò       | Sở hữu dữ liệu                 |
| ------------- | ------------------------------ |
| Trung tâm CRM | Profile, Điểm, Hạng, Quà       |
| Bảo mật       | Mật khẩu, OTP                  |
| Tuân thủ PDPA | Dữ liệu cá nhân, Anonymization |

#### Backend LS Retail

| Vai trò           | Sở hữu dữ liệu              |
| ----------------- | --------------------------- |
| Hệ thống commerce | Đơn hàng, Địa chỉ giao hàng |
| Thanh toán        | Gift Card                   |

#### Mango CMS

| Vai trò     | Trách nhiệm                        |
| ----------- | ---------------------------------- |
| Middleware  | Điều phối giữa Frontend và Backend |
| Cache layer | Tăng tốc truy vấn                  |
| Content     | FAQ, Policy, Ngôn ngữ              |
| Session     | Quản lý đăng nhập, token           |

### 5.2. Tóm tắt nguyên tắc

| Nguyên tắc                          | Áp dụng                      |
| ----------------------------------- | ---------------------------- |
| **Dữ liệu khách hàng → Loyalty**    | Profile, điểm, hạng, quà     |
| **Dữ liệu giao dịch → LS Retail**   | Đơn hàng, địa chỉ, Gift Card |
| **Nội dung & Settings → Mango CMS** | FAQ, Policy, ngôn ngữ        |
| **Cache & Orchestrate → Mango CMS** | Tăng tốc và điều phối        |

### 1.2. Các thành phần chính

| Thành phần              | Mô tả                                            |
| ----------------------- | ------------------------------------------------ |
| **Thông tin cá nhân**   | Họ tên, SĐT, email, ngày sinh, địa chỉ           |
| **Hạng thành viên**     | Hiển thị hạng hiện tại (Đồng/Bạc/Vàng/Kim cương) |
| **Hoa đất (Điểm)**      | Số điểm tích lũy, điểm sắp hết hạn               |
| **Quà của tôi**         | Danh sách quà tặng đã nhận                       |
| **Quản lý đơn hàng**    | Lịch sử đơn hàng theo trạng thái                 |
| **Tài khoản trả trước** | Số dư Gift Card                                  |
| **Địa chỉ giao hàng**   | Danh sách địa chỉ đã lưu                         |
| **Cài đặt**             | Ngôn ngữ, bảo mật                                |
| **Hỗ trợ & Chính sách** | FAQ, liên hệ, điều khoản                         |
| **Xóa tài khoản**       | Tuân thủ PDPA                                    |

### 1.3. Sơ đồ kiến trúc tổng quan

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              FRONTEND APP                                   │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              MANGO CMS                                      │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  VAI TRÒ:                                                           │    │
│  │  • Điều phối request giữa Frontend và Backend                       │    │
│  │  • Cache dữ liệu để tăng tốc                                        │    │
│  │  • Quản lý session và authentication                                │    │
│  │  • Lưu trữ cài đặt ngôn ngữ                                         │    │
│  │  • Lưu trữ nội dung hỗ trợ (FAQ, Policy)                            │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
                    │                               │
                    ▼                               ▼
┌────────────────────────────────┐    ┌────────────────────────────────┐
│       BACKEND LOYALTY          │    │       BACKEND LS RETAIL        │
│  ┌──────────────────────────┐  │    │  ┌──────────────────────────┐  │
│  │  SỞ HỮU:                 │  │    │  │  SỞ HỮU:                 │  │
│  │  • Thông tin cá nhân     │  │    │  │  • Đơn hàng              │  │
│  │  • Điểm tích lũy         │  │    │  │  • Địa chỉ giao hàng     │  │
│  │  • Hạng thành viên       │  │    │  │  • Gift Card             │  │
│  │  • Kho quà               │  │    │  │                          │  │
│  │  • Mật khẩu/Bảo mật      │  │    │  │                          │  │
│  │  • Dữ liệu PDPA          │  │    │  │                          │  │
│  └──────────────────────────┘  │    │  └──────────────────────────┘  │
└────────────────────────────────┘    └────────────────────────────────┘
```

---

## 2. MA TRẬN PHÂN CHIA TRÁCH NHIỆM

### 2.1. Tổng quan phân chia

| Chức năng                           | Backend Loyalty | Backend LS Retail |   Mango CMS    |
| ----------------------------------- | :-------------: | :---------------: | :------------: |
| **Thông tin cá nhân**               | ✅ **Primary**  |         -         |     Cache      |
| **Quản lý đơn hàng**                |        -        |  ✅ **Primary**   |   Aggregate    |
| **Tài khoản trả trước (Gift Card)** |        -        |  ✅ **Primary**   |    Display     |
| **Địa chỉ giao hàng**               |        -        |  ✅ **Primary**   |     Cache      |
| **Hoa đất (Điểm)**                  | ✅ **Primary**  |         -         |    Display     |
| **Quà của tôi**                     | ✅ **Primary**  |         -         |     Cache      |
| **Cài đặt ngôn ngữ**                |        -        |         -         | ✅ **Primary** |
| **Cài đặt bảo mật**                 | ✅ **Primary**  |         -         |  Orchestrate   |
| **Nội dung hỗ trợ (FAQ, Policy)**   |        -        |         -         | ✅ **Primary** |
| **Xóa tài khoản (PDPA)**            | ✅ **Primary**  |         -         |  Orchestrate   |

### 2.2. Giải thích vai trò

| Vai trò         | Ý nghĩa                                       |
| --------------- | --------------------------------------------- |
| **Primary**     | Hệ thống sở hữu dữ liệu gốc (Source of Truth) |
| **Cache**       | Lưu tạm để tăng tốc truy vấn                  |
| **Display**     | Chỉ hiển thị, không lưu trữ                   |
| **Aggregate**   | Tổng hợp dữ liệu từ nhiều nguồn               |
| **Orchestrate** | Điều phối luồng xử lý giữa các hệ thống       |

### 2.3. Nguyên tắc phân chia

| Loại dữ liệu            | Hệ thống          | Lý do                                            |
| ----------------------- | ----------------- | ------------------------------------------------ |
| **Dữ liệu khách hàng**  | Backend Loyalty   | Profile, điểm, hạng, quà - thuộc về CRM          |
| **Dữ liệu giao dịch**   | Backend LS Retail | Đơn hàng, địa chỉ, Gift Card - thuộc về commerce |
| **Nội dung & Settings** | Mango CMS         | FAQ, Policy, ngôn ngữ - thuộc về content         |

---

## 3. CHI TIẾT TỪNG CHỨC NĂNG

### 3.1. Thông tin Cá nhân

#### Tại sao Backend Loyalty là Primary?

| Lý do                            | Giải thích                                          |
| -------------------------------- | --------------------------------------------------- |
| **Trung tâm dữ liệu khách hàng** | Loyalty là hệ thống CRM, nơi lưu trữ profile gốc    |
| **Liên kết với hạng thành viên** | Thông tin cá nhân gắn liền với chương trình loyalty |
| **Đồng bộ từ đăng ký**           | Khi user đăng ký, dữ liệu được tạo trên Loyalty     |

#### Phân chia chi tiết

| Nhiệm vụ               | Backend Loyalty |  Mango CMS  |
| ---------------------- | :-------------: | :---------: |
| Lưu trữ profile gốc    |       ✅        |      -      |
| Cập nhật thông tin     |       ✅        |      -      |
| Xác thực OTP đổi email |       ✅        |   Gọi API   |
| Cache profile          |        -        | ✅ (5 phút) |
| Validate format input  |        -        |     ✅      |
| Upload avatar          |        -        |     ✅      |

#### Luồng xem thông tin cá nhân

```
┌──────────┐      ┌───────────┐      ┌─────────────────┐
│ Frontend │─────▶│ Mango CMS │─────▶│ Backend Loyalty │
└──────────┘      └───────────┘      └─────────────────┘
     │                  │                    │
     │   Request        │                    │
     │─────────────────▶│  1. Check cache    │
     │                  │                    │
     │                  │  2. Cache miss?    │
     │                  │      → Gọi Loyalty │
     │                  │───────────────────▶│
     │                  │◀───────────────────│
     │                  │  3. Lưu cache      │
     │◀─────────────────│                    │
```

---

### 3.2. Quản lý Đơn hàng

#### Tại sao Backend LS Retail là Primary?

| Lý do                 | Giải thích                                   |
| --------------------- | -------------------------------------------- |
| **Hệ thống đặt hàng** | LS Retail quản lý toàn bộ quy trình đặt hàng |
| **Tích hợp với kho**  | Đơn hàng liên kết với tồn kho, vận chuyển    |
| **Thanh toán**        | LS Retail xử lý thanh toán và xuất hóa đơn   |

#### Phân chia chi tiết

| Nhiệm vụ                | Backend LS Retail | Backend Loyalty | Mango CMS |
| ----------------------- | :---------------: | :-------------: | :-------: |
| Lưu trữ đơn hàng        |        ✅         |        -        |     -     |
| Trạng thái đơn hàng     |        ✅         |        -        |     -     |
| Chi tiết sản phẩm       |        ✅         |        -        |     -     |
| Proxy API               |         -         |       ✅        |     -     |
| Aggregate hiển thị      |         -         |        -        |    ✅     |
| Mapping text trạng thái |         -         |        -        |    ✅     |

#### Vai trò Mango CMS trong Đơn hàng

| Nhiệm vụ      | Mô tả                                                        |
| ------------- | ------------------------------------------------------------ |
| **Aggregate** | Gộp đơn hàng từ LS Retail với thông tin sản phẩm             |
| **Phân loại** | Chia theo tab: Đang xử lý / Đang giao / Hoàn thành / Đơn hủy |
| **Mapping**   | Chuyển status code thành text hiển thị                       |
| **Format**    | Chuẩn bị dữ liệu cho mobile app                              |

#### Mapping trạng thái đơn hàng

| Status (LS Retail) | Hiển thị        | Tab        |
| ------------------ | --------------- | ---------- |
| pending            | Đang xử lý      | Đang xử lý |
| confirmed          | Đã xác nhận     | Đang xử lý |
| shipping           | Đang giao       | Đang giao  |
| delivered          | Giao thành công | Hoàn thành |
| cancelled          | Đã hủy          | Đơn hủy    |
| refunded           | Đã hoàn tiền    | Đơn hủy    |

---

### 3.3. Tài khoản Trả trước (Gift Card)

#### Tại sao Backend LS Retail là Primary?

| Lý do                      | Giải thích                                                   |
| -------------------------- | ------------------------------------------------------------ |
| **Phương thức thanh toán** | Gift Card là hình thức thanh toán, thuộc hệ thống bán hàng   |
| **Liên kết với đơn hàng**  | Khi thanh toán bằng Gift Card, LS Retail trừ tiền và tạo đơn |

#### Phân chia chi tiết

| Nhiệm vụ                | Backend LS Retail | Mango CMS |
| ----------------------- | :---------------: | :-------: |
| Quản lý số dư thẻ       |        ✅         |     -     |
| Nạp tiền vào thẻ        |        ✅         |     -     |
| Trừ tiền khi thanh toán |        ✅         |     -     |
| Hiển thị tổng số dư     |         -         |    ✅     |

> **Lưu ý:** Chi tiết đầy đủ xem tại Module 12: Quản lý Giftcard

---

### 3.4. Địa chỉ Giao hàng

#### Tại sao Backend LS Retail là Primary?

| Lý do                   | Giải thích                                         |
| ----------------------- | -------------------------------------------------- |
| **Phục vụ đặt hàng**    | Địa chỉ liên quan trực tiếp đến quy trình đặt hàng |
| **Tích hợp vận chuyển** | LS Retail tính phí vận chuyển dựa trên địa chỉ     |

#### Phân chia chi tiết

| Nhiệm vụ             | Backend LS Retail |  Mango CMS   |
| -------------------- | :---------------: | :----------: |
| Lưu trữ địa chỉ      |        ✅         |      -       |
| CRUD địa chỉ         |        ✅         |      -       |
| Set địa chỉ mặc định |        ✅         |      -       |
| Validate format      |         -         |      ✅      |
| Cache danh sách      |         -         | ✅ (10 phút) |

#### Lợi ích cache địa chỉ trên Mango CMS

| Lợi ích        | Giải thích                                             |
| -------------- | ------------------------------------------------------ |
| **Tăng tốc**   | Địa chỉ ít thay đổi, không cần query LS Retail mỗi lần |
| **Giảm tải**   | Mỗi lần checkout không cần gọi LS Retail               |
| **UX tốt hơn** | Danh sách địa chỉ load nhanh                           |

---

### 3.5. Hoa Đất (Điểm tích lũy)

#### Tại sao Backend Loyalty là Primary?

| Lý do                 | Giải thích                                        |
| --------------------- | ------------------------------------------------- |
| **Trung tâm loyalty** | Điểm tích lũy là core của chương trình loyalty    |
| **Liên kết với hạng** | Điểm quyết định hạng thành viên                   |
| **Quy tắc phức tạp**  | Tính điểm, hết hạn điểm, đổi điểm cần logic riêng |

#### Phân chia chi tiết

| Nhiệm vụ                | Backend Loyalty | Mango CMS |
| ----------------------- | :-------------: | :-------: |
| Tính tổng điểm          |       ✅        |     -     |
| Quản lý điểm theo batch |       ✅        |     -     |
| Tính điểm sắp hết hạn   |       ✅        |     -     |
| Hiển thị summary        |        -        |    ✅     |

> **Lưu ý:** Lịch sử điểm chi tiết xem tại Module 3,4,10

---

### 3.6. Quà của Tôi

#### Tại sao Backend Loyalty là Primary?

| Lý do                   | Giải thích                                  |
| ----------------------- | ------------------------------------------- |
| **Phần thưởng loyalty** | Quà tặng là reward của chương trình loyalty |
| **Đổi từ điểm**         | User dùng điểm để đổi quà                   |
| **Quy tắc sử dụng**     | Loyalty quản lý điều kiện sử dụng quà       |

#### Phân chia chi tiết

| Nhiệm vụ         | Backend Loyalty |  Mango CMS  |
| ---------------- | :-------------: | :---------: |
| Quản lý kho quà  |       ✅        |      -      |
| Đổi quà từ điểm  |       ✅        |      -      |
| Quy tắc sử dụng  |       ✅        |      -      |
| Hiển thị summary |        -        |     ✅      |
| Cache danh sách  |        -        | ✅ (2 phút) |

> **Lưu ý:** Chi tiết đầy đủ xem tại Module 5: Kho quà

---

### 3.7. Cài đặt

#### 3.7.1. Cài đặt Ngôn ngữ

**Mango CMS là Primary?**

| Lý do                       | Giải thích                             |
| --------------------------- | -------------------------------------- |
| **Preference local**        | Ngôn ngữ là setting riêng của app      |
| **Không liên quan loyalty** | Không ảnh hưởng đến dữ liệu khách hàng |
| **Áp dụng ngay**            | Đổi ngôn ngữ cần apply ngay lập tức    |

**Ngôn ngữ hỗ trợ:**

- Tiếng Việt (vi) - Mặc định
- Tiếng Anh (en)

#### 3.7.2. Cài đặt Bảo mật

| Chức năng               |  Backend Loyalty  |     Mango CMS     |
| ----------------------- | :---------------: | :---------------: |
| Đổi mật khẩu            | ✅ (lưu password) | Orchestrate + OTP |
| Xác thực sinh trắc học  |         -         |        ✅         |
| Quản lý session         |         -         |        ✅         |
| Đăng xuất thiết bị khác |         -         |        ✅         |

---

### 3.8. Hỗ trợ & Chính sách

#### Mango CMS là Primary?

| Lý do                     | Giải thích                                     |
| ------------------------- | ---------------------------------------------- |
| **Nội dung tĩnh**         | FAQ, Policy là content, CMS là công cụ quản lý |
| **Marketing tự cập nhật** | Không cần developer để thay đổi nội dung       |
| **Đa ngôn ngữ**           | CMS hỗ trợ quản lý content nhiều ngôn ngữ      |

#### Các trang tĩnh

| Trang              | Mô tả               |
| ------------------ | ------------------- |
| FAQ                | Câu hỏi thường gặp  |
| Phản hồi           | Form liên hệ hỗ trợ |
| Điều kiện sử dụng  | Terms & Conditions  |
| Điều khoản eGift   | Quy định Gift Card  |
| Chính sách bảo mật | Privacy Policy      |

> **Chi tiết:** Xem Module 11 - Trang chủ & Content

---

### 3.9. Xóa Tài khoản (PDPA Compliance)

> **Yêu cầu SOW:** B.4 - Bổ sung tính năng liên quan đến luật bảo vệ dữ liệu cá nhân

#### Tại sao Backend Loyalty là Primary?

| Lý do              | Giải thích                                          |
| ------------------ | --------------------------------------------------- |
| **Sở hữu dữ liệu** | Loyalty là nơi lưu trữ dữ liệu cá nhân gốc          |
| **Tuân thủ PDPA**  | Xóa dữ liệu phải thực hiện ở nơi lưu trữ gốc        |
| **Anonymization**  | Logic khử nhận dạng dữ liệu cần thực hiện ở Loyalty |

#### Phân chia chi tiết

| Nhiệm vụ                    | Backend Loyalty | Mango CMS |
| --------------------------- | :-------------: | :-------: |
| Xác thực mật khẩu           |       ✅        |     -     |
| Soft delete account         |       ✅        |     -     |
| Anonymize dữ liệu (30 ngày) |       ✅        |     -     |
| Gửi OTP xác nhận            |       ✅        |  Gọi API  |
| Revoke tất cả session       |        -        |    ✅     |
| Hiển thị confirm dialog     |        -        |    ✅     |

#### Luồng xóa tài khoản

| Bước | Hệ thống        | Trách nhiệm                   |
| ---- | --------------- | ----------------------------- |
| 1    | Mango CMS       | Hiển thị confirm dialog       |
| 2    | Mango CMS       | Yêu cầu nhập password         |
| 3    | Backend Loyalty | Verify password               |
| 4    | Mango CMS       | Yêu cầu nhập OTP              |
| 5    | Backend Loyalty | Verify OTP                    |
| 6    | Backend Loyalty | Soft delete account           |
| 7    | Mango CMS       | Revoke tất cả session         |
| 8    | Backend Loyalty | Queue anonymize job (30 ngày) |

#### Quyền khách hàng theo PDPA

| Quyền                 | Hệ thống xử lý  |
| --------------------- | --------------- |
| Rút lại sự đồng ý     | Backend Loyalty |
| Xóa dữ liệu cá nhân   | Backend Loyalty |
| Khử nhận dạng dữ liệu | Backend Loyalty |
| Chỉnh sửa dữ liệu     | Backend Loyalty |
| Xuất dữ liệu cá nhân  | Backend Loyalty |

---

_Tham khảo chi tiết: Module 3,4,10 (Hạng & Điểm), Module 5 (Kho quà), Module 11 (Trang chủ & Content), Module 12 (Gift Card)_
