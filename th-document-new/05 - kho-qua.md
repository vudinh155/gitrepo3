# MODULE KHO QUÀ - PHÂN CHIA TRÁCH NHIỆM HỆ THỐNG

> Tài liệu kỹ thuật mô tả cách phân chia trách nhiệm giữa các hệ thống Backend cho **Module 5: Kho Quà**.

---

## MỤC LỤC

1. [Tổng quan Module](#1-tổng-quan-module)
2. [Phân chia trách nhiệm tổng thể](#2-phân-chia-trách-nhiệm-tổng-thể)
3. [Chi tiết trách nhiệm theo chức năng](#3-chi-tiết-trách-nhiệm-theo-chức-năng)
4. [Luồng xử lý và phối hợp hệ thống](#4-luồng-xử-lý-và-phối-hợp-hệ-thống)
5. [Tổng kết](#5-tổng-kết)

---

## 1. TỔNG QUAN MODULE

### 1.1. Phạm vi Module Kho Quà

Module Kho Quà bao gồm các chức năng:

- **Quà Của Tôi:** Quản lý các quà tặng khách hàng đang sở hữu
- **Kho Quà TH Reward:** Danh sách quà có thể đổi bằng Hoa Đất
- **Đổi quà:** Xử lý nghiệp vụ đổi điểm lấy quà
- **Khảo sát:** Thu thập ý kiến khách hàng khi đổi quà

### 1.2. Các nguồn gốc quà tặng (Data Sources)


| Nguồn                             | Hệ thống cung cấp dữ liệu |
| ------------------------------------ | -------------------------------- |
| Đổi từ Hoa Đất                | Backend Loyalty                |
| Chương trình/Campaign           | Backend Loyalty                |
| Quyền lợi hạng thành viên     | Backend Loyalty                |
| Trúng thưởng (Vòng quay, Game) | Backend Loyalty                |

---

## 2. PHÂN CHIA TRÁCH NHIỆM TỔNG THỂ

### 2.1. Ma trận phân chia


| Chức năng                  | Backend Loyalty | Backend LS Retail |    Mango CMS    |
| ------------------------------ | :---------------: | :-----------------: | :---------------: |
| Quà của tôi               |   **Primary**   |         -         | Cache + Display |
| Kho quà đổi điểm        |   **Primary**   |         -         | Cache + Display |
| Chương trình đổi quà   |   **Primary**   |         -         | Cache + Display |
| Xử lý đổi quà           |   **Primary**   |         -         |   Orchestrate   |
| Trừ điểm (FIFO)           |   **Primary**   |         -         |        -        |
| Tạo voucher/coupon          |   **Primary**   |         -         |        -        |
| Khảo sát - Lưu dữ liệu  |   **Primary**   |         -         |        -        |
| Khảo sát - Hiển thị form |        -        |         -         |   **Primary**   |
| Gửi thông báo đổi quà  |        -        |         -         |   **Primary**   |

### 2.2. Chi tiết trách nhiệm từng hệ thống

#### Backend Loyalty - Nguồn dữ liệu chính


| Trách nhiệm                            | Mô tả                                                               |
| ------------------------------------------ | ----------------------------------------------------------------------- |
| **Lưu trữ danh sách quà**            | Quản lý toàn bộ voucher/coupon của từng khách hàng            |
| **Quản lý chương trình đổi quà** | Cấu hình các chương trình, điều kiện tham gia, thời hạn    |
| **Quản lý kho quà đổi điểm**      | Danh sách quà có thể đổi, số điểm cần, số lượng tồn kho |
| **Xử lý logic đổi quà**             | Kiểm tra điều kiện (hạng, điểm, tồn kho, giới hạn)          |
| **Trừ điểm theo FIFO**                | Ưu tiên trừ điểm sắp hết hạn trước                          |
| **Tạo mã quà**                        | Sinh mã voucher/coupon, mã QR, barcode                              |
| **Lưu kết quả khảo sát**            | Lưu trữ câu trả lời khảo sát từ khách hàng                  |
| **Quản lý trạng thái quà**          | Chưa sử dụng / Đã sử dụng / Hết hạn                          |

## Mango CMS - Điều phối và hiển thị


| Trách nhiệm                     | Mô tả                                                            |
| ----------------------------------- | -------------------------------------------------------------------- |
| **Điều phối yêu cầu**        | Nhận request từ Frontend, chuyển đến Backend Loyalty          |
| **Cache dữ liệu**               | Lưu tạm danh sách quà, chương trình để tăng tốc         |
| **Hiển thị form khảo sát**    | Render UI câu hỏi, validate input                                |
| **Gửi thông báo**              | Push notification khi đổi quà thành công, quà sắp hết hạn |
| **Xử lý địa chỉ giao hàng** | Lấy địa chỉ từ LS Retail nếu chọn giao tận nơi            |

#### Backend LS Retail - Không tham gia trực tiếp

Backend LS Retail **không có vai trò** trong Module Kho Quà, ngoại trừ:

- Cung cấp danh sách địa chỉ giao hàng (nếu quà giao tận nơi)
- Cung cấp thông tin cửa hàng (nếu quà nhận tại cửa hàng)

---

## 3. CHI TIẾT TRÁCH NHIỆM THEO CHỨC NĂNG

### 3.1. Quà Của Tôi

**Luồng dữ liệu:** Frontend → Mango CMS → Backend Loyalty


| Bước                   | Hệ thống thực hiện | Trách nhiệm                     |
| -------------------------- | ------------------------ | ----------------------------------- |
| Nhận request            | Mango CMS              | Validate session, kiểm tra cache |
| Lấy danh sách quà     | Backend Loyalty        | Query database theo customer_id   |
| Phân loại trạng thái | Backend Loyalty        | Group by: unused / used / expired |
| Cache kết quả          | Mango CMS              | Lưu tạm 2 phút                 |
| Trả về Frontend        | Mango CMS              | Format response                   |

**Dữ liệu do Backend Loyalty quản lý:**

- Danh sách voucher/coupon của khách
- Trạng thái sử dụng (chưa dùng, đã dùng, hết hạn)
- Mã sử dụng (code, QR, barcode)
- Nguồn gốc quà (campaign, đổi điểm, quyền lợi hạng)
- Điều kiện sử dụng
- Hạn sử dụng

---

### 3.2. Chi Tiết Quà

**Luồng dữ liệu:** Frontend → Mango CMS → Backend Loyalty


| Bước              | Hệ thống thực hiện | Trách nhiệm                      |
| --------------------- | ------------------------ | ------------------------------------ |
| Nhận request       | Mango CMS              | Validate gift_id                   |
| Lấy chi tiết quà | Backend Loyalty        | Query full detail từ database     |
| Sinh mã sử dụng  | Backend Loyalty        | Generate QR code, barcode data     |
| Trả về Frontend   | Mango CMS              | Format response với mã sử dụng |

**Dữ liệu do Backend Loyalty quản lý:**

- Thông tin chi tiết voucher
- Mã code dạng text
- Dữ liệu để sinh QR/Barcode
- Điều kiện áp dụng (đơn tối thiểu, sản phẩm áp dụng)
- Hướng dẫn sử dụng
- Lịch sử sử dụng (nếu đã dùng)

---

### 3.3. Kho Quà TH Reward (Đổi điểm)

**Luồng dữ liệu:** Frontend → Mango CMS → Backend Loyalty


| Bước                          | Hệ thống thực hiện | Trách nhiệm            |
| --------------------------------- | ------------------------ | -------------------------- |
| Nhận request                   | Mango CMS              | Kiểm tra cache          |
| Lấy danh sách chương trình | Backend Loyalty        | Query active programs    |
| Lấy điểm khách hàng        | Backend Loyalty        | Số Hoa Đất khả dụng |
| Cache kết quả                 | Mango CMS              | Lưu tạm 10 phút       |
| Trả về Frontend               | Mango CMS              | Format response          |

**Dữ liệu do Backend Loyalty quản lý:**

- Danh sách chương trình đổi quà (tên, mô tả, thời hạn)
- Danh sách quà trong từng chương trình
- Số điểm cần để đổi mỗi quà
- Số lượng tồn kho
- Điều kiện đổi (hạng thành viên, giới hạn mỗi người)
- Số điểm hiện có của khách hàng

---

### 3.4. Chi Tiết Quà Đổi Thưởng

**Luồng dữ liệu:** Frontend → Mango CMS → Backend Loyalty


| Bước                      | Hệ thống thực hiện | Trách nhiệm                         |
| ----------------------------- | ------------------------ | --------------------------------------- |
| Nhận request               | Mango CMS              | Validate gift_id                      |
| Lấy chi tiết quà         | Backend Loyalty        | Thông tin sản phẩm, điều kiện   |
| Kiểm tra tồn kho          | Backend Loyalty        | Số lượng còn lại                 |
| Kiểm tra đủ điều kiện | Backend Loyalty        | Hạng, điểm, giới hạn của khách |
| Trả về Frontend           | Mango CMS              | Format response + eligibility status  |

**Backend Loyalty kiểm tra các điều kiện:**


| Điều kiện          | Trách nhiệm                                              |
| ----------------------- | ------------------------------------------------------------ |
| Đủ điểm           | Backend Loyalty so sánh điểm khách vs điểm cần      |
| Đủ hạng            | Backend Loyalty kiểm tra tier của khách                 |
| Còn hàng            | Backend Loyalty kiểm tra stock > 0                        |
| Chưa quá giới hạn | Backend Loyalty đếm số lần khách đã đổi quà này |

---

### 3.5. Xử Lý Đổi Quà

**Luồng dữ liệu:** Frontend → Mango CMS → Backend Loyalty


| Bước                   | Hệ thống thực hiện | Trách nhiệm                                       |
| -------------------------- | ------------------------ | ----------------------------------------------------- |
| Nhận request đổi quà | Mango CMS              | Validate input (gift_id, quantity, delivery option) |
| Kiểm tra điều kiện   | Backend Loyalty        | Hạng, điểm, tồn kho, giới hạn                 |
| Trừ điểm FIFO         | Backend Loyalty        | Trừ từ lô điểm sắp hết hạn trước          |
| Giảm tồn kho           | Backend Loyalty        | stock = stock - quantity                            |
| Tạo voucher             | Backend Loyalty        | Sinh mã nhận quà cho khách                      |
| Lưu vào Quà Của Tôi | Backend Loyalty        | Insert vào danh sách quà của khách             |
| Gửi thông báo         | Mango CMS              | Push notification "Đổi quà thành công"         |
| Invalidate cache         | Mango CMS              | Xóa cache danh sách quà, điểm                  |

**Chi tiết logic trừ điểm FIFO (Backend Loyalty):**


| Bước | Mô tả                                                                         |
| -------- | --------------------------------------------------------------------------------- |
| 1      | Lấy tất cả lô điểm của khách, sắp xếp theo ngày hết hạn tăng dần |
| 2      | Lặp qua từng lô, trừ điểm từ lô sắp hết hạn trước                  |
| 3      | Tiếp tục cho đến khi trừ đủ số điểm cần                              |
| 4      | Ghi log giao dịch trừ điểm                                                  |

---

### 3.6. Khảo Sát

**Luồng dữ liệu:** Frontend → Mango CMS ↔ Backend Loyalty


| Bước                         | Hệ thống thực hiện | Trách nhiệm                                 |
| -------------------------------- | ------------------------ | ----------------------------------------------- |
| Kiểm tra quà cần khảo sát | Mango CMS              | Đọc flag surveyRequired từ Backend Loyalty |
| Lấy form khảo sát           | Backend Loyalty        | Trả về danh sách câu hỏi, options        |
| Render form UI                 | Mango CMS              | Hiển thị form cho khách                    |
| Validate câu trả lời        | Mango CMS              | Kiểm tra required fields                     |
| Gửi kết quả                 | Mango CMS              | Forward đến Backend Loyalty                 |
| Lưu kết quả                 | Backend Loyalty        | Insert vào database khảo sát               |
| Cho phép đổi quà           | Backend Loyalty        | Mark khách đã hoàn thành khảo sát      |

**Phân chia rõ:**

- **Backend Loyalty:** Sở hữu dữ liệu câu hỏi và câu trả lời
- **Mango CMS:** Chỉ render UI và validate form, không lưu dữ liệu khảo sát

---

## 4. LUỒNG XỬ LÝ VÀ PHỐI HỢP HỆ THỐNG

### 4.1. Luồng: Xem danh sách quà của tôi

```
Frontend                    Mango CMS                   Backend Loyalty
   │                            │                            │
   │──── GET /gifts ───────────>│                            │
   │                            │── Check cache ──┐          │
   │                            │<────────────────┘          │
   │                            │                            │
   │                            │ (cache miss)               │
   │                            │── GET customer gifts ─────>│
   │                            │                            │── Query DB
   │                            │                            │── Group by status
   │                            │<──── Gift list ────────────│
   │                            │                            │
   │                            │── Save to cache (2min) ───┐│
   │                            │<──────────────────────────┘│
   │<─── Response ───────────── │                            │
```

### 4.2. Luồng: Đổi điểm lấy quà

```
Frontend                    Mango CMS                   Backend Loyalty
   │                            │                            │
   │── POST /redeem ───────────>│                            │
   │   (gift_id, quantity)      │                            │
   │                            │── Validate session ──┐     │
   │                            │<─────────────────────┘     │
   │                            │                            │
   │                            │── POST redeem request ────>│
   │                            │                            │── Check tier ✓
   │                            │                            │── Check points ✓
   │                            │                            │── Check stock ✓
   │                            │                            │── Deduct points (FIFO)
   │                            │                            │── Decrease stock
   │                            │                            │── Create voucher
   │                            │<──── Success + voucher ────│
   │                            │                            │
   │                            │── Send push notification ─┐│
   │                            │<──────────────────────────┘│
   │                            │── Invalidate cache ──────┐ │
   │                            │<─────────────────────────┘ │
   │<─── Success response ─────│                             │
```

### 4.3. Luồng: Đổi quà có khảo sát

```
Frontend                    Mango CMS                   Backend Loyalty
   │                            │                            │
   │── GET gift detail ────────>│── GET gift ───────────────>│
   │                            │<── surveyRequired: true ───│
   │<── Need survey ─────────── │                            │
   │                            │                            │
   │── GET survey form ────────>│── GET survey questions ───>│
   │                            │<── Questions list ─────────│
   │<── Survey form ─────────── │                            │
   │                            │                            │
   │── Submit answers ─────────>│── Validate form ──┐        │
   │                            │<──────────────────┘        │
   │                            │── POST survey response ───>│
   │                            │                            │── Save responses
   │                            │<── Survey completed ───────│
   │<── Can proceed ─────────── │                            │
   │                            │                            │
   │── POST redeem ────────────>│      (tiếp tục đổi quà)    │
```

---

## 5. TỔNG KẾT

### 5.1. Tóm tắt trách nhiệm


| Hệ thống            | Vai trò chính      | Dữ liệu sở hữu                                               |
| ----------------------- | ---------------------- | ------------------------------------------------------------------ |
| **Backend Loyalty**   | Source of Truth      | Quà của khách, chương trình đổi quà, điểm, khảo sát |
| **Mango CMS**         | Orchestrator + Cache | Không sở hữu dữ liệu nghiệp vụ                            |
| **Backend LS Retail** | Không tham gia      | -                                                                |

### 5.2. Nguyên tắc quan trọng


| Nguyên tắc                                    | Mô tả                                                                |
| ------------------------------------------------- | ------------------------------------------------------------------------ |
| **Single Source of Truth**                      | Backend Loyalty là nguồn duy nhất cho dữ liệu quà tặng          |
| **Mango CMS không lưu dữ liệu nghiệp vụ** | Chỉ cache tạm thời, không persist                                  |
| **Mọi thay đổi qua Backend Loyalty**         | Đổi quà, trừ điểm, tạo voucher đều do Backend Loyalty xử lý |
| **FIFO cho trừ điểm**                        | Backend Loyalty tự động trừ điểm sắp hết hạn trước          |

### 5.3. Thông báo (Mango CMS gửi)


| Sự kiện               | Trigger từ             | Nội dung                                  |
| ------------------------- | ------------------------- | -------------------------------------------- |
| Đổi quà thành công | Backend Loyalty webhook | "Bạn đã đổi [tên quà] thành công" |
| Quà sắp hết hạn     | Scheduled job           | "Bạn có [n] quà sắp hết hạn"         |
| Nhận quà từ campaign | Backend Loyalty webhook | "Bạn nhận được [tên quà]"           |
| Quyền lợi hạng mới  | Backend Loyalty webhook | "Quyền lợi mới cho hạng [tên hạng]"  |

---

> **Ghi chú:** Tài liệu này mô tả phân chia trách nhiệm kỹ thuật giữa các hệ thống Backend. Chi tiết API và cấu trúc dữ liệu xem tại tài liệu orchestrator gốc.
