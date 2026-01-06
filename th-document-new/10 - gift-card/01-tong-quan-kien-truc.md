# TỔNG QUAN & KIẾN TRÚC MODULE GIFT CARD

> **Phạm vi:** Phát triển tính năng thẻ mua sắm trả trước (Gift Card) cho TH eLIFE App/Web
> **Tham chiếu SOW:** B.3 - Phát triển thêm tính năng thẻ mua sắm trả trước (Giftcard)

---

## 1. Giới Thiệu Tính Năng

Gift Card (Thẻ quà tặng) là tính năng thẻ mua sắm trả trước, cho phép khách hàng thành viên TH eLIFE thực hiện các hoạt động:

- **Mua thẻ mới:** Khách hàng có thể mua thẻ gift card online với các mệnh giá định sẵn hoặc tùy chỉnh
- **Biếu tặng thẻ:** Mua thẻ và gửi tặng cho người thân, bạn bè thông qua SMS/ZNS/Email
- **Kích hoạt thẻ:** Kích hoạt cả thẻ online (mua qua app) và thẻ cứng (mua tại cửa hàng)
- **Quản lý thẻ:** Xem danh sách thẻ, số dư, trạng thái, lịch sử giao dịch
- **Nạp tiền:** Nạp thêm tiền vào thẻ đang sử dụng
- **Thanh toán:** Sử dụng thẻ để thanh toán cả online (qua app/web) và offline (tại cửa hàng TrueMart)

---

## 2. Mục Tiêu Nghiệp Vụ

### 2.1. Đối Với Khách Hàng

- Cung cấp phương thức thanh toán tiện lợi, không cần mang tiền mặt
- Tạo kênh biếu tặng ý nghĩa cho người thân, bạn bè
- Quản lý chi tiêu hiệu quả thông qua việc nạp trước số dư
- Tích hợp liền mạch với hệ sinh thái TH eLIFE

### 2.2. Đối Với TH Milk

- Tăng dòng tiền trước khi khách hàng mua sắm
- Tăng tương tác và độ gắn kết với khách hàng thành viên
- Mở rộng kênh tiếp cận khách hàng mới thông qua tính năng biếu tặng
- Thu thập dữ liệu hành vi mua sắm để tối ưu marketing

---

## 3. Phân Loại Gift Card

### 3.1. Thẻ Online (Thẻ Điện Tử)

| Đặc Điểm | Mô Tả |
|----------|-------|
| **Hình thức** | Thẻ điện tử, không có hình thức vật lý |
| **Nguồn phát hành** | Mua qua TH eLIFE App hoặc Website |
| **Cách nhận thẻ** | Mã thẻ và mã PIN được gửi qua SMS/ZNS/Email |
| **Cách sử dụng** | Kích hoạt trên app, thanh toán bằng QR code hoặc mã thẻ |
| **Đối tượng** | Mua cho bản thân hoặc biếu tặng người khác |

### 3.2. Thẻ Cứng (Thẻ Vật Lý)

| Đặc Điểm | Mô Tả |
|----------|-------|
| **Hình thức** | Thẻ nhựa vật lý có in barcode/QR |
| **Nguồn phát hành** | Mua tại cửa hàng TrueMart hoặc nhận từ sự kiện |
| **Cách nhận thẻ** | Nhận trực tiếp thẻ vật lý kèm mã PIN (cào để xem) |
| **Cách sử dụng** | Kích hoạt trên app, thanh toán bằng barcode trên thẻ hoặc QR trên app |
| **Đối tượng** | Khách hàng mua tại cửa hàng hoặc nhận quà tặng |

---

## 4. Kiến Trúc Hệ Thống

### 4.1. Sơ Đồ Tổng Quan

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           FRONTEND (User Interface)                     │
│                  TH eLIFE App (Mobile) / TH eLIFE Web                   │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                          MANGO CMS (Middleware)                         │
│                                                                         │
│  Vai trò: Điều phối API, quản lý phiên, cache, gửi thông báo            │
│  - Nhận request từ Frontend                                             │
│  - Điều phối sang các hệ thống backend phù hợp                          │
│  - Xử lý thanh toán qua Payment Gateway                                 │
│  - Gửi thông báo SMS/ZNS/Email/Push Notification                        │
│  - Cache dữ liệu để tối ưu hiệu suất                                    │
└──────────────┬─────────────────────────────────────┬────────────────────┘
               │                                     │
               ▼                                     ▼
