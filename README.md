# BÀI KIỂM TRA SỐ 02 - HỆ QUẢN TRỊ CSDL SQL  
## 1. Thông tin sinh viên
|  Thông tin | Nội dung chi tiết |
| :--- | :--- |
| **Họ và tên** | Trần Hoàng Xuân Vũ |
| **Mã số sinh viên** | K235480106080 |
| **Lớp** | K59KMT.K01 - Kỹ thuật Máy tính |
| **Môn học** | Hệ Quản Trị Cơ Sở Dữ Liệu (SQL Server) |
| **Khoa** | Điện tử |
| **Trường** | Đại học Kỹ thuật Công nghiệp Thái Nguyên (TNUT) |
## 2. Yêu cầu của đề bài 
### Đề tài: Hệ thống Quản lý Thủy cung (Oceanarium Management)
- Thực hiện xây dựng một hệ thống quản lý thủy cung hoàn chỉnh trên SQL Server, đáp ứng đầy đủ các yêu cầu chuyên sâu về thiết kế cấu trúc dữ liệu và quản lý vận hành hệ sinh thái biển.
- Toàn bộ quá trình thực hiện được ghi lại bằng các screenshot minh họa chi tiết. Mỗi hình ảnh đi kèm với các câu lệnh SQL tương ứng, giải thích rõ ràng về chức năng, mục đích xử lý (như thiết lập các ràng buộc toàn vẹn, tối ưu hóa kiểu dữ liệu) cũng như kết quả thực thi đạt được trên môi trường SQL Server.
### Bài tập được nộp dưới dưới dạng GitHub Repository(Public) gồm 2 file:
- **README.md**: Chứa nội dung bài làm, các đoạn code và các ảnh chụp minh họa (dùng cú pháp Markdown để chèn ảnh).

- **baikiemtra2.sql**: File script chứa toàn bộ mã nguồn SQL đã viết.
## 3. Giới thiệu về đề tài hệ thống quản lý Thủy cung (Oceanarium Management)
Xây dựng hệ thống Quản lý Thủy cung (QuanLyThuyCung) trên nền tảng SQL Server nhằm mục đích số hóa quy trình theo dõi hệ sinh thái biển, bao gồm các chức năng cốt lõi như quản lý môi trường sống, giám sát cá thể sinh vật và lịch trình vận hành kỹ thuật.

Mỗi bể nuôi được lưu trữ thông tin về môi trường vật lý như thể tích khối, loại nguồn nước và các chỉ số nhiệt độ duy trì tối ưu.

Mỗi sinh vật lưu hồ sơ về loài, ngày nhập viện, tình trạng sức khỏe hiện tại và giá trị bảo hiểm định giá cho từng cá thể.

Bảng nhật ký chăm sóc thể hiện mối quan hệ giữa sinh vật và lịch trình dinh dưỡng, ghi nhận chi tiết khối lượng thực phẩm cũng như chi phí vận hành thực tế.

Toàn bộ bài làm được chia thành các phần trọng tâm:

- Thiết kế cơ sở dữ liệu: Khởi tạo các bảng `beNuoi`, `sinhVat`, `lichChoAn` đi kèm các ràng buộc toàn vẹn Primary Key, Foreign Key và Check Constraint.

- Xây dựng Function: Thiết lập các hàm tính toán tổng chi phí thức ăn hàng tháng và thống kê số lượng sinh vật theo từng loại môi trường nước.

- Xây dựng Stored Procedure: Xử lý quy trình chuyển bể cho sinh vật và tự động báo cáo danh sách các cá thể cần kiểm tra sức khỏe định kỳ.

- Xây dựng Trigger: Tự động cập nhật tình trạng bể nuôi và kiểm soát lịch trình cho ăn để tránh trùng lặp thời gian.

- Tối ưu hóa Truy vấn: Sử dụng Cursor để duyệt danh sách kiểm kê và so sánh hiệu năng với phương pháp Set-based để tối ưu hóa tốc độ xử lý dữ liệu lớn.

Cơ sở dữ liệu được đặt tên tuân thủ nghiêm ngặt yêu cầu: **QuanLyThuyCung_K235480106080**

Mỗi phân đoạn mã SQL đều được minh chứng bằng các screenshot trực quan, đi kèm kết quả thực thi và phần chú giải chi tiết, đảm bảo tính minh bạch, logic và đáp ứng đầy đủ tiêu chuẩn của môn học.
## Phần 1: Thiết kế và khởi tạo cấu trúc dữ liệu 
### 1.1 Khởi tạo Database 
Bước đầu tiên, chúng ta khai báo lệnh `CREATE DATABASE` để thiết lập một cơ sở dữ liệu riêng biệt trên máy chủ. Việc kết hợp thêm lệnh `USE` ngay sau đó giúp trình biên dịch tập trung thực thi vào đúng mục tiêu, đảm bảo các cấu trúc bảng sau này không bị ghi đè lên hệ thống mặc định.
``` SQL 
CREATE DATABASE [QuanLyThuyCung_K235480106080];
GO
USE [QuanLyThuyCung_K235480106080];
GO
```

<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/c64f3e10-ae04-4b35-9983-a58bb0b353b9" />

### 1.2 Thiết lập bảng dữ liệu 

Sau đây là mã nguồn chi tiết cho 3 bảng, em sẽ giải thích cụ thể về Khóa chính (PK), Khóa ngoại (FK) và Ràng buộc kiểm tra (CK) trong ảnh code 

### a. Khởi tạo bảng [beNuoi] (Bảng Cha)
<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/2f6e82d2-574f-47fb-b23b-8e34c1aa4ce5" />

### b. Khởi tạo bảng [sinhVat] (Bảng Con của beNuoi)
<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/bbd73036-54af-4ede-9d56-3d7a7285e9f7" />

### c. Khởi tạo bảng [lichChoAn] (Bảng Con của sinhVat)
<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/28023565-8500-45a6-9e73-b11c68620592" />

