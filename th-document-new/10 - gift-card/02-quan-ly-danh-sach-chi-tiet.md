# QUẢN LÝ DANH SÁCH & CHI TIẾT GIFT CARD

> **Tham chiếu SOW:** B.1 - Module 12: Quản lý Giftcard
> **Chức năng:** Khách hàng xem và quản lý các gift card đang sở hữu

---

## 1. Quản Lý Danh Sách Gift Card

### 1.1. Mô Tả Chức Năng

Màn hình danh sách Gift Card cho phép khách hàng thành viên xem tổng quan tất cả các thẻ gift card mình đang sở hữu. Khách hàng có thể:

- Xem tổng số thẻ và tổng số dư khả dụng
- Phân loại thẻ theo tab: Thẻ mua cho bản thân / Thẻ được tặng
- Lọc thẻ theo trạng thái
- Sắp xếp theo tiêu chí mong muốn
- Thực hiện các thao tác nhanh với từng thẻ

### 1.2. Phân Chia Trách Nhiệm Hệ Thống

#### LS Retail - Nhiệm Vụ Chính

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Cung cấp dữ liệu thẻ** | Trả về danh sách tất cả gift card của khách hàng bao gồm: mã thẻ, số dư, trạng thái, ngày tạo, ngày kích hoạt, hạn sử dụng |
| **Lọc theo member** | Lọc danh sách thẻ theo member ID của khách hàng đang đăng nhập |
| **Cung cấp trạng thái real-time** | Đảm bảo trạng thái và số dư thẻ luôn chính xác |

#### Mango CMS - Nhiệm Vụ Điều Phối

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Nhận request từ Frontend** | Xác thực user session, lấy member ID |
| **Gọi API LS Retail** | Gọi API truy vấn danh sách thẻ với member ID |
| **Cache dữ liệu** | Lưu cache danh sách thẻ với TTL 2 phút để giảm tải cho LS Retail |
| **Tổng hợp response** | Tính tổng số thẻ, tổng số dư, nhóm theo trạng thái/loại |
| **Xử lý lọc & sắp xếp** | Hỗ trợ lọc theo trạng thái, sắp xếp theo tiêu chí |

#### Backend Loyalty

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Không tham gia trực tiếp** | Gift card là nghiệp vụ của LS Retail |
| **Cung cấp member ID** | Member ID từ Loyalty được dùng để liên kết thẻ |

### 1.3. Các Trạng Thái Gift Card

| Trạng Thái | Ý Nghĩa | Actions Khả Dụng |
|------------|---------|------------------|
| **Chưa kích hoạt** | Thẻ mới mua/nhận, chưa sử dụng được | Kích hoạt |
| **Đã kích hoạt** | Thẻ đang hoạt động, có thể sử dụng | Nạp tiền, Xem lịch sử, Khóa thẻ |
| **Hết tiền** | Số dư = 0, vẫn còn hiệu lực | Nạp tiền, Xem lịch sử |
| **Bị khóa** | Thẻ bị khóa theo yêu cầu hoặc vi phạm | Liên hệ CSKH để mở khóa |
| **Hết hạn** | Quá hạn sử dụng, không dùng được | Mua thẻ mới |

### 1.4. Thông Tin Hiển Thị Mỗi Thẻ

Mỗi thẻ trong danh sách hiển thị các thông tin:

- **Theme/Hình ảnh:** Hình ảnh chủ đề của thẻ (nếu có)
- **Loại thẻ:** THẺ QUÀ TẶNG, hoặc các loại đặc biệt
- **Trạng thái:** Badge màu thể hiện trạng thái hiện tại
- **Số dư:** Số tiền còn lại trong thẻ (định dạng VND)
- **Mã thẻ:** Hiển thị dạng masked (VD: 4848 04** **** 4254)
- **Ngày tạo:** Ngày mua/nhận thẻ
- **Ngày kích hoạt:** Ngày thẻ được kích hoạt (nếu có)
- **Hạn sử dụng:** Ngày hết hạn của thẻ
- **Barcode:** Mã vạch để quét tại cửa hàng

### 1.5. Luồng Hoạt Động

