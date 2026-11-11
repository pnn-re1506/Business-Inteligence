# Giải thích chi tiết Lecture 4: ETL and Data Integration Challenges

> Bài giảng của TS. Phan Trọng Nhân, 62 slide, gồm 4 phần: **ETL overview → Data integration → Data quality → SSIS demo**.
> Giải thích đi theo đúng thứ tự slide. Cuối file có phần "Câu hỏi thi có thể ra".

---

## PHẦN 1: ETL OVERVIEW (slide 3–8)

### Slide 4: DWH Architecture (kiến trúc kho dữ liệu, hình từ sách Elmasri & Navathe)
Sơ đồ đọc từ trái sang phải:

1. **Databases + Other data inputs**: dữ liệu nguồn, gồm các CSDL nghiệp vụ (OLTP) và các nguồn khác (file, dữ liệu bên ngoài).
2. **Extract, Transform, Load (ETL)**: lấy dữ liệu từ nguồn, làm sạch, biến đổi, rồi nạp vào kho.
3. **Data Warehouse**: chứa **Data** và **Metadata** (dữ liệu mô tả dữ liệu: bảng nào, lấy từ đâu, biến đổi ra sao).
4. **Đầu ra phục vụ phân tích**:
   - **OLAP**: phân tích đa chiều (roll-up, drill-down, slice, dice).
   - **DSS / EIS**: Decision Support System và Executive Information System (hệ hỗ trợ ra quyết định, hệ thông tin cho lãnh đạo).
   - **Data mining**: khai phá tri thức.
5. Hai mũi tên quay ngược:
   - **Backflushing**: dữ liệu đã được làm sạch trong DW được **đẩy ngược về** hệ thống nguồn để sửa dữ liệu gốc.
   - **Updates/new data**: kết quả phân tích sinh ra dữ liệu mới (ví dụ điểm tín dụng, phân khúc khách hàng) và được đưa trở lại hệ thống vận hành.

> Ý chính: ETL là "cửa ngõ" duy nhất đưa dữ liệu vào DW. Chất lượng của DW phụ thuộc vào ETL.

### Slide 5–6: ETL vs ELT
- **ETL**: Extract → **Transform** (trên một server ETL riêng) → Load vào DW. Dữ liệu vào kho đã sạch.
- **ELT**: Extract → **Load** dữ liệu thô vào đích trước → **Transform** ngay trong đích (dùng sức mạnh tính toán của DW/data lake).

Slide 6 là câu hỏi thảo luận **"Which one would you prefer?"**. Đáp án là **tùy tình huống**:

| Chọn **ETL** khi | Chọn **ELT** khi |
|---|---|
| DW truyền thống, on-premise, sức tính toán của đích hạn chế | Cloud DW (BigQuery, Snowflake, Redshift, Synapse) mạnh, mở rộng được |
| Dữ liệu có cấu trúc, quy tắc biến đổi rõ ràng, ổn định | Dữ liệu lớn, đa dạng (bán cấu trúc, phi cấu trúc) |
| Cần lọc hoặc che dữ liệu nhạy cảm **trước** khi vào kho (tuân thủ pháp lý) | Muốn giữ dữ liệu thô để biến đổi lại theo nhu cầu mới |
| Muốn dữ liệu trong kho luôn sạch | Cần nạp nhanh, giảm độ trễ |

### Slide 7: How to refresh (cách làm mới dữ liệu trong DW)
Đây là các cách **phát hiện dữ liệu thay đổi** ở nguồn để cập nhật vào kho:

| Cách | Giải thích | Ưu/nhược |
|---|---|---|
| **By snapshot** | Chụp toàn bộ dữ liệu nguồn ở mỗi thời điểm, so sánh snapshot mới với cũ để tìm khác biệt (hoặc nạp lại toàn bộ) | Đơn giản, nhưng tốn tài nguyên với dữ liệu lớn |
| **By application code** | Sửa ứng dụng nguồn để mỗi khi có giao dịch thì ghi thêm bản ghi thay đổi ra nơi khác | Chính xác, nhưng phải sửa ứng dụng, hệ cũ khó làm |
| **By log or audit file** | Đọc **transaction log** hoặc audit log của DBMS (chính là kỹ thuật **CDC: Change Data Capture**) | Không ảnh hưởng ứng dụng, gần real-time, nhưng log có định dạng phức tạp, phụ thuộc DBMS |
| **By timestamp** | Mỗi bản ghi có cột thời gian (ví dụ `ModifiedDate`); chỉ lấy bản ghi có thời gian lớn hơn lần nạp trước | Phổ biến, dễ làm (đây là kỹ thuật "incremental by date time" ở slide 58). Nhược điểm: không bắt được bản ghi bị **xóa**, nguồn phải có cột timestamp |
| **By user-defined** | Cách do người dùng tự định nghĩa: trigger, cờ (flag), bảng tạm... | Linh hoạt |

### Slide 8: Data integration tools
Các công cụ ETL/tích hợp dữ liệu:
- **SSIS** (SQL Server Integration Services, Microsoft): được dùng trong demo của bài.
- **Talend (Open Studio)**, **Pentaho (Kettle)**, **CloverETL**: công cụ ETL mã nguồn mở hoặc thương mại, kéo thả.
- **Holistics, Skyvia, Alooma**: công cụ cloud.
- **Python + Airflow**: tự viết ETL bằng code, Airflow dùng để lập lịch và điều phối (orchestration).
- **Kafka + Graylog + Elasticsearch**: xử lý dữ liệu **streaming** (luồng thời gian thực) và log.
- **Information Builders, Syncsort**: giải pháp doanh nghiệp.

---

## PHẦN 2: DATA INTEGRATION (slide 9–39)

