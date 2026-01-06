# LÝ DO CHỌN MANGOCMS THAY VÌ MAGENTO

## TÓM TẮT EXECUTIVE

Sau khi phân tích kỹ lưỡng kiến trúc hệ thống, yêu cầu nghiệp vụ và các backend đã có sẵn tại TH Milk, MangoAds đề xuất xây dựng **MangoCMS** (Node.js/MongoDB) làm middleware layer thay vì sử dụng **Magento** vì các lý do sau:

1. **Tránh trùng lặp chức năng** với Backend Loyalty và LS Retail đã vận hành ổn định
2. **Không cần thiết phải dùng nền tảng E-commerce nặng** khi chỉ có dưới 1000 SKU
3. **Khả năng scale và microservice** vượt trội của Node.js so với Magento
4. **Chi phí phát triển và vận hành thấp hơn** khi chỉ cần một layer điều phối nhẹ
5. **Tốc độ phát triển nhanh** và dễ dàng tùy chỉnh theo yêu cầu đặc thù

---

## 1. BỐI CẢNH KIẾN TRÚC HỆ THỐNG

### 1.1. Hệ Thống Backend Đã Có Sẵn

TH Milk đang vận hành 2 hệ thống backend chuyên nghiệp:


| Hệ Thống            | Chức Năng Chính                                                                         | Trạng Thái                  |
| ----------------------- | -------------------------------------------------------------------------------------------- | ------------------------------- |
| **Backend Loyalty**   | Quản lý khách hàng, tích điểm, hạng thành viên, đổi thưởng, campaign loyalty | Đang hoạt động ổn định |
| **Backend LS Retail** | Quản lý sản phẩm, đơn hàng, tồn kho, cửa hàng, thanh toán, gift card            | Đang hoạt động ổn định |

### 1.2. Nhu Cầu Thực Tế

Yêu cầu của dự án **KHÔNG PHẢI** xây dựng một hệ thống E-commerce hoàn chỉnh từ đầu, mà là:

- ✅ Tạo API layer thống nhất cho Frontend (Web + Mobile App)
- ✅ Quản lý nội dung hiển thị (banner, flash sale, static pages)
- ✅ Điều phối các API call giữa Frontend và 2 Backend có sẵn
- ✅ Xử lý session, cache, push notification
- ❌ **KHÔNG** cần xây dựng lại module quản lý khách hàng (đã có ở Loyalty)
- ❌ **KHÔNG** cần xây dựng lại module sản phẩm/đơn hàng (đã có ở LS Retail)

---

## 2. TẠI SAO KHÔNG DÙNG MAGENTO

### 2.1. Trùng Lặp Chức Năng Nghiêm Trọng

Magento là nền tảng E-commerce đầy đủ, bao gồm:

```
╔═══════════════════════════════════════════════════════════════╗
║                     MAGENTO FULL STACK                        ║
╠═══════════════════════════════════════════════════════════════╣
║  • Customer Management      ← ❌ ĐÃ CÓ Ở LOYALTY              ║
║  • Product Management       ← ❌ ĐÃ CÓ Ở LS RETAIL            ║
║  • Order Management         ← ❌ ĐÃ CÓ Ở LS RETAIL            ║
║  • Inventory Management     ← ❌ ĐÃ CÓ Ở LS RETAIL            ║
║  • Loyalty Points           ← ❌ ĐÃ CÓ Ở LOYALTY              ║
║  • Payment Processing       ← ❌ ĐÃ CÓ Ở LS RETAIL            ║
║  • Membership Tier          ← ❌ ĐÃ CÓ Ở LOYALTY              ║
╚═══════════════════════════════════════════════════════════════╝
```

**Vấn đề:**

