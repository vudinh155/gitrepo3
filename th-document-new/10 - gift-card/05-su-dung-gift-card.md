# SỬ DỤNG GIFT CARD - THANH TOÁN ONLINE & OFFLINE

> **Tham chiếu SOW:** B.3 - Sử dụng giftcard mua hàng online/offline
> **Chức năng:** Khách hàng sử dụng gift card để thanh toán khi mua hàng

---

## 1. Sử Dụng Gift Card - Thanh Toán Online

### 1.1. Mô Tả Chức Năng

Khách hàng thành viên có thể sử dụng gift card để thanh toán đơn hàng online trên TH eLIFE App hoặc Website. Tính năng cho phép:

- Chọn gift card làm phương thức thanh toán tại bước checkout
- Sử dụng một hoặc nhiều thẻ cho cùng một đơn hàng
- Kết hợp gift card với các phương thức thanh toán khác nếu số dư không đủ

### 1.2. Phân Chia Trách Nhiệm Hệ Thống

#### LS Retail - Nhiệm Vụ Chính

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Cung cấp danh sách thẻ khả dụng** | Trả về danh sách thẻ đã kích hoạt, còn hiệu lực, còn số dư |
| **Kiểm tra số dư** | Xác nhận số dư thẻ đủ để thanh toán |
| **Tạm giữ số tiền (Reserve)** | Giữ chỗ số tiền từ thẻ khi khách tạo đơn hàng |
| **Trừ tiền (Deduct)** | Trừ tiền từ thẻ khi đơn hàng được xác nhận |
| **Hủy tạm giữ (Release)** | Trả lại số tiền nếu đơn hàng bị hủy hoặc hết thời gian giữ |
| **Ghi lịch sử giao dịch** | Lưu giao dịch chi tiêu vào lịch sử |

#### Mango CMS - Nhiệm Vụ Điều Phối

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Hiển thị option thanh toán** | Thêm "Tài khoản trả trước" vào danh sách phương thức |
| **Hiển thị danh sách thẻ** | Liệt kê các thẻ khả dụng khi user chọn thanh toán bằng gift card |
| **Tính toán thanh toán** | Phân bổ số tiền giữa gift card và phương thức khác |
| **Gọi API Reserve** | Tạm giữ số tiền từ gift card khi tạo đơn |
| **Gọi API Deduct** | Trừ tiền khi đơn hàng hoàn tất |
| **Gọi API Release** | Hủy tạm giữ khi đơn hàng bị hủy |
| **Gửi thông báo** | Push notification khi có giao dịch chi tiêu |
| **Invalidate cache** | Xóa cache chi tiết thẻ khi số dư thay đổi |

#### Backend Loyalty

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Không tham gia trực tiếp** | Thanh toán gift card thuộc LS Retail |
| **Cộng điểm (nếu có)** | Tích điểm cho đơn hàng theo chính sách (nếu áp dụng) |

### 1.3. Điều Kiện Sử Dụng

| Điều Kiện | Yêu Cầu |
|-----------|---------|
| **Trạng thái thẻ** | Đã kích hoạt |
| **Hiệu lực** | Còn trong thời hạn sử dụng |
| **Số dư** | Còn số dư (> 0) |

### 1.4. Các Trường Hợp Thanh Toán

| Trường Hợp | Xử Lý |
|------------|-------|
| **Số dư gift card >= Giá trị đơn hàng** | Thanh toán 100% bằng gift card |
| **Số dư gift card < Giá trị đơn hàng** | Dùng hết số dư + chọn phương thức khác cho phần còn lại |
| **Sử dụng nhiều gift card** | Cho phép chọn nhiều thẻ, tổng số dư >= giá trị đơn |

### 1.5. Cơ Chế Tạm Giữ (Reserve)

Khi khách hàng đặt hàng và chọn thanh toán bằng gift card, hệ thống sẽ **tạm giữ** số tiền từ thẻ:

| Thuộc Tính | Giá Trị |
|------------|---------|
| **Mục đích** | Đảm bảo số dư khi đơn hàng được xử lý |
| **Thời gian tạm giữ** | 30 phút (có thể cấu hình) |
| **Số dư hiển thị** | Số dư khả dụng = Số dư thực - Số tiền tạm giữ |
| **Khi hết thời gian** | Tự động release nếu đơn hàng chưa hoàn tất |

