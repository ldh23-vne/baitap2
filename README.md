Thông tin sinh viên:

Họ và tên: Lù Đình Hưng

Mã sinh viên: K235480106033

Lớp: K59KMT.K01

Khoa: Điện tử

Môn học: Hệ Quản Trị Cơ Sở Dữ Liệu (SQL Server) 

Yêu Cầu Đầu Bài

Đề tài: Quản lý nhà hàng  
Thực hiện xây dựng một hệ thống quản lý khách sạn hoàn chỉnh trên SQL Server, đáp ứng các tiêu chí cụ thể sau đây:

Toàn bộ quá trình thực hiện phải được ghi lại thông qua các screenshot minh họa, mỗi bức ảnh đều đi kèm với câu lệnh SQL tương ứng và chú thích rõ ràng về chức năng, mục đích xử lý, cũng như kết quả đạt được.

Bài tập được nộp dưới dạng repository GitHub (public), bao gồm hai thành phần chính: tệp README.md chứa toàn bộ nội dung, hình ảnh minh họa và giải thích chi tiết, tệp 2.sql chứa toàn bộ mã SQL.

Đánh giá dựa trên ba yếu tố quan trọng:

Logic xử lý dữ liệu — các câu lệnh có giải quyết đúng bài toán không

Quy tắc đặt tên — các bảng, cột, hàm có tuân theo chuẩn bướu Lạc Đà không

Commit history — quá trình phát triển có rõ ràng, có thể theo dõi được không. Deadline nộp bài là cuối kỳ, sinh viên cần hoàn thành và push lên GitHub trước ngày hết hạn.
Giới Thiệu Về Hệ Thống Quản Lý Nhà Hàng

Xây dựng một hệ thống quản lý nhà hàng (QuanLyNhaHang) từ nền tảng SQL Server, bao gồm các chức năng chủ yếu như quản lý thông tin khách hàng, quản lý dữ liệu bàn ăn, và quản lý các lượt đặt bàn/gọi món. Mỗi khách hàng đều có hồ sơ lưu trữ với các thông tin cá nhân, số điện thoại liên hệ, ngày sinh và điểm đánh giá; mỗi bàn trong nhà hàng được phân loại theo loại bàn (2 người, 4 người, 6 người, VIP), có vị trí và sức chứa riêng biệt; các lượt đặt bàn lưu giữ mối quan hệ giữa khách hàng và bàn, cùng với thời gian đặt, thời gian sử dụng và tiền cọc (nếu có). Ngoài ra, hệ thống còn quản lý thực đơn và các món ăn được gọi trong từng lượt phục vụ.

Toàn bộ bài làm được chia thành 5 phần chính theo thứ tự tăng độ phức tạp:

Thiết Kế Cơ Sở Dữ Liệu — khởi tạo các bảng KhachHang, Ban, MonAn, DatBan, HoaDon với các ràng buộc toàn vẹn (Primary Key, Foreign Key, Check Constraint) và chèn dữ liệu mẫu.

Xây Dựng Function — tạo các hàm tính toán như tính tổng tiền hóa đơn, tính số lượng món đã gọi, kiểm tra bàn trống; những hàm này giúp tái sử dụng logic và làm sạch mã.

Xây Dựng Stored Procedure — tạo các thủ tục lưu trữ để xử lý các nghiệp vụ phức tạp như đặt bàn mới, gọi món, thanh toán hóa đơn, báo cáo doanh thu theo ngày/tháng.

Trigger Xử Lý Nghiệp Vụ — định nghĩa các trigger tự động kích hoạt khi dữ liệu thay đổi (chẳng hạn, tự động cập nhật trạng thái bàn khi có khách đặt hoặc thanh toán, hoặc ghi log thay đổi dữ liệu).

Dùng Cursor và Xử Lý Dữ Liệu — sử dụng cursor để duyệt từng bản ghi và thực hiện các xử lý tuần tự, đồng thời so sánh hiệu năng giữa phương pháp cursor và phương pháp set-based, từ đó rút ra kết luận về tối ưu hóa.

Trong quá trình thực hiện, sẽ thiết kế cơ sở dữ liệu với tên chuẩn: QuanLyNhaHang_K235480106033. Mỗi phần lệnh SQL viết ra sẽ đi kèm một ảnh screenshot chứa mã lệnh và kết quả thực thi, với các chú thích chi tiết: ảnh dùng để minh họa bước nào, câu lệnh giải quyết vấn đề gì, kết quả thể hiện thông tin gì. Thông qua bài tập này sẽ nắm vững các khái niệm cơ bản và nâng cao của SQL Server, từ DDL (Data Definition Language) đến DML (Data Manipulation Language), từ những truy vấn đơn giản đến những xử lý phức tạp bằng Function, Procedure, Trigger, và Cursor, từ đó tích lũy kỹ năng thiết kế và quản trị cơ sở dữ liệu chuyên nghiệp.

Phần 1: Khởi tạo database và bảng

Tạo cơ sở dữ liệu  

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/8a4953a9-1586-4fe4-870c-2cd9a1c42996" />
1.1 Tạo cơ sở dữ liệu QuanLyNhaHang_K235480106033


Bảng [KhachHang]

Sử dụng MaKhachHang làm Khóa chính (PK), tự động tăng (IDENTITY(1,1)).

Họ tên dùng NVARCHAR để hỗ trợ tiếng Việt có dấu.

Điểm đánh giá (DiemDanhGia) có ràng buộc CHECK từ 1 đến 5 sao.


CREATE TABLE [KhachHang] (
    [MaKhachHang] INT IDENTITY(1,1) NOT NULL,
    [HoTenKhachHang] NVARCHAR(100) NOT NULL,
    [NgaySinh] DATE,
    [SoDienThoai] VARCHAR(15) NOT NULL,
    [DiemDanhGia] INT DEFAULT 5,
    CONSTRAINT [PK_KhachHang] PRIMARY KEY ([MaKhachHang]),
    CONSTRAINT [CK_DiemDanhGia] CHECK ([DiemDanhGia] >= 1 AND [DiemDanhGia] <= 5)
);  


<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/1819aa75-f468-4e8c-8e84-cf7c70516b37" />
1.2 Tạo bảng KhachHang


Bảng [Ban]

MaBan: khóa chính (PK), tự tăng IDENTITY(1,1)

TenBan: tên bàn (VD: Bàn 1, Bàn VIP A)

LoaiBan: phân loại (2 người, 4 người, VIP…)

SucChua: số người tối đa

TrangThai: trạng thái bàn
→ mặc định là Trống 

CREATE TABLE [Ban] (
    [MaBan] INT IDENTITY(1,1) NOT NULL,
    [TenBan] NVARCHAR(50) NOT NULL,
    [LoaiBan] NVARCHAR(50) NOT NULL,
    [SucChua] INT NOT NULL,
    [TrangThai] NVARCHAR(20) DEFAULT N'Trống',
    
    CONSTRAINT [PK_Ban] PRIMARY KEY ([MaBan]),
    CONSTRAINT [CK_SucChua] CHECK ([SucChua] > 0),
    CONSTRAINT [CK_TrangThai] CHECK ([TrangThai] IN (N'Trống', N'Đã đặt', N'Đang sử dụng'))
);

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/11ebde55-51c1-4662-a209-a0282047a1d0" />
1.3 Tạo bảng Ban


Bảng [MonAn]

Sử dụng MaMon làm khóa chính (PK), tự tăng IDENTITY(1,1)

Tên món dùng NVARCHAR để hỗ trợ tiếng Việt

Giá món dùng MONEY, có ràng buộc > 0

Trạng thái món: Còn / Hết

