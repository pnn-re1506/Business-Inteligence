# Business Intelligence Systems and Analytics: Tài liệu ôn thi Final (Session 1–8)

> Tài liệu này đi theo course outline 8 session. Mỗi session có: **khái niệm cốt lõi → so sánh/bảng biểu → ví dụ tính toán → câu hỏi hay ra thi**. Thuật ngữ giữ nguyên tiếng Anh vì đề thi thường dùng tiếng Anh.

---

## 0. ROADMAP ÔN THI

### 0.1 Bức tranh tổng thể (phải nhớ trước tiên)

Cả môn học là **một pipeline duy nhất**. Hiểu được sơ đồ này thì 8 session tự liên kết với nhau:

```
[Nguồn dữ liệu]  ──►  [ETL / Staging]  ──►  [Data Warehouse]  ──►  [OLAP / Data Mart]  ──►  [Data Mining]  ──►  [Visualization / Dashboard]  ──►  Quyết định
 OLTP, file,          Session 5–6           Session 3–4            Session 2               Session 7           Session 8
 ERP, CRM, web
 (Session 2)
                         ▲
                Session 1: Bài toán kinh doanh (problem statement) quyết định mọi thứ phía sau
```

### 0.2 Lộ trình học theo giai đoạn

| Giai đoạn | Nội dung | Mục tiêu đầu ra | Trọng tâm thi |
|---|---|---|---|
| **1. Nền tảng** | Session 1 + 2 | Giải thích BI là gì, so sánh OLTP vs OLAP, 5 phép toán OLAP | Câu lý thuyết, so sánh |
| **2. Thiết kế DW** (quan trọng nhất) | Session 3 + 4 | Tự vẽ được star schema từ một đề bài, xử lý SCD | Câu thiết kế, nhiều điểm |
| **3. ETL** | Session 5 + 6 | Mô tả quy trình ETL, liệt kê và đưa giải pháp cho các thách thức tích hợp | Câu tình huống |
| **4. Data mining** | Session 7 | Tính được support/confidence/lift, entropy/information gain, precision/recall, k-means | Câu tính toán |
| **5. Visualization** | Session 8 | Chọn đúng biểu đồ, nêu nguyên tắc thiết kế dashboard | Câu phân tích, phê bình biểu đồ |
| **6. Tổng ôn** | Toàn bộ | Làm bộ câu hỏi ở mục 9, vẽ lại pipeline từ trí nhớ | Đề tổng hợp |

**Cách học hiệu quả khi "chưa có gì trong đầu":**
1. Đọc mục 0.1 cho tới khi vẽ lại được pipeline mà không cần nhìn.
2. Mỗi session: đọc phần lý thuyết, sau đó **tự làm lại ví dụ** bằng giấy bút (không chỉ đọc).
3. Với Session 3–4: lấy bất kỳ một nghiệp vụ nào (bán hàng, đặt phòng khách sạn, thư viện, bệnh viện) và tự thiết kế star schema. Đây là dạng câu hỏi gần như chắc chắn có.
4. Học thuộc các **bảng so sánh** (OLTP/OLAP, Inmon/Kimball, Star/Snowflake, ETL/ELT, SCD types). Đề rất hay hỏi "Compare A and B".
5. Cuối cùng làm phần câu hỏi luyện tập ở mục 9 mà không xem đáp án.

---

## 1. SESSION 1: Introduction and Problem Statements

### 1.1 Business Intelligence là gì?
- **Định nghĩa:** BI là tập hợp các **quy trình, kiến trúc, công nghệ và công cụ** giúp biến dữ liệu thô thành thông tin có ý nghĩa, hỗ trợ **ra quyết định kinh doanh** tốt hơn và nhanh hơn.
- Thuật ngữ được Howard Dresner (Gartner) phổ biến năm 1989.
- **Business Analytics (BA)** thường được xem là phần mở rộng của BI, tập trung nhiều hơn vào dự đoán và tối ưu.

### 1.2 Kim tự tháp DIKW
| Cấp | Ý nghĩa | Ví dụ |
|---|---|---|
| **Data** | Sự kiện thô, chưa có ngữ cảnh | `2026-11-01, P001, 5, 200000` |
| **Information** | Dữ liệu đã được tổ chức, có ngữ cảnh | "Ngày 1/11 bán 5 sản phẩm P001, doanh thu 200k" |
| **Knowledge** | Quy luật, hiểu biết rút ra từ thông tin | "P001 bán chạy vào đầu tháng" |
| **Wisdom** | Hành động/quyết định đúng đắn | "Tăng tồn kho P001 vào cuối mỗi tháng" |

### 1.3 Bốn cấp độ Analytics (rất hay hỏi)
| Loại | Câu hỏi trả lời | Kỹ thuật | Giá trị và độ khó |
|---|---|---|---|
| **Descriptive** | What happened? | Báo cáo, dashboard, OLAP | Thấp nhất |
| **Diagnostic** | Why did it happen? | Drill-down, phân tích tương quan | ↑ |
| **Predictive** | What will happen? | Data mining, machine learning, forecasting | ↑↑ |
| **Prescriptive** | What should we do? | Optimization, simulation, recommendation | Cao nhất |

### 1.4 Các cấp ra quyết định trong tổ chức
| Cấp | Người dùng | Loại quyết định | Công cụ BI |
|---|---|---|---|
| **Strategic** | Ban giám đốc (C-level) | Dài hạn, không cấu trúc | Strategic dashboard, KPI scorecard |
| **Tactical** | Quản lý cấp trung | Trung hạn, bán cấu trúc | Analytical report, OLAP |
| **Operational** | Nhân viên, giám sát | Hằng ngày, có cấu trúc | Operational dashboard, báo cáo real-time |

### 1.5 Kiến trúc BI tổng quát (các thành phần)
1. **Data sources**: OLTP databases (ERP, CRM, POS), flat files (CSV, Excel), web/API, IoT, dữ liệu bên ngoài.
2. **ETL / Data integration**: trích xuất, làm sạch, chuyển đổi, nạp dữ liệu.
3. **Data Warehouse / Data Marts**: kho dữ liệu tích hợp, phục vụ phân tích.
4. **Analytics layer**: OLAP cubes, data mining, statistical analysis.
5. **Presentation layer**: reports, dashboards, ad-hoc query, visualization.
6. **Metadata & governance**: xuyên suốt tất cả các tầng.

### 1.6 Problem Statement trong dự án BI
Dự án BI **bắt đầu từ câu hỏi kinh doanh, không bắt đầu từ dữ liệu**. Một problem statement tốt gồm:
- **Bối cảnh doanh nghiệp** (business context): công ty làm gì, gặp vấn đề gì.
- **Vấn đề cụ thể**: ví dụ "doanh thu Q3 giảm 15% mà không rõ nguyên nhân".
- **Stakeholders**: ai sẽ dùng kết quả (CEO, Sales Manager, Marketing...).
- **Business questions**: các câu hỏi cụ thể cần trả lời. Ví dụ:
  - Doanh thu theo tháng/quý/năm, theo khu vực, theo dòng sản phẩm?
  - Top 10 khách hàng mang lại doanh thu cao nhất?
  - Sản phẩm nào thường được mua cùng nhau?
- **KPIs** (Key Performance Indicators): chỉ số đo lường, nên theo tiêu chí **SMART** (Specific, Measurable, Achievable, Relevant, Time-bound). Ví dụ: Revenue, Gross Margin %, Customer Retention Rate, Average Order Value.
- **Phạm vi và nguồn dữ liệu** cần dùng.
- **Kết quả mong đợi**: dashboard, báo cáo, mô hình dự đoán.

> **Mẹo thi:** Từ business questions suy ra **measures** (con số: doanh thu, số lượng) và **dimensions** (góc nhìn: thời gian, sản phẩm, khu vực). Đây chính là cầu nối sang Session 3–4.

### 1.7 Lợi ích và thách thức của BI
- **Lợi ích:** ra quyết định dựa trên dữ liệu (data-driven), một nguồn sự thật duy nhất (single version of the truth), tiết kiệm thời gian làm báo cáo, phát hiện cơ hội và rủi ro, cải thiện hiệu suất vận hành.
- **Thách thức:** chất lượng dữ liệu kém, dữ liệu phân tán (data silos), chi phí cao, thiếu kỹ năng, người dùng không chấp nhận (user adoption), yêu cầu không rõ ràng, bảo mật và quyền riêng tư.

---

## 2. SESSION 2: OLTP vs. OLAP and Dataset Investigation

### 2.1 Bảng so sánh OLTP vs OLAP (HỌC THUỘC)
| Tiêu chí | **OLTP** (Online Transaction Processing) | **OLAP** (Online Analytical Processing) |
|---|---|---|
| Mục đích | Xử lý nghiệp vụ hằng ngày | Phân tích, hỗ trợ ra quyết định |
| Người dùng | Nhân viên, khách hàng (nhiều người) | Nhà phân tích, nhà quản lý (ít người hơn) |
| Thao tác | INSERT, UPDATE, DELETE, SELECT nhỏ | SELECT phức tạp, tổng hợp (aggregation) |
| Dữ liệu | Hiện tại, chi tiết | Lịch sử, tổng hợp và chi tiết |
| Thiết kế | **Normalized** (3NF), ER model | **Denormalized**, dimensional model (star/snowflake) |
| Kích thước | GB | TB–PB |
| Số bản ghi mỗi truy vấn | Vài bản ghi | Hàng triệu bản ghi |
| Thời gian phản hồi | Mili giây | Giây đến phút |
| Tối ưu cho | Ghi nhanh, toàn vẹn dữ liệu (ACID) | Đọc nhanh, truy vấn phức tạp |
| Cập nhật | Liên tục, real-time | Định kỳ theo batch (ETL), non-volatile |
| Định hướng | Application-oriented | Subject-oriented |
| Ví dụ | Hệ thống POS, ngân hàng, đặt vé | Data warehouse, báo cáo doanh thu |