### 1.6. Luồng Hoạt Động - Thanh Toán Online

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    GIAI ĐOẠN 1: CHỌN PHƯƠNG THỨC                        │
└─────────────────────────────────────────────────────────────────────────┘

┌────────────────┐
│   Khách hàng   │
│ đến bước       │
│   checkout     │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Chọn phương   │
│  thức "Tài     │
│  khoản trả     │
│      trước"    │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  gọi API       │
│  LS Retail     │
│  lấy danh sách │
│  thẻ khả dụng  │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Hiển thị danh │
│  sách thẻ:     │
│  - Tên thẻ     │
│  - Số dư       │
│  - HSD         │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Khách chọn    │
│  thẻ muốn dùng │
│  + số tiền     │
└───────┬────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                      GIAI ĐOẠN 2: TÍNH TOÁN                             │
└─────────────────────────────────────────────────────────────────────────┘

        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  tính toán:    │
│  - Tổng đơn    │
│  - Số tiền từ  │
│    gift card   │
│  - Số tiền còn │
│    lại (nếu có)│
└───────┬────────┘
        │
        │  Số dư đủ?
        ├─────────────────────────────┐
        │                             │
       ĐỦ                         KHÔNG ĐỦ
        │                             │
        ▼                             ▼
┌────────────────┐           ┌────────────────┐
│  Chỉ dùng      │           │  Dùng hết số   │
│  gift card     │           │  dư + chọn     │
│                │           │  phương thức   │
│                │           │  khác          │
└───────┬────────┘           └───────┬────────┘
        │                             │
        └─────────────┬───────────────┘
                      │
                      ▼
              ┌────────────────┐
              │  Nhấn "Đặt     │
              │     hàng"      │
              └───────┬────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                     GIAI ĐOẠN 3: TẠM GIỮ TIỀN                           │
└─────────────────────────────────────────────────────────────────────────┘

                      │
                      ▼
              ┌────────────────┐
              │   Mango CMS    │
              │  gọi API       │
              │  LS Retail:    │
              │  Reserve       │
              │  (tạm giữ)     │
              └───────┬────────┘
                      │
                      ▼
              ┌────────────────┐
              │   LS Retail    │
              │  - Kiểm tra    │
              │    số dư       │
              │  - Tạm giữ     │
              │    số tiền     │
              └───────┬────────┘
                      │
          ┌───────────┴───────────┐
          │                       │
       THÀNH CÔNG              THẤT BẠI
          │                       │
          ▼                       ▼
  ┌──────────────┐        ┌──────────────┐
  │  Tiếp tục    │        │  Thông báo   │
  │  tạo đơn     │        │  lỗi: Số dư  │
  │              │        │  không đủ    │
  └──────┬───────┘        └──────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                   GIAI ĐOẠN 4: HOÀN TẤT ĐƠN HÀNG                        │
└─────────────────────────────────────────────────────────────────────────┘

          │
          ▼
  ┌────────────────┐
  │  LS Retail     │
  │  tạo đơn hàng  │
  └───────┬────────┘
          │
          ▼
  ┌────────────────┐
  │   Mango CMS    │
  │  gọi API       │
  │  LS Retail:    │
  │  Deduct        │
  │  (trừ tiền)    │
  └───────┬────────┘
          │
          ▼
  ┌────────────────┐
  │   LS Retail    │
  │  - Trừ tiền    │
  │    từ thẻ      │
  │  - Ghi lịch    │
  │    sử GD       │
  │  - Xóa tạm giữ │
  └───────┬────────┘
          │
          ▼
  ┌────────────────┐
  │   Mango CMS    │
  │  - Invalidate  │
  │    cache       │
  │  - Push notif  │
  │    giao dịch   │
  └───────┬────────┘
          │
          ▼
  ┌────────────────┐
  │  Hiển thị kết  │
  │  quả đặt hàng  │
  │  thành công    │
  └────────────────┘