### Slide 10: Pretty much everywhere
Tích hợp dữ liệu có mặt ở khắp nơi: kinh doanh, khoa học, web, chính phủ. Ví dụ **Flightradar24** gộp dữ liệu từ hàng nghìn trạm thu tín hiệu máy bay, hãng bay, sân bay để hiển thị máy bay trên bản đồ theo thời gian thực.
> Câu "**Data integration should be machine work**" có nghĩa là việc tích hợp nên được **tự động hóa** bằng máy, không thể làm thủ công ở quy mô lớn.

### Slide 11: Integration Issues (1/4): bảng phân loại của Schmidt et al. (2010)
Vấn đề tích hợp xảy ra ở **nhiều tầng**, không chỉ tầng dữ liệu:

| Tầng | Ví dụ vấn đề |
|---|---|
| **Strategy** (chiến lược) | Hai đơn vị kinh doanh không bổ trợ nhau; mô hình kinh doanh khác nhau; mục tiêu mâu thuẫn; KPI khác nhau |
| **Processes** (quy trình) | Số vai trò khác nhau; phạm vi chức năng khác; **độ chi tiết (granularity) khác**; thứ tự các bước khác; nghiệp vụ dùng từ đồng nghĩa/đồng âm khác nghĩa |
| **Systems/Applications** | Truy cập dữ liệu và chức năng từ bên ngoài; mức độ chi tiết của chức năng khác nhau |
| **Data** (phần được khoanh, quan trọng nhất cho môn này) | Xem bên dưới |

**Tầng Data (học kỹ):**
- **16. Data model heterogeneity**: khác **mô hình dữ liệu** (CSDL quan hệ vs NoSQL vs XML vs file Excel).
- **17. Syntactic heterogeneity** (khác cú pháp): khác **kiểu dữ liệu** (số vs chuỗi), **độ dài trường**, **miền giá trị**. Ví dụ mã khách hàng là `INT` ở hệ này, `VARCHAR(10)` ở hệ kia.
- **18. Structural heterogeneity** (khác cấu trúc): cùng một khái niệm nhưng hệ này mô hình là **thuộc tính**, hệ kia là **thực thể riêng**; khác **bản số (cardinality)**. Ví dụ hệ A: `Address` là một cột của Customer; hệ B: Address là bảng riêng, một khách có nhiều địa chỉ (1-n).
- **19. Semantic heterogeneity** (khác ngữ nghĩa): hiểu khác nhau về cùng một đối tượng; có **synonyms** (khác tên, cùng nghĩa: `Employee` và `Staff`) và **homonyms** (cùng tên, khác nghĩa: `Price` là giá nhập hay giá bán?).
- **20.** Thuộc tính được định nghĩa khác nhau (bắt buộc ở hệ này, tùy chọn ở hệ kia).
- **21.** Chất lượng dữ liệu kém / xung đột giá trị (sai, thiếu thuộc tính).

### Slide 12–14: Integration Issues (2/4 → 4/4): danh sách vấn đề tích hợp (HỌC THUỘC)

| # | Vấn đề | Giải thích | Ví dụ |
|---|---|---|---|
| 1 | **Heterogeneous data sources** | Nguồn không đồng nhất về công nghệ | SQL Server, Oracle, PostgreSQL, file CSV, Excel |
| 2 | **Data mapping** | Phải ánh xạ schema nguồn sang schema đích vì tên/cấu trúc khác nhau | Bảng `Employee` vs `Emp` |
| 3 | **Data conflicts** | Cùng thông tin nhưng khác **kiểu dữ liệu, giá trị, định dạng, đơn vị, độ chính xác** | Ngày `dd/mm/yyyy` vs `mm/dd/yyyy`; kg vs pound; giá làm tròn 2 vs 4 chữ số thập phân |
| 4 | **Data redundancy** | Dữ liệu bị **trùng lặp** | Một khách hàng có 2 bản ghi ở 2 hệ thống |
| 5 | **Entity resolution** | Xác định các bản ghi khác nhau có phải **cùng một thực thể** thật không | 3 cách viết địa chỉ Trường ĐH Bách Khoa ở slide 13 |
| 6 | **Constraints violation** | Dữ liệu sau khi gộp vi phạm ràng buộc | Gộp 2 nguồn làm **trùng khóa chính** (cả hai đều có CustID = 1 nhưng là 2 người khác nhau); vi phạm ràng buộc ngữ nghĩa (ngày giao trước ngày đặt) |
| 7 | **Data quality** | Accuracy, completeness, uniqueness, timeliness, consistency | (xem Phần 3) |
| 8 | **Communication heterogeneity** | Các nguồn cung cấp dữ liệu qua **giao diện khác nhau** | Một nguồn là web page, nguồn khác là REST API, nguồn khác là kết nối DB trực tiếp |

**Slide 13: ví dụ entity resolution.** Ba chuỗi sau **cùng chỉ một trường đại học**:
1. `HCMC University of Technology, 268 Ly Thuong Kiet Street, Ward 14, District 10, Ho Chi Minh City, Vietnam`
2. `University of Technology, VNU-HCM, 268 Ly Thuong Kiet Street, Ward 14, District 10, ...`
3. `University of Technology, VNU-HCM, 268 Ly Thuong Kiet Str., Dist. 10, HCMC, VN`

Máy so sánh chuỗi chính xác (`=`) sẽ coi là 3 thực thể khác nhau. Cần **chuẩn hóa** (Str. thành Street, Dist. thành District, HCMC thành Ho Chi Minh City) và **so khớp gần đúng (fuzzy matching)**, ví dụ dùng độ đo edit distance, Jaccard.

### Slide 15: An example (ví dụ schema mapping)
Hai nguồn lưu khách hàng:

| S1.Cust | S2.Customer |
|---|---|
| CNo | CustID |
| CompName | Company |
| FirstName | Contact |
| LastName | Phone |

Phân tích:
- `Cust` ↔ `Customer`: **khác tên bảng, cùng nghĩa** (synonym).
- `CNo` ↔ `CustID`, `CompName` ↔ `Company`: ánh xạ **1-1** (khác tên, cùng nghĩa).
- `FirstName` + `LastName` ↔ `Contact`: ánh xạ **n-1**. S2 gộp họ tên vào một cột, nên khi tích hợp phải **ghép** (`Contact = FirstName + ' ' + LastName`) hoặc **tách** Contact ra.
- `Phone` chỉ có ở S2, S1 không có, nên sau khi gộp sẽ bị **thiếu dữ liệu (NULL)** cho khách từ S1.
- Ngoài ra `CNo` và `CustID` có thể **trùng giá trị** giữa hai nguồn, nên cần **surrogate key** trong DW.

### Slide 16–20: Airline sources (ví dụ xuyên suốt, từ sách *Big Data Integration*, Dong & Srivastava 2015)
Có 5 nguồn dữ liệu về chuyến bay:

| Nguồn | Bảng | Nội dung |
|---|---|---|
| **Airline1** (hãng bay 1) | `Schedule` | Lịch bay theo **khoảng thời gian hiệu lực** (Start Date – End Date), giờ đi/đến dự kiến |
| | `Flight` | Từng chuyến bay thực tế: ngày/giờ/cổng đi, ngày/giờ/cổng đến, máy bay |
| **Airline2** (hãng bay 2) | `Flight` | Gộp chung lịch dự kiến (**Scheduled**) và thực tế (**Actual**) trong một bảng |
| **Airport3** (sân bay) | `Departures`, `Arrivals` | Hãng, số hiệu, Scheduled, Actual, **Gate Time** (giờ rời/đến cổng), **Takeoff/Landing Time** (giờ cất/hạ cánh), Terminal, Gate, Runway |
| **Airfare4** (trang bán vé) | `Flight`, `Fares` | Chuyến bay và **giá vé** theo hạng (Fare Class) |
| **Airinfo5** (dữ liệu tham chiếu) | `AirportCodes`, `AirlineCodes` | Bảng tra mã sân bay (EWR = Newark Liberty) và mã hãng bay |

**Nhận xét quan trọng:** cùng là "chuyến bay" nhưng **mỗi nguồn mô hình hóa khác nhau**: Airline1 tách Schedule và Flight, Airline2 gộp làm một, Airport3 tách theo đi và đến. Đây là **structural heterogeneity**.

### Slide 21: Integration needs (nhu cầu tích hợp)
- **Linking (liên kết)**: Airline + Airport; Airline + Airport + Airfare.
- **Lợi ích:** tìm nguyên nhân trễ chuyến, phân tích mô hình chuyến bay, hỗ trợ đặt vé, **một điểm truy cập duy nhất (single point access)**.
- **Câu truy vấn mẫu:** *"Với mỗi số hiệu chuyến bay, tính độ trễ trung bình giữa giờ khởi hành dự kiến và thực tế, và giữa giờ rời cổng thực tế và giờ cất cánh, trong 1 tháng qua."*
  - Muốn trả lời phải **gộp** Airline (giờ dự kiến) với Airport3 (giờ rời cổng và giờ cất cánh). Không nguồn đơn lẻ nào có đủ dữ liệu, nên **bắt buộc phải tích hợp**.

### Slide 22–28: Data integration challenges (3 loại thách thức chính)

#### Slide 23: Semantic ambiguity (mơ hồ ngữ nghĩa)
Có hai dạng:
1. **Giống nhau về khái niệm nhưng mô hình khác nhau.** "Departure/Arrival time":
   - Airline1 lưu **giờ rời/đến cổng (gate)**.
   - Airline2 lưu **giờ cất/hạ cánh (takeoff/landing)**.
   - Cả hai đều gọi là "departure time" nhưng thực chất **lệch nhau vài chục phút** (máy bay rời cổng, lăn bánh, rồi mới cất cánh).
2. **Khác nhau nhưng mô hình giống nhau.** "Departure date":
   - Ở Airline1 là ngày khởi hành **thực tế** của chuyến bay.
   - Ở Airfare4 là ngày khởi hành **dự kiến** (trên vé).
   - Cùng tên cột nhưng ý nghĩa khác.

#### Slide 24: Instance representation ambiguity (mơ hồ biểu diễn thể hiện)
**Cùng một giá trị thực tế nhưng biểu diễn khác nhau** giữa các nguồn:
- Số hiệu chuyến bay: Airline1 ghi `49`, Airfare4 ghi `A1-49` (kèm mã hãng).
- Sân bay: Airline ghi mã `EWR`, `SFO`; Airfare4 ghi tên `Newark Liberty`, `San Francisco`; Airinfo5 ghi `Newark Liberty, NJ, US`.
- Muốn khớp phải giải **bài toán so khớp chuỗi (string matching)**: "Newark Liberty" có khớp "Newark Liberty, NJ, US" không?

#### Slide 25: For example: hình "The issue of integration" (Inmon, *Building the Data Warehouse*, HAY THI)
Bốn ví dụ kinh điển về việc **phải biến đổi khi đưa dữ liệu từ các ứng dụng vận hành vào DW**:

| Vấn đề | Ở nguồn | Trong DW | Giải pháp |
|---|---|---|---|
| **Encoding** (mã hóa giới tính) | appl A: `m,f`; appl B: `1,0`; appl C: `x,y`; appl D: `male, female` | Thống nhất `m,f` | Bảng ánh xạ mã (mapping) |
| **Attribute measurement** (đơn vị đo) | Chiều dài ống: cm, inches, mcf, yards | Thống nhất **cm** | Quy đổi đơn vị |
| **Multiple sources** (nhiều nguồn cho một thuộc tính) | 4 nguồn cùng cung cấp `description` | Chọn **một** `description` | Quy tắc chọn nguồn đáng tin nhất (dấu `?`): đây chính là **data fusion** |
| **Conflicting keys** (khóa xung đột) | `char(10)`, `dec fixed(9,2)`, `pic '9999999'`, `char(12)` | Thống nhất `key char(12)` | Chuyển kiểu, chuẩn hóa độ dài khóa |

#### Slide 26–28: Data inconsistency (dữ liệu mâu thuẫn): đọc bảng ví dụ
Ký hiệu cột: FN = Flight Number, DA = Departure Airport, SDD/SDT = Scheduled Departure Date/Time, ADT = Actual Departure Time, AA = Arrival Airport, SAD/SAT = Scheduled Arrival Date/Time, AAT = Actual Arrival Time; ở Airport3: S = Scheduled, A = Actual, GT = Gate Time, LT = Landing Time.

**(1/3): r32 (Airline2.Flight) và r52 (Airport3.Arrivals), chuyến 53 SFO → EWR ngày 2013-12-22:**
- r32 ghi giờ đến thực tế **00:30** nhưng **không có "(+1d)"**, tức là hiểu là 00:30 ngày 22/12. Điều này **vô lý** vì máy bay cất cánh lúc 16:15 ngày 22/12.
- r52 ghi ngày đến thực tế **2013-12-23**, hạ cánh 00:30.
- Vậy r32 **thiếu thông tin ngày** (đúng ra phải là 00:30 (+1d)). Hai nguồn **mâu thuẫn**, phải tin r52.
- (So sánh với r31: chuyến hôm trước ghi đúng `00:15 (+1d)`, khớp với r51.)

**(2/3): r62 (Airfare4.Flight) và r12 (Airline1.Schedule), chuyến A1-49 Newark → San Francisco ngày 2014-04-05:**
- Airline1.Schedule có hai lịch cho chuyến 49:
  - r11 (hiệu lực 2013-10-01 → 2014-03-31): đi 18:05, đến 21:10.
  - r12 (hiệu lực **2014-04-01 → 2014-09-30**): đi **18:20**, đến **21:25**.
- Ngày 2014-04-05 thuộc lịch r12, nhưng Airfare4 (r62) vẫn ghi **18:05 / 21:10**.
- Vậy Airfare4 dùng **dữ liệu lỗi thời (out-of-date)**, chưa cập nhật lịch mới. Đây là vấn đề **timeliness**.

**(3/3): r65 (Airfare4.Flight) và r33 (Airline2.Flight), chuyến A2-53 SFO → Newark ngày 2014-06-28:**
- Airfare4 ghi đi **15:30**, đến **23:35**.
- Airline2 (r33) ghi lịch dự kiến đi **16:00**, đến **00:05 ngày 2014-06-29**.
- Vậy giờ trên trang bán vé lại **khác** với giờ của hãng: vẫn là giờ của lịch cũ (giống r31, r32). Đây là **xung đột giá trị** cần **data fusion** để quyết định tin nguồn nào (thường tin hãng bay hơn trang bán vé).

> Bài học: các nguồn **mâu thuẫn nhau** vì gõ sai, thiếu thông tin, hoặc lỗi thời. DW cần cơ chế giải quyết xung đột.

### Slide 29–32: Data integration architecture: 3 bước (RẤT QUAN TRỌNG)

```
Schema Alignment  ──►  Record Linkage  ──►  Data Fusion
(khớp lược đồ)        (liên kết bản ghi)    (hợp nhất dữ liệu)
 giải quyết             giải quyết              giải quyết
 semantic ambiguity     instance representation data inconsistency
                        ambiguity
```

#### Slide 30: Schema Alignment (căn chỉnh lược đồ)
- **Kết quả đầu ra:**
  1. **Mediated schema**: một lược đồ chung, thống nhất mà người dùng truy vấn.
  2. **Attribute matching**: cặp thuộc tính nào ở các nguồn tương ứng với nhau.
  3. **Schema mapping**: quy tắc chuyển dữ liệu từ lược đồ nguồn sang mediated schema.
- **Thách thức:**
  - **Cùng miền giá trị nhưng khác schema:** ngày đến thực tế có tên `Arrival Date` (Airline1.Flight), `Actual Arrival Date` (Airline2.Flight), `Actual` (Airport3.Arrivals).
  - **Cùng tên thuộc tính nhưng khác nghĩa:** `Actual` trong Airport3.**Departures** là giờ **đi** thực tế, `Actual` trong Airport3.**Arrivals** là giờ **đến** thực tế.

#### Slide 31: Record Linkage (liên kết bản ghi, còn gọi là entity resolution, deduplication)
- **Kết quả:** xác định các bản ghi nào **cùng chỉ một thực thể** ngoài đời thực.
- **Thách thức:**
  - Cùng thực thể nhưng mô tả khác nhau: r11 (Airline1.Schedule) và r21 (Airline1.Flight) phải được liên kết với r41 (Airport3.Departures). Cả 3 là cùng một chuyến bay.
  - Cùng thông tin nhưng biểu diễn khác: các cách ghi sân bay (EWR, Newark Liberty, Newark Liberty, NJ, US).
  - **So sánh mọi cặp bản ghi là không khả thi**: n bản ghi cần khoảng n²/2 phép so sánh. 1 triệu bản ghi cho ra khoảng 5×10¹¹ cặp. Giải pháp là **blocking**: chỉ so sánh các bản ghi trong cùng nhóm, ví dụ cùng số hiệu chuyến bay và cùng ngày.