### 2.2 Normalization (nhắc lại vì OLTP dùng)
- **1NF**: mỗi ô chứa giá trị nguyên tử, không có nhóm lặp.
- **2NF**: đạt 1NF và không có phụ thuộc bộ phận (mọi thuộc tính non-key phụ thuộc vào **toàn bộ** khóa chính).
- **3NF**: đạt 2NF và không có phụ thuộc bắc cầu (non-key không phụ thuộc vào non-key khác).
- **Tại sao OLTP normalize?** Giảm dư thừa, tránh update/insert/delete anomaly.
- **Tại sao OLAP denormalize?** Giảm số phép JOIN, truy vấn đọc nhanh hơn, dễ hiểu với người dùng nghiệp vụ.

### 2.3 Data Cube và các phép toán OLAP (RẤT HAY THI)
Dữ liệu được nhìn như một **khối nhiều chiều (cube)**: mỗi chiều là một dimension (Time, Product, Location), mỗi ô chứa measure (Sales).

| Phép toán | Mô tả | Ví dụ |
|---|---|---|
| **Roll-up** (drill-up) | Tổng hợp lên mức cao hơn của hierarchy, hoặc bỏ bớt một chiều | Doanh thu theo **tháng → quý → năm**; City → Country |
| **Drill-down** | Ngược lại roll-up: đi xuống mức chi tiết hơn | Năm → quý → tháng → ngày |
| **Slice** | Chọn **một giá trị** trên **một chiều**, tạo sub-cube ít hơn 1 chiều | Chỉ xem `Time = Q1-2026` |
| **Dice** | Chọn điều kiện trên **hai chiều trở lên**, tạo sub-cube | `Time ∈ {Q1,Q2}` AND `Location ∈ {HCM, HN}` AND `Product = Laptop` |
| **Pivot** (rotate) | Xoay trục, đổi hàng thành cột | Đổi Product từ hàng sang cột |
| *Drill-across* | Truy vấn qua nhiều fact table dùng chung conformed dimension | So sánh Sales và Inventory theo Product |
| *Drill-through* | Đi từ cube xuống dữ liệu chi tiết gốc | Từ tổng doanh thu xem từng hóa đơn |

**SQL tương ứng:**
```sql
-- Roll-up: tổng theo Year, Quarter và tổng con
SELECT d.Year, d.Quarter, SUM(f.SalesAmount)
FROM FactSales f JOIN DimDate d ON f.DateKey = d.DateKey
GROUP BY ROLLUP (d.Year, d.Quarter);

-- CUBE: tất cả tổ hợp tổng hợp (2^n nhóm)
GROUP BY CUBE (d.Year, p.Category);

-- Slice
WHERE d.Quarter = 'Q1' AND d.Year = 2026;

-- Dice
WHERE d.Quarter IN ('Q1','Q2') AND s.City IN ('HCM','HN');
```
> Với n dimension, CUBE tạo ra **2ⁿ** nhóm tổng hợp (cuboids). Ví dụ có 3 chiều thì có 8 cuboid, từ apex cuboid (tổng tất cả) đến base cuboid (chi tiết nhất).

### 2.4 Các kiểu lưu trữ OLAP
| Loại | Lưu trữ | Ưu điểm | Nhược điểm |
|---|---|---|---|
| **MOLAP** (Multidimensional) | Mảng đa chiều, tính sẵn aggregation | Truy vấn rất nhanh | Tốn dung lượng, khó mở rộng, xử lý cube lâu |
| **ROLAP** (Relational) | Bảng quan hệ (star schema) | Mở rộng tốt, dữ liệu lớn | Chậm hơn do truy vấn SQL trực tiếp |
| **HOLAP** (Hybrid) | Chi tiết ở ROLAP, tổng hợp ở MOLAP | Cân bằng hai loại | Phức tạp hơn |

### 2.5 Dataset Investigation (khảo sát dữ liệu)
Trước khi thiết kế DW phải **hiểu dữ liệu nguồn**:
1. **Hiểu nghiệp vụ**: dữ liệu mô tả quy trình gì (bán hàng, đặt hàng, kho...).
2. **Đọc schema / vẽ ERD nguồn**: các bảng, khóa chính/khóa ngoại, quan hệ 1-n, n-n.
3. **Data profiling** (lập hồ sơ dữ liệu):
   - Số dòng, số cột, kiểu dữ liệu.
   - Thống kê: min, max, mean, median, phân phối.
   - Tỷ lệ **NULL/missing**, số giá trị **distinct**, **duplicate**.
   - **Outlier** (giá trị bất thường, ví dụ quantity âm, tuổi = 200).
   - Định dạng không nhất quán (ngày `01/02/2026` vs `2026-02-01`, "HCM" vs "Ho Chi Minh City").
   - Kiểm tra toàn vẹn tham chiếu (orphan records: đơn hàng trỏ tới khách hàng không tồn tại).
4. **Xác định**: đâu là **sự kiện đo lường được** (ứng viên fact) và đâu là **ngữ cảnh mô tả** (ứng viên dimension).
5. **Đánh giá chất lượng dữ liệu** theo các chiều:

| Dimension | Ý nghĩa |
|---|---|
| **Accuracy** | Đúng với thực tế |
| **Completeness** | Không thiếu dữ liệu |
| **Consistency** | Nhất quán giữa các hệ thống |
| **Timeliness** | Cập nhật kịp thời |
| **Validity** | Đúng định dạng/miền giá trị |
| **Uniqueness** | Không trùng lặp |

Dataset mẫu thường dùng trong môn: **Northwind, AdventureWorks, Superstore, Online Retail (UCI)**.

---

## 3. SESSION 3: Data Warehouse Design

### 3.1 Định nghĩa Data Warehouse (Bill Inmon, HỌC THUỘC 4 đặc điểm)
> "A data warehouse is a **subject-oriented, integrated, time-variant, and non-volatile** collection of data in support of management's decision-making process."

| Đặc điểm | Giải thích |
|---|---|
| **Subject-oriented** | Tổ chức theo chủ đề (Sales, Customer, Product), không theo ứng dụng |
| **Integrated** | Dữ liệu từ nhiều nguồn được thống nhất về tên, định dạng, đơn vị, mã |
| **Time-variant** | Lưu lịch sử dài hạn (5–10 năm), mọi dữ liệu gắn với thời gian |
| **Non-volatile** | Dữ liệu được nạp vào và đọc, **không bị update/delete** như OLTP |

### 3.2 Các khái niệm liên quan
- **Data Mart**: tập con của DW phục vụ một phòng ban/chủ đề (Sales mart, Finance mart).
  - *Dependent data mart*: lấy dữ liệu từ DW trung tâm.
  - *Independent data mart*: lấy trực tiếp từ nguồn (dễ tạo data silo).
- **ODS (Operational Data Store)**: lưu dữ liệu tích hợp, gần real-time, dữ liệu hiện tại, có thể cập nhật. Phục vụ báo cáo vận hành.
- **Staging Area**: vùng trung gian để làm sạch và biến đổi dữ liệu trước khi nạp vào DW.
- **Metadata**: "dữ liệu về dữ liệu". Có *technical metadata* (schema, nguồn, quy tắc ETL) và *business metadata* (định nghĩa KPI, ý nghĩa nghiệp vụ).
- **Data Lake**: lưu dữ liệu thô mọi định dạng (structured, semi, unstructured), schema-on-read. DW thì schema-on-write.

### 3.3 Inmon vs Kimball (RẤT HAY THI)
| Tiêu chí | **Inmon** (Top-down) | **Kimball** (Bottom-up) |
|---|---|---|
| Cách tiếp cận | Xây Enterprise DW trước, sau đó tạo data marts | Xây các data marts theo quy trình nghiệp vụ, tích hợp qua **conformed dimensions** (Data Warehouse Bus) |
| Mô hình DW | **Normalized (3NF)** | **Dimensional (star schema)** |
| Thời gian triển khai | Lâu, chi phí ban đầu cao | Nhanh, có kết quả sớm |
| Tích hợp | Rất cao, single version of truth | Phụ thuộc vào kỷ luật dùng conformed dimensions |
| Linh hoạt | Dễ thích ứng thay đổi | Kém hơn khi nghiệp vụ thay đổi lớn |
| Phù hợp | Doanh nghiệp lớn, chiến lược dài hạn | Dự án cần kết quả nhanh, phạm vi phòng ban |

### 3.4 Kiến trúc DW
- **Single-tier**: giảm tối thiểu dữ liệu lưu trữ, ít dùng.
- **Two-tier**: nguồn tách khỏi DW.
- **Three-tier** (phổ biến nhất):
  1. **Bottom tier**: DW server (RDBMS) cùng ETL.
  2. **Middle tier**: OLAP server (ROLAP/MOLAP).
  3. **Top tier**: front-end tools (reporting, dashboard, data mining).

### 3.5 Dimensional Modeling: thành phần cơ bản
**Fact table** (bảng sự kiện):
- Lưu các **measures** (số liệu đo lường) của một quy trình nghiệp vụ.
- Chứa **foreign keys** tới các dimension tables.
- Rất nhiều dòng, ít cột, "dài và hẹp".
- Khóa chính thường là tổ hợp các FK (composite key).

