# Giải thích Lecture 1, 2, 3, 5 và 6

> Cùng giảng viên với Lecture 4 (TS. Phan Trọng Nhân). File này đi theo đúng nội dung slide, giải các câu hỏi giảng viên đặt ngay trên slide, và có phần "câu hỏi thi" cuối mỗi lecture.
>
> Ánh xạ với course outline: Lecture 1 = Session 1, Lecture 2 = Session 2, Lecture 3 = Session 3–4 (thiết kế DW + SCD), Lecture 4 = Session 5–6 (ETL), Lecture 5 = Session 7 (data mining), Lecture 6 = Session 1 (BI) + Session 8 (visualization).

---

# LECTURE 1: INTRODUCTION (53 slide)

## Câu chuyện Alice (slide 3–17): vì sao cần BI và Data Warehouse

Alice mở một nhà hàng Việt ở Sài Gòn, rồi mở rộng nhiều chi nhánh, nhiều món, nhiều kênh bán (dine-in, delivery, take-away), lại gặp dịch, lockdown, chi phí tăng. Cô quản lý số liệu bằng Excel nên:

- Tổng hợp doanh thu rất lâu, mỗi kỳ (tuần, tháng, năm) phải làm lại từ đầu.
- Sai một chỗ phải sửa rồi kiểm lại toàn bộ số.
- Slide 12 gọi đây là **"such a rice consumption system"**: hệ thống ngốn thời gian, làm thủ công, không tái sử dụng được.

**Ba nhóm vấn đề kinh điển của hệ thống vận hành (theo Inmon, slide 13–15):**

| Nhóm | Biểu hiện | Nguyên nhân |
|---|---|---|
| **Thiếu độ tin cậy (credibility)** | Sales nói doanh thu +10%, Finance nói −15%, SCM nói +5% | Không có mốc thời gian chung; công thức tính khác nhau; mức độ tổng hợp khác nhau; dùng dữ liệu ngoài không kiểm soát; không có nguồn dữ liệu chung ngay từ đầu; lỗi các loại |
| **Năng suất (productivity)** | Lâu mới ra được một báo cáo, phải nhờ lập trình viên | Dữ liệu lớn (3V), công nghệ khác nhau, không tái sử dụng được, không biết trước nhu cầu tương lai |
| **Không lấy được thông tin** | Dữ liệu không có hoặc không được tích hợp | Không lưu lịch sử, nhu cầu dữ liệu không lường trước, các ứng dụng cũ không tích hợp với nhau |

**Giải pháp:** một CSDL tập trung (slide 16) vẫn chưa đủ, vì truy vấn trên hệ thống vận hành vẫn chậm và không lưu lịch sử. Giải pháp tốt hơn (slide 17) là **Data Warehouse + dashboard BI**: một nguồn sự thật duy nhất, số liệu đã được tích hợp, người dùng tự xem báo cáo mà không phải làm lại Excel.

## Data Warehouse (slide 18–29)

**Định nghĩa (slide 20):** DW là kho lưu trữ trung tâm, dữ liệu trong đó được phân tích để ra quyết định sáng suốt hơn.

**Lợi ích (slide 21):** dữ liệu nhất quán, quyết định tốt hơn, truy cập dữ liệu toàn doanh nghiệp dễ hơn, tài liệu hóa dữ liệu ở một nơi, **độc lập với hệ thống vận hành** (phân tích không làm chậm hệ thống bán hàng).

**4 đặc tính của Inmon (slide 22, học thuộc):** Subject-oriented, Integrated, Non-volatile, Time-variant.

**Kiến trúc 3 tầng (slide 23–24, hình Han và Inmon):**

| Tầng | Thành phần | Vai trò |
|---|---|---|
| **Bottom tier** | DW server: dữ liệu, data mart, metadata repository; bên dưới là Extract, Clean, Transform, Load, Refresh từ operational DB và nguồn ngoài | Lưu trữ |
| **Middle tier** | OLAP server (cube) | Tính toán phân tích đa chiều |
| **Top tier** | Query/report, phân tích, data mining | Công cụ cho người dùng |

Mũi tên **Backflushing** đẩy dữ liệu đã làm sạch về hệ thống nguồn. **Updates/new data** đưa kết quả phân tích trở lại vận hành.

**Architected environment (slide 25–26, Inmon): dữ liệu đi qua 4 mức**

```
Operational  →  Atomic / data warehouse  →  Departmental (data mart)  →  Individual
```

| Mức | Đặc điểm | Câu hỏi nó trả lời (ví dụ khách hàng J. Jones) |
|---|---|---|
| Operational | Chi tiết, hiện tại, cập nhật được, phục vụ một ứng dụng | Điểm tín dụng của J. Jones **bây giờ** là gì? |
| Atomic DW | Chi tiết nhất, có thời gian, tích hợp, theo chủ đề | **Lịch sử** điểm tín dụng của J. Jones? |
| Departmental | Một phần, đã tổng hợp theo phòng ban | Ta có đang thu hút thêm khách theo thời gian không? |
| Individual | Tạm thời, ad hoc, cho một người | Xu hướng của đúng nhóm khách mà tôi đang phân tích? |

**Bảng Primitive data vs Derived data (slide 25, hay thi):**

| Dữ liệu vận hành (primitive) | Dữ liệu phân tích (derived / DSS) |
|---|---|
| Application-oriented | Subject-oriented |
| Chi tiết | Tổng hợp hoặc tinh chế |
| Chính xác tại thời điểm truy cập | Đại diện giá trị theo thời gian, snapshot |
| Phục vụ nhân viên | Phục vụ nhà quản lý |
| Cập nhật được | Không cập nhật (nạp thêm) |
| Chạy lặp đi lặp lại | Chạy theo kiểu heuristic, không biết trước |
| Yêu cầu xử lý hiểu trước | Yêu cầu xử lý không hiểu trước |
| Tối ưu hiệu năng, giao dịch | Tối ưu phân tích |
| Truy cập từng đơn vị một | Truy cập một tập một lúc |
| Hướng giao dịch | Hướng phân tích |
| Kiểm soát cập nhật là vấn đề lớn | Kiểm soát cập nhật gần như không có |
| Sẵn sàng cao | Sẵn sàng thoải mái hơn |
| Quản lý toàn vẹn | Quản lý theo từng tập con |
| Không dư thừa | Dư thừa là một thực tế |
| Cấu trúc tĩnh | Cấu trúc linh hoạt |
| Lượng dữ liệu dùng trong một quy trình nhỏ | Lượng dữ liệu dùng trong một quy trình lớn |
| Hỗ trợ vận hành hằng ngày | Hỗ trợ nhu cầu quản lý |
| Xác suất truy cập cao | Xác suất truy cập thấp |

**Khi nào nên làm DW (slide 27, thảo luận):** khi có nhiều hệ thống nguồn, các phòng ban ra số liệu mâu thuẫn, cần lịch sử dài, báo cáo thủ công tốn quá nhiều công, và câu hỏi phân tích không trả lời được trên OLTP.

**DW dùng để làm gì (slide 28):** data analytics, data mining, machine learning, BI, truy vấn ad hoc.

**Sản phẩm (slide 29):** Google BigQuery, Snowflake, Amazon Redshift, Teradata, Oracle, Cloudera, Panoply, Informatica, IBM InfoSphere...

## Decision Support System (slide 30–50)

**Ra quyết định (slide 38):** "suy nghĩ qua các phương án và chọn một". Ví dụ theo đuổi một công nghệ hoặc dòng sản phẩm mới. Quyết định tốt dựa trên thông tin; quyết định kém thường do thiếu hoặc sai thông tin.

**3 mức quyết định (slide 39–40):**

| Mức | Cấu trúc | Hệ thống hỗ trợ | Ai quyết | Ví dụ |
|---|---|---|---|---|
| **Strategic** | Unstructured (không cấu trúc) | Executive Information Systems | Ban giám đốc, CEO, HĐQT | Có sáp nhập không? Có ra dòng sản phẩm mới không? Có thu nhỏ tổ chức không? |
| **Managerial / Tactical** | Semi-structured | Decision Support Systems | Quản lý | Làm sao để nhân viên hai công ty hợp tác sau sáp nhập? Ai sẽ ra đi khi thu nhỏ? |
| **Operational** | Structured | Transaction Processing Systems | Nhân viên | Bao lâu liên lạc với đồng nghiệp mới một lần? Cân bằng khối lượng công việc thế nào? |

Kim tự tháp: đáy là operational (nhiều, có cấu trúc), đỉnh là strategic (ít, không cấu trúc).

**Quy trình ra quyết định 6 bước (slide 43):**
1. Nhận ra cần phải quyết định.
2. Sinh nhiều phương án (brainstorm).
3. Phân tích các phương án (nghiên cứu, dựa trên bằng chứng).
4. Chọn phương án tốt nhất.
5. Thực hiện.
6. Đánh giá hiệu quả.

