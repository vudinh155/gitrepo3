# MUA & BIẾU TẶNG GIFT CARD

> **Tham chiếu SOW:** B.3 - Mua/Biếu tặng thẻ online
> **Chức năng:** Khách hàng mua gift card online cho bản thân hoặc biếu tặng người khác

---

## 1. Mua Gift Card Online (Cho Bản Thân)

### 1.1. Mô Tả Chức Năng

Khách hàng thành viên TH eLIFE có thể mua gift card online để sử dụng cho bản thân. Quy trình bao gồm:

- Chọn loại thẻ và theme (nếu có)
- Chọn mệnh giá thẻ (định sẵn hoặc tùy chỉnh)
- Chọn số lượng thẻ muốn mua
- Thanh toán qua các phương thức được hỗ trợ
- Nhận mã thẻ và mã PIN qua SMS/ZNS/Email
- Kích hoạt thẻ để sử dụng

### 1.2. Phân Chia Trách Nhiệm Hệ Thống

#### LS Retail - Nhiệm Vụ Chính

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Tạo thẻ mới** | Sau khi thanh toán thành công, LS Retail tạo thẻ gift card mới với số dư tương ứng |
| **Generate mã thanh toán** | Tạo mã thẻ (card number) duy nhất |
| **Generate mã bí mật (PIN)** | Tạo mã PIN để kích hoạt thẻ |
| **Lưu thông tin thẻ** | Lưu trữ thông tin thẻ: số dư, trạng thái "Chưa kích hoạt", hạn sử dụng |
| **Liên kết với member** | Gắn thẻ với member ID của người mua (để hiển thị trong danh sách) |

#### Mango CMS - Nhiệm Vụ Điều Phối

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Hiển thị form mua thẻ** | Cung cấp danh sách loại thẻ, mệnh giá cho Frontend |
| **Xác thực user** | Kiểm tra user đã đăng nhập và là thành viên |
| **Điều phối thanh toán** | Kết nối với Payment Gateway để xử lý thanh toán |
| **Gọi API LS Retail** | Sau khi thanh toán thành công, gọi API tạo thẻ mới |
| **Gửi mã kích hoạt** | Gửi mã thẻ + mã PIN cho khách hàng qua SMS/ZNS/Email |
| **Gửi thông báo** | Push notification xác nhận mua thẻ thành công |
| **Invalidate cache** | Xóa cache danh sách thẻ để cập nhật thẻ mới |

#### Payment Gateway - Xử Lý Thanh Toán

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Xử lý giao dịch** | Nhận request thanh toán, xử lý với ngân hàng/ví điện tử |
| **Xác nhận thanh toán** | Trả kết quả thanh toán thành công/thất bại |
| **Hỗ trợ nhiều phương thức** | Napas, OnePay, Momo, ZaloPay, QR Pay |

#### Backend Loyalty

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Không tham gia trực tiếp** | Nghiệp vụ mua thẻ thuộc LS Retail |
| **Cung cấp member info** | Member ID để liên kết thẻ với khách hàng |

### 1.3. Mệnh Giá Thẻ

| Loại | Giá Trị |
|------|---------|
| **Mệnh giá định sẵn** | 100.000đ, 200.000đ, 300.000đ, 500.000đ, 1.000.000đ, 2.000.000đ |
| **Mệnh giá tùy chỉnh** | Tối thiểu: 100.000đ, Tối đa: 5.000.000đ |

### 1.4. Phương Thức Thanh Toán

| Phương Thức | Mô Tả |
|-------------|-------|
| **Napas** | Thẻ ATM nội địa |
| **OnePay** | Thẻ quốc tế Visa/Master |
| **Momo** | Ví điện tử Momo |
| **ZaloPay** | Ví điện tử ZaloPay |
| **QR Pay** | VNPAY/VietQR |
| **Tài khoản trả trước** | Sử dụng gift card đang có để mua thẻ mới |