**Dimension table** (bảng chiều):
- Lưu **ngữ cảnh mô tả**: who, what, where, when, why, how.
- Ít dòng, nhiều cột (thuộc tính văn bản), "ngắn và rộng".
- Có **surrogate key** làm khóa chính.
- Chứa **hierarchies** (Day → Month → Quarter → Year; City → Province → Country).

### 3.6 Quy trình thiết kế 4 bước của Kimball (HỌC THUỘC, gần như chắc chắn thi)
1. **Select the business process**: chọn quy trình nghiệp vụ cần phân tích (ví dụ: bán hàng lẻ, nhập kho, đặt phòng).
2. **Declare the grain**: khai báo **độ chi tiết** của một dòng trong fact table. Đây là bước **quan trọng nhất**.
   - Ví dụ: "Một dòng = một sản phẩm trên một dòng hóa đơn (line item) tại một cửa hàng trong một ngày."
   - Nên chọn **grain thấp nhất (atomic)** để linh hoạt nhất.
3. **Identify the dimensions**: các chiều mô tả cho mỗi dòng fact (Date, Product, Store, Customer, Promotion...).
4. **Identify the facts**: các measures số học phù hợp với grain (Quantity, SalesAmount, Discount, Cost, Profit).

### 3.7 Phân loại Measures (facts)
| Loại | Định nghĩa | Ví dụ |
|---|---|---|
| **Additive** | Cộng được theo **mọi** dimension | SalesAmount, Quantity |
| **Semi-additive** | Cộng được theo **một số** dimension, **không cộng theo thời gian** | Account Balance, Inventory Level (tồn kho): cộng theo kho được nhưng không cộng theo ngày, mà dùng AVG hoặc giá trị cuối kỳ |
| **Non-additive** | Không cộng được | Tỷ lệ %, Unit Price, Profit Margin %. **Lưu tử số và mẫu số** rồi tính lại khi truy vấn |

### 3.8 Star Schema vs Snowflake Schema vs Galaxy (Fact Constellation)
**Star schema**: 1 fact ở trung tâm, các dimension **denormalized** bao quanh, mỗi dimension chỉ cách fact 1 JOIN.

```
                 DimDate
                    │
DimCustomer ── FactSales ── DimProduct
                    │
                 DimStore
```

**Snowflake schema**: dimension được **normalize** thành nhiều bảng con (DimProduct → DimSubcategory → DimCategory).

**Galaxy / Fact constellation**: **nhiều fact tables** dùng chung (share) các dimension (ví dụ FactSales và FactInventory cùng dùng DimDate, DimProduct).

| Tiêu chí | Star | Snowflake |
|---|---|---|
| Chuẩn hóa dimension | Denormalized | Normalized |
| Số JOIN | Ít, nhanh hơn | Nhiều, chậm hơn |
| Dư thừa dữ liệu | Có | Ít hơn |
| Dung lượng | Lớn hơn (không đáng kể vì dimension nhỏ) | Nhỏ hơn |
| Dễ hiểu với người dùng | Rất dễ | Khó hơn |
| Bảo trì | Cập nhật thuộc tính phải sửa nhiều dòng | Dễ cập nhật hơn |
| Kimball khuyên dùng | **Có** | Hạn chế |

### 3.9 Ví dụ thiết kế hoàn chỉnh: Retail Sales
**Đề:** Một chuỗi siêu thị muốn phân tích doanh thu theo thời gian, sản phẩm, cửa hàng, khách hàng và chương trình khuyến mãi.

1. **Business process:** Retail sales (POS transactions).
2. **Grain:** Một dòng = một sản phẩm trên một hóa đơn bán hàng.
3. **Dimensions:** DimDate, DimProduct, DimStore, DimCustomer, DimPromotion.
4. **Facts:** Quantity, UnitPrice, DiscountAmount, SalesAmount, CostAmount, Profit.

```sql
CREATE TABLE DimDate (
  DateKey       INT PRIMARY KEY,        -- dạng 20261109
  FullDate      DATE,
  DayOfWeek     VARCHAR(10),
  DayOfMonth    INT,
  Month         INT,
  MonthName     VARCHAR(10),
  Quarter       INT,
  Year          INT,
  IsWeekend     BIT,
  IsHoliday     BIT
);

CREATE TABLE DimProduct (
  ProductKey    INT PRIMARY KEY,        -- surrogate key
  ProductID     VARCHAR(20),            -- natural/business key từ nguồn
  ProductName   VARCHAR(100),
  Brand         VARCHAR(50),
  Subcategory   VARCHAR(50),
  Category      VARCHAR(50),            -- hierarchy: Product → Subcategory → Category
  UnitCost      DECIMAL(12,2)
);

CREATE TABLE DimStore (
  StoreKey      INT PRIMARY KEY,
  StoreID       VARCHAR(20),
  StoreName     VARCHAR(100),
  City          VARCHAR(50),
  Province      VARCHAR(50),
  Region        VARCHAR(50)
);

CREATE TABLE DimCustomer (
  CustomerKey   INT PRIMARY KEY,
  CustomerID    VARCHAR(20),
  FullName      VARCHAR(100),
  Gender        VARCHAR(10),
  AgeGroup      VARCHAR(20),
  City          VARCHAR(50),
  MembershipTier VARCHAR(20),
  EffectiveDate DATE,                   -- cho SCD Type 2
  ExpiryDate    DATE,
  IsCurrent     BIT
);

CREATE TABLE DimPromotion (
  PromotionKey  INT PRIMARY KEY,
  PromotionName VARCHAR(100),
  PromotionType VARCHAR(50),
  DiscountPct   DECIMAL(5,2)
);

CREATE TABLE FactSales (
  DateKey       INT REFERENCES DimDate(DateKey),
  ProductKey    INT REFERENCES DimProduct(ProductKey),
  StoreKey      INT REFERENCES DimStore(StoreKey),
  CustomerKey   INT REFERENCES DimCustomer(CustomerKey),
  PromotionKey  INT REFERENCES DimPromotion(PromotionKey),
  InvoiceNumber VARCHAR(20),            -- degenerate dimension
  Quantity      INT,
  UnitPrice     DECIMAL(12,2),          -- non-additive
  DiscountAmount DECIMAL(12,2),
  SalesAmount   DECIMAL(14,2),          -- additive
  CostAmount    DECIMAL(14,2),
  Profit        DECIMAL(14,2),
  PRIMARY KEY (DateKey, ProductKey, StoreKey, CustomerKey, PromotionKey, InvoiceNumber)
);
```

**Truy vấn mẫu trả lời business question:**
```sql
-- Doanh thu và lợi nhuận theo Category, theo Quý năm 2026, chỉ miền Nam
SELECT p.Category, d.Quarter, SUM(f.SalesAmount) AS Revenue, SUM(f.Profit) AS Profit
FROM FactSales f
JOIN DimProduct p ON f.ProductKey = p.ProductKey
JOIN DimDate d    ON f.DateKey = d.DateKey
JOIN DimStore s   ON f.StoreKey = s.StoreKey
WHERE d.Year = 2026 AND s.Region = 'South'
GROUP BY p.Category, d.Quarter
ORDER BY p.Category, d.Quarter;
```

---

## 4. SESSION 4: Data Warehouse Design (cont.): Kỹ thuật nâng cao

### 4.1 Surrogate Key vs Natural Key
- **Natural (business) key**: khóa từ hệ thống nguồn (CustomerID = "KH001").
- **Surrogate key**: số nguyên tự tăng, vô nghĩa về nghiệp vụ, do DW sinh ra.
- **Tại sao dùng surrogate key?**
  1. Cho phép lưu **lịch sử thay đổi (SCD Type 2)**: một khách hàng có nhiều phiên bản.
  2. **Tích hợp nhiều nguồn** có natural key trùng hoặc khác định dạng.
  3. Độc lập với thay đổi của hệ thống nguồn.
  4. JOIN bằng số nguyên nhanh hơn, tiết kiệm dung lượng.
  5. Xử lý được giá trị "Unknown/Not applicable" (ví dụ key = -1).

### 4.2 Slowly Changing Dimensions (SCD): HỌC KỸ
Xử lý khi thuộc tính của dimension **thay đổi theo thời gian** (khách hàng chuyển thành phố, sản phẩm đổi category).

| Type | Cách xử lý | Lưu lịch sử? | Khi nào dùng |
|---|---|---|---|
| **Type 0** | Giữ nguyên giá trị gốc, không cho thay đổi | Không | Ngày sinh, ngày đăng ký |
| **Type 1** | **Ghi đè** (overwrite) giá trị cũ | Không | Sửa lỗi chính tả, thuộc tính không quan trọng về lịch sử |
| **Type 2** | **Thêm dòng mới** với surrogate key mới, có `EffectiveDate`, `ExpiryDate`, `IsCurrent` | **Có, đầy đủ** | Cần phân tích theo lịch sử (khách chuyển vùng, doanh thu tính đúng vùng tại thời điểm mua) |
| **Type 3** | **Thêm cột** `PreviousValue` / `CurrentValue` | Có, giới hạn (thường 1 bản trước) | Cần so sánh trước/sau một thay đổi (tổ chức lại khu vực bán hàng) |
| **Type 4** | Tách lịch sử sang **history table** riêng (mini-dimension) | Có | Thuộc tính thay đổi rất thường xuyên |
| **Type 6** | Kết hợp 1 + 2 + 3 (1+2+3 = 6) | Có | Vừa cần lịch sử, vừa cần giá trị hiện tại trên mọi dòng |