**Vì sao cần DSS (slide 44–46):** khó theo dõi mọi hoạt động, cạnh tranh mạnh, đội IT bận, thông tin phải kịp thời và chính xác, cần giảm chi phí, xu hướng cá nhân hóa. DSS giúp tính toán nhanh hơn, tăng năng suất, nâng chất lượng quyết định, tăng năng lực cạnh tranh, vượt giới hạn bộ nhớ và xử lý của con người.

**Định nghĩa (slide 47):** DSS nhận đầu vào của một quy trình ra quyết định đã biết (hoặc biết một phần) và cung cấp thông tin cần thiết để quyết định.

**4 kỹ thuật phân tích của DSS (slide 48, học thuộc):**

| Kỹ thuật | Làm gì | Ví dụ |
|---|---|---|
| **What-if** | Đổi một giả định, xem tác động lên mô hình | Nếu giá tăng 10% thì lợi nhuận còn bao nhiêu? |
| **Sensitivity** | Đổi **lặp đi lặp lại** một biến để xem độ nhạy | Tăng giá từng 1% từ 0 đến 20% |
| **Goal seek** | Biết mục tiêu, tìm đầu vào cần thiết | Muốn lợi nhuận 1 tỷ thì phải bán bao nhiêu? |
| **Optimization** | Tìm giá trị tối ưu của biến mục tiêu bằng cách đổi các biến khác | Kết hợp giá và chi phí quảng cáo để lợi nhuận cực đại |

**4 thành phần DSS (slide 49):**
- **Data management:** quản lý dữ liệu (thường lấy từ DW).
- **Model management:** quản lý các mô hình (what-if, tối ưu...).
- **Knowledge base management:** tri thức, luật, kinh nghiệm.
- **User interface:** giao diện cho người quyết định.

**Có dùng DSS mà không có DW không? (slide 50):** Có, DSS có thể đọc trực tiếp OLTP hoặc file. Nhưng khi nhiều nguồn, cần lịch sử và số liệu nhất quán thì DW là nền tảng dữ liệu tốt nhất cho DSS. DW cung cấp dữ liệu, DSS cung cấp mô hình ra quyết định. Hai thứ bổ sung cho nhau.

**Use case trong slide:** Google Maps (chọn đường), Slack (phối hợp), dashboard bất động sản Power BI, điểm tín nhiệm MoMo, DXplain (hỗ trợ chẩn đoán y khoa), bảng tính lãi suất vay.

### Câu hỏi thi từ Lecture 1
1. Nêu 3 nhóm vấn đề của hệ thống vận hành (credibility, productivity, information) và nguyên nhân.
2. 4 đặc tính của DW. Phân biệt primitive data và derived data (ít nhất 6 tiêu chí).
3. Kiến trúc DW 3 tầng. 4 mức của architected environment và loại câu hỏi mỗi mức trả lời.
4. 3 mức quyết định, ai ra quyết định, hệ thống nào hỗ trợ.
5. 6 bước ra quyết định. 4 kỹ thuật phân tích của DSS. 4 thành phần của DSS.

---

# LECTURE 2: DATASET INVESTIGATION (44 slide)

## OLTP vs OLAP (slide 4–15)

Nhà hàng của Alice có hai nhóm hệ thống (slide 5):
- **Vận hành:** POS, giao hàng, đặt bàn, phản hồi, bếp, nhân sự, tài chính, SCM, CRM, marketing, ERP.
- **Phân tích:** data warehouse, BI, đảm bảo chất lượng, phát triển cửa hàng mới, phân tích thị trường, data mining.

**OLTP (slide 6):** nhiều giao dịch, thao tác SELECT/INSERT/UPDATE/DELETE (thêm món vào giỏ, sửa giá, hiện thông tin sản phẩm), truy vấn nhanh và đơn giản, toàn vẹn dữ liệu khi nhiều người truy cập, dữ liệu hiện tại và chi tiết, CSDL chuẩn hóa cao.

**OLAP (slide 7):** ít giao dịch, chủ yếu SELECT (tổng doanh thu mỗi khu vực mỗi tháng, sản phẩm "siêu anh hùng", khách VIP), truy vấn phức tạp có tổng hợp, quan tâm tích hợp và chất lượng, dữ liệu lịch sử và đã tổng hợp, chuẩn hóa thấp.

**Bảng so sánh tổng quát (slide 8, Han, học thuộc):**

| Đặc điểm | OLTP | OLAP |
|---|---|---|
| Đặc trưng | Xử lý vận hành | Xử lý thông tin |
| Hướng tới | Giao dịch | Phân tích |
| Người dùng | Nhân viên, DBA | Nhà quản lý, nhà phân tích |
| Chức năng | Vận hành hằng ngày | Hỗ trợ quyết định dài hạn |
| Thiết kế | ER, theo ứng dụng | Star/snowflake, theo chủ đề |
| Dữ liệu | Hiện tại, đảm bảo cập nhật | Lịch sử, duy trì theo thời gian |
| Tóm tắt | Rất chi tiết | Tổng hợp, cô đọng |
| Khung nhìn | Chi tiết, quan hệ phẳng | Tóm tắt, đa chiều |
| Đơn vị công việc | Giao dịch ngắn, đơn giản | Truy vấn phức tạp |
| Truy cập | Đọc và ghi | Chủ yếu đọc |
| Số bản ghi mỗi lần | Vài chục | Hàng triệu |
| Số người dùng | Hàng nghìn | Hàng trăm |
| Kích thước | MB đến GB | ≥ TB |
| Ưu tiên | Hiệu năng cao, sẵn sàng cao | Linh hoạt cao, người dùng tự chủ |
| Đo lường | Thông lượng giao dịch | Thông lượng truy vấn, thời gian phản hồi |

**Bảng bổ sung của giảng viên (slide 9):**

| | OLTP | OLAP |
|---|---|---|
| Thời gian phản hồi | Quan trọng, thường < 1 giây | Có thể lâu (giờ đến ngày) |
| Mô hình | Phức tạp, chuẩn hóa, SQL hoặc NoSQL | Đơn giản hóa, phi chuẩn hóa |
| Dữ liệu | Luôn mới và nhất quán, dữ liệu hiện tại | Chất lượng cao, đã tích hợp, hiện tại và lịch sử |
| Quy trình | Một quy trình (đặt món) | Nhiều quy trình tích hợp (doanh thu ròng) |
| Nguồn | Một | Nhiều |

**Slide 11 minh họa Transactions vs Analytics bằng dữ liệu thật:** bảng sinh viên (họ tên, ngày sinh, email, lớp) và bảng đăng ký lớp là dữ liệu giao dịch, từng dòng một. Đoạn JSON lồng nhau (phòng ban, sở thích) cho thấy dữ liệu vận hành có thể ở nhiều dạng. Phân tích thì hỏi những câu xuyên qua nhiều dòng: lớp nào đông, độ tuổi trung bình...

**Normalization vs denormalization (slide 12–13):**
Bảng bán hàng gộp `AgencyID, AgencyName, ProductID, ProductName, ProductPrice, Quantity, Date` là **phi chuẩn hóa**: tên đại lý và tên sản phẩm lặp lại mỗi dòng. Dễ đọc, dễ cộng doanh thu, nhưng sửa tên đại lý phải sửa nhiều dòng.
Hai bảng `Student(StudentID, StudentName)` và `Enrollment(StudentID, Course)` là **chuẩn hóa**: mỗi sự kiện một chỗ, không lặp tên sinh viên.
OLTP chọn chuẩn hóa để ghi đúng. OLAP chọn phi chuẩn hóa để đọc nhanh và người dùng nghiệp vụ hiểu được.

**Mẫu trong môn:** sơ đồ OLTP AdventureWorks (rất nhiều bảng, nhiều quan hệ) và sơ đồ DW AdventureWorks (vài dimension bao quanh vài fact). Cùng một nghiệp vụ, hai cách mô hình hóa.

## Dataset investigation: phương pháp câu hỏi WH (slide 16–41)

Giảng viên dùng CSDL **CompanyX = AdventureWorks** (nhà sản xuất xe đạp của Microsoft) và dạy cách tự khám phá dữ liệu lạ bằng 4 câu hỏi.

### WHAT: dữ liệu này là gì, về cái gì, nghĩa là gì (slide 19–25)
Mục tiêu: hiểu đối tượng dữ liệu (bảng, cột, giá trị) và hiểu nghiệp vụ.

Cách làm:
1. Có schema thì đọc schema. CompanyX có **71 bảng** trong các schema `HumanResources, Person, Production, Purchasing, Sales`, cùng 20 view, 10 procedure, 11 function.
2. Nhìn dữ liệu mẫu. Bảng `ProductCategory` có 4 dòng: **Bikes, Components, Clothing, Accessories**, cột `rowguid` (định danh toàn cục) và `ModifiedDate`.
3. Đối chiếu với tri thức đời thực (các bộ phận của một chiếc xe đạp: khung, yên, phanh, líp...).