- **Các chức năng cốt lõi của Magento đã tồn tại sẵn** trong Backend Loyalty và LS Retail
- Nếu dùng Magento, ta sẽ có **2 hệ thống quản lý khách hàng** (Loyalty vs Magento Customer)
- Sẽ có **2 hệ thống quản lý sản phẩm/đơn hàng** (LS Retail vs Magento)
- Phải **đồng bộ dữ liệu liên tục** giữa 3 hệ thống → tăng complexity
- **Data inconsistency risk** cao khi có nhiều source of truth

### 2.2. Magento Thiếu Module Quan Trọng Nhất

Chức năng **quan trọng nhất** của hệ thống TH Milk là:

- ✅ **Quản lý thành viên với logic phức tạp** (hạng thành viên, điều kiện nâng/giữ hạng)
- ✅ **Hoa đất (điểm thưởng với FIFO logic)**
- ✅ **Campaign Loyalty đa dạng** (vòng quay, tích lũy, nhiệm vụ)
- ✅ **Đổi thưởng/Kho quà phức tạp**

Magento **KHÔNG** có sẵn các module này. Để có, phải:

1. **Mua plugin thương mại** (Aheadworks Points & Rewards, Amasty Loyalty, etc.)

   - Chi phí license cao
   - Khó tùy chỉnh theo yêu cầu đặc thù
   - Phụ thuộc vào nhà cung cấp
2. **Phát triển custom module trên Magento**

   - Thời gian phát triển dài
   - Phải học kiến trúc Magento phức tạp
   - Không tận dụng được **Backend Loyalty đã có sẵn và đang chạy tốt**

**Kết luận:** Nếu dùng Magento cho tích điểm, ta đang **"cơi nới" Magento** để thêm chức năng, thay vì tận dụng thế mạnh truyền thống của nó (E-commerce với nhiều SKU, multi-store, B2B/B2C phức tạp).

### 2.3. Quy Mô Sản Phẩm Không Phù Hợp

**Thực tế TH Milk:**

- Số lượng SKU: **< 1000 sản phẩm**
- Danh mục: Sữa tươi, sữa chua, nước ép, thực phẩm sạch (có giới hạn)
- Không phải marketplace, không có hàng nghìn vendors

✅ **Magento phù hợp khi:**

- Có **10,000+ SKU** với thuộc tính phức tạp
- Multi-store, multi-warehouse, multi-currency
- B2B với quote, negotiation, purchase order
- Cần extension ecosystem lớn

❌ **TH Milk không cần:**

- Product catalog engine nặng của Magento
- Complex pricing rules (đã có ở LS Retail)
- Multi-store setup (chỉ 1 brand TH Milk)

**Bất kỳ framework hiện đại nào** (Node.js, Django, Laravel) đều có thể xử lý < 1000 SKU một cách nhẹ nhàng mà **không bị over-engineering**.

### 2.4. Khả Năng Scale và Microservices

**Magento (PHP monolithic):**

```
┌─────────────────────────────────────────────┐
│         MAGENTO MONOLITHIC APP              │
│  • Difficult to scale horizontally          │
│  • Resource-intensive (PHP-FPM, MySQL)      │
│  • Hard to split into microservices         │
│  • Require powerful servers                 │
└─────────────────────────────────────────────┘
       Scale by vertical (bigger server)
```

**Node.js + MangoCMS:**

```
┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐
│ MangoCMS │  │ MangoCMS │  │ MangoCMS │  │ MangoCMS │
│ Instance │  │ Instance │  │ Instance │  │ Instance │
└──────────┘  └──────────┘  └──────────┘  └──────────┘
      Easy horizontal scaling with load balancer
            Lightweight containers
         Microservices-ready architecture
```

**Ưu điểm Node.js:**

- ✅ **Horizontal scaling dễ dàng** (spawn nhiều instance nhẹ)
- ✅ **Non-blocking I/O** → xử lý concurrent requests tốt hơn
- ✅ **Containerization** (Docker) nhẹ hơn nhiều
- ✅ **Microservices migration** dễ dàng khi cần tách module
- ✅ **Cloud-native** (AWS Lambda, Google Cloud Run ready)

### 2.5. Chi Phí Vận Hành


