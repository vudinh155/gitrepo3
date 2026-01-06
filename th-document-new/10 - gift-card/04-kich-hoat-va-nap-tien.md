# KÍCH HOẠT & NẠP TIỀN GIFT CARD

> **Tham chiếu SOW:** B.3 - Kích hoạt thẻ & Nạp tiền thẻ online
> **Chức năng:** Kích hoạt thẻ để sử dụng và nạp thêm tiền vào thẻ đang có

---

## 1. Kích Hoạt Gift Card

### 1.1. Mô Tả Chức Năng

Kích hoạt là bước bắt buộc trước khi sử dụng gift card. Chức năng này áp dụng cho:

- **Thẻ online:** Thẻ mua cho bản thân hoặc được tặng qua app/web
- **Thẻ cứng:** Thẻ vật lý mua tại cửa hàng TrueMart hoặc nhận từ sự kiện

Sau khi kích hoạt, thẻ sẽ được liên kết với tài khoản member và có thể sử dụng để thanh toán.

### 1.2. Phân Chia Trách Nhiệm Hệ Thống

#### LS Retail - Nhiệm Vụ Chính

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Xác thực mã thẻ** | Kiểm tra mã thanh toán có tồn tại và hợp lệ |
| **Xác thực mã PIN** | Kiểm tra mã bí mật (PIN) có đúng |
| **Kiểm tra trạng thái** | Đảm bảo thẻ chưa được kích hoạt trước đó |
| **Liên kết với member** | Gắn thẻ với member ID của người kích hoạt |
| **Cập nhật trạng thái** | Chuyển trạng thái từ "Chưa kích hoạt" sang "Đã kích hoạt" |
| **Ghi nhận ngày kích hoạt** | Lưu timestamp kích hoạt để tính hạn sử dụng |
| **Giới hạn nhập sai** | Khóa thẻ sau 3-5 lần nhập sai mã PIN |

#### Mango CMS - Nhiệm Vụ Điều Phối

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Hiển thị form kích hoạt** | Form nhập mã thẻ, mã PIN, số điện thoại |
| **Hỗ trợ quét QR/Barcode** | Cho phép quét mã QR hoặc barcode trên thẻ cứng |
| **Xác thực user session** | Đảm bảo user đã đăng nhập |
| **Gọi API LS Retail** | Gọi API kích hoạt với mã thẻ + PIN + member ID |
| **Xử lý kết quả** | Hiển thị thông báo thành công/thất bại |
| **Gửi thông báo** | Push notification xác nhận kích hoạt thành công |
| **Invalidate cache** | Xóa cache danh sách thẻ để cập nhật |

#### Backend Loyalty

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Không tham gia trực tiếp** | Kích hoạt là nghiệp vụ của LS Retail |
| **Cung cấp member ID** | Member ID để liên kết thẻ |

### 1.3. Các Trường Hợp Kích Hoạt

| Nguồn Thẻ | Cách Nhận Mã | Cách Kích Hoạt |
|-----------|--------------|----------------|
| **Mua online cho bản thân** | SMS/ZNS/Email | Nhập mã thẻ + PIN trên app |
| **Được tặng online** | SMS/ZNS/Email | Nhập mã thẻ + PIN trên app |
| **Mua thẻ cứng tại cửa hàng** | In trên thẻ | Quét barcode hoặc nhập mã + PIN |
| **Nhận thẻ cứng từ sự kiện** | In trên thẻ | Quét barcode hoặc nhập mã + PIN |

### 1.4. Validation Kích Hoạt

| Điều Kiện | Kết Quả |
|-----------|---------|
| Mã thẻ hợp lệ + PIN đúng + Chưa kích hoạt | Cho phép kích hoạt |
| Mã thẻ không tồn tại | Báo lỗi "Mã thẻ không hợp lệ" |
| PIN sai | Báo lỗi "Mã PIN không đúng" + đếm số lần sai |
| Thẻ đã kích hoạt rồi | Báo lỗi "Thẻ đã được kích hoạt" |
| Thẻ đã bị khóa | Báo lỗi "Thẻ đã bị khóa, liên hệ CSKH" |
| Nhập sai PIN 5 lần | Khóa thẻ tự động |

### 1.5. Luồng Hoạt Động

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        GIAI ĐOẠN 1: NHẬP MÃ                             │
└─────────────────────────────────────────────────────────────────────────┘