**Giả thuyết (slide 24):** CompanyX là nhà sản xuất xe đạp, với các mảng Manufacturing, Sales, Purchasing, Product Management, Contact Management, Human Resources.

**Thực hành "thị trường bán hàng ở đâu":** đọc `Sales.SalesTerritory` (các nước, khu vực như Canada, Australia, Southwest...).

### WHEN: dữ liệu của giai đoạn nào (slide 26–28)
Tìm các cột Date/Timestamp rồi lấy MIN và MAX.

```sql
-- Hệ thống bắt đầu vận hành từ khi nào
SELECT MIN(HireDate) AS MIN_HIRE_DATE
FROM CompanyX.HumanResources.Employee;          -- kết quả: 2006-06-30

-- Bán hàng trong khoảng nào
SELECT MIN(OrderDate) AS MIN_DATE, MAX(OrderDate) AS MAX_DATE
FROM CompanyX.Sales.SalesOrderHeader;           -- 2011-05-31 đến 2014-06-30
```

**Thực hành "ngày bắt đầu bán sản phẩm đầu tiên":** `SELECT MIN(SellStartDate) FROM Production.Product`.

### HOW: các dữ liệu liên hệ với nhau thế nào, nghiệp vụ vận hành ra sao (slide 29–36)
Dùng **table dependency** (Object Dependencies trong SSMS): bảng nào phụ thuộc vào bảng này, bảng này phụ thuộc vào bảng nào.
Ví dụ `Production.ProductCategory` không phụ thuộc bảng nào, nhưng `ProductSubcategory` phụ thuộc vào nó. Suy ra hierarchy: Category → Subcategory → Product.

**Khách hàng của CompanyX là ai? (slide 32–35)** Đi theo khóa ngoại:
- `Sales.Customer` có `PersonID` và `StoreID`.
- `PersonID` trỏ sang `Person.Person` (cá nhân).
- `StoreID` trỏ sang `Sales.Store` (cửa hàng đại lý).
- Vậy khách hàng là **một người hoặc một cửa hàng**, không phải lúc nào cũng là cá nhân. Nhân viên bán (`SalesPerson`) lại là một nhân viên trong `HumanResources.Employee`, nối tiếp sang `Person.Person`.
- Câu hỏi trên slide "PersonID is null? StoreID is null" nhằm phát hiện quy tắc: mỗi khách phải là person **hoặc** store. Dòng mà cả hai đều NULL (hoặc cả hai đều có giá trị) là điểm cần kiểm tra chất lượng dữ liệu.
- **Nhân khẩu học khách hàng:** cột `Demographics` (XML) trong `Person.Person`, hoặc ghép `Sales.Customer` → `Person.Person`.

### WHY: vì sao có giá trị này, giả thuyết của mình có đúng không (slide 37–40)
Nhìn `Sales.SalesOrderHeader` và hỏi vì sao một số cột có giá trị như vậy:
- `TotalDue` luôn bằng `SubTotal + TaxAmt + Freight` (cột tính sẵn).
- `SalesOrderNumber` có dạng `SO` + `SalesOrderID` (suy ra từ mã, không nhập tay).
- `PurchaseOrderNumber`, `CreditCardApprovalCode` NULL ở một số đơn: đơn online hoặc đơn không trả bằng thẻ thì các cột này trống. NULL ở đây **có nghĩa nghiệp vụ**, không phải dữ liệu lỗi.
- `Status` là mã trạng thái đơn (số, không phải chữ).
- `OnlineOrderFlag` phân biệt đơn web và đơn qua nhân viên bán.

**Thực hành:** vì sao `OrderHeader` có cả `CustomerID` lẫn `SalesPersonID`? Vì một đơn vừa thuộc về khách mua, vừa thuộc về nhân viên phụ trách (đơn qua kênh nhân viên bán; đơn online có thể không có salesperson). Muốn tên, giá, category của sản phẩm thì đi `SalesOrderDetail` → `Product` → `ProductSubcategory` → `ProductCategory`, giá lấy từ `OrderDetail.UnitPrice` (giá tại thời điểm bán, không lấy giá hiện tại trong `Product`).

**Slide 41 "weird data values":** trò chơi tìm dữ liệu lạ. Những chỗ thường gặp trong AdventureWorks: ngày trong tương lai hoặc rất cũ, số lượng 0, giá 0, mã trạng thái không có trong bảng tra, bản ghi mồ côi, chuỗi toàn dấu cách.

### Câu hỏi thi từ Lecture 2
1. So sánh OLTP và OLAP theo ít nhất 8 tiêu chí.
2. Vì sao OLTP chuẩn hóa còn OLAP phi chuẩn hóa? Minh họa bằng bảng Agency/Product.
3. Trình bày cách khảo sát một CSDL lạ theo What, When, How, Why, kèm một câu SQL minh họa cho When.
4. Cho schema Customer có PersonID và StoreID, hãy suy ra quy tắc nghiệp vụ và nêu vấn đề chất lượng dữ liệu có thể có.

---

# LECTURE 3: DATA WAREHOUSE DESIGN (63 slide)

## Vì sao phải mô hình hóa (slide 3–13)

Đưa dữ liệu từ môi trường vận hành vào DW là bài toán **tích hợp** (slide 5): cùng dữ liệu khác tên, khác dữ liệu cùng tên, mã hóa không nhất quán (`0/1`, `m/f`, `male/female`, `cm/inch`, `bal/balance/curbal`).

Mô hình tốt cho (slide 6): cái nhìn thống nhất, nhất quán, đáng tin, trừu tượng hóa gọn, và hiệu năng vì việc gom dữ liệu đã làm từ trước. Ví dụ nghiệp vụ được "đóng" thành thuộc tính: `date + interval`, `is_delete = '0'`, thời gian giao hàng tính tới điểm trả đầu tiên.

**ER modeling (vận hành) và Dimensional modeling (phân tích):**
- ER tối ưu cho ghi, nhiều bảng, nhiều quan hệ, người dùng nghiệp vụ khó tự viết câu hỏi.
- Dimensional tối ưu cho đọc. Người dùng hiểu được ("bán cái gì, ở đâu, khi nào") và truy vấn nhanh.
- Trên mô hình chiều mới làm được **drill-down / roll-up** (đi lên xuống hierarchy Region → Plant → Amount, hoặc năm → quý → tháng) và **slice & dice** (cắt một lát hoặc một khối con của cube).

**Cube (slide 10):** một hình hộp với các cạnh là dimension (Location, Time, Product) và ô bên trong là measurement (doanh số). Hierarchy nằm trên mỗi cạnh: East/Central/West → thành phố; 2001/2011; CellPhone/Pager.

## Mô hình đa chiều (slide 14–21)

- Cài dimensional model trên **RDBMS** thì gọi là **star schema**.
- Cài trên **CSDL đa chiều** thì gọi là **OLAP cube**.

Slide 16–18 xây trực giác:
- **2 chiều:** bảng Item × Store Location, ô là Quantity. Paper A4 bán 40 ở Chennai và 89 ở Delhi.
- **3 chiều:** thêm thời gian, thành một khối.
- **4 chiều:** thêm một chiều nữa (ví dụ khách hàng). Con người không vẽ nổi khối 4 chiều, nhưng máy lưu trữ bình thường. Mỗi chiều thêm làm số ô tăng theo tích.

**Star, Snowflake và biến thể (slide 20–21):**
- **Star:** một fact ở giữa (Sales Facts), các dimension denormalized quanh nó: Time, Customer, Seller, Manufacturing Location, Product. Mỗi dimension chỉ một bảng.
- **Snowflake:** dimension được chuẩn hóa tiếp. Time tách thành Date, Week, Month. Location tách thành Plant và Region. Seller tách thành SalesPerson, Outlet, rồi Outlet tách Region và Type. Product tách Model.
- **Constellation / multistar:** nhiều fact dùng chung dimension (đã học ở Lecture 4 và tài liệu ôn).

## Fact và Dimension (slide 22–25)

**Fact table** lưu các phép đo hiệu suất của một sự kiện nghiệp vụ.
- Khóa chính là **khóa tổ hợp** của các khóa ngoại tới dimension, có ràng buộc tham chiếu.
- Fact hữu ích nhất là **số và cộng được**. Ba loại: additive (cộng mọi chiều), semi-additive (không cộng theo thời gian, ví dụ số dư), non-additive (tỷ lệ, đơn giá).

**Dimension table** chứa ngữ cảnh dạng chữ của sự kiện đo: ai, cái gì, ở đâu, khi nào. Dimension thường mang **hierarchy** (ngày → tháng → quý → năm, cửa hàng → quận → vùng).

**Một báo cáo suy ra trực tiếp từ mô hình (slide 24–25), ví dụ Kimball rất đáng học:**
Báo cáo "Sales Activity for June 2013": mỗi dòng là một District, mỗi cột là một Brand, ô là tổng Sales Dollars.

