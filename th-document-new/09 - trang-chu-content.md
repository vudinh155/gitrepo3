# MODULE TRANG CHỦ & CONTENT - VAI TRÒ CỦA MANGO CMS

> Tài liệu này giải thích vai trò của **Mango CMS** trong việc quản lý **Module 11: Trang chủ & Content** - một trong những module mà Mango CMS đóng vai trò chủ đạo.

## MỤC LỤC

1. [Tổng quan Module](#1-tổng-quan-module)
2. [Vai trò chủ đạo của Mango CMS](#2-vai-trò-chủ-đạo-của-mango-cms)
3. [Các tính năng chính](#3-các-tính-năng-chính)
4. [Chi tiết từng chức năng](#4-chi-tiết-từng-chức-năng)
5. [Điểm mạnh của Mango CMS](#5-điểm-mạnh-của-mango-cms)
6. [Tổng kết](#6-tổng-kết)

---

## 1. TỔNG QUAN MODULE

### 1.1. Module Trang chủ & Content là gì?

Module này bao gồm tất cả nội dung hiển thị trên ứng dụng mà **không liên quan trực tiếp đến giao dịch mua bán**. Đây là frontend của ứng dụng, nơi khách hàng tiếp xúc đầu tiên.

### 1.2. Các thành phần chính

| Thành phần             | Mô tả                                   | Ví dụ                              |
| ---------------------- | --------------------------------------- | ---------------------------------- |
| **Trang chủ**          | Màn hình đầu tiên khi mở app            | Banner, chào mừng, menu            |
| **Banner quảng cáo**   | Hình ảnh quảng bá sản phẩm/chương trình | Slider, popup, category banner     |
| **Trang tĩnh**         | Nội dung thông tin cố định              | FAQ, chính sách, liên hệ           |
| **Flash Sale Display** | Hiển thị chương trình khuyến mãi        | Countdown, sản phẩm sale           |
| **Menu điều hướng**    | Thanh menu dưới cùng                    | Trang chủ, Sản phẩm, TH Rewards... |

### 1.3. Đặc điểm nội dung

| Đặc điểm                  | Mô tả                                      |
| ------------------------- | ------------------------------------------ |
| **Thay đổi thường xuyên** | Banner, chương trình mới cập nhật liên tục |
| **Đa dạng định dạng**     | Hình ảnh, video, text, HTML                |
| **Cá nhân hóa**           | Nội dung khác nhau theo hạng thành viên    |
| **Đa ngôn ngữ**           | Hỗ trợ tiếng Việt và tiếng Anh             |
| **Lên lịch tự động**      | Banner tự động bật/tắt theo thời gian      |

---

## 2. VAI TRÒ CHỦ ĐẠO CỦA MANGO CMS

### 2.1. Mango CMS là gì?

**Mango CMS** (Content Management System) là hệ thống quản lý nội dung - đóng vai trò là "trung tâm điều phối" giữa Frontend và các Backend.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              MANGO CMS                                      │
│                    HỆ THỐNG QUẢN LÝ NỘI DUNG                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                    QUẢN LÝ NỘI DUNG                                 │   │
│   │  • Banner (slider, popup, category)                                 │   │
│   │  • Trang tĩnh (FAQ, chính sách, giới thiệu)                         │   │
│   │  • Chương trình mới, sự kiện                                        │   │
│   │  • Flash Sale (cấu hình hiển thị)                                   │   │
│   │  • Menu điều hướng                                                  │   │
│   │  • Thông điệp, lời chào                                             │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                    CÁ NHÂN HÓA NỘI DUNG                             │   │
│   │  • Banner theo hạng thành viên                                      │   │
│   │  • Ưu đãi dành riêng cho từng nhóm khách                            │   │
│   │  • Đề xuất dựa trên lịch sử                                         │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                    ĐA NGÔN NGỮ                                      │   │
│   │  • Tiếng Việt (mặc định)                                            │   │
│   │  • Tiếng Anh                                                        │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.2. Ma trận phân chia trách nhiệm

| Chức năng                        | Backend Loyalty  | Backend LS Retail |   Mango CMS    |
| -------------------------------- | :--------------: | :---------------: | :------------: |
| **Trang chủ - Nội dung**         |        -         |         -         | ✅**Primary**  |
| **Trang chủ - Thông tin user**   |   ✅ Cung cấp    |         -         |    Hiển thị    |
| **Trang chủ - Sản phẩm nổi bật** |        -         |    ✅ Cung cấp    |    Hiển thị    |
| **Banner quản lý**               |        -         |         -         | ✅**Primary**  |
| **Popup banner**                 |        -         |         -         | ✅**Primary**  |
| **Trang tĩnh (FAQ, Policy)**     |        -         |         -         | ✅**Primary**  |
| **Flash Sale - Hiển thị**        |        -         |       Rules       | ✅**Primary**  |
| **Chương trình mới**             | ✅ Campaign data |         -         | ✅**Hiển thị** |
| **Menu điều hướng**              |        -         |         -         | ✅**Primary**  |
| **Đa ngôn ngữ**                  |        -         |         -         | ✅**Primary**  |

### 2.3. Tại sao Mango CMS phù hợp cho Module này?

| Lý do                  | Giải thích                                                       |
| ---------------------- | ---------------------------------------------------------------- |
| **Chuyên về nội dung** | CMS được thiết kế để quản lý content, không phải xử lý giao dịch |
| **Linh hoạt**          | Dễ dàng thêm/sửa/xóa banner, trang mới mà không cần developer    |
| **Lên lịch tự động**   | Hỗ trợ schedule banner theo thời gian                            |
| **Cá nhân hóa**        | Có thể target content theo nhóm khách hàng                       |
| **Đa ngôn ngữ**        | Hỗ trợ sẵn việc quản lý nhiều ngôn ngữ                           |
| **Cache thông minh**   | Tối ưu tốc độ tải trang                                          |

---

## 3. CÁC TÍNH NĂNG CHÍNH

### 3.1. Tổng quan tính năng

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         TÍNH NĂNG MANGO CMS                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ╔═══════════════════╗  ╔═══════════════════╗  ╔═══════════════════╗        │
│  ║  BANNER           ║  ║  TRANG TĨNH       ║  ║  CÁ NHÂN HÓA      ║        │
│  ║  MANAGEMENT       ║  ║  MANAGEMENT       ║  ║  CONTENT          ║        │
│  ╠═══════════════════╣  ╠═══════════════════╣  ╠═══════════════════╣        │
│  ║ • Slider banner   ║  ║ • FAQ             ║  ║ • Theo hạng TV    ║        │
│  ║ • Popup banner    ║  ║ • Chính sách      ║  ║ • Theo segment    ║        │
│  ║ • Category banner ║  ║ • Giới thiệu      ║  ║ • Đề xuất cá nhân ║        │
│  ║ • Schedule auto   ║  ║ • Liên hệ         ║  ║                   ║        │
│  ╚═══════════════════╝  ╚═══════════════════╝  ╚═══════════════════╝        │
│                                                                             │
│  ╔═══════════════════╗  ╔═══════════════════╗  ╔═══════════════════╗        │
│  ║  FLASH SALE       ║  ║  ĐA NGÔN NGỮ      ║  ║  SEO              ║        │
│  ║  DISPLAY          ║  ║  SUPPORT          ║  ║  MANAGEMENT       ║        │
│  ╠═══════════════════╣  ╠═══════════════════╣  ╠═══════════════════╣        │
│  ║ • Countdown timer ║  ║ • Tiếng Việt      ║  ║ • Meta tags       ║        │
│  ║ • Progress bar    ║  ║ • Tiếng Anh       ║  ║ • Sitemap         ║        │
│  ║ • Teaser upcoming ║  ║ • Dễ mở rộng      ║  ║ • Schema markup   ║        │
│  ╚═══════════════════╝  ╚═══════════════════╝  ╚═══════════════════╝        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 4. CHI TIẾT TỪNG CHỨC NĂNG

### 4.1. Quản lý Trang chủ

#### Trang chủ cho khách chưa đăng nhập

| Thành phần                | Mô tả                                            | Nguồn dữ liệu                          |
| ------------------------- | ------------------------------------------------ | -------------------------------------- |
| **Header**                | Thông điệp chào mừng "Ngày đẹp trời để mua sắm!" | Mango CMS                              |
| **Nút đăng nhập/đăng ký** | Điều hướng đến form                              | Mango CMS                              |
| **Banner slider**         | Khuyến mãi nổi bật (carousel)                    | Mango CMS                              |
| **Banner Gift Card**      | Quảng cáo mua Gift Card                          | Mango CMS                              |
| **Chương trình mới**      | Danh sách chương trình                           | Loyalty → Mango CMS hiển thị           |
| **Sự kiện nổi bật**       | Danh sách sự kiện                                | Mango CMS                              |
| **Flash Sale**            | Block hiển thị sale                              | Mango CMS (config) + LS Retail (rules) |
| **Menu bottom**           | 5 tab điều hướng                                 | Mango CMS                              |

#### Trang chủ cho khách đã đăng nhập

| Thành phần             | Mô tả                              | Nguồn dữ liệu                  |
| ---------------------- | ---------------------------------- | ------------------------------ |
| **Header cá nhân hóa** | "Xin chào [Tên], Hạng [Vàng]"      | Loyalty → Mango CMS hiển thị   |
| **Số dư & Điểm**       | Tài khoản trả trước, điểm tích lũy | Loyalty                        |
| **Thông báo quà tặng** | "Bạn có 3 quà tặng đang chờ"       | Loyalty                        |
| **Nút nạp tiền**       | Điều hướng đến nạp tiền            | Mango CMS                      |
| **Banner cá nhân hóa** | Banner theo hạng thành viên        | Mango CMS                      |
| **Ưu đãi riêng**       | Khuyến mãi dành cho bạn            | Mango CMS + Loyalty            |
| **Sản phẩm đề xuất**   | Dựa trên lịch sử mua               | LS Retail → Mango CMS hiển thị |
| **Đơn hàng gần đây**   | Trạng thái đơn hàng                | LS Retail → Mango CMS hiển thị |

#### Luồng xử lý trang chủ

```
                    TRANG CHỦ ĐÃ ĐĂNG NHẬP

┌──────────┐      ┌───────────┐      ┌───────────┐      ┌───────────┐
│ Frontend │─────▶│ Mango CMS │─────▶│  Loyalty  │      │ LS Retail │
└──────────┘      └───────────┘      └───────────┘      └───────────┘
     │                  │                  │                  │
     │  Request         │                  │                  │
     │  trang chủ       │                  │                  │
     │─────────────────▶│                  │                  │
     │                  │                  │                  │
     │                  │  Lấy thông tin   │                  │
     │                  │  user (tên,      │                  │
     │                  │  hạng, điểm)     │                  │
     │                  │─────────────────▶│                  │
     │                  │                  │                  │
     │                  │◀─────────────────│                  │
     │                  │                  │                  │
     │                  │  Lấy sản phẩm    │                  │
     │                  │  đề xuất         │                  │
     │                  │─────────────────────────────────────▶
     │                  │                  │                  │
     │                  │◀─────────────────────────────────────
     │                  │                  │                  │
     │                  │  GỘP DỮ LIỆU:    │                  │
     │                  │  • Banner từ CMS │                  │
     │                  │  • User từ Loyalty                  │
     │                  │  • Products từ LS│                  │
     │                  │                  │                  │
     │◀─────────────────│                  │                  │
     │  Trang chủ       │                  │                  │
     │  hoàn chỉnh      │                  │                  │
```

---

### 4.2. Quản lý Banner

#### Các loại banner

| Loại Banner         | Vị trí hiển thị       | Mô tả                                     |
| ------------------- | --------------------- | ----------------------------------------- |
| **Homepage Slider** | Trang chủ - phía trên | Carousel banner chính, tự động chuyển     |
| **Category Banner** | Đầu trang danh mục    | Banner riêng cho mỗi danh mục sản phẩm    |
| **Popup Banner**    | Toàn app/web          | Popup có thể tắt, hiển thị theo điều kiện |
| **In-app Banner**   | Trong các section     | Banner nhỏ xen kẽ trong nội dung          |

#### Tính năng quản lý banner

| Tính năng            | Mô tả                                | Lợi ích                                |
| -------------------- | ------------------------------------ | -------------------------------------- |
| **Responsive**       | Hình ảnh riêng cho Desktop và Mobile | Hiển thị tối ưu trên mọi thiết bị      |
| **Lên lịch tự động** | Đặt ngày bắt đầu/kết thúc            | Banner tự bật/tắt, không cần can thiệp |
| **Target audience**  | Chọn hiển thị theo hạng thành viên   | Cá nhân hóa nội dung                   |
| **Ưu tiên hiển thị** | Đặt thứ tự ưu tiên                   | Banner quan trọng hiển thị trước       |
| **Tracking link**    | Gắn link điều hướng                  | Đo lường hiệu quả banner               |

#### Popup Banner - Tính năng đặc biệt

Popup banner có nhiều tùy chọn hiển thị thông minh:

| Tùy chọn                | Mô tả                                     | Ví dụ                          |
| ----------------------- | ----------------------------------------- | ------------------------------ |
| **Chọn trang hiển thị** | Toàn bộ app hoặc chỉ trang cụ thể         | Chỉ hiện trên trang Sản phẩm   |
| **Chọn danh mục**       | Hiển thị trên danh mục cụ thể             | Chỉ hiện khi xem Sữa Tươi      |
| **Thời điểm hiển thị**  | Khi load trang, khi scroll, khi rời trang | Hiện sau 3 giây                |
| **Giới hạn tần suất**   | Số lần hiển thị tối đa                    | Tối đa 1 lần/phiên, 2 lần/ngày |
| **Target theo user**    | Hiển thị cho nhóm khách cụ thể            | Chỉ cho user mới               |

---

### 4.3. Quản lý Trang tĩnh (Static Pages)

#### Danh sách trang tĩnh

| Trang                    | Đường dẫn           | Mô tả                       |
| ------------------------ | ------------------- | --------------------------- |
| **Điều khoản sử dụng**   | /policy/terms       | Điều kiện sử dụng dịch vụ   |
| **Điều khoản eGift**     | /policy/egift-terms | Quy định sử dụng Gift Card  |
| **Chính sách bảo mật**   | /policy/privacy     | Bảo vệ thông tin khách hàng |
| **Chính sách giao hàng** | /policy/delivery    | Quy định vận chuyển         |
| **Chính sách đổi trả**   | /policy/return      | Điều kiện đổi/trả hàng      |
| **FAQ**                  | /faq                | Câu hỏi thường gặp          |
| **Liên hệ**              | /contact            | Form phản hồi, hotline      |
| **Giới thiệu**           | /about              | Thông tin về TH             |

#### Tính năng quản lý trang tĩnh

| Tính năng            | Mô tả                                               |
| -------------------- | --------------------------------------------------- |
| **Rich Text Editor** | Soạn thảo nội dung với định dạng phong phú          |
| **Đa ngôn ngữ**      | Mỗi trang có phiên bản Việt/Anh riêng               |
| **SEO**              | Cấu hình title, description, keywords cho mỗi trang |
| **Version history**  | Lưu lịch sử chỉnh sửa, có thể khôi phục             |
| **Preview**          | Xem trước nội dung trước khi publish                |

---

### 4.4. Quản lý FAQ

#### Tính năng FAQ

| Tính năng                   | Mô tả                                  |
| --------------------------- | -------------------------------------- |
| **Phân loại theo danh mục** | Dễ tìm kiếm theo chủ đề                |
| **Tìm kiếm**                | Tìm nhanh bằng từ khóa                 |
| **Đánh giá hữu ích**        | User vote câu trả lời có hữu ích không |
| **Câu hỏi nổi bật**         | Hiển thị FAQ được xem nhiều nhất       |
| **Đa ngôn ngữ**             | FAQ tiếng Việt và tiếng Anh            |

---

### 4.5. Hiển thị Flash Sale

#### Vai trò của Mango CMS trong Flash Sale

| Trách nhiệm                       | Mango CMS | LS Retail |
| --------------------------------- | :-------: | :-------: |
| **Cấu hình hiển thị**             |    ✅     |     -     |
| **Countdown timer**               |    ✅     |     -     |
| **Layout & Design**               |    ✅     |     -     |
| **Teaser "sắp diễn ra"**          |    ✅     |     -     |
| **Progress bar (đã bán/còn lại)** |    ✅     |     -     |
| **Quy tắc giảm giá**              |     -     |    ✅     |
| **Số lượng tồn kho**              |     -     |    ✅     |

#### Hiển thị Flash Sale trên trang chủ

---

### 4.6. Hiển thị Promotion Tags

#### Các loại tag khuyến mãi

Mango CMS quản lý cách hiển thị các tag khuyến mãi trên sản phẩm:

| Loại Tag      | Màu sắc    | Ví dụ                       |
| ------------- | ---------- | --------------------------- |
| **Giảm giá**  | Đỏ cam     | "Giảm 20%"                  |
| **Quà tặng**  | Xanh lá    | "Quà tặng kèm"              |
| **Combo**     | Xanh dương | "Mua 2 tặng 1"              |
| **Điều kiện** | Tím        | "Đơn từ 500K giảm thêm 10%" |

#### Vị trí hiển thị

| Vị trí                 | Hiển thị                            |
| ---------------------- | ----------------------------------- |
| **Thumbnail sản phẩm** | Tag góc trên (Giảm 20%)             |
| **Chi tiết sản phẩm**  | Danh sách tất cả khuyến mãi áp dụng |
| **Giỏ hàng**           | Tóm tắt ưu đãi đang được áp dụng    |

---

### 4.7. Hỗ trợ Đa ngôn ngữ

#### Ngôn ngữ hỗ trợ

| Ngôn ngữ       | Mã  | Trạng thái |
| -------------- | :-: | :--------: |
| **Tiếng Việt** | vi  |  Mặc định  |
| **Tiếng Anh**  | en  |   Hỗ trợ   |

#### Nội dung đa ngôn ngữ

| Loại nội dung            | Việt | Anh |
| ------------------------ | :--: | :-: |
| Banner                   |  ✅  | ✅  |
| Trang tĩnh (Policy, FAQ) |  ✅  | ✅  |
| Thông điệp, lời chào     |  ✅  | ✅  |
| Menu điều hướng          |  ✅  | ✅  |
| Thông báo hệ thống       |  ✅  | ✅  |

---

### 4.8. Product Catalog cho Quảng cáo

#### Catalog Export

Mango CMS hỗ trợ xuất catalog sản phẩm cho quảng cáo Google/Facebook:

| Loại Catalog          | Mô tả               | Sử dụng                               |
| --------------------- | ------------------- | ------------------------------------- |
| **Deep Link Catalog** | Link dẫn về app     | Quảng cáo Facebook/Google trên mobile |
| **Web Link Catalog**  | Link dẫn về website | Quảng cáo Google Shopping             |

#### Dữ liệu Catalog

| Trường       | Mô tả             |
| ------------ | ----------------- |
| Product ID   | Mã sản phẩm       |
| Title        | Tên sản phẩm      |
| Description  | Mô tả             |
| Image URL    | Link hình ảnh     |
| Image URL    | Link hình ảnh 1   |
| Price        | Giá bán           |
| Sale Price   | Giá khuyến mãi    |
| Deep Link    | Link mở app       |
| Web Link     | Link website      |
| Availability | Còn hàng/Hết hàng |

---

## 5. ĐIỂM MẠNH CỦA MANGO CMS

### 5.1. Lợi ích cho đội Marketing

| Lợi ích              | Mô tả                                                |
| -------------------- | ---------------------------------------------------- |
| **Tự chủ nội dung**  | Cập nhật banner, chương trình mà không cần developer |
| **Lên lịch tự động** | Đặt sẵn banner, tự động bật/tắt đúng giờ             |
| **A/B Testing**      | Thử nghiệm nhiều phiên bản banner                    |
| **Analytics**        | Theo dõi lượt xem, click trên banner                 |
| **Cá nhân hóa**      | Target content theo nhóm khách hàng                  |

### 5.2. Lợi ích cho đội Kỹ thuật

| Lợi ích               | Mô tả                                              |
| --------------------- | -------------------------------------------------- |
| **Giảm tải Backend**  | Nội dung tĩnh cache trên CMS, không query database |
| **Tách biệt concern** | Nội dung và logic nghiệp vụ tách riêng             |
| **Dễ scale**          | CMS scale độc lập với Backend                      |
| **API nhất quán**     | Frontend chỉ cần gọi Mango CMS                     |

### 5.3. Lợi ích cho Khách hàng

| Lợi ích                  | Mô tả                                |
| ------------------------ | ------------------------------------ |
| **Tải trang nhanh**      | Nội dung được cache, trả về nhanh    |
| **Nội dung cá nhân hóa** | Xem ưu đãi phù hợp với mình          |
| **Đa ngôn ngữ**          | Sử dụng app bằng ngôn ngữ quen thuộc |
| **Thông tin chính xác**  | Nội dung được cập nhật kịp thời      |

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
│                    (Trung tâm quản lý nội dung)                             │
│                                                                             │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐            │
│  │   Banner    │ │ Trang tĩnh  │ │    FAQ      │ │ Flash Sale  │            │
│  │  (Primary)  │ │  (Primary)  │ │  (Primary)  │ │  (Display)  │            │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘            │
│                                                                             │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐            │
│  │   Menu      │ │ Đa ngôn ngữ │ │   Catalog   │ │ Cá nhân hóa │            │
│  │  (Primary)  │ │  (Primary)  │ │  (Primary)  │ │  (Primary)  │            │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘            │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    ĐIỀU PHỐI DỮ LIỆU                                │    │
│  │         (Gọi Backend khi cần thông tin user/sản phẩm)               │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
                │                                       │
                ▼                                       ▼
┌────────────────────────────────┐      ┌────────────────────────────────────┐
│       BACKEND LOYALTY          │      │        BACKEND LS RETAIL           │
│                                │      │                                    │
│  • Thông tin user (tên, hạng)  │      │  • Sản phẩm đề xuất                │
│  • Điểm, số dư                 │      │  • Đơn hàng gần đây                │
│  • Chương trình loyalty        │      │  • Quy tắc Flash Sale              │
└────────────────────────────────┘      └────────────────────────────────────┘
```

### 6.2. Tóm tắt vai trò Mango CMS

| Vai trò              | Mô tả                                   |
| -------------------- | --------------------------------------- |
| **Quản lý nội dung** | Toàn bộ banner, trang tĩnh, FAQ         |
| **Điều phối**        | Gộp dữ liệu từ nhiều nguồn cho Frontend |
| **Cá nhân hóa**      | Target content theo nhóm khách hàng     |
| **Đa ngôn ngữ**      | Quản lý nội dung nhiều ngôn ngữ         |
| **Performance**      | Cache nội dung, giảm tải Backend        |
| **Marketing**        | Hỗ trợ đội marketing tự chủ nội dung    |

---

_Tài liệu này mô tả vai trò của Mango CMS trong Module 11 - Trang chủ & Content của hệ thống TH Milk App._
