# TÍCH HỢP LS RETAIL & GỬI THÔNG BÁO

> **Tham chiếu SOW:** B.3 - API tích hợp với LS Retail
> **Chức năng:** Tổng quan tích hợp API với LS Retail và cơ chế gửi thông báo

---

## 1. Tích Hợp LS Retail

### 1.1. Tổng Quan

LS Retail là hệ thống backend chính (source of truth) cho module Gift Card. Mango CMS sẽ tích hợp với LS Retail thông qua các API để thực hiện các nghiệp vụ gift card.

### 1.2. Danh Sách API Tích Hợp

Theo yêu cầu từ SOW B.3, các API cần tích hợp với LS Retail bao gồm:

| STT | API | Mô Tả | Chức Năng Sử Dụng |
|-----|-----|-------|-------------------|
| 1 | **Tạo mới thẻ Giftcard online** | Tạo thẻ mới với số dư ban đầu | Mua thẻ, Biếu tặng |
| 2 | **Kích hoạt thẻ Giftcard** | Kích hoạt và liên kết thẻ với member | Kích hoạt thẻ |
| 3 | **Nạp tiền thẻ Giftcard online** | Cộng tiền vào thẻ, gia hạn | Nạp tiền |
| 4 | **Tạm giữ thanh toán thẻ Giftcard** | Reserve số tiền khi checkout | Thanh toán online |
| 5 | **Hủy thanh toán thẻ Giftcard** | Release/Cancel khi hủy đơn | Hủy đơn hàng |
| 6 | **Truy vấn danh sách thẻ Giftcard** | Lấy danh sách thẻ của member | Danh sách thẻ |
| 7 | **Truy vấn lịch sử giao dịch thẻ Giftcard** | Lấy lịch sử giao dịch | Lịch sử GD |

### 1.3. Chi Tiết Từng API

#### 1.3.1. Tạo Mới Thẻ Giftcard Online

| Thuộc Tính | Chi Tiết |
|------------|----------|
| **Mục đích** | Tạo thẻ gift card mới sau khi thanh toán thành công |
| **Gọi từ** | Mango CMS |
| **Thời điểm gọi** | Sau khi Payment Gateway callback thành công |
| **Dữ liệu gửi đi** | Member ID, số tiền, loại thẻ, thông tin người tặng (nếu có) |
| **Dữ liệu trả về** | Card ID, mã thẻ, mã PIN, HSD |
| **Xử lý tiếp theo** | Mango CMS gửi mã thẻ qua SMS/ZNS/Email |

#### 1.3.2. Kích Hoạt Thẻ Giftcard

| Thuộc Tính | Chi Tiết |
|------------|----------|
| **Mục đích** | Kích hoạt thẻ và liên kết với tài khoản member |
| **Gọi từ** | Mango CMS |
| **Thời điểm gọi** | Khi user nhập mã thẻ + PIN và xác nhận |
| **Dữ liệu gửi đi** | Mã thẻ, mã PIN, Member ID |
| **Dữ liệu trả về** | Kết quả kích hoạt, thông tin thẻ (số dư, HSD) |
| **Xử lý tiếp theo** | Mango CMS gửi thông báo thành công |

#### 1.3.3. Nạp Tiền Thẻ Giftcard Online

| Thuộc Tính | Chi Tiết |
|------------|----------|
| **Mục đích** | Cộng tiền vào thẻ sau khi thanh toán thành công |
| **Gọi từ** | Mango CMS |
| **Thời điểm gọi** | Sau khi Payment Gateway callback thành công |
| **Dữ liệu gửi đi** | Card ID, số tiền nạp |
| **Dữ liệu trả về** | Số dư mới, HSD mới |
| **Xử lý tiếp theo** | Mango CMS gửi thông báo nạp tiền thành công |

#### 1.3.4. Tạm Giữ Thanh Toán Thẻ Giftcard (Reserve)

| Thuộc Tính | Chi Tiết |
|------------|----------|
| **Mục đích** | Giữ chỗ số tiền từ thẻ khi user đặt hàng |
| **Gọi từ** | Mango CMS |
| **Thời điểm gọi** | Khi user xác nhận đặt hàng với gift card |
| **Dữ liệu gửi đi** | Card ID(s), số tiền tạm giữ, Order ID |
| **Dữ liệu trả về** | Reserve ID, thời gian hết hạn |
| **Timeout** | 30 phút (auto release nếu không confirm) |