| District | PowerClean | Zippy | Clean Fast |
|---|---|---|---|
| Atherton | 2.035 | 707 | 2.320 |
| Belmont | | | 527 |

Câu SQL tương ứng, thấy rõ mô hình chiều biến báo cáo thành một câu rất đều:

```sql
SELECT store.district_name, product.brand,
       SUM(sales_facts.sales_dollars) AS "Sales Dollars"
FROM store, product, date, sales_facts
WHERE date.month_name = 'June' AND date.year = 2013
  AND store.store_key = sales_facts.store_key
  AND product.product_key = sales_facts.product_key
  AND date.date_key = sales_facts.date_key
GROUP BY store.district_name, product.brand;
```

`WHERE` là **filter** (cắt lát thời gian), `GROUP BY` là mức tổng hợp trên dimension, `SUM` là measure.

## Quy trình thiết kế 4 bước của Kimball (slide 26)

1. **Select the business process.** Chọn quy trình nghiệp vụ, thường tương ứng một fact.
2. **Declare the grain.** Khai báo một dòng fact là gì. Bước quyết định nhất.
3. **Identify the dimensions.**
4. **Identify the facts.**

Làm cùng người dùng nghiệp vụ (collaborative dimensional modeling), không làm một mình trong phòng kỹ thuật.

## Bus matrix (slide 27)

Ma trận kế hoạch của Kimball: **hàng dọc là data mart / business process** (Customer Billing, Service Orders, Trouble Reports...), **hàng ngang là dimension** (Date, Customer, Service, Employee, Location...). Ô có chữ X nghĩa là fact đó dùng dimension đó. Các cột được dùng ở nhiều hàng chính là **conformed dimension**, sợi dây tích hợp các data mart.

## Case study Retail Sales (slide 28–32, học theo đúng 4 bước)

1. **Business process:** khách mua hàng tại POS. Câu hỏi: sản phẩm nào bán ở cửa hàng nào, ngày nào, trong điều kiện khuyến mãi nào, thuộc giao dịch nào.
2. **Grain:** doanh số của **một sản phẩm trong một giỏ hàng, trên một dòng** (một line item).
3. **Dimensions:** Date, Product, Store, Promotion, Cashier, Payment Method. `POS Transaction #` là **degenerate dimension** (nằm trong fact, không có bảng dimension).
4. **Facts:** Sales Quantity, Regular Unit Price, Discount Unit Price, Net Unit Price, Extended Discount Dollar Amount, Extended Sales Dollar Amount, Extended Cost Dollar Amount, Extended Gross Profit Dollar Amount.

Chi tiết dimension (slide 32) cho thấy hierarchy được **làm phẳng trong một bảng**:
- Date: Date, Day of Week, Calendar Month, Calendar Quarter, Calendar Year.
- Store: Store Name, Store Number, Store District, Store Region.
- Product: SKU Number, Product Description, Brand, Category, Department.
- Promotion: Promotion Code, Promotion Name, Promotion Media Type, Promotion Begin/End Date, Price Reduction Type.
- Cashier: Cashier Employee ID, Cashier Name.
- Payment Method: Description, Payment Method Group.

Đơn giá (Regular Unit Price) là **non-additive**: không được SUM đơn giá theo sản phẩm. Các cột "Extended ... Dollar Amount" mới là additive vì đã nhân với số lượng.

## Slowly Changing Dimensions (slide 35–54)

**Định nghĩa:** dimension biết xử lý thuộc tính thay đổi theo thời gian, tức là giữ hoặc không giữ lịch sử. Ví dụ khách đổi địa chỉ, đổi số điện thoại.

Dữ liệu minh họa xuyên suốt: bảng cũ có khách 03 với số điện thoại 456, dữ liệu mới đến thì khách 03 đổi thành 999 và có thêm khách 04, 05.

| Type | Hành động trên dimension | Ảnh hưởng khi phân tích fact | Lịch sử |
|---|---|---|---|
| **0** | Không đổi gì. Dùng cho thuộc tính bất biến: họ tên khai sinh, ngày sinh, ngày trong DimDate | Fact luôn gắn với giá trị gốc | Không |
| **1** | Ghi đè. Khách 03 từ 456 thành 999 | Mọi fact, kể cả fact cũ, gắn với giá trị **hiện tại** | Không |
| **2** | Thêm dòng mới. Dòng cũ: `is_active = 0`, `valid_to = 2021-09-01`. Dòng mới: surrogate key mới, `is_active = 1`, `valid_from = 2021-09-01`, `valid_to = 9999-12-31` | Fact gắn với giá trị **đang có hiệu lực tại thời điểm fact xảy ra** | Đầy đủ |
| **3** | Thêm cột. `previous_phone_number = 456`, `current_phone_number = 999` | Fact gắn với **cả giá trị hiện tại lẫn giá trị liền trước** | Có, nhưng chỉ một thế hệ |
| **4** | Tách thuộc tính **hay đổi** (age band, tần suất mua, mức thu nhập) thành **mini-dimension**. Fact lưu thêm `Demographics Key`. Mini-dimension không lưu lịch sử của từng khách; lịch sử nằm ở việc fact trỏ tới phiên bản nhân khẩu nào tại thời điểm đó | Fact gắn với thuộc tính thay đổi nhanh **đúng lúc fact xảy ra** | Có, qua fact |
| **5** | Type 4 cộng Type 1: trong bảng Customer gốc có thêm khóa ngoại "current demographics" bị ghi đè. Muốn biết hồ sơ hiện tại của khách thì nhìn ngay dimension, không cần đi qua fact | Vừa có giá trị lúc fact xảy ra, vừa có giá trị hiện tại | Có |
| **6** | Type 1 + 2 + 3. Ví dụ sản phẩm IntelliKidz: mỗi lần chuyển phòng ban thì **thêm dòng** (Type 2: Education → Strategy → Critical Thinking, có effective/expiration date), đồng thời cột `Current Department Name` được **ghi đè trên mọi dòng cũ** thành giá trị mới nhất | Fact gắn với giá trị tại thời điểm fact, và vẫn lấy được giá trị hiện tại trên chính dòng lịch sử | Đầy đủ, kèm giá trị hiện tại |
| **7** | Hai dimension song song: một dimension Type 2 (khóa thay đổi theo phiên bản) và một dimension/view Type 1 chỉ chứa dòng hiện tại, nối với nhau bằng **durable key** (khóa bền, ví dụ 12345 cho mọi phiên bản của IntelliKidz). Cùng khả năng với Type 6 nhưng ETL nhẹ hơn, và truy vấn theo giá trị hiện tại chỉ lọc trên bảng nhỏ | Giống Type 6 | Đầy đủ |

Cột bắt buộc của Type 2: **surrogate key**, `valid_from`, `valid_to`, `is_active`.

Đọc ví dụ Type 6 trên slide 51 cho chắc:
- Ban đầu một dòng, Product Key 12345, phòng Education, hiệu lực từ 2012-01-01, Current.
- Lần chuyển thứ nhất (2013-01-01): dòng 12345 bị đóng lại ngày 2012-12-31, thêm dòng 25984 phòng Strategy. Cả hai dòng đều ghi `Current Department Name = Strategy`.
- Lần chuyển thứ hai (2013-02-04): dòng 25984 đóng lại ngày 2013-02-03, thêm dòng 31726 phòng Critical Thinking, và **cả ba dòng** đều có `Current Department Name = Critical Thinking`.

Vì sao có Type 4 trở lên: thuộc tính đổi quá thường xuyên (tuổi, điểm hành vi) nếu dùng Type 2 sẽ làm nổ số dòng dimension. Tách chúng ra mini-dimension giữ cho dimension chính nhỏ và duyệt nhanh.

## Công cụ và bài lab (slide 55–60)

**ETL tools:** SSIS, Talend, Xplenty, Informatica, IBM InfoSphere.

**3 kiểu OLAP:**
- **ROLAP:** lưu trên CSDL quan hệ (star schema), truy vấn SQL, chịu được dữ liệu lớn.
- **MOLAP:** lưu bằng mảng đa chiều, tính sẵn tổng hợp, truy vấn rất nhanh, tốn chỗ.
- **HOLAP:** lai, chi tiết ở quan hệ, tổng hợp ở đa chiều.

**Lab Internet Sales:** mở cube AdventureWorks trong Excel/SSAS, kéo `Sales Territory Region`, `English Product Name`, `English Product Subcategory` ra hàng và `Sales Amount` ra giá trị. Kết quả là báo cáo doanh số theo khu vực và sản phẩm mà không viết SQL.

Slide 60 cho câu SQL tương đương trên **OLTP** để thấy cái giá của việc không có DW: 5 JOIN (`SalesOrderDetail`, `Product`, `SalesOrderHeader`, `SalesTerritory`, `ProductSubcategory`) mới ra được một phép tổng. Trên star schema chỉ cần JOIN fact với vài dimension.