### d. Ảnh sau khi đã tạo các 3 bảng a,b,c sẽ hiển thị 
<img width="803" height="1799" alt="image" src="https://github.com/user-attachments/assets/e35243f7-d654-4991-be3d-8773a0577052" />

### 1.3 Chèn dữ liệu mẫu vào bảng 
Sau khi thiết lập cấu trúc, hệ thống tiến hành nạp dữ liệu thử nghiệm bằng lệnh `INSERT INTO`. Việc này giúp xác nhận các mối quan hệ giữa các bảng và đảm bảo các ràng buộc CHECK hoạt động đúng như thiết kế.
<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/7453056f-a741-440f-b08d-adb45c240e62" />

## Phần 2: Xây dựng Function
### 2.1. Tìm hiểu về các Built-in Function (Hàm có sẵn)
### Trong SQL Server,các hàm Built-in được chia thành nhiều nhóm chính dựa trên kiểu dữ liệu mà chúng xử lý:

- Hàm Chuỗi (String Functions): Xử lý văn bản như `LEN`, `UPPER`, `LOWER`, `REPLACE`.

- Hàm Ngày tháng (Date and Time Functions): Làm việc với thời gian như `GETDATE`, `DATEDIFF`, `DATEADD`.

- Hàm Toán học (Mathematical Functions): Tính toán số học như `ABS`, `ROUND`, `SQRT`.

- Hàm Chuyển đổi (Conversion Functions): Chuyển đổi kiểu dữ liệu như `CAST`, `CONVERT`.

Hàm Tổng hợp (Aggregate Functions): Tính toán trên tập dữ liệu như `SUM`, `AVG`, `COUNT`.
### Các System Function đặc sắc theo góc nhìn cá nhân của em:
Thay vì những hàm quá cơ bản, em chọn ra 3 hàm mà em biết để đưa vào bài:

- Hàm `COALESCE()`: Trả về giá trị đầu tiên không NULL trong danh sách. Đây là "vị cứu tinh" khi báo cáo dữ liệu bị trống.

- Hàm `DATEDIFF()`: Tính khoảng cách giữa hai mốc thời gian. Rất quan trọng trong việc quản lý vòng đời sinh vật.

- Hàm `FORMAT()`: Định dạng dữ liệu theo chuẩn địa phương (như tiền tệ VNĐ). Hàm này giúp dữ liệu xuất ra trông rất đẹp mắt.
                                          
<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/044fee1f-8485-4cb3-a6c9-fc1cdd08c84e" />

Em sử dụng hàm `FORMAT` để chuyên nghiệp hóa cách hiển thị tiền tệ, hàm `COALESCE` để xử lý dữ liệu rác/trống, và cặp hàm `DATEDIFF/GETDATE` để tính toán thời gian vận hành thực tế của thủy cung. Điều này giúp hệ thống không chỉ lưu trữ mà còn cung cấp thông tin có ý nghĩa quản trị cao.

### 2.2. Tìm hiểu về User-Defined Functions (UDF)
### a. Mục đích của hàm tự viết
**Hàm tự viết (UDF)** được xây dựng để đóng gói các **logic nghiệp vụ đặc thù** của dự án mà các hàm hệ thống không hỗ trợ sẵn. Việc sử dụng UDF giúp:
*   **Tái sử dụng mã nguồn:** Tránh lặp lại các đoạn code logic ở nhiều nơi.
*   **Giảm độ phức tạp:** Làm gọn các câu lệnh truy vấn SQL.
*   **Tăng tính nhất quán:** Đảm bảo dữ liệu được xử lý theo một chuẩn duy nhất trên toàn hệ thống.

### b. Tại sao cần tự viết hàm khi đã có nhiều hàm hệ thống?
Mặc dù SQL Server cung cấp hàng trăm hàm có sẵn (toán học, chuỗi, thời gian), nhưng chúng thường mang tính tổng quát. Trong thực tế quản lý, chúng ta cần các phép tính riêng biệt như:
*   Tính thuế phí theo quy định riêng của tổ chức.
*   Phân loại khách hàng dựa trên các thang điểm doanh số tùy chỉnh.
*   **Đặc thù trong đề tài này:** Tính toán định mức lượng thức ăn dựa trên các chỉ số sinh trưởng riêng của từng loài cá.

Hàm tự viết giúp **"địa phương hóa"** các công thức tính toán phức tạp này thành những câu lệnh đơn giản.

### c. Phân loại và thời điểm sử dụng

| Loại hàm | Đặc điểm | Khi nào nên dùng? |
| :--- | :--- | :--- |
| **Scalar Function** (Hàm vô hướng) | Trả về một **giá trị duy nhất** (số, chuỗi, ngày). | Dùng trong danh sách `SELECT` hoặc mệnh đề `WHERE` để tính toán giá trị cho từng dòng. |
| **Inline Table-Valued Function** (Hàm bảng dòng đơn) | Trả về một **tập kết quả (bảng)** từ một lệnh `SELECT` duy nhất. | Dùng khi cần lọc dữ liệu phức tạp trên nhiều bảng mà vẫn muốn đạt **hiệu năng cao** như một View có tham số. |
| **Multi-statement Table-Valued Function** (Hàm bảng nhiều câu lệnh) | Trả về một bảng nhưng có **logic xử lý phức tạp** (`IF-ELSE`, vòng lặp) bên trong. | Dùng khi cần xử lý, tính toán hoặc tổng hợp dữ liệu qua **nhiều bước trung gian** trước khi trả ra kết quả cuối cùng. |