**Ví dụ SCD Type 2:** Khách hàng KH001 chuyển từ Hà Nội vào HCM ngày 2026-06-01.

| CustomerKey | CustomerID | Name | City | EffectiveDate | ExpiryDate | IsCurrent |
|---|---|---|---|---|---|---|
| 101 | KH001 | An | Hà Nội | 2023-01-01 | 2026-05-31 | 0 |
| 245 | KH001 | An | HCM | 2026-06-01 | 9999-12-31 | 1 |

Đơn hàng trước 1/6 trỏ vào key 101 (Hà Nội), sau 1/6 trỏ vào key 245 (HCM). Báo cáo doanh thu theo thành phố vì vậy **đúng lịch sử**.

**Cùng tình huống với Type 1:** chỉ sửa City = HCM, mất lịch sử, toàn bộ doanh thu cũ bị tính vào HCM.

**Cùng tình huống với Type 3:**

| CustomerKey | CustomerID | Name | CurrentCity | PreviousCity |
|---|---|---|---|---|
| 101 | KH001 | An | HCM | Hà Nội |

### 4.3 Các loại Fact Table
| Loại | Mô tả | Grain | Ví dụ |
|---|---|---|---|
| **Transaction fact** | Mỗi dòng là một sự kiện tại một thời điểm | 1 giao dịch | Dòng hóa đơn bán hàng |
| **Periodic snapshot fact** | Chụp trạng thái theo chu kỳ đều đặn | 1 kỳ (ngày/tháng) | Tồn kho cuối ngày, số dư tài khoản cuối tháng |
| **Accumulating snapshot fact** | Theo dõi một quy trình có các mốc, **dòng được cập nhật** khi tiến tới mốc mới, có nhiều cột ngày | 1 quy trình | Xử lý đơn hàng: OrderDate, ShipDate, DeliveryDate; tuyển sinh; xử lý claim bảo hiểm |
| **Factless fact** | Không có measure, chỉ ghi nhận sự kiện hoặc độ phủ (coverage) | 1 sự kiện | Điểm danh sinh viên (Student, Course, Date), sản phẩm được khuyến mãi nhưng không bán được |
| **Aggregate fact** | Fact tổng hợp sẵn để tăng tốc | Thô hơn | Doanh thu theo tháng theo category |

### 4.4 Các loại Dimension đặc biệt
| Loại | Mô tả | Ví dụ |
|---|---|---|
| **Conformed dimension** | Dimension dùng chung, **cùng ý nghĩa** giữa nhiều fact/data mart. Là chìa khóa tích hợp của Kimball | DimDate, DimProduct dùng cho cả Sales và Inventory |
| **Degenerate dimension** | Thuộc tính dimension **nằm luôn trong fact table**, không có bảng riêng | InvoiceNumber, OrderNumber |
| **Junk dimension** | Gom nhiều cờ/flag nhỏ, ít giá trị vào một dimension | PaymentType, IsGift, OrderChannel |
| **Role-playing dimension** | Một dimension vật lý đóng **nhiều vai** trong cùng fact | DimDate làm OrderDate, ShipDate, DeliveryDate |
| **Outrigger dimension** | Dimension tham chiếu tới dimension khác (snowflake cục bộ) | DimCustomer → DimDate (FirstPurchaseDate) |
| **Mini-dimension** | Tách thuộc tính thay đổi nhanh ra khỏi dimension lớn | Demographics (thu nhập, độ tuổi) của khách hàng |
| **Bridge table** | Xử lý quan hệ **nhiều-nhiều** giữa fact và dimension | Một tài khoản có nhiều chủ sở hữu; bệnh nhân có nhiều chẩn đoán |
| **Date dimension** | Luôn tạo riêng, không dùng hàm ngày trong SQL, vì chứa thuộc tính nghiệp vụ (ngày lễ, mùa vụ, năm tài chính) | DimDate |

### 4.5 Enterprise Data Warehouse Bus Matrix
Ma trận: **hàng = business process (fact)**, **cột = conformed dimensions**. Dùng để lập kế hoạch và đảm bảo tích hợp.

| Business Process | Date | Product | Store | Customer | Promotion | Supplier | Warehouse |
|---|---|---|---|---|---|---|---|
| Retail Sales | ✓ | ✓ | ✓ | ✓ | ✓ | | |
| Inventory | ✓ | ✓ | ✓ | | | | ✓ |
| Purchase Orders | ✓ | ✓ | | | | ✓ | ✓ |

### 4.6 Hierarchies
- **Balanced (cân bằng)**: mọi nhánh cùng độ sâu (Day → Month → Quarter → Year).
- **Unbalanced / ragged**: các nhánh có độ sâu khác nhau (sơ đồ tổ chức nhân sự, một số quốc gia không có cấp tỉnh).
- **Multiple hierarchies**: Date có thể đi theo Calendar Year hoặc Fiscal Year; Day → Week → Year.

### 4.7 Tối ưu hiệu năng DW
- **Indexing**: bitmap index (cột ít giá trị distinct), B-tree index.
- **Partitioning**: chia fact table theo thời gian (theo tháng/năm).
- **Aggregates / Materialized views**: tính sẵn tổng hợp.
- **Columnar storage**: lưu theo cột, nén tốt, phù hợp truy vấn phân tích.

### 4.8 Ước lượng kích thước fact table (dạng bài tính)
> Ví dụ: 500 cửa hàng, mỗi cửa hàng trung bình 2.000 dòng hóa đơn/ngày, lưu 3 năm, mỗi dòng 100 bytes.
> Số dòng = 500 × 2.000 × 365 × 3 = **1.095.000.000 dòng**.
> Dung lượng ≈ 1,095 × 10⁹ × 100 B ≈ **109,5 GB** (chưa tính index).

---

## 5. SESSION 5: ETL and Data Integration Challenges

### 5.1 ETL là gì?
**ETL = Extract, Transform, Load**: quá trình đưa dữ liệu từ nguồn vào DW. Thường chiếm **60–80% công sức** của dự án DW.

```
Sources ──Extract──► Staging Area ──Transform──► ──Load──► Data Warehouse
```

### 5.2 Extract (trích xuất)
| Phương pháp | Mô tả | Ưu/Nhược |
|---|---|---|
| **Full extraction** | Lấy toàn bộ dữ liệu mỗi lần | Đơn giản nhưng chậm, tốn tài nguyên |
| **Incremental extraction** | Chỉ lấy dữ liệu mới/thay đổi từ lần chạy trước (dựa vào cột `LastModified`, timestamp, ID tăng dần) | Nhanh, nhưng cần cột theo dõi thay đổi, khó bắt bản ghi bị xóa |
| **CDC (Change Data Capture)** | Bắt thay đổi qua transaction log, trigger hoặc so sánh snapshot | Gần real-time, chính xác, nhưng phức tạp |

- **Online extraction**: kết nối trực tiếp hệ thống nguồn.
- **Offline extraction**: thông qua file dump hoặc export.
- **Nguyên tắc:** giảm tối đa ảnh hưởng tới hệ thống OLTP nguồn (chạy ngoài giờ cao điểm).

### 5.3 Transform (biến đổi): phần quan trọng nhất
| Kỹ thuật | Ví dụ |
|---|---|
| **Data cleansing** (làm sạch) | Sửa lỗi chính tả, loại giá trị không hợp lệ |
| **Standardization** (chuẩn hóa định dạng) | Ngày về `YYYY-MM-DD`; "M"/"Male"/"1" thành "Male"; đơn vị tiền tệ, đo lường |
| **Handling missing values** | Thay bằng mặc định ("Unknown"), mean/median, hoặc loại bỏ |
| **Deduplication** | Loại bản ghi trùng (cùng một khách hàng ở 2 hệ thống) |
| **Data type conversion** | String thành Date, Number |
| **Derivation / Calculation** | `Profit = SalesAmount − CostAmount`; `AgeGroup` từ `BirthDate` |
| **Splitting / Merging** | Tách `FullName` thành `FirstName` và `LastName`; gộp địa chỉ |
| **Lookup / Surrogate key assignment** | Tra natural key để lấy surrogate key tương ứng của dimension |
| **Aggregation** | Tổng hợp theo ngày/tháng |
| **Filtering** | Loại bỏ đơn test, đơn bị hủy |
| **Joining / Integration** | Ghép dữ liệu nhiều nguồn |
| **Validation** | Kiểm tra quy tắc nghiệp vụ (Quantity > 0) |
| **Encoding / Mapping** | Ánh xạ mã khác nhau về một chuẩn chung |

### 5.4 Load (nạp)
| Kiểu | Mô tả |
|---|---|
| **Initial load** | Nạp lần đầu toàn bộ dữ liệu lịch sử |
| **Incremental load** | Chỉ nạp dữ liệu mới/thay đổi |
| **Full refresh** | Xóa toàn bộ và nạp lại |
| **Upsert (MERGE)** | Có thì update, chưa có thì insert |

**Thứ tự nạp:** **Dimension trước, Fact sau** (vì fact cần surrogate key của dimension).

### 5.5 ETL vs ELT
| Tiêu chí | **ETL** | **ELT** |
|---|---|---|
| Thứ tự | Transform **trước** khi load | Load dữ liệu thô vào đích **rồi mới** transform |
| Nơi transform | ETL server / staging | Ngay trong DW/data lake (sức mạnh tính toán của đích) |
| Phù hợp | DW truyền thống on-premise, dữ liệu có cấu trúc | Cloud DW (Snowflake, BigQuery, Redshift), big data |
| Dữ liệu thô | Không giữ lại trong đích | Giữ lại, có thể transform lại |
| Bảo mật/tuân thủ | Dễ lọc dữ liệu nhạy cảm trước khi nạp | Dữ liệu nhạy cảm vào đích trước |
| Tốc độ nạp | Chậm hơn | Nhanh hơn |

