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

### 1. Kiểm tra phân vùng và các Disk

- Dùng lệnh `fdisk` để kiểm tra: 

`sudo fdisk -l`

<img src="https://i.imgur.com/SpJhM0f.png">

Thấy 2 phân vùng /dev/sda (HDD) và /dev/sdb (SSD)

## 2.Tạo phân vùng trên /dev/sdb (SSD) để lưu OS của Linux (Phân vùng trên SSD phải >= phân vùng OS cũ trên HDD)

- **Bước 1**: Dùng lệnh `fdisk` để tạo:

`sudo fdisk /dev/sdb`

<img src="https://i.imgur.com/sIlQ1dn.png">


- Trong đó các tùy chọn:

n: Tạo phân vùng mới

Partition number: số phân vùng 

First sector: Vị trí sector lưu đầu tiên 

Last sector: Vị trí lưu sector cuối (tương ứng số GB cung cấp cho ổ đĩa)

w: Lưu lại thay đổi tạo 

- **Bước 2**: sau đó confirm với hđh phân vùng được tạo bằng lệnh 

`partprobe /dev/sdb`

- **Bước 3**: Bảng phân vùng đã được cập nhật. Chúng ta phải định dạng phân vùng của để sử dụng. Hệ thống định dạng tệp được hỗ trợ Linux là ext4. Lệnh sau để định dạng phân vùng sdbX với ext4.

` sudo mkfs.ext4 /dev/sdbX` (X là số phân vùng vừa tạo ở trên)

## 3. Sao lưu từ phân vùng `root` chứa HĐH từ  HDD cũ sang SDD 

- Sử dụng commnad `dd`

` sudo dd sudo dd if=/dev/sdaX of=/dev/sdbY conv=noerror,sync`

- Trong đó:

if=/dev/sdaX: Nguồn, phân vùng chứa  `/`root của HĐH chạy trên disk HDD (X là sô vị̣ trí của phân vùng chứa)

of=/dev/sdbY: Đích, phân vùng chứa `/` root của HĐH chạy trên disk SSD mới (Y là số vị trí của phân vùng SSD mà mình vừa tạo ở bước trên)

conv=noerror,sync: Là option của command dd hỗ trợ tiếp tục chạy nếu có lỗi và sync 

## 4. Sau khi hoàn tất kiểm tra Phân vùng mới được tạo và UUID của phân vùng

- **Bước 1**: Kiểm tra UUID của phân vùng mới tạo bằng lệnh `blkid`

`sudo blkid | grep sd`

Ta sẽ thấy list các phân vùng và UUID của từng phần vùng

*Vì khi ta clone phân vùng Boot của Windows ở phần I và sự dụng lệnh dd  để sao chép từ /dev/sda (disk HDD) sang /dev/sdb (disk SDD) sẽ có cùng UUID với nhau 
*

- **Bước 2**: Kiểm tra trong `/etc/fstab` xem phân vùng boot và / đã đúng UUID ở phân vùng ỏ SSD hay chưa.

`sudo vim /etc/fstab`

<img src="https://i.imgur.com/FQArNvy.png">

- **Bước 3**: Xóa phân vùng chứa HĐH ở ổ HDD cũ để tránh lỗi trùng UUID

`sudo fdisk /dev/sda (disk HDD)`

<img src="https://i.imgur.com/ANvv5T5.png">

- Trong đó:

d: Tùy chọn xóa phân vùng

Partition number: Số thứ tự phân vùng cần xóa (Lưu ý kiểm tra `fdisk -l` để chắc chắn)

w: Lưu thay đổi

`Các phân vùng cần xóa ở ổ HDD cũ là: Boot (Windows recovery environment), EFI System, Root Linux filesystem
)
`

- **Bước 4**: Load lại Grub để cập nhật

`sudo update-grub`

## 5. Khởi động lại và chỉnh sửa thứ tự Boot ưu tiên trong Bios 

- Sau khi vào lại phân vùng đã chuyển qua SSD thì sẽ vào chế độ BusyBox, vì sau khi chúng ta xóa phân vùng cũ phải cập nhật lại phân vùng mới cho HĐH hiểu.

<img src="https://i.imgur.com/qNz7C4r.png">


- Gõ câu lệnh để cập nhật lại phân vùng 

`fsck /dev/sdaX -y (Trong đó X là phân vùng OS root bạn vừa tạo ở trên SSD)`

- Sau khi chạy xong gõ `exit` để thoát và khởi động vào OS bình thường.


# III. Hoàn tất chuyển dual Windows và Linux từ HDD sang SSD.

 **Kiểm tra lại khi khởi động lại máy Boot option đã đúng chạy dual Windows và Linux chưa** 