#### Slide 32: Data Fusion (hợp nhất dữ liệu)
- **Kết quả:** khi các bản ghi đã được liên kết mà **giá trị mâu thuẫn**, chọn ra giá trị đúng. Mâu thuẫn có thể do **gõ sai, tính sai, dữ liệu lỗi thời**.
- **Thách thức:** **chất lượng dữ liệu**: nguồn nào đáng tin hơn? Các chiến lược thường gặp:
  - Bỏ phiếu đa số (voting).
  - Tin nguồn uy tín nhất (ví dụ hãng bay và sân bay đáng tin hơn trang bán vé).
  - Lấy giá trị mới nhất.

> Ví dụ ghép cả 3 bước với chuyến A2-53:
> - **Schema alignment** biết `SDT` (Airline2) và `DT` (Airfare4) cùng là giờ khởi hành dự kiến.
> - **Record linkage** biết r65 và r33 cùng là một chuyến bay.
> - **Data fusion** chọn 16:00 (của hãng) thay vì 15:30 (của trang vé lỗi thời).

### Slide 33: More challenges with Big Data: 4V
| V | Thách thức với tích hợp |
|---|---|
| **Volume** (khối lượng) | Số lượng nguồn và dữ liệu khổng lồ, so sánh cặp bản ghi càng không khả thi |
| **Variety** (đa dạng) | Nhiều định dạng: bảng, JSON, XML, text, ảnh; schema khác nhau nhiều |
| **Velocity** (tốc độ) | Dữ liệu sinh liên tục (streaming), nguồn thay đổi nhanh, phải tích hợp gần real-time |
| **Veracity** (độ tin cậy) | Nguồn có chất lượng khác nhau, nhiều dữ liệu sai hoặc mâu thuẫn |

### Slide 34: Implementation challenges (thách thức khi triển khai dự án)
- **Time, effort, and cost**: tốn thời gian, công sức, chi phí (ETL thường chiếm phần lớn dự án DW).
- **Technology variance**: công nghệ đa dạng, thay đổi nhanh.
- **Cross-divisional collaboration**: cần phối hợp nhiều phòng ban (mỗi phòng sở hữu dữ liệu riêng, định nghĩa khác nhau).
- **Business requirement change**: yêu cầu kinh doanh thay đổi, phải sửa ETL và DW.

### Slide 35: Data source evolution (nguồn dữ liệu thay đổi theo thời gian)
Ví dụ hệ thống giao hàng (kiểu GrabFood, ShopeeFood): ban đầu chỉ có order, store, product, customer. Sau đó phát sinh thêm:
- **Shipping**: tài xế nội bộ hay thuê ngoài, nên có thêm bảng và thuộc tính mới.
- **E-voucher**: nghiệp vụ khuyến mãi mới.
- **Store open/close and boundary**: cửa hàng mở/đóng, thay đổi vùng phục vụ (ảnh hưởng SCD).

> Nguồn thay đổi thì ETL và DW **phải tiến hóa theo**. Đây là thách thức bảo trì liên tục.

### Slide 36: Maintenance and management
DW cần được quản trị **như một DBA** quản trị CSDL (sao lưu, tối ưu, phân quyền) và cần một **đội quản lý** riêng.

### Slide 37–38: Data warehouse monitoring (giám sát DW, theo Inmon)
Giám sát để biết:
- **Tăng trưởng**: dữ liệu tăng **cái gì, ở đâu, tốc độ bao nhiêu**.
- **Sử dụng**: dữ liệu nào được dùng, **ai** dùng, **khi nào**, **bao nhiêu phần** của kho được dùng, mức độ sử dụng.
- **Hiệu năng**: thời gian phản hồi người dùng nhận được.
- **Chi tiết từng truy vấn**: dữ liệu nào được truy cập, khi nào, bởi ai, tần suất, ở **mức chi tiết** nào; thời gian phản hồi; giờ nào trong ngày; kích thước truy vấn; truy vấn **bị hủy** hay kết thúc bình thường.

> **Mục đích:** phát hiện dữ liệu "chết" không ai dùng (có thể lưu trữ ra ngoài), tạo bảng tổng hợp (summary table) cho truy vấn hay dùng, lập kế hoạch dung lượng, tối ưu index.

### Slide 39: Data profile (hồ sơ dữ liệu của DW)
Cần duy trì:
- Danh mục (catalog) **tất cả các bảng** trong kho.
- Hồ sơ **nội dung** các bảng (số dòng, phân phối giá trị, NULL...).
- Hồ sơ **tăng trưởng** của các bảng.
- Danh mục **index** có sẵn để truy cập bảng.
- Danh mục **bảng tổng hợp (summary tables)** và **nguồn** của chúng (lineage).

---

## PHẦN 3: DATA QUALITY (slide 40–49)

### Slide 41: What do real-world numbers say? (bài tính chi phí dữ liệu kém, CÓ THỂ RA BÀI TÍNH)
Ví dụ một công ty bảo hiểm (báo cáo TDWI 2002):
- 2 triệu hồ sơ bồi thường (claim) mỗi tháng, mỗi claim có 377 trường dữ liệu.
- Tổng số trường mỗi tháng = 2.000.000 × 377 = 754.000.000.
- Tỷ lệ lỗi 1/1000, nên số lỗi mỗi tháng = 754.000.000 / 1000 = **754.000 lỗi/tháng**, tức **9,048 triệu lỗi/năm** (× 12).
- 10% là trường quan trọng, nên khoảng **904.800, xấp xỉ 1 triệu lỗi/năm** cần sửa.
- Mỗi lỗi tốn **$10** (thời gian nhân viên, chi trả sai, mất lòng tin khách hàng), nên khoảng **$10 triệu/năm**.