┌────────────────┐
│   Khách hàng   │
│ vào màn hình   │
│  Kích hoạt     │
└───────┬────────┘
        │
        ├─────────────────────────────┐
        │                             │
        ▼                             ▼
┌────────────────┐           ┌────────────────┐
│   Nhập mã thẻ  │           │  Quét QR/      │
│   thủ công     │           │  Barcode       │
└───────┬────────┘           └───────┬────────┘
        │                             │
        └─────────────┬───────────────┘
                      │
                      ▼
              ┌────────────────┐
              │  Nhập mã PIN   │
              │   (bí mật)     │
              └───────┬────────┘
                      │
                      ▼
              ┌────────────────┐
              │ Nhập SĐT liên  │
              │ kết (tùy chọn) │
              └───────┬────────┘
                      │
                      ▼
              ┌────────────────┐
              │ Nhấn "Kích     │
              │     hoạt"      │
              └───────┬────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                       GIAI ĐOẠN 2: XÁC THỰC                             │
└─────────────────────────────────────────────────────────────────────────┘

                      │
                      ▼
              ┌────────────────┐
              │   Mango CMS    │
              │  gọi API       │
              │  LS Retail     │
              │  kích hoạt     │
              └───────┬────────┘
                      │
                      ▼
              ┌────────────────┐
              │   LS Retail    │
              │  - Kiểm tra    │
              │    mã thẻ      │
              │  - Kiểm tra    │
              │    PIN         │
              │  - Kiểm tra    │
              │    trạng thái  │
              └───────┬────────┘
                      │
          ┌───────────┴───────────┐
          │                       │
       HỢP LỆ                 KHÔNG HỢP LỆ
          │                       │
          ▼                       ▼
  ┌──────────────┐        ┌──────────────┐
  │  Tiến hành   │        │  Trả về lỗi  │
  │  kích hoạt   │        │  + Đếm số    │
  │              │        │  lần sai     │
  └──────┬───────┘        └──────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                    GIAI ĐOẠN 3: KÍCH HOẠT                               │
└─────────────────────────────────────────────────────────────────────────┘

          │
          ▼
  ┌────────────────┐
  │   LS Retail    │
  │  - Liên kết    │
  │    member ID   │
  │  - Đổi trạng   │
  │    thái thẻ    │
  │  - Ghi ngày    │
  │    kích hoạt   │
  └───────┬────────┘
          │
          ▼
  ┌────────────────┐
  │   Mango CMS    │
  │  - Invalidate  │
  │    cache       │
  │  - Push notif  │
  │    thành công  │
  └───────┬────────┘
          │
          ▼
  ┌────────────────┐
  │  Hiển thị kết  │
  │  quả + popup   │
  │  "Xem gift     │
  │  card" hoặc    │
  │  "Để sau"      │
  └────────────────┘