CREATE TABLE [MonAn] (
    [MaMon] INT IDENTITY(1,1) NOT NULL,
    [TenMon] NVARCHAR(100) NOT NULL,
    [Gia] MONEY NOT NULL,
    [LoaiMon] NVARCHAR(50),
    [TrangThai] NVARCHAR(20) DEFAULT N'Còn',

    CONSTRAINT [PK_MonAn] PRIMARY KEY ([MaMon]),
    CONSTRAINT [CK_GiaMon] CHECK ([Gia] > 0),
    CONSTRAINT [CK_TrangThaiMon] CHECK ([TrangThai] IN (N'Còn', N'Hết'))
);


<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/b45e2baf-2130-434f-bd8e-c07381bbe813" />
1.4 Tạo bảng MonAn 

-Bảng [DatBan]

CREATE TABLE [DatBan] (
    [MaDatBan] INT IDENTITY(1,1) PRIMARY KEY,
    [MaKhachHang] INT NOT NULL,
    [MaBan] VARCHAR(10) NOT NULL,
    [ThoiGianDat] DATETIME NOT NULL,
    [ThoiGianDen] DATETIME,
    [TienCoc] MONEY DEFAULT 0,

    FOREIGN KEY (MaKhachHang) REFERENCES KhachHang(MaKhachHang),
    FOREIGN KEY (MaBan) REFERENCES Ban(MaBan)
);

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/dbcae2a6-37ec-4348-b446-d2511e939f17" />
1.5 Tạo bảng DatBan


-Bảng [HoaDon] 

Mỗi hóa đơn = 1 lần khách gọi món

Liên kết với bàn và khách hàng

[CREATE TABLE [HoaDon] (
    [MaHoaDon] INT IDENTITY(1,1) NOT NULL,
    [MaBan] VARCHAR(10) NOT NULL,
    [MaKhachHang] INT,
    [ThoiGianVao] DATETIME NOT NULL,
    [ThoiGianRa] DATETIME,
    [TongTien] MONEY DEFAULT 0,

    CONSTRAINT [PK_HoaDon] PRIMARY KEY ([MaHoaDon]),

    CONSTRAINT [FK_HoaDon_Ban]
        FOREIGN KEY ([MaBan]) REFERENCES [Ban]([MaBan]),

    CONSTRAINT [FK_HoaDon_KhachHang]
        FOREIGN KEY ([MaKhachHang]) REFERENCES [KhachHang]([MaKhachHang])
);]


<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/808f1fa2-5e93-4da8-b212-fb3bd05b223d" />
1.6 Tạo bảng HoaDon

-Bảng [ChiTietHoaDon] (Chi tiết món ăn)

--Đây là bảng quan trọng nhất để lưu món

CREATE TABLE [ChiTietHoaDon] (
    [MaHoaDon] INT NOT NULL,
    [MaMon] INT NOT NULL,
    [SoLuong] INT NOT NULL,
    [DonGia] MONEY NOT NULL,

    CONSTRAINT [PK_ChiTietHoaDon] PRIMARY KEY ([MaHoaDon], [MaMon]),

    CONSTRAINT [FK_CTHD_HoaDon]
        FOREIGN KEY ([MaHoaDon]) REFERENCES [HoaDon]([MaHoaDon]),

    CONSTRAINT [FK_CTHD_MonAn]
        FOREIGN KEY ([MaMon]) REFERENCES [MonAn]([MaMon]),

    CONSTRAINT [CK_SoLuong] CHECK ([SoLuong] > 0),
    CONSTRAINT [CK_DonGia] CHECK ([DonGia] > 0)
);


<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/addc6c7b-7287-4688-953a-657263e005cf" />
1.7 Tạo bảng ChiTietHoaDon

Chèn dữ liệu vào các bảng  

-- 1. CHÈN 20 BÀN VÀO BẢNG [Ban]
DECLARE @iBan INT = 1;
DECLARE @MaBan VARCHAR(10);
DECLARE @LoaiBan NVARCHAR(50);
DECLARE @SucChua INT;
DECLARE @RanBan INT;

WHILE @iBan <= 20
BEGIN
    SET @MaBan = 'B' + RIGHT('00' + CAST(@iBan AS VARCHAR), 2);

    SET @RanBan = ABS(CHECKSUM(NEWID())) % 4 + 1;

    SET @LoaiBan = CHOOSE(@RanBan,
        N'Bàn 2 người',
        N'Bàn 4 người',
        N'Bàn 6 người',
        N'Bàn VIP');

    SET @SucChua = CHOOSE(@RanBan, 2, 4, 6, 10);

    INSERT INTO [Ban] (MaBan, LoaiBan, SucChua)
    VALUES (@MaBan, @LoaiBan, @SucChua);

    SET @iBan = @iBan + 1;
END;
GO
-- 2. CHÈN 100 KHÁCH HÀNG VÀO BẢNG [KhachHang]
DECLARE @iKhach INT = 1;
DECLARE @Ho NVARCHAR(50);
DECLARE @Dem NVARCHAR(50);
DECLARE @Ten NVARCHAR(50);
DECLARE @HoTen NVARCHAR(100);
DECLARE @NgaySinh DATE;
DECLARE @Sdt VARCHAR(15);
DECLARE @Diem INT;
DECLARE @RanHo INT;
DECLARE @RanDem INT;
DECLARE @RanTen INT;
DECLARE @RanSdt INT;
DECLARE @SdtPrefix VARCHAR(2);

WHILE @iKhach <= 100
BEGIN
    SET @RanHo = ABS(CHECKSUM(NEWID())) % 10 + 1;
    SET @RanDem = ABS(CHECKSUM(NEWID())) % 10 + 1;
    SET @RanTen = ABS(CHECKSUM(NEWID())) % 15 + 1;

    SET @Ho = CHOOSE(@RanHo, N'Nguyễn', N'Trần', N'Lê', N'Phạm', N'Hoàng', N'Huỳnh', N'Phan', N'Vũ', N'Võ', N'Đặng');
    SET @Dem = CHOOSE(@RanDem, N'Văn', N'Thị', N'Hữu', N'Minh', N'Ngọc', N'Thanh', N'Đức', N'Xuân', N'Hải', N'Hoài');
    SET @Ten = CHOOSE(@RanTen, N'An', N'Bình', N'Cường', N'Dung', N'Hương', N'Khoa', N'Linh', N'Nam', N'Nghĩa', N'Oanh', N'Phong', N'Quỳnh', N'Sơn', N'Trang', N'Tuấn');

    SET @HoTen = @Ho + ' ' + @Dem + ' ' + @Ten;

    SET @NgaySinh = DATEADD(DAY, -(ABS(CHECKSUM(NEWID())) % (45 * 365)), '2005-01-01');

    SET @RanSdt = ABS(CHECKSUM(NEWID())) % 3 + 1;
    SET @SdtPrefix = CHOOSE(@RanSdt, '09', '08', '03');
    SET @Sdt = @SdtPrefix + RIGHT('00000000' + CAST(ABS(CHECKSUM(NEWID())) % 100000000 AS VARCHAR), 8);

    SET @Diem = ABS(CHECKSUM(NEWID())) % 3 + 3;

    INSERT INTO [KhachHang] (HoTenKhachHang, NgaySinh, SoDienThoai, DiemDanhGia)
    VALUES (@HoTen, @NgaySinh, @Sdt, @Diem);

    SET @iKhach = @iKhach + 1;
END;
GO
-- 3. CHÈN 50 MÓN ĂN VÀO BẢNG [MonAn]
DECLARE @iMon INT = 1;
DECLARE @TenMon NVARCHAR(100);
DECLARE @Gia MONEY;
DECLARE @LoaiMon NVARCHAR(50);
DECLARE @RanMon INT;

