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
