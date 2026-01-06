# MODULE SẢN PHẨM VÀ ĐẶT HÀNG - PHÂN CHIA TRÁCH NHIỆM HỆ THỐNG

> Tài liệu này giải thích cách phân chia trách nhiệm giữa các hệ thống cho **Module 8: Sản phẩm và Đặt hàng** trong ứng dụng TH Milk.

## MỤC LỤC

1. [Tổng quan Module](#1-tổng-quan-module)
2. [Đề xuất kiến trúc](#2-đề-xuất-kiến-trúc)
3. [Ma trận phân chia trách nhiệm](#3-ma-trận-phân-chia-trách-nhiệm)
4. [Chi tiết từng chức năng](#4-chi-tiết-từng-chức-năng)
5. [Cơ chế đồng bộ dữ liệu](#5-cơ-chế-đồng-bộ-dữ-liệu)
6. [Tổng kết lợi ích](#6-tổng-kết-lợi-ích)

---

## 1. TỔNG QUAN MODULE

### 1.1. Các chức năng chính

Module Sản phẩm và Đặt hàng bao gồm các tính năng:


| Nhóm chức năng | Các tính năng                                       |
| ------------------- | -------------------------------------------------------- |
| **Sản phẩm**    | Danh sách sản phẩm, tìm kiếm, lọc theo danh mục |
| **Chi tiết**     | Thông tin sản phẩm, hình ảnh, giá, variant       |
| **Giỏ hàng**    | Thêm/sửa/xóa sản phẩm, áp dụng voucher          |
| **Thanh toán**   | Chọn địa chỉ, vận chuyển, cổng thanh toán      |
| **Đơn hàng**   | Tạo đơn, xác nhận, biên nhận                    |
| **Flash Sale**    | Khuyến mãi flash, đếm ngược thời gian           |
| **Đánh giá**   | Đánh giá sản phẩm sau khi mua                     |

### 1.2. Đặc điểm dữ liệu


| Loại dữ liệu           | Đặc điểm                             | Yêu cầu                            |
| --------------------------- | ------------------------------------------ | -------------------------------------- |
| **Thông tin sản phẩm** | Tên, mô tả, hình ảnh, giá cơ bản | Thay đổi không thường xuyên    |
| **Danh mục sản phẩm**  | Cây danh mục, phân cấp               | Tĩnh, ít thay đổi                |
| **Tồn kho**              | Số lượng tồn theo cửa hàng         | Thay đổi liên tục, cần realtime |
| **Giá khuyến mãi**     | Giá sale, % giảm                       | Thay đổi theo chương trình      |
| **Flash Sale**            | Thời gian, sản phẩm, giới hạn       | Cấu hình theo chiến dịch         |
| **Giỏ hàng**            | Sản phẩm đang chọn mua               | Theo session/user                    |
| **Đánh giá**           | Rating, bình luận, hình ảnh          | Do người dùng tạo                |

---

## 2. ĐỀ XUẤT KIẾN TRÚC

### 2.1. Nguyên tắc phân chia

Để tối ưu hiệu suất và trải nghiệm người dùng, đề xuất phân chia như sau:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           MANGO CMS                                     │
│                     (Lưu trữ nội dung + Điều phối)                      │
├─────────────────────────────────────────────────────────────────────────┤
│  • Thông tin sản phẩm (tên, mô tả, hình ảnh, thông số kỹ thuật)         │
│  • Danh mục sản phẩm                                                    │
│  • Giỏ hàng (lưu trữ cho cả guest và logged-in user)                    │
│  • Flash Sale (cấu hình, hiển thị)                                      │
│  • Đánh giá sản phẩm                                                    │
│  • Voucher/Coupon (hiển thị danh sách)                                  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ Đồng bộ định kỳ + Realtime khi cần
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                         BACKEND LS RETAIL                               │
│                    (Nguồn dữ liệu gốc + Xử lý giao dịch)                │
├─────────────────────────────────────────────────────────────────────────┤
│  • Tồn kho realtime (số lượng thực tế tại cửa hàng)                     │
│  • Giá bán hiện hành (giá gốc, giá khuyến mãi)                          │
│  • Xử lý đơn hàng (tạo đơn, xác nhận, hủy)                              │
│  • Tính phí vận chuyển                                                  │
│  • Kết nối cổng thanh toán                                              │
│  • Validate voucher (điều kiện áp dụng)                                 │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ Cộng điểm sau thanh toán
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                        BACKEND LOYALTY                                  │
│                       (Điểm thưởng + Khách hàng)                        │
├─────────────────────────────────────────────────────────────────────────┤
│  • Thông tin khách hàng đăng nhập                                       │
│  • Cộng điểm tích lũy sau đơn hàng thành công                           │
│  • Verify voucher loyalty (nếu voucher từ hệ thống loyalty)             │
└─────────────────────────────────────────────────────────────────────────┘
```

### 2.2. Lý do lựa chọn kiến trúc này


| Lợi ích                 | Giải thích                                                                                           |
| --------------------------- | -------------------------------------------------------------------------------------------------------- |
| **Tốc độ truy vấn**   | Dữ liệu sản phẩm lưu trên Mango CMS → trả về nhanh hơn so với query LS Retail               |
| **Giảm tải hệ thống** | LS Retail chỉ xử lý giao dịch quan trọng (tồn kho, thanh toán)                                  |
| **Tìm kiếm linh hoạt** | Mango CMS có thể dùng tính năng search cho tìm kiếm sản phẩm                                  |
| **Quản lý nội dung**   | Thông tin marketing, banner, flash sale quản lý tập trung trên Mango CMS                          |
| **Giỏ hàng nhanh**      | Giỏ hàng lưu thông qua Mango CMS → thao tác thêm/sửa/xóa không phụ thuộc hệ thống ngoài |

---

## 3. MA TRẬN PHÂN CHIA TRÁCH NHIỆM

### 3.1. Tổng quan phân chia


| Chức năng                | Backend Loyalty | Backend LS Retail |         Mango CMS         |
| ---------------------------- | :----------------: | :-----------------: | :--------------------------: |
| **Danh sách sản phẩm**  |        -        |    Nguồn gốc    | ✅**Primary** (lưu trữ) |
| **Chi tiết sản phẩm**   |        -        |    Nguồn gốc    | ✅**Primary** (lưu trữ) |
| **Danh mục sản phẩm**   |        -        |    Nguồn gốc    | ✅**Primary** (lưu trữ) |
| **Tìm kiếm sản phẩm**  |        -        |         -         |       ✅**Primary**       |
| **Tồn kho**               |        -        |   ✅**Primary**   |       Realtime query       |
| **Giá hiện hành**       |        -        |   ✅**Primary**   |   Đồng bộ định kỳ   |
| **Giỏ hàng**             |        -        |         -         |       ✅**Primary**       |
| **Flash Sale**             |        -        |       Rules       | ✅**Primary** (cấu hình) |
| **Voucher/Coupon**         | Verify (loyalty) |    ✅ Validate    |   Hiển thị danh sách   |
| **Thanh toán**            |   Cộng điểm   |   ✅**Primary**   |        Điều phối        |
| **Tạo đơn hàng**       |   Cộng điểm   |   ✅**Primary**   |        Điều phối        |
| **Đánh giá sản phẩm** |        -        |         -         |       ✅**Primary**       |

### 3.2. Giải thích ký hiệu

- **Primary**: Hệ thống chịu trách nhiệm chính, lưu trữ và xử lý
- **Nguồn gốc**: Dữ liệu ban đầu từ đây, được đồng bộ sang Mango CMS
- **Điều phối**: Mango CMS gọi API đến các hệ thống khác để hoàn thành tác vụ
- **Đồng bộ định kỳ**: Dữ liệu được cập nhật theo lịch (không realtime)
- **Realtime query**: Luôn gọi trực tiếp để lấy dữ liệu mới nhất

---

## 4. CHI TIẾT TỪNG CHỨC NĂNG

### 4.1. Danh sách và Chi tiết Sản phẩm

#### Luồng xử lý

```
┌──────────┐      ┌───────────┐      ┌───────────┐
│ Frontend │─────▶│ Mango CMS │─────▶│ LS Retail │
└──────────┘      └───────────┘      └───────────┘
     │                  │                  │
     │  Request sản     │                  │
     │  phẩm            │                  │
     │─────────────────▶│                  │
     │                  │                  │
     │                  │  Query tồn kho   │
     │                  │  (chỉ khi cần)   │
     │                  │─────────────────▶│
     │                  │                  │
     │                  │◀─────────────────│
     │                  │  Số lượng tồn    │
     │                  │                  │
     │◀─────────────────│                  │
     │  Sản phẩm +      │                  │
     │  tồn kho         │                  │
```

#### Phân chia trách nhiệm


| Bước | Hệ thống    | Trách nhiệm                                     |
| -------- | --------------- | --------------------------------------------------- |
| 1      | **Mango CMS** | Nhận request từ Frontend                        |
| 2      | **Mango CMS** | Trả về thông tin sản phẩm từ database local |
| 3      | **LS Retail** | Cung cấp số lượng tồn kho realtime           |
| 4      | **Mango CMS** | Gộp dữ liệu và trả về Frontend              |

#### Dữ liệu sản phẩm lưu trên Mango CMS


| Trường              | Mô tả                                    | Nguồn gốc                          |
| ----------------------- | -------------------------------------------- | -------------------------------------- |
| Tên sản phẩm       | "Sữa tươi TH True Milk 1L"              | Đồng bộ từ LS Retail             |
| Hình ảnh            | Gallery sản phẩm                         | Upload trên Mango CMS               |
| Mô tả               | Thông tin chi tiết                       | Có thể chỉnh sửa trên Mango CMS |
| Thông số kỹ thuật | Thương hiệu, xuất xứ, thành phần... | Đồng bộ từ LS Retail             |
| Danh mục             | Sữa tươi, Sữa chua, ...                | Đồng bộ từ LS Retail             |
| Giá cơ bản         | Giá niêm yết                            | Đồng bộ định kỳ                |
| Tag khuyến mãi      | "Giảm 20%", "Quà tặng kèm"             | Cấu hình trên Mango CMS           |

#### Dữ liệu từ LS Retail (realtime)


| Trường                     | Mô tả               | Lý do realtime                           |
| ------------------------------ | ----------------------- | ------------------------------------------- |
| Tồn kho                     | Số lượng còn lại | Thay đổi liên tục khi có đơn hàng |
| Giá khuyến mãi hiện tại | Giá sau giảm        | Có thể thay đổi theo chương trình  |
| Trạng thái                 | Còn hàng/Hết hàng | Phụ thuộc tồn kho                      |

---

### 4.2. Giỏ hàng

#### Đề xuất: Lưu trữ hoàn toàn trên Mango CMS

```
┌─────────────────────────────────────────────────────────────────┐
│                        MANGO CMS                                │
│                   (Quản lý giỏ hàng)                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌─────────────────┐     ┌─────────────────┐                   │
│   │  Guest Cart     │     │  User Cart      │                   │
│   │  (Session/Redis)│     │  (MongoDB)      │                   │
│   └─────────────────┘     └─────────────────┘                   │
│                                                                 │
│   • Thêm sản phẩm vào giỏ                                       │
│   • Cập nhật số lượng                                           │
│   • Xóa sản phẩm                                                │
│   • Áp dụng voucher (hiển thị)                                  │
│   • Tính tổng tiền tạm tính                                     │
│   • Merge cart khi đăng nhập                                    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ Chỉ khi cần validate
                              ▼
                    ┌─────────────────┐
                    │   LS Retail     │
                    │                 │
                    │ • Check tồn kho │
                    │ • Validate      │
                    │   voucher       │
                    └─────────────────┘
```

#### Phân chia trách nhiệm giỏ hàng


| Thao tác              |     Mango CMS     |        LS Retail        |
| ------------------------ | :------------------: | :------------------------: |
| Thêm sản phẩm       | ✅ Lưu vào cart |      Check tồn kho      |
| Cập nhật số lượng | ✅ Cập nhật cart |      Check tồn kho      |
| Xóa sản phẩm        | ✅ Xóa khỏi cart |            -            |
| Xem giỏ hàng         |  ✅ Trả về cart  |            -            |
| Thêm ghi chú         |  ✅ Lưu ghi chú  |            -            |
| Áp dụng voucher      |  Lưu mã voucher  | ✅ Validate điều kiện |
| Tính tiền tạm tính |   ✅ Tính toán   |            -            |

#### Lợi ích giỏ hàng trên Mango CMS


| Lợi ích                | Giải thích                                       |
| -------------------------- | ---------------------------------------------------- |
| **Thao tác nhanh**      | Thêm/sửa/xóa không cần gọi hệ thống ngoài |
| **Hoạt động offline** | Giỏ hàng vẫn hoạt động nếu LS Retail chậm  |
| **Guest checkout**       | Khách vãng lai có thể mua hàng                |
| **Cart merge**           | Khi đăng nhập, merge giỏ hàng guest vào user |

---

### 4.3. Flash Sale

#### Lưu trữ cấu hình trên Mango CMS

```
┌─────────────────────────────────────────────────────────────────┐
│                        MANGO CMS                                │
│                 (Quản lý Flash Sale)                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Cấu hình Flash Sale:                                          │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ • Tên flash sale: "Flash Sale 12:00"                    │   │
│   │ • Thời gian bắt đầu - kết thúc                          │   │
│   │ • Danh sách sản phẩm tham gia                           │   │
│   │ • Giá flash sale                                        │   │
│   │ • Giới hạn mua mỗi user                                 │   │
│   │ • Số lượng tồn kho cho flash sale                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Hiển thị:                                                     │
│   • Đếm ngược thời gian                                         │
│   • Số lượng đã bán / còn lại                                   │
│   • Trạng thái (Sắp diễn ra / Đang diễn ra / Đã kết thúc)       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### Phân chia trách nhiệm Flash Sale


| Trách nhiệm           | Hệ thống    | Mô tả                        |
| ------------------------- | --------------- | -------------------------------- |
| Cấu hình chiến dịch | **Mango CMS** | Tạo/sửa/xóa flash sale      |
| Danh sách sản phẩm   | **Mango CMS** | Chọn sản phẩm tham gia      |
| Giá flash sale         | **Mango CMS** | Đặt giá đặc biệt         |
| Hiển thị countdown    | **Mango CMS** | Đếm ngược thời gian       |
| Giới hạn mua          | **Mango CMS** | Kiểm tra số lượng đã mua |
| Tồn kho thực tế      | **LS Retail** | Số lượng thực có          |
| Xử lý đơn hàng     | **LS Retail** | Tạo đơn khi mua             |

---

### 4.4. Đánh giá Sản phẩm

#### Mango CMS quản lý toàn bộ đánh giá

```
┌─────────────────────────────────────────────────────────────────┐
│                        MANGO CMS                                │
│              (Quản lý Đánh giá Sản phẩm)                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Dữ liệu đánh giá (MongoDB):                                   │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ • Sản phẩm được đánh giá                                │   │
│   │ • Người đánh giá (user_id)                              │   │
│   │ • Đơn hàng liên quan (order_id)                         │   │
│   │ • Rating (1-5 sao) cho sản phẩm, dịch vụ, giao hàng     │   │
│   │ • Bình luận văn bản                                     │   │
│   │ • Hình ảnh/video đính kèm                               │   │
│   │ • Thời gian đánh giá                                    │   │
│   │ • Trạng thái (published/hidden)                         │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Thống kê hiển thị:                                            │
│   • Rating trung bình                                           │
│   • Phân bố số sao (5 sao: 80, 4 sao: 30, ...)                  │
│   • Tổng số đánh giá                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ Kiểm tra đã mua hàng chưa
                              ▼
                    ┌─────────────────┐
                    │   LS Retail     │
                    │                 │
                    │ • Check order   │
                    │   đã hoàn thành │
                    └─────────────────┘
```

#### Luồng gửi đánh giá


| Bước | Hệ thống    | Trách nhiệm                                                   |
| -------- | --------------- | ----------------------------------------------------------------- |
| 1      | **Frontend**  | User gửi đánh giá (rating, comment, images)                 |
| 2      | **Mango CMS** | Nhận và validate dữ liệu                                    |
| 3      | **LS Retail** | Kiểm tra: User đã mua và nhận hàng sản phẩm này chưa? |
| 4      | **Mango CMS** | Nếu hợp lệ → Lưu đánh giá vào MongoDB                  |
| 5      | **Mango CMS** | Cập nhật rating trung bình của sản phẩm                   |

---

### 4.5. Thanh toán và Đặt hàng

#### Luồng xử lý checkout

```
                              CHECKOUT FLOW

┌──────────┐      ┌───────────┐      ┌───────────┐      ┌─────────────┐
│ Frontend │─────▶│ Mango CMS │─────▶│ LS Retail │─────▶│ Payment GW  │
└──────────┘      └───────────┘      └───────────┘      └─────────────┘
                        │                  │
                        ▼                  │
                  ┌───────────┐            │
                  │  Loyalty  │◀───────────┘
                  └───────────┘
                  (Cộng điểm sau khi thanh toán thành công)
```

#### Chi tiết các bước checkout


| Giai đoạn      | Bước | Hệ thống      | Trách nhiệm                      |
| ------------------ | -------- | ----------------- | ------------------------------------ |
| **Khởi tạo**   | 1      | Mango CMS       | Lấy giỏ hàng hiện tại         |
|                  | 2      | LS Retail       | Validate tồn kho lần cuối       |
|                  | 3      | Loyalty         | Lấy thông tin khách hàng       |
| **Vận chuyển** | 4      | Mango CMS       | Hiển thị form chọn địa chỉ   |
|                  | 5      | LS Retail       | Tính phí vận chuyển            |
| **Thanh toán**  | 6      | Mango CMS       | Hiển thị cổng thanh toán       |
|                  | 7      | LS Retail       | Kiểm tra Gift Card (nếu dùng)   |
| **Xác nhận**   | 8      | LS Retail       | **Tạo đơn hàng**               |
|                  | 9      | Payment Gateway | Xử lý thanh toán                |
|                  | 10     | LS Retail       | Xác nhận đơn hàng             |
|                  | 11     | Loyalty         | **Cộng điểm tích lũy**        |
|                  | 12     | Mango CMS       | Gửi thông báo + Xóa giỏ hàng |

#### Phân chia trách nhiệm thanh toán


| Trách nhiệm               |   Backend Loyalty   | Backend LS Retail | Mango CMS |
| ----------------------------- | :--------------------: | :-----------------: | :---------: |
| Hiển thị checkout form    |          -          |         -         |    ✅    |
| Validate tồn kho           |          -          |        ✅        |     -     |
| Tính phí vận chuyển     |          -          |        ✅        |     -     |
| Validate voucher            | ✅ (loyalty voucher) |    ✅ (coupon)    |     -     |
| **Tạo đơn hàng**        |          -          |        ✅        |     -     |
| Kết nối cổng thanh toán |          -          |        ✅        |     -     |
| **Cộng điểm**            |          ✅          |         -         |     -     |
| Gửi thông báo xác nhận |          -          |         -         |    ✅    |

---

### 4.6. Voucher và Mã khuyến mãi

#### Phân loại voucher


| Loại                  | Nguồn                       | Hệ thống validate |
| ------------------------ | ------------------------------ | --------------------- |
| **Voucher giảm giá** | Chương trình khuyến mãi | LS Retail           |
| **Phiếu quà tặng**  | Tặng từ chiến dịch       | Loyalty             |
| **Coupon code**        | Marketing campaign           | LS Retail           |

#### Luồng áp dụng voucher

```
┌──────────┐      ┌───────────┐      ┌───────────────────────────────┐
│ Frontend │─────▶│ Mango CMS │─────▶│ LS Retail hoặc Loyalty        │
└──────────┘      └───────────┘      └───────────────────────────────┘
     │                  │                           │
     │  Nhập mã         │                           │
     │  voucher         │                           │
     │─────────────────▶│                           │
     │                  │                           │
     │                  │  Gửi validate             │
     │                  │──────────────────────────▶│
     │                  │                           │
     │                  │  Kết quả:                 │
     │                  │  - Hợp lệ/Không hợp lệ    │
     │                  │  - Số tiền giảm           │
     │                  │  - Điều kiện áp dụng      │
     │                  │◀──────────────────────────│
     │                  │                           │
     │◀─────────────────│                           │
     │  Hiển thị kết    │                           │
     │  quả áp dụng     │                           │
```

---

## 5. CƠ CHẾ ĐỒNG BỘ DỮ LIỆU

### 5.1. Đồng bộ sản phẩm từ LS Retail sang Mango CMS

#### Chiến lược đồng bộ


| Loại dữ liệu           | Tần suất đồng bộ | Cơ chế             |
| --------------------------- | ----------------------- | ---------------------- |
| **Danh sách sản phẩm** | 2 giờ/lần           | Scheduled job        |
| **Thông tin sản phẩm** | 2 giờ/lần           | Scheduled job        |
| **Danh mục**             | 1 ngày/lần          | Scheduled job        |
| **Giá cơ bản**         | 2 giờ/lần           | Scheduled job        |
| **Giá khuyến mãi**     | Webhook               | Khi có thay đổi   |
| **Tồn kho**              | Không đồng bộ     | Luôn query realtime |

#### Luồng đồng bộ sản phẩm

```
┌───────────┐                    ┌───────────┐
│ LS Retail │                    │ Mango CMS │
└─────┬─────┘                    └─────┬─────┘
      │                                │
      │      Scheduled Job (2h)        │
      │◀───────────────────────────────│
      │                                │
      │   Danh sách sản phẩm mới/      │
      │   thay đổi                     │
      │───────────────────────────────▶│
      │                                │
      │                                │ Cập nhật database
      │                                │ local
      │                                │
      │                                │
      │      Webhook: product.updated  │
      │───────────────────────────────▶│
      │                                │
      │                                │ Cập nhật sản phẩm
      │                                │ cụ thể
```

### 5.2. Webhook từ LS Retail


| Event                  | Hành động trên Mango CMS               |
| ------------------------ | -------------------------------------------- |
| `product.created`      | Thêm sản phẩm mới vào database        |
| `product.updated`      | Cập nhật thông tin sản phẩm           |
| `product.deleted`      | Đánh dấu sản phẩm không còn bán    |
| `price.changed`        | Cập nhật giá khuyến mãi               |
| `order.status_changed` | Cập nhật trạng thái + gửi thông báo |

### 5.3. Realtime vs Cached


| Dữ liệu             | Cách lấy                      | Lý do                                    |
| ----------------------- | --------------------------------- | ------------------------------------------- |
| Thông tin sản phẩm | **Cached** trên Mango CMS      | Ít thay đổi, cần nhanh                |
| Tồn kho              | **Realtime** từ LS Retail      | Thay đổi liên tục                     |
| Giá hiện tại       | **Cached** + refresh định kỳ | Cân bằng tốc độ và độ chính xác |
| Giỏ hàng            | **Local** trên Mango CMS       | Cần thao tác nhanh                      |
| Flash Sale config     | **Cached** trên Mango CMS      | Cấu hình từ admin                      |

---

## 6. TỔNG KẾT LỢI ÍCH

### 6.1. Lợi ích của kiến trúc đề xuất


| Lợi ích                  | Giải thích                                                 |
| ---------------------------- | -------------------------------------------------------------- |
| **Tốc độ truy vấn**    | Sản phẩm lưu local → trả về trong milliseconds         |
| **Giảm tải LS Retail**   | Chỉ gọi khi cần tồn kho, thanh toán                     |
| **Tìm kiếm linh hoạt**  | Dùng Elasticsearch trên Mango CMS                          |
| **Quản lý nội dung**    | Chỉnh sửa mô tả, hình ảnh không cần động LS Retail |
| **Giỏ hàng nhanh**       | Thao tác không phụ thuộc hệ thống ngoài               |
| **Flash Sale độc lập**  | Cấu hình và quản lý tập trung                          |
| **Đánh giá độc lập** | Không tạo thêm tải cho hệ thống đơn hàng            |

### 6.2. Phương thức thanh toán hỗ trợ


| Cổng thanh toán        | Mô tả                |
| -------------------------- | ------------------------ |
| Momo                     | Ví điện tử Momo    |
| ZaloPay                  | Ví điện tử ZaloPay |
| Napas                    | Thẻ nội địa        |
| OnePay                   | Thẻ quốc tế         |
| VNPay (VietQR)           | QR Code                |
| Tài khoản trả trước | Gift Card TH           |
| Tiền mặt               | COD                    |

### 6.3. Phương thức vận chuyển


| Loại                     | Mô tả                                    |
| --------------------------- | -------------------------------------------- |
| **Giao nhanh 2h**         | Giao trong 2 giờ (có phí)               |
| **Giao tiêu chuẩn**     | Giao trong 1-2 ngày (miễn phí từ 200K) |
| **Nhận tại cửa hàng** | Sẵn sàng trong 1 giờ                    |

---

## SƠ ĐỒ TỔNG QUAN

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              FRONTEND APP                                   │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              MANGO CMS                                      │
│                                                                             │
│  ┌────────────────┐ ┌────────────────┐ ┌────────────────┐ ┌──────────────┐  │
│  │   Sản phẩm     │ │   Giỏ hàng     │ │  Flash Sale    │ │  Đánh giá    │  │
│  │   (Cached)     │ │   (Primary)    │ │  (Primary)     │ │  (Primary)   │  │
│  └────────────────┘ └────────────────┘ └────────────────┘ └──────────────┘  │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    ORCHESTRATION LAYER                              │    │
│  │            (Điều phối gọi đến các Backend khi cần)                  │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
                │                                       │
                │ Đồng bộ + Realtime                    │ Khi checkout
                ▼                                       ▼
┌────────────────────────────────┐      ┌────────────────────────────────────┐
│       BACKEND LS RETAIL        │      │         BACKEND LOYALTY            │
│                                │      │                                    │
│  • Nguồn dữ liệu sản phẩm      │      │  • Thông tin khách hàng            │
│  • Tồn kho realtime            │      │  • Cộng điểm sau thanh toán        │
│  • Xử lý đơn hàng              │      │  • Validate loyalty voucher        │
│  • Tính phí vận chuyển         │      │                                    │
│  • Kết nối cổng thanh toán     │      │                                    │
│  • Validate coupon             │      │                                    │
└────────────────────────────────┘      └────────────────────────────────────┘
```

---

*Tài liệu này mô tả phân chia trách nhiệm Module 8 - Sản phẩm và Đặt hàng trong hệ thống TH Milk App.*
