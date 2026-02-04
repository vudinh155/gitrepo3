# Thống Kê Nâng Cao Trong DuckDB

> **📚 TÀI LIỆU HỌC DUCKDB - MANGOADS**
> Phiên bản 1.0 - Tháng 01/2026

---

## Mục Lục

1. [Tổng Quan Về Thống Kê](#1-tổng-quan-về-thống-kê)
2. [Các Đại Lượng Đo Lường Trung Tâm](#2-các-đại-lượng-đo-lường-trung-tâm)
3. [Các Đại Lượng Đo Lường Phân Tán](#3-các-đại-lượng-đo-lường-phân-tán)
4. [Percentile và Quantile](#4-percentile-và-quantile)
5. [Correlation - Tương Quan](#5-correlation---tương-quan)
6. [Regression - Hồi Quy](#6-regression---hồi-quy)
7. [Distribution Analysis](#7-distribution-analysis)
8. [Statistical Functions Nâng Cao](#8-statistical-functions-nâng-cao)
9. [Ví Dụ Thực Tế Marketing Analytics](#9-ví-dụ-thực-tế-marketing-analytics)
10. [Bài Tập Thực Hành](#10-bài-tập-thực-hành)

---

## 1. Tổng Quan Về Thống Kê

### 1.1. Tại Sao Cần Thống Kê Trong Analytics?

```
+------------------------------------------------------------------+
|                    TẦM QUAN TRỌNG CỦA THỐNG KÊ                    |
+------------------------------------------------------------------+
|                                                                  |
|   📊 HIỂU DỮ LIỆU:                                               |
|   - Mô tả đặc điểm của dataset                                   |
|   - Phát hiện patterns và anomalies                              |
|   - So sánh giữa các nhóm                                        |
|                                                                  |
|   📈 RA QUYẾT ĐỊNH:                                              |
|   - Đánh giá hiệu quả campaigns                                  |
|   - Dự đoán xu hướng                                             |
|   - Phân bổ ngân sách tối ưu                                     |
|                                                                  |
|   🎯 DIGITAL MARKETING:                                          |
|   - A/B Testing                                                  |
|   - Attribution Analysis                                         |
|   - Customer Segmentation                                        |
|                                                                  |
+------------------------------------------------------------------+
```

### 1.2. Các Loại Thống Kê

| Loại | Mô tả | Ví dụ |
|------|-------|-------|
| **Descriptive** | Mô tả dữ liệu | Mean, Median, Std Dev |
| **Inferential** | Suy luận từ mẫu | Correlation, Regression |
| **Predictive** | Dự đoán tương lai | Forecasting, Trends |

---

## 2. Các Đại Lượng Đo Lường Trung Tâm

### 2.1. Mean (Trung Bình Cộng)

**Định nghĩa:** Tổng tất cả giá trị chia cho số lượng.

```sql
CREATE TABLE campaign_metrics AS
SELECT * FROM (VALUES
    ('A', 100), ('A', 120), ('A', 110), ('A', 130), ('A', 140),
    ('A', 90), ('A', 150), ('A', 115), ('A', 125), ('A', 135),
    ('B', 200), ('B', 180), ('B', 190), ('B', 210), ('B', 220),
    ('B', 170), ('B', 230), ('B', 195), ('B', 205), ('B', 215)
) AS t(campaign, conversions);
```

**📊 Dữ liệu mẫu campaign_metrics:**

| Campaign A | | Campaign B | |
|------------|---|------------|---|
| 90 | 115 | 170 | 195 |
| 100 | 120 | 180 | 200 |
| 110 | 125 | 190 | 205 |
| 130 | 135 | 210 | 215 |
| 140 | 150 | 220 | 230 |

```sql
-- Tính Mean
SELECT
    campaign,
    AVG(conversions) AS mean_conversions
FROM campaign_metrics
GROUP BY campaign;
```

**📊 Kết quả:**

| campaign | mean_conversions | Cách tính |
|----------|------------------|-----------|
| A | 121.5 | (90+100+110+115+120+125+130+135+140+150) / 10 |
| B | 201.5 | (170+180+190+195+200+205+210+215+220+230) / 10 |

**Khi nào dùng Mean:**
- Dữ liệu phân phối đều
- Không có outliers (giá trị ngoại lai)

### 2.2. Median (Trung Vị)

**Định nghĩa:** Giá trị ở vị trí giữa khi sắp xếp dữ liệu.

```sql
-- DuckDB có hàm MEDIAN
SELECT
    campaign,
    MEDIAN(conversions) AS median_conversions
FROM campaign_metrics
GROUP BY campaign;

-- Hoặc dùng PERCENTILE_CONT
SELECT
    campaign,
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY conversions) AS median_conversions
FROM campaign_metrics
GROUP BY campaign;
```

**Khi nào dùng Median:**
- Có outliers
- Dữ liệu skewed (lệch)
- VD: Thu nhập, chi tiêu quảng cáo

### 2.3. Mode (Yếu Vị)

**Định nghĩa:** Giá trị xuất hiện nhiều nhất.

```sql
-- DuckDB có hàm MODE
SELECT
    campaign,
    MODE(conversions) AS mode_conversions
FROM campaign_metrics
GROUP BY campaign;
```

### 2.4. So Sánh Mean vs Median

```sql
CREATE TABLE chi_tieu AS
SELECT * FROM (VALUES
    (100), (120), (110), (130), (115),  -- Phần lớn chi tiêu thấp
    (5000)  -- 1 outlier chi tiêu cao
) AS t(spend);

SELECT
    AVG(spend) AS mean_spend,           -- Bị ảnh hưởng bởi outlier
    MEDIAN(spend) AS median_spend,      -- Không bị ảnh hưởng
    MODE(spend) AS mode_spend
FROM chi_tieu;
```

**📊 Dữ liệu chi_tieu (có outlier):**

| Giá trị | Loại |
|---------|------|
| 100 | Bình thường |
| 110 | Bình thường |
| 115 | Bình thường |
| 120 | Bình thường |
| 130 | Bình thường |
| **5000** | **⚠️ Outlier** |

**📊 Kết quả so sánh:**

| Thống kê | Giá trị | Giải thích |
|----------|---------|------------|
| Mean | 929.17 | Bị kéo lên bởi outlier 5000 ⚠️ |
| Median | 115 | Không bị ảnh hưởng ✅ |
| Mode | 100 | Giá trị xuất hiện nhiều nhất |

```
Phân bố dữ liệu:
100  110  115  120  130                              5000
 │    │    │    │    │                                 │
 ▼    ▼    ▼    ▼    ▼                                 ▼
 ○────○────○────○────○─────────────────────────────────●
           ↑                        ↑
        Median=115              Mean=929 (bị kéo về phía outlier)
```

---

## 3. Các Đại Lượng Đo Lường Phân Tán

### 3.1. Range (Phạm Vi)

```sql
SELECT
    campaign,
    MAX(conversions) - MIN(conversions) AS range_conversions
FROM campaign_metrics
GROUP BY campaign;
```

### 3.2. Variance (Phương Sai)

**Định nghĩa:** Trung bình của bình phương độ lệch so với mean.

```
Variance = Σ(xi - mean)² / n

Trong đó:
- xi: Giá trị từng điểm dữ liệu
- mean: Trung bình
- n: Số lượng điểm dữ liệu
```

```sql
SELECT
    campaign,
    -- Variance mẫu (chia cho n-1)
    VAR_SAMP(conversions) AS variance_sample,
    -- Variance tổng thể (chia cho n)
    VAR_POP(conversions) AS variance_population
FROM campaign_metrics
GROUP BY campaign;
```

**Lưu ý:**
- `VAR_SAMP` (hoặc `VARIANCE`): Dùng cho **mẫu** (sample)
- `VAR_POP`: Dùng cho **tổng thể** (population)

### 3.3. Standard Deviation (Độ Lệch Chuẩn)

**Định nghĩa:** Căn bậc hai của Variance.

```
Standard Deviation = √Variance
```

```sql
SELECT
    campaign,
    ROUND(AVG(conversions), 2) AS mean,
    ROUND(STDDEV_SAMP(conversions), 2) AS stddev_sample,
    ROUND(STDDEV_POP(conversions), 2) AS stddev_population
FROM campaign_metrics
GROUP BY campaign;
```

**📊 Kết quả và giải thích:**

| campaign | mean | stddev | Ý nghĩa |
|----------|------|--------|---------|
| A | 121.5 | 18.93 | Dữ liệu dao động ±19 quanh 121.5 |
| B | 201.5 | 18.93 | Dữ liệu dao động ±19 quanh 201.5 |

**🔍 Ví dụ cụ thể với Campaign A (mean=121.5, stddev≈19):**
- 68% giá trị nằm trong: 121.5 ± 19 = [102.5, 140.5]
- Kiểm tra: 90, **100, 110, 115, 120, 125, 130, 135, 140**, 150 → 8/10 = 80% ✓

### 3.4. Ý Nghĩa Của Standard Deviation

```
+------------------------------------------------------------------+
|              DIỄN GIẢI STANDARD DEVIATION                         |
+------------------------------------------------------------------+
|                                                                  |
|   Trong phân phối chuẩn (normal distribution):                   |
|                                                                  |
|   ┌───────────────────────────────────────────────────────┐      |
|   │     68.27%          95.45%          99.73%            │      |
|   │   ├─────────┤    ├───────────────┤    ├─────────────┤ │      |
|   │   │  ±1σ    │    │     ±2σ       │    │    ±3σ      │ │      |
|   │                                                       │      |
|   │        mean-2σ  mean-1σ  mean  mean+1σ  mean+2σ       │      |
|   └───────────────────────────────────────────────────────┘      |
|                                                                  |
|   - ~68% dữ liệu nằm trong ±1 standard deviation                 |
|   - ~95% dữ liệu nằm trong ±2 standard deviations                |
|   - ~99.7% dữ liệu nằm trong ±3 standard deviations              |
|                                                                  |
+------------------------------------------------------------------+
```

### 3.5. Coefficient of Variation (Hệ Số Biến Thiên)

**Định nghĩa:** Standard Deviation chia cho Mean, nhân 100%.

```
CV = (Standard Deviation / Mean) × 100%
```

**Tại sao cần CV?**
- So sánh độ biến thiên giữa các nhóm có mean khác nhau
- CV nhỏ = Dữ liệu ổn định
- CV lớn = Dữ liệu biến động nhiều

```sql
SELECT
    campaign,
    ROUND(AVG(conversions), 2) AS mean,
    ROUND(STDDEV(conversions), 2) AS stddev,
    ROUND(STDDEV(conversions) / AVG(conversions) * 100, 2) AS cv_percent
FROM campaign_metrics
GROUP BY campaign;
```

---

## 4. Percentile và Quantile

### 4.1. Khái Niệm

```
+------------------------------------------------------------------+
|                    PERCENTILE / QUANTILE                          |
+------------------------------------------------------------------+
|                                                                  |
|   PERCENTILE: Giá trị mà X% dữ liệu nằm dưới nó                  |
|                                                                  |
|   - P25 (25th percentile) = Q1 = Quartile 1                      |
|   - P50 (50th percentile) = Q2 = Median                          |
|   - P75 (75th percentile) = Q3 = Quartile 3                      |
|   - P90, P95, P99: Thường dùng trong SLA, performance            |
|                                                                  |
|   IQR (Interquartile Range) = Q3 - Q1                            |
|   → Dùng để phát hiện outliers                                   |
|                                                                  |
+------------------------------------------------------------------+
```

### 4.2. Tính Percentile Trong DuckDB

```sql
SELECT
    campaign,
    -- Percentiles cơ bản
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY conversions) AS p25,
    PERCENTILE_CONT(0.50) WITHIN GROUP (ORDER BY conversions) AS p50,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY conversions) AS p75,
    -- Performance percentiles
    PERCENTILE_CONT(0.90) WITHIN GROUP (ORDER BY conversions) AS p90,
    PERCENTILE_CONT(0.95) WITHIN GROUP (ORDER BY conversions) AS p95,
    PERCENTILE_CONT(0.99) WITHIN GROUP (ORDER BY conversions) AS p99
FROM campaign_metrics
GROUP BY campaign;
```

### 4.3. PERCENTILE_CONT vs PERCENTILE_DISC

```sql
CREATE TABLE test_data AS
SELECT * FROM (VALUES (10), (20), (30), (40), (50)) AS t(value);

SELECT
    -- CONT: Interpolates (nội suy) giữa 2 giá trị
    PERCENTILE_CONT(0.3) WITHIN GROUP (ORDER BY value) AS p30_cont,
    -- DISC: Lấy giá trị thực tế gần nhất
    PERCENTILE_DISC(0.3) WITHIN GROUP (ORDER BY value) AS p30_disc
FROM test_data;
```

Kết quả:
```
┌───────────┬───────────┐
│ p30_cont  │ p30_disc  │
├───────────┼───────────┤
│      22.0 │        20 │  ← CONT nội suy, DISC lấy giá trị có sẵn
└───────────┴───────────┘
```

### 4.4. Five-Number Summary

```sql
SELECT
    campaign,
    MIN(conversions) AS min,
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY conversions) AS q1,
    MEDIAN(conversions) AS median,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY conversions) AS q3,
    MAX(conversions) AS max,
    -- IQR để phát hiện outliers
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY conversions)
    - PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY conversions) AS iqr
FROM campaign_metrics
GROUP BY campaign;
```

### 4.5. Phát Hiện Outliers Bằng IQR

```sql
WITH stats AS (
    SELECT
        campaign,
        PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY conversions) AS q1,
        PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY conversions) AS q3
    FROM campaign_metrics
    GROUP BY campaign
),
bounds AS (
    SELECT
        campaign,
        q1,
        q3,
        q1 - 1.5 * (q3 - q1) AS lower_bound,
        q3 + 1.5 * (q3 - q1) AS upper_bound
    FROM stats
)
SELECT
    m.campaign,
    m.conversions,
    CASE
        WHEN m.conversions < b.lower_bound THEN 'Lower Outlier'
        WHEN m.conversions > b.upper_bound THEN 'Upper Outlier'
        ELSE 'Normal'
    END AS status
FROM campaign_metrics m
JOIN bounds b ON m.campaign = b.campaign
ORDER BY m.campaign, m.conversions;
```

---

## 5. Correlation - Tương Quan

### 5.1. Khái Niệm Correlation

**Correlation coefficient (r)** đo lường **mức độ** và **hướng** của mối quan hệ tuyến tính giữa 2 biến.

```
+------------------------------------------------------------------+
|                    CORRELATION COEFFICIENT                        |
+------------------------------------------------------------------+
|                                                                  |
|   r = +1.0  : Tương quan dương hoàn hảo (cùng tăng)              |
|   r = +0.7  : Tương quan dương mạnh                               |
|   r = +0.3  : Tương quan dương yếu                                |
|   r = 0.0   : Không có tương quan tuyến tính                      |
|   r = -0.3  : Tương quan âm yếu                                   |
|   r = -0.7  : Tương quan âm mạnh                                  |
|   r = -1.0  : Tương quan âm hoàn hảo (ngược chiều)               |
|                                                                  |
+------------------------------------------------------------------+
```

### 5.2. Tính Correlation Trong DuckDB

```sql
CREATE TABLE ads_data AS
SELECT * FROM (VALUES
    (100, 50, 1000),
    (150, 75, 1400),
    (120, 55, 1100),
    (200, 100, 1800),
    (180, 85, 1650),
    (130, 60, 1200),
    (170, 80, 1550),
    (140, 65, 1300),
    (190, 95, 1750),
    (160, 78, 1500)
) AS t(spend, clicks, impressions);

-- Correlation giữa spend và clicks
SELECT
    ROUND(CORR(spend, clicks), 4) AS corr_spend_clicks,
    ROUND(CORR(spend, impressions), 4) AS corr_spend_impressions,
    ROUND(CORR(clicks, impressions), 4) AS corr_clicks_impressions
FROM ads_data;
```

### 5.3. Ma Trận Correlation

```sql
-- Tạo correlation matrix
WITH vars AS (
    SELECT
        spend,
        clicks,
        impressions
    FROM ads_data
)
SELECT
    'spend' AS variable,
    ROUND(CORR(spend, spend), 4) AS spend,
    ROUND(CORR(spend, clicks), 4) AS clicks,
    ROUND(CORR(spend, impressions), 4) AS impressions
FROM vars
UNION ALL
SELECT
    'clicks',
    ROUND(CORR(clicks, spend), 4),
    ROUND(CORR(clicks, clicks), 4),
    ROUND(CORR(clicks, impressions), 4)
FROM vars
UNION ALL
SELECT
    'impressions',
    ROUND(CORR(impressions, spend), 4),
    ROUND(CORR(impressions, clicks), 4),
    ROUND(CORR(impressions, impressions), 4)
FROM vars;
```

### 5.4. Lưu Ý Quan Trọng

```
+------------------------------------------------------------------+
|                ⚠️ CORRELATION ≠ CAUSATION                         |
+------------------------------------------------------------------+
|                                                                  |
|   Correlation cao KHÔNG có nghĩa là biến này GÂY RA biến kia!    |
|                                                                  |
|   Ví dụ:                                                         |
|   - Số kem bán ra ↔ Số vụ đuối nước (cả 2 tăng vào mùa hè)       |
|   - Spend ads ↔ Conversions (có thể do nhiều yếu tố khác)        |
|                                                                  |
|   Cần thêm:                                                      |
|   - Domain knowledge                                              |
|   - A/B testing                                                  |
|   - Phân tích sâu hơn                                            |
|                                                                  |
+------------------------------------------------------------------+
```

---

## 6. Regression - Hồi Quy

### 6.1. Linear Regression Cơ Bản

**Phương trình hồi quy tuyến tính:**
```
y = a + b × x

Trong đó:
- y: Biến phụ thuộc (dependent variable)
- x: Biến độc lập (independent variable)
- a: Intercept (hệ số chặn)
- b: Slope (hệ số góc)
```

### 6.2. Tính Regression Trong DuckDB

```sql
SELECT
    -- Slope (hệ số góc)
    REGR_SLOPE(clicks, spend) AS slope,
    -- Intercept (hệ số chặn)
    REGR_INTERCEPT(clicks, spend) AS intercept,
    -- R-squared (hệ số xác định)
    REGR_R2(clicks, spend) AS r_squared
FROM ads_data;
```

### 6.3. Ý Nghĩa Các Hệ Số

```sql
WITH regression AS (
    SELECT
        REGR_SLOPE(clicks, spend) AS slope,
        REGR_INTERCEPT(clicks, spend) AS intercept,
        REGR_R2(clicks, spend) AS r_squared
    FROM ads_data
)
SELECT
    ROUND(slope, 4) AS slope,
    ROUND(intercept, 4) AS intercept,
    ROUND(r_squared, 4) AS r_squared,
    -- Diễn giải
    'Mỗi $1 spend tăng thêm ' || ROUND(slope, 2) || ' clicks' AS interpretation
FROM regression;
```

### 6.4. Dự Đoán Với Regression

```sql
WITH model AS (
    SELECT
        REGR_SLOPE(clicks, spend) AS slope,
        REGR_INTERCEPT(clicks, spend) AS intercept
    FROM ads_data
)
SELECT
    spend_plan AS planned_spend,
    ROUND(intercept + slope * spend_plan, 0) AS predicted_clicks
FROM model, (VALUES (100), (150), (200), (250), (300)) AS plans(spend_plan);
```

### 6.5. Các Hàm Regression Khác

```sql
SELECT
    -- Số quan sát
    REGR_COUNT(clicks, spend) AS n,
    -- Tổng X
    REGR_SXX(clicks, spend) AS sum_xx,
    -- Tổng Y
    REGR_SYY(clicks, spend) AS sum_yy,
    -- Tổng XY
    REGR_SXY(clicks, spend) AS sum_xy,
    -- Trung bình X
    REGR_AVGX(clicks, spend) AS avg_x,
    -- Trung bình Y
    REGR_AVGY(clicks, spend) AS avg_y
FROM ads_data;
```

---

## 7. Distribution Analysis

### 7.1. Histogram Data

```sql
-- Tạo histogram bins
WITH bins AS (
    SELECT
        FLOOR(conversions / 20) * 20 AS bin_start,
        FLOOR(conversions / 20) * 20 + 19 AS bin_end,
        COUNT(*) AS frequency
    FROM campaign_metrics
    GROUP BY FLOOR(conversions / 20)
    ORDER BY bin_start
)
SELECT
    bin_start || '-' || bin_end AS bin_range,
    frequency,
    REPEAT('█', frequency) AS histogram
FROM bins;
```

### 7.2. Skewness (Độ Lệch)

```sql
-- Tính skewness thủ công
WITH stats AS (
    SELECT
        AVG(conversions) AS mean,
        STDDEV(conversions) AS stddev,
        COUNT(*) AS n
    FROM campaign_metrics
)
SELECT
    ROUND(
        SUM(POWER((conversions - mean) / stddev, 3)) / n,
        4
    ) AS skewness
FROM campaign_metrics, stats;
```

**Diễn giải Skewness:**
- Skewness = 0: Phân phối đối xứng
- Skewness > 0: Lệch phải (right-skewed)
- Skewness < 0: Lệch trái (left-skewed)

### 7.3. Kurtosis (Độ Nhọn)

```sql
-- Tính kurtosis thủ công
WITH stats AS (
    SELECT
        AVG(conversions) AS mean,
        STDDEV(conversions) AS stddev,
        COUNT(*) AS n
    FROM campaign_metrics
)
SELECT
    ROUND(
        SUM(POWER((conversions - mean) / stddev, 4)) / n - 3,
        4
    ) AS excess_kurtosis
FROM campaign_metrics, stats;
```

**Diễn giải Kurtosis:**
- Kurtosis = 0: Phân phối chuẩn
- Kurtosis > 0: Đuôi nặng (heavy tails), nhiều outliers
- Kurtosis < 0: Đuôi nhẹ (light tails)

---

## 8. Statistical Functions Nâng Cao

### 8.1. Covariance (Hiệp Phương Sai)

```sql
SELECT
    -- Covariance mẫu
    COVAR_SAMP(clicks, spend) AS covar_sample,
    -- Covariance tổng thể
    COVAR_POP(clicks, spend) AS covar_population
FROM ads_data;
```

### 8.2. Moving Statistics

```sql
-- Moving average và moving standard deviation
SELECT
    ngay,
    doanh_thu,
    ROUND(AVG(doanh_thu) OVER (
        ORDER BY ngay
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ), 2) AS moving_avg_7d,
    ROUND(STDDEV(doanh_thu) OVER (
        ORDER BY ngay
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ), 2) AS moving_stddev_7d
FROM (
    SELECT * FROM (VALUES
        ('2024-01-01', 100),
        ('2024-01-02', 120),
        ('2024-01-03', 110),
        ('2024-01-04', 130),
        ('2024-01-05', 125),
        ('2024-01-06', 140),
        ('2024-01-07', 135),
        ('2024-01-08', 150),
        ('2024-01-09', 145),
        ('2024-01-10', 160)
    ) AS t(ngay, doanh_thu)
);
```

### 8.3. Z-Score

```sql
-- Chuẩn hóa dữ liệu bằng Z-score
WITH stats AS (
    SELECT
        AVG(conversions) AS mean,
        STDDEV(conversions) AS stddev
    FROM campaign_metrics
)
SELECT
    campaign,
    conversions,
    ROUND((conversions - mean) / stddev, 2) AS z_score,
    CASE
        WHEN ABS((conversions - mean) / stddev) > 3 THEN 'Extreme Outlier'
        WHEN ABS((conversions - mean) / stddev) > 2 THEN 'Outlier'
        WHEN ABS((conversions - mean) / stddev) > 1 THEN 'Unusual'
        ELSE 'Normal'
    END AS status
FROM campaign_metrics, stats
ORDER BY z_score DESC;
```

### 8.4. Approximate Quantiles

```sql
-- DuckDB hỗ trợ approx_quantile cho big data
SELECT
    APPROX_QUANTILE(conversions, 0.5) AS approx_median,
    APPROX_QUANTILE(conversions, 0.95) AS approx_p95
FROM campaign_metrics;
```

---

## 9. Ví Dụ Thực Tế Marketing Analytics

### 9.1. Campaign Performance Summary

```sql
CREATE TABLE campaign_daily AS
SELECT * FROM (VALUES
    ('A', '2024-01-01', 100, 50, 1000),
    ('A', '2024-01-02', 120, 60, 1100),
    ('A', '2024-01-03', 90, 40, 900),
    ('A', '2024-01-04', 130, 70, 1200),
    ('A', '2024-01-05', 110, 55, 1050),
    ('B', '2024-01-01', 200, 90, 1800),
    ('B', '2024-01-02', 180, 80, 1600),
    ('B', '2024-01-03', 210, 100, 2000),
    ('B', '2024-01-04', 190, 85, 1700),
    ('B', '2024-01-05', 220, 110, 2100)
) AS t(campaign, date, spend, clicks, impressions);

-- Full statistical summary
SELECT
    campaign,
    -- Central tendency
    ROUND(AVG(spend), 2) AS mean_spend,
    MEDIAN(spend) AS median_spend,
    -- Dispersion
    ROUND(STDDEV(spend), 2) AS stddev_spend,
    ROUND(STDDEV(spend) / AVG(spend) * 100, 2) AS cv_percent,
    -- Distribution
    MIN(spend) AS min_spend,
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY spend) AS p25_spend,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY spend) AS p75_spend,
    MAX(spend) AS max_spend,
    -- CTR statistics
    ROUND(AVG(clicks * 100.0 / impressions), 2) AS mean_ctr,
    ROUND(STDDEV(clicks * 100.0 / impressions), 2) AS stddev_ctr
FROM campaign_daily
GROUP BY campaign;
```

### 9.2. Channel Attribution Analysis

```sql
CREATE TABLE conversions_by_channel AS
SELECT * FROM (VALUES
    ('Search', 500, 100),
    ('Social', 300, 45),
    ('Display', 200, 20),
    ('Email', 100, 30),
    ('Direct', 150, 50)
) AS t(channel, clicks, conversions);

SELECT
    channel,
    clicks,
    conversions,
    ROUND(conversions * 100.0 / clicks, 2) AS conv_rate,
    -- Percentile ranking
    ROUND(PERCENT_RANK() OVER (ORDER BY conversions * 1.0 / clicks) * 100, 0) AS percentile_rank,
    -- Share of total conversions
    ROUND(conversions * 100.0 / SUM(conversions) OVER (), 2) AS share_of_conversions
FROM conversions_by_channel
ORDER BY conv_rate DESC;
```

### 9.3. Spend vs Revenue Correlation

```sql
CREATE TABLE weekly_performance AS
SELECT * FROM (VALUES
    ('Week 1', 5000, 25000),
    ('Week 2', 6000, 28000),
    ('Week 3', 5500, 27000),
    ('Week 4', 7000, 32000),
    ('Week 5', 6500, 30000),
    ('Week 6', 8000, 38000),
    ('Week 7', 7500, 35000),
    ('Week 8', 9000, 42000)
) AS t(week, spend, revenue);

-- Analyze relationship
SELECT
    -- Correlation
    ROUND(CORR(spend, revenue), 4) AS correlation,
    -- Regression
    ROUND(REGR_SLOPE(revenue, spend), 2) AS slope,
    ROUND(REGR_INTERCEPT(revenue, spend), 2) AS intercept,
    ROUND(REGR_R2(revenue, spend), 4) AS r_squared,
    -- Interpretation
    'Mỗi $1 chi tiêu tạo ra $' ||
    ROUND(REGR_SLOPE(revenue, spend), 2) || ' doanh thu' AS roi_interpretation
FROM weekly_performance;
```

### 9.4. Anomaly Detection Dashboard

```sql
WITH daily_stats AS (
    SELECT
        date,
        campaign,
        spend,
        AVG(spend) OVER (PARTITION BY campaign) AS avg_spend,
        STDDEV(spend) OVER (PARTITION BY campaign) AS stddev_spend,
        PERCENTILE_CONT(0.25) OVER (PARTITION BY campaign ORDER BY date
            ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS q1,
        PERCENTILE_CONT(0.75) OVER (PARTITION BY campaign ORDER BY date
            ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS q3
    FROM campaign_daily
)
SELECT
    date,
    campaign,
    spend,
    ROUND(avg_spend, 2) AS avg_spend,
    -- Z-score method
    ROUND((spend - avg_spend) / NULLIF(stddev_spend, 0), 2) AS z_score,
    -- IQR method
    CASE
        WHEN spend < q1 - 1.5 * (q3 - q1) THEN 'Low Anomaly'
        WHEN spend > q3 + 1.5 * (q3 - q1) THEN 'High Anomaly'
        WHEN ABS((spend - avg_spend) / NULLIF(stddev_spend, 0)) > 2 THEN 'Z-Score Anomaly'
        ELSE 'Normal'
    END AS status
FROM daily_stats
ORDER BY campaign, date;
```

---

## 10. Bài Tập Thực Hành

### Bài 1: Descriptive Statistics
Cho bảng sales(region, product, amount). Tính mean, median, stddev, cv cho amount theo region.

<details>
<summary>Đáp án</summary>

```sql
SELECT
    region,
    ROUND(AVG(amount), 2) AS mean,
    MEDIAN(amount) AS median,
    ROUND(STDDEV(amount), 2) AS stddev,
    ROUND(STDDEV(amount) / AVG(amount) * 100, 2) AS cv_percent
FROM sales
GROUP BY region;
```
</details>

### Bài 2: Percentile Analysis
Tính 5-number summary và phát hiện outliers cho conversions.

<details>
<summary>Đáp án</summary>

```sql
WITH stats AS (
    SELECT
        MIN(conversions) AS min_val,
        PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY conversions) AS q1,
        MEDIAN(conversions) AS median,
        PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY conversions) AS q3,
        MAX(conversions) AS max_val
    FROM data
)
SELECT
    d.conversions,
    CASE
        WHEN d.conversions < s.q1 - 1.5 * (s.q3 - s.q1) THEN 'Lower Outlier'
        WHEN d.conversions > s.q3 + 1.5 * (s.q3 - s.q1) THEN 'Upper Outlier'
        ELSE 'Normal'
    END AS status
FROM data d, stats s;
```
</details>

### Bài 3: Correlation Matrix
Tạo correlation matrix cho spend, clicks, conversions.

<details>
<summary>Đáp án</summary>

```sql
SELECT 'spend' AS var,
    1 AS spend,
    ROUND(CORR(spend, clicks), 4) AS clicks,
    ROUND(CORR(spend, conversions), 4) AS conversions
FROM data
UNION ALL
SELECT 'clicks',
    ROUND(CORR(clicks, spend), 4),
    1,
    ROUND(CORR(clicks, conversions), 4)
FROM data
UNION ALL
SELECT 'conversions',
    ROUND(CORR(conversions, spend), 4),
    ROUND(CORR(conversions, clicks), 4),
    1
FROM data;
```
</details>

### Bài 4: Regression Prediction
Dùng regression để dự đoán clicks từ spend.

<details>
<summary>Đáp án</summary>

```sql
WITH model AS (
    SELECT
        REGR_SLOPE(clicks, spend) AS slope,
        REGR_INTERCEPT(clicks, spend) AS intercept
    FROM historical_data
)
SELECT
    new_spend AS planned_spend,
    ROUND(intercept + slope * new_spend, 0) AS predicted_clicks
FROM model, future_plans;
```
</details>

---

## 🔑 Tóm Tắt Statistical Functions

```
+------------------------------------------------------------------+
|                STATISTICAL FUNCTIONS CHEAT SHEET                  |
+------------------------------------------------------------------+
|                                                                  |
|   CENTRAL TENDENCY:                                              |
|   - AVG(): Mean (trung bình)                                     |
|   - MEDIAN(): Median (trung vị)                                  |
|   - MODE(): Mode (yếu vị)                                        |
|                                                                  |
|   DISPERSION:                                                    |
|   - VAR_SAMP() / VARIANCE(): Phương sai mẫu                      |
|   - VAR_POP(): Phương sai tổng thể                               |
|   - STDDEV_SAMP() / STDDEV(): Độ lệch chuẩn mẫu                  |
|   - STDDEV_POP(): Độ lệch chuẩn tổng thể                         |
|                                                                  |
|   PERCENTILES:                                                   |
|   - PERCENTILE_CONT(p): Percentile nội suy                       |
|   - PERCENTILE_DISC(p): Percentile rời rạc                       |
|   - APPROX_QUANTILE(p): Percentile xấp xỉ (big data)             |
|                                                                  |
|   CORRELATION & REGRESSION:                                       |
|   - CORR(y, x): Correlation coefficient                          |
|   - COVAR_SAMP/POP(y, x): Covariance                             |
|   - REGR_SLOPE(y, x): Hệ số góc                                  |
|   - REGR_INTERCEPT(y, x): Hệ số chặn                             |
|   - REGR_R2(y, x): R-squared                                     |
|                                                                  |
+------------------------------------------------------------------+
```

---

**Tiếp theo:** [07 - Import/Export Data](./07-import-export-data.md)

---

*DuckDB Tutorial - MangoAds Internal Training*
*"Học DuckDB qua ví dụ thực tế"*
