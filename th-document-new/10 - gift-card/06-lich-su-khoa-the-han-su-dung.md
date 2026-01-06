# LỊCH SỬ GIAO DỊCH, KHÓA THẺ & HẠN SỬ DỤNG

> **Tham chiếu SOW:** B.3 - Quản lý thẻ
> **Chức năng:** Xem lịch sử giao dịch, khóa thẻ khi cần, quản lý hạn sử dụng

---

## 1. Lịch Sử Giao Dịch Gift Card

### 1.1. Mô Tả Chức Năng

Khách hàng có thể xem toàn bộ lịch sử giao dịch của từng gift card, bao gồm:

- Các lần nạp tiền (+)
- Các lần chi tiêu (-)
- Các lần hoàn tiền (nếu có)

Hỗ trợ lọc và phân loại giao dịch theo nhiều tiêu chí.

### 1.2. Phân Chia Trách Nhiệm Hệ Thống

#### LS Retail - Nhiệm Vụ Chính

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Lưu trữ lịch sử giao dịch** | Source of truth cho toàn bộ lịch sử |
| **Cung cấp API truy vấn** | API lấy lịch sử giao dịch theo card ID |
| **Hỗ trợ phân trang** | Trả về theo page để xử lý lịch sử dài |
| **Cung cấp thông tin chi tiết** | Loại GD, số tiền, thời gian, mã đơn hàng, cửa hàng |

#### Mango CMS - Nhiệm Vụ Điều Phối

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Nhận request từ Frontend** | Xác thực user, card ID, filter params |
| **Gọi API LS Retail** | Gọi API truy vấn lịch sử giao dịch |
| **Không cache** | Lịch sử giao dịch luôn lấy real-time |
| **Format response** | Định dạng dữ liệu cho Frontend hiển thị |

### 1.3. Các Loại Giao Dịch

| Loại Giao Dịch | Ký Hiệu | Mô Tả |
|----------------|---------|-------|
| **Nạp tiền** | + | Nạp thêm tiền vào thẻ |
| **Chi tiêu online** | - | Thanh toán đơn hàng qua app/web |
| **Chi tiêu offline** | - | Thanh toán tại cửa hàng TrueMart |
| **Hoàn tiền** | + | Hoàn tiền khi đơn hàng bị hủy |

### 1.4. Thông Tin Mỗi Giao Dịch

| Thông Tin | Mô Tả | Nguồn |
|-----------|-------|-------|
| **Loại giao dịch** | Nạp tiền / Chi tiêu / Hoàn tiền | LS Retail |
| **Số tiền** | Số tiền (+/-) với định dạng VND | LS Retail |
| **Ngày giờ** | Timestamp giao dịch | LS Retail |
| **Mã đơn hàng** | Mã đơn hàng (nếu là chi tiêu) | LS Retail |
| **Loại thanh toán** | Online / Tại cửa hàng | LS Retail |
| **Tên cửa hàng** | Tên cửa hàng (nếu offline) | LS Retail |
| **Phương thức nạp** | Napas/Momo/... (nếu là nạp tiền) | LS Retail |

### 1.5. Bộ Lọc Lịch Sử

| Bộ Lọc | Options |
|--------|---------|
| **Loại giao dịch** | Tất cả / Nạp tiền / Chi tiêu |
| **Khoảng thời gian** | 7 ngày qua / 30 ngày qua / Tùy chọn |

### 1.6. Luồng Hoạt Động

```
┌────────────────┐
│   Khách hàng   │
│ vào Lịch sử    │
│ giao dịch      │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Chọn bộ lọc:  │
│  - Tab: Tất cả │
│    /Nạp/Chi    │
│  - Thời gian   │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  gọi API       │
│  LS Retail     │
│  (không cache) │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   LS Retail    │
│  truy vấn DB   │
│  trả về danh   │
│  sách GD       │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  format data   │
│  trả Frontend  │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Hiển thị danh │
│  sách giao dịch│
│  với phân trang│
└────────────────┘
```

---

## 2. Khóa Thẻ Gift Card

### 2.1. Mô Tả Chức Năng

Khách hàng có thể chủ động khóa thẻ gift card của mình trong trường hợp:

- Nghi ngờ bị lộ thông tin thẻ
- Mất điện thoại có đăng nhập app
- Không muốn tiếp tục sử dụng thẻ

Sau khi khóa:
- Thẻ không thể sử dụng để thanh toán
- Số dư vẫn được giữ nguyên
- Cần liên hệ CSKH để mở khóa

### 2.2. Phân Chia Trách Nhiệm Hệ Thống

#### LS Retail - Nhiệm Vụ Chính

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Cập nhật trạng thái thẻ** | Chuyển trạng thái sang "Bị khóa" |
| **Chặn giao dịch** | Từ chối mọi giao dịch với thẻ bị khóa |
| **Giữ nguyên số dư** | Không trừ hay mất số dư khi khóa |
| **Ghi log khóa thẻ** | Ghi nhận thời điểm và lý do khóa |
| **Mở khóa (Admin)** | Admin có thể mở khóa qua backend |