### 5.6 Công cụ ETL
- **Truyền thống:** SSIS (SQL Server Integration Services), Informatica PowerCenter, Talend, Pentaho Data Integration (Kettle), IBM DataStage, Oracle Data Integrator.
- **Hiện đại/Cloud:** Azure Data Factory, AWS Glue, Google Dataflow, Apache Airflow (orchestration), dbt (transform trong ELT), Fivetran.
- **Power BI:** Power Query cũng là một dạng ETL nhẹ.

---

## 6. SESSION 6: ETL and Data Integration Challenges (cont.)

### 6.1 Các thách thức tích hợp dữ liệu (HỌC THUỘC và NÊU ĐƯỢC GIẢI PHÁP)
| Thách thức | Ví dụ | Giải pháp |
|---|---|---|
| **Schema heterogeneity** (khác biệt cấu trúc) | Hệ A: `CustName`; hệ B: `Customer_Full_Name`; một hệ lưu địa chỉ 1 cột, hệ khác 5 cột | Schema mapping, xây dựng target schema chung |
| **Semantic heterogeneity** (khác ngữ nghĩa) | "Revenue" ở phòng Sales là trước thuế, ở Finance là sau thuế; "Customer" có hệ tính cả khách tiềm năng | Business glossary, thống nhất định nghĩa, metadata |
| **Syntactic / format heterogeneity** | Ngày `dd/mm/yyyy` vs `mm/dd/yyyy`; giới tính `M/F` vs `0/1`; tiền VND vs USD | Standardization rules, lookup/mapping tables |
| **Entity resolution / Duplicate records** | "Nguyễn Văn An" và "Nguyen Van An" là cùng một người | Matching rules, fuzzy matching, **Master Data Management (MDM)** |
| **Different keys** | Cùng sản phẩm có mã khác nhau ở mỗi hệ thống | Cross-reference table, surrogate key |
| **Data quality** | Missing, sai, outlier, không nhất quán | Data profiling, cleansing rules, data quality checks, reject table |
| **Data volume & performance** | Hàng trăm triệu dòng, cửa sổ ETL (batch window) ngắn | Incremental load, CDC, partitioning, parallel processing, bulk load |
| **Data latency / Timeliness** | Cần dữ liệu gần real-time | Streaming ETL, CDC, micro-batch |
| **Late-arriving facts** | Giao dịch ngày 1 đến DW vào ngày 5 | Lookup đúng phiên bản dimension (SCD2) theo ngày giao dịch |
| **Late-arriving dimensions (early-arriving facts)** | Fact có CustomerID mà dimension chưa có khách này | Tạo dòng **inferred member / placeholder** ("Unknown", key = -1), cập nhật sau |
| **Changing dimensions** | Khách đổi địa chỉ | Áp dụng SCD Type 1/2/3 |
| **Time zone / Unit differences** | Chi nhánh ở nhiều múi giờ, đơn vị kg vs lb | Chuẩn hóa về UTC/đơn vị chung |
| **Source system changes** | Nguồn thêm/đổi cột | Metadata-driven ETL, monitoring, versioning |
| **Error handling & recovery** | Job lỗi giữa chừng | Logging, checkpoints, restartability, rollback |
| **Auditing & lineage** | Không biết số liệu đến từ đâu | Audit columns (LoadDate, SourceSystem, BatchID), data lineage |
| **Security & privacy** | Dữ liệu cá nhân (PII) | Masking, encryption, phân quyền, tuân thủ quy định (GDPR, Nghị định 13/2023 của VN) |

### 6.2 Quy trình xử lý SCD Type 2 trong ETL
1. Đọc bản ghi từ nguồn (staging).
2. Lookup natural key trong dimension với `IsCurrent = 1`.
3. **Không tìm thấy** thì INSERT dòng mới (surrogate key mới, IsCurrent = 1).
4. **Tìm thấy và thuộc tính Type 2 thay đổi** thì:
   - UPDATE dòng cũ: `ExpiryDate = ngày hôm qua`, `IsCurrent = 0`.
   - INSERT dòng mới: `EffectiveDate = hôm nay`, `ExpiryDate = 9999-12-31`, `IsCurrent = 1`.
5. **Tìm thấy và chỉ thuộc tính Type 1 thay đổi** thì UPDATE ghi đè.
6. **Không thay đổi** thì bỏ qua.

```sql
-- Bước 4a: Đóng phiên bản cũ
UPDATE d
SET d.ExpiryDate = DATEADD(day, -1, CAST(GETDATE() AS DATE)), d.IsCurrent = 0
FROM DimCustomer d
JOIN Stg_Customer s ON d.CustomerID = s.CustomerID
WHERE d.IsCurrent = 1 AND d.City <> s.City;

-- Bước 3 và 4b: Thêm phiên bản mới cho khách mới hoặc khách vừa đổi City
INSERT INTO DimCustomer (CustomerID, FullName, City, EffectiveDate, ExpiryDate, IsCurrent)
SELECT s.CustomerID, s.FullName, s.City, CAST(GETDATE() AS DATE), '9999-12-31', 1
FROM Stg_Customer s
LEFT JOIN DimCustomer d ON d.CustomerID = s.CustomerID AND d.IsCurrent = 1
WHERE d.CustomerID IS NULL;
```

### 6.3 Quy trình nạp Fact
1. Extract các giao dịch mới (incremental).
2. Transform: làm sạch, tính toán measures.
3. **Surrogate key pipeline**: với mỗi natural key (ProductID, CustomerID, ngày...), lookup để lấy surrogate key tương ứng. Với SCD2 phải lấy phiên bản **có hiệu lực tại ngày giao dịch**: `TransactionDate BETWEEN EffectiveDate AND ExpiryDate`.
4. Không tìm thấy key thì gán key "Unknown" (-1) hoặc đưa vào bảng lỗi.
5. Load vào fact table (bulk insert).

### 6.4 Data Quality trong ETL
- **Data profiling** trước khi ETL.
- **Quy tắc kiểm tra:** NOT NULL, range check (0 ≤ Discount ≤ 100), format check (email, số điện thoại), referential integrity, business rule check.
- **Xử lý bản ghi lỗi:** reject/quarantine table, ghi log, thông báo, sửa và nạp lại.
- **Reconciliation:** đối chiếu số dòng và tổng tiền giữa nguồn và đích.

### 6.5 Master Data Management (MDM)
- Quản lý **dữ liệu chủ** (khách hàng, sản phẩm, nhà cung cấp, nhân viên) để có **một bản ghi vàng (golden record)** thống nhất trong toàn tổ chức.
- Gồm: matching, merging, survivorship rules (chọn giá trị nào đúng khi các nguồn mâu thuẫn), data stewardship.

### 6.6 Data Integration approaches
| Cách | Mô tả |
|---|---|
| **Consolidation** (ETL vào DW) | Gom dữ liệu vật lý vào một chỗ |
| **Federation / Virtualization** | Truy vấn trực tiếp nhiều nguồn qua một lớp ảo, không di chuyển dữ liệu |
| **Propagation** | Sao chép dữ liệu giữa hệ thống theo sự kiện (replication, EAI) |

---

## 7. SESSION 7: Data Mining

### 7.1 Khái niệm
- **Data mining**: quá trình **khám phá các mẫu (patterns), quy luật, tri thức hữu ích, chưa biết trước** từ tập dữ liệu lớn.
- Là một bước trong **KDD (Knowledge Discovery in Databases)**:
  **Selection → Preprocessing (cleaning) → Transformation → Data Mining → Interpretation/Evaluation → Knowledge**

### 7.2 CRISP-DM (Cross-Industry Standard Process for Data Mining): HỌC THUỘC 6 PHA
1. **Business Understanding**: xác định mục tiêu kinh doanh, chuyển thành mục tiêu data mining.
2. **Data Understanding**: thu thập, khám phá, đánh giá chất lượng dữ liệu.
3. **Data Preparation**: làm sạch, chọn thuộc tính, biến đổi (thường tốn công nhất).
4. **Modeling**: chọn kỹ thuật, xây dựng mô hình, tinh chỉnh tham số.
5. **Evaluation**: đánh giá mô hình có đạt mục tiêu kinh doanh không.
6. **Deployment**: triển khai, giám sát, bảo trì.

> Quy trình **lặp (iterative)**, có thể quay lại pha trước.

### 7.3 Phân loại tác vụ Data Mining
| Nhóm | Tác vụ | Học | Ví dụ |
|---|---|---|---|
| **Predictive** | **Classification** (dự đoán nhãn rời rạc) | Supervised | Khách có rời bỏ (churn) không? Email spam? |
| | **Regression** (dự đoán giá trị liên tục) | Supervised | Dự đoán doanh thu, giá nhà |
| | **Time-series forecasting** | Supervised | Dự báo nhu cầu tháng tới |
| **Descriptive** | **Clustering** (gom nhóm) | Unsupervised | Phân khúc khách hàng |
| | **Association rules** | Unsupervised | Market basket analysis: "mua bánh mì thì mua sữa" |
| | **Anomaly/Outlier detection** | Cả hai | Phát hiện gian lận thẻ tín dụng |
| | **Sequential pattern** | Unsupervised | Mua laptop rồi sau đó mua chuột |

**Supervised vs Unsupervised:**
- **Supervised**: dữ liệu huấn luyện **có nhãn** (biết đáp án), học hàm ánh xạ input sang output.
- **Unsupervised**: **không có nhãn**, tìm cấu trúc ẩn trong dữ liệu.