```sql
SELECT st.Name, p.Name, ps.Name, SUM(od.LineTotal)
FROM Sales.SalesOrderDetail od
JOIN Production.Product p ON od.ProductID = p.ProductID
JOIN Sales.SalesOrderHeader oh ON od.SalesOrderID = oh.SalesOrderID
JOIN Sales.SalesTerritory st ON oh.TerritoryID = st.TerritoryID
JOIN Production.ProductSubcategory ps ON p.ProductSubcategoryID = ps.ProductSubcategoryID
GROUP BY st.Name, p.Name, ps.Name
ORDER BY st.Name, p.Name, ps.Name;
```

### Câu hỏi thi từ Lecture 3
1. Phân biệt ER modeling và dimensional modeling. Star khác Snowflake chỗ nào.
2. Thực hiện 4 bước Kimball cho một nghiệp vụ cho trước (nhà hàng, khách sạn, bán lẻ).
3. Phân loại fact additive, semi-additive, non-additive trên sơ đồ Retail Sales. Chỉ ra degenerate dimension.
4. Cho tình huống khách đổi số điện thoại, vẽ bảng sau khi áp dụng SCD Type 1, 2 và 3.
5. Type 6 khác Type 2 ở điểm nào? Vì sao cần Type 4?
6. Viết câu SQL của một báo cáo từ star schema cho trước (dạng slide 25).

---

# LECTURE 5: DATA MINING (91 slide)

## Knowledge discovery và chức năng của data mining (slide 3–18)

**KDD (slide 4, hình Han):**
```
Databases, Flat files
  → Cleaning and integration → Data Warehouse
  → Selection and transformation
  → Data mining → Patterns
  → Evaluation and presentation → Knowledge
```
Data mining chỉ là **một bước** của cả quy trình khám phá tri thức.

**Định nghĩa:** khám phá các mẫu và tri thức thú vị từ lượng dữ liệu lớn.

**Hai nhóm tác vụ:**
- **Descriptive:** mô tả tính chất của tập dữ liệu.
- **Predictive:** suy diễn từ dữ liệu hiện có để dự đoán.

**Dữ liệu có thể khai phá:** CSDL, DW, dữ liệu giao dịch, luồng (stream), chuỗi (sequence), đồ thị, không gian, văn bản, đa phương tiện.

**Các dạng mẫu (slide 8–16):**

| Mẫu | Ý nghĩa | Ví dụ trong slide |
|---|---|---|
| **Characterization** | Tóm tắt lớp đang xét | Khách chi trên 5.000 USD/năm: 40–50 tuổi, có việc, tín dụng tốt. Có thể drill-down theo nghề |
| **Discrimination** | So sánh lớp mục tiêu với lớp đối chứng | 80% khách mua máy tính thường xuyên ở tuổi 20–40 và có đại học; 60% khách mua hiếm là người già hoặc rất trẻ, không có đại học |
| **Frequent patterns** | Mẫu xuất hiện thường xuyên: itemset, subsequence (chuỗi con), substructure (đồ thị, cây) | |
| **Association** | Luật rút từ frequent pattern | `buys(X, computer) → buys(X, software)`, support 1%, confidence 50%. `age 20..29` và `income 40K..49K` → mua laptop, support 2%, confidence 60% |
| **Classification** | Tìm mô hình gán **nhãn rời rạc** | Hồ sơ vay an toàn hay rủi ro; khách có mua máy tính không |
| **Regression** | Dự đoán **giá trị liên tục** | Doanh thu, giá nhà. Khác classification ở chỗ đầu ra là số thực chứ không phải nhãn |
| **Clustering** | Gom nhóm sao cho trong nhóm giống nhau, khác nhóm thì khác. **Không dùng nhãn** | Phân nhóm sở thích theo vị trí địa lý |
| **Outlier** | Đối tượng không tuân theo hành vi chung | Gian lận thẻ tín dụng |

**Mọi mẫu có thú vị không? (slide 17):** Không. Một mẫu đáng quan tâm khi nó dễ hiểu, đúng trên dữ liệu mới, hữu ích, và **mới** (chưa biết trước). Mẫu đúng hiển nhiên ("phụ nữ có thai là nữ") vô dụng.

**Data mining và machine learning (slide 18, thảo luận):** phần giao nhau lớn. Data mining nhấn mạnh cả quy trình từ dữ liệu thật, bẩn, lớn đến tri thức dùng được trong nghiệp vụ. Machine learning nhấn mạnh thuật toán học. Trong môn này classification, clustering, regression xuất hiện ở cả hai tên.

## Thống kê mô tả (slide 19–47): có bài tính

Cùng một bộ dữ liệu lương (nghìn USD), đã sắp tăng dần, dùng cho mọi câu hỏi trên slide:

```
30, 36, 47, 50, 52, 52, 56, 60, 63, 70, 70, 110     (n = 12, tổng = 696)
```

| Đại lượng | Cách tính | Kết quả |
|---|---|---|
| **Mean** | 696 / 12 | **58** (tức 58.000 USD) |
| **Weighted mean** | Σ(wᵢxᵢ) / Σwᵢ | Trọng số do đề cho. Mean nhạy với outlier: riêng giá trị 110 đã kéo mean lên |
| **Median** | Trung bình hai giá trị giữa (thứ 6 và thứ 7): (52 + 56) / 2 | **54** |
| **Mode** | Giá trị xuất hiện nhiều nhất. 52 và 70 đều xuất hiện 2 lần | **52 và 70** (phân phối hai mode) |
| **Midrange** | (max + min) / 2 = (110 + 30) / 2 | **70** |
| **Range** | max − min = 110 − 30 | **80** |
| **Q1** | Trung vị nửa dưới (30, 36, 47, 50, 52, 52) = (47 + 50) / 2 | **48,5** |
| **Q2** | Chính là median | **54** |
| **Q3** | Trung vị nửa trên (56, 60, 63, 70, 70, 110) = (63 + 70) / 2 | **66,5** |
| **IQR** | Q3 − Q1 | **18** |
| **Five-number summary** | Min, Q1, Median, Q3, Max | **30, 48,5, 54, 66,5, 110** |
| **Variance** | (1/n) Σ(xᵢ − mean)² = 4550 / 12 | **379,17** |
| **Standard deviation** | căn bậc hai của variance | **19,47** |

Tổng bình phương độ lệch bằng 4550 (tự tính lại được: các độ lệch so với 58 là −28, −22, −11, −8, −6, −6, −2, 2, 5, 12, 12, 52). Nếu đề yêu cầu mẫu (chia cho n − 1) thì variance = 4550 / 11 = 413,64 và độ lệch chuẩn = 20,34. Công thức mean trên slide chia cho N, nên variance tương ứng cũng chia cho N.

**Vì sao cần nhiều đại lượng:** mean 58 bị 110 kéo lên, median 54 trung thực hơn với phần đông. Midrange 70 còn lệch hơn mean. IQR 18 mô tả độ phân tán của **nửa giữa** và không bị outlier chi phối, trong khi range 80 bị 110 thống trị.

**Độ lệch (slide 27–31):**
- Lệch phải (đuôi dài bên phải, mean > median): thu nhập, giá nhà. Đa số giá trị nhỏ, vài giá trị rất lớn.
- Lệch trái (mean < median): điểm thi khi nhiều người điểm cao, tuổi tử vong.
- Đối xứng: mean ≈ median ≈ mode.
- Cách kéo phân phối lệch về gần đối xứng để áp dụng các phương pháp thống kê: lấy **căn bậc hai**, lấy **log**, hoặc **Box-Cox**.

**Boxplot (slide 37):** hộp kéo từ Q1 đến Q3, gạch ngang ở median, râu kéo tới giá trị xa nhất còn nằm trong 1,5 × IQR, chấm bên ngoài là outlier. Hình so sánh giá bán của 4 chi nhánh AllElectronics: chi nhánh 1 thấp và có outlier trên 200, chi nhánh 3 cao và phân tán rộng.

**Đồ thị (slide 40–47):**

| Đồ thị | Dùng khi |
|---|---|
| Bar chart | So sánh dữ liệu phân loại, chiều cao tỉ lệ với giá trị, đứng hoặc ngang |
| Pie chart | Tỉ trọng, độ dài cung tỉ lệ với số lượng |
| Line chart | Điểm nối bởi đoạn thẳng, thường là theo thời gian |
| Quantile plot | Nhìn phân phối một biến: mỗi điểm là một giá trị với phân vị của nó |
| Q-Q plot | Chồng phân vị của hai phân phối lên nhau để xem chúng khác nhau theo kiểu dịch chuyển, giãn, hay đuôi |
| Histogram | Phân phối một thuộc tính, chiều cao cột là tần suất |
| Scatter plot | Mỗi cặp giá trị là một điểm, để thấy quan hệ, mẫu, xu hướng giữa hai biến số |

## Tiền xử lý dữ liệu (slide 48–56)

Bốn việc: **cleaning, integration, reduction, transformation**.

**Vì sao:** dữ liệu thật thiếu chính xác, thiếu đủ, không nhất quán, không kịp thời, khó tin, khó diễn giải.

