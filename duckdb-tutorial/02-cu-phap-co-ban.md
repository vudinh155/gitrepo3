# Cú Pháp SQL Cơ Bản

> **Phần 2:** SELECT, WHERE, ORDER BY, JOIN, GROUP BY

---

## Dữ Liệu Mẫu

Trước khi bắt đầu, hãy tạo dữ liệu mẫu:

```sql
-- Bảng nhân viên
CREATE TABLE nhan_vien (
    id INTEGER PRIMARY KEY,
    ho_ten VARCHAR(100),
    phong_ban VARCHAR(50),
    luong INTEGER,
    ngay_vao DATE
);

INSERT INTO nhan_vien VALUES
    (1, 'Nguyễn Văn An', 'IT', 15000000, '2020-01-15'),
    (2, 'Trần Thị Bình', 'Marketing', 12000000, '2019-06-20'),
    (3, 'Lê Văn Cường', 'IT', 18000000, '2018-03-10'),
    (4, 'Phạm Thị Dung', 'HR', 10000000, '2021-09-01'),
    (5, 'Hoàng Văn Em', 'Marketing', 14000000, '2020-11-15'),
    (6, 'Vũ Thị Phương', 'IT', 16000000, '2019-08-22'),
    (7, 'Đặng Văn Giang', 'Sales', 13000000, '2020-04-10'),
    (8, 'Bùi Thị Hoa', 'Sales', 11000000, '2021-02-28');

-- Bảng doanh số
CREATE TABLE doanh_so (
    id INTEGER,
    nhan_vien_id INTEGER,
    san_pham VARCHAR(50),
    so_luong INTEGER,
    don_gia INTEGER,
    ngay_ban DATE
);

INSERT INTO doanh_so VALUES
    (1, 7, 'Laptop', 2, 20000000, '2024-01-05'),
    (2, 7, 'Mouse', 10, 500000, '2024-01-10'),
    (3, 8, 'Laptop', 1, 20000000, '2024-01-12'),
    (4, 7, 'Keyboard', 5, 1000000, '2024-01-15'),
    (5, 8, 'Monitor', 3, 5000000, '2024-01-18'),
    (6, 7, 'Laptop', 1, 20000000, '2024-02-01'),
    (7, 8, 'Mouse', 20, 500000, '2024-02-05'),
    (8, 7, 'Monitor', 2, 5000000, '2024-02-10');
```

---

## 1. SELECT - Truy Vấn Dữ Liệu

### 1.1. Cú Pháp Cơ Bản

```sql
-- Lấy tất cả cột
SELECT * FROM nhan_vien;

-- Lấy một số cột
SELECT ho_ten, phong_ban, luong FROM nhan_vien;

-- Đặt tên alias cho cột
SELECT
    ho_ten AS ten_nhan_vien,
    luong AS muc_luong,
    luong * 12 AS luong_nam
FROM nhan_vien;
```

### 1.2. DISTINCT - Loại Bỏ Trùng Lặp

```sql
-- Danh sách phòng ban (không trùng)
SELECT DISTINCT phong_ban FROM nhan_vien;
```

Kết quả:
```
┌───────────┐
│ phong_ban │
├───────────┤
│ IT        │
│ Marketing │
│ HR        │
│ Sales     │
└───────────┘
```

### 1.3. LIMIT và OFFSET

```sql
-- Lấy 3 dòng đầu tiên
SELECT * FROM nhan_vien LIMIT 3;

-- Lấy 3 dòng, bỏ qua 2 dòng đầu
SELECT * FROM nhan_vien LIMIT 3 OFFSET 2;

-- Cú pháp khác (tương đương)
SELECT * FROM nhan_vien LIMIT 2, 3;  -- offset 2, limit 3
```

---

## 2. WHERE - Lọc Dữ Liệu

### 2.1. Toán Tử So Sánh

| Toán tử | Ý nghĩa |
|---------|---------|
| `=` | Bằng |
| `<>` hoặc `!=` | Khác |
| `<` | Nhỏ hơn |
| `>` | Lớn hơn |
| `<=` | Nhỏ hơn hoặc bằng |
| `>=` | Lớn hơn hoặc bằng |

```sql
-- Nhân viên lương >= 15 triệu
SELECT * FROM nhan_vien WHERE luong >= 15000000;

-- Nhân viên phòng IT
SELECT * FROM nhan_vien WHERE phong_ban = 'IT';

-- Nhân viên không phải phòng IT
SELECT * FROM nhan_vien WHERE phong_ban <> 'IT';
```

