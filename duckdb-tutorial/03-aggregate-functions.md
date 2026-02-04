# Aggregate Functions (Hàm Tổng Hợp)

> **Phần 3:** SUM, COUNT, AVG, MIN, MAX và các hàm thống kê cơ bản

---

## 1. Aggregate Functions Là Gì?

**Aggregate Functions** (Hàm tổng hợp) là các hàm tính toán trên một **nhóm các dòng** và trả về **một giá trị duy nhất**.

```
┌─────────────────────────────────────────────────────────────┐
│               AGGREGATE FUNCTIONS                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Nhiều dòng dữ liệu    →    Một giá trị kết quả            │
│                                                             │
│   ┌────┐                                                    │
│   │ 10 │                                                    │
│   │ 20 │   ───► SUM() ───►   60                             │
│   │ 30 │                                                    │
│   └────┘                                                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 2. Dữ Liệu Mẫu

```sql
-- Tạo bảng doanh số chi tiết
CREATE TABLE doanh_so (
    id INTEGER,
    nhan_vien VARCHAR(50),
    phong_ban VARCHAR(30),
    san_pham VARCHAR(50),
    so_luong INTEGER,
    don_gia INTEGER,
    ngay_ban DATE
);

INSERT INTO doanh_so VALUES
    (1, 'An', 'Sales', 'Laptop', 2, 20000000, '2024-01-05'),
    (2, 'An', 'Sales', 'Mouse', 10, 500000, '2024-01-10'),
    (3, 'Bình', 'Sales', 'Laptop', 1, 20000000, '2024-01-12'),
    (4, 'An', 'Sales', 'Keyboard', 5, 1000000, '2024-01-15'),
    (5, 'Bình', 'Sales', 'Monitor', 3, 5000000, '2024-01-18'),
    (6, 'Cường', 'Marketing', 'Laptop', 1, 20000000, '2024-02-01'),
    (7, 'Cường', 'Marketing', 'Mouse', 20, 500000, '2024-02-05'),
    (8, 'Dung', 'Marketing', 'Monitor', 2, 5000000, '2024-02-10'),
    (9, 'An', 'Sales', 'Laptop', 3, 20000000, '2024-02-15'),
    (10, 'Bình', 'Sales', 'Keyboard', 8, 1000000, '2024-02-20'),
    (11, 'Cường', 'Marketing', 'Monitor', 1, 5000000, '2024-03-01'),
    (12, 'Dung', 'Marketing', 'Laptop', 2, 20000000, '2024-03-05');

-- Thêm cột tính thành tiền
ALTER TABLE doanh_so ADD COLUMN thanh_tien INTEGER;
UPDATE doanh_so SET thanh_tien = so_luong * don_gia;
```

**📊 Bảng dữ liệu mẫu sau khi tạo:**

| id | nhan_vien | phong_ban | san_pham | so_luong | don_gia | ngay_ban | thanh_tien |
|----|-----------|-----------|----------|----------|---------|----------|------------|
| 1 | An | Sales | Laptop | 2 | 20,000,000 | 2024-01-05 | 40,000,000 |
| 2 | An | Sales | Mouse | 10 | 500,000 | 2024-01-10 | 5,000,000 |
| 3 | Bình | Sales | Laptop | 1 | 20,000,000 | 2024-01-12 | 20,000,000 |
| 4 | An | Sales | Keyboard | 5 | 1,000,000 | 2024-01-15 | 5,000,000 |
| 5 | Bình | Sales | Monitor | 3 | 5,000,000 | 2024-01-18 | 15,000,000 |
| 6 | Cường | Marketing | Laptop | 1 | 20,000,000 | 2024-02-01 | 20,000,000 |
| 7 | Cường | Marketing | Mouse | 20 | 500,000 | 2024-02-05 | 10,000,000 |
| 8 | Dung | Marketing | Monitor | 2 | 5,000,000 | 2024-02-10 | 10,000,000 |
| 9 | An | Sales | Laptop | 3 | 20,000,000 | 2024-02-15 | 60,000,000 |
| 10 | Bình | Sales | Keyboard | 8 | 1,000,000 | 2024-02-20 | 8,000,000 |
| 11 | Cường | Marketing | Monitor | 1 | 5,000,000 | 2024-03-01 | 5,000,000 |
| 12 | Dung | Marketing | Laptop | 2 | 20,000,000 | 2024-03-05 | 40,000,000 |

---

## 3. Các Hàm Aggregate Cơ Bản

### 3.1. COUNT - Đếm

```sql
-- Đếm tất cả các dòng
SELECT COUNT(*) AS tong_so_don FROM doanh_so;
-- Kết quả: 12