┌──────────────────────────────────┐   ┌──────────────────────────────────┐
│       LS RETAIL (Primary)        │   │      PAYMENT GATEWAY             │
│                                  │   │                                  │
│  Vai trò: Quản lý Gift Card      │   │  Vai trò: Xử lý thanh toán       │
│  - Source of truth cho gift card │   │  - Napas, OnePay                 │
│  - Tạo mới thẻ                   │   │  - Momo, ZaloPay                 │
│  - Kích hoạt thẻ                 │   │  - QR Pay (VNPAY/VietQR)         │
│  - Quản lý số dư                 │   │                                  │
│  - Xử lý giao dịch               │   │                                  │
│  - Lưu lịch sử giao dịch         │   │                                  │
└──────────────────────────────────┘   └──────────────────────────────────┘
               │
               ▼
┌──────────────────────────────────┐
│         POS (Cửa hàng)           │
│                                  │
│  Vai trò: Thanh toán offline     │
│  - Quét barcode/QR gift card     │
│  - Xác thực với LS Retail        │
│  - Trừ tiền từ thẻ               │
└──────────────────────────────────┘
```

### 4.2. Giải Thích Luồng Dữ Liệu

1. **Khách hàng** thao tác trên TH eLIFE App hoặc Website
2. **Frontend** gửi request đến **Mango CMS**
3. **Mango CMS** kiểm tra xác thực, điều phối request đến hệ thống phù hợp:
   - Các thao tác về gift card → **LS Retail**
   - Thanh toán mua thẻ/nạp tiền → **Payment Gateway**
4. **LS Retail** xử lý nghiệp vụ gift card và trả kết quả
5. **Mango CMS** tổng hợp kết quả, gửi thông báo (nếu cần), và trả về Frontend
6. Khi thanh toán tại cửa hàng, **POS** quét mã và xác thực trực tiếp với **LS Retail**

---

## 5. Phân Chia Trách Nhiệm Hệ Thống

### 5.1. LS Retail - Hệ Thống Chủ Đạo (Primary System)

LS Retail là **source of truth** (nguồn dữ liệu chính xác) cho toàn bộ nghiệp vụ Gift Card vì gift card liên quan trực tiếp đến thanh toán và giao dịch tài chính.

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Quản lý vòng đời thẻ** | Tạo mới, kích hoạt, khóa, hết hạn |
| **Quản lý số dư** | Lưu trữ và cập nhật số dư thẻ chính xác |
| **Xử lý giao dịch** | Nạp tiền, trừ tiền khi thanh toán |
| **Lưu lịch sử** | Ghi nhận toàn bộ lịch sử giao dịch |
| **Xác thực thẻ** | Kiểm tra mã thẻ, mã PIN khi kích hoạt |
| **Tích hợp POS** | Xử lý thanh toán offline tại cửa hàng |

**API LS Retail cung cấp:**
- Tạo mới thẻ Giftcard online
- Kích hoạt thẻ Giftcard
- Nạp tiền thẻ Giftcard online
- Tạm giữ thanh toán thẻ Giftcard
- Hủy thanh toán thẻ Giftcard
- Truy vấn danh sách thẻ Giftcard
- Truy vấn lịch sử giao dịch thẻ Giftcard

### 5.2. Mango CMS - Tầng Điều Phối (Middleware)

Mango CMS đóng vai trò **middleware**, không lưu trữ dữ liệu nghiệp vụ chính của gift card mà chỉ điều phối và hỗ trợ.

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Điều phối API** | Nhận request từ Frontend, gọi API LS Retail tương ứng |
| **Xử lý thanh toán** | Tích hợp với Payment Gateway khi mua thẻ/nạp tiền |
| **Gửi thông báo** | SMS/ZNS/Email mã kích hoạt, Push notification giao dịch |
| **Quản lý cache** | Cache danh sách thẻ, chi tiết thẻ để tối ưu hiệu suất |
| **Tạo QR Code** | Generate QR code cho thanh toán offline |
| **Quản lý phiên** | Xác thực user, quản lý session |

**Mango CMS KHÔNG làm:**
- Không lưu trữ số dư thẻ (chỉ cache tạm thời)
- Không lưu mã PIN (chỉ truyền qua khi kích hoạt)
- Không xử lý logic nghiệp vụ gift card (chỉ điều phối)

### 5.3. Payment Gateway - Cổng Thanh Toán

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Xử lý thanh toán mua thẻ** | Thanh toán khi khách hàng mua gift card mới |
| **Xử lý thanh toán nạp tiền** | Thanh toán khi khách hàng nạp thêm tiền vào thẻ |
| **Hỗ trợ nhiều phương thức** | Napas, OnePay, Momo, ZaloPay, QR Pay |

### 5.4. Backend Loyalty

Backend Loyalty **KHÔNG tham gia** trực tiếp vào nghiệp vụ Gift Card vì gift card là nghiệp vụ thanh toán, thuộc phạm vi quản lý của LS Retail.

Tuy nhiên, Loyalty vẫn liên quan gián tiếp:
- Xác thực thông tin khách hàng thành viên
- Cung cấp thông tin member ID để liên kết thẻ

---

## 6. Nguyên Tắc Thiết Kế

### 6.1. Data Ownership (Quyền Sở Hữu Dữ Liệu)

| Loại Dữ Liệu | Hệ Thống Sở Hữu | Ghi Chú |
|--------------|-----------------|---------|
| Thông tin thẻ (số dư, trạng thái, HSD) | LS Retail | Source of truth |
| Lịch sử giao dịch | LS Retail | Source of truth |
| Mã thẻ, mã PIN | LS Retail | Không lưu tại Mango CMS |
| Session user | Mango CMS | Xác thực người dùng |
| Cache dữ liệu thẻ | Mango CMS | Tạm thời, có TTL |
| Thông báo đã gửi | Mango CMS | Log notification |

### 6.2. Luồng Xử Lý Chung

```
Bước 1: Frontend gửi request → Mango CMS
Bước 2: Mango CMS xác thực user session
Bước 3: Mango CMS gọi API LS Retail tương ứng
Bước 4: LS Retail xử lý nghiệp vụ, trả kết quả
Bước 5: Mango CMS xử lý hậu kỳ (cache, notification)
Bước 6: Mango CMS trả response cho Frontend
```

### 6.3. Cache Strategy

| Dữ Liệu | Thời Gian Cache | Khi Nào Xóa Cache |
|---------|-----------------|-------------------|
| Danh sách gift card | 2 phút | Khi có thay đổi trạng thái thẻ |
| Chi tiết gift card | 1 phút | Khi có giao dịch hoặc đổi trạng thái |
| Lịch sử giao dịch | Không cache | Luôn lấy real-time |

---

## 7. Tổng Quan Các Chức Năng

| STT | Chức Năng | LS Retail | Mango CMS | Payment Gateway |
|-----|-----------|:---------:|:---------:|:---------------:|
| 1 | Quản lý danh sách thẻ | Cung cấp dữ liệu | Điều phối, cache | - |
| 2 | Chi tiết gift card | Cung cấp dữ liệu | Điều phối, tạo QR | - |
| 3 | Mua gift card online | Tạo thẻ mới | Điều phối, gửi mã | Xử lý thanh toán |
| 4 | Biếu tặng gift card | Tạo thẻ mới | Điều phối, gửi mã đến người nhận | Xử lý thanh toán |
| 5 | Kích hoạt gift card | Kích hoạt, liên kết member | Điều phối, gửi thông báo | - |
| 6 | Nạp tiền gift card | Cộng tiền, gia hạn | Điều phối, gửi thông báo | Xử lý thanh toán |
| 7 | Sử dụng online | Trừ tiền, ghi lịch sử | Điều phối checkout | - |
| 8 | Sử dụng offline | Trừ tiền, ghi lịch sử | Gửi thông báo giao dịch | - |
| 9 | Lịch sử giao dịch | Cung cấp dữ liệu | Điều phối, hiển thị | - |
| 10 | Khóa thẻ | Đổi trạng thái | Điều phối, gửi thông báo | - |
| 11 | Hạn sử dụng & cảnh báo | Quản lý HSD | Gửi cảnh báo | - |

---

## 8. Tham Chiếu Tài Liệu Chi Tiết

| Chức Năng | File Tài Liệu |
|-----------|---------------|
| Quản lý danh sách & Chi tiết | [02-quan-ly-danh-sach-chi-tiet.md](./02-quan-ly-danh-sach-chi-tiet.md) |
| Mua & Biếu tặng | [03-mua-va-bieu-tang.md](./03-mua-va-bieu-tang.md) |
| Kích hoạt & Nạp tiền | [04-kich-hoat-va-nap-tien.md](./04-kich-hoat-va-nap-tien.md) |
| Sử dụng Gift Card | [05-su-dung-gift-card.md](./05-su-dung-gift-card.md) |
| Lịch sử, Khóa thẻ, Hạn sử dụng | [06-lich-su-khoa-the-han-su-dung.md](./06-lich-su-khoa-the-han-su-dung.md) |
| Tích hợp LS Retail & Thông báo | [07-tich-hop-va-thong-bao.md](./07-tich-hop-va-thong-bao.md) |

---

*Tài liệu này là phần tổng quan kiến trúc cho Module Gift Card, mô tả vai trò và trách nhiệm của từng hệ thống trong việc triển khai tính năng thẻ mua sắm trả trước.*
