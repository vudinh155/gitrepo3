# TỔNG QUAN & KIẾN TRÚC MODULE ĐĂNG KÝ - ĐĂNG NHẬP

> **Phạm vi:** Phát triển tính năng đăng ký, đăng nhập, xác thực cho TH eLIFE App/Web
> **Tham chiếu SOW:** B.1 - Module 1: Đăng ký – Đăng nhập

---

## 1. Giới Thiệu Module

Module Đăng ký - Đăng nhập là nền tảng xác thực người dùng cho hệ thống TH eLIFE. Module này đảm bảo:

- Khách hàng có thể tạo tài khoản thành viên mới
- Xác thực danh tính qua nhiều phương thức (mật khẩu, OTP, sinh trắc học)
- Quản lý phiên đăng nhập an toàn
- Khôi phục và thay đổi mật khẩu khi cần

---

## 2. Các Chức Năng Chính

| STT | Chức Năng                   | Mô Tả                                          |
| --- | --------------------------- | ---------------------------------------------- |
| 1   | **Đăng ký tài khoản**       | Tạo tài khoản thành viên mới với số điện thoại |
| 2   | **Đăng nhập**               | Xác thực bằng Email/SĐT + Mật khẩu             |
| 3   | **Xác thực OTP**            | Xác minh danh tính qua mã OTP gửi SMS/Email    |
| 4   | **Quên mật khẩu**           | Khôi phục mật khẩu thông qua OTP               |
| 5   | **Đổi mật khẩu**            | Thay đổi mật khẩu khi đang đăng nhập           |
| 6   | **Sinh trắc học (Face ID)** | Đăng nhập nhanh bằng Face ID/Touch ID          |
| 7   | **Đăng xuất**               | Kết thúc phiên đăng nhập                       |

---

## 3. Kiến Trúc Hệ Thống

### 3.1. Sơ Đồ Tổng Quan

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           FRONTEND (User Interface)                     │
│                  TH eLIFE App (Mobile) / TH eLIFE Web                   │
│                                                                         │
│  • Màn hình Splash                    • Xác thực Face ID/Touch ID       │
│  • Form đăng ký/đăng nhập             • Nhập OTP                        │
│  • Form quên mật khẩu                 • Cài đặt bảo mật                 │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                          MANGO CMS (Middleware)                         │
│                                                                         │
│  Vai trò: Điều phối xác thực, quản lý session, gửi OTP                  │
│                                                                         │
│  • Validate dữ liệu đầu vào (format SĐT, email, password)               │
│  • Tạo và xác thực OTP                                                  │
│  • Quản lý JWT token & session                                          │
│  • Lưu trữ thông tin thiết bị                                           │
│  • Gửi OTP qua SMS/Zalo/Email                                           │
│  • Xử lý sinh trắc học (Face ID)                                        │
└──────────────┬─────────────────────────────────────┬────────────────────┘
               │                                     │
               ▼                                     ▼