Phần tiếp theo trong phần thực hành là xây dựng Scalar Function. Em sẽ đưa ra một logic rất thực tế cho Thủy cung: Tính toán tổng chi phí bảo trì (thức ăn) cho một sinh vật cụ thể.
### 2.3. Xây dựng Scalar Function (Hàm vô hướng)
- Logic nghiệp vụ: Trong quản lý Thủy cung, việc theo dõi xem một cá thể sinh vật đã tiêu tốn bao nhiêu chi phí thức ăn từ trước đến nay là rất quan trọng để đánh giá ngân sách vận hành. Thay vì viết lệnh `SUM` phức tạp mỗi khi cần xem, ta sẽ đóng gói nó vào một hàm.

- Yêu cầu của hàm: Nhận đầu vào là `maSinhVat`, thực hiện tính tổng cột `chiPhiBuaAn` trong bảng `lichChoAn` và trả về một con số tổng duy nhất.
### Script khởi tạo hàm:
```sql
-- Tạo hàm tính tổng chi phí thức ăn của một sinh vật cụ thể
CREATE FUNCTION fn_TinhTongChiPhiSinhVat (@idSV INT)
RETURNS MONEY
AS
BEGIN
    DECLARE @tongTien MONEY;

    -- Tính tổng chi phí từ bảng lichChoAn dựa theo mã sinh vật truyền vào
    SELECT @tongTien = SUM([chiPhiBuaAn]) 
    FROM [lichChoAn] 
    WHERE [maSinhVat] = @idSV;

    -- Nếu sinh vật chưa có dữ liệu cho ăn (NULL), trả về 0
    RETURN ISNULL(@tongTien, 0);
END;
GO
```
### Câu lệnh SQL khai thác hàm:
Sau khi tạo hàm thành công, ta sử dụng hàm này trực tiếp trong câu lệnh SELECT để xem báo cáo tổng hợp.
```sql
-- Sử dụng hàm để hiển thị danh sách sinh vật kèm tổng chi phí thức ăn của từng con
SELECT 
    [maSinhVat], 
    [tenLoai], 
    [tinhTrangSucKhoe],
    dbo.fn_TinhTongChiPhiSinhVat([maSinhVat]) AS [TongChiPhiThucAn]
FROM [sinhVat];
```

<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/0030bb1e-230a-425f-8192-3c41964cbd29" />

### 2.4. Xây dựng Inline Table-Valued Function
- Logic nghiệp vụ: Trong thủy cung, việc kiểm tra xem bể nào đang nuôi loài sinh vật nào và tình trạng ra sao là công việc thường nhật. Thay vì viết lệnh `JOIN` nhiều lần, ta sẽ tạo một hàm cho phép lọc nhanh các sinh vật theo Loại môi trường (ví dụ: Nước mặn, Nước ngọt).

- Yêu cầu của hàm: Nhận vào tham số là `@loaiMT` `NVARCHAR`, trả về một bảng gồm danh sách các sinh vật, tên loài và tên bể tương ứng thuộc môi trường đó.

### Script khởi tạo hàm:
```sql
-- Tạo hàm lấy danh sách sinh vật theo loại môi trường nước
CREATE FUNCTION fn_DanhSachSinhVatTheoMoiTruong (@loaiMT NVARCHAR(50))
RETURNS TABLE
AS
RETURN 
(
    -- Câu lệnh SELECT duy nhất trả về kết quả dưới dạng bảng
    SELECT 
        S.[maSinhVat], 
        S.[tenLoai], 
        B.[tenBe], 
        B.[loaiMoiTruong],
        S.[tinhTrangSucKhoe]
    FROM [sinhVat] S
    INNER JOIN [beNuoi] B ON S.[maBe] = B.[maBe]
    WHERE B.[loaiMoiTruong] = @loaiMT
);
GO
```

### Câu lệnh SQL khai thác hàm:
Vì kết quả trả về là một bảng, nên em sẽ sử dụng hàm này ở vị trí của một bảng sau từ khóa `FROM`.
```sql
-- Khai thác hàm để xem tất cả sinh vật sống trong môi trường 'Nước mặn'
SELECT * FROM dbo.fn_DanhSachSinhVatTheoMoiTruong(N'Nước mặn');

-- Bạn cũng có thể lọc thêm từ kết quả của hàm
SELECT [tenLoai], [tenBe] 
FROM dbo.fn_DanhSachSinhVatTheoMoiTruong(N'Nước ngọt')
WHERE [tinhTrangSucKhoe] = N'Khỏe mạnh';
```

<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/5e7802d1-85d8-4090-aa7b-27e838d118c3" />


### 2.5. Viết 01 Multi-statement Table-Valued Function

Để hoàn tất phần Function với loại phức tạp nhất là Multi-statement Table-Valued Function (mTVF), em sẽ xây dựng một hàm giúp quản lý đưa ra quyết định dựa trên tình trạng sức khỏe và chi phí.

Hàm này khác biệt ở chỗ nó có một cấu trúc bảng tạm được định nghĩa trước bằng `DECLARE @... TABLE`, cho phép em thực hiện nhiều câu lệnh bên trong thân hàm.


Yêu cầu của hàm: Thống kê danh sách sinh vật kèm theo cột "Phân loại mức độ chăm sóc".

 - Nếu sinh vật có tình trạng 'Yếu', ghi chú là 'Cần chế độ đặc biệt'.

 - Nếu chi phí cho ăn cao (> 400), ghi chú là 'Cần tối ưu chi phí'.

 - Còn lại là 'Chăm sóc bình thường'.

