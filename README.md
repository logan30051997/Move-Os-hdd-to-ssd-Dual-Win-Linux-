# Hướng dẫn chuyển HĐH Windows và Linux từ ổ HDD sang SSD

## I. Chuyển HĐH Windows và phân vùng dual boot sang SSD

### 1. Tải phần mềm Macrium Reflect

- Download tại: [Reflect 7 Free Commercial](https://www.macrium.com/reflectfree)

### 2. Clone phân vùng chưa HĐH Windows và boot 

- Chọn Disk có phân vùng của hđh và clone sang Disk ssd. 

<img src="https://i.imgur.com/ji0JupS.pngS">

- Các phân vùng Boot và ổ C sẽ đc clone sang Disk ssd đích

Chọn Source là Disk HDD chứa HĐH Windows và Destination là Disk SSD cần clone sang

<img src="https://i.imgur.com/LHj5F5A.png">

- Đợi quá trình clone, tùy thuộc vào tốc độ và dung lượng của ổ

### 3. Hoàn tất quá trình clone, kiểm tra menu Boot

- Khi hoàn tất clone sang SSD, ta cần vào trong Mode Boot (Vào chế độ Boot khi bắt đầu khởi động máy), thấy Boot SSD của Windows

- Đăng nhập vào, khởi tạo và chạy Boot của HĐH ở SSD, kiểm tra Disk Management. Thấy 2 phân vùng hđh Windows ở cả ổ HDD cũ và SDD mới clone sang.

- Xóa phân vùng Windows ở HDD để tránh nhầm lẫn, Delete Volume...


## II. Chuyển HĐH Linux sang SSD