### 2.2. Toán Tử Logic: AND, OR, NOT

```sql
-- Phòng IT VÀ lương >= 16 triệu
SELECT * FROM nhan_vien
WHERE phong_ban = 'IT' AND luong >= 16000000;

-- Phòng IT HOẶC phòng Marketing
SELECT * FROM nhan_vien
WHERE phong_ban = 'IT' OR phong_ban = 'Marketing';

-- KHÔNG phải phòng HR
SELECT * FROM nhan_vien
WHERE NOT phong_ban = 'HR';

-- Kết hợp (dùng ngoặc để rõ ràng)
SELECT * FROM nhan_vien
WHERE (phong_ban = 'IT' OR phong_ban = 'Marketing')
  AND luong >= 14000000;
```

### 2.3. BETWEEN - Trong Khoảng

```sql
-- Lương từ 12 đến 16 triệu
SELECT * FROM nhan_vien
WHERE luong BETWEEN 12000000 AND 16000000;

-- Tương đương với:
SELECT * FROM nhan_vien
WHERE luong >= 12000000 AND luong <= 16000000;

-- Ngày vào làm trong năm 2020
SELECT * FROM nhan_vien
WHERE ngay_vao BETWEEN '2020-01-01' AND '2020-12-31';
```

### 2.4. IN - Trong Danh Sách

```sql
-- Phòng IT hoặc Marketing hoặc Sales
SELECT * FROM nhan_vien
WHERE phong_ban IN ('IT', 'Marketing', 'Sales');

-- Tương đương với:
SELECT * FROM nhan_vien
WHERE phong_ban = 'IT'
   OR phong_ban = 'Marketing'
   OR phong_ban = 'Sales';

-- NOT IN
SELECT * FROM nhan_vien
WHERE phong_ban NOT IN ('HR');
```

### 2.5. LIKE - Tìm Kiếm Chuỗi

| Ký tự | Ý nghĩa |
|-------|---------|
| `%` | Bất kỳ chuỗi nào (0 hoặc nhiều ký tự) |
| `_` | Đúng 1 ký tự |

```sql
-- Tên bắt đầu bằng 'Nguyễn'
SELECT * FROM nhan_vien WHERE ho_ten LIKE 'Nguyễn%';

-- Tên chứa 'Văn'
SELECT * FROM nhan_vien WHERE ho_ten LIKE '%Văn%';

-- Tên kết thúc bằng 'ng'
SELECT * FROM nhan_vien WHERE ho_ten LIKE '%ng';

-- Tên có đúng 12 ký tự
SELECT * FROM nhan_vien WHERE ho_ten LIKE '____________';

-- ILIKE: không phân biệt hoa thường
SELECT * FROM nhan_vien WHERE ho_ten ILIKE '%van%';
```

### 2.6. IS NULL / IS NOT NULL

```sql
-- Tìm giá trị NULL (không dùng = NULL)
SELECT * FROM nhan_vien WHERE phong_ban IS NULL;

-- Tìm giá trị không NULL
SELECT * FROM nhan_vien WHERE phong_ban IS NOT NULL;
```

---

## 3. ORDER BY - Sắp Xếp

### 3.1. Sắp Xếp Cơ Bản

```sql
-- Sắp xếp theo lương tăng dần (mặc định)
SELECT * FROM nhan_vien ORDER BY luong;

-- Sắp xếp theo lương giảm dần
SELECT * FROM nhan_vien ORDER BY luong DESC;

-- Sắp xếp theo tên (alphabetical)
SELECT * FROM nhan_vien ORDER BY ho_ten;
```

### 3.2. Sắp Xếp Nhiều Cột

```sql
-- Sắp xếp theo phòng ban, trong mỗi phòng sắp xếp theo lương giảm dần
SELECT * FROM nhan_vien
ORDER BY phong_ban ASC, luong DESC;
```