-- Đếm giá trị không NULL
SELECT COUNT(san_pham) AS so_san_pham FROM doanh_so;

-- Đếm giá trị duy nhất (DISTINCT)
SELECT COUNT(DISTINCT san_pham) AS so_loai_san_pham FROM doanh_so;
-- Kết quả: 4 (Laptop, Mouse, Keyboard, Monitor)

SELECT COUNT(DISTINCT nhan_vien) AS so_nhan_vien FROM doanh_so;
-- Kết quả: 4 (An, Bình, Cường, Dung)
```

### 3.2. SUM - Tổng

```sql
-- Tổng số lượng bán
SELECT SUM(so_luong) AS tong_so_luong FROM doanh_so;

-- Tổng doanh số
SELECT SUM(thanh_tien) AS tong_doanh_so FROM doanh_so;

-- Tổng theo điều kiện
SELECT SUM(thanh_tien) AS doanh_so_laptop
FROM doanh_so
WHERE san_pham = 'Laptop';
```

### 3.3. AVG - Trung Bình

```sql
-- Doanh số trung bình mỗi đơn
SELECT AVG(thanh_tien) AS doanh_so_tb FROM doanh_so;

-- Số lượng trung bình mỗi đơn
SELECT AVG(so_luong) AS so_luong_tb FROM doanh_so;

-- Làm tròn kết quả
SELECT ROUND(AVG(thanh_tien), 0) AS doanh_so_tb FROM doanh_so;
```

### 3.4. MIN và MAX

```sql
-- Đơn hàng nhỏ nhất và lớn nhất
SELECT
    MIN(thanh_tien) AS don_nho_nhat,
    MAX(thanh_tien) AS don_lon_nhat
FROM doanh_so;

-- Ngày bán đầu tiên và cuối cùng
SELECT
    MIN(ngay_ban) AS ngay_dau,
    MAX(ngay_ban) AS ngay_cuoi
FROM doanh_so;

-- Sản phẩm (theo thứ tự alphabet)
SELECT
    MIN(san_pham) AS san_pham_dau,
    MAX(san_pham) AS san_pham_cuoi
FROM doanh_so;
```

### 3.5. Kết Hợp Nhiều Hàm

```sql
SELECT
    COUNT(*) AS so_don,
    SUM(so_luong) AS tong_so_luong,
    SUM(thanh_tien) AS tong_doanh_so,
    ROUND(AVG(thanh_tien), 0) AS doanh_so_tb,
    MIN(thanh_tien) AS don_nho_nhat,
    MAX(thanh_tien) AS don_lon_nhat