```
┌────────────────┐
│   Khách hàng   │
│  mở danh sách  │
│   Gift Card    │
└───────┬────────┘
        │
        ▼
┌────────────────┐      ┌────────────────┐
│   Mango CMS    │──────│  Kiểm tra      │
│  nhận request  │      │  cache Redis   │
└───────┬────────┘      └───────┬────────┘
        │                       │
        │    Cache còn hiệu lực?│
        │          ┌────────────┴────────────┐
        │          │                         │
        │         CÓ                       KHÔNG
        │          │                         │
        │          ▼                         ▼
        │   ┌──────────────┐         ┌──────────────┐
        │   │ Trả về cache │         │  Gọi API     │
        │   │              │         │  LS Retail   │
        │   └──────────────┘         └───────┬──────┘
        │                                    │
        │                                    ▼
        │                            ┌──────────────┐
        │                            │  LS Retail   │
        │                            │ trả danh sách│
        │                            └───────┬──────┘
        │                                    │
        │                                    ▼
        │                            ┌──────────────┐
        │                            │  Mango CMS   │
        │                            │ cache + tổng │
        │                            │    hợp       │
        │                            └───────┬──────┘
        │                                    │
        └────────────────────────────────────┘
                         │
                         ▼
                 ┌───────────────┐
                 │  Trả response │
                 │  cho Frontend │
                 └───────────────┘
```

---

## 2. Chi Tiết Gift Card

### 2.1. Mô Tả Chức Năng

Màn hình chi tiết cho phép khách hàng xem đầy đủ thông tin của một gift card cụ thể và thực hiện các thao tác với thẻ đó.

### 2.2. Phân Chia Trách Nhiệm Hệ Thống

#### LS Retail - Nhiệm Vụ Chính

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Cung cấp chi tiết thẻ** | Trả về đầy đủ thông tin: mã thẻ (full), số dư, trạng thái, ngày kích hoạt, hạn sử dụng |
| **Cung cấp barcode data** | Dữ liệu barcode để generate mã vạch |

#### Mango CMS - Nhiệm Vụ Điều Phối

| Nhiệm Vụ | Chi Tiết |
|----------|----------|
| **Nhận request từ Frontend** | Xác thực user session, card ID |
| **Gọi API LS Retail** | Gọi API lấy chi tiết thẻ |
| **Generate QR Code** | Tạo QR code cho thanh toán offline (có expire time) |
| **Cache dữ liệu** | Lưu cache chi tiết thẻ với TTL 1 phút |
| **Điều phối actions** | Điều hướng đến các chức năng: Nạp tiền, Lịch sử, Khóa thẻ |

### 2.3. Thông Tin Hiển Thị Chi Tiết

| Thông Tin | Mô Tả | Nguồn Dữ Liệu |
|-----------|-------|---------------|
| **Theme/Hình ảnh** | Hình ảnh chủ đề thẻ | LS Retail |
| **Loại thẻ** | Tên loại thẻ (VD: THẺ QUÀ TẶNG) | LS Retail |
| **Trạng thái** | Badge + màu trạng thái | LS Retail |
| **Số dư** | Số tiền còn lại (VD: 1.000.000 VND) | LS Retail |
| **Mã thẻ đầy đủ** | Hiển thị full số thẻ | LS Retail |
| **Ngày kích hoạt** | Ngày thẻ được kích hoạt | LS Retail |
| **Hạn sử dụng** | Ngày hết hạn | LS Retail |
| **Barcode** | Mã vạch để quét tại cửa hàng | LS Retail + Mango CMS generate |
| **QR Code** | Mã QR để thanh toán offline | Mango CMS generate |

### 2.4. QR Code Cho Thanh Toán Offline

Khi khách hàng muốn thanh toán tại cửa hàng TrueMart, họ hiển thị QR code trên màn hình chi tiết thẻ để nhân viên quét.

**Đặc điểm QR Code:**

| Thuộc Tính | Giá Trị |
|------------|---------|
| **Thời gian hiệu lực** | 5 phút |
| **Tự động refresh** | Khi user refresh màn hình hoặc hết hạn |
| **Nội dung mã hóa** | Card ID + Member ID + Timestamp + Signature |
| **Bảo mật** | Chống replay attack bằng timestamp |

**Luồng xử lý QR Code:**