WHILE @iMon <= 50
BEGIN
    -- Tạo tên món
    SET @TenMon = N'Món ' + CAST(@iMon AS NVARCHAR);

    -- Random loại món
    SET @RanMon = ABS(CHECKSUM(NEWID())) % 3 + 1;
    SET @LoaiMon = CHOOSE(@RanMon,
        N'Món chính',
        N'Nước uống',
        N'Tráng miệng');

    -- Random giá
    SET @Gia = ABS(CHECKSUM(NEWID())) % 200000 + 20000;

    INSERT INTO [MonAn] (TenMon, Gia, LoaiMon)
    VALUES (@TenMon, @Gia, @LoaiMon);

    SET @iMon = @iMon + 1;
END;
GO
-- 4. CHÈN 100 LƯỢT ĐẶT BÀN VÀO BẢNG [DatBan]
DECLARE @iDatBan INT = 1;
DECLARE @MaKH INT;
DECLARE @MaB VARCHAR(10);
DECLARE @NgayDat DATETIME;
DECLARE @NgayDen DATETIME;
DECLARE @TienCoc MONEY;
DECLARE @RanCoc INT;

WHILE @iDatBan <= 100
BEGIN
    SELECT TOP 1 @MaKH = MaKhachHang FROM [KhachHang] ORDER BY NEWID();
    SELECT TOP 1 @MaB = MaBan FROM [Ban] ORDER BY NEWID();

    -- Random ngày đặt
    SET @NgayDat = DATEADD(DAY, ABS(CHECKSUM(NEWID())) % 30, '2023-01-01');

    -- Giờ đến (17h - 22h)
    SET @NgayDen = DATEADD(HOUR, ABS(CHECKSUM(NEWID())) % 6 + 17, @NgayDat);

    -- Tiền cọc
    SET @RanCoc = ABS(CHECKSUM(NEWID())) % 3 + 1;
    SET @TienCoc = CHOOSE(@RanCoc, 0, 200000, 500000);

    INSERT INTO [DatBan] (MaKhachHang, MaBan, ThoiGianDat, ThoiGianDen, TienCoc)
    VALUES (@MaKH, @MaB, @NgayDat, @NgayDen, @TienCoc);

    SET @iDatBan = @iDatBan + 1;
END;
GO

Phần 2: Xây dựng Function
Các loại hàm có sẵn (Built-in Functions)
SQL Server cung cấp hàng trăm hàm có sẵn để xử lý dữ liệu, được chia thành các nhóm chính:

Hàm chuỗi (String Functions): LEN(), SUBSTRING(), REPLACE(), UPPER(), LOWER()...

Hàm ngày tháng (Date/Time Functions): GETDATE(), DATEDIFF(), DATEADD(), YEAR(), MONTH()...

Hàm toán học (Mathematical Functions): ABS(), ROUND(), CEILING(), FLOOR()...

Hàm tổng hợp (Aggregate Functions): SUM(), COUNT(), MAX(), MIN(), AVG()...

Hàm chuyển đổi kiểu (Conversion Functions): CAST(), CONVERT()...

Hàm hệ thống (System Functions): ISNULL(), COALESCE(), NEWID(), CHOOSE()...

Tìm hiểu chi tiết 1 số Built-in Functions
NEWID(): Tạo ra một mã định danh duy nhất (GUID) dùng khi cần sắp xếp dữ liệu ngẫu nhiên.
--Lấy ngẫu nhiên 1 bàn để gợi ý cho khách
SELECT TOP 1 MaBan, LoaiBan, SucChua
FROM [Ban]
ORDER BY NEWID();

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/c0a0e06a-070a-44de-bfdb-fa5de3f9906d" />
2.1 Lấy ngẫu nhiên 1 bàn để gợi ý cho khách


CHOOSE(): Trả về một mục từ một danh sách dựa trên chỉ mục (giống như mảng). Giúp tránh phải viết CASE WHEN dài dòng.
--Phân loại bàn theo số ngẫu nhiên
SELECT CHOOSE(2,
    N'Bàn 2 người',
    N'Bàn 4 người',
    N'Bàn 6 người',
    N'Bàn VIP') AS LoaiBan;
    
    <img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/10933f1c-a9ae-4719-9f95-c265005fe29e" />
2.2 Phân loại bàn theo số ngẫu nhiên


DATEDIFF(): Tính khoảng cách giữa 2 mốc thời gian (theo ngày, tháng, năm, giờ...). Vô cùng quan trọng trong bài toán quản lý nhà hàng.
--Tính thời gian từ lúc đặt đến lúc đến (DatBan)
SELECT 
    MaDatBan,
    DATEDIFF(MINUTE, ThoiGianDat, ThoiGianDen) AS ThoiGianCho
FROM DatBan;

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/2176fb4f-cb4e-4d47-af62-dd4088f5c138" />
2.3 Tính thời gian từ lúc đặt đến lúc đến (DatBan)


Hàm do người dùng tự viết (User-Defined Functions - UDFs)
a) Mục đích

UDF được sử dụng để đóng gói các logic nghiệp vụ phức tạp thành một khối xử lý có thể tái sử dụng nhiều lần.

Giúp các câu lệnh như SELECT, UPDATE trở nên ngắn gọn, dễ đọc và dễ bảo trì.

Tăng tính tái sử dụng và nhất quán trong hệ thống cơ sở dữ liệu.

b) Phân loại và khi sử dụng

Scalar Function (Hàm vô hướng)

Trả về một giá trị duy nhất (số, chuỗi, ngày tháng…). Phù hợp khi cần tính toán trên từng dòng dữ liệu riêng lẻ. Ví dụ: tính tiền, tính thuế, định dạng số điện thoại.

Inline Table-Valued Function (ITVF)

Trả về một bảng dữ liệu từ một câu lệnh SELECT duy nhất. Hoạt động tương tự như View nhưng có thể truyền tham số. Dùng khi cần lọc hoặc truy vấn dữ liệu theo điều kiện động.

Multi-statement Table-Valued Function (MSTVF)

Trả về một bảng dữ liệu, nhưng cho phép sử dụng nhiều câu lệnh trong thân hàm. Có thể dùng các cấu trúc như IF...ELSE, WHILE, biến tạm, hoặc chèn dữ liệu vào bảng trung gian. Phù hợp khi xử lý logic phức tạp, nhiều bước, không thể biểu diễn bằng một câu lệnh SELECT đơn giản.

c) Tại sao vẫn cần UDF khi đã có Built-in Functions?

Mặc dù hệ quản trị cơ sở dữ liệu cung cấp nhiều hàm dựng sẵn (Built-in Functions), nhưng các hàm này chỉ xử lý các tác vụ cơ bản và mang tính chung như: tính toán số học, xử lý chuỗi, ngày tháng,...

Tuy nhiên, trong thực tế:

Mỗi hệ thống đều có quy tắc nghiệp vụ riêng (Business Logic)

Các quy tắc này thường phức tạp và đặc thù, không thể dùng trực tiếp hàm có sẵn

Việc viết lặp lại logic nhiều lần trong các câu lệnh SQL sẽ gây khó bảo trì và dễ sai sót

Vì vậy, cần sử dụng UDF để:

Đóng gói logic nghiệp vụ thành một hàm riêng biệt

Tái sử dụng nhiều lần trong các truy vấn khác nhau

Đảm bảo tính nhất quán và dễ bảo trì cho hệ thống


Viết các Function

Viết 1 Scalar Function (Hàm trả về 1 giá trị)

