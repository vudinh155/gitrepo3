# Giới Thiệu DuckDB

> **Phần 1:** Cài đặt, khởi động và làm quen với DuckDB

---

## 1. DuckDB Là Gì?

### 1.1. Định Nghĩa

**DuckDB** là một hệ quản trị cơ sở dữ liệu quan hệ (RDBMS) được thiết kế đặc biệt cho **phân tích dữ liệu** (analytics).

```
┌─────────────────────────────────────────────────────────────┐
│                      DuckDB                                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   "SQLite cho Analytics"                                    │
│                                                             │
│   • Embedded (nhúng trong ứng dụng)                         │
│   • Không cần server                                        │
│   • Tối ưu cho OLAP (phân tích)                             │
│   • Xử lý dữ liệu cột (columnar)                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 1.2. So Sánh Với Các Database Khác

| Đặc điểm | DuckDB | SQLite | PostgreSQL | MySQL |
|----------|--------|--------|------------|-------|
| Loại | OLAP | OLTP | OLTP/OLAP | OLTP |
| Cài đặt | Embedded | Embedded | Server | Server |
| Tối ưu cho | Analytics | Transactions | General | Transactions |
| Đọc CSV/Parquet | ✅ Native | ❌ | ❌ | ❌ |
| Window Functions | ✅ Đầy đủ | ✅ | ✅ | ✅ |
| PIVOT | ✅ Native | ❌ | ❌ | ❌ |

### 1.3. OLTP vs OLAP

```
OLTP (Online Transaction Processing)
- Nhiều giao dịch nhỏ
- INSERT, UPDATE, DELETE
- Ví dụ: Hệ thống bán hàng, ngân hàng

OLAP (Online Analytical Processing)  ← DuckDB
- Ít truy vấn nhưng phức tạp
- SELECT với GROUP BY, JOIN lớn
- Ví dụ: Báo cáo, phân tích, BI
```

---

## 2. Cài Đặt DuckDB

### 2.1. Cài Đặt CLI (Command Line Interface)

#### macOS
```bash
# Dùng Homebrew
brew install duckdb

# Kiểm tra
duckdb --version
```

#### Linux (Ubuntu/Debian)
```bash
# Tải file zip
wget https://github.com/duckdb/duckdb/releases/latest/download/duckdb_cli-linux-amd64.zip

# Giải nén
unzip duckdb_cli-linux-amd64.zip

# Di chuyển vào /usr/local/bin
sudo mv duckdb /usr/local/bin/

# Kiểm tra
duckdb --version
```

#### Windows
```powershell
# Tải từ: https://github.com/duckdb/duckdb/releases
# Giải nén và thêm vào PATH

# Hoặc dùng Chocolatey
choco install duckdb
```

### 2.2. Cài Đặt Cho Python

```bash
# Dùng pip
pip install duckdb

# Kiểm tra trong Python
python -c "import duckdb; print(duckdb.__version__)"
```

### 2.3. Cài Đặt Cho Node.js

```bash
npm install duckdb
```

---

## 3. Khởi Động DuckDB

### 3.1. Chạy DuckDB CLI

```bash
# Chế độ in-memory (dữ liệu mất khi thoát)
duckdb

# Chế độ persistent (lưu vào file)
duckdb mydata.db
```

### 3.2. Giao Diện CLI

```
v1.0.0
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.
D>
```

**Prompt `D>`** là nơi bạn nhập lệnh SQL.

### 3.3. Các Lệnh Dot Commands Quan Trọng

| Lệnh | Mô tả |
|------|-------|
| `.help` | Xem danh sách lệnh |
| `.tables` | Xem danh sách bảng |
| `.schema TABLE` | Xem cấu trúc bảng |
| `.mode` | Đổi chế độ hiển thị |
| `.output FILE` | Xuất kết quả ra file |
| `.read FILE` | Chạy SQL từ file |
| `.exit` hoặc `.quit` | Thoát |

**Ví dụ:**
```sql
D> .mode markdown
D> SELECT 1 as num, 'hello' as text;
```

Kết quả:
```
| num | text  |
|-----|-------|
| 1   | hello |
```

---

## 4. Truy Vấn Đầu Tiên

### 4.1. Tính Toán Đơn Giản

```sql
-- Phép tính cơ bản
SELECT 1 + 1;
-- Kết quả: 2

-- Nhiều cột
SELECT
    10 + 5 AS cong,
    10 - 5 AS tru,
    10 * 5 AS nhan,
    10 / 5 AS chia;