Kết quả:
```
┌────┬───────────────┬───────────┬──────────┬────────────┐
│ id │    ho_ten     │ phong_ban │   luong  │  ngay_vao  │
├────┼───────────────┼───────────┼──────────┼────────────┤
│ 4  │ Phạm Thị Dung │ HR        │ 10000000 │ 2021-09-01 │
│ 3  │ Lê Văn Cường  │ IT        │ 18000000 │ 2018-03-10 │
│ 6  │ Vũ Thị Phương │ IT        │ 16000000 │ 2019-08-22 │
│ 1  │ Nguyễn Văn An │ IT        │ 15000000 │ 2020-01-15 │
│ 5  │ Hoàng Văn Em  │ Marketing │ 14000000 │ 2020-11-15 │
│ 2  │ Trần Thị Bình │ Marketing │ 12000000 │ 2019-06-20 │
│ 7  │ Đặng Văn Giang│ Sales     │ 13000000 │ 2020-04-10 │
│ 8  │ Bùi Thị Hoa   │ Sales     │ 11000000 │ 2021-02-28 │
└────┴───────────────┴───────────┴──────────┴────────────┘
```

### 3.3. Sắp Xếp Theo Vị Trí Cột

```sql
-- Sắp xếp theo cột thứ 3 (phong_ban)
SELECT ho_ten, luong, phong_ban FROM nhan_vien
ORDER BY 3;

-- Sắp xếp theo cột thứ 2 giảm dần
SELECT ho_ten, luong FROM nhan_vien
ORDER BY 2 DESC;
```

### 3.4. NULLS FIRST / NULLS LAST

```sql
-- NULL xếp đầu
SELECT * FROM nhan_vien ORDER BY phong_ban NULLS FIRST;

-- NULL xếp cuối
SELECT * FROM nhan_vien ORDER BY phong_ban NULLS LAST;
```

---

## 4. JOIN - Nối Bảng

### 4.1. Các Loại JOIN

```
┌─────────────────────────────────────────────────────────────┐
│                     CÁC LOẠI JOIN                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  INNER JOIN     LEFT JOIN      RIGHT JOIN     FULL JOIN     │
│    ┌───┐          ┌───┐          ┌───┐          ┌───┐       │
│   ╱ A∩B ╲        ╱█████╲        ╱█████╲        ╱█████╲      │
│  │  ███  │      │ █████ │      │ █████ │      │█████│█      │
│   ╲     ╱        ╲█████╱        ╲█████╱        ╲█████╱      │
│    └───┘          └───┘          └───┘          └───┘       │
│                                                             │
│  Chỉ lấy       Lấy tất cả    Lấy tất cả     Lấy tất cả     │
│  phần chung    bên trái      bên phải       cả hai bên     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 4.2. INNER JOIN

Chỉ lấy các dòng có match ở cả hai bảng.

```sql
-- Doanh số với tên nhân viên
SELECT
    d.id AS ma_don,
    n.ho_ten,
    d.san_pham,
    d.so_luong,
    d.don_gia,
    d.so_luong * d.don_gia AS thanh_tien
FROM doanh_so d
INNER JOIN nhan_vien n ON d.nhan_vien_id = n.id;
```

Kết quả:
```
┌────────┬────────────────┬──────────┬──────────┬──────────┬────────────┐
│ ma_don │     ho_ten     │ san_pham │ so_luong │  don_gia │ thanh_tien │
├────────┼────────────────┼──────────┼──────────┼──────────┼────────────┤
│ 1      │ Đặng Văn Giang │ Laptop   │ 2        │ 20000000 │ 40000000   │
│ 2      │ Đặng Văn Giang │ Mouse    │ 10       │ 500000   │ 5000000    │
│ 3      │ Bùi Thị Hoa    │ Laptop   │ 1        │ 20000000 │ 20000000   │
│ ...    │ ...            │ ...      │ ...      │ ...      │ ...        │
└────────┴────────────────┴──────────┴──────────┴──────────┴────────────┘
```

### 4.3. LEFT JOIN

Lấy tất cả từ bảng trái, match với bảng phải (NULL nếu không match).

```sql
-- Tất cả nhân viên và doanh số (nếu có)
SELECT
    n.ho_ten,
    n.phong_ban,
    COUNT(d.id) AS so_don,
    COALESCE(SUM(d.so_luong * d.don_gia), 0) AS tong_doanh_so
FROM nhan_vien n
LEFT JOIN doanh_so d ON n.id = d.nhan_vien_id
GROUP BY n.id, n.ho_ten, n.phong_ban;
```

### 4.4. RIGHT JOIN và FULL JOIN

```sql
-- RIGHT JOIN (ít dùng, đổi thứ tự bảng dùng LEFT JOIN)
SELECT * FROM doanh_so d
RIGHT JOIN nhan_vien n ON d.nhan_vien_id = n.id;

