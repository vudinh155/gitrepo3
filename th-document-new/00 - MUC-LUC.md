# MỤC LỤC TÀI LIỆU ĐỀ XUẤT DỰ ÁN

## TH eLIFE - Nâng Cấp Hệ Thống Web & Mobile App

---

## Cấu Trúc Tài Liệu

```
📁 TH-PROPOSAL/
│
├── 00. Mục lục
├── 01. Tổng quan kiến trúc Backend
├── 02. Đăng ký - Đăng nhập
├── 03. Quản lý tài khoản
├── 04. Hạng thành viên & Thông báo
├── 05. Kho quà
├── 06. Campaign Loyalty & Tặng Hoa Đất
├── 07. Sản phẩm & Đặt hàng
├── 08. Cửa hàng
├── 09. Trang chủ & Content
├── 10. Gift Card (7 tài liệu chi tiết)
└── 11. Hạ tầng
```

---

## Chi Tiết Mục Lục

### 01. Tổng Quan Kiến Trúc Backend
- Kiến trúc 3 thành phần: Frontend → Mango CMS → Backend (Loyalty + LS Retail)
- Phân chia trách nhiệm và quyền sở hữu dữ liệu giữa các hệ thống

---

### 02. Đăng Ký - Đăng Nhập
- Đăng ký bằng SĐT, xác thực OTP, gán hạng Bạc mặc định
- Đăng nhập bằng Email/SĐT + Mật khẩu
- Quên/đổi mật khẩu, sinh trắc học (Face ID/Touch ID)

---

### 03. Quản Lý Tài Khoản
- Thông tin cá nhân, địa chỉ giao hàng
- Quản lý đơn hàng, tài khoản trả trước (Gift Card)
- Cài đặt ngôn ngữ/bảo mật, xóa tài khoản (PDPA)

---

### 04. Hạng Thành Viên & Thông Báo
- Thông tin hạng, tiến trình, quyền lợi
- Lịch sử hạng và điểm (Hoa Đất)
- Push notification, in-app notification, webhook

---

### 05. Kho Quà
- Quà của tôi, Kho quà TH Reward
- Đổi điểm lấy quà (trừ điểm FIFO)
- Khảo sát khi đổi quà

---

### 06. Campaign Loyalty & Tặng Hoa Đất
- Vòng quay may mắn, phát thưởng chọn quà
- Giới thiệu bạn bè, tích lũy mua hàng
- Tặng Hoa Đất cho bạn bè

---

### 07. Sản Phẩm & Đặt Hàng
- Danh sách/chi tiết sản phẩm, tìm kiếm, lọc
- Giỏ hàng, thanh toán (Momo, ZaloPay, VNPay, COD...)
- Flash Sale, đánh giá sản phẩm

---

### 08. Cửa Hàng
- Danh sách TH True Mart, tìm cửa hàng gần nhất
- Chi tiết cửa hàng, Google Maps, chỉ đường

---

### 09. Trang Chủ & Content
- Trang chủ cá nhân hóa, banner (slider, popup)
- Trang tĩnh (FAQ, chính sách), đa ngôn ngữ
- Flash Sale display, Product Catalog

---

### 10. Gift Card (7 tài liệu)

| # | Nội dung |
|---|----------|
| 01 | Tổng quan kiến trúc |
| 02 | Quản lý danh sách & chi tiết |
| 03 | Mua và biếu tặng |
| 04 | Kích hoạt và nạp tiền |
| 05 | Sử dụng Gift Card (Online/Offline) |
| 06 | Lịch sử, khóa thẻ, hạn sử dụng |
| 07 | Tích hợp và thông báo |

---

### 11. Hạ Tầng
- Kiến trúc 3-Tier: DMZ → Application → Database
- Stack: Node.js 20, Fastify, MongoDB 7.0, Redis, Elasticsearch
- Phương án: On-Cloud / On-Premise / Hybrid (đề xuất)
- HA 99.9%, bảo mật, giám sát

---

## Ma Trận Phân Chia Trách Nhiệm

| Module | Backend Loyalty | Backend LS Retail | Mango CMS |
|--------|:---------------:|:-----------------:|:---------:|
| Đăng ký/Đăng nhập | ✅ Primary | - | Orchestrate |
| Quản lý tài khoản | ✅ Primary | Đơn hàng, Địa chỉ | Cache |
| Hạng thành viên | ✅ Primary | - | Cache + Notify |
| Kho quà | ✅ Primary | - | Cache |
| Campaign Loyalty | ✅ Primary | Purchase data | Notify |
| Sản phẩm | - | ✅ Source | ✅ Primary (Cache) |
| Đơn hàng | Cộng điểm | ✅ Primary | Orchestrate |
| Cửa hàng | - | Source | ✅ Primary (Sync) |
| Trang chủ/Content | User info | Sản phẩm | ✅ Primary |
| Gift Card | - | ✅ Primary | Orchestrate |
| Thông báo | Webhook | Webhook | ✅ Primary |

---