```

Kết quả:
```
┌──────┬─────┬──────┬──────┐
│ cong │ tru │ nhan │ chia │
├──────┼─────┼──────┼──────┤
│ 15   │ 5   │ 50   │ 2    │
└──────┴─────┴──────┴──────┘
```

### 4.2. Làm Việc Với Chuỗi

```sql
-- Nối chuỗi
SELECT 'Hello' || ' ' || 'World' AS greeting;
-- Kết quả: Hello World

-- Hàm chuỗi
SELECT
    upper('duckdb') AS upper_case,
    lower('DUCKDB') AS lower_case,
    length('DuckDB') AS do_dai;
```

### 4.3. Làm Việc Với Ngày Tháng

```sql
-- Ngày hiện tại
SELECT current_date AS hom_nay;

-- Thời gian hiện tại
SELECT current_timestamp AS bay_gio;

-- Tính toán ngày
SELECT
    current_date AS hom_nay,
    current_date + INTERVAL 7 DAY AS tuan_sau,
    current_date - INTERVAL 1 MONTH AS thang_truoc;
```

---

## 5. Tạo Bảng Và Dữ Liệu Mẫu

### 5.1. Tạo Bảng

```sql
-- Tạo bảng nhân viên
CREATE TABLE nhan_vien (
    id INTEGER PRIMARY KEY,
    ho_ten VARCHAR(100),
    phong_ban VARCHAR(50),
    luong INTEGER,
    ngay_vao DATE
);

-- Xem cấu trúc bảng
DESCRIBE nhan_vien;
```

### 5.2. Thêm Dữ Liệu

```sql
-- Thêm từng dòng
INSERT INTO nhan_vien VALUES
    (1, 'Nguyễn Văn A', 'IT', 15000000, '2020-01-15'),
    (2, 'Trần Thị B', 'Marketing', 12000000, '2019-06-20'),
    (3, 'Lê Văn C', 'IT', 18000000, '2018-03-10'),
    (4, 'Phạm Thị D', 'HR', 10000000, '2021-09-01'),
    (5, 'Hoàng Văn E', 'Marketing', 14000000, '2020-11-15');

-- Xem dữ liệu
SELECT * FROM nhan_vien;
```

Kết quả:
```
┌────┬──────────────┬───────────┬──────────┬────────────┐
│ id │   ho_ten     │ phong_ban │   luong  │  ngay_vao  │
├────┼──────────────┼───────────┼──────────┼────────────┤
│ 1  │ Nguyễn Văn A │ IT        │ 15000000 │ 2020-01-15 │
│ 2  │ Trần Thị B   │ Marketing │ 12000000 │ 2019-06-20 │
│ 3  │ Lê Văn C     │ IT        │ 18000000 │ 2018-03-10 │
│ 4  │ Phạm Thị D   │ HR        │ 10000000 │ 2021-09-01 │
│ 5  │ Hoàng Văn E  │ Marketing │ 14000000 │ 2020-11-15 │
└────┴──────────────┴───────────┴──────────┴────────────┘
```

### 5.3. Tạo Bảng Doanh Số (Dùng Cho Ví Dụ Sau)

```sql
-- Bảng doanh số bán hàng
CREATE TABLE doanh_so (
    id INTEGER,
    nhan_vien_id INTEGER,
    san_pham VARCHAR(50),
    so_luong INTEGER,
    don_gia INTEGER,
    ngay_ban DATE
);

INSERT INTO doanh_so VALUES
    (1, 1, 'Laptop', 2, 20000000, '2024-01-05'),
    (2, 1, 'Mouse', 10, 500000, '2024-01-10'),
    (3, 2, 'Laptop', 1, 20000000, '2024-01-12'),
    (4, 2, 'Keyboard', 5, 1000000, '2024-01-15'),
    (5, 3, 'Monitor', 3, 5000000, '2024-01-18'),
    (6, 1, 'Laptop', 1, 20000000, '2024-02-01'),
    (7, 3, 'Mouse', 20, 500000, '2024-02-05'),
    (8, 2, 'Monitor', 2, 5000000, '2024-02-10'),
    (9, 4, 'Keyboard', 8, 1000000, '2024-02-15'),
    (10, 5, 'Laptop', 3, 20000000, '2024-02-20');
```

---

## 6. Sử Dụng DuckDB Với Python

### 6.1. Kết Nối Cơ Bản

```python
import duckdb

# Kết nối in-memory
con = duckdb.connect()

# Hoặc kết nối với file
# con = duckdb.connect('mydata.db')

# Chạy query
result = con.execute("SELECT 1 + 1 AS ket_qua").fetchall()
print(result)  # [(2,)]

# Đóng kết nối
con.close()
```

### 6.2. Làm Việc Với Pandas

```python
import duckdb
import pandas as pd