-- FULL OUTER JOIN
SELECT * FROM nhan_vien n
FULL OUTER JOIN doanh_so d ON n.id = d.nhan_vien_id;
```

### 4.5. CROSS JOIN

Tích Descartes - mỗi dòng bảng A ghép với mọi dòng bảng B.

```sql
-- Mọi kết hợp nhân viên - sản phẩm
SELECT n.ho_ten, d.san_pham
FROM nhan_vien n
CROSS JOIN (SELECT DISTINCT san_pham FROM doanh_so) d;
```

### 4.6. Self JOIN

Join bảng với chính nó.

```sql
-- Tìm nhân viên cùng phòng ban
SELECT
    a.ho_ten AS nhan_vien_1,
    b.ho_ten AS nhan_vien_2,
    a.phong_ban
FROM nhan_vien a
JOIN nhan_vien b ON a.phong_ban = b.phong_ban AND a.id < b.id;
```

---

## 5. GROUP BY - Nhóm Dữ Liệu

### 5.1. Cú Pháp Cơ Bản

```sql
-- Đếm số nhân viên mỗi phòng ban
SELECT
    phong_ban,
    COUNT(*) AS so_nhan_vien
FROM nhan_vien
GROUP BY phong_ban;
```

Kết quả:
```
┌───────────┬──────────────┐
│ phong_ban │ so_nhan_vien │
├───────────┼──────────────┤
│ HR        │ 1            │
│ IT        │ 3            │
│ Marketing │ 2            │
│ Sales     │ 2            │
└───────────┴──────────────┘
```

### 5.2. GROUP BY Với Nhiều Cột

```sql
-- Doanh số theo nhân viên và sản phẩm
SELECT
    nhan_vien_id,
    san_pham,
    COUNT(*) AS so_don,
    SUM(so_luong) AS tong_so_luong,
    SUM(so_luong * don_gia) AS tong_tien
FROM doanh_so
GROUP BY nhan_vien_id, san_pham
ORDER BY nhan_vien_id, san_pham;
```

### 5.3. HAVING - Lọc Sau Khi Nhóm

**Khác biệt WHERE vs HAVING:**
- `WHERE`: Lọc TRƯỚC khi nhóm (lọc dòng)
- `HAVING`: Lọc SAU khi nhóm (lọc nhóm)

```sql
-- Phòng ban có >= 2 nhân viên
SELECT
    phong_ban,
    COUNT(*) AS so_nhan_vien,
    AVG(luong) AS luong_tb
FROM nhan_vien
GROUP BY phong_ban
HAVING COUNT(*) >= 2;
```

```sql
-- Kết hợp WHERE và HAVING
SELECT
    phong_ban,
    COUNT(*) AS so_nhan_vien,
    AVG(luong) AS luong_tb
FROM nhan_vien
WHERE ngay_vao >= '2019-01-01'  -- Lọc trước khi nhóm
GROUP BY phong_ban
HAVING AVG(luong) >= 13000000;  -- Lọc sau khi nhóm
```

### 5.4. GROUP BY ALL (Tính năng DuckDB)

DuckDB có thể tự động group by tất cả cột không aggregate.

```sql
-- Thay vì viết:
SELECT phong_ban, COUNT(*) FROM nhan_vien GROUP BY phong_ban;

-- Có thể viết:
SELECT phong_ban, COUNT(*) FROM nhan_vien GROUP BY ALL;
```

---

## 6. UNION - Gộp Kết Quả

### 6.1. UNION vs UNION ALL

```sql
-- UNION: Loại bỏ trùng lặp
SELECT phong_ban FROM nhan_vien WHERE luong > 15000000
UNION
SELECT phong_ban FROM nhan_vien WHERE ngay_vao < '2019-01-01';

-- UNION ALL: Giữ nguyên (nhanh hơn)
SELECT phong_ban FROM nhan_vien WHERE luong > 15000000
UNION ALL
SELECT phong_ban FROM nhan_vien WHERE ngay_vao < '2019-01-01';
```

### 6.2. INTERSECT và EXCEPT

```sql
-- INTERSECT: Phần giao (có ở cả hai)
SELECT phong_ban FROM nhan_vien WHERE luong > 13000000
INTERSECT
SELECT phong_ban FROM nhan_vien WHERE ngay_vao >= '2020-01-01';