| Yếu Tố                | Magento                               | MangoCMS (Node.js)                 |
| ------------------------- | --------------------------------------- | ------------------------------------ |
| **Server Requirements** | 8GB+ RAM, 4+ CPU cores                | 2GB RAM, 1-2 CPU cores             |
| **Database**            | MySQL (heavy queries)                 | MongoDB (lightweight)              |
| **Caching**             | Varnish + Redis                       | Redis only                         |
| **License**             | Open Source (nhưng cần plugins $$$) | Free                               |
| **Dev Team**            | Cần Magento specialists              | JavaScript developers (phổ biến) |
| **Deployment**          | Phức tạp, downtime cao              | CI/CD dễ dàng, zero-downtime     |
| **Monthly Hosting**     | $200-500+/month                       | $50-150/month                      |

**Kết luận:** Chi phí vận hành Magento cao hơn **3-5 lần** so với Node.js stack.

### 2.6. Độ Phức Tạp Phát Triển

**Magento:**

- ❌ Customization khó (phải override nhiều layer)
- ❌ Debugging khó khăn
- ❌ Build time chậm (code generation)
- ❌ Testing phức tạp

**MangoCMS (Node.js):**

- ✅ Framework đơn giản (Fastify, Express)
- ✅ Dễ tùy chỉnh (JavaScript linh hoạt)
- ✅ Debugging dễ dàng
- ✅ Hot reload, fast development
- ✅ Testing frameworks phong phú (Jest, Mocha)

---

## 3. TẠI SAO MANGOCMS LÀ GIẢI PHÁP TỐI ƯU

### 3.1. Đúng Vai Trò: Middleware Layer Nhẹ

MangoCMS được thiết kế là **orchestration layer**, không phải backend nghiệp vụ:

```
┌─────────────────────────────────────────────────────────┐
│                     FRONTEND                            │
│              Web (Next.js) + Mobile App                 │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│               MANGOCMS (Lightweight)                    │
│  ┌───────────────────────────────────────────────────┐  │
│  │ • API Orchestration    • Cache Management         │  │
│  │ • Content Management   • Push Notification        │  │
│  │ • Session Management   • Media Upload             │  │
│  └───────────────────────────────────────────────────┘  │
│        Node.js + Fastify + MongoDB + Redis              │
└───────────────────┬─────────────────┬───────────────────┘
                    │                 │
        ┌───────────┴────────┐        │
        ▼                    ▼        ▼
┌────────────────┐  ┌─────────────────────────┐
│ Backend        │  │ Backend LS Retail       │
│ LOYALTY        │  │                         │
│ (Source of     │  │ (Source of truth for    │
│  truth for     │  │  products, orders,      │
│  customers,    │  │  inventory, gift card)  │
│  points, tier) │  │                         │
└────────────────┘  └─────────────────────────┘
```

**Vai trò của MangoCMS:**

- ✅ **Không lưu trữ dữ liệu nghiệp vụ chính** (chỉ cache)
- ✅ **Không thay thế** Backend Loyalty/LS Retail
- ✅ **Chỉ điều phối**, không modify trực tiếp
- ✅ **Layer nhẹ**, không phải "ngôi nhà lớn" như Magento

**So sánh:**

- ❌ Magento = **Ngôi nhà đầy đủ** (foundation, walls, roof, furniture)
- ✅ MangoCMS = **Bộ điều phối giao thông** (traffic controller)

### 3.2. Tránh "Double Layer" Nặng Nề

**Nếu dùng Magento:**

```
Frontend
   │
   ▼
╔═══════════════════════════════════════╗
║        MAGENTO (HEAVY LAYER)          ║  ← Layer 1 (nặng)
║  • Customer Management (unused)       ║
║  • Product Management (unused)        ║
║  • Order Management (unused)          ║
║  • Need powerful servers              ║
╚═══════════════════════════════════════╝
   │                    │
   ▼                    ▼
┌──────────────┐  ┌──────────────┐
│ Loyalty      │  │ LS Retail    │  ← Layer 2 (vẫn cần)
│ (Still need) │  │ (Still need) │
└──────────────┘  └──────────────┘

❌ Phải quản lý 3 hệ thống nặng
❌ Sync data giữa 3 hệ thống
❌ Chi phí server gấp 3 lần
```