> Ý nghĩa: tỷ lệ lỗi tưởng nhỏ (0,1%) nhưng nhân với quy mô lớn thì **thiệt hại khổng lồ**.

### Slide 42: Problems due to poor data quality (hậu quả của dữ liệu kém, khảo sát TDWI)
| Hậu quả | % doanh nghiệp gặp |
|---|---|
| Tốn thêm thời gian đối chiếu dữ liệu (reconcile) | **87%** (cao nhất) |
| Mất uy tín, độ tin cậy của hệ thống | 81% |
| Chi phí phát sinh (ví dụ gửi thư trùng lặp) | 72% |
| Khách hàng không hài lòng | 67% |
| Chậm triển khai hệ thống mới | 64% |
| Mất doanh thu | 54% |
| Vấn đề tuân thủ pháp lý (compliance) | 38% |

### Slide 43: Reasons for bad quality data (nguyên nhân dữ liệu kém)
| Nguyên nhân | Giải thích ví dụ |
|---|---|
| **Historical changes** | Ngày sinh khách hàng của công ty bảo hiểm: trước đây không bắt buộc hoặc nhập theo định dạng cũ, sau này mới quan trọng thì dữ liệu cũ thiếu/sai |
| **Data usage** | Dữ liệu được thu thập cho mục đích A nhưng dùng cho mục đích B. Ví dụ nghề nghiệp của khách mua cổ phiếu ở ngân hàng: nhân viên nhập qua loa vì không quan trọng với giao dịch, nhưng lại quan trọng với phân tích |
| **Company mergers** | Ngân hàng sáp nhập với công ty bảo hiểm thành tập đoàn: hai hệ thống, hai cách mã hóa, khách hàng trùng nhau |
| **Dormant data** (dữ liệu "ngủ", lỗi thời) | Khoảng 10% dân số chuyển địa chỉ mỗi năm, nên dữ liệu địa chỉ tự "hỏng" dần theo thời gian dù lúc nhập là đúng |
| **Data enrichment** | Làm giàu dữ liệu nội bộ bằng nguồn bên ngoài (ví dụ dữ liệu từ ứng dụng B2B của nhà cung cấp phần cứng): nguồn ngoài có chất lượng không kiểm soát được |

### Slide 44: Sources of data quality problems (nguồn gốc lỗi, khảo sát TDWI)
| Nguồn gốc | % |
|---|---|
| **Nhân viên nhập liệu** (data entry by employees) | **76%** (lớn nhất) |
| Thay đổi hệ thống nguồn | 53% |
| Dự án chuyển đổi/di chuyển dữ liệu (migration) | 48% |
| Kỳ vọng khác nhau của người dùng | 46% |
| Dữ liệu bên ngoài | 34% |
| Lỗi hệ thống | 26% |
| Khách hàng tự nhập | 25% |

> Ý chính: **con người** là nguồn lỗi lớn nhất. Giải pháp là validate ngay khi nhập (dropdown thay vì gõ tự do, kiểm tra định dạng), đào tạo, quy trình.

### Slide 45: How to manage data quality? Data quality methodology (TDWI)
Quy trình 9 bước:
1. **Launch a DQ program**: khởi động chương trình chất lượng dữ liệu (cần lãnh đạo ủng hộ).
2. **Develop a project plan** + **Build a team**: lập kế hoạch và xây dựng đội ngũ (song song).
3. **Review business processes** + **Review data architecture**: xem xét quy trình nghiệp vụ và kiến trúc dữ liệu hiện tại (song song).
4. **Assess data quality** (bước được **khoanh đỏ**, trọng tâm): đánh giá chất lượng hiện tại bằng **data profiling**, đo theo các dimension.
5. **Clean data** + **Monitor data**: làm sạch dữ liệu và giám sát liên tục (song song).
6. **Improve business processes**: cải tiến quy trình để **ngăn lỗi tận gốc** (không chỉ sửa ngọn).

> Chất lượng dữ liệu là một **chương trình liên tục**, không phải dự án làm một lần.

### Slide 46: Data quality dimensions (1/3): 6 chiều chính của DAMA UK (HỌC THUỘC ĐỊNH NGHĨA)
| Dimension | Định nghĩa | Ví dụ lỗi |
|---|---|---|
| **Completeness** (đầy đủ) | Tỷ lệ dữ liệu được lưu so với mức "100% đầy đủ" | 30% khách hàng không có email |
| **Uniqueness** (duy nhất) | Không có đối tượng nào được ghi nhận **hơn một lần** | Một khách hàng có 2 bản ghi |
| **Timeliness** (kịp thời) | Mức độ dữ liệu phản ánh thực tế **tại thời điểm cần** | Giá vé Airfare4 dùng lịch bay cũ |
| **Validity** (hợp lệ) | Dữ liệu tuân theo **cú pháp** (định dạng, kiểu, miền giá trị) của định nghĩa | Ngày `2026-13-45`, email không có `@`, tuổi = −5 |
| **Accuracy** (chính xác) | Mức độ dữ liệu mô tả **đúng** đối tượng/sự kiện ngoài đời thực | Địa chỉ hợp lệ về định dạng nhưng **không phải** địa chỉ thật của khách |
| **Consistency** (nhất quán) | Không có **khác biệt** khi so sánh hai hay nhiều biểu diễn của cùng một thứ | Hệ CRM ghi khách ở HN, hệ Billing ghi ở HCM |