### Script khởi tạo hàm:
```sql
-- Tạo hàm đa câu lệnh để phân loại mức độ ưu tiên chăm sóc
CREATE FUNCTION fn_PhanLoaiUuTienChamSoc()
RETURNS @BangKetQua TABLE (
    [MaSV] INT,
    [TenLoai] NVARCHAR(200),
    [TinhTrang] NVARCHAR(100),
    [MucDoUuTien] NVARCHAR(100)
)
AS
BEGIN
    -- Bước 1: Đổ dữ liệu cơ bản vào bảng tạm @BangKetQua
    INSERT INTO @BangKetQua ([MaSV], [TenLoai], [TinhTrang], [MucDoUuTien])
    SELECT 
        s.[maSinhVat], 
        s.[tenLoai], 
        s.[tinhTrangSucKhoe],
        N'Chưa xác định' -- Giá trị tạm thời
    FROM [sinhVat] s;

    -- Bước 2: Cập nhật logic phân loại phức tạp dựa trên tình trạng sức khỏe
    UPDATE @BangKetQua
    SET [MucDoUuTien] = N'Cần chế độ đặc biệt'
    WHERE [TinhTrang] = N'Yếu' OR [TinhTrang] = N'Đang theo dõi';

    -- Bước 3: Cập nhật logic dựa trên chi phí (sử dụng Scalar Function đã viết ở phần trước)
    UPDATE @BangKetQua
    SET [MucDoUuTien] = N'Cần tối ưu chi phí'
    WHERE dbo.fn_TinhTongChiPhiSinhVat([MaSV]) > 400 
      AND [MucDoUuTien] = N'Chưa xác định';

    -- Bước 4: Những trường hợp còn lại
    UPDATE @BangKetQua
    SET [MucDoUuTien] = N'Chăm sóc bình thường'
    WHERE [MucDoUuTien] = N'Chưa xác định';

    RETURN;
END;
GO
```

### Câu lệnh SQL khai thác hàm:
```sql
-- Xem báo cáo phân loại ưu tiên từ hàm
SELECT * FROM dbo.fn_PhanLoaiUuTienChamSoc();
```
<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/5fae40d6-316c-49dc-aea2-9b66a548026f" />

### em sẽ giải thích ngắn gọn về đoạn code chạy trên:
- Sử dụng biến bảng `@BangKetQua`: Khác với hàm Inline (chỉ có 1 lệnh `RETURN`), hàm này định nghĩa rõ cấu trúc bảng trả về ngay tại phần khai báo. điều này cho phép ta thực hiện nhiều bước xử lý (`INSERT`, `UPDATE`) bên trong.

- Logic xử lý: Hàm thực hiện phân loại qua nhiều giai đoạn. Đặc biệt, nó có khả năng gọi lại hàm Scalar `fn_TinhTongChiPhiSinhVat` đã tạo trước đó để kiểm tra điều kiện về chi phí, thể hiện sự kết nối logic giữa các thành phần trong `Database`.

- Ứng dụng: Đây là công cụ hỗ trợ ra quyết định cho ban quản lý thủy cung, giúp lọc ra những cá thể cần chú ý ngay lập tức mà không cần truy vấn thủ công từng điều kiện.

## Phần 3: Xây dựng Store Procedure
Khác với Function (Hàm) dùng để tính toán và trả về giá trị, Stored Procedure (SP) mạnh mẽ hơn vì nó có thể thực hiện các tác vụ quản trị, thay đổi cấu trúc hệ thống và xử lý logic phức tạp.

### 3.1. Tìm hiểu về System Stored Procedures
Trong SQL Server, các System SP thường bắt đầu bằng tiền tố sp_. Chúng được lưu trữ trong database hệ thống (master) nhưng bạn có thể gọi chúng ở bất kỳ database nào để thực hiện các nhiệm vụ kiểm tra và quản trị.

Sau đây là 3 System SP mà em tìm hiểu được:

- ### 1. sp_help
Mục đích: Đây là lệnh "cứu cánh" khi  quên mất cấu trúc bảng. Nó hiển thị chi tiết các cột, kiểu dữ liệu, khóa chính, khóa ngoại của một đối tượng.

Cách dùng: `EXEC sp_help 'Tên_Bảng';`

- ### 2. sp_helptext
Mục đích: Dùng để xem lại "ruột" (mã nguồn) của một Function, View hoặc Stored Procedure mà bạn đã viết trước đó. Rất hữu ích khi bạn muốn copy code từ cái cũ sang cái mới.

Cách dùng: `EXEC sp_helptext 'Tên_Function_Hoặc_SP';`

- ### 3. sp_rename
Mục đích: Dùng để đổi tên một đối tượng (bảng, cột) mà không cần phải xóa đi tạo lại. Tuy nhiên, SQL Server sẽ cảnh báo bạn vì việc này có thể làm hỏng các script liên quan.

Cách dùng: `EXEC sp_rename 'TenCu', 'TenMoi';`

### Truy vấn SQL khai thác các System SP trên:
Em sẽ chạy thử các lệnh này trong `database QuanLyThuyCung` của mình để thấy được kết quả 
```sql
-- 1. Xem chi tiết cấu trúc bảng sinhVat (Cực kỳ hay dùng)
-- Kết quả sẽ hiện ra: Cột nào là khóa chính, khóa ngoại, kiểu dữ liệu là gì...
EXEC sp_help 'sinhVat';

-- 2. Xem lại đoạn code mình đã viết cho hàm fn_TinhTongChiPhiSinhVat
-- Rất hữu ích khi cần kiểm tra lại logic đã viết
EXEC sp_helptext 'fn_TinhTongChiPhiSinhVat';

-- 3. Kiểm tra danh sách các database đang có trên Server
-- Giúp bạn biết hệ thống đang quản lý những vùng lưu trữ nào
EXEC sp_databases;
```
### Ảnh chạy đoạn code trên 
<img width="2878" height="1799" alt="image" src="https://github.com/user-attachments/assets/7bc31e36-416c-4e09-ae46-be3b48bede2d" />
<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/fc426f22-d7cc-42d2-9d04-763e03153bdc" />
<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/036b03ab-6f11-40b4-9d03-5cc8373da53a" />

### 3.2. Viết Stored Procedure thực hiện INSERT dữ liệu có kiểm tra logic
Logic nghiệp vụ: Khi thêm một lịch cho ăn mới (`lichChoAn`), chúng ta cần đảm bảo tính hợp lệ của dữ liệu:

- Sinh vật đó phải tồn tại trong hệ thống.