┌──────────────────────────────────┐   ┌──────────────────────────────────┐
│      BACKEND LOYALTY (Primary)   │   │        SMS/ZNS GATEWAY           │
│                                  │   │                                  │
│  Vai trò: Quản lý tài khoản KH   │   │  Vai trò: Gửi OTP                │
│                                  │   │                                  │
│  • Lưu trữ thông tin khách hàng  │   │  • Gửi SMS qua nhà mạng          │
│  • Xác thực mật khẩu             │   │  • Gửi Zalo ZNS                  │
│  • Kiểm tra trùng lặp SĐT/Email  │   │  • Gửi Email                     │
│  • Quản lý hạng thành viên       │   │                                  │
│  • Lưu lịch sử đăng nhập         │   │                                  │
└──────────────────────────────────┘   └──────────────────────────────────┘
```

### 3.2. Giải Thích Luồng Dữ Liệu

1. **Khách hàng** thao tác trên TH eLIFE App hoặc Website
2. **Frontend** gửi request đến **Mango CMS**
3. **Mango CMS** validate dữ liệu và điều phối đến hệ thống phù hợp:
   - Các thao tác về tài khoản → **Backend Loyalty**
   - Gửi OTP → **SMS/ZNS Gateway**
4. **Backend Loyalty** xử lý nghiệp vụ tài khoản và trả kết quả
5. **Mango CMS** tạo session, tổng hợp kết quả và trả về Frontend

---

## 4. Phân Chia Trách Nhiệm Hệ Thống

### 4.1. Backend Loyalty - Hệ Thống Chủ Đạo (Primary System)

Backend Loyalty là **source of truth** (nguồn dữ liệu chính xác) cho thông tin khách hàng thành viên.

| Nhiệm Vụ                          | Chi Tiết                                       |
| --------------------------------- | ---------------------------------------------- |
| **Lưu trữ thông tin khách hàng**  | Họ tên, SĐT, email, ngày sinh, địa chỉ         |
| **Xác thực mật khẩu**             | Kiểm tra mật khẩu khi đăng nhập                |
| **Kiểm tra trùng lặp**            | Kiểm tra SĐT/Email đã tồn tại chưa khi đăng ký |
| **Tạo tài khoản mới**             | Tạo customer profile với hạng mặc định         |
| **Cập nhật mật khẩu**             | Lưu mật khẩu mới khi đổi/reset                 |
| **Quản lý hạng thành viên**       | Gán hạng mặc định khi đăng ký                  |
| **Kiểm tra trạng thái tài khoản** | Xác định tài khoản có bị khóa không            |
| **Xử lý mã giới thiệu**           | Validate và áp dụng referral code              |

**Dữ liệu Loyalty lưu trữ:**

- Customer Profile (thông tin cá nhân)
- Membership Tier (hạng thành viên)
- Password Hash (mật khẩu đã mã hóa)
- Referral Code (mã giới thiệu)
- Account Status (trạng thái tài khoản)

### 4.2. Mango CMS - Tầng Điều Phối (Middleware)

Mango CMS đóng vai trò **middleware**, xử lý các nghiệp vụ phụ trợ và điều phối.

| Nhiệm Vụ                | Chi Tiết                                          |
| ----------------------- | ------------------------------------------------- |
| **Validate dữ liệu**    | Kiểm tra format SĐT (+84), email, password policy |
| **Tạo và quản lý OTP**  | Generate OTP 6 số, lưu Redis với TTL 5 phút       |
| **Gửi OTP**             | Điều phối gửi qua SMS/Zalo/Email                  |
| **Quản lý session**     | Tạo JWT token, refresh token, device tracking     |
| **Rate limiting**       | Giới hạn số lần gọi API để chống brute force      |
| **Xử lý sinh trắc học** | Lưu cấu hình Face ID/Touch ID                     |
| **Push notification**   | Gửi thông báo đăng nhập từ thiết bị mới           |

**Dữ liệu Mango CMS lưu trữ:**

- Session Token (JWT, refresh token)
- OTP (tạm thời, Redis)
- Device Info (thông tin thiết bị)
- FCM Token (push notification)
- Biometric Config (cấu hình sinh trắc học)

### 4.3. Backend LS Retail

**LS Retail KHÔNG tham gia** trực tiếp vào module Đăng ký - Đăng nhập vì đây là nghiệp vụ quản lý khách hàng, thuộc phạm vi của Loyalty.

Tuy nhiên, sau khi đăng nhập thành công, thông tin khách hàng có thể được đồng bộ sang LS Retail nếu cần thiết cho các nghiệp vụ bán hàng.

---

## 5. Nguyên Tắc Thiết Kế

### 5.1. Data Ownership (Quyền Sở Hữu Dữ Liệu)

| Loại Dữ Liệu         | Hệ Thống Sở Hữu | Ghi Chú              |
| -------------------- | --------------- | -------------------- |
| Thông tin khách hàng | Loyalty         | Source of truth      |
| Mật khẩu (hash)      | Loyalty         | Mã hóa bcrypt/Argon2 |
| Hạng thành viên      | Loyalty         | Gán khi đăng ký      |
| Session token        | Mango CMS       | JWT + Refresh token  |
| OTP                  | Mango CMS       | Tạm thời, Redis      |
| Device info          | Mango CMS       | Theo dõi thiết bị    |
| Biometric config     | Mango CMS       | Face ID/Touch ID     |

### 5.2. Luồng Xử Lý Chung

```
Bước 1: Frontend gửi request → Mango CMS
Bước 2: Mango CMS validate dữ liệu đầu vào
Bước 3: Mango CMS gọi API Loyalty (nếu cần)
Bước 4: Loyalty xử lý nghiệp vụ tài khoản
Bước 5: Mango CMS tạo/cập nhật session
Bước 6: Mango CMS trả response cho Frontend
```

---

## 6. Tổng Quan Các Chức Năng

| STT | Chức Năng         |           Loyalty            |       Mango CMS        | SMS Gateway |
| --- | ----------------- | :--------------------------: | :--------------------: | :---------: |
| 1   | Đăng ký tài khoản |         Tạo customer         |     Điều phối, OTP     |   Gửi OTP   |
| 2   | Đăng nhập         |      Xác thực password       |      Tạo session       |      -      |
| 3   | Gửi/Xác thực OTP  |              -               |    Tạo & verify OTP    | Gửi SMS/ZNS |
| 4   | Quên mật khẩu     | Tìm account, update password |     OTP, điều phối     |   Gửi OTP   |
| 5   | Đổi mật khẩu      |       Update password        | Validate old password  |      -      |
| 6   | Face ID/Touch ID  |              -               | Lưu config, auto-login |      -      |
| 7   | Đăng xuất         |              -               |      Xóa session       |      -      |

---
# ĐĂNG KÝ TÀI KHOẢN & XÁC THỰC SINH TRẮC HỌC

> **Tham chiếu SOW:** B.1 - Module 1: Đăng ký – Đăng nhập
> **Bổ sung:** B.4 - Đăng nhập bằng sinh trắc học (Face ID)

---

## 1. Đăng Ký Tài Khoản Thành Viên

### 1.1. Tổng Quan

Khách hàng mới có thể đăng ký tài khoản TH eLIFE qua số điện thoại. Sau khi đăng ký, khách hàng được gán hạng thành viên Bạc và có thể bắt đầu mua sắm, tích điểm.

### 1.2. Phân Chia Nhiệm Vụ Các Hệ Thống

| Hệ Thống | Vai Trò | Nhiệm Vụ |
|----------|---------|----------|
| **Backend Loyalty** | Primary | Kiểm tra SĐT trùng, tạo tài khoản, gán hạng mặc định, xử lý mã giới thiệu |
| **Mango CMS** | Middleware | Validate dữ liệu, tạo và xác thực OTP, tạo session, gửi thông báo |
| **SMS Gateway** | Support | Gửi mã OTP qua SMS/Zalo |
| **LS Retail** | Không tham gia | - |

### 1.3. Luồng Đăng Ký

```
┌──────────────────────────────────────────────────────────────────┐
│  BƯỚC 1: Nhập số điện thoại                                      │
├──────────────────────────────────────────────────────────────────┤
│  • Khách hàng nhập SĐT (+84)                                     │
│  • Mango CMS validate format                                     │
│  • Gọi Loyalty kiểm tra SĐT đã tồn tại chưa                      │
│    → Nếu tồn tại: Thông báo, gợi ý đăng nhập                     │
│    → Nếu chưa: Tiếp tục bước 2                                   │
└──────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌──────────────────────────────────────────────────────────────────┐
│  BƯỚC 2: Xác thực OTP                                            │
├──────────────────────────────────────────────────────────────────┤
│  • Mango CMS tạo OTP 6 số (hiệu lực 5 phút)                      │
│  • Gửi OTP qua SMS Gateway                                       │
│  • Khách hàng nhập mã OTP                                        │
│  • Mango CMS xác thực OTP (tối đa 3 lần sai)                     │
└──────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌──────────────────────────────────────────────────────────────────┐
│  BƯỚC 3: Nhập thông tin & tạo tài khoản                          │
├──────────────────────────────────────────────────────────────────┤
│  • Khách nhập: Họ tên, Email, Mật khẩu, Mã giới thiệu (tùy chọn) │
│  • Mango CMS validate thông tin                                  │
│  • Gọi Loyalty tạo tài khoản:                                    │
│    - Lưu thông tin khách hàng                                    │
│    - Hash và lưu mật khẩu                                        │
│    - Gán hạng Bạc mặc định                                       │
│    - Xử lý referral bonus (nếu có mã giới thiệu)                 │
│  • Mango CMS tạo JWT session                                     │
│  • Tự động đăng nhập, chuyển đến trang chủ                       │
└──────────────────────────────────────────────────────────────────┘
```

### 1.4. Dữ Liệu Lưu Trữ

| Dữ Liệu | Hệ Thống Lưu | Ghi Chú |
|---------|--------------|---------|
| Thông tin cá nhân (tên, SĐT, email) | Loyalty | Source of truth |
| Mật khẩu (đã hash) | Loyalty | Mã hóa bcrypt |
| Hạng thành viên | Loyalty | Mặc định: Bạc |
| OTP | Mango CMS (Redis) | Tạm thời, 5 phút |
| Session token | Mango CMS | JWT + Refresh token |

---

## 2. Xác Thực Sinh Trắc Học (Face ID / Touch ID)

### 2.1. Tổng Quan

Đây là tính năng **bổ sung mới theo yêu cầu SOW B.4**, cho phép khách hàng đăng nhập nhanh bằng Face ID hoặc Touch ID thay vì nhập mật khẩu.

**Lưu ý quan trọng:** Sinh trắc học là phương thức xác thực bổ sung, hoạt động **hoàn toàn trên thiết bị (device-side)**, không lưu dữ liệu sinh trắc lên server.

### 2.2. Phân Chia Nhiệm Vụ Các Hệ Thống

| Hệ Thống | Vai Trò | Nhiệm Vụ |
|----------|---------|----------|
| **Frontend (App)** | Primary | Xử lý Face ID/Touch ID, lưu credentials mã hóa trên device |
| **Mango CMS** | Support | Lưu config sinh trắc học đã bật, xử lý auto-login |
| **Loyalty** | Không tham gia | - |
| **LS Retail** | Không tham gia | - |

### 2.3. Cơ Chế Hoạt Động

```
┌──────────────────────────────────────────────────────────────────┐
│  KÍCH HOẠT SINH TRẮC HỌC                                         │
├──────────────────────────────────────────────────────────────────┤
│  1. Khách hàng vào Cài đặt → Bảo mật → Bật Face ID/Touch ID      │
│  2. App yêu cầu xác nhận mật khẩu hiện tại                       │
│  3. App lưu credentials (mã hóa) vào Keychain/Keystore           │
│  4. Mango CMS lưu config: biometricEnabled = true                │
└──────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────┐
│  ĐĂNG NHẬP BẰNG SINH TRẮC HỌC                                    │
├──────────────────────────────────────────────────────────────────┤
│  1. Mở app → Hiển thị màn hình đăng nhập với option Face ID      │
│  2. Khách chọn "Đăng nhập bằng Face ID"                          │
│  3. App gọi API sinh trắc học của iOS/Android                    │
│  4. Nếu xác thực thành công:                                     │
│     → App lấy credentials từ Keychain/Keystore                   │
│     → Tự động đăng nhập với credentials đã lưu                   │
│  5. Nếu thất bại: Yêu cầu nhập mật khẩu thủ công                 │
└──────────────────────────────────────────────────────────────────┘
```

### 2.4. Điểm Quan Trọng

| Đặc Điểm | Chi Tiết |
|----------|----------|
| **Bảo mật** | Dữ liệu sinh trắc KHÔNG gửi lên server, xử lý hoàn toàn trên device |
| **Lưu trữ credentials** | Mã hóa trong iOS Keychain / Android Keystore |
| **Hủy sinh trắc học** | Vào Cài đặt → Tắt, hoặc đăng xuất tất cả thiết bị |
| **Fallback** | Nếu Face ID thất bại → Nhập mật khẩu thủ công |
| **Đổi mật khẩu** | Tự động vô hiệu sinh trắc học, cần kích hoạt lại |

### 2.5. Dữ Liệu Lưu Trữ

| Dữ Liệu | Nơi Lưu | Ghi Chú |
|---------|---------|---------|
| Dữ liệu sinh trắc học | Thiết bị (Secure Enclave) | Không gửi lên server |
| Credentials mã hóa | Keychain/Keystore | Chỉ trên thiết bị |
| Config biometricEnabled | Mango CMS | Chỉ lưu trạng thái bật/tắt |

---

## 3. Tóm Tắt Phân Công

### Đăng Ký Tài Khoản

| Công Việc | Loyalty | Mango CMS | SMS Gateway |
|-----------|:-------:|:---------:|:-----------:|
| Kiểm tra SĐT trùng | ✅ | - | - |
| Tạo & xác thực OTP | - | ✅ | ✅ |
| Tạo tài khoản | ✅ | - | - |
| Gán hạng Bạc | ✅ | - | - |
| Xử lý mã giới thiệu | ✅ | - | - |
| Tạo session | - | ✅ | - |

### Sinh Trắc Học

| Công Việc | Frontend App | Mango CMS |
|-----------|:------------:|:---------:|
| Xử lý Face ID/Touch ID | ✅ | - |
| Lưu credentials mã hóa | ✅ | - |
| Lưu config bật/tắt | - | ✅ |
| Auto-login | - | ✅ |

---

*Tài liệu này mô tả 2 chức năng chính: Đăng ký tài khoản mới và Xác thực sinh trắc học (Face ID/Touch ID).*
