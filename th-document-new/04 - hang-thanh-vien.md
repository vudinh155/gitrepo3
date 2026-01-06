# MODULE HẠNG THÀNH VIÊN & THÔNG BÁO - PHÂN CHIA TRÁCH NHIỆM HỆ THỐNG

> Tài liệu này giải thích cách phân chia trách nhiệm giữa các hệ thống cho **Module 3, 4, 10: Hạng Thành Viên và Hệ thống Thông báo** trong ứng dụng TH Milk.

## MỤC LỤC

1. [Tổng quan Module](#1-tổng-quan-module)
2. [Ma trận phân chia trách nhiệm](#2-ma-trận-phân-chia-trách-nhiệm)
3. [Phần A: Quản lý Hạng Thành Viên (Module 3, 4)](#3-phần-a-quản-lý-hạng-thành-viên-module-3-4)
4. [Phần B: Hệ thống Thông báo (Module 10)](#4-phần-b-hệ-thống-thông-báo-module-10)
5. [Cơ chế webhook giữa các hệ thống](#5-cơ-chế-webhook-giữa-các-hệ-thống)
6. [Tổng kết](#6-tổng-kết)

---

## 1. TỔNG QUAN MODULE

### 1.1. Ba module trong một nhóm


| Module        | Tên gọi                     | Chức năng chính                          |
| --------------- | ------------------------------- | --------------------------------------------- |
| **Module 3**  | Thông tin Hạng Thành Viên | Hiển thị hạng, quyền lợi, tiến trình |
| **Module 4**  | Lịch sử Hạng & Điểm      | Tra cứu lịch sử thay đổi               |
| **Module 10** | Hệ thống Thông báo        | Push notification, in-app notification      |

### 1.2. Mối liên hệ giữa ba module

```
┌───────────────────────────────────────────────────────────────────┐
│                    MỐI LIÊN HỆ LOGIC                              │
├───────────────────────────────────────────────────────────────────┤
│                                                                   │
│   HẠNG THÀNH VIÊN (3,4)  ────────────►  THÔNG BÁO (10)            │
│                                                                   │
│   • Nâng hạng          ───────────────►  Gửi thông báo chúc mừng  │
│   • Điểm sắp hết hạn   ───────────────►  Gửi thông báo nhắc nhở   │
│   • Quyền lợi mới      ───────────────►  Gửi thông báo thông tin  │
│   • Gần đủ điểm nâng   ───────────────►  Gửi thông báo khích lệ   │
│                                                                   │
└───────────────────────────────────────────────────────────────────┘
```

### 1.3. Các chức năng cụ thể

**Module 3 - Thông tin Hạng:**

- Hạng hiện tại và biểu tượng
- Thanh tiến trình nâng hạng
- Danh sách quyền lợi
- Điều kiện nâng/giữ hạng
- Danh sách tất cả các hạng

**Module 4 - Lịch sử:**

- Lịch sử thay đổi hạng (timeline)
- Lịch sử giao dịch điểm (Hoa Đất)
- Bộ lọc theo thời gian, loại giao dịch

**Module 10 - Thông báo:**

- Push notification (iOS, Android, Web)
- In-app notification
- Lịch sử thông báo
- Phân loại theo danh mục

---

## 2. MA TRẬN PHÂN CHIA TRÁCH NHIỆM

### 2.1. Tổng quan phân chia


| Chức năng                      | Backend Loyalty |   Backend LS Retail   |        Mango CMS        |
| ---------------------------------- | :---------------: | :----------------------: | :-----------------------: |
| **MODULE 3: HẠNG THÀNH VIÊN** |                 |                       |                         |
| Thông tin hạng hiện tại      |  ✅**Primary**  |           -           |   Cache + Hiển thị   |
| Thanh tiến trình               |  ✅**Primary**  |           -           |       Hiển thị       |
| Danh sách tất cả hạng        |  ✅**Primary**  |           -           |     Cache (static)     |
| Điều kiện nâng/giữ hạng    |  ✅**Primary**  |           -           |          Cache          |
| Tính toán nâng/giảm hạng    |  ✅**Primary**  |           -           |            -            |
| **MODULE 4: LỊCH SỬ**          |                 |                       |                         |
| Lịch sử thay đổi hạng       |  ✅**Primary**  |           -           |       Hiển thị       |
| Lịch sử điểm                 |  ✅**Primary**  | Điểm từ đơn hàng | Tổng hợp + Hiển thị |
| **MODULE 10: THÔNG BÁO**       |                 |                       |                         |
| Lưu trữ thông báo            |        -        |           -           |      ✅**Primary**      |
| Gửi Push Notification           |        -        |           -           |      ✅**Primary**      |
| In-app Notification              |        -        |           -           |      ✅**Primary**      |
| Quản lý device token           |        -        |           -           |      ✅**Primary**      |
| Kích hoạt thông báo          |     Webhook     |        Webhook        |     Nhận + Xử lý     |
| Scheduled notification           |        -        |           -           |      ✅**Primary**      |

### 2.2. Giải thích vai trò từng hệ thống

#### Backend Loyalty - "Nguồn sự thật" về Thành viên


| Trách nhiệm                 | Mô tả                                                    |
| ------------------------------- | ------------------------------------------------------------ |
| **Quản lý hạng**           | Lưu trữ và tính toán hạng của tất cả khách hàng |
| **Tính toán điểm**        | Xử lý tích lũy, tiêu điểm, hết hạn điểm         |
| **Quy tắc nâng/giữ hạng** | Định nghĩa điều kiện, thực hiện tự động         |
| **Lưu lịch sử**            | Ghi nhận mọi thay đổi về hạng và điểm             |
| **Gửi webhook**              | Thông báo cho Mango CMS khi có sự kiện                |

#### Backend LS Retail - Cung cấp dữ liệu đơn hàng


| Trách nhiệm             | Mô tả                                      |
| --------------------------- | ---------------------------------------------- |
| **Điểm từ mua hàng**  | Thông tin điểm tích lũy từ đơn hàng |
| **Chi tiết đơn hàng** | Reference cho lịch sử điểm               |
| **Gửi webhook**          | Thông báo trạng thái đơn hàng         |

#### Mango CMS - Điều phối và Thông báo


| Trách nhiệm             | Mô tả                               |
| --------------------------- | --------------------------------------- |
| **Cache dữ liệu**       | Lưu tạm để truy vấn nhanh        |
| **Điều phối API**      | Gọi đến Loyalty/LS Retail khi cần |
| **Quản lý thông báo** | Toàn bộ hệ thống notification     |
| **Nhận webhook**         | Xử lý sự kiện từ các backend    |
| **Scheduled jobs**        | Chạy các tác vụ định kỳ        |

---

## 3. PHẦN A: QUẢN LÝ HẠNG THÀNH VIÊN (MODULE 3, 4)

### 3.1. Thông tin Hạng Hiện Tại

#### Luồng xử lý

```
┌──────────┐      ┌───────────┐      ┌───────────┐
│ Frontend │─────▶│ Mango CMS │─────▶│  Loyalty  │
└──────────┘      └───────────┘      └───────────┘
     │                  │                  │
     │  Request thông   │                  │
     │  tin hạng        │                  │
     │─────────────────▶│                  │
     │                  │                  │
     │                  │  Check cache     │
     │                  │  (TTL 5 phút)    │
     │                  │                  │
     │                  │  Nếu cache miss  │
     │                  │─────────────────▶│
     │                  │                  │
     │                  │◀─────────────────│
     │                  │  Hạng + Điểm +   │
     │                  │  Quyền lợi       │
     │                  │                  │
     │◀─────────────────│                  │
     │  Response        │                  │
```

#### Phân chia trách nhiệm


| Bước | Hệ thống    | Trách nhiệm                                      |
| -------- | --------------- | ---------------------------------------------------- |
| 1      | **Mango CMS** | Nhận request, check cache                         |
| 2      | **Loyalty**   | Trả về: hạng, điểm, tiến trình, quyền lợi |
| 3      | **Mango CMS** | Cache 5 phút, trả về Frontend                   |

#### Dữ liệu trả về


| Trường              | Nguồn  | Mô tả                     |
| ----------------------- | --------- | ----------------------------- |
| Tên hạng, icon      | Loyalty | "Vàng", gold_icon          |
| Điểm xếp hạng     | Loyalty | 15.000 điểm               |
| Tiến trình          | Loyalty | 50% đến hạng Kim Cương |
| Quyền lợi           | Loyalty | Danh sách benefits         |
| Thời hạn hiệu lực | Loyalty | 31/12/2024                  |

---

### 3.2. Danh sách Tất cả Hạng

#### Phân chia trách nhiệm


| Bước | Hệ thống                      | Trách nhiệm                               |
| -------- | --------------------------------- | --------------------------------------------- |
| 1      | **Mango CMS**                   | Nhận request, check cache                  |
| 2      | **Loyalty** hoặc **Mango CMS** | Trả về danh sách hạng (dữ liệu tĩnh) |
| 3      | **Mango CMS**                   | Cache 1 giờ (ít thay đổi)               |

**Lưu ý:** Danh sách hạng là dữ liệu tĩnh, có thể lưu trữ lâu dài trên Mango CMS.

---

### 3.3. Điều kiện Nâng/Giữ Hạng

#### Quy tắc xử lý (Backend Loyalty)


| Hành động    | Điều kiện                          | Thời điểm              |
| ----------------- | --------------------------------------- | --------------------------- |
| **Nâng hạng** | Đạt đủ điểm của hạng cao hơn | Ngay lập tức            |
| **Giữ hạng**  | Duy trì đủ điểm trong chu kỳ    | Cuối chu kỳ đánh giá |
| **Giảm hạng** | Không đủ điểm khi hết chu kỳ   | Cuối chu kỳ đánh giá |

#### Phân chia trách nhiệm


| Trách nhiệm                     | Hệ thống    |
| ----------------------------------- | --------------- |
| Định nghĩa ngưỡng điểm     | **Loyalty**   |
| Tính toán nâng/giảm hạng     | **Loyalty**   |
| Gửi webhook khi thay đổi hạng | **Loyalty**   |
| Hiển thị điều kiện cho user  | **Mango CMS** |

---

### 3.4. Lịch sử Hạng Thành Viên

#### Các loại sự kiện


| Event       | Mô tả         | Trigger                     |
| ------------- | ----------------- | ----------------------------- |
| `upgrade`   | Nâng hạng     | Đạt đủ điểm           |
| `downgrade` | Giảm hạng     | Hết chu kỳ, thiếu điểm |
| `maintain`  | Giữ hạng      | Hết chu kỳ, đủ điểm   |
| `register`  | Đăng ký mới | Tạo tài khoản            |

#### Phân chia trách nhiệm


| Bước | Hệ thống    | Trách nhiệm                        |
| -------- | --------------- | -------------------------------------- |
| 1      | **Loyalty**   | Lưu trữ lịch sử thay đổi hạng |
| 2      | **Mango CMS** | Lấy dữ liệu, format timeline      |
| 3      | **Frontend**  | Hiển thị dạng dòng thời gian    |

---

### 3.5. Lịch sử Điểm Thành Viên

#### Luồng xử lý

```
┌──────────┐      ┌───────────┐      ┌───────────┐      ┌───────────┐
│ Frontend │─────▶│ Mango CMS │─────▶│  Loyalty  │─────▶│ LS Retail │
└──────────┘      └───────────┘      └───────────┘      └───────────┘
     │                  │                  │                  │
     │  Request lịch    │                  │                  │
     │  sử điểm         │                  │                  │
     │─────────────────▶│                  │                  │
     │                  │                  │                  │
     │                  │─────────────────▶│                  │
     │                  │                  │                  │
     │                  │                  │  Lấy chi tiết    │
     │                  │                  │  đơn hàng        │
     │                  │                  │─────────────────▶│
     │                  │                  │                  │
     │                  │                  │◀─────────────────│
     │                  │                  │                  │
     │                  │◀─────────────────│                  │
     │                  │  Lịch sử gộp     │                  │
     │                  │                  │                  │
     │◀─────────────────│                  │                  │
```

#### Các loại giao dịch điểm


| Loại           | Nguồn               | Mô tả                       |
| ----------------- | ---------------------- | ------------------------------- |
| `earn_purchase` | LS Retail → Loyalty | Tích điểm từ mua hàng    |
| `earn_campaign` | Loyalty              | Tích điểm từ chiến dịch |
| `earn_referral` | Loyalty              | Tích điểm giới thiệu     |
| `spend_redeem`  | Loyalty              | Đổi điểm lấy quà        |
| `spend_gift`    | Loyalty              | Tặng điểm cho bạn bè     |
| `receive_gift`  | Loyalty              | Nhận điểm từ bạn bè     |
| `expire`        | Loyalty              | Điểm hết hạn              |

#### Phân chia trách nhiệm


| Trách nhiệm                   | Hệ thống    |
| --------------------------------- | --------------- |
| Lưu trữ lịch sử điểm      | **Loyalty**   |
| Cung cấp chi tiết đơn hàng | **LS Retail** |
| Tổng hợp từ nhiều nguồn    | **Loyalty**   |
| Hiển thị cho user             | **Mango CMS** |

---

## 4. PHẦN B: HỆ THỐNG THÔNG BÁO (MODULE 10)

### 4.1. Kiến trúc hệ thống thông báo

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           MANGO CMS                                         │
│                  (Quản lý toàn bộ Notification)                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐              │
│  │  Push Service   │  │  In-app Store   │  │  Device Token   │              │
│  │  (Firebase)     │  │  (MongoDB)      │  │  Management     │              │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘              │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    NOTIFICATION ENGINE                              │    │
│  │  • Nhận webhook từ Loyalty/LS Retail                                │    │
│  │  • Tạo notification từ template                                     │    │
│  │  • Gửi push + lưu in-app                                            │    │
│  │  • Chạy scheduled jobs                                              │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
                    ▲                          ▲
                    │ Webhook                  │ Webhook
          ┌─────────┴─────────┐       ┌────────┴────────┐
          │   Backend Loyalty │       │ Backend LS Retail│
          │                   │       │                  │
          │ • tier.upgraded   │       │ • order.created  │
          │ • points.earned   │       │ • order.shipping │
          │ • points.expiring │       │ • order.delivered│
          └───────────────────┘       └──────────────────┘
```

### 4.2. Các kênh thông báo


| Kênh                   | Nền tảng | Công nghệ              |
| ------------------------- | ------------ | -------------------------- |
| **Push Notification**   | iOS        | APNs via Firebase        |
|                         | Android    | Firebase Cloud Messaging |
|                         | Web        | Web Push API             |
| **In-app Notification** | Tất cả   | MongoDB storage          |
| **Email** (optional)    | Tất cả   | SMTP/Email service       |

### 4.3. Loại thông báo và nguồn kích hoạt


| Loại thông báo     | Nguồn kích hoạt  | Trigger event    |
| ----------------------- | --------------------- | ------------------ |
| **Đơn hàng**       | LS Retail webhook   | order.\* events  |
| **Nâng/giảm hạng** | Loyalty webhook     | tier.\* events   |
| **Nhận điểm**      | Loyalty webhook     | points.earned    |
| **Điểm hết hạn**  | Mango CMS scheduled | Daily job        |
| **Flash Sale**        | Mango CMS scheduled | 30 phút trước |
| **Sinh nhật**        | Mango CMS scheduled | Daily job        |
| **Khuyến mãi**      | Mango CMS manual    | Admin trigger    |

### 4.4. Phân loại thông báo (Categories)


| Danh mục              | Mô tả                  |
| ------------------------ | -------------------------- |
| **Đơn hàng**        | Trạng thái đơn hàng |
| **Hoa Đất**          | Giao dịch điểm        |
| **Hạng thành viên** | Thay đổi hạng         |
| **Quyền lợi**        | Benefits mới            |
| **Khuyến mãi**       | Chương trình sale     |
| **Hệ thống**         | Bảo trì, cập nhật    |

### 4.5. Tính năng gửi thông báo


| Tính năng     | Mô tả                             | Hệ thống xử lý |
| ----------------- | ------------------------------------- | -------------------- |
| **Mass Push**   | Gửi cho tất cả users             | Mango CMS          |
| **Group Push**  | Gửi theo segment (hạng, khu vực) | Mango CMS          |
| **Personalize** | Gửi cá nhân hóa                 | Mango CMS          |
| **Schedule**    | Đặt lịch gửi trước            | Mango CMS          |

### 4.6. Scheduled Jobs


| Job                    | Lịch chạy      | Mô tả                              |
| ------------------------ | ------------------ | -------------------------------------- |
| Nhắc điểm hết hạn | Daily 9:00       | Query Loyalty → Gửi notification   |
| Chúc mừng sinh nhật | Daily 8:00       | Query Loyalty → Gửi notification   |
| Nhắc Flash Sale       | 30 phút trước | Check schedule → Gửi notification  |
| Nhắc user inactive    | Weekly           | Query analytics → Gửi notification |

### 4.7. Quản lý Device Token


| Trách nhiệm        | Mô tả                              |
| ---------------------- | -------------------------------------- |
| **Đăng ký token** | Khi user cài app/đăng nhập       |
| **Cập nhật token** | Khi token thay đổi                 |
| **Hủy token**       | Khi user đăng xuất/gỡ app        |
| **Lưu trữ**        | userId, deviceId, platform, fcmToken |

---

## 5. CƠ CHẾ WEBHOOK GIỮA CÁC HỆ THỐNG

### 5.1. Webhook từ Backend Loyalty → Mango CMS


| Event             | Khi nào                | Notification được tạo                     |
| ------------------- | ------------------------- | ----------------------------------------------- |
| `tier.upgraded`   | Nâng hạng             | "Chúc mừng bạn lên hạng [Tên]"          |
| `tier.downgraded` | Giảm hạng             | "Hạng thành viên đã thay đổi"          |
| `points.earned`   | Nhận điểm            | "Bạn nhận được [n] Hoa Đất"            |
| `points.expiring` | Điểm sắp hết hạn   | "[n] Hoa Đất sẽ hết hạn trong [x] ngày" |
| `gift.received`   | Nhận điểm từ bạn   | "Bạn nhận được [n] Hoa Đất từ [Tên]" |
| `reward.redeemed` | Đổi quà thành công | "Đổi quà thành công: [Tên quà]"        |
| `campaign.won`    | Trúng thưởng         | "Chúc mừng bạn trúng [Tên quà]"         |

### 5.2. Webhook từ Backend LS Retail → Mango CMS


| Event             | Khi nào          | Notification được tạo                 |
| ------------------- | ------------------- | ------------------------------------------- |
| `order.created`   | Tạo đơn mới   | "Đơn hàng #123 đã được tạo"      |
| `order.confirmed` | Xác nhận đơn  | "Đơn hàng #123 đã xác nhận"        |
| `order.shipping`  | Bắt đầu giao   | "Đơn hàng #123 đang giao"             |
| `order.delivered` | Giao thành công | "Đơn hàng #123 đã giao thành công" |
| `order.cancelled` | Hủy đơn        | "Đơn hàng #123 đã hủy"              |

### 5.3. Luồng xử lý webhook

```
┌───────────────────┐                    ┌───────────────────┐
│  Backend Loyalty  │                    │     Mango CMS     │
│  hoặc LS Retail   │                    │                   │
└─────────┬─────────┘                    └─────────┬─────────┘
          │                                        │
          │  POST /webhook                         │
          │  { event: "tier.upgraded",             │
          │    userId: "...",                      │
          │    data: {...} }                       │
          │───────────────────────────────────────▶│
          │                                        │
          │                                        │ 1. Validate webhook
          │                                        │ 2. Tạo notification
          │                                        │ 3. Gửi Push (Firebase)
          │                                        │ 4. Lưu in-app (MongoDB)
          │                                        │ 5. Invalidate cache
          │                                        │
          │◀───────────────────────────────────────│
          │  200 OK                                │
```

---

## 6. TỔNG KẾT

### 6.1. Sơ đồ tổng quan

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              FRONTEND APP                                   │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              MANGO CMS                                      │
│                                                                             │
│  ┌────────────────┐  ┌────────────────┐  ┌────────────────────────────┐     │
│  │  Hạng & Điểm   │  │  Lịch sử       │  │  Notification System       │     │
│  │  (Cache)       │  │  (Display)     │  │  (Primary)                 │     │
│  └────────────────┘  └────────────────┘  └────────────────────────────┘     │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    WEBHOOK RECEIVER                                 │    │
│  │              (Nhận events từ Loyalty + LS Retail)                   │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
                │                                       │
                ▼                                       ▼
┌────────────────────────────────┐      ┌────────────────────────────────────┐
│       BACKEND LOYALTY          │      │        BACKEND LS RETAIL           │
│      (Primary cho Hạng/Điểm)   │      │     (Điểm từ đơn hàng)             │
│                                │      │                                    │
│  • Quản lý hạng thành viên     │      │  • Thông tin đơn hàng              │
│  • Tính toán nâng/giảm hạng    │      │  • Điểm từ purchase                │
│  • Lịch sử hạng & điểm         │      │  • Webhook order status            │
│  • Webhook tier/points events  │      │                                    │
└────────────────────────────────┘      └────────────────────────────────────┘
```

### 6.2. Tóm tắt phân chia


| Module        | Backend Loyalty | Backend LS Retail |      Mango CMS      |
| --------------- | :---------------: | :-----------------: | :--------------------: |
| **Module 3**  |   **Primary**   |         -         |   Cache + Display   |
| **Module 4**  |   **Primary**   |     Hỗ trợ     | Tổng hợp + Display |
| **Module 10** |     Webhook     |      Webhook      |     **Primary**     |

### 6.3. Cache Strategy


| Dữ liệu                   | TTL      | Invalidation        |
| ----------------------------- | ---------- | --------------------- |
| Thông tin hạng hiện tại | 5 phút  | Khi có tier change |
| Danh sách tất cả hạng   | 1 giờ   | Manual              |
| Lịch sử hạng             | 10 phút | Khi có tier change |
| Lịch sử điểm            | 2 phút  | Khi có transaction |
| Notifications               | No cache | Real-time           |

---

_Tài liệu này mô tả phân chia trách nhiệm Module 3, 4, 10 - Hạng Thành Viên & Thông báo trong hệ thống TH Milk App._