- Lượng thức ăn không được vượt quá mức cho phép (ví dụ: tối đa 50kg/lần) để tránh lãng phí hoặc sai sót nhập liệu.

Yêu cầu của SP: Nhận vào các thông số lịch ăn. Nếu thỏa mãn điều kiện thì thực hiện INSERT, nếu không thì báo lỗi và hủy thao tác.

### Script khởi tạo Stored Procedure:
```sql
CREATE PROCEDURE sp_ThemLichChoAn
    @maSV INT,
    @thoiGian DATETIME,
    @luongAn FLOAT,
    @chiPhi MONEY
AS
BEGIN
    -- 1. Kiểm tra sinh vật có tồn tại không
    IF NOT EXISTS (SELECT 1 FROM [sinhVat] WHERE [maSinhVat] = @maSV)
    BEGIN
        PRINT N'Lỗi: Mã sinh vật không tồn tại trong hệ thống!';
        RETURN;
    END

    -- 2. Kiểm tra logic lượng thức ăn (Giả sử tối đa là 50kg)
    IF @luongAn > 50
    BEGIN
        PRINT N'Lỗi: Lượng thức ăn quá lớn (vượt ngưỡng 50kg), vui lòng kiểm tra lại!';
        RETURN;
    END

    -- 3. Thực hiện chèn dữ liệu nếu mọi điều kiện hợp lệ
    INSERT INTO [lichChoAn] ([maSinhVat], [thoiGianChoAn], [luongThucAnKg], [chiPhiBuaAn])
    VALUES (@maSV, @thoiGian, @luongAn, @chiPhi);

    PRINT N'Thêm lịch cho ăn thành công!';
END;
GO
```
### Câu lệnh SQL khai thác (Thực thi) Stored Procedure:
Chúng ta sẽ thử 2 trường hợp: một trường hợp lỗi và một trường hợp thành công để kiểm chứng logic.
```sql
-- Trường hợp 1: Thử thêm cho một sinh vật không tồn tại (Mã 999)
EXEC sp_ThemLichChoAn 999, '2026-05-02 10:00:00', 5.0, 100;

-- Trường hợp 2: Thử nhập lượng thức ăn quá lớn (60kg)
EXEC sp_ThemLichChoAn 1, '2026-05-02 10:00:00', 60.0, 500;

-- Trường hợp 3: Thêm dữ liệu hợp lệ
EXEC sp_ThemLichChoAn 1, '2026-05-02 11:30:00', 10.5, 200;

-- Kiểm tra lại bảng dữ liệu
SELECT * FROM [lichChoAn];
```
<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/66a639fd-a965-432a-9fb8-02901ec759be" />

### 3.3. Viết Stored Procedure sử dụng tham số OUTPUT
Yêu cầu của SP: Tính tổng chi phí vận hành (bao gồm bảo hiểm và chi phí thức ăn) của một bể nuôi bất kỳ. Kết quả tổng tiền này sẽ được trả về qua một biến OUTPUT.

Logic nghiệp vụ: Ban quản lý cần biết một bể nuôi cụ thể đang tiêu tốn bao nhiêu ngân sách để cân đối tài chính.

### Script khởi tạo Stored Procedure:
```sql
CREATE PROCEDURE sp_TinhNganSachBeNuoi
    @idBe INT,
    @tongNganSach MONEY OUTPUT -- Tham số trả kết quả ra ngoài
AS
BEGIN
    -- Kiểm tra bể có tồn tại không
    IF NOT EXISTS (SELECT 1 FROM [beNuoi] WHERE [maBe] = @idBe)
    BEGIN
        SET @tongNganSach = 0;
        PRINT N'Lỗi: Không tìm thấy bể nuôi này!';
        RETURN;
    END

    -- Tính toán: Tổng Bảo hiểm sinh vật trong bể + Tổng chi phí các bữa ăn
    DECLARE @tienBaoHiem MONEY = 0;
    DECLARE @tienThucAn MONEY = 0;

    -- 1. Tính tổng bảo hiểm của các sinh vật thuộc bể này
    SELECT @tienBaoHiem = SUM([giaTriBaoHiem]) 
    FROM [sinhVat] 
    WHERE [maBe] = @idBe;

    -- 2. Tính tổng chi phí thức ăn của các sinh vật thuộc bể này
    SELECT @tienThucAn = SUM(L.[chiPhiBuaAn])
    FROM [lichChoAn] L
    JOIN [sinhVat] S ON L.[maSinhVat] = S.[maSinhVat]
    WHERE S.[maBe] = @idBe;

    -- Gán tổng giá trị vào biến OUTPUT (Xử lý trường hợp NULL bằng 0)
    SET @tongNganSach = ISNULL(@tienBaoHiem, 0) + ISNULL(@tienThucAn, 0);

    PRINT N'Đã tính toán xong ngân sách cho bể số: ' + CAST(@idBe AS NVARCHAR(10));
END;
GO
```

### Câu lệnh SQL khai thác (Sử dụng biến để nhận giá trị):
Để lấy được giá trị từ tham số `OUTPUT`, bạn cần thực hiện theo các bước khai báo biến hứng như sau:
```sql
-- 1. Khai báo biến để nhận kết quả trả về từ SP
DECLARE @nganSachHienTai MONEY;

-- 2. Thực thi SP với tham số OUTPUT
EXEC sp_TinhNganSachBeNuoi 
    @idBe = 1, 
    @tongNganSach = @nganSachHienTai OUTPUT;

-- 3. Hiển thị kết quả cuối cùng
SELECT 
    @nganSachHienTai AS [TongNganSach_Be_1],
    FORMAT(@nganSachHienTai, 'C', 'vi-VN') AS [DinhDangTienTe];
```

<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/d5f70ebc-eb30-43bf-9d84-030018d8a135" />