**Với MangoCMS:**

```
Frontend
   │
   ▼
╔═══════════════════════════════════════╗
║      MANGOCMS (LIGHTWEIGHT LAYER)     ║  ← Layer mỏng
║  • API orchestration only             ║
║  • Content + cache + session          ║
║  • Low resource usage                 ║
╚═══════════════════════════════════════╝
   │                    │
   ▼                    ▼
┌──────────────┐  ┌──────────────┐
│ Loyalty      │  │ LS Retail    │  ← Backend chuyên nghiệp
│ (Existing)   │  │ (Existing)   │
└──────────────┘  └──────────────┘

✅ Chỉ thêm 1 layer mỏng
✅ Không duplicate chức năng
✅ Chi phí server tối ưu
```

### 3.3. Tận Dụng Backend Chuyên Nghiệp Đã Có

Backend Loyalty và LS Retail là các hệ thống:

- ✅ **Đã được đầu tư** nhiều thời gian và chi phí
- ✅ **Đang vận hành ổn định** với dữ liệu thực tế
- ✅ **Có logic nghiệp vụ phức tạp** đã được validate
- ✅ **Team đã quen** với cách vận hành

**Thay vì:**

- ❌ Bỏ đi và dùng Magento Customer/Order module
- ❌ Migrate dữ liệu sang Magento (rủi ro cao)
- ❌ Train team học Magento từ đầu

**MangoCMS cho phép:**

- ✅ **Giữ nguyên** Backend Loyalty/LS Retail
- ✅ **Tận dụng** API đã có sẵn
- ✅ **Không cần migration** dữ liệu
- ✅ **Team tiếp tục** làm việc với hệ thống quen thuộc

### 3.4. Linh Hoạt và Tốc Độ Phát Triển

**Yêu cầu đặc thù của TH Milk:**

- Campaign loyalty theo mùa (Tết, Trung thu, Hè)
- Flash sale theo khung giờ đặc biệt
- Tích hợp với hệ thống nội bộ (POS, ERP)
- Tùy chỉnh flow đổi thưởng theo chính sách

**MangoCMS (Node.js):**

- ✅ Dễ dàng thêm/sửa endpoint mới
- ✅ Tích hợp API bất kỳ (REST, GraphQL, SOAP)
- ✅ Custom business logic nhanh chóng
- ✅ Deploy feature mới trong vài giờ

**Magento:**

- ❌ Phải tạo module theo chuẩn Magento
- ❌ Override nhiều layer (plugin, observer, preference)
- ❌ Deploy phức tạp (setup:upgrade, cache:flush, compile)
- ❌ Feature mới mất vài ngày đến vài tuần

### 3.5. Tech Stack Hiện Đại

**MangoCMS Stack:**

```
┌─────────────────────────────────────────────┐
│  Framework: Fastify (Node.js)               │  ← Fastest Node.js framework
│  Database: MongoDB                          │  ← Flexible schema, fast read
│  Cache: Redis                               │  ← Sub-millisecond latency
│  Search: Elasticsearch (optional)           │  ← Powerful search
│  Queue: Bull (Redis-based)                  │  ← Background jobs
│  Deployment: Docker + K8s                   │  ← Cloud-native
└─────────────────────────────────────────────┘
```

**Ưu điểm:**

- ✅ **Microservices-ready** (có thể tách thành nhiều service sau)
- ✅ **Cloud-native** (AWS, GCP, Azure đều hỗ trợ tốt)
- ✅ **Containerization** dễ dàng
- ✅ **CI/CD pipeline** đơn giản
- ✅ **Monitoring & logging** (Prometheus, Grafana, ELK stack)