**Cleaning:**
- Dữ liệu thật thì không đầy đủ, nhiễu, mâu thuẫn.
- **Giá trị thiếu**, 6 cách xử lý: bỏ cả dòng; điền tay; điền hằng số chung ("Unknown"); điền mean hoặc median của thuộc tính; điền mean/median của **đúng lớp** của dòng đó; điền giá trị có khả năng nhất (suy từ các thuộc tính khác, ví dụ bằng hồi quy hoặc cây quyết định).
- **Dữ liệu nhiễu:** binning (chia giỏ rồi làm trơn), regression, phân tích outlier (ví dụ bằng clustering).

**Integration:**
- Nhận dạng thực thể: `cust_id` với `cust_no` có phải một thuộc tính; "chiết khấu hóa đơn" khác "chiết khấu dòng hàng".
- Phát hiện dư thừa bằng **chi-square** (hai thuộc tính phân loại), **hệ số tương quan** và **hiệp phương sai** (hai thuộc tính số).
- Dòng trùng.
- Xung đột giá trị: cùng một mã sản phẩm nhưng khác đơn vị tiền.

**Reduction (giảm mà vẫn giữ đặc trưng):**
- Giảm chiều: wavelet, **PCA**, chọn tập con thuộc tính.
- Giảm số lượng: hồi quy và mô hình log-linear, histogram, clustering, lấy mẫu, tổng hợp theo cube.
- Nén dữ liệu.

**Transformation:** làm trơn, tạo thuộc tính mới, tổng hợp, chuẩn hóa (min-max, z-score), rời rạc hóa, sinh hierarchy cho dữ liệu định danh (phố → quận → tỉnh).

## Classification (slide 57–69)

Hai bước:
1. **Học:** dựng mô hình từ dữ liệu huấn luyện có nhãn.
2. **Phân loại:** dùng mô hình gán nhãn cho dữ liệu mới.

Ví dụ slide 59: từ bảng hồ sơ vay (age, income, loan_decision) rút ra luật `IF age = youth THEN risky`, `IF income = high THEN safe`, rồi áp luật cho khách mới.

### Cây quyết định: ID3, C4.5, CART

Độ đo trên slide:

\[
Info(D) = - \sum p_i \log_2 p_i
\]

\[
Info_A(D) = \sum \frac{|D_j|}{|D|} \times Info(D_j), \qquad Gain(A) = Info(D) - Info_A(D)
\]

Chọn thuộc tính có **Gain lớn nhất** để tách.

**Bộ AllElectronics (14 dòng, 9 mua máy tính, 5 không).** Slide 63 tính sẵn:

- Info(D) = −(9/14)log₂(9/14) − (5/14)log₂(5/14) = **0,940 bit**.
- Info theo age = **0,694 bit**, nên Gain(age) = 0,940 − 0,694 = **0,246 bit**.
- Gain(income) = **0,029**, Gain(student) = **0,151**, Gain(credit_rating) = **0,048**.

Vậy nút gốc là **age**. Nhánh middle_aged toàn bộ là "yes" nên thành lá. Hai nhánh youth và senior tách tiếp bằng các thuộc tính còn lại.

### Naive Bayes (slide 64–66): bài tính có lời giải đầy đủ

Bộ phân loại thống kê, cho ra xác suất một dòng thuộc từng lớp. Giả định các thuộc tính độc lập khi đã biết lớp.

X = (age = youth, income = medium, student = yes, credit_rating = fair). Hỏi mua máy tính hay không.

Xác suất tiên nghiệm: P(yes) = 9/14 = 0,643; P(no) = 5/14 = 0,357.

Xác suất có điều kiện:

| | yes (9 dòng) | no (5 dòng) |
|---|---|---|
| age = youth | 2/9 = 0,222 | 3/5 = 0,600 |
| income = medium | 4/9 = 0,444 | 2/5 = 0,400 |
| student = yes | 6/9 = 0,667 | 1/5 = 0,200 |
| credit = fair | 6/9 = 0,667 | 2/5 = 0,400 |

\[
P(X \mid yes) = 0{,}222 \times 0{,}444 \times 0{,}667 \times 0{,}667 = 0{,}044
\]

\[
P(X \mid no) = 0{,}600 \times 0{,}400 \times 0{,}200 \times 0{,}400 = 0{,}019
\]

Nhân với tiên nghiệm: P(X|yes)P(yes) = 0,044 × 0,643 = **0,028**; P(X|no)P(no) = 0,019 × 0,357 = **0,007**.

0,028 > 0,007 nên dự đoán **buys_computer = yes**. Mẫu số P(X) giống nhau cho mọi lớp nên không cần tính.

### Đánh giá (slide 67–68)

- **TP:** dương và đoán đúng là dương. **TN:** âm và đoán đúng là âm.
- **FP:** âm nhưng đoán là dương. **FN:** dương nhưng đoán là âm.

| Đo | Công thức |
|---|---|
| Accuracy | (TP + TN) / (P + N) |
| Error rate | (FP + FN) / (P + N) |
| Sensitivity, recall | TP / P với P = TP + FN |
| Specificity | TN / N với N = FP + TN |
| Precision | TP / (TP + FP) |
| F₁ | 2 × precision × recall / (precision + recall) |
| Fβ | (1 + β²) × precision × recall / (β² × precision + recall) |

β > 1 nhấn recall, β < 1 nhấn precision.

Demo trên slide 69 là bài toán **churn** (khách rời bỏ) của một công ty: nhãn rời/ở lại, huấn luyện mô hình, đọc confusion matrix.

## Clustering (slide 70–80)

Gom đối tượng thành cụm: trong cụm giống nhau, giữa các cụm khác nhau. Ứng dụng: phân khúc khách hàng, nhận dạng chữ viết tay, gom kết quả tìm kiếm, phát hiện outlier.

**Partitioning:** K-means (trung bình), K-modes (dữ liệu phân loại, dùng mode), K-medoids (dùng một điểm thật làm tâm, chịu outlier tốt hơn K-means).

### K-means, làm đủ các vòng

S = {2, 3, 4, 10, 11, 12, 20, 25, 30}, k = 2, tâm khởi tạo m1 = 4, m2 = 12.

| Vòng | Cụm 1 | Cụm 2 | Tâm mới |
|---|---|---|---|
| Gán theo tâm 4 và 12 | {2, 3, 4} | {10, 11, 12, 20, 25, 30} | 3 và 18 |
| Gán theo tâm 3 và 18 | {2, 3, 4, 10} | {11, 12, 20, 25, 30} | 4,75 và 19,6 |
| Gán theo tâm 4,75 và 19,6 | {2, 3, 4, 10, 11, 12} | {20, 25, 30} | 7 và 25 |
| Gán theo tâm 7 và 25 | không đổi | không đổi | hội tụ |

Kết quả đúng như cụm ghi sẵn trên slide 73. Quy tắc khi khoảng cách bằng nhau nên ghi rõ trong bài làm (gán về cụm có chỉ số nhỏ hơn).

**Hierarchical (slide 74–75):**
- **Agglomerative:** mỗi điểm là một cụm, lần lượt gộp hai cụm gần nhất đến khi còn một cụm. Kết quả là dendrogram.
- **Divisive:** bắt đầu từ một cụm duy nhất rồi tách dần.
- Ví dụ 6 điểm: (1,1), (2,1), (3,5), (4,5), (5,3), (6,4). Khoảng cách Euclidean: (1,1) với (2,1) bằng 1, gần nhất nên gộp trước; (3,5) với (4,5) cũng bằng 1, gộp tiếp; sau đó các cụm này nhập dần với (5,3) và (6,4).

**Density-based (slide 76–77):** cụm là vùng đặc, ngăn bởi vùng thưa, nên tìm được cụm không tròn.
- **DBSCAN** có hai tham số: `eps` (bán kính) và `MinPts` (số điểm tối thiểu trong bán kính đó để thành core point).
- OPTICS, DENCLUE là hai thuật toán cùng họ.
- Ví dụ trên slide với eps = 0,6 và MinPts = 4 cho ra:
  - Cụm 1: (3, 4), (2.5, 4), (3, 5), (2.8, 4.5), (2.5, 4.5)
  - Cụm 2: (1, 2), (1.5, 2.5), (1.2, 2.5), (1, 3), (1, 2.5)
  - Outlier: (1, 5), (5, 6), (4, 3)
- Kiểm tra nhanh: (1.2, 2.5) cách (1, 2) đúng 0,53 < 0,6 nên nằm cùng cụm; (1, 5) cách (1, 2) đúng 3 nên bị loại.

**Grid-based (slide 78):** chia không gian thành các ô **không phụ thuộc** phân bố dữ liệu rồi gom theo ô. Hai thuật toán: STING, CLIQUE. Nhanh vì làm việc trên ô chứ không trên từng điểm.