FROM doanh_so;
```

Kết quả:
```
┌────────┬───────────────┬──────────────┬────────────┬─────────────┬─────────────┐
│ so_don │ tong_so_luong │ tong_doanh_so│ doanh_so_tb│ don_nho_nhat│ don_lon_nhat│
├────────┼───────────────┼──────────────┼────────────┼─────────────┼─────────────┤
│ 12     │ 58            │ 218000000   │ 18166667   │ 5000000     │ 60000000    │
└────────┴───────────────┴──────────────┴────────────┴─────────────┴─────────────┘
```

---

## 4. Aggregate Với GROUP BY

### 4.1. Nhóm Theo Một Cột

**🎯 Minh họa cách GROUP BY hoạt động:**

```
BƯỚC 1: Dữ liệu ban đầu (12 dòng)
┌───────────┬──────────────┐
│ nhan_vien │  thanh_tien  │
├───────────┼──────────────┤
│ An        │   40,000,000 │
│ An        │    5,000,000 │
│ An        │    5,000,000 │
│ An        │   60,000,000 │  ← 4 dòng của An
├───────────┼──────────────┤
│ Bình      │   20,000,000 │
│ Bình      │   15,000,000 │
│ Bình      │    8,000,000 │  ← 3 dòng của Bình
├───────────┼──────────────┤
│ Cường     │   20,000,000 │
│ Cường     │   10,000,000 │
│ Cường     │    5,000,000 │  ← 3 dòng của Cường
├───────────┼──────────────┤
│ Dung      │   10,000,000 │
│ Dung      │   40,000,000 │  ← 2 dòng của Dung
└───────────┴──────────────┘

BƯỚC 2: GROUP BY gom nhóm + Aggregate tính toán → 4 dòng kết quả
┌───────────┬────────┬───────────────┐
│ nhan_vien │ so_don │ tong_doanh_so │
├───────────┼────────┼───────────────┤
│ An        │   4    │   110,000,000 │  ← COUNT=4, SUM=40+5+5+60
│ Bình      │   3    │    43,000,000 │  ← COUNT=3, SUM=20+15+8
│ Cường     │   3    │    35,000,000 │  ← COUNT=3, SUM=20+10+5
│ Dung      │   2    │    50,000,000 │  ← COUNT=2, SUM=10+40
└───────────┴────────┴───────────────┘
```

```sql
-- Doanh số theo nhân viên
SELECT
    nhan_vien,
    COUNT(*) AS so_don,
    SUM(thanh_tien) AS tong_doanh_so
FROM doanh_so
GROUP BY nhan_vien
ORDER BY tong_doanh_so DESC;
```

**📊 Kết quả:**

| nhan_vien | so_don | tong_doanh_so |
|-----------|--------|---------------|
| An | 4 | 110,000,000 |
| Dung | 2 | 50,000,000 |
| Bình | 3 | 43,000,000 |
| Cường | 3 | 35,000,000 |

### 4.2. Nhóm Theo Nhiều Cột

```sql
-- Doanh số theo nhân viên và sản phẩm
SELECT
    nhan_vien,
    san_pham,
    COUNT(*) AS so_don,
    SUM(so_luong) AS tong_sl,
    SUM(thanh_tien) AS tong_tien
FROM doanh_so
GROUP BY nhan_vien, san_pham
ORDER BY nhan_vien, tong_tien DESC;
```

### 4.3. GROUP BY Với HAVING

```sql
-- Nhân viên có doanh số >= 50 triệu
SELECT
    nhan_vien,
    SUM(thanh_tien) AS tong_doanh_so
FROM doanh_so
GROUP BY nhan_vien
HAVING SUM(thanh_tien) >= 50000000
ORDER BY tong_doanh_so DESC;
```

### 4.4. GROUP BY Theo Biểu Thức

```sql
-- Doanh số theo tháng
SELECT
    DATE_TRUNC('month', ngay_ban) AS thang,
    COUNT(*) AS so_don,
    SUM(thanh_tien) AS doanh_so
FROM doanh_so
GROUP BY DATE_TRUNC('month', ngay_ban)
ORDER BY thang;

-- Doanh số theo mức giá
SELECT
    CASE
        WHEN don_gia >= 10000000 THEN 'Cao cấp'
        WHEN don_gia >= 1000000 THEN 'Trung bình'
        ELSE 'Phổ thông'
    END AS phan_khuc,
    COUNT(*) AS so_don,
    SUM(thanh_tien) AS doanh_so