---

## 4. SO SÁNH CHI TIẾT

### 4.1. Bảng So Sánh Tổng Quan


| Tiêu Chí                      | Magento                  | MangoCMS (Node.js)     | Ghi Chú                        |
| --------------------------------- | -------------------------- | ------------------------ | --------------------------------- |
| **Vai trò**                    | Full E-commerce platform | Lightweight middleware | MangoCMS đúng vai trò hơn   |
| **Trùng lặp chức năng**     | ❌ Cao                   | ✅ Không              | Magento overlap với Loyalty/LS |
| **Số SKU phù hợp**           | 10,000+                  | < 5,000                | TH Milk có < 1000 SKU          |
| **Tận dụng Backend có sẵn** | ❌ Không                | ✅ Có                 | MangoCMS gọi API Loyalty/LS    |
| **Chi phí server**             | $200-500+/month          | $50-150/month          | MangoCMS tiết kiệm 3-5x       |
| **Chi phí phát triển**       | Cao                      | Thấp                  | Node.js developers phổ biến   |
| **Thời gian phát triển**     | 3-6 tháng               | 1-3 tháng             | MangoCMS nhanh hơn 2x          |
| **Horizontal scaling**          | Khó                     | Dễ                    | Node.js scale tốt hơn         |
| **Microservices**               | Khó                     | Dễ                    | MangoCMS dễ tách module       |
| **Deployment**                  | Phức tạp               | Đơn giản            | Docker + K8s ready              |
| **Customization**               | Khó                     | Dễ                    | JavaScript linh hoạt           |
| **Learning curve**              | Dốc                     | Thoải                 | Magento phức tạp hơn nhiều  |
| **Community**                   | Lớn (E-commerce)        | Lớn (Node.js)         | Cả 2 đều có support tốt    |

### 4.2. Chi Phí Tổng Thể (12 tháng)


| Hạng Mục             | Magento     | MangoCMS    | Tiết Kiệm |
| ------------------------ | ------------- | ------------- | ------------- |
| **Development**        | $50,000     | $25,000     | $25,000     |
| **Hosting**            | $4,800/yr   | $1,200/yr   | $3,600      |
| **Plugins/Extensions** | $3,000      | $0          | $3,000      |
| **Maintenance**        | $12,000/yr  | $6,000/yr   | $6,000      |
| **Team Training**      | $5,000      | $1,000      | $4,000      |
| **TỔNG (Year 1)**     | **$74,800** | **$33,200** | **$41,600** |

**Tiết kiệm:** **$41,600 (~56%)** trong năm đầu tiên

---

## 5. KẾT LUẬN VÀ ĐỀ XUẤT

### 5.1. Tóm Tắt Lý Do Chính


| #     | Lý Do                                                | Tác Động                               |
| ------- | ------------------------------------------------------- | ------------------------------------------- |
| **1** | **Tránh trùng lặp** với Backend Loyalty/LS Retail | Không phải quản lý 3 hệ thống nặng |
| **2** | **Quy mô SKU nhỏ** (< 1000) không cần Magento     | Không bị over-engineering               |
| **3** | **Magento thiếu module loyalty phức tạp**          | Phải mua plugin hoặc dev custom         |
| **4** | **Chi phí tiết kiệm 3-5x**                         | Giảm $40,000+ năm đầu                 |
| **5** | **Scale tốt hơn** với Node.js                      | Dễ horizontal scale + microservices      |
| **6** | **Phát triển nhanh hơn 2x**                        | Time to market ngắn hơn                 |
| **7** | **MangoCMS là layer mỏng**                          | Đúng vai trò orchestration             |

### 5.2. Triết Lý Thiết Kế

> **"Don't build a house when you only need a bridge"**
>
> Magento là một "ngôi nhà đầy đủ" (full E-commerce platform) với rất nhiều phòng (modules) mà TH Milk không cần sử dụng. Thay vào đó, MangoCMS là một "cây cầu" (middleware) kết nối Frontend với các Backend chuyên nghiệp đã có sẵn.