```
┌────────────────┐
│   Khách hàng   │
│ mở chi tiết thẻ│
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│ generate QR    │
│ (expire 5 phút)│
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Hiển thị QR   │
│  trên màn hình │
└───────┬────────┘
        │
        ▼ (Khi thanh toán)
┌────────────────┐
│  Nhân viên     │
│  quét QR/      │
│  Barcode       │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│     POS        │
│ gửi mã đến     │
│   LS Retail    │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   LS Retail    │
│ xác thực +     │
│ trừ tiền       │
└────────────────┘
```

### 2.5. Các Actions Theo Trạng Thái Thẻ

| Trạng Thái | Actions Khả Dụng |
|------------|------------------|
| **Chưa kích hoạt** | Kích hoạt |
| **Đã kích hoạt** | Nạp tiền, Xem lịch sử giao dịch, Khóa thẻ |
| **Hết tiền** | Nạp tiền, Xem lịch sử giao dịch |
| **Bị khóa** | Xem lịch sử giao dịch, Hướng dẫn mở khóa (liên hệ CSKH) |
| **Hết hạn** | Xem lịch sử giao dịch, Gợi ý mua thẻ mới |

### 2.6. Luồng Hoạt Động Xem Chi Tiết

```
┌────────────────┐
│   Khách hàng   │
│ chọn xem chi   │
│ tiết một thẻ   │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Mango CMS    │
│  nhận request  │
│  (card ID)     │
└───────┬────────┘
        │
        ├─────────────────────────┐
        │                         │
        ▼                         ▼
┌────────────────┐       ┌────────────────┐
│  Kiểm tra      │       │  Gọi API       │
│  cache Redis   │       │  LS Retail     │
│  (TTL 1 phút)  │       │  lấy chi tiết  │
└───────┬────────┘       └───────┬────────┘
        │                        │
        │      ┌─────────────────┘
        │      │
        ▼      ▼
┌────────────────┐
│   Mango CMS    │
│  - Tổng hợp    │
│  - Generate QR │
│  - Cache data  │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Trả response  │
│  cho Frontend  │
│  (card detail  │
│   + QR code)   │
└────────────────┘
```

---

## 3. Invalidate Cache

### 3.1. Khi Nào Cache Bị Xóa

Cache danh sách và chi tiết gift card sẽ bị xóa (invalidate) khi có các sự kiện sau:

| Sự Kiện | Invalidate Cache |
|---------|------------------|
| Kích hoạt thẻ mới | Danh sách + Chi tiết thẻ đó |
| Nạp tiền vào thẻ | Chi tiết thẻ đó |
| Sử dụng thẻ thanh toán | Chi tiết thẻ đó |
| Khóa thẻ | Danh sách + Chi tiết thẻ đó |
| Mua thẻ mới | Danh sách |
| Thẻ hết hạn | Danh sách + Chi tiết thẻ đó |

### 3.2. Cơ Chế Invalidate

```
┌────────────────┐
│  LS Retail     │
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
│  Mango CMS     │
│  xóa cache     │
│  liên quan     │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Lần request   │
│  tiếp theo     │
│  lấy data mới  │
└────────────────┘
```

---

## 4. Tóm Tắt Phân Công

### Danh Sách Gift Card

| Hệ Thống | Vai Trò | Nhiệm Vụ Cụ Thể |
|----------|---------|-----------------|
| **LS Retail** | Source of Truth | Cung cấp dữ liệu danh sách thẻ, trạng thái, số dư |
| **Mango CMS** | Middleware | Điều phối API, cache 2 phút, tổng hợp response |
| **Loyalty** | Không tham gia | - |

### Chi Tiết Gift Card

| Hệ Thống | Vai Trò | Nhiệm Vụ Cụ Thể |
|----------|---------|-----------------|
| **LS Retail** | Source of Truth | Cung cấp chi tiết thẻ, barcode data |
| **Mango CMS** | Middleware | Điều phối API, cache 1 phút, generate QR code |
| **Loyalty** | Không tham gia | - |

---

*Tài liệu này mô tả chi tiết chức năng Quản lý danh sách và Chi tiết Gift Card, bao gồm phân chia trách nhiệm giữa các hệ thống backend.*