#### 1.3.5. Hủy Thanh Toán Thẻ Giftcard (Cancel/Release)

| Thuộc Tính | Chi Tiết |
|------------|----------|
| **Mục đích** | Hoàn tiền về thẻ khi đơn hàng bị hủy hoặc timeout |
| **Gọi từ** | Mango CMS hoặc LS Retail (auto) |
| **Thời điểm gọi** | Khi đơn hàng bị hủy hoặc hết thời gian reserve |
| **Dữ liệu gửi đi** | Reserve ID hoặc Order ID |
| **Dữ liệu trả về** | Kết quả hoàn tiền, số dư mới |
| **Xử lý tiếp theo** | Mango CMS gửi thông báo hoàn tiền (nếu do hủy đơn) |

#### 1.3.6. Truy Vấn Danh Sách Thẻ Giftcard

| Thuộc Tính | Chi Tiết |
|------------|----------|
| **Mục đích** | Lấy danh sách thẻ của một member |
| **Gọi từ** | Mango CMS |
| **Thời điểm gọi** | Khi user mở màn hình danh sách gift card |
| **Dữ liệu gửi đi** | Member ID, filter (trạng thái, loại thẻ) |
| **Dữ liệu trả về** | Danh sách thẻ với thông tin: ID, mã thẻ (masked), số dư, trạng thái, HSD |
| **Cache** | Mango CMS cache 2 phút |

#### 1.3.7. Truy Vấn Lịch Sử Giao Dịch Thẻ Giftcard

| Thuộc Tính | Chi Tiết |
|------------|----------|
| **Mục đích** | Lấy lịch sử giao dịch của một thẻ |
| **Gọi từ** | Mango CMS |
| **Thời điểm gọi** | Khi user mở màn hình lịch sử giao dịch |
| **Dữ liệu gửi đi** | Card ID, khoảng thời gian, loại giao dịch, phân trang |
| **Dữ liệu trả về** | Danh sách giao dịch với thông tin chi tiết |
| **Cache** | Không cache (real-time) |

### 1.4. Webhook Events (LS Retail → Mango CMS)

LS Retail sẽ gửi webhook đến Mango CMS khi có các sự kiện quan trọng:

| Event | Mô Tả | Xử Lý Tại Mango CMS |
|-------|-------|---------------------|
| **giftcard.activated** | Thẻ được kích hoạt | Gửi push notification xác nhận |
| **giftcard.toppedup** | Thẻ được nạp tiền | Gửi push + SMS/Email xác nhận |
| **giftcard.used** | Thẻ được sử dụng (online/offline) | Gửi push notification giao dịch |
| **giftcard.locked** | Thẻ bị khóa | Gửi push notification cảnh báo |
| **giftcard.expiring** | Thẻ sắp hết hạn (30 ngày, 7 ngày) | Gửi cảnh báo Push/Email/SMS |
| **giftcard.expired** | Thẻ đã hết hạn | Gửi thông báo, invalidate cache |
| **giftcard.gifted** | Nhận được thẻ tặng | Gửi thông báo đến người nhận |

### 1.5. Xử Lý Lỗi API

#### Retry Policy

| Loại Lỗi | Retry | Thời Gian Chờ |
|----------|-------|---------------|
| **Timeout** | 3 lần | 1s, 2s, 4s (exponential backoff) |
| **Server Error (5xx)** | 3 lần | 1s, 2s, 4s |
| **Client Error (4xx)** | Không retry | - |
| **Network Error** | 3 lần | 1s, 2s, 4s |

#### Fallback Handling

| Trường Hợp | Xử Lý |
|------------|-------|
| **Tạo thẻ thất bại** | Hoàn tiền qua Payment Gateway, thông báo user |
| **Kích hoạt thất bại** | Thông báo user thử lại, log để kiểm tra |
| **Nạp tiền thất bại** | Hoàn tiền qua Payment Gateway, thông báo user |
| **Reserve thất bại** | Thông báo user chọn phương thức khác |

---

## 2. Gửi Thông Báo (SMS/ZNS/Email/Push)

### 2.1. Tổng Quan

Mango CMS chịu trách nhiệm gửi thông báo đến khách hàng thông qua các kênh:

- **Push Notification:** Thông báo real-time trên app
- **SMS:** Tin nhắn văn bản đến số điện thoại
- **Zalo ZNS:** Thông báo qua Zalo (nếu user có liên kết Zalo)
- **Email:** Thư điện tử