### 3.4. Viết Stored Procedure trả về tập kết quả (Join nhiều bảng)
Logic nghiệp vụ: Ban quản lý thủy cung cần một "Báo cáo Nhật ký Nuôi dưỡng Chi tiết". Báo cáo này phải cho biết: Tên sinh vật là gì, nó đang ở bể nào, và lịch sử ăn uống gần nhất của nó ra sao (thời gian, lượng thức ăn).

Yêu cầu của SP: Thực hiện JOIN 3 bảng `beNuoi`, `sinhVat` và `lichChoAn` để đưa ra một cái nhìn tổng thể.

### Script khởi tạo Stored Procedure:
```sql
CREATE PROCEDURE sp_BaoCaoChiTietNuoiDuong
AS
BEGIN
    -- Thiết lập định dạng ngày tháng hiển thị cho đẹp (tùy chọn)
    SET NOCOUNT ON; 

    -- Thực hiện truy vấn JOIN 3 bảng để lấy thông tin tổng hợp
    SELECT 
        B.[tenBe] AS [TenBeNuoi],
        S.[tenLoai] AS [TenSinhVat],
        S.[tinhTrangSucKhoe] AS [SucKhoe],
        L.[thoiGianChoAn] AS [NgayGioAn],
        L.[luongThucAnKg] AS [LuongAn_Kg],
        L.[chiPhiBuaAn] AS [ChiPhi]
    FROM [beNuoi] B
    INNER JOIN [sinhVat] S ON B.[maBe] = S.[maBe]
    INNER JOIN [lichChoAn] L ON S.[maSinhVat] = L.[maSinhVat]
    ORDER BY L.[thoiGianChoAn] DESC; -- Sắp xếp lịch ăn mới nhất lên đầu
END;
GO
```

### Câu lệnh SQL khai thác Stored Procedure:
Đối với loại SP trả về tập kết quả, bạn chỉ cần gọi lệnh `EXEC` là bảng dữ liệu sẽ hiện ra ngay lập tức.
```sql
-- Thực thi để xem báo cáo tổng hợp từ 3 bảng
EXEC sp_BaoCaoChiTietNuoiDuong;
```

<img width="2879" height="1793" alt="image" src="https://github.com/user-attachments/assets/dc3084b9-14a6-4ee0-b7f7-b4234fffe015" />

## Phần 4: Xây dựng Trigger và Xử lý logic nghiệp vụ
Trong quản lý Thủy cung, logic thực tế nhất chính là: Tự động cập nhật tình trạng sức khỏe của sinh vật khi chúng được cho ăn.
### 4.1. Trigger tự động cập nhật dữ liệu liên bảng
Logic nghiệp vụ: Khi một nhân viên thêm một bản ghi vào bảng `lichChoAn` (Bảng A), hệ thống sẽ tự động cập nhật cột `tinhTrangSucKhoe` trong bảng `sinhVat` (Bảng B) thành 'Khỏe mạnh'.

Lý do thuyết phục: Việc được cho ăn đầy đủ là dấu hiệu tốt nhất cho thấy sinh vật đang được chăm sóc. Tự động hóa việc này giúp giảm bớt thao tác thủ công cho nhân viên và đảm bảo dữ liệu luôn phản ánh trạng thái mới nhất.
### Script khởi tạo Trigger:
```sql
CREATE TRIGGER trg_CapNhatSucKhoeSauKhiAn
ON [lichChoAn]
AFTER INSERT -- Kích hoạt sau khi thêm dữ liệu vào bảng lịch cho ăn
AS
BEGIN
    -- Cập nhật tình trạng sức khỏe trong bảng sinhVat
    -- Dựa trên mã sinh vật vừa được chèn vào trong bảng ảo 'inserted'
    UPDATE [sinhVat]
    SET [tinhTrangSucKhoe] = N'Khỏe mạnh'
    FROM [sinhVat] S
    INNER JOIN inserted I ON S.[maSinhVat] = I.[maSinhVat];

    PRINT N'Hệ thống: Đã tự động cập nhật trạng thái Khỏe mạnh cho sinh vật vừa ăn.';
END;
GO
```

### Câu lệnh SQL khai thác (Kiểm chứng) Trigger:
Để thấy được sự "vi diệu" của Trigger, chúng ta hãy làm theo 2 bước:
- Bước 1: Kiểm tra trạng thái hiện tại (Giả sử con cá mã số 2 đang 'Đang theo dõi')
```sql
SELECT [maSinhVat], [tenLoai], [tinhTrangSucKhoe] 
FROM [sinhVat] WHERE [maSinhVat] = 2;
```

<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/594afbd6-7d9e-4ec2-9120-8c011e10d122" />


- Bước 2: Thêm một lịch cho ăn cho con cá số 2 này
```sql
INSERT INTO [lichChoAn] ([maSinhVat], [thoiGianChoAn], [luongThucAnKg], [chiPhiBuaAn])
VALUES (2, GETDATE(), 0.5, 25);
```

<img width="2874" height="1799" alt="image" src="https://github.com/user-attachments/assets/1457072f-043a-4dfb-a5b8-301b84218df1" />



- Bước 3: Kiểm tra lại bảng sinhVat
```sql
-- Bạn sẽ thấy tình trạng của nó tự động nhảy sang 'Khỏe mạnh' mà không cần lệnh UPDATE thủ công
SELECT [maSinhVat], [tenLoai], [tinhTrangSucKhoe] 
FROM [sinhVat] WHERE [maSinhVat] = 2;
```
<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/a132390a-d543-4cf5-bf6a-f372383bfe70" />

- Bước 4: Chạy hết toàn bộ code 
<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/1e035ff9-411e-4be0-ae6f-d4d4638ed0d1" />

### 4.2. Trigger vòng lặp (Recursive Triggers).

#### 4.2.1. Thiết lập thí nghiệm (Scenario)
 - Bảng A (`sinhVat`): Khi cập nhật trạng thái cá, sẽ cập nhật ghi chú bên bảng B.

 - Bảng B (`lichChoAn`): Khi cập nhật ghi chú lịch ăn, sẽ cập nhật ngược lại trạng thái bên bảng A.