Tổng tiền = Tổng (Số lượng × Đơn giá) của các món ăn trong hóa đơn.

Nếu hóa đơn chưa có món ăn thì trả về 0.

Luồng xử lý tổng quát:

 Bước 1.

Hàm nhận vào mã hóa đơn làm tham số đầu vào.

 Bước 2.

Từ mã này, hệ thống truy xuất dữ liệu liên quan gồm:

Số lượng món ăn

Đơn giá của từng món

Từ bảng ChiTietHoaDon

Bước 3.
 
Hệ thống tiến hành tính toán:

Nhân Số lượng × Đơn giá cho từng dòng

Sau đó dùng hàm SUM() để cộng tất cả lại

 Bước 4.

Kiểm tra nếu:

Hóa đơn chưa có dữ liệu (SUM = NULL)
→ thì gán bằng 0
 
 Bước 5.

Cuối cùng, hàm trả về tổng tiền hóa đơn:

CREATE FUNCTION fn_TinhTongTienHoaDon (@MaHoaDon INT)
RETURNS MONEY
AS
BEGIN
    DECLARE @TongTien MONEY;

    -- Bước 2 + 3: Lấy dữ liệu và tính tổng
    SELECT @TongTien = SUM(SoLuong * DonGia)
    FROM ChiTietHoaDon
    WHERE MaHoaDon = @MaHoaDon;

    -- Bước 4: Nếu NULL thì = 0
    IF @TongTien IS NULL
        SET @TongTien = 0;

    -- Bước 5: Trả về kết quả
    RETURN @TongTien;
END;
GO

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/00edcbdb-4aef-44f1-ad6b-469d8ffc62e5" />
2.4 Tạo hàm TinhTongTienHoaDon


Cách sử dụng
SELECT dbo.fn_TinhTongTienHoaDon(1) AS TongTien;

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/446ba657-c273-4fb7-869c-4b3ca910cfc0" />
2.5 Hiển thị tổng tiền hóa đơn


Viết 1 Inline Table-Valued Function (ITVF)

Viết hàm lấy ra danh sách lịch sử đặt bàn của một khách hàng khi truyền vào Mã Khách Hàng.

Luồng xử lý tổng quát:

 Bước 1.

Hàm nhận vào mã khách hàng làm tham số đầu vào.

 Bước 2.

Hệ thống truy xuất dữ liệu từ bảng DatBan để lấy các thông tin liên quan đến các lần đặt bàn của khách hàng.

 Bước 3.

Đồng thời, hệ thống kết nối với bảng Ban để lấy thêm thông tin về loại bàn và sức chứa.

 Bước 4.

Thực hiện liên kết giữa hai bảng thông qua MaBan.

 Bước 5.

Lọc dữ liệu theo MaKhachHang đã truyền vào để chỉ lấy các giao dịch của khách đó.

 Bước 6.

Trả về danh sách kết quả bao gồm:

Mã đặt bàn

Loại bàn

Sức chứa

Thời gian đặt

Thời gian đến

Tiền cọc

CREATE FUNCTION fn_LichSuDatBan (@MaKhachHang INT)
RETURNS TABLE
AS
RETURN
(
    SELECT 
        db.MaDatBan,
        b.LoaiBan,
        b.SucChua,
        db.ThoiGianDat,
        db.ThoiGianDen,
        db.TienCoc
    FROM DatBan db
    JOIN Ban b ON db.MaBan = b.MaBan
    WHERE db.MaKhachHang = @MaKhachHang
);
GO

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/839d3fd7-ce3b-499d-9fcb-dcb596c21a8c" />
2.6 Tạo Function LichSuDatBan


--Xem lịch sử đặt bàn của khách hàng có mã số 1
SELECT * 
FROM dbo.fn_LichSuDatBan(1);

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/2404b8ca-ef87-404c-a6b5-3653040dbffd" />
2.7 Xem lịch sử đặt bàn của khách hàng có mã số 1


Viết 1 Multi-statement Table-Valued Function (MSTVF)

Ban quản lý cần một hàm thống kê tình trạng hoạt động của các bàn theo Tháng và Năm.

Phân loại:

≥ 3 lần → N'Bàn Hot'

1–2 lần → N'Bình thường'

0 lần → N'Ế - Cần khuyến mãi'

Luồng xử lý tổng quát:

Bước 1.

Hàm nhận vào hai tham số:

Tháng

Năm

Bước 2.

Hệ thống truy xuất danh sách tất cả các bàn trong bảng Ban.

Bước 3.

Kết nối với bảng DatBan để:

Lọc các lượt đặt theo tháng và năm

Đếm số lần mỗi bàn được đặt

 Bước 4.

Đưa dữ liệu vào bảng tạm gồm:

Mã bàn

Loại bàn

Số lần được đặt

Trạng thái (tạm thời)

Bước 5.

Dựa trên số lần đặt để phân loại:

≥ 3 → "Bàn Hot"

1–2 → "Bình thường"

0 → "Ế - Cần khuyến mãi"

 Bước 6.

Trả về bảng kết quả thống kê

CREATE FUNCTION fn_ThongKeBan (@Thang INT, @Nam INT)
RETURNS @KetQua TABLE
(
    MaBan VARCHAR(10),
    LoaiBan NVARCHAR(50),
    SoLanDat INT,
    DanhGia NVARCHAR(50)
)
AS
BEGIN
    -- Bước 2 + 3 + 4: Lấy dữ liệu và đếm số lần đặt
    INSERT INTO @KetQua (MaBan, LoaiBan, SoLanDat, DanhGia)
    SELECT 
        b.MaBan,
        b.LoaiBan,
        COUNT(db.MaDatBan) AS SoLanDat,
        N'' -- tạm thời
    FROM Ban b
    LEFT JOIN DatBan db 
        ON b.MaBan = db.MaBan
        AND MONTH(db.ThoiGianDat) = @Thang
        AND YEAR(db.ThoiGianDat) = @Nam
    GROUP BY b.MaBan, b.LoaiBan;

    -- Bước 5: Cập nhật phân loại
    UPDATE @KetQua
    SET DanhGia =
        CASE 
            WHEN SoLanDat >= 3 THEN N'Bàn Hot'
            WHEN SoLanDat BETWEEN 1 AND 2 THEN N'Bình thường'
            ELSE N'Ế - Cần khuyến mãi'
        END;

    -- Bước 6: Trả kết quả
    RETURN;
END;
GO

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/b3851073-ff17-4af8-a960-31d99112ff36" />
2.8 Tạo hàm ThongKeBan


--Lấy báo cáo các bàn được đặt tháng 1/2023
SELECT *
FROM dbo.fn_ThongKeBan(1, 2023);

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/aca17892-9b9e-46cd-9211-1dbc4f9b9ee1" />
2.9 Lấy báo cáo các bàn được đặt tháng 1/2023


Phần 3: Xây dựng Store Procedure

Stored Procedure hệ thống (System Stored Procedures)

Trong SQL Server, hệ quản trị cung cấp nhiều Stored Procedure hệ thống (System Stored Procedures) nhằm hỗ trợ quản trị, truy vấn thông tin và thao tác với cơ sở dữ liệu.

Các nhóm Stored Procedure phổ biến:

Nhóm quản lý cơ sở dữ liệu (Database Management)

Dùng để tạo, sửa, xóa hoặc cấu hình database
Ví dụ: tạo database, kiểm tra trạng thái, sao lưu dữ liệu

Nhóm quản lý bảng và đối tượng (Object Management)

Thao tác với bảng, view, index, constraint

Hỗ trợ xem cấu trúc hoặc thay đổi đối tượng trong database