FROM doanh_so
GROUP BY 1
ORDER BY doanh_so DESC;
```

---

## 5. Hàm Thống Kê Trong DuckDB

### 5.1. STDDEV - Độ Lệch Chuẩn

**Độ lệch chuẩn** (Standard Deviation) đo lường mức độ phân tán của dữ liệu quanh giá trị trung bình.

```
Độ lệch chuẩn NHỎ = Dữ liệu tập trung gần trung bình
Độ lệch chuẩn LỚN = Dữ liệu phân tán xa trung bình
```

```sql
-- Độ lệch chuẩn mẫu (sample)
SELECT
    AVG(thanh_tien) AS trung_binh,
    STDDEV_SAMP(thanh_tien) AS do_lech_chuan
FROM doanh_so;

-- Độ lệch chuẩn tổng thể (population)
SELECT STDDEV_POP(thanh_tien) AS do_lech_chuan_pop FROM doanh_so;

-- Viết tắt
SELECT STDDEV(thanh_tien) FROM doanh_so;  -- = STDDEV_SAMP
```

**Giải thích:**
```
Dữ liệu: [10, 20, 30, 40, 50]
Trung bình = 30

Độ lệch mỗi giá trị: [-20, -10, 0, +10, +20]
Bình phương: [400, 100, 0, 100, 400]
Trung bình bình phương = 200
Căn bậc 2 = 14.14 (độ lệch chuẩn)
```

### 5.2. VARIANCE - Phương Sai

**Phương sai** = Bình phương của độ lệch chuẩn.

```sql
SELECT
    VAR_SAMP(thanh_tien) AS phuong_sai_mau,
    VAR_POP(thanh_tien) AS phuong_sai_tong_the,
    VARIANCE(thanh_tien) AS phuong_sai  -- = VAR_SAMP
FROM doanh_so;
```

### 5.3. So Sánh Độ Ổn Định

```sql
-- So sánh độ ổn định doanh số giữa các nhân viên
SELECT
    nhan_vien,
    COUNT(*) AS so_don,
    ROUND(AVG(thanh_tien), 0) AS doanh_so_tb,
    ROUND(STDDEV(thanh_tien), 0) AS do_lech_chuan,
    ROUND(STDDEV(thanh_tien) / AVG(thanh_tien) * 100, 2) AS he_so_bien_thien
FROM doanh_so
GROUP BY nhan_vien
ORDER BY he_so_bien_thien;
```

**📊 Kết quả và phân tích:**

| nhan_vien | so_don | doanh_so_tb | do_lech_chuan | he_so_bien_thien | Đánh giá |
|-----------|--------|-------------|---------------|------------------|----------|
| Cường | 3 | 11,666,667 | 7,637,626 | 65.47% | Biến động trung bình |
| Bình | 3 | 14,333,333 | 6,027,714 | 42.05% | Tương đối ổn định |
| An | 4 | 27,500,000 | 26,299,941 | 95.64% | Biến động cao ⚠️ |
| Dung | 2 | 25,000,000 | 21,213,203 | 84.85% | Biến động cao ⚠️ |

**🔍 Giải thích kết quả:**
- **An** có CV = 95.64%: Doanh số dao động mạnh (có đơn 5 triệu, có đơn 60 triệu)
- **Bình** có CV = 42.05%: Doanh số ổn định nhất (các đơn 8-20 triệu)

**Hệ số biến thiên (CV)** = Độ lệch chuẩn / Trung bình × 100%
- CV < 30% = Doanh số ổn định ✅
- CV 30-60% = Biến động trung bình ⚡
- CV > 60% = Doanh số biến động nhiều ⚠️

---

## 6. Các Hàm Aggregate Khác

### 6.1. STRING_AGG - Nối Chuỗi

```sql
-- Danh sách sản phẩm mỗi nhân viên đã bán
SELECT
    nhan_vien,
    STRING_AGG(DISTINCT san_pham, ', ') AS danh_sach_sp
