# PIVOT và UNPIVOT Trong DuckDB

> **📚 TÀI LIỆU HỌC DUCKDB - MANGOADS**
> Phiên bản 1.0 - Tháng 01/2026

---

## Mục Lục

1. [PIVOT Là Gì?](#1-pivot-là-gì)
2. [Cú Pháp PIVOT](#2-cú-pháp-pivot)
3. [PIVOT Cơ Bản](#3-pivot-cơ-bản)
4. [PIVOT Với Nhiều Aggregate Functions](#4-pivot-với-nhiều-aggregate-functions)
5. [PIVOT Với Nhiều Cột](#5-pivot-với-nhiều-cột)
6. [UNPIVOT - Chuyển Ngược Lại](#6-unpivot---chuyển-ngược-lại)
7. [Dynamic PIVOT](#7-dynamic-pivot)
8. [Ví Dụ Thực Tế Cho Digital Marketing](#8-ví-dụ-thực-tế-cho-digital-marketing)
9. [Tips và Best Practices](#9-tips-và-best-practices)
10. [Bài Tập Thực Hành](#10-bài-tập-thực-hành)

---

## 1. PIVOT Là Gì?

### 1.1. Khái Niệm

**PIVOT** là kỹ thuật chuyển đổi dữ liệu từ dạng **dọc (tall/long)** sang dạng **ngang (wide)**. Đây là tính năng rất mạnh của DuckDB, giúp tạo báo cáo dễ đọc.

```
DỮ LIỆU DỌC (Long Format):              DỮ LIỆU NGANG (Wide Format):
┌─────────┬─────────┬──────────┐        ┌─────────┬────────┬───────┬────────┐
│ khu_vuc │ san_pham│ doanh_thu│        │ khu_vuc │ Laptop │ Phone │ Tablet │
├─────────┼─────────┼──────────┤        ├─────────┼────────┼───────┼────────┤
│ Hà Nội  │ Laptop  │     1000 │   →    │ Hà Nội  │   1000 │   800 │    500 │
│ Hà Nội  │ Phone   │      800 │ PIVOT  │ TP.HCM  │   1200 │   900 │    600 │
│ Hà Nội  │ Tablet  │      500 │        └─────────┴────────┴───────┴────────┘
│ TP.HCM  │ Laptop  │     1200 │
│ TP.HCM  │ Phone   │      900 │
│ TP.HCM  │ Tablet  │      600 │
└─────────┴─────────┴──────────┘
```

### 1.2. Khi Nào Dùng PIVOT?

| Tình huống | PIVOT hữu ích |
|------------|---------------|
| Tạo bảng so sánh theo thời gian (cột = tháng/quý/năm) | ✅ |
| So sánh metrics giữa các campaigns | ✅ |
| Tạo cross-tab report | ✅ |
| Chuyển dữ liệu cho Excel/BI tools | ✅ |
| Phân tích xu hướng theo chiều ngang | ✅ |

---

## 2. Cú Pháp PIVOT

### 2.1. Cú Pháp Cơ Bản

```sql
PIVOT table_name
ON column_to_pivot
USING aggregate_function(value_column)
[GROUP BY grouping_columns]
```

### 2.2. Các Thành Phần

```
+------------------------------------------------------------------+
|                       CẤU TRÚC PIVOT                              |
+------------------------------------------------------------------+
|                                                                  |
|   PIVOT table_name                                               |
|         ↳ Bảng nguồn chứa dữ liệu                                |
|                                                                  |
|   ON column_to_pivot                                             |
|         ↳ Cột có giá trị sẽ trở thành TÊN CỘT mới               |
|                                                                  |
|   USING aggregate_function(value_column)                         |
|         ↳ Hàm tính toán giá trị cho mỗi ô                       |
|                                                                  |
|   GROUP BY grouping_columns                                      |
|         ↳ Cột giữ nguyên làm dòng (row headers)                 |
|                                                                  |
+------------------------------------------------------------------+
```

---

## 3. PIVOT Cơ Bản

### 3.1. Tạo Dữ Liệu Mẫu

```sql
CREATE TABLE doanh_so AS
SELECT * FROM (VALUES
    ('Hà Nội', 'Laptop', 1000),
    ('Hà Nội', 'Phone', 800),
    ('Hà Nội', 'Tablet', 500),
    ('TP.HCM', 'Laptop', 1200),
    ('TP.HCM', 'Phone', 900),
    ('TP.HCM', 'Tablet', 600),
    ('Đà Nẵng', 'Laptop', 700),
    ('Đà Nẵng', 'Phone', 550),
    ('Đà Nẵng', 'Tablet', 400)
) AS t(khu_vuc, san_pham, doanh_thu);
```

**📊 Bảng dữ liệu mẫu (dạng dọc - 9 dòng):**

| khu_vuc | san_pham | doanh_thu |
|---------|----------|-----------|
| Hà Nội | Laptop | 1000 |
| Hà Nội | Phone | 800 |
| Hà Nội | Tablet | 500 |
| TP.HCM | Laptop | 1200 |
| TP.HCM | Phone | 900 |
| TP.HCM | Tablet | 600 |
| Đà Nẵng | Laptop | 700 |
| Đà Nẵng | Phone | 550 |
| Đà Nẵng | Tablet | 400 |

### 3.2. PIVOT Đơn Giản

```sql
-- Chuyển sản phẩm thành cột
PIVOT doanh_so
ON san_pham
USING SUM(doanh_thu)
GROUP BY khu_vuc;
```

**📊 Kết quả (dạng ngang - 3 dòng):**

| khu_vuc | Laptop | Phone | Tablet |
|---------|--------|-------|--------|
| Đà Nẵng | 700 | 550 | 400 |
| Hà Nội | 1000 | 800 | 500 |
| TP.HCM | 1200 | 900 | 600 |

**🔍 Giải thích từng bước PIVOT:**

```
BƯỚC 1: Xác định các giá trị unique của cột ON (san_pham)
        → Laptop, Phone, Tablet → Trở thành TÊN CỘT mới

BƯỚC 2: Xác định các giá trị unique của GROUP BY (khu_vuc)
        → Đà Nẵng, Hà Nội, TP.HCM → Trở thành các DÒNG

BƯỚC 3: Với mỗi ô (khu_vuc, san_pham), apply USING SUM(doanh_thu)

        Ví dụ ô (Hà Nội, Laptop):
        - Tìm: WHERE khu_vuc='Hà Nội' AND san_pham='Laptop'
        - Kết quả: doanh_thu = 1000
        - SUM(1000) = 1000 → Điền vào ô

        Ví dụ ô (TP.HCM, Phone):
        - Tìm: WHERE khu_vuc='TP.HCM' AND san_pham='Phone'
        - Kết quả: doanh_thu = 900
        - SUM(900) = 900 → Điền vào ô
```

### 3.3. PIVOT Với IN - Chỉ Định Cột Cụ Thể

```sql
-- Chỉ pivot 2 sản phẩm cụ thể
PIVOT doanh_so
ON san_pham IN ('Laptop', 'Phone')
USING SUM(doanh_thu)
GROUP BY khu_vuc;
```

Kết quả:
```
┌─────────┬────────┬───────┐
│ khu_vuc │ Laptop │ Phone │
├─────────┼────────┼───────┤
│ Đà Nẵng │    700 │   550 │
│ Hà Nội  │   1000 │   800 │
│ TP.HCM  │   1200 │   900 │
└─────────┴────────┴───────┘
```

### 3.4. PIVOT Với Alias Cho Cột

```sql
PIVOT doanh_so
ON san_pham IN ('Laptop' AS laptop_sales, 'Phone' AS phone_sales)
USING SUM(doanh_thu)
GROUP BY khu_vuc;
```

---

## 4. PIVOT Với Nhiều Aggregate Functions

### 4.1. Nhiều Hàm Aggregate

```sql
-- Thêm dữ liệu có nhiều giao dịch
CREATE TABLE giao_dich AS
SELECT * FROM (VALUES
    ('Hà Nội', 'Laptop', 500),
    ('Hà Nội', 'Laptop', 600),
    ('Hà Nội', 'Phone', 300),
    ('Hà Nội', 'Phone', 400),
    ('Hà Nội', 'Phone', 100),
    ('TP.HCM', 'Laptop', 700),
    ('TP.HCM', 'Laptop', 500),
    ('TP.HCM', 'Phone', 450),
    ('TP.HCM', 'Phone', 350)
) AS t(khu_vuc, san_pham, gia_tri);

-- PIVOT với nhiều aggregate
PIVOT giao_dich
ON san_pham
USING
    SUM(gia_tri) AS tong,
    COUNT(*) AS so_luong,
    ROUND(AVG(gia_tri), 0) AS trung_binh
GROUP BY khu_vuc;
```

Kết quả:
```
┌─────────┬─────────────┬────────────────┬──────────────────┬────────────┬───────────────┬─────────────────┐
│ khu_vuc │ Laptop_tong │ Laptop_so_luong│ Laptop_trung_binh│ Phone_tong │ Phone_so_luong│ Phone_trung_binh│
├─────────┼─────────────┼────────────────┼──────────────────┼────────────┼───────────────┼─────────────────┤
│ Hà Nội  │        1100 │              2 │              550 │        800 │             3 │             267 │
│ TP.HCM  │        1200 │              2 │              600 │        800 │             2 │             400 │
└─────────┴─────────────┴────────────────┴──────────────────┴────────────┴───────────────┴─────────────────┘
```

---

## 5. PIVOT Với Nhiều Cột

### 5.1. Pivot Theo Thời Gian

```sql
CREATE TABLE doanh_so_quy AS
SELECT * FROM (VALUES
    ('2024', 'Q1', 'Hà Nội', 1000),
    ('2024', 'Q2', 'Hà Nội', 1200),
    ('2024', 'Q3', 'Hà Nội', 1100),
    ('2024', 'Q4', 'Hà Nội', 1400),
    ('2024', 'Q1', 'TP.HCM', 1500),
    ('2024', 'Q2', 'TP.HCM', 1600),
    ('2024', 'Q3', 'TP.HCM', 1550),
    ('2024', 'Q4', 'TP.HCM', 1800)
) AS t(nam, quy, khu_vuc, doanh_thu);

-- Pivot theo quý
PIVOT doanh_so_quy
ON quy
USING SUM(doanh_thu)
GROUP BY nam, khu_vuc
ORDER BY nam, khu_vuc;
```

Kết quả:
```
┌──────┬─────────┬──────┬──────┬──────┬──────┐
│ nam  │ khu_vuc │  Q1  │  Q2  │  Q3  │  Q4  │
├──────┼─────────┼──────┼──────┼──────┼──────┤
│ 2024 │ Hà Nội  │ 1000 │ 1200 │ 1100 │ 1400 │
│ 2024 │ TP.HCM  │ 1500 │ 1600 │ 1550 │ 1800 │
└──────┴─────────┴──────┴──────┴──────┴──────┘
```

### 5.2. PIVOT Lồng Nhau (Qua CTE)

```sql
-- Pivot 2 chiều: Sản phẩm và Quý
WITH pivot_san_pham AS (
    SELECT * FROM (VALUES
        ('Q1', 'Laptop', 1000),
        ('Q1', 'Phone', 800),
        ('Q2', 'Laptop', 1100),
        ('Q2', 'Phone', 850),
        ('Q3', 'Laptop', 1200),
        ('Q3', 'Phone', 900),
        ('Q4', 'Laptop', 1300),
        ('Q4', 'Phone', 950)
    ) AS t(quy, san_pham, doanh_thu)
)
PIVOT pivot_san_pham
ON quy
USING SUM(doanh_thu)
GROUP BY san_pham;
```

---

## 6. UNPIVOT - Chuyển Ngược Lại

### 6.1. Khái Niệm UNPIVOT

**UNPIVOT** làm ngược lại PIVOT: chuyển dữ liệu từ dạng **ngang** sang dạng **dọc**.

```
DỮ LIỆU NGANG:                          DỮ LIỆU DỌC:
┌─────────┬──────┬──────┬──────┐        ┌─────────┬─────┬───────┐
│ khu_vuc │  Q1  │  Q2  │  Q3  │        │ khu_vuc │ quy │ value │
├─────────┼──────┼──────┼──────┤        ├─────────┼─────┼───────┤
│ Hà Nội  │ 1000 │ 1200 │ 1100 │  →     │ Hà Nội  │ Q1  │  1000 │
│ TP.HCM  │ 1500 │ 1600 │ 1550 │UNPIVOT │ Hà Nội  │ Q2  │  1200 │
└─────────┴──────┴──────┴──────┘        │ Hà Nội  │ Q3  │  1100 │
                                        │ TP.HCM  │ Q1  │  1500 │
                                        │ TP.HCM  │ Q2  │  1600 │
                                        │ TP.HCM  │ Q3  │  1550 │
                                        └─────────┴─────┴───────┘
```

### 6.2. Cú Pháp UNPIVOT

```sql
UNPIVOT table_name
ON columns_to_unpivot
INTO
    NAME variable_name
    VALUE value_name
```

### 6.3. Ví Dụ UNPIVOT

```sql
-- Tạo bảng dạng wide
CREATE TABLE bao_cao_quy AS
SELECT * FROM (VALUES
    ('Hà Nội', 1000, 1200, 1100, 1400),
    ('TP.HCM', 1500, 1600, 1550, 1800),
    ('Đà Nẵng', 700, 800, 750, 900)
) AS t(khu_vuc, Q1, Q2, Q3, Q4);

-- UNPIVOT
UNPIVOT bao_cao_quy
ON Q1, Q2, Q3, Q4
INTO
    NAME quy
    VALUE doanh_thu;
```

Kết quả:
```
┌─────────┬─────┬──────────┐
│ khu_vuc │ quy │ doanh_thu│
├─────────┼─────┼──────────┤
│ Hà Nội  │ Q1  │     1000 │
│ Hà Nội  │ Q2  │     1200 │
│ Hà Nội  │ Q3  │     1100 │
│ Hà Nội  │ Q4  │     1400 │
│ TP.HCM  │ Q1  │     1500 │
│ TP.HCM  │ Q2  │     1600 │
│ ...     │ ... │      ... │
└─────────┴─────┴──────────┘
```

### 6.4. UNPIVOT Nhiều Cột

```sql
CREATE TABLE metrics_wide AS
SELECT * FROM (VALUES
    ('Campaign A', 100, 1000, 50, 5000),
    ('Campaign B', 150, 1200, 75, 6000)
) AS t(campaign, clicks_q1, impressions_q1, clicks_q2, impressions_q2);

-- UNPIVOT với pattern matching
UNPIVOT metrics_wide
ON COLUMNS(* EXCLUDE campaign)
INTO
    NAME metric_period
    VALUE value;
```

---

## 7. Dynamic PIVOT

### 7.1. Vấn Đề Với PIVOT Tĩnh

Khi không biết trước danh sách giá trị cần pivot, ta cần **Dynamic PIVOT**.

### 7.2. Sử Dụng Macro

```sql
-- DuckDB hỗ trợ dynamic PIVOT thông qua macro
-- Khi không chỉ định IN, DuckDB tự động lấy tất cả giá trị

CREATE TABLE dynamic_data AS
SELECT * FROM (VALUES
    ('A', 'cat1', 10),
    ('A', 'cat2', 20),
    ('A', 'cat3', 30),
    ('B', 'cat1', 15),
    ('B', 'cat2', 25),
    ('B', 'cat4', 35)  -- cat4 chỉ có ở B
) AS t(id, category, value);

-- Dynamic: Tự động pivot tất cả categories
PIVOT dynamic_data
ON category
USING SUM(value)
GROUP BY id;
```

Kết quả:
```
┌────┬──────┬──────┬──────┬──────┐
│ id │ cat1 │ cat2 │ cat3 │ cat4 │
├────┼──────┼──────┼──────┼──────┤
│ A  │   10 │   20 │   30 │ NULL │
│ B  │   15 │   25 │ NULL │   35 │
└────┴──────┴──────┴──────┴──────┘
```

### 7.3. Xử Lý NULL Trong PIVOT

```sql
-- Thay NULL bằng 0
SELECT
    id,
    COALESCE(cat1, 0) AS cat1,
    COALESCE(cat2, 0) AS cat2,
    COALESCE(cat3, 0) AS cat3,
    COALESCE(cat4, 0) AS cat4
FROM (
    PIVOT dynamic_data
    ON category
    USING SUM(value)
    GROUP BY id
);
```

---

## 8. Ví Dụ Thực Tế Cho Digital Marketing

### 8.1. Báo Cáo Performance Theo Tháng

```sql
CREATE TABLE ads_performance AS
SELECT * FROM (VALUES
    ('Campaign A', '2024-01', 1000, 50000, 500),
    ('Campaign A', '2024-02', 1200, 55000, 600),
    ('Campaign A', '2024-03', 1100, 52000, 550),
    ('Campaign B', '2024-01', 800, 40000, 400),
    ('Campaign B', '2024-02', 900, 45000, 450),
    ('Campaign B', '2024-03', 950, 47000, 480),
    ('Campaign C', '2024-01', 1500, 70000, 700),
    ('Campaign C', '2024-02', 1600, 75000, 750),
    ('Campaign C', '2024-03', 1700, 80000, 800)
) AS t(campaign, thang, spend, impressions, clicks);

-- Báo cáo spend theo tháng
PIVOT ads_performance
ON thang
USING SUM(spend) AS spend
GROUP BY campaign
ORDER BY campaign;
```

Kết quả:
```
┌────────────┬──────────────────┬──────────────────┬──────────────────┐
│  campaign  │ 2024-01_spend    │ 2024-02_spend    │ 2024-03_spend    │
├────────────┼──────────────────┼──────────────────┼──────────────────┤
│ Campaign A │             1000 │             1200 │             1100 │
│ Campaign B │              800 │              900 │              950 │
│ Campaign C │             1500 │             1600 │             1700 │
└────────────┴──────────────────┴──────────────────┴──────────────────┘
```

### 8.2. Báo Cáo Multi-Metric

```sql
-- Báo cáo nhiều metrics
PIVOT ads_performance
ON thang
USING
    SUM(spend) AS spend,
    SUM(clicks) AS clicks,
    ROUND(SUM(clicks) * 100.0 / SUM(impressions), 2) AS ctr
GROUP BY campaign;
```

### 8.3. So Sánh Kênh Marketing

```sql
CREATE TABLE channel_performance AS
SELECT * FROM (VALUES
    ('Facebook', 'Q1', 5000, 250),
    ('Facebook', 'Q2', 5500, 280),
    ('Facebook', 'Q3', 6000, 300),
    ('Google', 'Q1', 8000, 400),
    ('Google', 'Q2', 8500, 420),
    ('Google', 'Q3', 9000, 450),
    ('TikTok', 'Q1', 3000, 200),
    ('TikTok', 'Q2', 4000, 280),
    ('TikTok', 'Q3', 5000, 350)
) AS t(channel, quarter, spend, conversions);

-- Pivot để so sánh channels
PIVOT channel_performance
ON channel
USING
    SUM(spend) AS spend,
    SUM(conversions) AS conv,
    ROUND(SUM(spend) / NULLIF(SUM(conversions), 0), 2) AS cpa
GROUP BY quarter
ORDER BY quarter;
```

### 8.4. Cohort Analysis Pivot

```sql
CREATE TABLE user_cohort AS
SELECT * FROM (VALUES
    ('2024-01', 'week_0', 1000),
    ('2024-01', 'week_1', 700),
    ('2024-01', 'week_2', 500),
    ('2024-01', 'week_3', 400),
    ('2024-02', 'week_0', 1200),
    ('2024-02', 'week_1', 840),
    ('2024-02', 'week_2', 600),
    ('2024-02', 'week_3', 480),
    ('2024-03', 'week_0', 1100),
    ('2024-03', 'week_1', 770),
    ('2024-03', 'week_2', 550),
    ('2024-03', 'week_3', 440)
) AS t(cohort_month, week, active_users);

-- Cohort retention table
WITH retention AS (
    SELECT
        cohort_month,
        week,
        active_users,
        FIRST_VALUE(active_users) OVER (
            PARTITION BY cohort_month
            ORDER BY week
        ) AS initial_users
    FROM user_cohort
)
SELECT
    cohort_month,
    week,
    ROUND(active_users * 100.0 / initial_users, 1) AS retention_rate
FROM retention;

-- Pivot thành bảng retention
PIVOT (
    SELECT
        cohort_month,
        week,
        ROUND(active_users * 100.0 / FIRST_VALUE(active_users) OVER (
            PARTITION BY cohort_month ORDER BY week
        ), 1) AS retention_rate
    FROM user_cohort
)
ON week
USING MAX(retention_rate)
GROUP BY cohort_month
ORDER BY cohort_month;
```

Kết quả:
```
┌──────────────┬────────┬────────┬────────┬────────┐
│ cohort_month │ week_0 │ week_1 │ week_2 │ week_3 │
├──────────────┼────────┼────────┼────────┼────────┤
│ 2024-01      │  100.0 │   70.0 │   50.0 │   40.0 │
│ 2024-02      │  100.0 │   70.0 │   50.0 │   40.0 │
│ 2024-03      │  100.0 │   70.0 │   50.0 │   40.0 │
└──────────────┴────────┴────────┴────────┴────────┘
```

### 8.5. Cross-Tab: Platform x Device

```sql
CREATE TABLE platform_device AS
SELECT * FROM (VALUES
    ('Facebook', 'Mobile', 5000),
    ('Facebook', 'Desktop', 2000),
    ('Facebook', 'Tablet', 1000),
    ('Google', 'Mobile', 4000),
    ('Google', 'Desktop', 6000),
    ('Google', 'Tablet', 800),
    ('TikTok', 'Mobile', 7000),
    ('TikTok', 'Desktop', 500),
    ('TikTok', 'Tablet', 300)
) AS t(platform, device, impressions);

-- Cross-tab với tổng hàng/cột
WITH pivoted AS (
    PIVOT platform_device
    ON device
    USING SUM(impressions)
    GROUP BY platform
)
SELECT
    platform,
    Mobile,
    Desktop,
    Tablet,
    Mobile + Desktop + Tablet AS total
FROM pivoted

UNION ALL

SELECT
    'TOTAL' AS platform,
    SUM(Mobile),
    SUM(Desktop),
    SUM(Tablet),
    SUM(Mobile) + SUM(Desktop) + SUM(Tablet)
FROM pivoted

ORDER BY
    CASE WHEN platform = 'TOTAL' THEN 1 ELSE 0 END,
    platform;
```

Kết quả:
```
┌──────────┬────────┬─────────┬────────┬───────┐
│ platform │ Mobile │ Desktop │ Tablet │ total │
├──────────┼────────┼─────────┼────────┼───────┤
│ Facebook │   5000 │    2000 │   1000 │  8000 │
│ Google   │   4000 │    6000 │    800 │ 10800 │
│ TikTok   │   7000 │     500 │    300 │  7800 │
│ TOTAL    │  16000 │    8500 │   2100 │ 26600 │
└──────────┴────────┴─────────┴────────┴───────┘
```

---

## 9. Tips và Best Practices

### 9.1. Khi Nào Dùng PIVOT vs Không

```
+------------------------------------------------------------------+
|                      KHI NÀO DÙNG PIVOT?                          |
+------------------------------------------------------------------+
|                                                                  |
|   ✅ NÊN DÙNG:                                                   |
|   - Tạo báo cáo cho người dùng cuối                              |
|   - Export ra Excel/BI tools                                     |
|   - So sánh theo thời gian (cột = tháng/năm)                     |
|   - Tạo cross-tab reports                                        |
|                                                                  |
|   ❌ KHÔNG NÊN DÙNG:                                             |
|   - Dữ liệu cần join với bảng khác                               |
|   - Dữ liệu cần filter động                                      |
|   - Số lượng cột pivot quá lớn (>50)                             |
|   - Cần tính toán phức tạp sau đó                                |
|                                                                  |
+------------------------------------------------------------------+
```

### 9.2. Performance Tips

```sql
-- ✅ TỐT: Filter trước khi pivot
PIVOT (
    SELECT * FROM big_table
    WHERE year = 2024
)
ON month
USING SUM(value)
GROUP BY category;

-- ❌ KHÔNG TỐT: Pivot toàn bộ rồi filter
SELECT * FROM (
    PIVOT big_table
    ON month
    USING SUM(value)
    GROUP BY category, year
)
WHERE year = 2024;
```

### 9.3. Xử Lý Tên Cột Đặc Biệt

```sql
-- Nếu giá trị pivot có ký tự đặc biệt
PIVOT data
ON status IN (
    'in-progress' AS in_progress,
    'not started' AS not_started,
    'completed' AS completed
)
USING COUNT(*)
GROUP BY category;
```

### 9.4. PIVOT + Window Functions

```sql
-- Kết hợp PIVOT với tính toán thêm
WITH monthly_data AS (
    PIVOT ads_performance
    ON thang
    USING SUM(spend) AS spend
    GROUP BY campaign
)
SELECT
    campaign,
    "2024-01_spend",
    "2024-02_spend",
    "2024-03_spend",
    -- Tính growth rate
    ROUND(
        ("2024-03_spend" - "2024-01_spend") * 100.0 / "2024-01_spend",
        2
    ) AS growth_q1_to_q3
FROM monthly_data;
```

---

## 10. Bài Tập Thực Hành

### Bài 1: PIVOT Cơ Bản
Cho bảng `sales(region, product, amount)`. Tạo báo cáo với region là dòng, product là cột.

<details>
<summary>Đáp án</summary>

```sql
PIVOT sales
ON product
USING SUM(amount)
GROUP BY region;
```
</details>

### Bài 2: PIVOT Theo Thời Gian
Cho bảng `monthly_revenue(company, month, revenue)`. Tạo báo cáo so sánh revenue các tháng.

<details>
<summary>Đáp án</summary>

```sql
PIVOT monthly_revenue
ON month
USING SUM(revenue)
GROUP BY company
ORDER BY company;
```
</details>

### Bài 3: UNPIVOT
Cho bảng `wide_data(id, jan, feb, mar, apr)`. Chuyển về dạng `(id, month, value)`.

<details>
<summary>Đáp án</summary>

```sql
UNPIVOT wide_data
ON jan, feb, mar, apr
INTO
    NAME month
    VALUE value;
```
</details>

### Bài 4: PIVOT Với Nhiều Aggregates
Cho bảng `orders(category, status, amount)`. Tạo báo cáo với count và sum theo status.

<details>
<summary>Đáp án</summary>

```sql
PIVOT orders
ON status
USING
    COUNT(*) AS count,
    SUM(amount) AS total
GROUP BY category;
```
</details>

### Bài 5: Cross-Tab Với Totals
Tạo bảng cross-tab có tổng hàng và tổng cột.

<details>
<summary>Đáp án</summary>

```sql
WITH base AS (
    PIVOT data
    ON col_dimension
    USING SUM(value)
    GROUP BY row_dimension
),
with_row_total AS (
    SELECT
        row_dimension,
        col1, col2, col3,
        col1 + col2 + col3 AS total
    FROM base
)
SELECT * FROM with_row_total
UNION ALL
SELECT
    'TOTAL',
    SUM(col1), SUM(col2), SUM(col3),
    SUM(col1) + SUM(col2) + SUM(col3)
FROM with_row_total;
```
</details>

---

## 🔑 Tóm Tắt PIVOT/UNPIVOT

```
+------------------------------------------------------------------+
|                    PIVOT/UNPIVOT CHEAT SHEET                      |
+------------------------------------------------------------------+
|                                                                  |
|   PIVOT (Dọc → Ngang):                                           |
|   PIVOT table                                                    |
|   ON column_to_become_headers                                    |
|   USING aggregate(value)                                         |
|   GROUP BY row_headers;                                          |
|                                                                  |
|   UNPIVOT (Ngang → Dọc):                                         |
|   UNPIVOT table                                                  |
|   ON column1, column2, ...                                       |
|   INTO NAME col_name VALUE val_name;                             |
|                                                                  |
|   TIPS:                                                          |
|   - Dùng IN (...) để chỉ định cột cụ thể                         |
|   - Dùng AS để đặt tên cột                                       |
|   - Filter TRƯỚC khi pivot để tăng performance                   |
|   - Dùng COALESCE để xử lý NULL                                  |
|   - Kết hợp với CTE cho queries phức tạp                         |
|                                                                  |
+------------------------------------------------------------------+
```

---

**Tiếp theo:** [06 - Thống Kê Nâng Cao](./06-thong-ke-nang-cao.md)

---

*DuckDB Tutorial - MangoAds Internal Training*
*"Học DuckDB qua ví dụ thực tế"*