Nhóm bảo mật (Security Management)

Quản lý user, login, quyền truy cập

Phân quyền và kiểm soát truy cập dữ liệu

Nhóm thông tin hệ thống (System Information)

Cung cấp thông tin về cấu trúc database, bảng, cột, index

Giúp kiểm tra metadata của hệ thống

Nhóm hiệu năng và giám sát (Performance & Monitoring)

Theo dõi hoạt động hệ thống

Kiểm tra truy vấn, cache, tài nguyên sử dụng

Đặc điểm chung của System Stored Procedures:

Thường có tiền tố sp_

Được tích hợp sẵn trong SQL Server

Có thể gọi trực tiếp mà không cần tự định nghĩa

Ví dụ một số Stored Procedure phổ biến:

sp_help: Xem thông tin chi tiết của bảng hoặc đối tượng

sp_tables: Liệt kê danh sách các bảng

sp_columns: Xem thông tin các cột trong bảng

sp_rename: Đổi tên đối tượng

Một vài System SP và cách dùng:

sp_helptext (Trình soi code ẩn):

Công dụng: Khi bạn có quá nhiều Trigger, View, hoặc các Function/SP cũ do người khác viết và bạn không biết bên trong họ code gì, SP này sẽ in ra toàn bộ mã nguồn (source code) của đối tượng đó.

EXEC sp_helptext 'fn_TinhTongTienHoaDon'; -- Trả về source code của function đã viết ở bài trước

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/ec32e02c-d735-4116-a19b-0133a58ed391" />
3.1 Trả về source code của function đã viết ở bài trước


sp_spaceused (Kế toán dung lượng):

Công dụng: Báo cáo nhanh xem một bảng dữ liệu hoặc toàn bộ Database đang chiếm bao nhiêu MB trên ổ cứng, và có chính xác bao nhiêu dòng dữ liệu (nhanh hơn nhiều so với việc dùng COUNT(*) trên bảng hàng triệu dòng).

EXEC sp_spaceused 'HoaDon';-- Kiểm tra xem bảng Hóa Đơn đang nặng bao nhiêu KB

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/5ff8a1d8-6ddc-4600-b032-aae7f88a75e9" />
3.2 Kiểm tra xem bảng Hóa Đơn đang nặng bao nhiêu KB


Viết 01 Store Procedure đơn giản để thực hiện lệnh INSERT hoặc UPDATE dữ liệu, có kiểm tra điều kiện logic

SP Thực hiện INSERT có kiểm tra logic

Ý tưởng:Đặt bàn Thần tốc (Flash Booking)

Tình huống: Khách hàng đến nhà hàng và muốn có bàn ngay theo một loại bàn cụ thể (VD: Bàn 4 người). Nhân viên không có thời gian tìm từng bàn.
Yêu cầu SP:

Nhân viên chỉ cần nhập:

Mã Khách Hàng

Loại Bàn

Hệ thống sẽ:
Tự động tìm bàn phù hợp còn trống

Nếu có → tạo đặt bàn ngay

Nếu không → thông báo hết bàn

Phân tích logic Stored Procedure sp_DatBanThanToc
Bước 1.

Procedure nhận vào hai tham số:

Mã khách hàng

Loại bàn mong muốn

Bước 2.

Hệ thống tiến hành tìm kiếm một bàn phù hợp:

Thuộc đúng loại bàn khách yêu cầu

Có trạng thái là Trống

Bao gồm cả các bàn chưa từng được đặt

Bước 3.

Sau khi tìm kiếm:

--Nếu tồn tại bàn trống:
Gán bàn đó cho khách hàng

Tạo một bản ghi đặt bàn mới với:

Thời gian đặt = thời điểm hiện tại (GETDATE())

Thời gian đến = thời điểm hiện tại

Tiền đặt cọc = 0

Đồng thời cập nhật trạng thái bàn → Đang sử dụng

 --Nếu không tìm thấy bàn phù hợp:

Thông báo: Không còn bàn trống cho loại yêu cầu

Bước 4.

Kết quả của procedure:

Trả về thông báo tương ứng với trạng thái thành công hoặc thất bại

CREATE PROCEDURE sp_DatBanThanToc
    @MaKhachHang INT,
    @LoaiBan NVARCHAR(50)
AS
BEGIN
    DECLARE @MaBan VARCHAR(10);

    -- Bước 2: Tìm bàn trống
    SELECT TOP 1 @MaBan = MaBan
    FROM Ban
    WHERE LoaiBan = @LoaiBan
        AND TrangThai = N'Trống';

    -- Bước 3: Kiểm tra
    IF @MaBan IS NOT NULL
    BEGIN
        -- Insert đặt bàn
        INSERT INTO DatBan (MaKhachHang, MaBan, ThoiGianDat, ThoiGianDen, TienCoc)
        VALUES (@MaKhachHang, @MaBan, GETDATE(), GETDATE(), 0);

        -- Update trạng thái bàn
        UPDATE Ban
        SET TrangThai = N'Đang sử dụng'
        WHERE MaBan = @MaBan;

        PRINT N'Đặt bàn thành công! Mã bàn: ' + @MaBan;
    END
    ELSE
    BEGIN
        PRINT N'Xin lỗi! Không còn bàn trống loại này.';
    END
END;
GO

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/cc641604-c98e-4826-a04a-006649f16004" />
3.3 Tạo Procedure sp_DatBanThanToc(Đặt bàn Thần tốc)


--Khách hàng mã 1 muốn đặt gấp 1 bàn VIP
EXEC sp_DatBanThanToc 1, N'Bàn VIP';

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/c3492943-d1ea-4910-9dbb-0989a9450ad8" />
3.4 Đặt bàn gấp cho khách hàng mã 1 , bàn VIP


Viết 01 Stored Procedure có tham số OUTPUT

SP có tham số OUTPUT

Ý tưởng (Scenario):"Vòng quay may mắn: Tính tỷ lệ giảm giá cho khách"
Tình huống: Nhà hàng có chương trình tri ân khách hàng. Khi khách thanh toán, hệ thống sẽ quay thưởng để xác định % giảm giá dựa trên điểm đánh giá của khách.

Yêu cầu SP:

Dựa vào DiemDanhGia (1–5 sao)

Khách 5 sao → giảm 20% – 40%

Khách 4 sao → giảm 5% – 15%

Khách < 4 sao → không giảm

 Kết quả trả về thông qua biến OUTPUT

 Phân tích logic Stored Procedure sp_QuayThuongGiamGia
 
Luồng xử lý tổng quát:
 
Bước 1.

Procedure nhận vào:

Mã khách hàng
Một biến OUTPUT để lưu % giảm giá
 Bước 2.

Hệ thống truy xuất DiemDanhGia từ bảng KhachHang.

 Bước 3.

Dựa trên điểm đánh giá:

-- Nếu = 5 sao:
Random từ 20 → 40
-- Nếu = 4 sao:
Random từ 5 → 15
-- Nếu < 4:
Giảm giá = 0
 Bước 4.

Gán giá trị giảm giá vào biến OUTPUT

 Bước 5.

Trả về kết quả thông qua OUTPUT

CREATE PROCEDURE sp_QuayThuongGiamGia
    @MaKhachHang INT,
    @PhanTramGiam INT OUTPUT
AS
BEGIN
    DECLARE @Diem INT;

    -- Bước 2: Lấy điểm đánh giá
    SELECT @Diem = DiemDanhGia
    FROM KhachHang
    WHERE MaKhachHang = @MaKhachHang;

    -- Bước 3: Xử lý logic
    IF @Diem = 5
    BEGIN
        SET @PhanTramGiam = FLOOR(RAND() * 21) + 20; -- 20 → 40
    END
    ELSE IF @Diem = 4
    BEGIN
        SET @PhanTramGiam = FLOOR(RAND() * 11) + 5; -- 5 → 15
    END
    ELSE
    BEGIN
        SET @PhanTramGiam = 0;
    END