```

### 1.6. Kết Quả Sau Khi Kích Hoạt

| Kết Quả | Chi Tiết |
|---------|----------|
| **Trạng thái thẻ** | Chuyển sang "Đã kích hoạt" |
| **Liên kết member** | Thẻ được gắn với tài khoản khách hàng |
| **Hiển thị trong danh sách** | Thẻ xuất hiện trong danh sách với trạng thái "Đã kích hoạt" |
| **Sẵn sàng sử dụng** | Có thể dùng thanh toán online và offline |
| **Push notification** | Thông báo "Kích hoạt thẻ thành công" |

---

## 2. Nạp Tiền Gift Card

### 2.1. Mô Tả Chức Năng

Khách hàng có thể nạp thêm tiền vào gift card đang sở hữu. Khi nạp tiền:

- Số dư thẻ được cộng thêm
- Thẻ được gia hạn thêm 12 tháng kể từ ngày nạp

### 2.2. Phân Chia Trách Nhiệm Hệ Thống

#### LS Retail - Nhiệm Vụ Chính

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Kiểm tra điều kiện thẻ** | Xác nhận thẻ có thể nạp tiền (đã kích hoạt, còn hiệu lực) |
| **Validate số tiền** | Kiểm tra số dư sau nạp không vượt quá 5 triệu |
| **Cộng tiền vào thẻ** | Cập nhật số dư thẻ sau khi thanh toán thành công |
| **Gia hạn thẻ** | Cộng thêm 12 tháng vào hạn sử dụng |
| **Ghi lịch sử giao dịch** | Lưu giao dịch nạp tiền vào lịch sử |

#### Mango CMS - Nhiệm Vụ Điều Phối

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Hiển thị form nạp tiền** | Thông tin thẻ hiện tại, các mệnh giá nạp |
| **Validate sơ bộ** | Kiểm tra số tiền nạp hợp lệ trước khi gọi API |
| **Điều phối thanh toán** | Kết nối Payment Gateway |
| **Gọi API LS Retail** | Gọi API nạp tiền sau khi thanh toán thành công |
| **Gửi thông báo** | Push notification + SMS/ZNS/Email xác nhận nạp tiền |
| **Invalidate cache** | Xóa cache chi tiết thẻ để cập nhật số dư mới |

#### Payment Gateway - Xử Lý Thanh Toán

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Xử lý giao dịch** | Thanh toán số tiền nạp |
| **Hỗ trợ nhiều phương thức** | Napas, OnePay, Momo, ZaloPay, QR Pay |

### 2.3. Điều Kiện Thẻ Được Nạp Tiền

| Điều Kiện | Yêu Cầu |
|-----------|---------|
| **Trạng thái** | "Đã kích hoạt" hoặc "Hết tiền" |
| **Hiệu lực** | Chưa hết hạn sử dụng |
| **Số dư hiện tại** | Không giới hạn (có thể là 0) |

**Thẻ KHÔNG được nạp tiền:**
- Thẻ chưa kích hoạt
- Thẻ đã bị khóa
- Thẻ đã hết hạn

### 2.4. Quy Tắc Nạp Tiền

| Quy Tắc | Giá Trị |
|---------|---------|
| **Số tiền nạp tối thiểu** | 50.000đ |
| **Số dư tối đa sau nạp** | 5.000.000đ |
| **Mệnh giá định sẵn** | 100.000đ, 200.000đ, 500.000đ, 1.000.000đ |
| **Mệnh giá tùy chỉnh** | 50.000đ - (5.000.000đ - số dư hiện tại) |
| **Gia hạn khi nạp** | +12 tháng từ ngày nạp |

### 2.5. Luồng Hoạt Động

```
┌─────────────────────────────────────────────────────────────────────────┐
│                       GIAI ĐOẠN 1: CHỌN NẠP TIỀN                        │
└─────────────────────────────────────────────────────────────────────────┘

┌────────────────┐
│   Khách hàng   │
│ vào chi tiết   │
│ thẻ → Nạp tiền │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  hiển thị:     │
│  - Số dư hiện  │
│    tại         │
│  - HSD hiện    │
│    tại         │
│  - Các mệnh    │
│    giá nạp     │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Khách chọn    │
│  mệnh giá nạp  │
│  (hoặc custom) │
└───────┬────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                        GIAI ĐOẠN 2: VALIDATE                            │
└─────────────────────────────────────────────────────────────────────────┘

        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  kiểm tra:     │
│  - Số tiền >=  │
│    50.000đ?    │
│  - Số dư sau   │
│    nạp <=      │
│    5 triệu?    │
└───────┬────────┘
        │
        ├─────────────────────────────┐
        │                             │
     HỢP LỆ                      KHÔNG HỢP LỆ
        │                             │
        ▼                             ▼
┌────────────────┐           ┌────────────────┐
│  Hiển thị các  │           │  Thông báo     │
│ phương thức TT │           │  lỗi validation│
└───────┬────────┘           └────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                         GIAI ĐOẠN 3: THANH TOÁN                         │
└─────────────────────────────────────────────────────────────────────────┘

        │
        ▼
┌────────────────┐
│  Khách chọn    │
│ phương thức TT │
└───────┬────────┘
        │
        ▼
┌────────────────┐      ┌────────────────┐
│   Mango CMS    │─────▶│    Payment     │
│ gửi request TT │      │    Gateway     │
└────────────────┘      └───────┬────────┘
                                │
                                ▼
                        ┌────────────────┐
                        │  Xử lý thanh   │
                        │     toán       │
                        └───────┬────────┘
                                │
                    ┌───────────┴───────────┐
                    │                       │
                 THÀNH CÔNG              THẤT BẠI
                    │                       │
                    ▼                       ▼
            ┌──────────────┐         ┌──────────────┐
            │  Callback    │         │  Thông báo   │
            │  success     │         │  lỗi, retry  │
            └──────┬───────┘         └──────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                        GIAI ĐOẠN 4: NẠP TIỀN                            │
