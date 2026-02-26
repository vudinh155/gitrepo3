# Window Functions Trong DuckDB

> **📚 TÀI LIỆU HỌC DUCKDB - MANGOADS**
> Phiên bản 1.0 - Tháng 01/2026

---

## Mục Lục

1. [Window Functions Là Gì?](#1-window-functions-là-gì)
2. [Cú Pháp OVER()](#2-cú-pháp-over)
3. [PARTITION BY - Chia Nhóm Dữ Liệu](#3-partition-by---chia-nhóm-dữ-liệu)
4. [ORDER BY Trong Window](#4-order-by-trong-window)
5. [Các Hàm Đánh Số Thứ Tự](#5-các-hàm-đánh-số-thứ-tự)
6. [Các Hàm Ranking](#6-các-hàm-ranking)
7. [LAG và LEAD - Truy Cập Dòng Trước/Sau](#7-lag-và-lead---truy-cập-dòng-trướcsau)
8. [FIRST_VALUE, LAST_VALUE, NTH_VALUE](#8-first_value-last_value-nth_value)
9. [Window Frame - Định Nghĩa Phạm Vi](#9-window-frame---định-nghĩa-phạm-vi)
10. [Aggregate Functions Với OVER](#10-aggregate-functions-với-over)
11. [Ví Dụ Thực Tế](#11-ví-dụ-thực-tế)
12. [Bài Tập Thực Hành](#12-bài-tập-thực-hành)

---

## 1. Window Functions Là Gì?

### 1.1. Khái Niệm

**Window Functions** (Hàm Cửa Sổ) là các hàm thực hiện tính toán trên một "cửa sổ" các dòng liên quan đến dòng hiện tại, **NHƯNG KHÔNG làm giảm số dòng** như GROUP BY.

```
+------------------------------------------------------------------+
|                                                                  |
|   GROUP BY: 100 dòng → Gom nhóm → 10 dòng kết quả                |
|                                                                  |
|   WINDOW:   100 dòng → Tính toán → 100 dòng (giữ nguyên)         |
|                        + thêm cột kết quả                        |
|                                                                  |
+------------------------------------------------------------------+
```

### 1.2. Ví Dụ So Sánh

**Dữ liệu mẫu:**
```sql
CREATE TABLE doanh_so AS
SELECT * FROM (VALUES
    ('Hà Nội', 'Laptop', 1000),
    ('Hà Nội', 'Điện thoại', 800),
    ('Hà Nội', 'Tablet', 500),
    ('TP.HCM', 'Laptop', 1200),
    ('TP.HCM', 'Điện thoại', 900),
    ('TP.HCM', 'Tablet', 600)
) AS t(khu_vuc, san_pham, doanh_thu);
```

**GROUP BY - Gom nhóm, mất chi tiết:**
```sql
SELECT
    khu_vuc,
    SUM(doanh_thu) AS tong_doanh_thu
FROM doanh_so
GROUP BY khu_vuc;
```

Kết quả:
```
┌─────────┬────────────────┐
│ khu_vuc │ tong_doanh_thu │
├─────────┼────────────────┤
│ Hà Nội  │           2300 │
│ TP.HCM  │           2700 │
└─────────┴────────────────┘
```
→ Chỉ còn 2 dòng, mất thông tin sản phẩm!

**WINDOW - Giữ nguyên chi tiết:**
```sql
SELECT
    khu_vuc,
    san_pham,
    doanh_thu,
    SUM(doanh_thu) OVER (PARTITION BY khu_vuc) AS tong_khu_vuc
FROM doanh_so;
```

Kết quả:
```
┌─────────┬────────────┬──────────┬──────────────┐
│ khu_vuc │ san_pham   │ doanh_thu│ tong_khu_vuc │
├─────────┼────────────┼──────────┼──────────────┤
│ Hà Nội  │ Laptop     │     1000 │         2300 │
│ Hà Nội  │ Điện thoại │      800 │         2300 │
│ Hà Nội  │ Tablet     │      500 │         2300 │
│ TP.HCM  │ Laptop     │     1200 │         2700 │
│ TP.HCM  │ Điện thoại │      900 │         2700 │
│ TP.HCM  │ Tablet     │      600 │         2700 │
└─────────┴────────────┴──────────┴──────────────┘
```
→ Vẫn 6 dòng, có thêm cột tổng khu vực!

### 1.3. Khi Nào Dùng Window Functions?

| Tình huống | Dùng Window Functions |
|------------|----------------------|
| So sánh dòng hiện tại với tổng nhóm | ✅ |
| Tính phần trăm đóng góp | ✅ |
| Xếp hạng trong nhóm | ✅ |
| So sánh với dòng trước/sau | ✅ |
| Tính running total (tổng lũy kế) | ✅ |
| Moving average (trung bình trượt) | ✅ |
| Chỉ cần tổng/trung bình đơn giản | ❌ Dùng GROUP BY |

---

## 2. Cú Pháp OVER()

### 2.1. Cấu Trúc Cơ Bản

```sql
<window_function>() OVER (
    [PARTITION BY cột_chia_nhóm]
    [ORDER BY cột_sắp_xếp]
    [ROWS/RANGE frame_specification]
)
```

### 2.2. Các Thành Phần

```
+------------------------------------------------------------------+
|                         OVER (...)                                |
|                                                                  |
|   ┌─────────────────────────────────────────────────────────┐    |
|   │ PARTITION BY: Chia dữ liệu thành các nhóm nhỏ           │    |
|   │               (Giống GROUP BY nhưng không gom)          │    |
|   └─────────────────────────────────────────────────────────┘    |
|                              ↓                                   |
|   ┌─────────────────────────────────────────────────────────┐    |
|   │ ORDER BY: Sắp xếp các dòng trong mỗi partition          │    |
|   │           (Quan trọng cho ranking, LAG, LEAD)           │    |
|   └─────────────────────────────────────────────────────────┘    |
|                              ↓                                   |
|   ┌─────────────────────────────────────────────────────────┐    |
|   │ FRAME: Định nghĩa "cửa sổ" dòng để tính toán            │    |
|   │        (ROWS BETWEEN ... AND ...)                       │    |
|   └─────────────────────────────────────────────────────────┘    |
|                                                                  |
+------------------------------------------------------------------+
```

### 2.3. OVER() Rỗng - Toàn Bộ Dataset

```sql
-- OVER() rỗng = tính toán trên TOÀN BỘ dữ liệu
SELECT
    khu_vuc,
    san_pham,
    doanh_thu,
    SUM(doanh_thu) OVER () AS tong_tat_ca,
    ROUND(doanh_thu * 100.0 / SUM(doanh_thu) OVER (), 2) AS phan_tram
FROM doanh_so;
```

Kết quả:
```
┌─────────┬────────────┬──────────┬─────────────┬──────────┐
│ khu_vuc │ san_pham   │ doanh_thu│ tong_tat_ca │ phan_tram│
├─────────┼────────────┼──────────┼─────────────┼──────────┤
│ Hà Nội  │ Laptop     │     1000 │        5000 │    20.00 │
│ Hà Nội  │ Điện thoại │      800 │        5000 │    16.00 │
│ Hà Nội  │ Tablet     │      500 │        5000 │    10.00 │
│ TP.HCM  │ Laptop     │     1200 │        5000 │    24.00 │
│ TP.HCM  │ Điện thoại │      900 │        5000 │    18.00 │
│ TP.HCM  │ Tablet     │      600 │        5000 │    12.00 │
└─────────┴────────────┴──────────┴─────────────┴──────────┘
```

---

## 3. PARTITION BY - Chia Nhóm Dữ Liệu

### 3.1. Khái Niệm

**PARTITION BY** chia dữ liệu thành các "partition" (phân vùng) độc lập. Window function sẽ tính toán **riêng biệt** cho mỗi partition.

```
Dữ liệu gốc:                    Sau PARTITION BY khu_vuc:
┌────────────────────┐          ┌──────────────────────┐
│ Hà Nội  | Laptop   │          │ PARTITION: Hà Nội    │
│ Hà Nội  | Phone    │    →     │ ├─ Laptop            │
│ TP.HCM  | Laptop   │          │ ├─ Phone             │
│ TP.HCM  | Phone    │          │ └─ Tablet            │
│ Hà Nội  | Tablet   │          ├──────────────────────┤
│ TP.HCM  | Tablet   │          │ PARTITION: TP.HCM    │
└────────────────────┘          │ ├─ Laptop            │
                                │ ├─ Phone             │
                                │ └─ Tablet            │
                                └──────────────────────┘
```

### 3.2. Ví Dụ: Phần Trăm Trong Nhóm

```sql
SELECT
    khu_vuc,
    san_pham,
    doanh_thu,
    SUM(doanh_thu) OVER (PARTITION BY khu_vuc) AS tong_khu_vuc,
    ROUND(doanh_thu * 100.0 / SUM(doanh_thu) OVER (PARTITION BY khu_vuc), 2)
        AS phan_tram_khu_vuc
FROM doanh_so
ORDER BY khu_vuc, doanh_thu DESC;
```

Kết quả:
```
┌─────────┬────────────┬──────────┬──────────────┬──────────────────┐
│ khu_vuc │ san_pham   │ doanh_thu│ tong_khu_vuc │ phan_tram_khu_vuc│
├─────────┼────────────┼──────────┼──────────────┼──────────────────┤
│ Hà Nội  │ Laptop     │     1000 │         2300 │            43.48 │
│ Hà Nội  │ Điện thoại │      800 │         2300 │            34.78 │
│ Hà Nội  │ Tablet     │      500 │         2300 │            21.74 │
│ TP.HCM  │ Laptop     │     1200 │         2700 │            44.44 │
│ TP.HCM  │ Điện thoại │      900 │         2700 │            33.33 │
│ TP.HCM  │ Tablet     │      600 │         2700 │            22.22 │
└─────────┴────────────┴──────────┴──────────────┴──────────────────┘
```

### 3.3. PARTITION BY Nhiều Cột

```sql
-- Dữ liệu mẫu mở rộng
CREATE TABLE doanh_so_chi_tiet AS
SELECT * FROM (VALUES
    ('Hà Nội', 'Q1', 'Laptop', 1000),
    ('Hà Nội', 'Q1', 'Phone', 800),
    ('Hà Nội', 'Q2', 'Laptop', 1100),
    ('Hà Nội', 'Q2', 'Phone', 850),
    ('TP.HCM', 'Q1', 'Laptop', 1200),
    ('TP.HCM', 'Q1', 'Phone', 900),
    ('TP.HCM', 'Q2', 'Laptop', 1300),
    ('TP.HCM', 'Q2', 'Phone', 950)
) AS t(khu_vuc, quy, san_pham, doanh_thu);

-- Partition theo nhiều cột
SELECT
    khu_vuc,
    quy,
    san_pham,
    doanh_thu,
    SUM(doanh_thu) OVER (PARTITION BY khu_vuc) AS tong_khu_vuc,
    SUM(doanh_thu) OVER (PARTITION BY khu_vuc, quy) AS tong_khu_vuc_quy
FROM doanh_so_chi_tiet
ORDER BY khu_vuc, quy, san_pham;
```

---

## 4. ORDER BY Trong Window

### 4.1. Tác Dụng Của ORDER BY

**ORDER BY** trong OVER() làm hai việc:
1. **Sắp xếp** các dòng trong partition
2. **Xác định frame mặc định** cho các hàm aggregate

```sql
-- Không có ORDER BY: tính tổng TOÀN BỘ partition
SUM(doanh_thu) OVER (PARTITION BY khu_vuc)

-- Có ORDER BY: tính tổng LŨY KẾ (running total)
SUM(doanh_thu) OVER (PARTITION BY khu_vuc ORDER BY ngay)
```

### 4.2. Running Total - Tổng Lũy Kế

```sql
CREATE TABLE giao_dich AS
SELECT * FROM (VALUES
    ('2024-01-01', 'Hà Nội', 100),
    ('2024-01-02', 'Hà Nội', 150),
    ('2024-01-03', 'Hà Nội', 200),
    ('2024-01-01', 'TP.HCM', 120),
    ('2024-01-02', 'TP.HCM', 180),
    ('2024-01-03', 'TP.HCM', 160)
) AS t(ngay, khu_vuc, doanh_thu);

SELECT
    ngay,
    khu_vuc,
    doanh_thu,
    SUM(doanh_thu) OVER (
        PARTITION BY khu_vuc
        ORDER BY ngay
    ) AS tong_luy_ke
FROM giao_dich
ORDER BY khu_vuc, ngay;
```

Kết quả:
```
┌────────────┬─────────┬──────────┬─────────────┐
│    ngay    │ khu_vuc │ doanh_thu│ tong_luy_ke │
├────────────┼─────────┼──────────┼─────────────┤
│ 2024-01-01 │ Hà Nội  │      100 │         100 │
│ 2024-01-02 │ Hà Nội  │      150 │         250 │  ← 100 + 150
│ 2024-01-03 │ Hà Nội  │      200 │         450 │  ← 100 + 150 + 200
│ 2024-01-01 │ TP.HCM  │      120 │         120 │
│ 2024-01-02 │ TP.HCM  │      180 │         300 │  ← 120 + 180
│ 2024-01-03 │ TP.HCM  │      160 │         460 │  ← 120 + 180 + 160
└────────────┴─────────┴──────────┴─────────────┘
```

### 4.3. ORDER BY Nhiều Cột

```sql
SELECT
    ngay,
    khu_vuc,
    doanh_thu,
    ROW_NUMBER() OVER (ORDER BY doanh_thu DESC, ngay ASC) AS thu_tu
FROM giao_dich;
```

---

## 5. Các Hàm Đánh Số Thứ Tự

### 5.1. ROW_NUMBER() - Số Thứ Tự Duy Nhất

**ROW_NUMBER()** gán số thứ tự duy nhất cho mỗi dòng, bắt đầu từ 1.

```sql
SELECT
    khu_vuc,
    san_pham,
    doanh_thu,
    ROW_NUMBER() OVER (ORDER BY doanh_thu DESC) AS stt_chung,
    ROW_NUMBER() OVER (
        PARTITION BY khu_vuc
        ORDER BY doanh_thu DESC
    ) AS stt_trong_khu_vuc
FROM doanh_so;
```

Kết quả:
```
┌─────────┬────────────┬──────────┬───────────┬───────────────────┐
│ khu_vuc │ san_pham   │ doanh_thu│ stt_chung │ stt_trong_khu_vuc │
├─────────┼────────────┼──────────┼───────────┼───────────────────┤
│ TP.HCM  │ Laptop     │     1200 │         1 │                 1 │
│ Hà Nội  │ Laptop     │     1000 │         2 │                 1 │
│ TP.HCM  │ Điện thoại │      900 │         3 │                 2 │
│ Hà Nội  │ Điện thoại │      800 │         4 │                 2 │
│ TP.HCM  │ Tablet     │      600 │         5 │                 3 │
│ Hà Nội  │ Tablet     │      500 │         6 │                 3 │
└─────────┴────────────┴──────────┴───────────┴───────────────────┘
```

### 5.2. Ứng Dụng: Lấy Top N Mỗi Nhóm

```sql
-- Lấy sản phẩm có doanh thu cao nhất mỗi khu vực
WITH xep_hang AS (
    SELECT
        khu_vuc,
        san_pham,
        doanh_thu,
        ROW_NUMBER() OVER (
            PARTITION BY khu_vuc
            ORDER BY doanh_thu DESC
        ) AS hang
    FROM doanh_so
)
SELECT khu_vuc, san_pham, doanh_thu
FROM xep_hang
WHERE hang = 1;
```

Kết quả:
```
┌─────────┬──────────┬──────────┐
│ khu_vuc │ san_pham │ doanh_thu│
├─────────┼──────────┼──────────┤
│ Hà Nội  │ Laptop   │     1000 │
│ TP.HCM  │ Laptop   │     1200 │
└─────────┴──────────┴──────────┘
```

---

## 6. Các Hàm Ranking

### 6.1. So Sánh RANK, DENSE_RANK, ROW_NUMBER

```sql
CREATE TABLE diem_thi AS
SELECT * FROM (VALUES
    ('An', 95),
    ('Bình', 90),
    ('Cường', 90),
    ('Dũng', 85),
    ('Em', 85),
    ('Phong', 80)
) AS t(hoc_sinh, diem);

SELECT
    hoc_sinh,
    diem,
    ROW_NUMBER() OVER (ORDER BY diem DESC) AS row_number,
    RANK() OVER (ORDER BY diem DESC) AS rank,
    DENSE_RANK() OVER (ORDER BY diem DESC) AS dense_rank
FROM diem_thi;
```

Kết quả:
```
┌──────────┬──────┬────────────┬──────┬────────────┐
│ hoc_sinh │ diem │ row_number │ rank │ dense_rank │
├──────────┼──────┼────────────┼──────┼────────────┤
│ An       │   95 │          1 │    1 │          1 │
│ Bình     │   90 │          2 │    2 │          2 │
│ Cường    │   90 │          3 │    2 │          2 │  ← Cùng điểm
│ Dũng     │   85 │          4 │    4 │          3 │  ← Khác nhau!
│ Em       │   85 │          5 │    4 │          3 │
│ Phong    │   80 │          6 │    6 │          4 │
└──────────┴──────┴────────────┴──────┴────────────┘
```

### 6.2. Giải Thích Sự Khác Biệt

```
+------------------------------------------------------------------+
|                                                                  |
|   ROW_NUMBER: Luôn đánh số liên tiếp 1, 2, 3, 4, 5, 6...         |
|               Không quan tâm giá trị trùng                       |
|                                                                  |
|   RANK:       Cùng giá trị → Cùng hạng                           |
|               Hạng tiếp theo = Hạng hiện tại + Số người cùng hạng|
|               VD: 1, 2, 2, 4, 4, 6 (nhảy cóc)                    |
|                                                                  |
|   DENSE_RANK: Cùng giá trị → Cùng hạng                           |
|               Hạng tiếp theo = Hạng hiện tại + 1                 |
|               VD: 1, 2, 2, 3, 3, 4 (không nhảy)                  |
|                                                                  |
+------------------------------------------------------------------+
```

### 6.3. PERCENT_RANK và CUME_DIST

```sql
SELECT
    hoc_sinh,
    diem,
    ROUND(PERCENT_RANK() OVER (ORDER BY diem DESC), 2) AS percent_rank,
    ROUND(CUME_DIST() OVER (ORDER BY diem DESC), 2) AS cume_dist
FROM diem_thi;
```

Kết quả:
```
┌──────────┬──────┬──────────────┬───────────┐
│ hoc_sinh │ diem │ percent_rank │ cume_dist │
├──────────┼──────┼──────────────┼───────────┤
│ An       │   95 │         0.00 │      0.17 │
│ Bình     │   90 │         0.20 │      0.50 │
│ Cường    │   90 │         0.20 │      0.50 │
│ Dũng     │   85 │         0.60 │      0.83 │
│ Em       │   85 │         0.60 │      0.83 │
│ Phong    │   80 │         1.00 │      1.00 │
└──────────┴──────┴──────────────┴───────────┘
```

**Giải thích:**
- **PERCENT_RANK** = (rank - 1) / (total_rows - 1)
  - An: (1-1)/(6-1) = 0
  - Bình/Cường: (2-1)/(6-1) = 0.2
- **CUME_DIST** = Số người có điểm >= x / Tổng số người
  - An: 1/6 = 0.17 (chỉ có 1 người điểm >= 95)
  - Bình/Cường: 3/6 = 0.5 (có 3 người điểm >= 90)

### 6.4. NTILE - Chia Thành N Nhóm

```sql
SELECT
    hoc_sinh,
    diem,
    NTILE(3) OVER (ORDER BY diem DESC) AS nhom_3,
    NTILE(4) OVER (ORDER BY diem DESC) AS nhom_4
FROM diem_thi;
```

Kết quả:
```
┌──────────┬──────┬────────┬────────┐
│ hoc_sinh │ diem │ nhom_3 │ nhom_4 │
├──────────┼──────┼────────┼────────┤
│ An       │   95 │      1 │      1 │
│ Bình     │   90 │      1 │      1 │
│ Cường    │   90 │      2 │      2 │
│ Dũng     │   85 │      2 │      2 │
│ Em       │   85 │      3 │      3 │
│ Phong    │   80 │      3 │      4 │
└──────────┴──────┴────────┴────────┘
```

**Ứng dụng:** Chia học sinh thành nhóm Giỏi/Khá/Trung bình

---

## 7. LAG và LEAD - Truy Cập Dòng Trước/Sau

### 7.1. Cú Pháp

```sql
LAG(cột, offset, default_value) OVER (ORDER BY ...)
LEAD(cột, offset, default_value) OVER (ORDER BY ...)
```

- **LAG**: Lấy giá trị từ dòng **TRƯỚC**
- **LEAD**: Lấy giá trị từ dòng **SAU**
- **offset**: Số dòng cách xa (mặc định = 1)
- **default_value**: Giá trị trả về nếu không có dòng (mặc định = NULL)

**🎯 Minh họa LAG và LEAD:**

```
Dữ liệu theo thứ tự:
┌────────────────────────────────────────────────────────────────┐
│  Tháng    │  Doanh thu  │  LAG (trước)  │  LEAD (sau)          │
├────────────────────────────────────────────────────────────────┤
│  2024-01  │    1000     │     NULL      │    1200              │
│           │             │      ↑        │      ↓               │
│           │             │  Không có     │  Lấy từ 2024-02      │
├───────────┼─────────────┼───────────────┼──────────────────────┤
│  2024-02  │    1200     │    1000       │    1100              │
│           │      ★      │      ↑        │      ↓               │
│           │  Dòng hiện  │  Lấy từ       │  Lấy từ              │
│           │    tại      │  2024-01      │  2024-03             │
├───────────┼─────────────┼───────────────┼──────────────────────┤
│  2024-03  │    1100     │    1200       │    1500              │
│           │             │      ↑        │      ↓               │
│           │             │  Lấy từ       │  Lấy từ              │
│           │             │  2024-02      │  2024-04             │
└────────────────────────────────────────────────────────────────┘

LAG = Nhìn lên trên (quá khứ)    ↑
LEAD = Nhìn xuống dưới (tương lai) ↓
```

### 7.2. Ví Dụ: So Sánh Với Tháng Trước

```sql
CREATE TABLE doanh_so_thang AS
SELECT * FROM (VALUES
    ('2024-01', 1000),
    ('2024-02', 1200),
    ('2024-03', 1100),
    ('2024-04', 1500),
    ('2024-05', 1400),
    ('2024-06', 1600)
) AS t(thang, doanh_thu);

SELECT
    thang,
    doanh_thu,
    LAG(doanh_thu, 1, 0) OVER (ORDER BY thang) AS thang_truoc,
    doanh_thu - LAG(doanh_thu, 1, 0) OVER (ORDER BY thang) AS chenh_lech,
    CASE
        WHEN LAG(doanh_thu) OVER (ORDER BY thang) IS NULL THEN NULL
        ELSE ROUND(
            (doanh_thu - LAG(doanh_thu) OVER (ORDER BY thang)) * 100.0
            / LAG(doanh_thu) OVER (ORDER BY thang),
            2
        )
    END AS tang_truong_pct
FROM doanh_so_thang;
```

Kết quả:
```
┌─────────┬──────────┬─────────────┬────────────┬───────────────┐
│  thang  │ doanh_thu│ thang_truoc │ chenh_lech │ tang_truong_pct│
├─────────┼──────────┼─────────────┼────────────┼───────────────┤
│ 2024-01 │     1000 │           0 │       1000 │          NULL │
│ 2024-02 │     1200 │        1000 │        200 │         20.00 │
│ 2024-03 │     1100 │        1200 │       -100 │         -8.33 │
│ 2024-04 │     1500 │        1100 │        400 │         36.36 │
│ 2024-05 │     1400 │        1500 │       -100 │         -6.67 │
│ 2024-06 │     1600 │        1400 │        200 │         14.29 │
└─────────┴──────────┴─────────────┴────────────┴───────────────┘
```

### 7.3. LAG/LEAD Với Offset > 1

```sql
-- So sánh với cùng kỳ năm trước (12 tháng trước)
SELECT
    thang,
    doanh_thu,
    LAG(doanh_thu, 3) OVER (ORDER BY thang) AS quy_truoc,
    LEAD(doanh_thu, 1) OVER (ORDER BY thang) AS thang_sau
FROM doanh_so_thang;
```

### 7.4. LAG/LEAD Với PARTITION BY

```sql
CREATE TABLE doanh_so_kv_thang AS
SELECT * FROM (VALUES
    ('Hà Nội', '2024-01', 500),
    ('Hà Nội', '2024-02', 600),
    ('Hà Nội', '2024-03', 550),
    ('TP.HCM', '2024-01', 700),
    ('TP.HCM', '2024-02', 750),
    ('TP.HCM', '2024-03', 800)
) AS t(khu_vuc, thang, doanh_thu);

SELECT
    khu_vuc,
    thang,
    doanh_thu,
    LAG(doanh_thu) OVER (
        PARTITION BY khu_vuc
        ORDER BY thang
    ) AS thang_truoc_cung_kv
FROM doanh_so_kv_thang;
```

Kết quả:
```
┌─────────┬─────────┬──────────┬─────────────────────┐
│ khu_vuc │  thang  │ doanh_thu│ thang_truoc_cung_kv │
├─────────┼─────────┼──────────┼─────────────────────┤
│ Hà Nội  │ 2024-01 │      500 │                NULL │
│ Hà Nội  │ 2024-02 │      600 │                 500 │
│ Hà Nội  │ 2024-03 │      550 │                 600 │
│ TP.HCM  │ 2024-01 │      700 │                NULL │  ← Reset cho partition mới
│ TP.HCM  │ 2024-02 │      750 │                 700 │
│ TP.HCM  │ 2024-03 │      800 │                 750 │
└─────────┴─────────┴──────────┴─────────────────────┘
```

---

## 8. FIRST_VALUE, LAST_VALUE, NTH_VALUE

### 8.1. FIRST_VALUE - Giá Trị Đầu Tiên

```sql
SELECT
    khu_vuc,
    san_pham,
    doanh_thu,
    FIRST_VALUE(san_pham) OVER (
        PARTITION BY khu_vuc
        ORDER BY doanh_thu DESC
    ) AS san_pham_top_1,
    FIRST_VALUE(doanh_thu) OVER (
        PARTITION BY khu_vuc
        ORDER BY doanh_thu DESC
    ) AS doanh_thu_cao_nhat
FROM doanh_so;
```

### 8.2. LAST_VALUE - Giá Trị Cuối Cùng

**⚠️ Lưu ý quan trọng:** LAST_VALUE cần frame đầy đủ!

```sql
-- SAI: Kết quả không như mong đợi
SELECT
    san_pham,
    doanh_thu,
    LAST_VALUE(san_pham) OVER (ORDER BY doanh_thu DESC) AS sai
FROM doanh_so;

-- ĐÚNG: Chỉ định frame rõ ràng
SELECT
    san_pham,
    doanh_thu,
    LAST_VALUE(san_pham) OVER (
        ORDER BY doanh_thu DESC
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS dung
FROM doanh_so;
```

### 8.3. NTH_VALUE - Giá Trị Thứ N

```sql
SELECT
    khu_vuc,
    san_pham,
    doanh_thu,
    NTH_VALUE(san_pham, 2) OVER (
        PARTITION BY khu_vuc
        ORDER BY doanh_thu DESC
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS san_pham_top_2
FROM doanh_so;
```

---

## 9. Window Frame - Định Nghĩa Phạm Vi

### 9.1. Cú Pháp Frame

```sql
ROWS BETWEEN <start> AND <end>
RANGE BETWEEN <start> AND <end>
```

Các giá trị cho start/end:
- `UNBOUNDED PRECEDING` - Từ dòng đầu tiên
- `n PRECEDING` - n dòng trước
- `CURRENT ROW` - Dòng hiện tại
- `n FOLLOWING` - n dòng sau
- `UNBOUNDED FOLLOWING` - Đến dòng cuối cùng

### 9.2. Minh Họa Frame

```
Dữ liệu:  [A] [B] [C] [D] [E] [F]
                    ↑
              Current Row

ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW:
[A] [B] [C] [D]  ← Tính từ đầu đến hiện tại

ROWS BETWEEN 2 PRECEDING AND CURRENT ROW:
    [B] [C] [D]  ← Tính 2 dòng trước + hiện tại

ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING:
        [C] [D] [E]  ← Tính 1 trước + hiện tại + 1 sau

ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING:
            [D] [E] [F]  ← Tính từ hiện tại đến cuối
```

### 9.3. Moving Average - Trung Bình Trượt

```sql
SELECT
    thang,
    doanh_thu,
    -- Trung bình 3 tháng gần nhất (2 trước + hiện tại)
    ROUND(AVG(doanh_thu) OVER (
        ORDER BY thang
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ), 2) AS avg_3_thang,
    -- Trung bình 3 tháng (1 trước + hiện tại + 1 sau)
    ROUND(AVG(doanh_thu) OVER (
        ORDER BY thang
        ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING
    ), 2) AS avg_centered
FROM doanh_so_thang;
```

Kết quả:
```
┌─────────┬──────────┬─────────────┬──────────────┐
│  thang  │ doanh_thu│ avg_3_thang │ avg_centered │
├─────────┼──────────┼─────────────┼──────────────┤
│ 2024-01 │     1000 │     1000.00 │      1100.00 │
│ 2024-02 │     1200 │     1100.00 │      1100.00 │
│ 2024-03 │     1100 │     1100.00 │      1266.67 │
│ 2024-04 │     1500 │     1266.67 │      1333.33 │
│ 2024-05 │     1400 │     1333.33 │      1500.00 │
│ 2024-06 │     1600 │     1500.00 │      1500.00 │
└─────────┴──────────┴─────────────┴──────────────┘
```

**🔍 Giải thích chi tiết cách tính:**

| Tháng | Doanh thu | avg_3_thang (2 PRECEDING + CURRENT) | avg_centered (1 PRECEDING + CURRENT + 1 FOLLOWING) |
|-------|-----------|-------------------------------------|-----------------------------------------------------|
| 01 | 1000 | (1000) / 1 = **1000** | (1000 + 1200) / 2 = **1100** |
| 02 | 1200 | (1000 + 1200) / 2 = **1100** | (1000 + 1200 + 1100) / 3 = **1100** |
| 03 | 1100 | (1000 + 1200 + 1100) / 3 = **1100** | (1200 + 1100 + 1500) / 3 = **1267** |
| 04 | 1500 | (1200 + 1100 + 1500) / 3 = **1267** | (1100 + 1500 + 1400) / 3 = **1333** |
| 05 | 1400 | (1100 + 1500 + 1400) / 3 = **1333** | (1500 + 1400 + 1600) / 3 = **1500** |
| 06 | 1600 | (1500 + 1400 + 1600) / 3 = **1500** | (1400 + 1600) / 2 = **1500** |

### 9.4. ROWS vs RANGE

```sql
-- ROWS: Đếm theo số dòng vật lý
ROWS BETWEEN 2 PRECEDING AND CURRENT ROW

-- RANGE: Đếm theo giá trị logic
RANGE BETWEEN INTERVAL '2 days' PRECEDING AND CURRENT ROW
```

---

## 10. Aggregate Functions Với OVER

### 10.1. Tất Cả Aggregate Đều Dùng Được Với OVER

```sql
SELECT
    khu_vuc,
    san_pham,
    doanh_thu,
    -- Tổng
    SUM(doanh_thu) OVER (PARTITION BY khu_vuc) AS tong,
    -- Trung bình
    ROUND(AVG(doanh_thu) OVER (PARTITION BY khu_vuc), 2) AS avg,
    -- Đếm
    COUNT(*) OVER (PARTITION BY khu_vuc) AS so_sp,
    -- Min/Max
    MIN(doanh_thu) OVER (PARTITION BY khu_vuc) AS min,
    MAX(doanh_thu) OVER (PARTITION BY khu_vuc) AS max,
    -- Độ lệch chuẩn
    ROUND(STDDEV(doanh_thu) OVER (PARTITION BY khu_vuc), 2) AS stddev
FROM doanh_so;
```

### 10.2. Phần Trăm Lũy Kế

```sql
SELECT
    thang,
    doanh_thu,
    SUM(doanh_thu) OVER (ORDER BY thang) AS tong_luy_ke,
    SUM(doanh_thu) OVER () AS tong_nam,
    ROUND(
        SUM(doanh_thu) OVER (ORDER BY thang) * 100.0
        / SUM(doanh_thu) OVER (),
        2
    ) AS phan_tram_luy_ke
FROM doanh_so_thang;
```

Kết quả:
```
┌─────────┬──────────┬─────────────┬──────────┬──────────────────┐
│  thang  │ doanh_thu│ tong_luy_ke │ tong_nam │ phan_tram_luy_ke │
├─────────┼──────────┼─────────────┼──────────┼──────────────────┤
│ 2024-01 │     1000 │        1000 │     7800 │            12.82 │
│ 2024-02 │     1200 │        2200 │     7800 │            28.21 │
│ 2024-03 │     1100 │        3300 │     7800 │            42.31 │
│ 2024-04 │     1500 │        4800 │     7800 │            61.54 │
│ 2024-05 │     1400 │        6200 │     7800 │            79.49 │
│ 2024-06 │     1600 │        7800 │     7800 │           100.00 │
└─────────┴──────────┴─────────────┴──────────┴──────────────────┘
```

---

## 11. Ví Dụ Thực Tế

### 11.1. Phân Tích Doanh Số Ads Campaign

```sql
CREATE TABLE ads_campaign AS
SELECT * FROM (VALUES
    ('Campaign A', '2024-01-01', 100, 1000, 50),
    ('Campaign A', '2024-01-02', 120, 1100, 55),
    ('Campaign A', '2024-01-03', 90, 900, 45),
    ('Campaign A', '2024-01-04', 150, 1400, 70),
    ('Campaign A', '2024-01-05', 130, 1200, 65),
    ('Campaign B', '2024-01-01', 80, 850, 40),
    ('Campaign B', '2024-01-02', 95, 950, 48),
    ('Campaign B', '2024-01-03', 110, 1050, 55),
    ('Campaign B', '2024-01-04', 100, 1000, 50),
    ('Campaign B', '2024-01-05', 120, 1150, 60)
) AS t(campaign, ngay, spend, impressions, clicks);

-- Phân tích đầy đủ
SELECT
    campaign,
    ngay,
    spend,
    impressions,
    clicks,
    -- CTR
    ROUND(clicks * 100.0 / impressions, 2) AS ctr,
    -- So với ngày trước
    LAG(spend) OVER (PARTITION BY campaign ORDER BY ngay) AS spend_hom_truoc,
    spend - LAG(spend, 1, spend) OVER (PARTITION BY campaign ORDER BY ngay) AS spend_change,
    -- Tổng lũy kế
    SUM(spend) OVER (PARTITION BY campaign ORDER BY ngay) AS total_spend,
    -- Trung bình 3 ngày
    ROUND(AVG(clicks) OVER (
        PARTITION BY campaign
        ORDER BY ngay
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ), 2) AS avg_clicks_3d,
    -- Xếp hạng theo CTR trong ngày
    RANK() OVER (
        PARTITION BY ngay
        ORDER BY (clicks * 1.0 / impressions) DESC
    ) AS rank_ctr_ngay
FROM ads_campaign
ORDER BY ngay, campaign;
```

### 11.2. Phân Tích RFM (Recency, Frequency, Monetary)

```sql
CREATE TABLE orders AS
SELECT * FROM (VALUES
    ('KH001', '2024-01-15', 500),
    ('KH001', '2024-02-20', 800),
    ('KH001', '2024-03-10', 600),
    ('KH002', '2024-01-10', 1200),
    ('KH002', '2024-03-25', 1500),
    ('KH003', '2024-02-01', 300),
    ('KH003', '2024-02-15', 400),
    ('KH003', '2024-02-28', 350),
    ('KH003', '2024-03-15', 500)
) AS t(customer_id, order_date, amount);

WITH rfm_base AS (
    SELECT
        customer_id,
        MAX(order_date) AS last_order,
        COUNT(*) AS frequency,
        SUM(amount) AS monetary
    FROM orders
    GROUP BY customer_id
),
rfm_scores AS (
    SELECT
        customer_id,
        last_order,
        frequency,
        monetary,
        -- R Score: Gần đây nhất = điểm cao
        NTILE(3) OVER (ORDER BY last_order DESC) AS r_score,
        -- F Score: Mua nhiều = điểm cao
        NTILE(3) OVER (ORDER BY frequency DESC) AS f_score,
        -- M Score: Chi nhiều = điểm cao
        NTILE(3) OVER (ORDER BY monetary DESC) AS m_score
    FROM rfm_base
)
SELECT
    customer_id,
    last_order,
    frequency,
    monetary,
    r_score,
    f_score,
    m_score,
    r_score || f_score || m_score AS rfm_segment
FROM rfm_scores
ORDER BY monetary DESC;
```

### 11.3. Phát Hiện Anomaly (Giá Trị Bất Thường)

```sql
WITH stats AS (
    SELECT
        campaign,
        ngay,
        spend,
        AVG(spend) OVER (PARTITION BY campaign) AS avg_spend,
        STDDEV(spend) OVER (PARTITION BY campaign) AS stddev_spend
    FROM ads_campaign
)
SELECT
    campaign,
    ngay,
    spend,
    ROUND(avg_spend, 2) AS avg_spend,
    ROUND(stddev_spend, 2) AS stddev_spend,
    ROUND((spend - avg_spend) / NULLIF(stddev_spend, 0), 2) AS z_score,
    CASE
        WHEN ABS((spend - avg_spend) / NULLIF(stddev_spend, 0)) > 2 THEN 'Anomaly'
        WHEN ABS((spend - avg_spend) / NULLIF(stddev_spend, 0)) > 1 THEN 'Warning'
        ELSE 'Normal'
    END AS status
FROM stats
ORDER BY campaign, ngay;
```

---

## 12. Bài Tập Thực Hành

### Bài 1: Xếp Hạng Sản Phẩm
Cho bảng `products(category, name, sales)`. Viết query xếp hạng sản phẩm theo doanh số trong từng category.

<details>
<summary>Đáp án</summary>

```sql
SELECT
    category,
    name,
    sales,
    RANK() OVER (PARTITION BY category ORDER BY sales DESC) AS rank
FROM products;
```
</details>

### Bài 2: Tăng Trưởng Tháng
Cho bảng `monthly_sales(month, revenue)`. Tính % tăng trưởng so với tháng trước.

<details>
<summary>Đáp án</summary>

```sql
SELECT
    month,
    revenue,
    LAG(revenue) OVER (ORDER BY month) AS prev_month,
    ROUND(
        (revenue - LAG(revenue) OVER (ORDER BY month)) * 100.0
        / LAG(revenue) OVER (ORDER BY month),
        2
    ) AS growth_pct
FROM monthly_sales;
```
</details>

### Bài 3: Running Total
Cho bảng `transactions(date, amount)`. Tính tổng lũy kế theo ngày.

<details>
<summary>Đáp án</summary>

```sql
SELECT
    date,
    amount,
    SUM(amount) OVER (ORDER BY date) AS running_total
FROM transactions;
```
</details>

### Bài 4: Moving Average
Cho bảng `daily_metrics(date, value)`. Tính trung bình trượt 7 ngày.

<details>
<summary>Đáp án</summary>

```sql
SELECT
    date,
    value,
    ROUND(AVG(value) OVER (
        ORDER BY date
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ), 2) AS moving_avg_7d
FROM daily_metrics;
```
</details>

### Bài 5: Top N Per Group
Cho bảng `employees(department, name, salary)`. Lấy 3 nhân viên lương cao nhất mỗi phòng ban.

<details>
<summary>Đáp án</summary>

```sql
WITH ranked AS (
    SELECT
        department,
        name,
        salary,
        ROW_NUMBER() OVER (
            PARTITION BY department
            ORDER BY salary DESC
        ) AS rn
    FROM employees
)
SELECT department, name, salary
FROM ranked
WHERE rn <= 3;
```
</details>

---

## 🔑 Tóm Tắt Key Points

```
+------------------------------------------------------------------+
|                    WINDOW FUNCTIONS CHEAT SHEET                   |
+------------------------------------------------------------------+
|                                                                  |
|   RANKING:                                                       |
|   - ROW_NUMBER(): Số thứ tự duy nhất                             |
|   - RANK(): Cùng giá trị = cùng hạng, nhảy cóc                   |
|   - DENSE_RANK(): Cùng giá trị = cùng hạng, không nhảy           |
|   - NTILE(n): Chia thành n nhóm                                  |
|                                                                  |
|   VALUE ACCESS:                                                  |
|   - LAG(col, n): Giá trị n dòng TRƯỚC                            |
|   - LEAD(col, n): Giá trị n dòng SAU                             |
|   - FIRST_VALUE(col): Giá trị đầu tiên                           |
|   - LAST_VALUE(col): Giá trị cuối cùng (cần frame!)              |
|                                                                  |
|   AGGREGATES + OVER:                                             |
|   - SUM(), AVG(), COUNT(), MIN(), MAX(), STDDEV()                |
|   - Thêm ORDER BY = Running calculation                          |
|                                                                  |
|   FRAME (ROWS BETWEEN):                                          |
|   - UNBOUNDED PRECEDING: Từ đầu                                  |
|   - n PRECEDING: n dòng trước                                    |
|   - CURRENT ROW: Dòng hiện tại                                   |
|   - n FOLLOWING: n dòng sau                                      |
|   - UNBOUNDED FOLLOWING: Đến cuối                                |
|                                                                  |
+------------------------------------------------------------------+
```

---

**Tiếp theo:** [05 - PIVOT và UNPIVOT](./05-pivot-unpivot.md)

---

*DuckDB Tutorial - MangoAds Internal Training*
*"Học DuckDB qua ví dụ thực tế"*