### 2.2. Phân Chia Trách Nhiệm

| Hệ Thống | Nhiệm Vụ |
|----------|----------|
| **LS Retail** | Phát sinh sự kiện (webhook) cần thông báo |
| **Mango CMS** | Nhận webhook, xác định kênh gửi, gửi thông báo |
| **Firebase** | Xử lý push notification |
| **SMS Gateway** | Gửi tin nhắn SMS |
| **Zalo ZNS** | Gửi thông báo Zalo |
| **Email Service** | Gửi email |

### 2.3. Các Loại Thông Báo

#### 2.3.1. Thông Báo Mua Thẻ / Nhận Thẻ Tặng

| Thông Tin | Chi Tiết |
|-----------|----------|
| **Khi nào** | Sau khi tạo thẻ thành công |
| **Kênh** | SMS + ZNS + Email |
| **Nội dung** | Mã thẻ, mã PIN, hướng dẫn kích hoạt |
| **Ưu tiên** | Cao - Phải gửi ngay |

**Nội dung mẫu:**
```
TH eLIFE: Bạn đã mua Gift Card thành công.
Mã thẻ: XXXX-XXXX-XXXX-1234
Mã PIN: ****56
Truy cập app TH eLIFE để kích hoạt và sử dụng.
HSD: 12 tháng.
```

#### 2.3.2. Thông Báo Kích Hoạt Thành Công

| Thông Tin | Chi Tiết |
|-----------|----------|
| **Khi nào** | Sau khi kích hoạt thẻ thành công |
| **Kênh** | Push Notification |
| **Nội dung** | Xác nhận kích hoạt, số dư, HSD |

**Nội dung mẫu:**
```
Gift Card đã kích hoạt thành công!
Số dư: 500.000đ
Hạn sử dụng: 15/01/2026
```

#### 2.3.3. Thông Báo Nạp Tiền Thành Công

| Thông Tin | Chi Tiết |
|-----------|----------|
| **Khi nào** | Sau khi nạp tiền thành công |
| **Kênh** | Push + SMS/Email |
| **Nội dung** | Số tiền nạp, số dư mới, HSD mới |

**Nội dung mẫu:**
```
Nạp tiền Gift Card thành công!
+500.000đ
Số dư mới: 1.000.000đ
HSD mới: 15/01/2026
```

#### 2.3.4. Thông Báo Giao Dịch (Chi Tiêu)

| Thông Tin | Chi Tiết |
|-----------|----------|
| **Khi nào** | Sau khi thanh toán bằng gift card (online/offline) |
| **Kênh** | Push Notification |
| **Nội dung** | Số tiền, nơi thanh toán, số dư còn lại |

**Nội dung mẫu (Online):**
```
Thanh toán Gift Card
-150.000đ
Đơn hàng #ORD_123456
Số dư còn: 350.000đ
```

**Nội dung mẫu (Offline):**
```
Thanh toán Gift Card
-85.000đ
TH True Mart Nguyễn Trãi
Số dư còn: 265.000đ
```

#### 2.3.5. Cảnh Báo Sắp Hết Hạn

| Thông Tin | Chi Tiết |
|-----------|----------|
| **Khi nào** | Trước 30 ngày và 7 ngày khi hết hạn |
| **Kênh** | Push + Email (30 ngày), Push + Email + SMS (7 ngày) |
| **Nội dung** | Thẻ sắp hết hạn, gợi ý nạp tiền để gia hạn |

**Nội dung mẫu (30 ngày):**
```
Gift Card sắp hết hạn!
Thẻ ***1234 sẽ hết hạn trong 30 ngày.
Số dư: 200.000đ
Nạp tiền ngay để gia hạn thêm 12 tháng.
```

**Nội dung mẫu (7 ngày):**
```
KHẨN: Gift Card sắp hết hạn!
Thẻ ***1234 sẽ hết hạn trong 7 ngày.
Số dư: 200.000đ
Nạp tiền NGAY để không mất số dư!
```

#### 2.3.6. Thông Báo Khóa Thẻ

| Thông Tin | Chi Tiết |
|-----------|----------|
| **Khi nào** | Sau khi thẻ bị khóa |
| **Kênh** | Push + Email |
| **Nội dung** | Xác nhận khóa, hướng dẫn mở khóa |