#### Mango CMS - Nhiệm Vụ Điều Phối

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Hiển thị nút khóa** | Nút "Khóa thẻ" trên màn hình chi tiết |
| **Hiển thị cảnh báo** | Popup xác nhận với thông tin rủi ro |
| **Yêu cầu xác thực** | Nhập mật khẩu để xác nhận khóa |
| **Gọi API LS Retail** | Gọi API khóa thẻ |
| **Gửi thông báo** | Push notification xác nhận đã khóa |
| **Invalidate cache** | Xóa cache để cập nhật trạng thái |

### 2.3. Điều Kiện Khóa Thẻ

| Điều Kiện | Yêu Cầu |
|-----------|---------|
| **Thẻ đã liên kết** | Chỉ khóa được thẻ đã liên kết với tài khoản |
| **Trạng thái hiện tại** | "Đã kích hoạt" hoặc "Hết tiền" |
| **Xác thực** | Phải nhập mật khẩu tài khoản để xác nhận |

### 2.4. Luồng Khóa Thẻ

```
┌────────────────┐
│   Khách hàng   │
│ vào chi tiết   │
│ thẻ → Khóa thẻ │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  hiển thị      │
│  popup cảnh    │
│  báo:          │
│  - Thẻ không   │
│    dùng được   │
│  - Số dư giữ   │
│    nguyên      │
│  - Mở khóa qua │
│    CSKH        │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Khách nhập    │
│  mật khẩu      │
│  xác nhận      │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  xác thực      │
│  mật khẩu      │
└───────┬────────┘
        │
        ├─────────────────────────────┐
        │                             │
     ĐÚNG MK                       SAI MK
        │                             │
        ▼                             ▼
┌────────────────┐           ┌────────────────┐
│   Mango CMS    │           │  Thông báo     │
│  gọi API       │           │  mật khẩu sai  │
│  LS Retail     │           │  thử lại       │
│  khóa thẻ      │           │                │
└───────┬────────┘           └────────────────┘
        │
        ▼
┌────────────────┐
│   LS Retail    │
│  - Đổi trạng   │
│    thái thẻ    │
│  - Ghi log     │
│    khóa        │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  - Invalidate  │
│    cache       │
│  - Push notif  │
│    "Thẻ đã bị  │
│    khóa"       │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Hiển thị thông│
│  báo thành công│
│  + Hướng dẫn   │
│  mở khóa       │
└────────────────┘
```

### 2.5. Quy Trình Mở Khóa Thẻ

Mở khóa thẻ chỉ được thực hiện thông qua bộ phận Chăm sóc khách hàng (CSKH):

```
┌────────────────┐
│   Khách hàng   │
│  liên hệ       │
│  hotline CSKH  │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│     CSKH       │
│  xác minh      │
│  thông tin:    │
│  - CMND/CCCD   │
│  - SĐT đăng ký │
│  - Mã thẻ      │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│     Admin      │
│  mở khóa thẻ   │
│  trong backend │
│  LS Retail     │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   LS Retail    │
│  đổi trạng     │
│  thái về "Đã   │
│  kích hoạt"    │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  nhận webhook  │
│  → Push notif  │
│  "Thẻ đã được  │
│  mở khóa"      │
└────────────────┘
```

### 2.6. Nguyên Tắc Khóa Thẻ

| Nguyên Tắc | Chi Tiết |
|------------|----------|
| **Chỉ áp dụng thẻ đã liên kết** | Thẻ chưa kích hoạt không cần khóa |
| **Không hoàn tiền mặt** | Số dư được giữ trên thẻ, không chuyển thành tiền mặt |
| **Mở khóa qua CSKH** | Đảm bảo an ninh, tránh trường hợp mở khóa trái phép |

---

## 3. Hạn Sử Dụng & Cảnh Báo

### 3.1. Quy Định Hạn Sử Dụng

| Thời Điểm | Hạn Sử Dụng |
|-----------|-------------|
| **Khi tạo thẻ mới** | 12 tháng kể từ ngày tạo |
| **Khi nạp tiền** | Gia hạn +12 tháng từ ngày nạp |

**Ví dụ:**

| Ngày | Sự Kiện | HSD |
|------|---------|-----|
| 01/01/2025 | Mua thẻ mới | 01/01/2026 |
| 15/03/2025 | Nạp tiền | 15/03/2026 |
| 01/07/2025 | Nạp tiền | 01/07/2026 |

### 3.2. Phân Chia Trách Nhiệm Hệ Thống

#### LS Retail - Nhiệm Vụ Chính

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Quản lý HSD** | Lưu trữ và cập nhật hạn sử dụng |
| **Tính toán gia hạn** | Cộng 12 tháng khi nạp tiền |
| **Phát sinh sự kiện hết hạn** | Gửi webhook khi thẻ sắp hết hạn / đã hết hạn |
| **Chặn thẻ hết hạn** | Từ chối giao dịch với thẻ đã quá HSD |