-- EXCEPT: Phần hiệu (có ở query 1, không có ở query 2)
SELECT phong_ban FROM nhan_vien
EXCEPT
SELECT DISTINCT 'HR';
```

---

## 7. Biểu Thức CASE

### 7.1. CASE WHEN Cơ Bản

```sql
SELECT
    ho_ten,
    luong,
    CASE
        WHEN luong >= 16000000 THEN 'Cao'
        WHEN luong >= 12000000 THEN 'Trung bình'
        ELSE 'Thấp'
    END AS muc_luong
FROM nhan_vien;
```

Kết quả:
```
┌───────────────┬──────────┬────────────┐
│    ho_ten     │   luong  │  muc_luong │
├───────────────┼──────────┼────────────┤
│ Nguyễn Văn An │ 15000000 │ Trung bình │
│ Trần Thị Bình │ 12000000 │ Trung bình │
│ Lê Văn Cường  │ 18000000 │ Cao        │
│ Phạm Thị Dung │ 10000000 │ Thấp       │
│ ...           │ ...      │ ...        │
└───────────────┴──────────┴────────────┘
```

### 7.2. CASE Trong Aggregate

```sql
-- Đếm theo điều kiện
SELECT
    COUNT(CASE WHEN luong >= 15000000 THEN 1 END) AS luong_cao,
    COUNT(CASE WHEN luong < 15000000 THEN 1 END) AS luong_thap
FROM nhan_vien;

-- Tổng theo điều kiện
SELECT
    SUM(CASE WHEN phong_ban = 'IT' THEN luong ELSE 0 END) AS tong_luong_it,
    SUM(CASE WHEN phong_ban = 'Marketing' THEN luong ELSE 0 END) AS tong_luong_mkt
FROM nhan_vien;
```

---

## 8. Thứ Tự Thực Thi SQL

```
┌─────────────────────────────────────────────────────────────┐
│             THỨ TỰ THỰC THI CÂU LỆNH SQL                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. FROM      → Xác định bảng nguồn                        │
│   2. JOIN      → Nối các bảng                               │
│   3. WHERE     → Lọc dòng                                   │
│   4. GROUP BY  → Nhóm dữ liệu                               │
│   5. HAVING    → Lọc nhóm                                   │
│   6. SELECT    → Chọn cột                                   │
│   7. DISTINCT  → Loại bỏ trùng lặp                          │
│   8. ORDER BY  → Sắp xếp                                    │
│   9. LIMIT     → Giới hạn số dòng                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘

Viết:                          Thực thi:
SELECT ...          (6)        FROM      (1)
FROM ...            (1)        JOIN      (2)
JOIN ...            (2)        WHERE     (3)
WHERE ...           (3)        GROUP BY  (4)
GROUP BY ...        (4)        HAVING    (5)
HAVING ...          (5)        SELECT    (6)
ORDER BY ...        (8)        DISTINCT  (7)
LIMIT ...           (9)        ORDER BY  (8)
                               LIMIT     (9)
```

---

## 9. Bài Tập Thực Hành

### Bài 1: SELECT và WHERE
```sql
-- a) Liệt kê nhân viên phòng IT, sắp xếp theo lương giảm dần
-- b) Liệt kê nhân viên có lương từ 12-15 triệu
-- c) Tìm nhân viên có tên chứa "Văn"
```

### Bài 2: JOIN
```sql
-- a) Liệt kê doanh số kèm tên nhân viên và phòng ban
-- b) Liệt kê tất cả nhân viên, nếu có doanh số thì hiện doanh số
```

### Bài 3: GROUP BY
```sql
-- a) Tính tổng lương mỗi phòng ban
-- b) Tìm phòng ban có lương trung bình cao nhất
-- c) Đếm số đơn hàng và tổng doanh số theo từng nhân viên
```

### Bài 4: Tổng hợp
```sql
-- Báo cáo doanh số theo tháng:
-- - Tháng
-- - Số đơn hàng
-- - Tổng doanh số
-- - Doanh số trung bình mỗi đơn
-- Chỉ hiện tháng có doanh số >= 20 triệu
```

---

**Tiếp theo:** [03 - Aggregate Functions](./03-aggregate-functions.md)

---

*Chúc bạn học tốt! 🦆*