**Nội dung mẫu:**
```
Gift Card đã bị khóa
Thẻ ***1234 đã được khóa theo yêu cầu.
Số dư 200.000đ được giữ nguyên.
Liên hệ hotline 1800-xxxx để mở khóa.
```

### 2.4. Ưu Tiên Kênh Gửi

Khi cần gửi thông báo quan trọng (như mã kích hoạt), hệ thống sẽ gửi theo thứ tự ưu tiên:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         THỨ TỰ ƯU TIÊN                                  │
└─────────────────────────────────────────────────────────────────────────┘

1. Zalo ZNS (nếu user có liên kết Zalo)
   │
   │ Thất bại?
   ▼
2. SMS
   │
   │ Thất bại?
   ▼
3. Email
   │
   │ Thất bại?
   ▼
4. Lưu vào queue, retry sau
   + Thông báo admin để xử lý
```

### 2.5. Xử Lý Gửi Thất Bại

| Kênh | Retry | Fallback |
|------|-------|----------|
| **Push** | 3 lần | Không fallback (user có thể không online) |
| **SMS** | 3 lần | Fallback sang Email |
| **ZNS** | 3 lần | Fallback sang SMS |
| **Email** | 3 lần | Fallback sang SMS |

### 2.6. Luồng Gửi Thông Báo

```
┌────────────────┐
│   LS Retail    │
│  phát sinh     │
│  sự kiện       │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Webhook đến   │
│  Mango CMS     │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  xác định:     │
│  - Loại thông  │
│    báo         │
│  - Kênh gửi    │
│  - Template    │
└───────┬────────┘
        │
        ├─────────────────┬─────────────────┬─────────────────┐
        │                 │                 │                 │
        ▼                 ▼                 ▼                 ▼
┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│   Firebase   │  │  SMS Gateway │  │  Zalo ZNS    │  │    Email     │
│   (Push)     │  │              │  │              │  │   Service    │
└──────────────┘  └──────────────┘  └──────────────┘  └──────────────┘
        │                 │                 │                 │
        └─────────────────┴─────────────────┴─────────────────┘
                                   │
                                   ▼
                          ┌──────────────┐
                          │  Log kết quả │
                          │  gửi thông   │
                          │  báo         │
                          └──────────────┘
```

---

## 3. Cache Strategy

### 3.1. Tổng Quan Cache

Mango CMS sử dụng Redis để cache dữ liệu từ LS Retail nhằm giảm tải và tăng tốc độ phản hồi.

### 3.2. Chi Tiết Cache

| Dữ Liệu | TTL | Invalidation |
|---------|-----|--------------|
| **Danh sách gift card** | 2 phút | Khi có thay đổi trạng thái/số dư bất kỳ thẻ |
| **Chi tiết gift card** | 1 phút | Khi có giao dịch hoặc đổi trạng thái |
| **Lịch sử giao dịch** | Không cache | Luôn lấy real-time từ LS Retail |

### 3.3. Cơ Chế Invalidate Cache

```
┌────────────────┐
│   LS Retail    │
│  gửi webhook   │
│  sự kiện       │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  nhận webhook  │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Xác định các  │
│  cache key     │
│  liên quan     │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Redis        │
│   DEL keys     │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Request tiếp  │
│  theo sẽ lấy   │
│  data mới từ   │
│  LS Retail     │
└────────────────┘
```

---

## 4. Tóm Tắt

### 4.1. Tích Hợp LS Retail

| Hạng Mục | Chi Tiết |
|----------|----------|
| **Số lượng API** | 7 API chính |
| **Webhook Events** | 7 loại sự kiện |
| **Retry Policy** | 3 lần với exponential backoff |
| **Timeout** | Cấu hình theo từng API |

### 4.2. Gửi Thông Báo

| Hạng Mục | Chi Tiết |
|----------|----------|
| **Kênh** | Push, SMS, ZNS, Email |
| **Số loại thông báo** | 6+ loại chính |
| **Ưu tiên** | ZNS → SMS → Email |
| **Retry** | 3 lần mỗi kênh |

### 4.3. Cache

| Hạng Mục | Chi Tiết |
|----------|----------|
| **Công nghệ** | Redis |
| **TTL** | 1-2 phút cho gift card, không cache lịch sử |
| **Invalidation** | Qua webhook từ LS Retail |

---

*Tài liệu này mô tả chi tiết việc tích hợp với LS Retail và cơ chế gửi thông báo cho module Gift Card.*