### 1.5. Luồng Hoạt Động

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           GIAI ĐOẠN 1: CHỌN THẺ                         │
└─────────────────────────────────────────────────────────────────────────┘

┌────────────────┐
│   Khách hàng   │
│ vào màn hình   │
│   Mua thẻ      │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Chọn loại thẻ │
│  (Theme)       │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Chọn mệnh giá │
│ (100K-2 triệu  │
│  hoặc custom)  │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Chọn số lượng │
│     thẻ        │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│ Nhấn "Thanh    │
│ toán ngay"     │
└───────┬────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                         GIAI ĐOẠN 2: THANH TOÁN                         │
└─────────────────────────────────────────────────────────────────────────┘

        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  hiển thị các  │
│ phương thức TT │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Khách chọn    │
│ phương thức    │
│  thanh toán    │
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
│                        GIAI ĐOẠN 3: TẠO THẺ                             │
└─────────────────────────────────────────────────────────────────────────┘

               │
               ▼
       ┌────────────────┐
       │   Mango CMS    │
       │  gọi API       │
       │  LS Retail     │
       │  tạo thẻ mới   │
       └───────┬────────┘
               │
               ▼
       ┌────────────────┐
       │   LS Retail    │
       │  - Tạo thẻ     │
       │  - Generate mã │
       │  - Generate PIN│
       │  - Lưu thông   │
       │    tin thẻ     │
       └───────┬────────┘
               │
               ▼
       ┌────────────────┐
       │   LS Retail    │
       │  trả mã thẻ    │
       │  + mã PIN      │
       └───────┬────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                       GIAI ĐOẠN 4: GỬI MÃ                               │
└─────────────────────────────────────────────────────────────────────────┘

               │
               ▼
       ┌────────────────┐
       │   Mango CMS    │
       │  gửi mã thẻ    │
       │  + PIN qua:    │
       │  - SMS         │
       │  - ZNS         │
       │  - Email       │
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
       │  quả cho user  │
       │  + Hướng dẫn   │
       │  kích hoạt     │
       └────────────────┘