> **Phân biệt Validity và Accuracy (hay hỏi):** Validity chỉ kiểm tra **đúng định dạng/quy tắc** (số điện thoại có 10 chữ số), còn Accuracy kiểm tra **đúng thực tế** (đó có phải số của khách không). Dữ liệu có thể hợp lệ nhưng không chính xác.

### Slide 47: Data quality dimensions (2/3): theo Batini & Pernici (2006)
Ngoài các chiều trên, bổ sung:
- **Accuracy**: hệ thống biểu diễn trạng thái thế giới thực **khác** với trạng thái đáng lẽ phải biểu diễn.
- **Timeliness**: **độ trễ** giữa lúc thế giới thực thay đổi và lúc hệ thống được cập nhật.
- **Completeness**: khả năng biểu diễn **mọi trạng thái có ý nghĩa** của thế giới thực.
- **Consistency**: không nhất quán nghĩa là ánh xạ biểu diễn là **một-nhiều** (một thực thể có nhiều biểu diễn mâu thuẫn).
- **Interpretability** (diễn giải được): có **tài liệu và metadata** để hiểu đúng ý nghĩa dữ liệu.
- **Accessibility** (truy cập được): người dùng truy cập được dữ liệu phù hợp với văn hóa, thể trạng, công nghệ của họ.
- **Usability** (dễ sử dụng): hiệu quả, hiệu suất, sự hài lòng khi người dùng sử dụng dữ liệu.
- **Trustworthiness** (đáng tin cậy): tổ chức cung cấp dữ liệu có đáng tin không.

### Slide 48: Data quality dimensions (3/3): theo O'Brien & Marakas (2010), 3 nhóm chất lượng thông tin
| **Time** (thời gian) | **Content** (nội dung) | **Form** (hình thức) |
|---|---|---|
| Timeliness: có khi cần | Accuracy: không lỗi | Clarity: dễ hiểu |
| Currency: cập nhật mới | Relevance: liên quan nhu cầu | Detail: mức chi tiết phù hợp |
| Frequency: tần suất đủ | Completeness: đủ thông tin cần | Order: sắp xếp hợp lý |
| Time period: đúng giai đoạn (quá khứ, hiện tại, tương lai) | Conciseness: súc tích, chỉ cái cần | Presentation: dạng trình bày (văn bản, bảng, đồ thị) |
| | Scope: phạm vi phù hợp | Media: phương tiện (in, video, màn hình) |
| | Performance: phản ánh được hiệu suất | |

### Slide 49: Information management
Quản lý thông tin phải bao quát mọi khía cạnh, cũng là **vòng đời của dữ liệu**:
**Thu thập (collection) → Tổ chức (organization) → Lưu trữ (storage) → Thao tác (manipulation) → Xử lý (processing) → Bảo vệ (protection)**.
Lỗi chất lượng có thể phát sinh ở **bất kỳ khâu nào**.

---

## PHẦN 4: SSIS DEMO (slide 50–59)

### Slide 51: Yêu cầu cài đặt
SQL Server (bản Developer miễn phí), Visual Studio, SQL Server Data Tools (SSDT), extension **SQL Server Integration Services Projects**, và PostgreSQL ODBC driver (để đọc dữ liệu từ PostgreSQL, minh họa nguồn không đồng nhất).

### Slide 52–56: Demo di chuyển dữ liệu RetailSales
**Bài toán:**
- **Input:** 2 file CSV `SalesDetails_NorthAmerica.csv` và `SalesDetails_Others.csv` với các cột `SalesOrderID, ProductID, OrderQty, UnitPrice, TerritoryID`.
- **Calculated data (Derived Column trong SSIS):**
  - `Total quantity` (số lượng).
  - `Tax Amount = 8% × UnitPrice`.
  - `Total Sales = Sales + Tax Amount`, trong đó `Sales = OrderQty × UnitPrice`.
- **Lookup data (Lookup transformation):** tra CSDL `CompanyX` để lấy **product name** (từ ProductID) và **territory name** (từ TerritoryID).
- **Output:** bảng `Sales_NorthAmerica` và `Sales_Others` trong CSDL CompanyX.

**Kiểm tra bằng dòng đầu tiên của kết quả (slide 55):** ProductID 808, quantity 2, UnitPrice 24,2945:
- TaxAmt = 0,08 × 24,2945 = **1,94356** ✓
- TotalSales = 2 × 24,2945 + 1,94356 = 48,589 + 1,94356 = **50,5326** ✓
- productName = "LL Mountain Handlebars", territoryName = "Canada" (lấy qua lookup).

**Luồng SSIS điển hình (Data Flow Task):**
```
Flat File Source (CSV)  →  Data Conversion (đổi kiểu)  →  Derived Column (tính TaxAmt, TotalSales)
   →  Lookup (ProductName)  →  Lookup (TerritoryName)  →  OLE DB Destination (bảng đích)
```
Hai file nguồn có thể đi qua hai luồng riêng (hoặc dùng Conditional Split / Union All).

### Slide 57: ETL Upsert bằng MERGE (HAY THI, CẦN HIỂU CÂU LỆNH)
**Upsert = Update + Insert:** có rồi thì cập nhật, chưa có thì thêm mới.