### 7.4 Data Preprocessing
- **Cleaning**: xử lý missing (xóa, điền mean/median/mode), noise, outlier.
- **Integration**: gộp nhiều nguồn.
- **Transformation / Normalization**:
  - **Min-max:** \( v' = \dfrac{v - \min}{\max - \min} \) (đưa về [0,1])
  - **Z-score:** \( v' = \dfrac{v - \mu}{\sigma} \)
  - Ví dụ: thu nhập 73.600, min = 12.000, max = 98.000 cho ra \( (73600-12000)/(98000-12000) = 0{,}716 \).
  - Ví dụ z-score: μ = 54.000, σ = 16.000 cho ra \( (73600-54000)/16000 = 1{,}225 \).
- **Reduction**: giảm chiều (PCA, feature selection), lấy mẫu (sampling).
- **Discretization**: chia khoảng (tuổi thành nhóm tuổi), binning.

### 7.5 Association Rules: Apriori (DẠNG BÀI TÍNH HAY THI)
**Công thức:**
- **Support(X)** = số giao dịch chứa X / tổng số giao dịch
- **Support(X → Y)** = Support(X ∪ Y)
- **Confidence(X → Y)** = Support(X ∪ Y) / Support(X)
- **Lift(X → Y)** = Confidence(X → Y) / Support(Y)
  - Lift > 1: X và Y **tương quan dương** (mua X làm tăng khả năng mua Y).
  - Lift = 1: **độc lập**.
  - Lift < 1: tương quan âm.

**Ví dụ:**
| TID | Items |
|---|---|
| T1 | Bread, Milk |
| T2 | Bread, Diaper, Beer, Eggs |
| T3 | Milk, Diaper, Beer, Coke |
| T4 | Bread, Milk, Diaper, Beer |
| T5 | Bread, Milk, Diaper, Coke |

Luật **{Milk, Diaper} → {Beer}**:
- {Milk, Diaper, Beer} xuất hiện ở T3, T4 nên Support = 2/5 = **0,4**
- {Milk, Diaper} xuất hiện ở T3, T4, T5, tức 3/5 = 0,6, nên Confidence = 0,4 / 0,6 = **0,667**
- {Beer} xuất hiện ở T2, T3, T4, tức 3/5 = 0,6, nên Lift = 0,667 / 0,6 = **1,11** (tương quan dương nhẹ)

**Thuật toán Apriori:**
- **Nguyên lý Apriori:** *Nếu một itemset là frequent thì mọi tập con của nó cũng frequent.* Ngược lại, nếu một itemset **không** frequent thì mọi tập cha của nó cũng không frequent, nên có thể **cắt tỉa (prune)**.
- **Các bước:**
  1. Đặt ngưỡng **min_support** và **min_confidence**.
  2. Tìm frequent 1-itemsets (L1): đếm support từng item, loại item < min_support.
  3. Sinh ứng viên k-itemsets (Ck) từ L(k−1), prune những tập có tập con không frequent.
  4. Đếm support, giữ lại Lk. Lặp lại tới khi không còn ứng viên.
  5. Từ các frequent itemsets, sinh luật có confidence ≥ min_confidence.

**Làm thử với min_support = 3/5 (tức 3 giao dịch):**
- L1: Bread(4), Milk(4), Diaper(4), Beer(3). Eggs(1) và Coke(2) bị loại.
- C2 với số lần xuất hiện: {Bread,Milk}=3 ✓, {Bread,Diaper}=3 ✓, {Bread,Beer}=2 ✗, {Milk,Diaper}=3 ✓, {Milk,Beer}=2 ✗, {Diaper,Beer}=3 ✓
- C3: chỉ còn ứng viên {Bread,Milk,Diaper} (các tập có Beer bị prune vì {Bread,Beer}, {Milk,Beer} không frequent). Đếm được 2 (T4, T5), không đạt.
- Kết quả: frequent itemsets lớn nhất có kích thước 2.
- Ví dụ luật Diaper → Beer: confidence = 3/4 = 0,75; lift = 0,75 / 0,6 = 1,25.

### 7.6 Classification: Decision Tree (ID3, C4.5, CART)
**Entropy:** \( E(S) = -\sum p_i \log_2 p_i \)
**Information Gain:** \( Gain(S, A) = E(S) - \sum_v \dfrac{|S_v|}{|S|} E(S_v) \)
Chọn thuộc tính có **Information Gain cao nhất** làm nút chia.
- C4.5 dùng **Gain Ratio** (khắc phục thiên vị thuộc tính có nhiều giá trị).
- CART dùng **Gini index**: \( Gini = 1 - \sum p_i^2 \).

**Ví dụ (Play Tennis, 14 mẫu: 9 Yes, 5 No):**
- \( E(S) = -\frac{9}{14}\log_2\frac{9}{14} - \frac{5}{14}\log_2\frac{5}{14} = 0{,}940 \)
- Thuộc tính **Outlook**:
  - Sunny: 5 mẫu (2 Yes, 3 No), E = 0,971
  - Overcast: 4 mẫu (4 Yes, 0 No), E = 0
  - Rain: 5 mẫu (3 Yes, 2 No), E = 0,971
- \( Gain = 0{,}940 - \left(\frac{5}{14}(0{,}971) + \frac{4}{14}(0) + \frac{5}{14}(0{,}971)\right) = 0{,}940 - 0{,}694 = \mathbf{0{,}246} \)
- (Các giá trị chuẩn của bài này: Humidity 0,151; Wind 0,048; Temperature 0,029. Vì vậy **Outlook** được chọn làm root.)

**Bảng log₂ hay dùng:** log₂(1/2) = −1; log₂(1/3) ≈ −1,585; log₂(2/3) ≈ −0,585; log₂(1/4) = −2; log₂(3/4) ≈ −0,415; log₂(2/5) ≈ −1,322; log₂(3/5) ≈ −0,737.
- Entropy của phân phối 50/50 = 1; của tập thuần nhất (pure) = 0.

**Ưu điểm cây quyết định:** dễ hiểu, dễ giải thích, xử lý được cả dữ liệu số và phân loại. **Nhược điểm:** dễ **overfitting**, khắc phục bằng **pruning** (pre-pruning, post-pruning).

### 7.7 Các thuật toán classification khác (biết ý tưởng)
- **Naive Bayes**: dựa trên định lý Bayes \( P(C|X) = \dfrac{P(X|C)P(C)}{P(X)} \), giả định các thuộc tính **độc lập có điều kiện**. Nhanh, tốt với text.
- **k-NN (k-Nearest Neighbors)**: gán nhãn theo đa số k láng giềng gần nhất (khoảng cách Euclidean). "Lazy learner".
- **Logistic Regression**: dự đoán xác suất cho bài toán nhị phân.
- **SVM, Neural Networks, Random Forest** (ensemble nhiều cây).

### 7.8 Đánh giá mô hình classification (DẠNG BÀI TÍNH)
**Confusion Matrix:**
| | Predicted Positive | Predicted Negative |
|---|---|---|
| **Actual Positive** | TP (True Positive) | FN (False Negative) |
| **Actual Negative** | FP (False Positive) | TN (True Negative) |

**Công thức:**
- **Accuracy** = (TP + TN) / Tổng
- **Precision** = TP / (TP + FP): trong số dự đoán Positive, bao nhiêu đúng?
- **Recall (Sensitivity, TPR)** = TP / (TP + FN): trong số Positive thật, bắt được bao nhiêu?
- **Specificity** = TN / (TN + FP)
- **F1-score** = 2 × Precision × Recall / (Precision + Recall)
- **Error rate** = 1 − Accuracy

**Ví dụ:** TP = 40, FN = 20, FP = 10, TN = 130 (tổng 200)
- Accuracy = (40 + 130) / 200 = **0,85**
- Precision = 40 / 50 = **0,80**
- Recall = 40 / 60 = **0,667**
- Specificity = 130 / 140 = **0,929**
- F1 = 2 × 0,8 × 0,667 / (0,8 + 0,667) = **0,727**

**Khi nào ưu tiên chỉ số nào?**
- **Recall** quan trọng khi bỏ sót rất tốn kém: chẩn đoán ung thư, phát hiện gian lận.
- **Precision** quan trọng khi báo động sai rất tốn kém: lọc spam (không muốn mất email thật).
- **Accuracy** dễ gây hiểu lầm khi dữ liệu **mất cân bằng** (imbalanced). Ví dụ 99% giao dịch bình thường, mô hình đoán "bình thường" hết vẫn đạt 99% accuracy.
- **ROC curve**: vẽ TPR theo FPR; **AUC** càng gần 1 càng tốt (0,5 tương đương đoán ngẫu nhiên).

**Phương pháp chia dữ liệu:**
- **Holdout**: train/test (70/30 hoặc 80/20).
- **k-fold cross-validation**: chia k phần, lần lượt mỗi phần làm test, lấy trung bình.
- **Overfitting**: mô hình học quá khớp dữ liệu train (train tốt, test kém). **Underfitting**: mô hình quá đơn giản.

### 7.9 Clustering
**K-means:**
1. Chọn k, khởi tạo k centroid.
2. Gán mỗi điểm vào cụm có centroid gần nhất (khoảng cách Euclidean).
3. Tính lại centroid = trung bình các điểm trong cụm.
4. Lặp bước 2 và 3 đến khi centroid không đổi (hội tụ).

**Ví dụ 1 chiều:** dữ liệu {1, 2, 6, 7, 8, 15}, k = 2, centroid ban đầu c1 = 1, c2 = 8.
- Lần 1: cụm 1 = {1, 2}, cụm 2 = {6, 7, 8, 15}. Centroid mới c1 = 1,5; c2 = 9.
- Lần 2: điểm 6 cách c1 4,5 và cách c2 3, vẫn thuộc cụm 2. Các cụm không đổi nên **hội tụ**.

**Khoảng cách Euclidean 2D:** \( d = \sqrt{(x_1-x_2)^2 + (y_1-y_2)^2} \). Manhattan: \( |x_1-x_2| + |y_1-y_2| \).

**Đặc điểm K-means:** nhanh, đơn giản; nhưng phải chọn k trước (dùng **Elbow method**, Silhouette), nhạy với outlier và khởi tạo, chỉ tìm cụm dạng cầu.

**Hierarchical clustering:** agglomerative (bottom-up, gộp dần) hoặc divisive (top-down); kết quả là **dendrogram**; không cần chọn k trước.
**DBSCAN:** dựa trên mật độ, tìm được cụm hình dạng bất kỳ, phát hiện noise.

**Ứng dụng BI:** phân khúc khách hàng theo **RFM** (Recency, Frequency, Monetary).

### 7.10 Regression
- **Linear regression:** \( y = b_0 + b_1 x \). Đánh giá bằng **MAE, MSE, RMSE, R²**.
- R² ∈ [0,1]: tỷ lệ biến thiên của y được giải thích bởi mô hình.

### 7.11 Ứng dụng Data Mining trong kinh doanh
Market basket analysis (cross-selling, sắp xếp kệ hàng), customer segmentation, churn prediction, credit scoring, fraud detection, demand forecasting, recommendation systems.

### 7.12 Data mining vs OLAP
| OLAP | Data Mining |
|---|---|
| Người dùng đặt giả thuyết rồi kiểm chứng (hypothesis-driven) | Máy tự khám phá mẫu (discovery-driven) |
| Trả lời "what happened" | Trả lời "what will happen", "what patterns exist" |
| Descriptive | Predictive và descriptive |

---

## 8. SESSION 8: Fundamentals of Visualization

### 8.1 Tại sao cần visualization?
- Não người xử lý hình ảnh nhanh hơn văn bản/bảng số rất nhiều.
- **Anscombe's quartet**: 4 tập dữ liệu có cùng mean, variance, correlation, đường hồi quy nhưng khi vẽ ra **hoàn toàn khác nhau**. Đây là bằng chứng phải trực quan hóa, không chỉ nhìn thống kê.
- Mục đích: **exploratory** (khám phá, cho người phân tích) và **explanatory** (giải thích, truyền đạt cho người khác, kể chuyện bằng dữ liệu, *data storytelling*).

### 8.2 Nguyên tắc của Edward Tufte
- **Data-ink ratio** = mực dùng để biểu diễn dữ liệu / tổng mực. **Tối đa hóa** tỷ lệ này: bỏ trang trí thừa.
- **Chartjunk**: các yếu tố trang trí không mang thông tin (3D, hiệu ứng bóng, hình nền, grid đậm). **Tránh**.
- **Lie factor** = (độ lớn hiệu ứng trên hình) / (độ lớn hiệu ứng trong dữ liệu). Lý tưởng là **1**. Lie factor lớn hơn 1,05 hoặc nhỏ hơn 0,95 được coi là bóp méo.
- **Graphical integrity**: không bóp méo dữ liệu (ví dụ trục y của **bar chart phải bắt đầu từ 0**).
- **Small multiples**: nhiều biểu đồ nhỏ cùng thang đo để so sánh.

### 8.3 Shneiderman's Visual Information-Seeking Mantra
> **"Overview first, zoom and filter, then details-on-demand."**

### 8.4 Pre-attentive attributes (thuộc tính tiền chú ý)
Các thuộc tính thị giác mà não nhận ra **trong dưới 250ms** mà không cần tập trung: **màu sắc (hue, intensity), kích thước, hình dạng, vị trí, độ dài, hướng, độ đậm, chuyển động, bao quanh (enclosure)**.
- **Thứ hạng độ chính xác khi so sánh định lượng (Cleveland & McGill):** Vị trí trên cùng trục chung > Vị trí trên trục không cùng gốc > Độ dài > Góc/độ dốc > Diện tích > Thể tích > Màu sắc (độ đậm/bão hòa).
- Đó là lý do **bar chart tốt hơn pie chart** khi so sánh giá trị.

### 8.5 Gestalt principles (nguyên lý nhận thức thị giác)
| Nguyên lý | Ý nghĩa |
|---|---|
| **Proximity** | Các đối tượng gần nhau được xem là một nhóm |
| **Similarity** | Cùng màu/hình dạng được xem là cùng nhóm |
| **Enclosure** | Đối tượng trong cùng khung được xem là một nhóm |
| **Closure** | Não tự "điền" phần thiếu để thấy hình hoàn chỉnh |
| **Continuity** | Mắt đi theo đường liền mạch |
| **Connection** | Đối tượng được nối với nhau được xem là liên quan |
| **Figure/Ground** | Phân biệt đối tượng chính và nền |

### 8.6 Chọn biểu đồ phù hợp (RẤT HAY THI)
| Mục đích | Biểu đồ nên dùng | Ghi chú |
|---|---|---|
| **Comparison** (so sánh giữa các nhóm) | Bar chart (ngang nếu nhãn dài), column chart, grouped bar | Trục bắt đầu từ 0, sắp xếp theo giá trị |
| **Trend over time** (xu hướng) | **Line chart**, area chart | Thời gian luôn ở trục x |
| **Composition** (cơ cấu, tỷ trọng) | Stacked bar, 100% stacked bar, pie/donut (chỉ khi ≤ 5 phần và tổng = 100%), treemap, waterfall | Tránh pie nhiều lát, tránh 3D pie |
| **Distribution** (phân phối) | **Histogram**, box plot, violin plot, density plot | Box plot cho thấy median, quartile, outlier |
| **Relationship / Correlation** | **Scatter plot**, bubble chart, heatmap | Bubble thêm biến thứ 3 bằng kích thước |
| **Geographic** | Choropleth map, symbol map | |
| **Single KPI** | KPI card, gauge, bullet chart | Bullet chart tốt hơn gauge (tiết kiệm không gian) |
| **Ranking** | Sorted bar chart | |
| **Part-to-whole thay đổi theo từng bước** | Waterfall chart | Phân tích lợi nhuận |
| **Flow / process** | Funnel chart, Sankey | Phễu bán hàng |
| **Chi tiết chính xác** | Table, highlight table | Khi người dùng cần tra số cụ thể |

### 8.7 Dashboard
**Định nghĩa (Stephen Few):** "A dashboard is a visual display of the most important information needed to achieve one or more objectives, consolidated and arranged **on a single screen** so the information can be monitored **at a glance**."

**Các loại dashboard:**
| Loại | Người dùng | Mục đích | Tần suất cập nhật |
|---|---|---|---|
| **Strategic** | Lãnh đạo | Theo dõi KPI tổng thể, mục tiêu dài hạn | Hằng tháng/quý |
| **Analytical** | Nhà phân tích | Khám phá, drill-down tìm nguyên nhân | Theo nhu cầu |
| **Operational** | Nhân viên vận hành | Giám sát hoạt động real-time | Real-time/hằng ngày |

**Nguyên tắc thiết kế dashboard:**
1. **Biết người dùng và mục tiêu**: dashboard trả lời câu hỏi gì, cho ai.
2. **Vừa một màn hình**, không cuộn.
3. **Bố cục theo hướng đọc (Z-pattern / F-pattern)**: thông tin quan trọng nhất ở **góc trên bên trái**.
4. **KPI cards ở trên cùng**, sau đó là xu hướng, cuối cùng là chi tiết.
5. **Giới hạn số lượng visual** (khoảng 5–9).
6. **Dùng màu có chủ đích**: màu xám cho phần thường, màu nổi bật cho phần cần chú ý; đỏ/xanh cho xấu/tốt (chú ý người mù màu, tránh cặp đỏ-xanh lá đơn thuần).
7. **Cung cấp ngữ cảnh**: so sánh với target, kỳ trước (MoM, YoY), benchmark.
8. **Nhất quán**: font, màu, định dạng số, thang đo.
9. **Tương tác**: filter, slicer, drill-down, tooltip.
10. **Loại bỏ chartjunk**, tối đa data-ink ratio.

**Loại màu:**
- **Sequential**: một màu từ nhạt tới đậm, cho dữ liệu có thứ tự (thấp tới cao).
- **Diverging**: hai màu đối lập với điểm giữa trung tính, cho dữ liệu có điểm giữa (âm/dương, dưới/trên mục tiêu).
- **Categorical (qualitative)**: các màu khác biệt, cho nhóm không có thứ tự.

### 8.8 Lỗi trực quan hóa thường gặp
- Trục y bị cắt (không bắt đầu từ 0) ở bar chart làm phóng đại khác biệt.
- 3D chart, pie chart quá nhiều lát.
- Dùng quá nhiều màu, màu không có ý nghĩa.
- Dual-axis gây hiểu nhầm.
- Không có tiêu đề, nhãn, đơn vị, nguồn.
- Chọn sai loại biểu đồ (dùng line chart cho dữ liệu phân loại không có thứ tự).
- Quá tải thông tin.

### 8.9 Công cụ visualization/BI
- **Power BI** (Microsoft): Power Query (ETL), DAX (tính toán), data model (star schema).
- **Tableau**, **Qlik Sense**, **Looker / Looker Studio**, **Apache Superset**, **Metabase**.
- Lập trình: Python (Matplotlib, Seaborn, Plotly), R (ggplot2).

**DAX cơ bản (nếu lớp dùng Power BI):**
```DAX
Total Sales = SUM(FactSales[SalesAmount])
Profit Margin % = DIVIDE(SUM(FactSales[Profit]), SUM(FactSales[SalesAmount]))
Sales LY = CALCULATE([Total Sales], SAMEPERIODLASTYEAR(DimDate[FullDate]))
YoY Growth % = DIVIDE([Total Sales] - [Sales LY], [Sales LY])
```

---

## 9. BỘ CÂU HỎI LUYỆN TẬP (tự làm trước khi xem gợi ý)

### Lý thuyết
1. Define Business Intelligence. Explain the components of a BI architecture.
2. Distinguish descriptive, diagnostic, predictive, and prescriptive analytics with examples.
3. Compare OLTP and OLAP (ít nhất 6 tiêu chí).
4. Explain the four characteristics of a data warehouse according to Inmon.
5. Compare the Inmon and Kimball approaches.
6. Compare star schema and snowflake schema. Why does Kimball recommend star schema?
7. What is a surrogate key? Why is it used instead of a natural key?
8. Explain SCD Type 1, 2, 3 with examples. Which type preserves full history?
9. Classify additive, semi-additive, and non-additive facts with examples.
10. Explain the transaction, periodic snapshot, accumulating snapshot, and factless fact tables.
11. Describe the ETL process. Compare ETL and ELT.
12. List 5 data integration challenges and propose solutions.
13. Describe the 6 phases of CRISP-DM.
14. Distinguish supervised and unsupervised learning with examples.
15. Explain Tufte's data-ink ratio and lie factor.
16. Which chart would you use for: (a) monthly revenue trend, (b) market share of 4 brands, (c) relationship between ad spend and sales, (d) distribution of customer age?

### Thiết kế (dạng tự luận nhiều điểm)
17. **Khách sạn:** Thiết kế star schema để phân tích doanh thu đặt phòng theo thời gian, loại phòng, khách hàng, kênh đặt (online/offline), chi nhánh.
18. **Đại học:** Thiết kế DW phân tích kết quả học tập sinh viên theo học kỳ, môn học, giảng viên, khoa.
19. Thiết kế một **accumulating snapshot** cho quy trình xử lý đơn hàng thương mại điện tử.
20. Lập **bus matrix** cho một công ty có các quy trình: Sales, Inventory, Purchasing, Shipping.

### Tính toán
21. Cho tập giao dịch ở mục 7.5, tính support, confidence, lift của luật {Bread} → {Milk}.
22. Cho confusion matrix TP = 80, FP = 20, FN = 40, TN = 860. Tính Accuracy, Precision, Recall, F1. Nhận xét.
23. Một tập 10 mẫu có 6 Yes và 4 No. Tính entropy.
24. Chuẩn hóa min-max giá trị 50 trong khoảng [20, 120].
25. Chạy K-means (k = 2) với dữ liệu {2, 4, 10, 12, 3, 20, 30, 11, 25}, centroid ban đầu 2 và 4.

### Gợi ý đáp án
- **17. Khách sạn:**
  - Business process: Room booking (đặt phòng).
  - Grain: một dòng = một lượt đặt một phòng cho một đêm (hoặc một booking line).
  - Dimensions: DimDate (role-playing: BookingDate, CheckInDate, CheckOutDate), DimRoomType, DimGuest (SCD2), DimChannel, DimHotelBranch, DimPromotion.
  - Facts: NumberOfNights, RoomRevenue, DiscountAmount, ServiceRevenue, TotalRevenue; BookingID là degenerate dimension.
- **18. Đại học:** Fact_Enrollment/Fact_Result (grain: một sinh viên, một môn, một học kỳ); dimensions: DimStudent, DimCourse, DimLecturer, DimFaculty (hoặc nằm trong DimCourse), DimSemester; facts: Score, Credits, IsPassed (0/1, có thể cộng để đếm số lượt đậu), AttendanceRate (non-additive).
- **19.** FactOrderFulfillment: OrderKey (degenerate), CustomerKey, ProductKey, OrderDateKey, PaymentDateKey, ShipDateKey, DeliveryDateKey, ReturnDateKey; measures: OrderAmount, ShippingCost, DaysToShip, DaysToDeliver. Dòng được **UPDATE** khi đơn hàng chuyển trạng thái.
- **20.** Hàng: Sales, Inventory, Purchasing, Shipping. Cột: Date, Product, Customer, Store/Warehouse, Supplier, Shipper, Promotion. Đánh dấu ✓ phù hợp (Date và Product dùng chung cho tất cả, đó là conformed dimensions).
- **21.** {Bread, Milk} có ở T1, T4, T5, nên Support = 3/5 = 0,6. Support(Bread) = 4/5 = 0,8, nên Confidence = 0,6/0,8 = 0,75. Support(Milk) = 0,8, nên Lift = 0,75/0,8 = **0,9375**. Lift nhỏ hơn 1 nghĩa là tương quan âm nhẹ: luật không hữu ích dù confidence cao.
- **22.** Accuracy = 940/1000 = 0,94; Precision = 80/100 = 0,80; Recall = 80/120 = 0,667; F1 = 0,727. Accuracy cao nhưng bỏ sót 1/3 số ca positive do dữ liệu mất cân bằng.
- **23.** E = −0,6·log₂0,6 − 0,4·log₂0,4 = 0,6 × 0,737 + 0,4 × 1,322 = **0,971**.
- **24.** (50 − 20)/(120 − 20) = **0,3**.
- **25.**
  - Lần 1 (c1 = 2, c2 = 4): điểm 3 cách đều hai centroid (bằng nhau, quy ước gán vào cụm 1). Cụm 1 = {2, 3}, cụm 2 = {4, 10, 12, 20, 30, 11, 25}. c1 = 2,5; c2 = 16.
  - Lần 2: cụm 1 = {2, 3, 4}, cụm 2 = {10, 11, 12, 20, 25, 30}. c1 = 3; c2 = 18.
  - Lần 3: 10 cách c1 7 và cách c2 8, nên vào cụm 1. Cụm 1 = {2, 3, 4, 10}, cụm 2 = {11, 12, 20, 25, 30}. c1 = 4,75; c2 = 19,6.
  - Lần 4: 11 cách c1 6,25 và cách c2 8,6; 12 cách c1 7,25 và cách c2 7,6, đều vào cụm 1. Cụm 1 = {2, 3, 4, 10, 11, 12}, cụm 2 = {20, 25, 30}. c1 = 7; c2 = 25.
  - Lần 5: các cụm không đổi, nên **hội tụ**. Kết quả: {2, 3, 4, 10, 11, 12} và {20, 25, 30}.

---

## 10. CHEAT SHEET CUỐI CÙNG (đọc lại 30 phút trước giờ thi)

- **BI pipeline:** Sources → ETL → DW → OLAP / Mining → Visualization → Decision.
- **4 cấp analytics:** Descriptive (what) → Diagnostic (why) → Predictive (what will) → Prescriptive (what should).
- **OLTP:** normalized, write, current, nhiều user. **OLAP:** denormalized, read, history, aggregation.
- **OLAP ops:** Roll-up, Drill-down, Slice (1 chiều), Dice (≥2 chiều), Pivot.
- **DW (Inmon):** Subject-oriented, Integrated, Time-variant, Non-volatile.
- **Inmon:** top-down, 3NF EDW. **Kimball:** bottom-up, star schema, conformed dimensions, bus matrix.
- **Kimball 4 bước:** Business process → **Grain** → Dimensions → Facts.
- **Facts:** additive, semi-additive (balance, inventory), non-additive (%, ratio, price).
- **Fact tables:** transaction, periodic snapshot, accumulating snapshot, factless.
- **Dimensions đặc biệt:** conformed, degenerate, junk, role-playing, mini, bridge, outrigger.
- **SCD:** 0 = giữ nguyên, 1 = ghi đè, 2 = thêm dòng (full history), 3 = thêm cột, 4 = bảng history, 6 = 1+2+3.
- **ETL:** Extract (full, incremental, CDC) → Transform (clean, standardize, dedupe, lookup surrogate key) → Load (dimension trước, fact sau).
- **ETL vs ELT:** ELT transform trong đích (cloud DW).
- **Integration challenges:** schema, semantic, syntactic heterogeneity; duplicates/entity resolution; data quality; late-arriving data; performance; security.
- **CRISP-DM:** Business Understanding → Data Understanding → Data Preparation → Modeling → Evaluation → Deployment.
- **Apriori:** support = P(X∪Y); confidence = P(X∪Y)/P(X); lift = conf/P(Y).
- **Decision tree:** entropy −Σp·log₂p; chọn thuộc tính có gain lớn nhất.
- **Metrics:** Precision = TP/(TP+FP); Recall = TP/(TP+FN); F1 = 2PR/(P+R).
- **K-means:** gán vào centroid gần nhất, tính lại trung bình, lặp đến khi hội tụ.
- **Visualization:** data-ink ratio ↑, chartjunk ↓, lie factor = 1, bar bắt đầu từ 0; trend dùng line, comparison dùng bar, distribution dùng histogram/box, relationship dùng scatter, composition dùng stacked bar/pie ≤ 5.
- **Shneiderman:** Overview first, zoom and filter, details on demand.
- **Dashboard:** 1 màn hình, quan trọng nhất ở góc trên trái, có ngữ cảnh (target, kỳ trước), màu có chủ đích.
