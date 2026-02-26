# Học DuckDB Từ Cơ Bản Đến Nâng Cao

```
 ____             _    ____  ____
|  _ \ _   _  ___| | _|  _ \| __ )
| | | | | | |/ __| |/ / | | |  _ \
| |_| | |_| | (__|   <| |_| | |_) |
|____/ \__,_|\___|_|\_\____/|____/

    HƯỚNG DẪN TOÀN DIỆN - TIẾNG VIỆT
```

> **Tác giả:** MangoAds Tech Team
> **Phiên bản:** 1.0
> **Cập nhật:** Tháng 01/2026

---

## Giới Thiệu

Đây là bộ tài liệu học DuckDB từ cơ bản đến nâng cao, tập trung vào:
- **Analytics** (phân tích dữ liệu)
- **Window Functions** (hàm cửa sổ)
- **Thống kê** (statistics)
- **PIVOT/UNPIVOT** (xoay bảng dữ liệu)

## DuckDB Là Gì?

DuckDB là một **embedded analytics database** - cơ sở dữ liệu phân tích nhúng:
- Chạy trong process (không cần server)
- Tối ưu cho **OLAP** (Online Analytical Processing)
- Syntax tương tự PostgreSQL
- Hỗ trợ đọc trực tiếp CSV, Parquet, JSON
- **Miễn phí và mã nguồn mở**

## Tại Sao Nên Học DuckDB?

| Ưu điểm | Mô tả |
|---------|-------|
| **Nhanh** | Xử lý hàng triệu dòng trong vài giây |
| **Đơn giản** | Không cần cài đặt server |
| **Powerful** | Hỗ trợ đầy đủ SQL analytics |
| **Linh hoạt** | Python, R, Node.js, CLI |

## Cấu Trúc Tài Liệu

| File | Nội dung | Độ khó |
|------|----------|--------|
| [01-gioi-thieu-duckdb.md](./01-gioi-thieu-duckdb.md) | Cài đặt, khởi động, cơ bản | ⭐ |
| [02-cu-phap-co-ban.md](./02-cu-phap-co-ban.md) | SELECT, WHERE, JOIN, GROUP BY | ⭐ |
| [03-aggregate-functions.md](./03-aggregate-functions.md) | SUM, AVG, COUNT, thống kê cơ bản | ⭐⭐ |
| [04-window-functions.md](./04-window-functions.md) | OVER, PARTITION BY, LAG, LEAD, Frames | ⭐⭐⭐ |
| [05-pivot-unpivot.md](./05-pivot-unpivot.md) | PIVOT, UNPIVOT, xoay bảng | ⭐⭐⭐ |
| [06-thong-ke-nang-cao.md](./06-thong-ke-nang-cao.md) | Correlation, Percentile, Regression | ⭐⭐⭐ |

## Lộ Trình Học Đề Xuất

```
Giai đoạn 1: Cơ bản
├── 01-gioi-thieu-duckdb.md (DuckDB là gì, cài đặt)
├── 02-cu-phap-co-ban.md (SELECT, WHERE, JOIN)
└── 03-aggregate-functions.md (SUM, AVG, STDDEV)

Giai đoạn 2: Nâng cao
├── 04-window-functions.md (OVER, PARTITION BY, LAG/LEAD)
├── 05-pivot-unpivot.md (Xoay bảng dữ liệu)
└── 06-thong-ke-nang-cao.md (Correlation, Regression)
```

## Yêu Cầu Trước Khi Học

- Hiểu biết cơ bản về SQL (SELECT, WHERE)
- Biết sử dụng Terminal/Command Line
- (Tùy chọn) Python cơ bản

## Cài Đặt Nhanh

### macOS
```bash
brew install duckdb
```

### Ubuntu/Debian
```bash
wget https://github.com/duckdb/duckdb/releases/download/v1.0.0/duckdb_cli-linux-amd64.zip
unzip duckdb_cli-linux-amd64.zip
chmod +x duckdb
./duckdb
```

### Python
```bash
pip install duckdb
```

### Chạy DuckDB
```bash
# CLI
duckdb

# Hoặc với file database
duckdb mydata.db
```

---

## Thuật Ngữ Quan Trọng

| Thuật ngữ | Tiếng Việt | Giải thích |
|-----------|------------|------------|
| **Aggregate** | Tổng hợp | Tính toán trên nhóm dữ liệu (SUM, AVG) |
| **Window Function** | Hàm cửa sổ | Tính toán trên "cửa sổ" dữ liệu |
| **PARTITION BY** | Phân vùng | Chia dữ liệu thành các nhóm |
| **ORDER BY** | Sắp xếp | Sắp xếp dữ liệu |
| **PIVOT** | Xoay | Chuyển hàng thành cột |
| **UNPIVOT** | Xoay ngược | Chuyển cột thành hàng |
| **CTE** | Biểu thức bảng chung | Định nghĩa bảng tạm |
| **Percentile** | Phân vị | Giá trị tại % nhất định |
| **Correlation** | Tương quan | Mối quan hệ giữa 2 biến |

---

## Bắt Đầu Ngay!

👉 **Bước tiếp theo:** [01 - Giới Thiệu DuckDB](./01-gioi-thieu-duckdb.md)

---

*Happy Learning! 🦆*