**Đánh giá clustering (slide 79):**
- Có cấu trúc thật không, hay chỉ là ngẫu nhiên: **Hopkins statistic**. Gần 0,5 là ngẫu nhiên, gần 1 là có cụm.
- Chọn số cụm: **elbow** (vẽ tổng bình phương khoảng cách tới tâm theo k, chọn chỗ đường gãy).
- Chất lượng: **extrinsic** (có nhãn đúng để so) và **intrinsic** (không nhãn, dùng độ gắn kết trong cụm và độ tách giữa các cụm, ví dụ silhouette).

Demo slide 80: phân cụm khách hàng vay ngân hàng để thấy nhóm nào được giải ngân.

## Association rules (slide 81–83)

Dùng cho gợi ý mua hàng. Một luật đáng quan tâm khi **support** (độ phổ biến), **confidence** (độ tin cậy) và **lift** (độ phụ thuộc, lớn hơn 1 mới có tương quan dương) đều đạt ngưỡng.

Hai thuật toán trên slide: **Apriori** (sinh ứng viên theo từng kích thước, tỉa bằng nguyên lý "tập con của frequent itemset cũng frequent") và **FP-growth** (dựng cây FP-tree rồi khai phá không cần sinh ứng viên, nhanh hơn trên dữ liệu lớn).

Công thức:

\[
support(X \Rightarrow Y) = P(X \cup Y), \quad confidence(X \Rightarrow Y) = \frac{P(X \cup Y)}{P(X)}, \quad lift = \frac{confidence}{P(Y)}
\]

Demo slide 83 là market basket: giỏ hàng nào hay đi cùng nhau.

## Hồi quy tuyến tính (slide 84–88)

**Đơn:** \( Y = a + bX \). a là tung độ gốc, b là độ dốc.
Ví dụ: Weight = 80 + 2 × Height. Cao thêm 1 đơn vị thì nặng thêm 2.

**Bài trên slide 85.** Dữ liệu (2, 3), (4, 7), (6, 5), (8, 10).

\[
b = \frac{n\sum xy - \sum x\sum y}{n\sum x^2 - (\sum x)^2} = \frac{4 \times 144 - 20 \times 25}{4 \times 120 - 400} = \frac{76}{80} = 0{,}95
\]

\[
a = \bar y - b\bar x = 6{,}25 - 0{,}95 \times 5 = 1{,}5
\]

Vậy **Y = 1,5 + 0,95X**.

**Bội:** \( Y = a + b_1 X_1 + \dots + b_n X_n + e \), e là phần dư.
Ví dụ: BMI = 18 + 1,5 × diet score + 1,6 × male + 4,2 × (age > 20).

**Bài ô tô (slide 87), phương trình cho sẵn:**

\[
Price = 32{,}46 - 1{,}54 \times Age - 0{,}15 \times Mileage
\]

Đọc được ngay: xe thêm 1 năm tuổi thì giá giảm khoảng 1.540 USD, thêm 1.000 dặm thì giảm khoảng 150 USD, khi đã giữ biến kia không đổi. Kiểm tra xe 1: 32,46 − 1,54 × 1 − 0,15 × 18 = 28,22, giá thực 29. Phần lệch là e.

Demo slide 88: hồi quy doanh thu theo chi phí marketing.

### Câu hỏi thi từ Lecture 5
1. Vẽ quy trình KDD và chỉ ra data mining nằm ở đâu.
2. Phân biệt characterization với discrimination, classification với regression, classification với clustering.
3. Cho một dãy số, tính mean, median, mode, Q1, Q3, IQR, phương sai.
4. Tính information gain và chỉ ra thuộc tính tách đầu tiên.
5. Áp dụng Naive Bayes cho một bộ như AllElectronics.
6. Tính accuracy, precision, recall, F1 từ confusion matrix.
7. Chạy K-means vài vòng đến khi hội tụ.
8. Tính support, confidence, lift. Nêu ý tưởng Apriori.
9. Lập phương trình hồi quy tuyến tính đơn từ 4 đến 6 điểm.

---

# LECTURE 6: BUSINESS INTELLIGENCE AND VISUALIZATION (77 slide)

## Hệ thống BI (slide 3–16)

Theo Grossmann và Rinderle-Ma, một hệ BI có 4 đặc điểm:

| Đặc điểm | Nội dung |
|---|---|
| **BI task** | Hỗ trợ quyết định cho mục tiêu cụ thể trong một lĩnh vực nghiệp vụ |
| **BI foundation** | Quyết định dựa trên thông tin thực nghiệm, tức là dữ liệu |
| **BI realization** | Phải thành một hệ thống chạy được bằng công nghệ thông tin và truyền thông |
| **BI delivery** | Đưa đúng thông tin, đúng lúc, đúng người, đúng dạng |

**Ngữ cảnh (slide 5):** theo kịch bản sử dụng (báo cáo chuẩn cho một mảng, giám sát hiệu suất, phản hồi cho việc hoạch định chiến lược, BI như một nguồn lực chiến lược) và theo góc nhìn (sản xuất, khách hàng, tổ chức).

**Mục tiêu (slide 6):**
- **KPI:** đại lượng đo được, nối hoạt động với mục tiêu.
- **Influential factors:** các thuộc tính có thể làm KPI thay đổi.
- **Mục tiêu phân tích:** descriptive (báo cáo, phân khúc, phát hiện hành vi bất thường) và predictive (regression, classification).

**Việc Alice nên làm (slide 8), đây là cách biến nghiệp vụ thành đầu bài BI:**
- Dashboard bán hàng, kèm dự báo doanh thu.
- Hiệu suất giao hàng, hiệu suất đặt bàn.
- Dashboard khách hàng với **repeat rate** (tỷ lệ khách quay lại).
- Dashboard phản hồi với điểm feedback.
- Dashboard món ăn với **phân tích ABC** (nhóm A ít món nhưng chiếm phần lớn doanh thu, nhóm C nhiều món nhưng doanh thu nhỏ).

**5 nhóm tác vụ BI (slide 9–14):**

1. **Data task.** Tiền đề của mọi việc khác. Tổ chức thông tin sẵn có về doanh nghiệp và môi trường của nó. Không chỉ lấy dữ liệu mà còn sắp xếp lại và thu thập thêm cho mục đích riêng.
2. **Business and data understanding.** Xác định ngữ cảnh, phát biểu mục tiêu và KPI, xác định dữ liệu cần để đạt mục tiêu.
3. **Modeling.** Dựng mô hình nghiệp vụ phân tích, tức là mô hình hình thức trả lời được đúng mục tiêu. Hình slide 12 có ba tầng:
   - Trên: mục tiêu (business understanding, descriptive, predictive).
   - Giữa: ba góc nhìn Production, Customer, Organization giao nhau, nhìn theo state, theo sự kiện, và nhìn chéo thời điểm.
   - Dưới: cấu trúc mô hình có thể là đại số/logic, đồ thị, hoặc giải tích/thống kê.
4. **Analysis.** Thuật toán tính ra lời giải trong mô hình đó: thống kê, data mining, machine learning.
5. **Evaluation and reporting.** Đánh giá kết quả theo hai phía: có đạt mục tiêu phân tích không, và có ý nghĩa với nghiệp vụ không. Reporting diễn giải kết quả bằng lời và bằng hình.

**Phương pháp iMine (slide 15)** nối các tác vụ này thành vòng lặp:
```
Deployment
  → Business + Data Understanding → (Relevant Business, Analytical Goal, Relevant Data)
  → Modeling → Analytical Business Model
  → Analysis → Analysis Results
  → Evaluation → Evaluation Results
  → quay lại: sửa mục tiêu, bổ sung dữ liệu, chuẩn bị dữ liệu lại
```
Mỗi khối có kỹ thuật riêng: business modeling, data modeling, data preparation, analysis. Vòng lặp có phản hồi (feedback) từ kết quả đánh giá về lại mục tiêu. Đây là phiên bản BI của CRISP-DM.

## Nguyên tắc trực quan hóa (slide 17–40)

Sách gốc là Claus Wilke, *Fundamentals of Data Visualization*.

**Ba mức lỗi của một biểu đồ (slide 18):**
- **Ugly:** đúng số liệu nhưng xấu (màu sặc sỡ vô nghĩa, mỗi cột một màu trong khi chúng cùng một đại lượng).
- **Bad:** khó đọc (hai trục chồng lên nhau, người xem không biết cột thuộc trục nào).
- **Wrong:** sai sự thật (trục không bắt đầu từ 0 nên cột giá trị 1 trông chỉ bằng một phần tư cột giá trị 4).

Thứ tự ưu tiên khi sửa: **wrong trước, rồi bad, rồi ugly**. Một biểu đồ đẹp nhưng sai thì nguy hiểm hơn một biểu đồ xấu nhưng đúng.

**Thẩm mỹ (slide 19):** hình cân đối, chữ đọc được, màu phục vụ dữ liệu, không trang trí thừa.

**Chọn thang đo theo loại dữ liệu (slide 20):**