```

### 1.7. Xử Lý Khi Đơn Hàng Bị Hủy

Khi đơn hàng bị hủy sau khi đã thanh toán bằng gift card:

```
┌────────────────┐
│  Đơn hàng bị   │
│  hủy (bởi KH   │
│  hoặc hệ thống)│
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  gọi API       │
│  LS Retail:    │
│  Cancel        │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   LS Retail    │
│  - Hoàn tiền   │
│    về thẻ      │
│  - Ghi lịch sử │
│    "Hoàn tiền" │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  - Invalidate  │
│    cache       │
│  - Push notif  │
│    "Hoàn tiền  │
│    thành công" │
└────────────────┘
```

---

## 2. Sử Dụng Gift Card - Thanh Toán Offline (Tại Cửa Hàng)

### 2.1. Mô Tả Chức Năng

Khách hàng có thể sử dụng gift card để thanh toán tại các cửa hàng TrueMart bằng cách hiển thị mã QR hoặc barcode trên app cho nhân viên quét.

### 2.2. Phân Chia Trách Nhiệm Hệ Thống

#### LS Retail - Nhiệm Vụ Chính

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Xác thực thẻ** | Kiểm tra mã thẻ từ QR/barcode có hợp lệ |
| **Xác thực member** | Kiểm tra thẻ có thuộc về member đang quét |
| **Kiểm tra số dư** | Xác nhận số dư đủ để thanh toán |
| **Trừ tiền** | Trừ tiền từ thẻ sau khi xác nhận |
| **Ghi lịch sử giao dịch** | Lưu giao dịch với thông tin cửa hàng |

#### Mango CMS - Nhiệm Vụ Hỗ Trợ

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Generate QR Code** | Tạo QR code cho thanh toán offline (có expire time) |
| **Hiển thị mã** | Hiển thị QR/Barcode trên màn hình chi tiết thẻ |
| **Nhận webhook** | Nhận thông báo giao dịch từ LS Retail |
| **Gửi thông báo** | Push notification cho user sau khi giao dịch |
| **Invalidate cache** | Xóa cache khi số dư thay đổi |

#### POS (Point of Sale)

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Quét mã** | Quét QR/Barcode từ điện thoại khách hàng |
| **Gửi request** | Gửi thông tin thanh toán đến LS Retail |
| **Hiển thị kết quả** | Hiển thị kết quả giao dịch cho nhân viên |

### 2.3. Bảo Mật QR Code

| Thuộc Tính | Giá Trị |
|------------|---------|
| **Thời gian hiệu lực** | 5 phút |
| **Tự động refresh** | Khi user refresh hoặc QR hết hạn |
| **Nội dung mã hóa** | Card ID + Member ID + Timestamp + Signature |
| **Chống replay attack** | Mỗi QR chỉ sử dụng một lần |

### 2.4. Luồng Hoạt Động - Thanh Toán Offline

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    GIAI ĐOẠN 1: HIỂN THỊ MÃ                             │
└─────────────────────────────────────────────────────────────────────────┘

┌────────────────┐
│   Khách hàng   │
│  mở app TH     │
│  eLIFE         │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Vào chi tiết  │
│  gift card     │
│  muốn dùng     │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  generate QR   │
│  Code (expire  │
│    5 phút)     │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Hiển thị màn  │
│  hình QR Code  │
│  / Barcode     │
└───────┬────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                     GIAI ĐOẠN 2: QUÉT MÃ                                │
└─────────────────────────────────────────────────────────────────────────┘

        │
        ▼
┌────────────────┐
│  Khách đưa     │
│  điện thoại    │
│  cho nhân viên │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Nhân viên     │
│  quét mã bằng  │
│  máy POS       │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│     POS        │
│  decode mã,    │
│  gửi request   │
│  đến LS Retail │
└───────┬────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                    GIAI ĐOẠN 3: XÁC THỰC                                │
└─────────────────────────────────────────────────────────────────────────┘

        │
        ▼
┌────────────────┐
│   LS Retail    │
│  - Kiểm tra    │
│    QR còn hạn? │
│  - Kiểm tra    │
│    card hợp lệ?│
│  - Kiểm tra    │
│    member khớp?│
│  - Kiểm tra    │
│    số dư đủ?   │
└───────┬────────┘
        │
        ├─────────────────────────────┐
        │                             │
     HỢP LỆ                      KHÔNG HỢP LỆ
        │                             │
        ▼                             ▼
┌────────────────┐           ┌────────────────┐
│  Tiến hành     │           │  Trả về lỗi    │
│  thanh toán    │           │  cho POS       │
└───────┬────────┘           └────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                    GIAI ĐOẠN 4: THANH TOÁN                              │
└─────────────────────────────────────────────────────────────────────────┘

        │
        ▼
┌────────────────┐
│   LS Retail    │
│  - Trừ tiền    │
│    từ thẻ      │
│  - Ghi lịch sử │
│    GD (kèm tên │
│    cửa hàng)   │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   LS Retail    │
│  gửi webhook   │
│  → Mango CMS   │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  - Invalidate  │
│    cache       │
│  - Push notif  │
│    "Thanh toán │
│    [số tiền]   │
│    tại [cửa    │
│    hàng]"      │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│     POS        │
│  hiển thị kết  │
│  quả cho nhân  │
│  viên + in hóa │
│  đơn           │
└────────────────┘
```