# Tạo DataFrame
df = pd.DataFrame({
    'ten': ['An', 'Bình', 'Cường'],
    'tuoi': [25, 30, 28],
    'luong': [10000000, 15000000, 12000000]
})

# Query trực tiếp trên DataFrame
result = duckdb.query("""
    SELECT
        ten,
        luong,
        luong * 12 AS luong_nam
    FROM df
    WHERE tuoi >= 28
""").to_df()

print(result)
```

Kết quả:
```
     ten     luong   luong_nam
0   Bình  15000000   180000000
1  Cường  12000000   144000000
```

### 6.3. Đọc File CSV

```python
import duckdb

# Đọc CSV trực tiếp
result = duckdb.query("""
    SELECT *
    FROM read_csv_auto('data.csv')
    LIMIT 10
""").to_df()

# Hoặc tạo bảng từ CSV
duckdb.execute("""
    CREATE TABLE sales AS
    SELECT * FROM read_csv_auto('sales.csv')
""")
```

---

## 7. Đọc File Trực Tiếp (Không Cần Import)

### 7.1. Đọc CSV

```sql
-- Đọc CSV với auto-detect
SELECT * FROM read_csv_auto('data.csv');

-- Đọc CSV với options
SELECT * FROM read_csv('data.csv',
    header=true,
    delimiter=',',
    dateformat='%Y-%m-%d'
);

-- Đọc nhiều file CSV (glob pattern)
SELECT * FROM read_csv_auto('data/*.csv');
```

### 7.2. Đọc Parquet

```sql
-- Đọc file Parquet
SELECT * FROM read_parquet('data.parquet');

-- Đọc nhiều file Parquet
SELECT * FROM read_parquet('data/*.parquet');

-- Đọc từ S3 (nếu đã cài extension)
SELECT * FROM read_parquet('s3://bucket/data.parquet');
```

### 7.3. Đọc JSON

```sql
-- Đọc file JSON
SELECT * FROM read_json_auto('data.json');

-- Đọc JSON với schema
SELECT * FROM read_json('data.json',
    columns = {id: 'INTEGER', name: 'VARCHAR'}
);
```

---

## 8. Xuất Dữ Liệu

### 8.1. Xuất Ra CSV

```sql
-- Xuất toàn bộ bảng
COPY nhan_vien TO 'nhan_vien.csv' (HEADER, DELIMITER ',');

-- Xuất kết quả query
COPY (
    SELECT * FROM nhan_vien WHERE phong_ban = 'IT'
) TO 'it_team.csv' (HEADER);
```

### 8.2. Xuất Ra Parquet

```sql
-- Xuất ra Parquet (nén tốt, đọc nhanh)
COPY nhan_vien TO 'nhan_vien.parquet' (FORMAT PARQUET);

-- Xuất với compression
COPY nhan_vien TO 'nhan_vien.parquet' (
    FORMAT PARQUET,
    COMPRESSION 'ZSTD'
);
```

### 8.3. Xuất Ra JSON

```sql
COPY nhan_vien TO 'nhan_vien.json' (FORMAT JSON);
```

---

## 9. Bài Tập Thực Hành

### Bài 1: Khởi động
1. Cài đặt DuckDB CLI
2. Khởi động DuckDB
3. Chạy lệnh `.help` để xem các lệnh

### Bài 2: Tính toán
```sql
-- Tính các biểu thức sau:
-- a) 123 * 456
-- b) 100 / 3 (chia lấy phần nguyên)
-- c) 100 % 3 (chia lấy dư)
-- d) 2 ^ 10 (lũy thừa)
```

### Bài 3: Tạo bảng và dữ liệu
```sql
-- Tạo bảng san_pham với các cột:
-- id, ten_sp, loai, gia, so_luong_ton
-- Thêm 5 sản phẩm vào bảng
```

### Bài 4: Đọc file
```sql
-- Nếu có file CSV, thử đọc bằng:
-- SELECT * FROM read_csv_auto('your_file.csv') LIMIT 10;
```

---

## 10. Tổng Kết

Trong phần này, bạn đã học:

| Nội dung | Mô tả |
|----------|-------|
| DuckDB là gì | Database OLAP, embedded, nhanh |
| Cài đặt | CLI, Python, Node.js |
| Khởi động | `duckdb` hoặc `duckdb file.db` |
| Dot commands | `.help`, `.tables`, `.schema` |
| Tạo bảng | `CREATE TABLE` |
| Đọc file | `read_csv_auto`, `read_parquet` |
| Xuất file | `COPY ... TO ...` |

---

**Tiếp theo:** [02 - Cú Pháp Cơ Bản](./02-cu-phap-co-ban.md)

---

*Chúc bạn học tốt! 🦆*