- Bước 1: Viết Trigger cho bảng A (`sinhVat`) cập nhật bảng B
```sql
CREATE TRIGGER trg_A_to_B
ON [sinhVat]
AFTER UPDATE
AS
BEGIN
    PRINT N'--> Trigger A_to_B đang chạy...';
    UPDATE [lichChoAn]
    SET [chiPhiBuaAn] = [chiPhiBuaAn] + 1 -- Tăng nhẹ chi phí làm dấu
    FROM [lichChoAn] L
    INNER JOIN inserted I ON L.[maSinhVat] = I.[maSinhVat];
END;
GO
```

<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/efe9f056-bc74-4f68-a301-0d9336966e1e" />


- Bước 2: Viết Trigger cho bảng B (`lichChoAn`) cập nhật ngược lại bảng A
```sql
CREATE TRIGGER trg_B_to_A
ON [lichChoAn]
AFTER UPDATE
AS
BEGIN
    PRINT N'--> Trigger B_to_A đang chạy...';
    UPDATE [sinhVat]
    SET [tinhTrangSucKhoe] = N'Đã kiểm tra'
    FROM [sinhVat] S
    INNER JOIN inserted I ON S.[maSinhVat] = I.[maSinhVat];
END;
GO
```

<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/da017c30-bc8f-4599-9825-02bc3f9f6609" />


#### 4.2.1. Quan sát hiện tượng

Bây giờ, em  kích hoạt vòng lặp bằng một lệnh `UPDATE` đơn giản:
```sql
UPDATE [sinhVat] SET [tinhTrangSucKhoe] = N'Khỏe' WHERE [maSinhVat] = 1;
```

<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/56d389c5-29d3-421a-b159-23209214fd12" />

#### Giải thích thông báo hệ thống
- Lỗi Msg 217: "Maximum nesting level exceeded (limit 32)".

--> Giải thích: SQL Server có một cơ chế bảo vệ hệ thống. Nó chỉ cho phép các lệnh gọi nhau tối đa 32 tầng.

Lệnh Update A gọi Trigger A.

- Trigger A gọi lệnh Update B.

- Update B gọi Trigger B.

- Trigger B lại gọi lệnh Update A...

--> Cứ thế tạo thành một cái vòng lặp vô tận (Infinite Loop). Nếu SQL Server không tự ngắt ở tầng thứ 32, máy chủ sẽ bị treo (treo CPU và tràn bộ nhớ) vì hàng triệu lệnh thực hiện cùng lúc.

### 4.3 Nhận xét cuối cùng 

- Thiết kế sai lầm: Việc để hai Trigger cập nhật qua lại lẫn nhau tạo ra hiện tượng Đệ quy gián tiếp (Indirect Recursion). Đây là một lỗi thiết kế nghiêm trọng trong Database cần tuyệt đối tránh.

--> Hậu quả: Gây tốn tài nguyên hệ thống và khiến dữ liệu không bao giờ được cập nhật thành công (vì bị ROLLBACK khi chạm ngưỡng 32 lần).

- Giải pháp: >    * Chỉ nên để Trigger chạy một chiều.

Nếu cần cập nhật nhiều bảng, hãy gom tất cả vào 01 Stored Procedure duy nhất để kiểm soát luồng dữ liệu thay vì dùng nhiều Trigger rời rạc.

Sử dụng tùy chọn `IF TRIGGER_NESTLEVEL() > 1 RETURN` để ngắt vòng lặp nếu bắt buộc phải dùng.

### Tổng kết phần 4:
Qua thí nghiệm này, chúng ta rút ra được những điểm mấu chốt sau:

Tính nguy hiểm: Trigger rất mạnh nhưng nếu thiết kế không khéo (để chúng cập nhật chéo nhau) sẽ tạo ra "hố đen" nuốt chửng tài nguyên hệ thống.

Cơ chế tự vệ: SQL Server rất thông minh, nó có ngưỡng giới hạn (32 tầng) để ngăn chặn các sai lầm của lập trình viên làm sập hệ thống.

Lời khuyên chuyên gia: Luôn phải kiểm tra luồng dữ liệu (Data Flow) khi viết Trigger liên bảng. Nếu bảng A đã tác động bảng B, thì bảng B không nên tác động ngược lại bảng A bằng cùng một loại sự kiện.

## Phần 5: Cursor và Duyệt dữ liệu
Để hoàn thiện kỹ năng xử lý dữ liệu nâng cao, chúng ta sẽ làm quen với Cursor.

Mặc dù trong SQL Server, em sẽ thường ưu tiên xử lý dữ liệu theo tập hợp (Set-based), nhưng Cursor là công cụ không thể thay thế khi cần duyệt qua từng dòng một để thực hiện các tác vụ riêng biệt mà một câu lệnh `UPDATE` hay `INSERT` thông thường không làm được (ví dụ: gửi email, in báo cáo cá nhân hóa, hoặc thực hiện logic nghiệp vụ phức tạp cho mỗi bản ghi).

### 5.1. Sử dụng Cursor để xử lý dữ liệu từng dòng
- Logic nghiệp vụ: Ban quản lý muốn xuất một "Thông báo bảo trì định kỳ" cho từng bể nuôi.

- Với mỗi bể, chúng ta sẽ tính toán số lượng sinh vật hiện có.

- Dựa vào loại môi trường (Nước mặn/Nước ngọt) để đưa ra lời khuyên bảo trì khác nhau.

- In ra một thông điệp chi tiết cho từng bể.

