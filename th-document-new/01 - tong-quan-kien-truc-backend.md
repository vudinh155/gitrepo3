# TỔNG QUAN KIẾN TRÚC BACKEND

## 3 Thành Phần Chính

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              FRONTEND                                       │
│            Web (Next.js)  │  Mobile (React Native / Expo)                   │
└────────────────────────────────────┬────────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                          MANGO CMS (Middleware)                             │
│  • Điều phối API thống nhất cho Frontend                                    │
│  • Quản lý nội dung App/Web (thay thế Magento)                              │
│  • Cache & Session management                                               │
│  • Push Notification                                                        │
│  • Content Management (Banner, Flash Sale, Static Pages)                    │
│  Framework: Node.js + Fastify | Database: MongoDB                           │
└─────────────────┬───────────────────────────────────┬───────────────────────┘
                  │                                   │
                  ▼                                   ▼
┌─────────────────────────────────────┐ ┌─────────────────────────────────────┐
│        BACKEND LOYALTY              │ │        BACKEND LS RETAIL            │
│        (TH Milk có sẵn)             │ │        (TH Milk có sẵn)             │
│                                     │ │                                     │
│  • Quản lý khách hàng               │ │  • Quản lý sản phẩm                 │
│  • Tích điểm thành viên             │ │  • Quản lý đơn hàng                 │
│  • Hạng thành viên                  │ │  • Quản lý tồn kho                  │
│  • Đổi thưởng / Kho quà             │ │  • Quản lý cửa hàng                 │
│  • Campaign Loyalty                 │ │  • Thanh toán                       │
│  • Hoa đất (điểm thưởng)            │ │  • Gift Card                        │
└─────────────────────────────────────┘ └─────────────────────────────────────┘
```

---

## Chi Tiết Từng Hệ Thống

### 1. Backend Loyalty (Hệ thống có sẵn)

**Chức năng chính:** Quản lý khách hàng và chương trình tích điểm thành viên

| Chức Năng | Mô Tả |
|-----------|-------|
| **Customer Management** | Thông tin khách hàng, profile, authentication |
| **Membership Tier** | Hạng thành viên, điều kiện nâng/giữ hạng |
| **Points Management** | Hoa đất, tích điểm, trừ điểm, FIFO |
| **Rewards & Gifts** | Kho quà, đổi thưởng, voucher |
| **Campaign** | Chương trình loyalty, vòng quay, tích lũy |
| **Transaction History** | Lịch sử điểm, lịch sử hạng thành viên |

**API Integration:** Mango CMS gọi API Loyalty để xử lý nghiệp vụ khách hàng

---

### 2. Backend LS Retail (Hệ thống có sẵn)

**Chức năng chính:** Quản lý sản phẩm và các vấn đề liên quan đến bán hàng

| Chức Năng | Mô Tả |
|-----------|-------|
| **Product Management** | Danh mục, thông tin sản phẩm, giá |
| **Inventory** | Tồn kho theo cửa hàng, real-time |
| **Order Management** | Tạo đơn, xử lý đơn, trạng thái đơn |
| **Store Management** | Thông tin cửa hàng, vị trí, giờ mở cửa |
| **Payment Processing** | Xử lý thanh toán, cổng thanh toán |
| **Gift Card** | Kích hoạt, nạp tiền, sử dụng gift card |
| **Pricing & Promotion** | Giá khuyến mãi, flash sale rules |

**API Integration:** Mango CMS gọi API LS Retail để xử lý nghiệp vụ bán hàng

---

### 3. Mango CMS (Middleware - Xây dựng mới)

**Chức năng chính:**
- Thay thế Magento (module bán hàng online cũ)
- Quản lý nội dung cho App và Web
- Layer trung gian điều phối giữa Frontend và 2 Backend

| Chức Năng | Mô Tả |
|-----------|-------|
| **API Orchestration** | Gộp nhiều API call, chuẩn hóa response |
| **Content Management** | Banner, static pages, FAQ, policy |
| **Session Management** | JWT, refresh token, device management |
| **Push Notification** | Firebase, in-app notification |
| **Cache Layer** | Redis cache cho product, content |
| **Search** | Elasticsearch cho product search |
| **Media Management** | Upload, optimize images |
| **Flash Sale Management** | Cấu hình khung giờ, sản phẩm flash sale |

**Tech Stack:**
- Framework: Node.js + Fastify
- Database: MongoDB
- Cache: Redis
- Search: Elasticsearch (optional)

---

## Nguyên Tắc Phân Chia

### Data Ownership

| Data Type | Owner | Note |
|-----------|-------|------|
| Customer Profile | Loyalty | Source of truth cho thông tin KH |
| Membership Points | Loyalty | Hoa đất, điểm xét hạng |
| Product Info | LS Retail | Sản phẩm, giá, tồn kho |
| Order Data | LS Retail | Đơn hàng, thanh toán |
| Gift Card | LS Retail | Số dư, giao dịch thẻ |
| Content | Mango CMS | Banner, page content |
| Session | Mango CMS | Login state, token |
| Notification | Mango CMS | Push, in-app messages |

### API Flow Pattern

```
Frontend Request
      │
      ▼
┌─────────────┐
│  Mango CMS  │ ← Validate, transform, cache check
└──────┬──────┘
       │
       ├──────────────┬──────────────┐
       ▼              ▼              ▼
   Loyalty API    LS Retail API   MongoDB
       │              │              │
       └──────────────┴──────────────┘
                      │
                      ▼
              Aggregate Response
                      │
                      ▼
              Frontend Response
```


## Lưu Ý Quan Trọng

1. **Mango CMS KHÔNG lưu trữ dữ liệu nghiệp vụ chính**
   - Không lưu customer data (chỉ cache)
   - Không lưu order data (chỉ forward)
   - Chỉ lưu: content, notification, session

2. **Loyalty và LS Retail là Source of Truth**
   - Mọi thay đổi dữ liệu phải thông qua API của hệ thống tương ứng
   - Mango CMS chỉ điều phối, không modify trực tiếp

3. **Cache Strategy**
   - Product info: Cache 5-15 phút
   - Customer points: Cache 1-5 phút
   - Content/Banner: Cache 30-60 phút
   - Invalidate khi có thay đổi

---

*Xem chi tiết phân chia chức năng trong từng file module orchestrator*