END;
GO

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/a82d5425-46a5-425d-8edd-352bacb16cad" />
3.5 Tạo Procedure sp_QuayThuongGiamGia(Vòng quay may mắn: Tính tỷ lệ giảm giá cho khách)


-- Để chạy SP có OUTPUT, ta phải khai báo một biến để hứng kết quả
DECLARE @GiamGia INT;

-- Gọi hàm và truyền biến hứng vào 
EXEC sp_QuayThuongGiamGia 
    @MaKhachHang = 1,
    @PhanTramGiam = @GiamGia OUTPUT;

--Hiển thị kết quả
SELECT @GiamGia AS PhanTramGiam;

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/acb0681b-de58-4de9-ba3c-ae5f1c5da848" />
3.6 Vòng quay may mắn: giảm giá cho khách


Viết 01 Store Procedure trả về một tập kết quả (Result set) từ lệnh SELECT sau khi đã join nhiều bảng.
SP trả về Result Set (Lệnh SELECT JOIN nhiều bảng)
Ý tưởng (Scenario):Truy quét khách ăn nhanh 
Tình huống: Nhà hàng nhận thấy có một số khách đặt bàn VIP hoặc bàn lớn nhưng chỉ ngồi trong thời gian rất ngắn (≤ 1 giờ), chủ yếu để chụp ảnh check-in rồi rời đi.

→ Cần thống kê nhóm khách này để đưa ra chiến lược kinh doanh phù hợp.
Yêu cầu SP:
Trả về danh sách gồm:

Họ tên khách
Số điện thoại
Loại bàn
Thời gian vào
Thời gian ra
Tổng số phút sử dụng

-- Điều kiện:

Bàn VIP hoặc bàn lớn
Đã rời bàn (có ThoiGianRa)
Thời gian sử dụng ≤ 60 phút
JOIN nhiều bảng


Phân tích logic Stored Procedure sp_BaoCaoKhachHangAnNhanh
Luồng xử lý tổng quát:
 Bước 1.

Hệ thống truy xuất dữ liệu từ các bảng:

HoaDon (thời gian sử dụng)
KhachHang (thông tin khách)
Ban (loại bàn)
 Bước 2.

Thực hiện liên kết:

HoaDon.MaKhachHang = KhachHang.MaKhachHang
HoaDon.MaBan = Ban.MaBan
 Bước 3.

Lọc dữ liệu:

Loại bàn: VIP hoặc bàn lớn
ThoiGianRa IS NOT NULL
Thời gian sử dụng ≤ 60 phút
 Bước 4.

Tính toán:

Dùng DATEDIFF(MINUTE, ThoiGianVao, ThoiGianRa)
 Bước 5.

Sắp xếp:

Theo thời gian sử dụng tăng dần
 Bước 6.

Trả về kết quả (Result Set)

CREATE PROCEDURE sp_BaoCaoKhachHangAnNhanh
AS
BEGIN
    SELECT 
        kh.HoTenKhachHang,
        kh.SoDienThoai,
        b.LoaiBan,
        hd.ThoiGianVao,
        hd.ThoiGianRa,
        DATEDIFF(MINUTE, hd.ThoiGianVao, hd.ThoiGianRa) AS SoPhut
    FROM HoaDon hd
    JOIN KhachHang kh ON hd.MaKhachHang = kh.MaKhachHang
    JOIN Ban b ON hd.MaBan = b.MaBan
    WHERE 
        b.LoaiBan IN (N'Bàn VIP', N'Bàn lớn')
        AND hd.ThoiGianRa IS NOT NULL
        AND DATEDIFF(MINUTE, hd.ThoiGianVao, hd.ThoiGianRa) <= 60
    ORDER BY SoPhut ASC;
END;
GO

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/6c183a6e-5de1-49e9-8688-feac6f86bfb2" />
3.7 Tạo Procedure sp_BaoCaoKhachHangAnNhanh


EXEC sp_BaoCaoKhachHangAnNhanh;

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/327498e1-9c65-438d-91dc-c926a2e9fc57" />
3.8 Liệt kê danh sách khách hàng ăn nhanh


Phần 4: Trigger và Xử lý logic nghiệp vụ
YÊU CẦU 1: Viết 01 Trigger tự động cập nhật bảng B khi bảng A thay đổi
Ý tưởng (Scenario): Đưa vào Sổ Đen (Blacklist) khi khách quậy phá / không thanh toán
Tình huống thực tế:

Trong nhà hàng, có một số khách:

Quậy phá
Không thanh toán
Làm hỏng tài sản

-- Khi nhân viên đánh giá khách 1 sao (DiemDanhGia = 1)
→ Hệ thống cần:

Cấm khách đặt bàn trong tương lai
Xóa các lượt đặt bàn chưa diễn ra
Đưa khách vào Blacklist


Phân tích logic Trigger trg_KhachHang_Blacklist
Luồng xử lý tổng quát:
Bước 1. Trigger được kích hoạt khi có thao tác cập nhật dữ liệu trên bảng khách hàng.

Bước 2. Hệ thống kiểm tra xem cột điểm đánh giá có bị thay đổi hay không.

Bước 3. Nếu có thay đổi, hệ thống xác định danh sách các khách hàng vừa được cập nhật có điểm đánh giá bằng 1.

Bước 4. Với các khách hàng này, hệ thống thực hiện:

Xác định các lượt đặt phòng trong tương lai (chưa đến thời gian nhận phòng)
Xóa các lượt đặt phòng đó để giải phóng phòng cho khách khác
Bước 5. Sau khi xử lý dữ liệu đặt phòng, hệ thống tiếp tục:

Cập nhật trạng thái của các khách hàng này thành trạng thái bị cấm (Blacklist)
Bước 6. Hoàn tất quá trình, đảm bảo:

Khách hàng có điểm đánh giá thấp không còn các đặt phòng trong tương lai
Trạng thái của khách được cập nhật để phục vụ các xử lý tiếp theo trong hệ thống
-- Bước 1: Thêm cột TrangThai vào bảng KhachHang
ALTER TABLE [KhachHang] ADD [TrangThai] NVARCHAR(50) DEFAULT N'Bình thường';
GO

-- Bước 2: Viết Trigger
ALTER TRIGGER trg_KhachHang_Blacklist
ON KhachHang
AFTER UPDATE
AS
BEGIN
    IF EXISTS (
        SELECT 1
        FROM inserted i
        JOIN deleted d ON i.MaKhachHang = d.MaKhachHang
        WHERE i.DiemDanhGia = 1 
          AND d.DiemDanhGia <> 1
    )
    BEGIN
        DELETE db
        FROM DatBan db
        JOIN inserted i ON db.MaKhachHang = i.MaKhachHang
        WHERE db.ThoiGianDen > GETDATE();

        UPDATE KhachHang
        SET TrangThai = N'Blacklist (Cấm cửa)'
        WHERE MaKhachHang IN (
            SELECT MaKhachHang 
            FROM inserted 
            WHERE DiemDanhGia = 1
        );
    END
END;
GO

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/c0ec0467-7904-43f7-b2c2-3e4397a27c01" />
4.1 Tạo Trigger trg_KhachHang_Blacklist