└─────────────────────────────────────────────────────────────────────────┘

               │
               ▼
       ┌────────────────┐
       │   Mango CMS    │
       │  gọi API       │
       │  LS Retail     │
       │  nạp tiền      │
       └───────┬────────┘
               │
               ▼
       ┌────────────────┐
       │   LS Retail    │
       │  - Cộng tiền   │
       │    vào thẻ     │
       │  - Gia hạn     │
       │    +12 tháng   │
       │  - Ghi lịch    │
       │    sử GD       │
       └───────┬────────┘
               │
               ▼
       ┌────────────────┐
       │   Mango CMS    │
       │  - Invalidate  │
       │    cache       │
       │  - Push notif  │
       │  - SMS/Email   │
       │    xác nhận    │
       └───────┬────────┘
               │
               ▼
       ┌────────────────┐
       │  Hiển thị kết  │
       │  quả + popup   │
       │  "Sử dụng      │
       │  ngay" hoặc    │
       │  "Đóng"        │
       └────────────────┘
```

### 2.6. Kết Quả Sau Khi Nạp Tiền

| Kết Quả | Chi Tiết |
|---------|----------|
| **Số dư mới** | Số dư cũ + Số tiền nạp |
| **Hạn sử dụng mới** | Ngày nạp + 12 tháng |
| **Lịch sử giao dịch** | Thêm giao dịch "Nạp tiền" với số tiền (+) |
| **Push notification** | "Nạp tiền thành công: +[số tiền]đ" |
| **SMS/Email** | Xác nhận nạp tiền với số dư mới và HSD mới |

### 2.7. Ví Dụ Nạp Tiền

| Trước Khi Nạp | Nạp Tiền | Sau Khi Nạp |
|---------------|----------|-------------|
| Số dư: 200.000đ | Nạp: 500.000đ | Số dư: 700.000đ |
| HSD: 15/03/2025 | Ngày nạp: 15/01/2025 | HSD: 15/01/2026 |

---

## 3. Xử Lý Lỗi

### 3.1. Lỗi Kích Hoạt

| Loại Lỗi | Xử Lý |
|----------|-------|
| **Mã thẻ không tồn tại** | Thông báo lỗi, kiểm tra lại mã |
| **Mã PIN sai** | Thông báo lỗi + số lần còn lại |
| **Thẻ đã kích hoạt** | Thông báo đã kích hoạt, không cần thao tác |
| **Thẻ bị khóa** | Hướng dẫn liên hệ CSKH |
| **LS Retail không phản hồi** | Retry, thông báo thử lại sau |

### 3.2. Lỗi Nạp Tiền

| Loại Lỗi | Xử Lý |
|----------|-------|
| **Vượt quá số dư tối đa** | Thông báo, gợi ý số tiền nạp tối đa |
| **Thẻ không đủ điều kiện** | Thông báo lý do (bị khóa, hết hạn) |
| **Thanh toán thất bại** | Thông báo lỗi, cho phép thử lại |
| **LS Retail không phản hồi** | Retry, hoàn tiền nếu cần |

---

## 4. Tóm Tắt Phân Công

### Kích Hoạt Gift Card

| Hệ Thống | Vai Trò | Nhiệm Vụ Cụ Thể |
|----------|---------|-----------------|
| **LS Retail** | Primary | Xác thực mã thẻ + PIN, liên kết member, đổi trạng thái |
| **Mango CMS** | Middleware | Điều phối API, hỗ trợ quét QR, gửi thông báo |
| **Loyalty** | Không tham gia | Cung cấp member ID |

### Nạp Tiền Gift Card

| Hệ Thống | Vai Trò | Nhiệm Vụ Cụ Thể |
|----------|---------|-----------------|
| **LS Retail** | Primary | Validate, cộng tiền, gia hạn, ghi lịch sử |
| **Mango CMS** | Middleware | Điều phối thanh toán, gọi API nạp tiền, gửi thông báo |
| **Payment Gateway** | Payment | Xử lý thanh toán số tiền nạp |
| **Loyalty** | Không tham gia | - |

---

*Tài liệu này mô tả chi tiết chức năng Kích hoạt và Nạp tiền Gift Card, bao gồm phân chia trách nhiệm giữa các hệ thống backend.*
