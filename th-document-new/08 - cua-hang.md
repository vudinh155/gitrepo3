# MODULE CỬA HÀNG - PHÂN CHIA TRÁCH NHIỆM HỆ THỐNG

> Tài liệu kỹ thuật mô tả cách phân chia trách nhiệm giữa các hệ thống Backend cho **Module 9: Cửa hàng**.

---

## MỤC LỤC

1. [Tổng quan Module](#1-tổng-quan-module)
2. [Đề xuất kiến trúc](#2-đề-xuất-kiến-trúc)
3. [Phân chia trách nhiệm](#3-phân-chia-trách-nhiệm)
4. [Chi tiết trách nhiệm theo chức năng](#4-chi-tiết-trách-nhiệm-theo-chức-năng)
5. [Cơ chế đồng bộ dữ liệu](#5-cơ-chế-đồng-bộ-dữ-liệu)
6. [Luồng xử lý](#6-luồng-xử-lý)
7. [Tổng kết](#7-tổng-kết)

---

## 1. TỔNG QUAN MODULE

### 1.1. Phạm vi Module Cửa hàng

Module Cửa hàng bao gồm các chức năng:

- **Danh sách cửa hàng:** Hiển thị danh sách TH True Mart
- **Tìm cửa hàng gần nhất:** Dựa trên vị trí GPS của người dùng
- **Chi tiết cửa hàng:** Thông tin, giờ mở cửa, dịch vụ
- **Bộ lọc cửa hàng:** Lọc theo khu vực, dịch vụ, trạng thái
- **Tích hợp Google Maps:** Hiển thị bản đồ, chỉ đường

### 1.2. Đặc điểm dữ liệu cửa hàng


| Đặc điểm              | Mô tả                                                   |
| --------------------------- | ----------------------------------------------------------- |
| **Số lượng**           | Khoảng 300-1000 cửa hàng (không quá nhiều)          |
| **Tần suất thay đổi** | Thấp - cửa hàng mới mở/đóng không thường xuyên |
| **Dữ liệu tĩnh**       | Địa chỉ, tọa độ, giờ mở cửa ít thay đổi       |
| **Yêu cầu realtime**    | Không cần thiết cho dữ liệu cửa hàng               |

---

## 2. ĐỀ XUẤT KIẾN TRÚC

### 2.1. Thay đổi so với thiết kế ban đầu

**Thiết kế ban đầu:**

```
Frontend → Mango CMS → LS Retail (realtime)
```

**Đề xuất mới:**

```
Frontend → Mango CMS (Primary Storage)
                ↑
         Sync daily từ LS Retail
```

### 2.2. Lý do đề xuất


| Lý do                              | Giải thích                                                       |
| ------------------------------------- | -------------------------------------------------------------------- |
| **Số lượng ít, ít thay đổi** | ~50-100 cửa hàng, không cần query realtime                     |
| **Giảm phụ thuộc LS Retail**     | Không cần gọi API LS Retail mỗi request                        |
| **Tăng tốc độ**                 | Query trực tiếp từ MongoDB nhanh hơn                           |
| **Đơn giản hóa**                | Bớt một layer giao tiếp                                         |
| **Chủ động quản lý**           | Có thể bổ sung thông tin (hình ảnh, mô tả) trên Mango CMS |

### 2.3. Phương án đồng bộ


| Phương án           | Mô tả                                                     |
| ------------------------ | ------------------------------------------------------------- |
| **Sync hàng ngày**   | Job chạy mỗi đêm (VD: 2:00 AM) đồng bộ từ LS Retail |
| **Sync thủ công**    | Admin trigger sync khi có cửa hàng mới                  |
| **Webhook (optional)** | LS Retail gửi webhook khi có thay đổi                   |

---

## 3. PHÂN CHIA TRÁCH NHIỆM

### 3.1. Ma trận phân chia (Đề xuất mới)


| Chức năng                        | Backend Loyalty | Backend LS Retail |  Mango CMS  |
| ------------------------------------ | :---------------: | :-----------------: | :------------: |
| **Lưu trữ dữ liệu cửa hàng** |        -        |      Source      | **Primary** |
| **Danh sách cửa hàng**          |        -        |         -         | **Primary** |
| **Chi tiết cửa hàng**           |        -        |         -         | **Primary** |
| **Tìm cửa hàng gần nhất****** |        -        |         -         | **Primary** |
| Tính khoảng cách                |        -        |         -         | **Primary** |
| Bộ lọc cửa hàng                |        -        |         -         | **Primary** |
| Google Maps integration            |        -        |         -         | **Primary** |
| Đồng bộ dữ liệu               |        -        |   Cung cấp API   | Schedule job |
| Quản lý hình ảnh cửa hàng    |        -        |         -         | **Primary** |

### 3.2. Chi tiết trách nhiệm từng hệ thống

#### Backend LS Retail - Nguồn dữ liệu gốc (Source of Truth)


| Trách nhiệm                | Mô tả                                                           |
| ------------------------------ | ------------------------------------------------------------------- |
| **Quản lý master data**    | Thông tin chính thức của cửa hàng trong hệ thống bán lẻ |
| **Cung cấp API đồng bộ** | API để Mango CMS lấy danh sách cửa hàng                     |
| **Thông báo thay đổi**   | Webhook khi có cửa hàng mới/cập nhật (optional)             |

**Dữ liệu LS Retail cung cấp:**

- Store ID, Store Code
- Tên cửa hàng
- Địa chỉ (đường, phường, quận, thành phố)
- Số điện thoại
- Tọa độ GPS (latitude, longitude)
- Giờ hoạt động
- Trạng thái (active/inactive)
- Dịch vụ hỗ trợ (pickup, delivery)

#### Mango CMS - Lưu trữ và xử lý chính


| Trách nhiệm                      | Mô tả                                          |
| ------------------------------------ | -------------------------------------------------- |
| **Lưu trữ dữ liệu cửa hàng** | MongoDB collection stores                        |
| **Đồng bộ từ LS Retail**       | Scheduled job sync hàng ngày                   |
| **Xử lý API requests**           | Danh sách, chi tiết, tìm kiếm, lọc          |
| **Tính toán khoảng cách**      | Haversine formula từ user location              |
| **Quản lý hình ảnh**           | Upload, lưu trữ hình ảnh cửa hàng          |
| **Bổ sung thông tin**            | Mô tả, hình ảnh slider, thông tin marketing |
| **Google Maps integration**        | API key, marker config, directions               |

#### Backend Loyalty - Không tham gia

Backend Loyalty **không có vai trò** trong Module Cửa hàng.

---

## 4. CHI TIẾT TRÁCH NHIỆM THEO CHỨC NĂNG

### 4.1. Danh sách Cửa hàng

**Luồng dữ liệu:** Frontend → Mango CMS (direct query)


| Bước              | Hệ thống thực hiện | Trách nhiệm                           |
| --------------------- | ------------------------ | ----------------------------------------- |
| Nhận request       | Mango CMS              | Parse filters, search query             |
| Query database      | Mango CMS              | MongoDB query với filters              |
| Tính khoảng cách | Mango CMS              | Nếu có user location, dùng Haversine |
| Sắp xếp kết quả | Mango CMS              | Sort by distance/name                   |
| Trả về Frontend   | Mango CMS              | Paginated response                      |

**Mango CMS xử lý hoàn toàn:**

- Query danh sách từ MongoDB
- Filter theo city, district, ward, services, status
- Search theo tên cửa hàng
- Pagination

---

### 4.2. Tìm Cửa hàng Gần nhất

**Luồng dữ liệu:** Frontend → Mango CMS (calculate)


| Bước                    | Hệ thống thực hiện | Trách nhiệm                       |
| --------------------------- | ------------------------ | ------------------------------------- |
| Lấy vị trí user        | Frontend               | GPS/Geolocation API                 |
| Gửi tọa độ            | Mango CMS              | Nhận lat/lng từ Frontend          |
| Query tất cả cửa hàng | Mango CMS              | Lấy stores có tọa độ           |
| Tính khoảng cách       | Mango CMS              | Haversine formula cho từng store   |
| Filter theo radius        | Mango CMS              | Chỉ lấy stores trong bán kính   |
| Sort và limit            | Mango CMS              | Sắp xếp theo distance, lấy top N |

**Công thức Haversine (Mango CMS tính):**


| Input         | Output                           |
| --------------- | ---------------------------------- |
| User lat/lng  | Khoảng cách (km)               |
| Store lat/lng | Thời gian đi bộ (ước tính) |
|               | Thời gian lái xe (ước tính) |

---

### 4.3. Chi tiết Cửa hàng

**Luồng dữ liệu:** Frontend → Mango CMS (direct query)


| Bước                   | Hệ thống thực hiện | Trách nhiệm                               |
| -------------------------- | ------------------------ | --------------------------------------------- |
| Nhận request            | Mango CMS              | Store ID                                    |
| Query database           | Mango CMS              | MongoDB findById                            |
| Xác định trạng thái | Mango CMS              | Đang mở/đóng dựa trên giờ hiện tại |
| Build response           | Mango CMS              | Gộp thông tin + actions                   |

**Mango CMS lưu trữ và trả về:**

- Thông tin cơ bản (tên, địa chỉ, SĐT)
- Hình ảnh slider (upload trên Mango CMS)
- Giờ hoạt động theo ngày
- Trạng thái mở/đóng (tính realtime)
- Dịch vụ hỗ trợ
- Tọa độ cho Google Maps
- Actions (Gọi điện, Chỉ đường)

---

### 4.4. Bộ lọc Cửa hàng

**Luồng dữ liệu:** Frontend → Mango CMS


| Bước              | Hệ thống thực hiện | Trách nhiệm                       |
| --------------------- | ------------------------ | ------------------------------------- |
| Lấy filter options | Mango CMS              | Aggregate từ stores collection     |
| Build filter UI     | Mango CMS              | Danh sách cities, districts, wards |
| Đếm số lượng   | Mango CMS              | Count stores per filter option      |

**Mango CMS cung cấp filter options:**

- Danh sách Tỉnh/Thành phố (có count)
- Danh sách Quận/Huyện theo TP (có count)
- Danh sách Phường/Xã theo Quận (có count)
- Danh sách dịch vụ
- Trạng thái (Đang mở / Tất cả)

---

### 4.5. Google Maps Integration

**Mango CMS xử lý hoàn toàn:**


| Trách nhiệm      | Mô tả                                       |
| -------------------- | ----------------------------------------------- |
| **Config Maps**    | Lưu trữ API key, default center, zoom level |
| **Marker config**  | Custom marker icon cho TH True Mart           |
| **Cluster config** | Gom nhóm markers khi zoom out                |
| **Directions**     | Build Google Maps URL cho chỉ đường       |

---

## 5. CƠ CHẾ ĐỒNG BỘ DỮ LIỆU

### 5.1. Scheduled Sync (Đề xuất chính)


| Thông số         | Giá trị                                         |
| -------------------- | --------------------------------------------------- |
| **Tần suất**     | Mỗi ngày 1 lần                                 |
| **Thời điểm**   | 2:00 AM (giờ thấp điểm)                       |
| **Phương thức** | Mango CMS gọi API LS Retail                      |
| **Xử lý**        | Full sync hoặc incremental (based on updated_at) |

### 5.2. Luồng đồng bộ

```
Mango CMS                               LS Retail
    │                                       │
    │── [2:00 AM] GET /stores ─────────────>│
    │                                       │── Query all stores
    │<───────── Store list ─────────────────│
    │                                       │
    │── Compare với data hiện tại ──┐       │
    │<──────────────────────────────┘       │
    │                                       │
    │── Upsert vào MongoDB ──────────┐      │
    │<───────────────────────────────┘      │
    │                                       │
    │── Log sync result ─────────────┐      │
    │<───────────────────────────────┘      │
```

### 5.3. Xử lý đồng bộ


| Trường hợp               | Xử lý                                                   |
| ----------------------------- | ----------------------------------------------------------- |
| **Store mới**              | Insert vào MongoDB                                       |
| **Store cập nhật**        | Update fields từ LS Retail                               |
| **Store bị xóa/inactive** | Soft delete (status = inactive)                           |
| **Dữ liệu bổ sung**      | Giữ nguyên (hình ảnh, mô tả do Mango CMS quản lý) |

### 5.4. Manual Sync (Optional)


| Trường hợp              | Trigger            |
| ---------------------------- | -------------------- |
| Cửa hàng mới mở        | Admin trigger sync |
| Sửa thông tin khẩn cấp | Admin trigger sync |
| Fix data issue             | Admin trigger sync |

---

## 6. LUỒNG XỬ LÝ

### 6.1. Luồng: User xem danh sách cửa hàng gần nhất

```
Frontend                    Mango CMS                   MongoDB
   │                            │                          │
   │── GET /stores/nearest ────>│                          │
   │   (lat, lng, radius)       │                          │
   │                            │── Query stores ─────────>│
   │                            │<── All stores ───────────│
   │                            │                          │
   │                            │── Calculate distances ─┐ │
   │                            │<───────────────────────┘ │
   │                            │                          │
   │                            │── Filter by radius ────┐ │
   │                            │<───────────────────────┘ │
   │                            │                          │
   │                            │── Sort by distance ────┐ │
   │                            │<───────────────────────┘ │
   │                            │                          │
   │<── Nearest stores ──────── │                          │
```

### 6.2. Luồng: Đồng bộ dữ liệu hàng ngày

```
Scheduler                   Mango CMS                   LS Retail
   │                            │                          │
   │── Trigger (2:00 AM) ──────>│                          │
   │                            │── GET /api/stores ──────>│
   │                            │<── Store list ───────────│
   │                            │                          │
   │                            │── Process each store ─┐  │
   │                            │   - Compare           │  │
   │                            │   - Upsert to MongoDB │  │
   │                            │<──────────────────────┘  │
   │                            │                          │
   │                            │── Log: "Synced N stores" │
   │<── Done ──────────────────│                           │
```

---

## 7. TỔNG KẾT

### 7.1. Tóm tắt trách nhiệm


| Hệ thống          | Vai trò                     | Dữ liệu                 |
| --------------------- | ------------------------------ | --------------------------- |
| **LS Retail**       | Source of Truth              | Master data cửa hàng    |
| **Mango CMS**       | Primary Storage + Processing | Copy data + Enriched data |
| **Backend Loyalty** | Không tham gia              | -                         |

### 7.2. Lợi ích của kiến trúc đề xuất


| Lợi ích       | Mô tả                                             |
| ----------------- | ----------------------------------------------------- |
| **Performance** | Query trực tiếp MongoDB, không qua LS Retail     |
| **Độc lập**  | Mango CMS hoạt động độc lập với LS Retail    |
| **Linh hoạt**  | Có thể bổ sung thông tin (hình ảnh, mô tả)  |
| **Đơn giản** | Bớt complexity trong runtime                       |
| **Reliable**    | Không bị ảnh hưởng nếu LS Retail có downtime |

### 7.3. Dữ liệu phân chia


| Dữ liệu             | Nguồn gốc   | Lưu trữ tại   |
| ----------------------- | --------------- | ------------------ |
| Store ID, Code, Name  | LS Retail     | Mango CMS (sync) |
| Địa chỉ, Tọa độ | LS Retail     | Mango CMS (sync) |
| Giờ hoạt động     | LS Retail     | Mango CMS (sync) |
| Số điện thoại     | LS Retail     | Mango CMS (sync) |
| Trạng thái active   | LS Retail     | Mango CMS (sync) |
| Hình ảnh slider     | **Mango CMS** | Mango CMS        |
| Mô tả marketing     | **Mango CMS** | Mango CMS        |
| Google Maps config    | **Mango CMS** | Mango CMS        |

### 7.4. Nguyên tắc quan trọng


| Nguyên tắc                          | Mô tả                                                  |
| --------------------------------------- | ---------------------------------------------------------- |
| **LS Retail là nguồn gốc**         | Dữ liệu chính thức về cửa hàng vẫn từ LS Retail |
| **Mango CMS là bản sao làm việc** | Copy để phục vụ Frontend, có thể enrich            |
| **Sync định kỳ**                   | Đảm bảo dữ liệu nhất quán                         |
| **Không query realtime**             | Tất cả query từ MongoDB của Mango CMS                |

---

> **Ghi chú:** Kiến trúc này phù hợp với đặc điểm dữ liệu cửa hàng (số lượng ít, ít thay đổi). Nếu tương lai có yêu cầu realtime (VD: tồn kho theo cửa hàng), cần xem xét lại.