#### Mango CMS - Nhiệm Vụ Điều Phối

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Nhận webhook sắp hết hạn** | Từ LS Retail |
| **Gửi cảnh báo** | Push notification + Email trước 30 ngày, 7 ngày |
| **Hiển thị cảnh báo trên UI** | Badge/Icon cho thẻ sắp hết hạn |
| **Hiển thị gợi ý** | Gợi ý nạp tiền để gia hạn hoặc mua thẻ mới |

### 3.3. Cơ Chế Cảnh Báo

| Thời Điểm | Kênh Thông Báo | Nội Dung |
|-----------|----------------|----------|
| **Trước 30 ngày** | Push + Email | "Thẻ [mã thẻ] sẽ hết hạn trong 30 ngày. Nạp tiền để gia hạn." |
| **Trước 7 ngày** | Push + Email + SMS | "Thẻ [mã thẻ] sẽ hết hạn trong 7 ngày. Nạp tiền ngay để không mất số dư." |
| **Ngày hết hạn** | Push + Email + SMS | "Thẻ [mã thẻ] đã hết hạn. Số dư [số dư] không thể sử dụng." |

### 3.4. Luồng Xử Lý Hạn Sử Dụng

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      CẢNH BÁO TRƯỚC 30 NGÀY                             │
└─────────────────────────────────────────────────────────────────────────┘

┌────────────────┐
│   LS Retail    │
│  kiểm tra HSD  │
│  (job hàng     │
│   ngày)        │
└───────┬────────┘
        │
        │  Còn 30 ngày?
        ▼
┌────────────────┐
│   LS Retail    │
│  gửi webhook   │
│  "giftcard.    │
│   expiring"    │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  - Push notif  │
│  - Email nhắc  │
│    nhở         │
└────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                      CẢNH BÁO TRƯỚC 7 NGÀY                              │
└─────────────────────────────────────────────────────────────────────────┘

┌────────────────┐
│   LS Retail    │
│  kiểm tra HSD  │
└───────┬────────┘
        │
        │  Còn 7 ngày?
        ▼
┌────────────────┐
│   LS Retail    │
│  gửi webhook   │
│  "giftcard.    │
│   expiring"    │
│  (urgent)      │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  - Push notif  │
│    (urgent)    │
│  - Email nhắc  │
│  - SMS nhắc    │
└────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                       KHI THẺ HẾT HẠN                                   │
└─────────────────────────────────────────────────────────────────────────┘

┌────────────────┐
│   LS Retail    │
│  kiểm tra HSD  │
└───────┬────────┘
        │
        │  Đã hết hạn?
        ▼
┌────────────────┐
│   LS Retail    │
│  - Đổi trạng   │
│    thái thẻ    │
│    → "Hết hạn" │
│  - Gửi webhook │
│    "giftcard.  │
│     expired"   │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  - Invalidate  │
│    cache       │
│  - Push notif  │
│    "Thẻ đã     │
│    hết hạn"    │
│  - Email thông │
│    báo         │
│  - SMS thông   │
│    báo         │
└────────────────┘
```

### 3.5. Xử Lý Thẻ Hết Hạn

| Xử Lý | Chi Tiết |
|-------|----------|
| **Trạng thái** | Chuyển sang "Hết hạn" |
| **Sử dụng** | Không cho phép thanh toán |
| **Số dư** | Không thể sử dụng (theo quy định) |
| **Hiển thị** | Vẫn hiển thị trong danh sách với badge "Hết hạn" |
| **Actions** | Hiển thị nút "Mua thẻ mới" |

---

## 4. Tóm Tắt Phân Công

### Lịch Sử Giao Dịch

| Hệ Thống | Vai Trò | Nhiệm Vụ Cụ Thể |
|----------|---------|-----------------|
| **LS Retail** | Source of Truth | Lưu trữ và cung cấp lịch sử giao dịch |
| **Mango CMS** | Middleware | Điều phối API, format dữ liệu (không cache) |
| **Loyalty** | Không tham gia | - |

### Khóa Thẻ

| Hệ Thống | Vai Trò | Nhiệm Vụ Cụ Thể |
|----------|---------|-----------------|
| **LS Retail** | Primary | Đổi trạng thái thẻ, chặn giao dịch, mở khóa (admin) |
| **Mango CMS** | Middleware | Hiển thị UI, xác thực mật khẩu, gọi API, gửi thông báo |
| **Loyalty** | Không tham gia | - |

### Hạn Sử Dụng & Cảnh Báo

| Hệ Thống | Vai Trò | Nhiệm Vụ Cụ Thể |
|----------|---------|-----------------|
| **LS Retail** | Primary | Quản lý HSD, gia hạn, phát webhook cảnh báo/hết hạn |
| **Mango CMS** | Notification | Nhận webhook, gửi Push/Email/SMS cảnh báo |
| **Loyalty** | Không tham gia | - |

---

*Tài liệu này mô tả chi tiết chức năng Lịch sử giao dịch, Khóa thẻ và Quản lý hạn sử dụng Gift Card, bao gồm phân chia trách nhiệm giữa các hệ thống backend.*