### Script sử dụng Cursor:
```sql
-- 1. Khai báo các biến để chứa dữ liệu từ từng dòng
DECLARE @tenBe NVARCHAR(100);
DECLARE @loaiMT NVARCHAR(50);
DECLARE @maBe INT;
DECLARE @soLuongSV INT;

-- 2. Khai báo Cursor để duyệt qua danh sách các bể nuôi
DECLARE cur_BaoTriBe CURSOR FOR 
SELECT maBe, tenBe, loaiMoiTruong FROM [beNuoi];

-- 3. Mở Cursor
OPEN cur_BaoTriBe;

-- 4. Lấy dòng dữ liệu đầu tiên
FETCH NEXT FROM cur_BaoTriBe INTO @maBe, @tenBe, @loaiMT;

-- 5. Vòng lặp duyệt qua từng bản ghi cho đến khi hết dữ liệu (@@FETCH_STATUS = 0)
WHILE @@FETCH_STATUS = 0
BEGIN
    -- Xử lý logic riêng cho từng bản ghi: Đếm số sinh vật trong bể hiện tại
    SELECT @soLuongSV = COUNT(*) FROM [sinhVat] WHERE [maBe] = @maBe;

    -- In ra thông báo tùy biến theo logic nghiệp vụ
    PRINT '----------------------------------------------------';
    PRINT N'THÔNG BÁO BẢO TRÌ BỂ: ' + UPPER(@tenBe);
    PRINT N'Môi trường: ' + @loaiMT;
    PRINT N'Số lượng sinh vật đang quản lý: ' + CAST(@soLuongSV AS NVARCHAR(10));

    IF @loaiMT = N'Nước mặn'
        PRINT N'Lưu ý: Kiểm tra nồng độ muối và hệ thống lọc san hồ.';
    ELSE
        PRINT N'Lưu ý: Kiểm tra độ pH và hệ thống sục khí oxy.';

    -- 6. Lấy dòng tiếp theo
    FETCH NEXT FROM cur_BaoTriBe INTO @maBe, @tenBe, @loaiMT;
END;

-- 7. Đóng và giải phóng Cursor (Cực kỳ quan trọng để tránh tốn tài nguyên)
CLOSE cur_BaoTriBe;
DEALLOCATE cur_BaoTriBe;
```

<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/9717dcc6-c216-4d33-a8e6-71774a2d7f4f" />






Giải thích 
- DECLARE CURSOR: Định nghĩa tập dữ liệu mà bạn muốn duyệt qua.

- OPEN / FETCH NEXT: Mở danh sách và bắt đầu "bốc" từng dòng dữ liệu gán vào các biến tương ứng.

- WHILE @@FETCH_STATUS = 0: Vòng lặp này sẽ chạy cho đến khi không còn dòng nào để đọc nữa.

- CLOSE / DEALLOCATE: Cursor chiếm dụng bộ nhớ trên Server, vì vậy phải đóng và giải phóng ngay sau khi dùng xong.

### Nhận xét về Cursor:
- Ưu điểm: Cho phép xử lý logic cực kỳ chi tiết cho từng dòng (Row-by-row processing), phù hợp cho việc tạo báo cáo phức tạp hoặc gọi các Stored Procedure khác cho từng đối tượng.

- Nhược điểm: Hiệu năng chậm hơn so với xử lý tập hợp (Set-based) nếu danh sách có hàng triệu bản ghi. Vì vậy, chỉ nên dùng Cursor khi thực sự cần thiết.

 ### 5.2 Giải pháp không dùng Cursor (Set-based)
Chúng ta sẽ dùng một lệnh `SELECT` duy nhất để tính toán và ghép chuỗi dữ liệu.
```sql
-- Sử dụng SQL Set-based để tạo nội dung thông báo cho tất cả các bể cùng lúc
SELECT 
    '----------------------------------------------------' AS [Divider],
    UPPER([tenBe]) AS [TenBeNuoi],
    [loaiMoiTruong],
    (SELECT COUNT(*) FROM [sinhVat] S WHERE S.[maBe] = B.[maBe]) AS [SoLuongSV],
    CASE 
        WHEN [loaiMoiTruong] = N'Nước mặn' THEN N'Lưu ý: Kiểm tra nồng độ muối và hệ thống lọc san hồ.'
        ELSE N'Lưu ý: Kiểm tra độ pH và hệ thống sục khí oxy.'
    END AS [HuongDanBaoTri]
FROM [beNuoi] B;
```

### So sánh tốc độ và Hiệu năng
Để so sánh chính xác, bạn hãy bật tính năng đo thời gian và tài nguyên của SQL Server bằng cách chạy lệnh này trước khi thực thi code:
```sql
SET STATISTICS TIME ON; -- Đo thời gian CPU và thời gian thực thi
SET STATISTICS IO ON;   -- Đo số lần đọc/ghi dữ liệu trên ổ đĩa
```
<img width="2879" height="1799" alt="image" src="https://github.com/user-attachments/assets/5f75a0ba-f0df-4ef5-a8a4-64555469974e" />

<img width="2879" height="1789" alt="image" src="https://github.com/user-attachments/assets/6d98a20f-b43e-45c3-90a0-15d251339861" />

#### Bảng so sánh chi tiết:
| Tiêu chí | Sử dụng Cursor | Sử dụng Set-based (SELECT) |
| :--- | :--- | :--- |
| **Cơ chế** | **Duyệt từng dòng** (Row-by-row). | **Xử lý cả tập hợp/bảng** cùng lúc. |
| **Thời gian CPU** | **Cao** (do lặp lại việc cấp phát bộ nhớ cho biến). | **Rất thấp** (được tối ưu hóa bởi Query Optimizer). |
| **Số lần đọc dữ liệu** | **Nhiều** (mỗi dòng dữ liệu là một lần Fetch). | **Ít** (truy cập bảng theo chỉ mục tối ưu). |
| **Độ phức tạp mã** | **Dài dòng**, cấu trúc phức tạp, khó bảo trì. | **Ngắn gọn**, tường minh, dễ đọc. |
| **Hiệu năng** | **Chậm**, thường gây nghẽn hệ thống khi dữ liệu lớn. | **Nhanh**, là cách tiếp cận chuẩn trong SQL Server. |

