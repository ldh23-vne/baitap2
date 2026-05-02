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
                        Tạo cơ sở dữ liệu QuanLyNhaHang_K235480106033

- Bảng [KhachHang]

👉Sử dụng MaKhachHang làm Khóa chính (PK), tự động tăng (IDENTITY(1,1)).
👉Họ tên dùng NVARCHAR để hỗ trợ tiếng Việt có dấu.
👉Điểm đánh giá (DiemDanhGia) có ràng buộc CHECK từ 1 đến 5 sao.

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
                                Tạo bảng KhachHang

-Bảng [Ban]
👉MaBan: khóa chính (PK), tự tăng IDENTITY(1,1)
👉TenBan: tên bàn (VD: Bàn 1, Bàn VIP A)
👉LoaiBan: phân loại (2 người, 4 người, VIP…)
👉SucChua: số người tối đa
👉TrangThai: trạng thái bàn
→ mặc định là Trống 

[CREATE TABLE [Ban] (
    [MaBan] INT IDENTITY(1,1) NOT NULL,
    [TenBan] NVARCHAR(50) NOT NULL,
    [LoaiBan] NVARCHAR(50) NOT NULL,
    [SucChua] INT NOT NULL,
    [TrangThai] NVARCHAR(20) DEFAULT N'Trống',
    
    CONSTRAINT [PK_Ban] PRIMARY KEY ([MaBan]),
    CONSTRAINT [CK_SucChua] CHECK ([SucChua] > 0),
    CONSTRAINT [CK_TrangThai] CHECK ([TrangThai] IN (N'Trống', N'Đã đặt', N'Đang sử dụng'))
);]

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/11ebde55-51c1-4662-a209-a0282047a1d0" />
Tạo bảng Ban


-Bảng [MonAn]
👉Sử dụng MaMon làm khóa chính (PK), tự tăng IDENTITY(1,1)
👉Tên món dùng NVARCHAR để hỗ trợ tiếng Việt
👉Giá món dùng MONEY, có ràng buộc > 0
👉Trạng thái món: Còn / Hết

[CREATE TABLE [MonAn] (
    [MaMon] INT IDENTITY(1,1) NOT NULL,
    [TenMon] NVARCHAR(100) NOT NULL,
    [Gia] MONEY NOT NULL,
    [LoaiMon] NVARCHAR(50),
    [TrangThai] NVARCHAR(20) DEFAULT N'Còn',

    CONSTRAINT [PK_MonAn] PRIMARY KEY ([MaMon]),
    CONSTRAINT [CK_GiaMon] CHECK ([Gia] > 0),
    CONSTRAINT [CK_TrangThaiMon] CHECK ([TrangThai] IN (N'Còn', N'Hết'))
);]


<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/b45e2baf-2130-434f-bd8e-c07381bbe813" />
Tạo bảng Mon An  

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
Tạo bảng Dat Ban


-Bảng [HoaDon] 

👉Mỗi hóa đơn = 1 lần khách gọi món

👉Liên kết với bàn và khách hàng

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
Tạo bảng Hóa Đơn  

-Bảng [ChiTietHoaDon] (Chi tiết món ăn)

👉 Đây là bảng quan trọng nhất để lưu món

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
Tạo bảng Chi Tiet Hoa Don

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
Lấy ngẫu nhiên 1 bàn để gợi ý cho khách

CHOOSE(): Trả về một mục từ một danh sách dựa trên chỉ mục (giống như mảng). Giúp tránh phải viết CASE WHEN dài dòng.
--Phân loại bàn theo số ngẫu nhiên
SELECT CHOOSE(2,
    N'Bàn 2 người',
    N'Bàn 4 người',
    N'Bàn 6 người',
    N'Bàn VIP') AS LoaiBan;
    <img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/10933f1c-a9ae-4719-9f95-c265005fe29e" />
Phân loại bàn theo số ngẫu nhiên

DATEDIFF(): Tính khoảng cách giữa 2 mốc thời gian (theo ngày, tháng, năm, giờ...). Vô cùng quan trọng trong bài toán quản lý khách sạn.
--Tính thời gian khách ngồi bàn (theo giờ)
SELECT 
    MaHoaDon,
    DATEDIFF(HOUR, ThoiGianVao, ThoiGianRa) AS SoGioSuDung
FROM HoaDon;
<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/0083ae14-dc8f-4929-98ac-8e5c7d49503c" />