```

### 1.6. Kết Quả Sau Khi Mua

Sau khi mua thẻ thành công:

| Kết Quả | Chi Tiết |
|---------|----------|
| **Thẻ mới** | Được tạo với trạng thái "Chưa kích hoạt" |
| **SMS/ZNS/Email** | Khách hàng nhận mã thẻ + mã PIN |
| **Danh sách thẻ** | Thẻ mới xuất hiện trong danh sách với trạng thái "Chưa kích hoạt" |
| **Hạn sử dụng** | 12 tháng kể từ ngày tạo |
| **Bước tiếp theo** | Khách hàng cần kích hoạt thẻ để sử dụng |

---

## 2. Biếu Tặng Gift Card

### 2.1. Mô Tả Chức Năng

Khách hàng thành viên có thể mua gift card và gửi tặng cho người khác. Quy trình tương tự mua thẻ nhưng có thêm:

- Nhập thông tin người nhận (SĐT hoặc Email)
- Hỗ trợ tặng nhiều người cùng lúc
- Thêm lời nhắn tặng (tùy chọn)
- Mã thẻ được gửi đến người nhận, không phải người mua

### 2.2. Phân Chia Trách Nhiệm Hệ Thống

#### LS Retail - Nhiệm Vụ Chính

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Tạo thẻ cho mỗi người nhận** | Mỗi người nhận được tạo một thẻ riêng biệt |
| **Generate mã thẻ + PIN** | Tạo mã thẻ và PIN cho từng thẻ |
| **Đánh dấu nguồn gốc** | Ghi nhận thẻ được tặng bởi ai |
| **Không liên kết member** | Thẻ chưa được liên kết với bất kỳ member nào (chờ người nhận kích hoạt) |

#### Mango CMS - Nhiệm Vụ Điều Phối

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Hiển thị form biếu tặng** | Form nhập thông tin người nhận, mệnh giá, lời nhắn |
| **Xác thực thông tin người nhận** | Kiểm tra định dạng SĐT/Email hợp lệ |
| **Điều phối thanh toán** | Kết nối Payment Gateway |
| **Gọi API LS Retail** | Tạo thẻ cho từng người nhận |
| **Gửi mã đến người nhận** | SMS/ZNS/Email mã thẻ + PIN + lời nhắn tặng |
| **Thông báo cho người tặng** | Xác nhận đã gửi thành công |

#### Payment Gateway - Xử Lý Thanh Toán

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Xử lý tổng tiền** | Tính tổng = Mệnh giá x Số người nhận |
| **Xử lý giao dịch** | Thanh toán một lần cho tất cả thẻ |

### 2.3. Thông Tin Người Nhận

| Thông Tin | Yêu Cầu |
|-----------|---------|
| **Số điện thoại** | Bắt buộc một trong hai (SĐT hoặc Email) |
| **Email** | Bắt buộc một trong hai (SĐT hoặc Email) |
| **Số người nhận** | Hỗ trợ nhiều người, mỗi người nhận một thẻ riêng |
| **Lời nhắn tặng** | Tùy chọn, tối đa 200 ký tự |

### 2.4. Luồng Hoạt Động

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      GIAI ĐOẠN 1: NHẬP THÔNG TIN                         │
└─────────────────────────────────────────────────────────────────────────┘

┌────────────────┐
│   Khách hàng   │
│ vào màn hình   │
│  Biếu tặng     │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│ Nhập thông tin │
│ người nhận 1   │
│ (SĐT/Email)    │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Thêm người    │
│  nhận khác?    │
│  (Tùy chọn)    │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Chọn loại thẻ │
│  + Mệnh giá    │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Nhập lời nhắn │
│  (Tùy chọn)    │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│ Nhấn "Thanh    │
│ toán ngay"     │
└───────┬────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                         GIAI ĐOẠN 2: THANH TOÁN                         │
└─────────────────────────────────────────────────────────────────────────┘

        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  tính tổng:    │
│ Mệnh giá x Số  │
│  người nhận    │
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

┌─────────────────────────────────────────────────────────────────────────┐
│                    GIAI ĐOẠN 3: TẠO THẺ CHO MỖI NGƯỜI                   │
└─────────────────────────────────────────────────────────────────────────┘

               │
               ▼
       ┌────────────────┐
       │   Mango CMS    │
       │  gọi API       │
       │  LS Retail     │
       │  (loop mỗi     │
       │   người nhận)  │
       └───────┬────────┘
               │
               ▼
       ┌────────────────┐
       │   LS Retail    │
       │  Tạo N thẻ     │
       │  (N = số người │
       │    nhận)       │
       └───────┬────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                  GIAI ĐOẠN 4: GỬI MÃ ĐẾN NGƯỜI NHẬN                     │
└─────────────────────────────────────────────────────────────────────────┘

               │
               ▼
       ┌────────────────┐
       │   Mango CMS    │
       │  gửi đến mỗi   │
       │  người nhận:   │
       │  - Mã thẻ      │
       │  - Mã PIN      │
       │  - Lời nhắn    │
       │  (SMS/ZNS/     │
       │   Email)       │
       └───────┬────────┘
               │
               ▼
       ┌────────────────┐
       │   Mango CMS    │
       │  Thông báo     │
       │  người tặng:   │
       │  "Đã gửi       │
       │  thành công"   │
       └────────────────┘
```

### 2.5. Nội Dung Gửi Đến Người Nhận

Khi người nhận nhận được SMS/ZNS/Email, nội dung bao gồm:

| Thông Tin | Chi Tiết |
|-----------|----------|
| **Tiêu đề** | "Bạn nhận được Gift Card từ TH eLIFE" |
| **Người tặng** | Tên của người tặng (nếu có) |
| **Lời nhắn** | Lời nhắn từ người tặng (nếu có) |
| **Mệnh giá** | Giá trị thẻ |
| **Mã thẻ** | Mã thanh toán để kích hoạt |
| **Mã PIN** | Mã bí mật để kích hoạt |
| **Hướng dẫn** | Hướng dẫn cách kích hoạt trên app TH eLIFE |
| **Hạn sử dụng** | Thông báo thẻ có hiệu lực 12 tháng |

### 2.6. Khác Biệt So Với Mua Cho Bản Thân

| Tiêu Chí | Mua Cho Bản Thân | Biếu Tặng |
|----------|------------------|-----------|
| **Người nhận mã** | Người mua | Người được tặng |
| **Liên kết member** | Liên kết ngay với người mua | Chưa liên kết (chờ kích hoạt) |
| **Hiển thị trong danh sách** | Có (tab "Mua thẻ quà") | Không (cho đến khi người nhận kích hoạt) |
| **Số người nhận** | 1 (bản thân) | 1 hoặc nhiều |
| **Lời nhắn** | Không có | Có thể thêm |

### 2.7. Lịch Sử Thẻ Đã Tặng

Người tặng có thể xem lịch sử các thẻ đã tặng:

| Thông Tin | Mô Tả |
|-----------|-------|
| **Ngày tặng** | Ngày thực hiện giao dịch |
| **Người nhận** | SĐT/Email người nhận (masked) |
| **Mệnh giá** | Giá trị thẻ đã tặng |
| **Trạng thái** | Đã gửi / Đã kích hoạt |

---

## 3. Xử Lý Lỗi

### 3.1. Lỗi Thanh Toán

| Loại Lỗi | Xử Lý |
|----------|-------|
| **Thanh toán thất bại** | Thông báo lỗi, cho phép thử lại |
| **Timeout** | Kiểm tra trạng thái giao dịch, thông báo user |
| **Số dư không đủ** | Thông báo, gợi ý phương thức khác |

### 3.2. Lỗi Tạo Thẻ

| Loại Lỗi | Xử Lý |
|----------|-------|
| **LS Retail không phản hồi** | Retry 3 lần, sau đó hoàn tiền |
| **Tạo thẻ thất bại** | Hoàn tiền, thông báo user liên hệ CSKH |

### 3.3. Lỗi Gửi Mã

| Loại Lỗi | Xử Lý |
|----------|-------|
| **SMS thất bại** | Retry, fallback sang Email |
| **Email thất bại** | Retry, fallback sang SMS |
| **Cả hai thất bại** | Lưu pending, gửi lại sau, thông báo user |

---

## 4. Tóm Tắt Phân Công

### Mua Gift Card (Cho Bản Thân)

| Hệ Thống | Vai Trò | Nhiệm Vụ Cụ Thể |
|----------|---------|-----------------|
| **LS Retail** | Primary | Tạo thẻ, generate mã + PIN, liên kết member |
| **Mango CMS** | Middleware | Điều phối thanh toán, gọi API tạo thẻ, gửi mã SMS/ZNS/Email |
| **Payment Gateway** | Payment | Xử lý thanh toán |
| **Loyalty** | Không tham gia | - |

### Biếu Tặng Gift Card

| Hệ Thống | Vai Trò | Nhiệm Vụ Cụ Thể |
|----------|---------|-----------------|
| **LS Retail** | Primary | Tạo thẻ cho mỗi người nhận, generate mã + PIN |
| **Mango CMS** | Middleware | Điều phối thanh toán, tạo nhiều thẻ, gửi mã đến từng người nhận |
| **Payment Gateway** | Payment | Xử lý thanh toán tổng |
| **Loyalty** | Không tham gia | - |

---

*Tài liệu này mô tả chi tiết chức năng Mua và Biếu tặng Gift Card, bao gồm phân chia trách nhiệm giữa các hệ thống backend.*