VD: Giả sử Khách hàng số 1 đang có điểm đánh giá bình thường. Trong một lần đến nhà hàng, khách có hành vi quậy phá -> Nhân viên tiến hành cập nhật điểm đánh giá xuống 1 sao.

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/e5b39d29-265a-407b-8b8d-4c80d0583774" />
4.2 Khách hàng số 1 thấy cột TrangThai tự động đổi thành 'Blacklist (Cấm cửa)'


YÊU CẦU 2: Tình huống Trigger "Vòng Lặp" (A gọi B, B gọi A)
Ý tưởng (Scenario): Cuộc chiến Khuyến mãi – Điểm tích lũy & Hoàn tiền

Logic:
Bảng A (KhachHang): thêm cột DiemTichLuy
Bảng B (HoaDon): thêm cột TienHoanLai

Luồng xử lý tổng quát:
 Bước 1.

Khi có INSERT vào HoaDon
→ hệ thống thưởng +10 điểm cho khách

 Bước 2.

Khi DiemTichLuy tăng lên
→ hệ thống quay lại HoaDon

 Bước 3.

Cập nhật:

TienHoanLai = 50000
Cho hóa đơn gần nhất
-- NGUY HIỂM: VÒNG LẶP VÔ HẠN

-- Nếu không xử lý:

Trigger A gọi B
B lại gọi A
→ loop vô hạn → crash SQL

CÁCH CHẶN LOOP
IF TRIGGER_NESTLEVEL() > 1 RETURN;

BƯỚC 1: Thêm cột
ALTER TABLE KhachHang
ADD DiemTichLuy INT DEFAULT 0;

ALTER TABLE HoaDon
ADD TienHoanLai MONEY DEFAULT 0;

Trigger 1: (HoaDon → KhachHang)
CREATE TRIGGER trg_HoaDon_CongDiem
ON HoaDon
AFTER INSERT
AS
BEGIN
    -- Chặn loop
    IF TRIGGER_NESTLEVEL() > 1 RETURN;

    UPDATE kh
    SET DiemTichLuy = ISNULL(DiemTichLuy, 0) + 10
    FROM KhachHang kh
    JOIN inserted i ON kh.MaKhachHang = i.MaKhachHang;
END;
GO

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/6ef9d0cf-c49a-40f8-9b9d-6fef19219d67" />
4.3 Tạo Trigger 1: (HoaDon → KhachHang)


Trigger 2: (KhachHang → HoaDon)
CREATE TRIGGER trg_KhachHang_HoanTien
ON KhachHang
AFTER UPDATE
AS
BEGIN
    -- Chặn loop
    IF TRIGGER_NESTLEVEL() > 1 RETURN;

    IF UPDATE(DiemTichLuy)
    BEGIN
        UPDATE hd
        SET TienHoanLai = 50000
        FROM HoaDon hd
        JOIN (
            SELECT MaKhachHang, MAX(MaHoaDon) AS MaxHD
            FROM HoaDon
            GROUP BY MaKhachHang
        ) t ON hd.MaHoaDon = t.MaxHD
        JOIN inserted i ON hd.MaKhachHang = i.MaKhachHang;
    END
END;
GO

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/9a3508cb-fab5-421f-912f-da9c8192a219" />
4.4 Tạo Trigger 2: (KhachHang → HoaDon)


TEST HOẠT ĐỘNG
-- Tạo hóa đơn mới
INSERT INTO HoaDon (MaKhachHang, MaBan, ThoiGianVao)
VALUES (1, 'B01', GETDATE());

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/a3758009-3362-41c8-bbcd-7e874d088bc9" />
4.5 Tạo hóa đơn mới


KẾT QUẢ
SELECT MaKhachHang, DiemTichLuy 
FROM KhachHang WHERE MaKhachHang = 1;

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/e0c347ed-a998-4c37-9c82-b4ff656eb6e7" />
4.6

SELECT * FROM HoaDon WHERE MaKhachHang = 1;
<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/bbe0e9cc-30e0-48ea-ab3c-5ac07abd8578" />
4.7

Phân tích kết quả:
--Hai Trigger được thiết kế để phối hợp tự động giữa bảng KhachHang và HoaDon. Khi phát sinh giao dịch mới, hệ thống tự động cộng điểm cho khách hàng. Ngược lại, khi điểm tích lũy thay đổi, hệ thống cập nhật hoàn tiền cho hóa đơn gần nhất. Đồng thời, cơ chế kiểm soát vòng lặp được áp dụng để đảm bảo hệ thống hoạt động ổn định.


Phần 5: Cursor và Duyệt dữ liệu
YÊU CẦU 1: Viết một đoạn script sử dụng CURSOR và không dùng CURSOR để duyệt qua danh sách của 1 câu lệnh SQL dạng SELECT, duyệt qua từng bản ghi, xử lý riêng từng bản ghi, so sánh
Tình huống:

Trong năm qua, nhà hàng nhận nhiều phản hồi không tốt từ khách hàng. Ban quản lý quyết định trích một quỹ đền bù trị giá 3.000.000 VNĐ để hỗ trợ những khách có trải nghiệm chưa hài lòng.

Hệ thống cần:

-Duyệt danh sách khách hàng có DiemDanhGia < 4
-Sắp xếp theo mức độ không hài lòng (tăng dần)
-Mỗi khách được tặng 500.000 VNĐ (TienVoucher)
-Dừng ngay khi quỹ hết (không chi vượt ngân sách)
Chuẩn bị dữ liệu:
-- Thêm cột voucher
ALTER TABLE KhachHang 
ADD TienVoucher MONEY DEFAULT 0;
GO

-- Tạo dữ liệu test (khách đánh giá thấp)
UPDATE KhachHang 
SET DiemDanhGia = 2 
WHERE MaKhachHang IN (5, 10, 15, 20, 25, 30, 35, 40);

Sử dụng Cursor
Cầm 3 triệu trên tay, gặp khách 1 đưa 500k (còn 2.5tr), gặp khách 2 đưa 500k... cứ thế đến khi hết tiền thì thoát vòng lặp.

Phân tích logic sử dụng CURSOR "Phân phát Quỹ đền bù rủi ro"
Luồng xử lý tổng quát:
 Bước 1. Khởi tạo:
Quỹ: 3.000.000
Mỗi khách: 500.000
 Bước 2. Lấy danh sách khách:
DiemDanhGia < 4
Sắp xếp tăng dần
 Bước 3. Duyệt từng khách:
Sử dụng CURSOR
 Bước 4. Kiểm tra quỹ:
Nếu còn đủ tiền → tiếp tục
 Bước 5. Cấp voucher:
Cộng tiền vào TienVoucher
Trừ quỹ
 Bước 6. Nếu hết tiền:
Dừng ngay (BREAK)
 Bước 7. Lặp:
Đến khi hết khách hoặc hết quỹ
 Bước 8. Kết thúc:
Đảm bảo không chi vượt ngân sách

DECLARE @MaKH INT;
DECLARE @QuyConLai MONEY = 3000000;
DECLARE @TienMoiKhach MONEY = 500000;

DECLARE cur_KH CURSOR FOR
SELECT MaKhachHang
FROM KhachHang
WHERE DiemDanhGia < 4
ORDER BY DiemDanhGia ASC;

OPEN cur_KH;

FETCH NEXT FROM cur_KH INTO @MaKH;

WHILE @@FETCH_STATUS = 0
BEGIN
    IF @QuyConLai < @TienMoiKhach
        BREAK;

    UPDATE KhachHang
    SET TienVoucher = ISNULL(TienVoucher, 0) + @TienMoiKhach
    WHERE MaKhachHang = @MaKH;

    SET @QuyConLai = @QuyConLai - @TienMoiKhach;

    FETCH NEXT FROM cur_KH INTO @MaKH;