FROM doanh_so
GROUP BY nhan_vien;
```

Kết quả:
```
┌───────────┬────────────────────────────┐
│ nhan_vien │       danh_sach_sp         │
├───────────┼────────────────────────────┤
│ An        │ Keyboard, Laptop, Mouse    │
│ Bình      │ Keyboard, Laptop, Monitor  │
│ Cường     │ Laptop, Monitor, Mouse     │
│ Dung      │ Laptop, Monitor            │
└───────────┴────────────────────────────┘
```

### 6.2. ARRAY_AGG - Tạo Mảng

```sql
-- Danh sách thành tiền dưới dạng mảng
SELECT
    nhan_vien,
    ARRAY_AGG(thanh_tien ORDER BY ngay_ban) AS ds_thanh_tien
FROM doanh_so
GROUP BY nhan_vien;
```

### 6.3. BOOL_AND / BOOL_OR

```sql
-- Kiểm tra điều kiện trên tất cả dòng
SELECT
    nhan_vien,
    BOOL_AND(thanh_tien >= 5000000) AS tat_ca_don_lon,
    BOOL_OR(thanh_tien >= 50000000) AS co_don_rat_lon
FROM doanh_so
GROUP BY nhan_vien;
```

### 6.4. BIT_AND / BIT_OR / BIT_XOR

```sql
-- Phép toán bit trên nhóm (ít dùng trong analytics)
SELECT BIT_OR(so_luong) FROM doanh_so;
```

---

## 7. FILTER - Lọc Trong Aggregate

DuckDB hỗ trợ cú pháp `FILTER` để lọc dữ liệu cho từng hàm aggregate.

### 7.1. Cú Pháp FILTER

```sql
SELECT
    COUNT(*) AS tong_don,
    COUNT(*) FILTER (WHERE san_pham = 'Laptop') AS don_laptop,
    COUNT(*) FILTER (WHERE san_pham = 'Mouse') AS don_mouse,
    SUM(thanh_tien) FILTER (WHERE san_pham = 'Laptop') AS doanh_so_laptop
FROM doanh_so;
```

Kết quả:
```
┌──────────┬────────────┬───────────┬────────────────┐
│ tong_don │ don_laptop │ don_mouse │ doanh_so_laptop│
├──────────┼────────────┼───────────┼────────────────┤
│ 12       │ 5          │ 2         │ 140000000      │
└──────────┴────────────┴───────────┴────────────────┘
```

### 7.2. FILTER vs CASE WHEN

```sql
-- Cách 1: Dùng FILTER (DuckDB, PostgreSQL)
SELECT
    SUM(thanh_tien) FILTER (WHERE phong_ban = 'Sales') AS sales,
    SUM(thanh_tien) FILTER (WHERE phong_ban = 'Marketing') AS marketing
FROM doanh_so;

-- Cách 2: Dùng CASE WHEN (Mọi SQL database)
SELECT
    SUM(CASE WHEN phong_ban = 'Sales' THEN thanh_tien ELSE 0 END) AS sales,
    SUM(CASE WHEN phong_ban = 'Marketing' THEN thanh_tien ELSE 0 END) AS marketing
FROM doanh_so;
```

### 7.3. Ví Dụ Nâng Cao

```sql
-- Báo cáo doanh số theo tháng và loại sản phẩm
SELECT
    DATE_TRUNC('month', ngay_ban) AS thang,
    COUNT(*) AS tong_don,
    SUM(thanh_tien) AS tong_doanh_so,

    -- Doanh số theo loại sản phẩm
    SUM(thanh_tien) FILTER (WHERE san_pham = 'Laptop') AS laptop,
    SUM(thanh_tien) FILTER (WHERE san_pham = 'Monitor') AS monitor,
    SUM(thanh_tien) FILTER (WHERE san_pham NOT IN ('Laptop', 'Monitor')) AS khac,

    -- Tỷ lệ Laptop
    ROUND(
        SUM(thanh_tien) FILTER (WHERE san_pham = 'Laptop') * 100.0 /
        SUM(thanh_tien),
        1
    ) AS ty_le_laptop
