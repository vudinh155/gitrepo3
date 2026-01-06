# MODULE CAMPAIGN LOYALTY & TẶNG HOA ĐẤT - PHÂN CHIA TRÁCH NHIỆM HỆ THỐNG

> Tài liệu kỹ thuật mô tả cách phân chia trách nhiệm giữa các hệ thống Backend cho **Module 6: Campaign Loyalty** và **Module 7: Tặng Hoa Đất**.

---

## MỤC LỤC

1. [Tổng quan Module](#1-tổng-quan-module)
2. [Phân chia trách nhiệm tổng thể](#2-phân-chia-trách-nhiệm-tổng-thể)
3. [Phần A: Campaign Loyalty - Chi tiết trách nhiệm](#3-phần-a-campaign-loyalty---chi-tiết-trách-nhiệm)
4. [Phần B: Tặng Hoa Đất - Chi tiết trách nhiệm](#4-phần-b-tặng-hoa-đất---chi-tiết-trách-nhiệm)
5. [Luồng xử lý và phối hợp hệ thống](#5-luồng-xử-lý-và-phối-hợp-hệ-thống)
6. [Tổng kết](#6-tổng-kết)

---

## 1. TỔNG QUAN MODULE

### 1.1. Phạm vi Module 6, 7

| Module | Tên | Mô tả |
|--------|-----|-------|
| **Module 6** | Campaign Loyalty | Các chương trình khuyến mãi thành viên (vòng quay, phát thưởng, giới thiệu, tích lũy) |
| **Module 7** | Tặng Hoa Đất | Chuyển điểm thưởng giữa các thành viên |

### 1.2. Các loại Campaign trong hệ thống

| Loại Campaign | Tên tiếng Việt | Mô tả ngắn |
|---------------|----------------|------------|
| **spin** | Vòng quay may mắn | Quay để nhận thưởng ngẫu nhiên |
| **select** | Phát thưởng chọn quà | Chọn 1 trong các phần quà có sẵn |
| **referral** | Giới thiệu bạn bè | Nhận thưởng khi giới thiệu người mới |
| **accumulate** | Tích lũy | Nhận thưởng khi mua đủ số lượng sản phẩm |
| **milestone** | Cột mốc mua hàng | Nhận thưởng khi đạt cột mốc chi tiêu |

---

## 2. PHÂN CHIA TRÁCH NHIỆM TỔNG THỂ

### 2.1. Ma trận phân chia

| Chức năng | Backend Loyalty | Backend LS Retail | Mango CMS |
|-----------|:---------------:|:-----------------:|:---------:|
| **Module 6: Campaign Loyalty** |
| Danh sách campaign | **Primary** | - | Cache + Display |
| Chi tiết campaign | **Primary** | - | Display |
| Kiểm tra điều kiện tham gia | **Primary** | - | - |
| Tham gia campaign | **Primary** | - | Orchestrate |
| Vòng quay - Logic random | **Primary** | - | - |
| Vòng quay - Animation UI | - | - | **Primary** |
| Phát thưởng - Chọn quà | **Primary** | - | Display |
| Giới thiệu - Mã referral | **Primary** | - | Share UI |
| Giới thiệu - Tracking | **Primary** | - | - |
| Tích lũy - Track purchase | - | **Primary** | - |
| Tích lũy - Tính thưởng | **Primary** | - | Display |
| Phát quà/điểm | **Primary** | - | - |
| **Module 7: Tặng Hoa Đất** |
| Tìm người nhận | **Primary** | - | Validate format |
| Xử lý chuyển điểm | **Primary** | - | Orchestrate |
| Trừ điểm (FIFO) | **Primary** | - | - |
| Cộng điểm người nhận | **Primary** | - | - |
| Ghi log giao dịch | **Primary** | - | - |
| **Chung** |
| Gửi thông báo | - | - | **Primary** |
| Khảo sát (nếu có) | Data storage | - | Form UI |

### 2.2. Chi tiết trách nhiệm từng hệ thống

#### Backend Loyalty - Xử lý nghiệp vụ chính

| Trách nhiệm | Mô tả |
|-------------|-------|
| **Quản lý campaign** | Cấu hình, thời hạn, điều kiện, phần thưởng |
| **Kiểm tra điều kiện** | Xác nhận user đủ hạng, đủ điểm, còn lượt tham gia |
| **Xử lý vòng quay** | Random kết quả theo tỷ lệ cấu hình, quản lý stock giải |
| **Phát thưởng** | Tạo voucher, cộng điểm cho người thắng |
| **Quản lý referral** | Sinh mã giới thiệu, tracking đăng ký, tự động thưởng |
| **Tính tích lũy** | Nhận data từ LS Retail, tính tiến trình, phát thưởng khi đủ |
| **Xử lý tặng điểm** | Validate, trừ điểm FIFO, cộng điểm, ghi log |

#### Backend LS Retail - Cung cấp dữ liệu mua hàng

| Trách nhiệm | Mô tả |
|-------------|-------|
| **Ghi nhận đơn hàng** | Tracking purchase cho campaign tích lũy |
| **Webhook đến Loyalty** | Gửi thông tin đơn hàng để tính điểm bonus |
| **Thông tin cửa hàng** | Cung cấp danh sách cửa hàng để nhận quà |

#### Mango CMS - Điều phối và giao diện

| Trách nhiệm | Mô tả |
|-------------|-------|
| **Điều phối request** | Nhận từ Frontend, forward đến Backend Loyalty |
| **Cache danh sách** | Lưu tạm danh sách campaign để tăng tốc |
| **Animation vòng quay** | Render UI quay thưởng, hiệu ứng |
| **Share referral** | Chia sẻ mã giới thiệu qua các kênh (Zalo, FB, SMS) |
| **Hiển thị form khảo sát** | Render UI câu hỏi, validate trước khi gửi |
| **Gửi thông báo** | Push notification cho các sự kiện (trúng thưởng, nhận điểm) |

---

## 3. PHẦN A: CAMPAIGN LOYALTY - CHI TIẾT TRÁCH NHIỆM

### 3.1. Danh sách Campaign

**Luồng dữ liệu:** Frontend → Mango CMS → Backend Loyalty

| Bước | Hệ thống thực hiện | Trách nhiệm |
|------|-------------------|-------------|
| Nhận request | Mango CMS | Kiểm tra cache |
| Lấy danh sách campaign | Backend Loyalty | Query campaigns đang active |
| Lọc theo điều kiện user | Backend Loyalty | Filter theo hạng thành viên, eligibility |
| Phân loại | Backend Loyalty | Featured campaigns vs All campaigns |
| Cache kết quả | Mango CMS | Lưu tạm 5 phút |
| Trả về Frontend | Mango CMS | Format response |

**Dữ liệu do Backend Loyalty quản lý:**
- Danh sách tất cả campaign (tên, mô tả, banner, thời hạn)
- Loại campaign (spin, select, referral, accumulate)
- Điều kiện tham gia (hạng thành viên, số lượt, thời gian)
- Trạng thái campaign (active, upcoming, ended)

---

### 3.2. Chi tiết Campaign

**Luồng dữ liệu:** Frontend → Mango CMS → Backend Loyalty

| Bước | Hệ thống thực hiện | Trách nhiệm |
|------|-------------------|-------------|
| Nhận request | Mango CMS | Validate campaign_id |
| Lấy thông tin campaign | Backend Loyalty | Query full detail |
| Kiểm tra điều kiện user | Backend Loyalty | User có đủ điều kiện tham gia? |
| Lấy trạng thái user | Backend Loyalty | Đã tham gia chưa? Còn lượt? Đã thắng gì? |
| Trả về Frontend | Mango CMS | Detail + user status |

**Dữ liệu do Backend Loyalty quản lý:**
- Thông tin chi tiết campaign (giới thiệu, mô tả, điều kiện)
- Danh sách giải thưởng (tên, hình, tỷ lệ, stock)
- Trạng thái tham gia của user
- Lịch sử thắng giải của user

---

### 3.3. Vòng Quay May Mắn (Spin)

**Luồng dữ liệu:** Frontend → Mango CMS → Backend Loyalty

| Bước | Hệ thống thực hiện | Trách nhiệm |
|------|-------------------|-------------|
| Nhận request quay | Mango CMS | Validate session |
| Kiểm tra lượt quay | Backend Loyalty | User còn lượt không? |
| Random kết quả | Backend Loyalty | Áp dụng tỷ lệ xác suất đã cấu hình |
| Kiểm tra stock giải | Backend Loyalty | Giải còn hàng không? |
| Trừ lượt quay | Backend Loyalty | remaining_turns -= 1 |
| Phát thưởng | Backend Loyalty | Tạo voucher hoặc cộng điểm |
| Giảm stock giải | Backend Loyalty | prize_stock -= 1 |
| Trả kết quả + animation | Mango CMS | Tính toán góc dừng, thời gian quay |
| Hiển thị animation | Mango CMS | Render vòng quay UI |
| Gửi thông báo | Mango CMS | Push "Chúc mừng trúng thưởng" |

**Backend Loyalty xử lý logic random:**

| Trách nhiệm | Mô tả |
|-------------|-------|
| Cấu hình tỷ lệ | Mỗi giải có probability (%) |
| Random có trọng số | Thuật toán weighted random |
| Xử lý hết stock | Nếu giải hết, random lại sang giải khác |
| Ghi log | Lưu lịch sử quay + kết quả |

**Mango CMS xử lý animation:**

| Trách nhiệm | Mô tả |
|-------------|-------|
| Nhận prize_index | Vị trí giải trên vòng quay |
| Tính stop_angle | Góc dừng của kim quay |
| Tính duration | Thời gian quay (thường 5-8 giây) |
| Render animation | Hiệu ứng quay vòng |

---

### 3.4. Phát Thưởng Chọn Quà (Select)

**Luồng dữ liệu:** Frontend → Mango CMS → Backend Loyalty

| Bước | Hệ thống thực hiện | Trách nhiệm |
|------|-------------------|-------------|
| Hiển thị danh sách quà | Backend Loyalty | Trả về prizes available |
| User chọn quà | Mango CMS | Capture selection |
| Kiểm tra còn hàng | Backend Loyalty | prize_stock > 0? |
| Kiểm tra đã chọn chưa | Backend Loyalty | User đã chọn trong campaign này chưa? |
| Phát thưởng | Backend Loyalty | Tạo voucher cho user |
| Giảm stock | Backend Loyalty | prize_stock -= 1 |
| Gửi thông báo | Mango CMS | Push "Bạn đã nhận quà" |

---

### 3.5. Campaign Giới Thiệu Bạn Bè (Referral)

**Luồng dữ liệu:** Frontend ↔ Mango CMS ↔ Backend Loyalty

| Bước | Hệ thống thực hiện | Trách nhiệm |
|------|-------------------|-------------|
| Sinh mã referral | Backend Loyalty | Tạo mã unique cho user (VD: TH_NGUYEN_A) |
| Tạo link + QR | Backend Loyalty | Generate share link và QR code |
| Chia sẻ mã | Mango CMS | UI share qua Zalo, FB, SMS, Copy link |
| Người mới đăng ký | Backend Loyalty | Detect referral code khi register |
| Validate referral | Backend Loyalty | Mã hợp lệ? Người giới thiệu active? |
| Tính thưởng | Backend Loyalty | Cộng điểm cho người giới thiệu |
| Tính bonus milestone | Backend Loyalty | Đạt 5/10 lượt → bonus thêm |
| Gửi thông báo | Mango CMS | Push cho cả 2 bên |

**Dữ liệu do Backend Loyalty quản lý:**
- Mã referral của từng user
- Lịch sử giới thiệu (ai giới thiệu ai, khi nào)
- Trạng thái referral (pending, successful)
- Thống kê (total, successful, total_earned)
- Cấu hình thưởng (điểm/referral, bonus milestone)

---

### 3.6. Campaign Tích Lũy (Accumulate)

**Luồng dữ liệu:** LS Retail → Backend Loyalty ↔ Mango CMS → Frontend

| Bước | Hệ thống thực hiện | Trách nhiệm |
|------|-------------------|-------------|
| Ghi nhận đơn hàng | LS Retail | Order completed event |
| Webhook đến Loyalty | LS Retail | Gửi order data |
| Xác định campaign | Backend Loyalty | Order match campaign nào? |
| Tính số lượng sản phẩm | Backend Loyalty | Đếm sản phẩm eligible trong order |
| Cập nhật tiến trình | Backend Loyalty | user_progress += quantity |
| Kiểm tra hoàn thành | Backend Loyalty | progress >= target? |
| Phát thưởng | Backend Loyalty | Tạo voucher/quà nếu đủ |
| Reset cycle | Backend Loyalty | progress = progress - target |
| Gửi thông báo | Mango CMS | Push "Tích lũy thành công" |

**Phối hợp giữa LS Retail và Backend Loyalty:**

| LS Retail gửi | Backend Loyalty xử lý |
|---------------|----------------------|
| Order ID | Đối chiếu tránh duplicate |
| Customer ID | Map với member |
| Product list | Filter sản phẩm trong campaign |
| Quantity | Cộng vào progress |
| Order date | Validate trong thời hạn campaign |

---

## 4. PHẦN B: TẶNG HOA ĐẤT - CHI TIẾT TRÁCH NHIỆM

### 4.1. Tìm người nhận

**Luồng dữ liệu:** Frontend → Mango CMS → Backend Loyalty

| Bước | Hệ thống thực hiện | Trách nhiệm |
|------|-------------------|-------------|
| Nhập số điện thoại | Mango CMS | Validate format (10 số, bắt đầu 0) |
| Tìm kiếm member | Backend Loyalty | Query theo phone number |
| Validate | Backend Loyalty | Không phải chính mình? Account active? |
| Trả về thông tin | Backend Loyalty | Tên (masked), avatar |

**Backend Loyalty validate:**

| Kiểm tra | Mô tả |
|----------|-------|
| Tồn tại | SĐT có trong hệ thống không |
| Không tự tặng | sender_id != recipient_id |
| Account active | Recipient không bị khóa |

---

### 4.2. Xử lý chuyển điểm

**Luồng dữ liệu:** Frontend → Mango CMS → Backend Loyalty

| Bước | Hệ thống thực hiện | Trách nhiệm |
|------|-------------------|-------------|
| Nhận request tặng | Mango CMS | Validate input (amount, recipient, message) |
| Kiểm tra số dư | Backend Loyalty | sender_balance >= amount? |
| Kiểm tra giới hạn | Backend Loyalty | amount >= min_transfer? |
| Trừ điểm sender | Backend Loyalty | Deduct theo FIFO |
| Cộng điểm recipient | Backend Loyalty | Credit với expiry dates tương ứng |
| Ghi log giao dịch | Backend Loyalty | Transaction history cả 2 bên |
| Invalidate cache | Mango CMS | Xóa cache điểm |
| Gửi thông báo | Mango CMS | Push cho cả sender và recipient |

---

### 4.3. Logic trừ điểm FIFO khi tặng

**Backend Loyalty xử lý:**

| Bước | Mô tả |
|------|-------|
| 1. Lấy các lô điểm sender | Sắp xếp theo expiry_date tăng dần |
| 2. Lặp qua từng lô | Trừ từ lô sắp hết hạn trước |
| 3. Tạo lô điểm mới cho recipient | Giữ nguyên expiry_date từ lô gốc |
| 4. Ghi log chi tiết | Batch nào trừ bao nhiêu |

**Ví dụ:**
- Sender có: Lô A (300 điểm, hết hạn T6), Lô B (500 điểm, hết hạn T9)
- Tặng 400 điểm
- Kết quả:
  - Sender: Lô A = 0, Lô B = 400
  - Recipient nhận: 300 điểm (hết hạn T6) + 100 điểm (hết hạn T9)

---

## 5. LUỒNG XỬ LÝ VÀ PHỐI HỢP HỆ THỐNG

### 5.1. Luồng: Quay vòng quay may mắn

```
Frontend                    Mango CMS                   Backend Loyalty
   │                            │                            │
   │── POST /spin ─────────────>│                            │
   │                            │── Check session ──┐        │
   │                            │<──────────────────┘        │
   │                            │                            │
   │                            │── POST spin request ──────>│
   │                            │                            │── Check remaining turns
   │                            │                            │── Random with probability
   │                            │                            │── Check prize stock
   │                            │                            │── Deduct turn
   │                            │                            │── Award prize
   │                            │                            │── Decrease stock
   │                            │<── Result + prize_index ───│
   │                            │                            │
   │                            │── Calculate animation ──┐  │
   │                            │<────────────────────────┘  │
   │<── Animation data ──────── │                            │
   │                            │                            │
   │   (User sees spinning)     │                            │
   │                            │                            │
   │                            │── Send push notification ─┐│
   │                            │<──────────────────────────┘│
```

### 5.2. Luồng: Campaign tích lũy khi mua hàng

```
LS Retail                   Backend Loyalty              Mango CMS
   │                            │                            │
   │── Order completed ────────>│                            │
   │   (webhook)                │                            │
   │                            │── Identify campaign ──┐    │
   │                            │<──────────────────────┘    │
   │                            │                            │
   │                            │── Count eligible items ─┐  │
   │                            │<────────────────────────┘  │
   │                            │                            │
   │                            │── Update progress ──────┐  │
   │                            │<────────────────────────┘  │
   │                            │                            │
   │                            │── Check completion ─────┐  │
   │                            │<────────────────────────┘  │
   │                            │                            │
   │                            │ (if completed)             │
   │                            │── Award reward ─────────┐  │
   │                            │<────────────────────────┘  │
   │                            │                            │
   │                            │── Trigger notification ───>│
   │                            │                            │── Send push
```

### 5.3. Luồng: Tặng Hoa Đất cho bạn bè

```
Frontend                    Mango CMS                   Backend Loyalty
   │                            │                            │
   │── Find recipient ─────────>│── Validate phone ──┐       │
   │                            │<────────────────────┘      │
   │                            │── Find member ────────────>│
   │                            │                            │── Query by phone
   │                            │                            │── Check not self
   │                            │<── Recipient info ─────────│
   │<── Show recipient ──────── │                            │
   │                            │                            │
   │── POST gift points ───────>│── Validate input ──┐       │
   │                            │<────────────────────┘      │
   │                            │── POST transfer ──────────>│
   │                            │                            │── Check balance
   │                            │                            │── Deduct FIFO
   │                            │                            │── Credit recipient
   │                            │                            │── Log transaction
   │                            │<── Success ────────────────│
   │                            │                            │
   │                            │── Send push (sender) ────┐ │
   │                            │<─────────────────────────┘ │
   │                            │── Send push (recipient) ─┐ │
   │                            │<─────────────────────────┘ │
   │<── Success response ────── │                            │
```

---

## 6. TỔNG KẾT

### 6.1. Tóm tắt trách nhiệm theo Module

| Module | Backend Loyalty | Backend LS Retail | Mango CMS |
|--------|-----------------|-------------------|-----------|
| **6.1 Danh sách campaign** | Source of Truth | - | Cache |
| **6.2 Chi tiết campaign** | Data + Eligibility | - | Display |
| **6.3 Vòng quay** | Logic + Award | - | Animation + Notify |
| **6.4 Phát thưởng** | Stock + Award | - | Display |
| **6.5 Giới thiệu** | Referral code + Track | - | Share UI + Notify |
| **6.6 Tích lũy** | Calculate + Award | Purchase data | Display + Notify |
| **7.1 Tặng Hoa Đất** | Transfer logic | - | Orchestrate + Notify |

### 6.2. Nguyên tắc quan trọng

| Nguyên tắc | Mô tả |
|------------|-------|
| **Backend Loyalty owns campaign data** | Tất cả cấu hình, logic, award do Loyalty quản lý |
| **LS Retail chỉ cung cấp purchase data** | Không xử lý logic campaign |
| **Mango CMS không persist campaign data** | Chỉ cache và display |
| **FIFO cho mọi trừ điểm** | Kể cả khi tặng, điểm sắp hết hạn được trừ trước |
| **Notification qua Mango CMS** | Backend Loyalty trigger, Mango CMS gửi |

### 6.3. Thông báo (Mango CMS gửi)

| Sự kiện | Trigger từ | Recipient |
|---------|------------|-----------|
| Trúng thưởng quay | Backend Loyalty | User |
| Nhận quà phát thưởng | Backend Loyalty | User |
| Giới thiệu thành công | Backend Loyalty | Người giới thiệu |
| Tích lũy hoàn thành | Backend Loyalty | User |
| Tặng Hoa Đất thành công | Backend Loyalty | Sender |
| Nhận Hoa Đất | Backend Loyalty | Recipient |

---

> **Ghi chú:** Tài liệu này mô tả phân chia trách nhiệm kỹ thuật giữa các hệ thống. Chi tiết API và cấu trúc dữ liệu xem tại tài liệu orchestrator gốc.