END

CLOSE cur_KH;
DEALLOCATE cur_KH;

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/f82927ea-afa4-4638-9dda-f2e3f7f262f3" />
5.1 Khách có điểm đánh giá <4 lần lượt nhận TienVoucher


Phân tích logic không dùng CURSOR (Set-based) "Phân phát Quỹ đền bù rủi ro"
Luồng xử lý tổng quát:
 Bước 1. Hệ thống xác định danh sách khách hàng:
Chỉ lấy những khách có DiemDanhGia < 4
Sắp xếp theo mức độ không hài lòng (tăng dần)
 Bước 2. Thực hiện tính toán lũy kế:
Sử dụng hàm cửa sổ SUM() OVER (ORDER BY ...)
Với mỗi khách, tính tổng tiền lũy kế nếu phát voucher từ người đầu tiên đến người đó
 Bước 3. Xác định điều kiện nhận thưởng:
Những khách có tổng tiền lũy kế ≤ 3.000.000 sẽ được nhận voucher
 Bước 4. Thực hiện cập nhật:
Cập nhật đồng loạt TienVoucher + 500.000 cho các khách thỏa điều kiện
 Bước 5. Kết thúc:
Quỹ được phân bổ đúng giới hạn
Không cần duyệt từng bản ghi riêng lẻ

USE QuanLyNhaHang_K235480106033;
GO

-- Sử dụng CTE để tính toán lũy kế trước khi cập nhật
WITH DanhSachChoVoucher AS (
    SELECT 
        MaKhachHang,
        TienVoucher,
        -- Bước 2: Tính tổng tiền lũy kế (mỗi người 500k) 
        -- Sắp xếp theo DiemDanhGia tăng dần (người đánh giá thấp nhất được ưu tiên trước)
        SUM(500000) OVER (ORDER BY DiemDanhGia ASC, MaKhachHang ASC) AS TongTienLuyKe
    FROM KhachHang
    WHERE DiemDanhGia < 4
)
-- Bước 4: Thực hiện cập nhật đồng loạt cho những người thỏa điều kiện ngân sách
UPDATE KhachHang
SET TienVoucher = ISNULL(KhachHang.TienVoucher, 0) + 500000
FROM KhachHang
INNER JOIN DanhSachChoVoucher ON KhachHang.MaKhachHang = DanhSachChoVoucher.MaKhachHang
WHERE DanhSachChoVoucher.TongTienLuyKe <= 3000000; -- Bước 3: Chỉ lấy khách trong giới hạn quỹ 3 triệu

-- Kiểm tra kết quả sau khi cập nhật
SELECT MaKhachHang, DiemDanhGia, TienVoucher
FROM KhachHang
WHERE DiemDanhGia < 4
ORDER BY DiemDanhGia ASC;

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/40748cca-8118-420a-abb0-0253a6d8dd2a" />
5.2 Không dùng cursor vẫn giải quyết được bài toán phát TienVoucher


So sánh và nhận xét hiệu năng:
<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/a58c5afd-84ee-4805-80d8-dc76370c65c7" />
5.3 Hiệu năng khi dùng CURSOR


<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/d9ce9e00-a4aa-4889-ac84-913d3bdbf6ac" />
5.4 Hiệu năng khi dùng Set-based (không dùng CURSOR)

Sử dụng CURSOR:

Thời gian xử lý thường chậm hơn, thể hiện qua CPU time và Elapsed time cao.
Dữ liệu được xử lý theo từng dòng (row-by-row), không tận dụng được khả năng xử lý tập hợp của hệ quản trị.
Mỗi lần lặp, hệ thống phải thực thi riêng lẻ các câu lệnh, dẫn đến phát sinh chi phí xử lý lặp lại.
Gây ra hiện tượng chuyển đổi ngữ cảnh liên tục giữa các thao tác, làm giảm hiệu năng tổng thể.
Sử dụng Set-based (không dùng CURSOR):

Thời gian xử lý rất nhanh, gần như không đáng kể với tập dữ liệu vừa và lớn.
Dữ liệu được xử lý theo dạng tập hợp (set), đúng với cách tối ưu của SQL Server.
Hệ thống chỉ cần biên dịch và tối ưu câu lệnh một lần, sau đó thực thi đồng loạt.
Hiệu năng vượt trội, có thể nhanh hơn nhiều lần so với CURSOR khi xử lý dữ liệu lớn.
Kết luận:

Nên ưu tiên sử dụng phương pháp Set-based trong hầu hết các trường hợp.
CURSOR chỉ nên dùng khi bài toán yêu cầu xử lý tuần tự từng bản ghi mà không thể giải quyết bằng truy vấn tập hợp.


YÊU CẦU 2: XÂY DỰNG BÀI TOÁN MÀ CHỈ CURSOR MỚI GIẢI QUYẾT ĐƯỢC

Ý tưởng (Scenario): "Tự động phân tách lịch sử hóa đơn theo Tháng - Năm"
Tình huống:
Bảng HoaDon của nhà hàng sau nhiều năm hoạt động đã tích lũy khối lượng dữ liệu khổng lồ, làm chậm tốc độ xuất hóa đơn và báo cáo cuối ngày. Giám đốc yêu cầu: "Hãy dọn dẹp bảng chính. Cứ mỗi Tháng/Năm, hãy tự động tạo một bảng lưu trữ riêng (ví dụ: LichSuHD_2023_12) để cất dữ liệu cũ vào đó."

Tại sao Set-based (SQL thường) bó tay?
SQL Server yêu cầu tên bảng trong câu lệnh INSERT INTO hoặc SELECT INTO phải là một hằng số cố định. Bạn không thể viết một lệnh SQL thuần túy nào mà có thể tự động "đẻ" ra tên bảng mới dựa trên dữ liệu ngày tháng của từng tờ hóa đơn. Đây là "đất diễn" độc quyền của CURSOR kết hợp với Dynamic SQL.

Phân tích logic hệ thống CURSOR "Lưu trữ dữ liệu theo Tháng - Năm"
Bước 1. Xác định danh sách thời gian: Hệ thống quét bảng HoaDon để lấy ra tất cả các cặp (Tháng, Năm) duy nhất đã phát sinh giao dịch.

Bước 2. Khởi tạo cơ chế duyệt: Sử dụng CURSOR để "cầm" từng cặp (Tháng, Năm) này đi xử lý tuần tự.

Bước 3. Xây dựng tên bảng động: Với mỗi mốc, hệ thống tự ghép chuỗi để tạo tên bảng lưu trữ tương ứng (Ví dụ: LichSuHD_2024_05).

Bước 4. Kiểm tra hạ tầng: Nếu bảng lưu trữ của tháng đó chưa tồn tại, hệ thống sẽ tự động thực hiện lệnh CREATE TABLE.

Bước 5. Di trú dữ liệu: * Copy toàn bộ hóa đơn của tháng/năm đó sang bảng lưu trữ tương ứng.

Xóa dữ liệu cũ tại bảng HoaDon gốc để làm nhẹ hệ thống.

Bước 6. Lặp lại: Tiếp tục với các mốc thời gian còn lại cho đến khi sạch bảng gốc.

Bước 7. Kết thúc: Hoàn tất việc phân tách dữ liệu vào các "kho" riêng biệt.

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/2a446c0a-c100-4d61-abd4-c32a90987188" />
5.5 Dùng cursor đóng gói dữ liệu (Archive) động theo từng Năm và Tháng


<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/3a31f4b8-6b30-49fa-8455-8937da9c1c0c" />
5.6 Kiểm tra kết quả bảng sau khi xử lí
