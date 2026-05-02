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
                                            
                                              _Bảng tạo cơ sở dữ liệu_