| Loại biến | Thang | Ví dụ |
|---|---|---|
| Định lượng liên tục | Liên tục | 1.2, 5.8, 3e-2 |
| Định lượng rời rạc | Rời rạc | 1, 5, 8, 12 |
| Định tính không có thứ tự | Rời rạc | hồng, tím, trắng |
| Định tính có thứ tự | Rời rạc | kém, khá, giỏi |
| Thời gian | Rời rạc hoặc liên tục | 2023-02-23 04:35:58 |
| Văn bản | Không có thang hoặc rời rạc | "bánh bị cháy" |

### Màu (slide 35–40): 4 thang, học thuộc vì hay ra tình huống

| Thang | Dùng cho | Quy tắc |
|---|---|---|
| **Sequential** | Giá trị số một chiều: thu nhập, nhiệt độ, tốc độ | Nhìn phải biết giá trị nào lớn hơn và **lệch nhau bao nhiêu**. Thang phải được cảm nhận là đổi đều từ đầu đến cuối |
| **Diverging** | Giá trị lệch về hai phía quanh một mốc: lãi/lỗ, trên/dưới trung bình | Mốc ở giữa dùng màu sáng. Càng xa mốc càng đậm |
| **Qualitative** | Nhóm không có thứ tự: quốc gia, hãng sản xuất | Các màu tách bạch, **ngang hàng nhau**, không màu nào nổi hơn, không tạo cảm giác có thứ tự (không được dùng một màu nhạt dần) |
| **Accent** | Nhấn một phần tử | Nền là màu trầm, màu nhấn đậm và bão hòa hơn. Màu nền không được tranh sự chú ý |

Slide 40 minh họa cùng một bảng nhiệt độ 4 thành phố bằng **hai hình đúng mục đích khác nhau**: line chart để thấy đường đi của từng thành phố qua các tháng, heatmap (thang sequential) để so sánh toàn bộ thành phố và tháng trong một cái nhìn. Cùng dữ liệu, khác câu hỏi, khác biểu đồ.

### Biểu đồ xấu và biểu đồ tốt (slide 22–34)

Cả chuỗi slide lấy từ các cặp so sánh thực tế. Bài học lặp lại:

| Làm xấu / làm sai | Làm đúng |
|---|---|
| Pie chart nhiều lát, nhãn chồng lên nhau (slide 22: 11 loại trái cây) | Bar ngang, **sắp theo giá trị giảm dần**, nhóm nhỏ gộp thành "Others" |
| Trục cột không bắt đầu từ 0 | Trục cột bắt đầu từ 0 |
| 3D, bóng đổ, nền ảnh | Phẳng, nền trắng |
| Mỗi cột một màu trong khi cùng một thước đo | Một màu, chỉ tô khác khi muốn nhấn |
| Hai trục tung trên cùng một hình | Tách thành hai hình dùng chung trục hoành |
| Chú giải xa dữ liệu, người xem phải nhìn đi nhìn lại | Gắn nhãn trực tiếp |
| Đơn vị, nguồn, thời điểm bị thiếu | Có tiêu đề nêu kết luận, có đơn vị, có nguồn |

Slide 33 lấy bài báo VnExpress về cung cầu nước sạch Hà Nội làm ví dụ thực hành: bản đồ vị trí nhà máy (dữ liệu địa lý), bản đồ tô màu tỷ lệ dân được cấp nước (choropleth), và cột kế hoạch cấp nước theo năm (chuỗi thời gian cộng với mục tiêu). Một bài phân tích dùng **nhiều loại hình**, mỗi hình một câu hỏi.

## Chọn biểu đồ theo câu hỏi (slide 41–74)

### Số lượng (slide 41–47)
Khi quan tâm độ lớn: doanh số từng hãng, số khách từng khu vực.
- **Bar đơn:** một đại lượng, nhiều hạng mục. Hạng mục dài thì đặt bar ngang.
- **Grouped bar:** một đại lượng tách theo một nhóm thứ hai (doanh số từng hãng, tách theo năm).
- **Stacked bar:** cộng dồn các thành phần. Dùng khi muốn thấy cả tổng lẫn cơ cấu.
- **Dot plot:** thay cột bằng chấm. Hợp khi có rất nhiều hạng mục.
- **Heatmap:** hai chiều phân loại, màu biểu diễn giá trị (đúng hình nhiệt độ ở slide 40).

### Tỷ trọng (slide 48–53)
Cái toàn thể bị chia thành các phần.
- **Pie:** chỉ ổn khi ít phần (khoảng dưới 5) và các phần khác nhau rõ.
- **Stacked bar:** so sánh cơ cấu của nhiều toàn thể (cơ cấu doanh thu của nhiều năm). Có bản 100% khi muốn so tỷ lệ chứ không so độ lớn.
- **Stacked density:** như stacked bar nhưng trục hoành liên tục, thấy cơ cấu thay đổi mượt theo thời gian.

### Phân phối (slide 54–56)
- **Histogram:** chia biến thành các bin, chiều cao là số lượng. Slide 55 cho bảng tuổi: đỉnh ở nhóm 21–25 (139 người) và 26–30 (121), giảm dần hai phía, nhóm 0–5 nhô lên 36. Phân phối lệch phải kèm một nhóm trẻ nhỏ.
- **Density plot:** bản làm trơn của histogram, tiện khi chồng nhiều nhóm lên cùng một hình.

### Quan hệ giữa các biến (slide 57–61)
- **Scatter:** mỗi quan sát một điểm, thấy hướng, độ mạnh, điểm lệch.
- **Bubble:** scatter thêm biến thứ ba bằng diện tích chấm.
- **Scatterplot matrix:** mọi cặp trong một nhóm biến, sắp thành lưới, nhìn nhanh cặp nào liên quan.
- **Correlogram:** ma trận hệ số tương quan, thường tô bằng thang diverging (âm một màu, dương một màu, 0 là màu trung tính).

### Chuỗi thời gian (slide 62–65)
- Một chuỗi: line chart, trục hoành là thời gian.
- Nhiều chuỗi: nhiều đường trên cùng một hình nếu cùng đơn vị và số đường ít; tách thành các hình nhỏ cùng thang đo nếu nhiều đường.
- Hai biến theo thời gian: hai hình xếp chồng dùng chung trục thời gian, tránh trục tung kép.

### Xu hướng (slide 66–67)
Đè lên dữ liệu một lớp tóm tắt: **làm trơn** (trung bình trượt) khi muốn thấy hướng chung mà không giả định dạng hàm, **đường thẳng** khi giả định tuyến tính, **đường cong** khi quan hệ cong.

### Dữ liệu địa lý (slide 68–71)
- **Map layer:** chọn phép chiếu phù hợp rồi xếp các lớp (ranh giới, đường, điểm).
- **Choropleth:** tô màu từng vùng theo giá trị. Màu phải là sequential hoặc diverging, và cần lưu ý vùng rộng dễ bị nhìn thành quan trọng hơn vùng nhỏ.
- **Cartogram:** bóp hình vùng theo giá trị (vùng nhiều dân được vẽ to hơn), đánh đổi hình dạng lấy sự trung thực về độ lớn.

### Độ bất định (slide 72–74)
Số liệu ước lượng phải kèm độ không chắc.
- **Error bar:** vẽ trên điểm hoặc cột, nửa trên nửa dưới thường là sai số chuẩn hoặc khoảng tin cậy.
- **Confidence band:** dải tô quanh một đường (dự báo ARIMA trên slide 74). Dải càng rộng, dự báo càng không chắc, và thường rộng ra ở càng xa hiện tại.

### Câu hỏi thi từ Lecture 6
1. Nêu 4 đặc điểm của một hệ BI và 5 nhóm tác vụ. KPI khác influential factor chỗ nào.
2. Mô tả vòng iMine.
3. Phân biệt biểu đồ ugly, bad và wrong. Cho một ví dụ wrong.
4. Cho từng tình huống sau, chọn thang màu: nhiệt độ, lãi lỗ quanh mốc 0, hãng xe, nhấn một nước trên bản đồ.
5. Cho từng câu hỏi sau, chọn biểu đồ: so sánh doanh thu 12 cửa hàng, cơ cấu doanh thu 4 nhóm hàng, phân phối tuổi khách, quan hệ giữa chi phí quảng cáo và doanh thu, doanh thu 5 năm, doanh thu theo tỉnh.

---

# Nhịp ôn lại sau khi có đủ 6 lecture

| Nếu đề hỏi | Mở |
|---|---|
| Vì sao cần DW, kiến trúc, DSS | Lecture 1 |
| OLTP với OLAP, cách khám phá CSDL lạ | Lecture 2 |
| Vẽ star schema, SCD, viết SQL trên fact | Lecture 3 |
| ETL, tích hợp, chất lượng dữ liệu, MERGE | Lecture 4 |
| Tính gain, Bayes, K-means, hồi quy, support | Lecture 5 |
| Chọn biểu đồ, chọn màu, chỉ ra biểu đồ sai | Lecture 6 |