### 2.5. Các Trường Hợp Thanh Toán Offline

| Trường Hợp | Xử Lý |
|------------|-------|
| **Số dư đủ** | Trừ tiền, thông báo thành công |
| **Số dư không đủ** | Dùng hết số dư, khách thanh toán phần còn lại bằng tiền mặt/thẻ |
| **Sử dụng nhiều gift card** | Nhân viên quét lần lượt từng thẻ |
| **QR hết hạn** | Thông báo lỗi, khách refresh để lấy QR mới |

---

## 3. So Sánh Thanh Toán Online vs Offline

| Tiêu Chí | Thanh Toán Online | Thanh Toán Offline |
|----------|-------------------|-------------------|
| **Nơi thực hiện** | TH eLIFE App/Web | Cửa hàng TrueMart |
| **Cách chọn thẻ** | Chọn trong danh sách tại checkout | Quét QR/Barcode trên app |
| **Cơ chế giữ tiền** | Reserve → Deduct | Trừ ngay lập tức |
| **Kết hợp phương thức khác** | Tự động tính toán | Nhân viên xử lý thủ công |
| **Hoàn tiền khi hủy** | Tự động qua API | Xử lý tại cửa hàng |
| **Bảo mật** | Session + Token | QR Code expire + Signature |

---

## 4. Xử Lý Lỗi

### 4.1. Lỗi Thanh Toán Online

| Loại Lỗi | Xử Lý |
|----------|-------|
| **Số dư không đủ** | Thông báo, gợi ý nạp tiền hoặc chọn thẻ khác |
| **Thẻ hết hạn** | Thông báo, loại khỏi danh sách khả dụng |
| **Reserve thất bại** | Retry, thông báo user |
| **Đơn hàng timeout** | Release tiền tự động |

### 4.2. Lỗi Thanh Toán Offline

| Loại Lỗi | Xử Lý |
|----------|-------|
| **QR hết hạn** | Báo lỗi trên POS, khách refresh QR |
| **Thẻ không khớp member** | Từ chối giao dịch |
| **Số dư không đủ** | Thông báo số dư hiện có, khách thanh toán phần còn lại |
| **LS Retail không phản hồi** | Retry, báo nhân viên xử lý |

---

## 5. Tóm Tắt Phân Công

### Thanh Toán Online

| Hệ Thống | Vai Trò | Nhiệm Vụ Cụ Thể |
|----------|---------|-----------------|
| **LS Retail** | Primary | Cung cấp danh sách thẻ, Reserve, Deduct, Cancel, ghi lịch sử |
| **Mango CMS** | Middleware | Điều phối checkout, gọi API thanh toán, gửi thông báo |
| **Loyalty** | Secondary | Tích điểm cho đơn hàng (nếu áp dụng) |

### Thanh Toán Offline

| Hệ Thống | Vai Trò | Nhiệm Vụ Cụ Thể |
|----------|---------|-----------------|
| **LS Retail** | Primary | Xác thực QR, trừ tiền, ghi lịch sử, gửi webhook |
| **Mango CMS** | Support | Generate QR, nhận webhook, gửi push notification |
| **POS** | Interface | Quét mã, gửi request, hiển thị kết quả |

---

*Tài liệu này mô tả chi tiết chức năng Sử dụng Gift Card để thanh toán online và offline, bao gồm phân chia trách nhiệm giữa các hệ thống backend.*