FROM doanh_so
GROUP BY 1
ORDER BY 1;
```

---

## 8. Xử Lý NULL Trong Aggregate

### 8.1. Hành Vi Mặc Định

```sql
-- Aggregate bỏ qua NULL
CREATE TABLE test_null (value INTEGER);
INSERT INTO test_null VALUES (10), (20), (NULL), (30);

SELECT
    COUNT(*) AS count_all,        -- 4 (đếm cả NULL)
    COUNT(value) AS count_value,  -- 3 (bỏ qua NULL)
    SUM(value) AS sum_value,      -- 60 (bỏ qua NULL)
    AVG(value) AS avg_value       -- 20 (60/3, không phải 60/4)
FROM test_null;

DROP TABLE test_null;
```

### 8.2. COALESCE - Thay Thế NULL

```sql
SELECT
    nhan_vien,
    COALESCE(SUM(thanh_tien), 0) AS doanh_so
FROM doanh_so
GROUP BY nhan_vien;
```

---

## 9. Bảng Tổng Hợp Aggregate Functions

| Hàm | Mô tả | Ví dụ |
|-----|-------|-------|
| `COUNT(*)` | Đếm tất cả dòng | `COUNT(*)` |
| `COUNT(col)` | Đếm giá trị không NULL | `COUNT(name)` |
| `COUNT(DISTINCT col)` | Đếm giá trị duy nhất | `COUNT(DISTINCT city)` |
| `SUM(col)` | Tổng | `SUM(sales)` |
| `AVG(col)` | Trung bình | `AVG(price)` |
| `MIN(col)` | Giá trị nhỏ nhất | `MIN(date)` |
| `MAX(col)` | Giá trị lớn nhất | `MAX(date)` |
| `STDDEV(col)` | Độ lệch chuẩn mẫu | `STDDEV(score)` |
| `STDDEV_POP(col)` | Độ lệch chuẩn tổng thể | `STDDEV_POP(score)` |
| `VARIANCE(col)` | Phương sai mẫu | `VARIANCE(score)` |
| `STRING_AGG(col, sep)` | Nối chuỗi | `STRING_AGG(name, ', ')` |
| `ARRAY_AGG(col)` | Tạo mảng | `ARRAY_AGG(id)` |
| `BOOL_AND(cond)` | Tất cả TRUE? | `BOOL_AND(active)` |
| `BOOL_OR(cond)` | Có ít nhất 1 TRUE? | `BOOL_OR(active)` |

---

## 10. Bài Tập Thực Hành

### Bài 1: Aggregate cơ bản
```sql
-- a) Tính tổng, trung bình, min, max của thanh_tien
-- b) Đếm số sản phẩm khác nhau đã bán
-- c) Tìm ngày bán đầu tiên và cuối cùng
```

### Bài 2: GROUP BY
```sql
-- a) Doanh số theo từng sản phẩm, sắp xếp giảm dần
-- b) Số đơn và doanh số theo từng tháng
-- c) Nhân viên có doanh số cao nhất mỗi phòng ban
```

### Bài 3: FILTER
```sql
-- Tạo báo cáo với các cột:
-- - nhan_vien
-- - tong_don
-- - don_laptop (số đơn Laptop)
-- - don_khac (số đơn không phải Laptop)
-- - ty_le_laptop (%)
```

### Bài 4: Thống kê
```sql
-- So sánh độ ổn định doanh số giữa các sản phẩm:
-- - san_pham
-- - so_don
-- - doanh_so_tb
-- - do_lech_chuan
-- - he_so_bien_thien (CV%)
```

---

**Tiếp theo:** [04 - Window Functions Cơ Bản](./04-window-functions-co-ban.md)

---

*Chúc bạn học tốt! 🦆*