### 5.3. Đề Xuất Cuối Cùng

MangoAds đề xuất:

✅ **Xây dựng MangoCMS** (Node.js + MongoDB) làm middleware layer

**Lý do:**

1. Đúng vai trò (orchestration, không phải full backend)
2. Tận dụng Backend Loyalty/LS Retail đã ổn định
3. Tiết kiệm chi phí 50%+
4. Phát triển nhanh hơn 2x
5. Scale dễ dàng hơn
6. Phù hợp với quy mô SKU nhỏ

❌ **Không nên dùng Magento** vì:

1. Over-engineering (quá nặng cho use case này)
2. Trùng lặp chức năng với backend có sẵn
3. Chi phí cao (license, hosting, dev, maintenance)
4. Không tận dụng được thế mạnh E-commerce của Magento
5. Khó scale và khó chuyển sang microservices

### 5.4. Roadmap Đề Xuất

**Phase 1: MangoCMS Core (2-3 tháng)**

- API orchestration layer
- Content management (banner, pages)
- Session & authentication
- Cache layer (Redis)

**Phase 2: Advanced Features (1-2 tháng)**

- Push notification
- Flash sale management
- Elasticsearch integration
- Advanced caching strategies

**Phase 3: Optimization & Scale (ongoing)**

- Performance tuning
- Horizontal scaling setup
- Monitoring & alerting
- Microservices migration (nếu cần)

---

## PHỤ LỤC: CÂU HỎI THƯỜNG GẶP

### Q1: Magento có community lớn, tại sao không tận dụng?

**A:** Community Magento lớn trong lĩnh vực **E-commerce truyền thống** (nhiều SKU, catalog phức tạp, B2B/B2C). Nhưng với use case của TH Milk (loyalty-centric, ít SKU, đã có backend chuyên nghiệp), các module và extension của Magento **không phù hợp** và vẫn phải custom nhiều. Node.js community cũng rất lớn và có nhiều library cho API orchestration.

### Q2: Nếu sau này mở rộng nhiều sản phẩm thì sao?

**A:** Ngay cả khi mở rộng lên **5,000-10,000 SKU**, MangoCMS với MongoDB vẫn xử lý tốt. Nếu thực sự cần chuyển sang nền tảng E-commerce nặng hơn (khó xảy ra với TH Milk), kiến trúc microservices cho phép **chỉ thay thế product module** mà không ảnh hưởng toàn bộ hệ thống.

### Q3: Magento có sẵn nhiều tính năng, tiết kiệm thời gian phát triển?

**A:** **Không đúng** với TH Milk vì:

- Các tính năng cốt lõi của Magento (customer, product, order) **đã có sẵn** ở Loyalty/LS Retail
- Tính năng **quan trọng nhất** (loyalty phức tạp) Magento **không có** → vẫn phải dev custom
- Thời gian "customize Magento để fit" lâu hơn "xây MangoCMS từ đầu theo đúng yêu cầu"

### Q4: Rủi ro khi xây dựng custom solution?

**A:** MangoCMS **không phải custom solution hoàn toàn**:

- Sử dụng **proven stack** (Node.js, MongoDB, Redis) đã được hàng triệu ứng dụng sử dụng
- Kiến trúc **đơn giản, dễ maintain** (chỉ là middleware, không phải core business logic)
- **Backend Loyalty/LS Retail mới là core**, MangoCMS chỉ điều phối
- Nếu cần, có thể thuê bất kỳ Node.js developer nào để maintain

Rủi ro khi dùng Magento **cao hơn** vì:

- Phải customize nhiều → technical debt cao
- Phụ thuộc vào Magento specialists (đắt và khó tìm)
- Khó migrate ra khỏi Magento sau này

---

**Tài liệu này được soạn bởi MangoAds**
*Phiên bản: 1.0 | Ngày: 2026-01-05*