```sql
MERGE dbo.FactBuyingHabits AS Target                       -- bảng đích
USING (SELECT CustomerID, ProductID, PurchaseDate
       FROM dbo.Purchases) AS Source                       -- dữ liệu nguồn
ON (Target.ProductID = Source.ProductID
    AND Target.CustomerID = Source.CustomerID)             -- điều kiện so khớp
WHEN MATCHED THEN                                          -- đã có cặp (khách, sản phẩm)
    UPDATE SET Target.LastPurchaseDate = Source.PurchaseDate
WHEN NOT MATCHED BY TARGET THEN                            -- cặp mới, chưa có trong đích
    INSERT (CustomerID, ProductID, LastPurchaseDate)
    VALUES (Source.CustomerID, Source.ProductID, Source.PurchaseDate)
OUTPUT $action, Inserted.*, Deleted.*;                     -- in ra hành động đã làm (INSERT/UPDATE)
```
- Bảng `FactBuyingHabits` lưu **lần mua gần nhất** của mỗi khách với mỗi sản phẩm.
- `$action` cho biết mỗi dòng được INSERT hay UPDATE; `Deleted.*` là giá trị **cũ**, `Inserted.*` là giá trị **mới**.
- Có thể thêm `WHEN NOT MATCHED BY SOURCE THEN DELETE` để xóa những dòng không còn trong nguồn.
- **Liên hệ SCD:** `WHEN MATCHED THEN UPDATE` chính là **SCD Type 1** (ghi đè).

### Slide 58: ETL Upsert load demo + Incremental by date time
1. `SELECT * INTO dbo.ProductClone FROM Production.Product;` tạo bảng **bản sao** từ bảng Product của AdventureWorks để làm bảng đích.
2. **Upsert:** sửa dữ liệu nguồn (ví dụ sản phẩm 1 có `Color = Red` được tô vàng) và thêm sản phẩm mới, rồi chạy MERGE. Dòng cũ được **update** màu, dòng mới được **insert**.
3. **Incremental by date time:** cột `ModifiedDate` của dòng vừa sửa là `2023-02-09`, mới hơn các dòng khác (`2014-02-08`). ETL chỉ lấy những dòng có `ModifiedDate > thời điểm nạp lần trước`:

```sql
DECLARE @LastLoad DATETIME = (SELECT MAX(LoadTime) FROM etl.LoadLog WHERE TableName = 'Product');

SELECT * FROM Production.Product
WHERE ModifiedDate > @LastLoad;      -- chỉ lấy bản ghi mới/thay đổi
```
Đây là cách refresh **"by timestamp"** ở slide 7.

### Slide 59: ETL Scheduler
Dùng **SQL Server Agent → Jobs** để lập lịch chạy gói SSIS tự động:
- Schedule type: **Recurring** (lặp lại).
- Frequency: **Daily**, chạy **mỗi 15 phút** từ 00:00 đến 23:59:59.
- Có ngày bắt đầu và kết thúc.

> ETL trong thực tế phải chạy **tự động theo lịch** (hằng đêm, hằng giờ, hoặc vài phút một lần), không ai bấm tay. Ngoài SQL Server Agent còn có Apache Airflow, cron, Azure Data Factory triggers.

---

## TÓM TẮT 1 TRANG

1. **ETL** là Extract → Transform → Load; **ELT** là load trước, transform trong đích. Chọn cái nào tùy hạ tầng, khối lượng dữ liệu, yêu cầu bảo mật.
2. **Refresh** có 5 cách: snapshot, application code, log/audit (CDC), timestamp, user-defined.
3. **Các vấn đề tích hợp:** nguồn không đồng nhất, data mapping, data conflicts, redundancy, entity resolution, vi phạm ràng buộc, chất lượng dữ liệu, khác giao diện truyền thông.
4. **Dạng heterogeneity:** data model, syntactic, structural, semantic.
5. **3 thách thức chính:** semantic ambiguity, instance representation ambiguity, data inconsistency.
6. **Kiến trúc tích hợp 3 bước:** Schema alignment → Record linkage → Data fusion (mỗi bước giải quyết một thách thức ở trên).
7. **Big data:** Volume, Variety, Velocity, Veracity.
8. **Triển khai:** chi phí, công nghệ, phối hợp phòng ban, yêu cầu thay đổi, nguồn tiến hóa; cần monitoring và data profile.
9. **Data quality:**
   - Chi phí lỗi rất lớn (ví dụ $10 triệu/năm).
   - Nguồn lỗi lớn nhất là nhân viên nhập liệu (76%).
   - Quy trình quản lý xoay quanh **Assess data quality**.
   - **6 dimensions:** Completeness, Uniqueness, Timeliness, Validity, Accuracy, Consistency.
10. **SSIS:** Flat file source → Derived column → Lookup → Destination; **MERGE** cho upsert; **ModifiedDate** cho incremental; **SQL Server Agent** để lập lịch.

---

## CÂU HỎI THI CÓ THỂ RA TỪ BÀI NÀY

1. Compare ETL and ELT. In which situations would you choose each?
2. List and explain the methods to refresh a data warehouse.
3. List the data integration issues with examples.
4. Explain semantic ambiguity, instance representation ambiguity, and data inconsistency using the airline example.
5. Describe the three steps of the data integration architecture (schema alignment, record linkage, data fusion) and their challenges.
6. Given two schemas (like S1.Cust and S2.Customer), identify the mapping and integration problems.
7. Explain the six data quality dimensions; distinguish validity from accuracy.
8. Calculate the cost of poor data quality given a scenario (like slide 41).
9. Explain the MERGE statement and what each clause does; relate it to SCD Type 1.
10. Explain incremental loading by timestamp and its limitation (cannot detect deleted rows).

**Gợi ý cho câu 8 (dạng biến thể):** Một ngân hàng có 500.000 giao dịch/tháng, mỗi giao dịch 40 trường, tỷ lệ lỗi 0,5%, 20% lỗi là quan trọng, chi phí $5/lỗi. Tính chi phí mỗi năm.
- Số lỗi mỗi tháng = 500.000 × 40 × 0,005 = 100.000.
- Mỗi năm = 1.200.000 lỗi; lỗi quan trọng = 240.000.
- Chi phí = 240.000 × $5 = **$1,2 triệu/năm**.
