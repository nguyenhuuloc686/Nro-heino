Đây là phiên bản đầu tiên có lỗi hay gì anh em cứ vào box trao đổi với mình❤️
Link Box: https://zalo.me/g/nran3u1pi3hgm9mq5mpc
Mã nguồn game:https://drive.google.com/file/d/1SWQYXWfEoOcKOVWki7SzcA_-ibDwWw-M/view?usp=drivesdk

Sql:https://drive.google.com/file/d/1Qh6cevyZYJg5x7dGpF5jTvW0ZvhtSdMi/view?usp=drivesdk

navicat:https://drive.google.com/file/d/1Qol_88hyiSJJitKKZVqib6Cdu3lXuUP2/view?usp=drivesdk

termux:https://drive.google.com/file/d/1QrUrDNnrKbPdlAEcoigPhsbEIhm0nW50/view?usp=drivesdk

# Hướng Dẫn Cài Đặt, Bổ Sung Patch Navicat & Sửa Lỗi Server Heino NRO Trên Termux

Tài liệu tổng hợp quy trình cài đặt, nâng cấp Web Panel quản lý Database và cách xử lý tất cả các lỗi thường gặp khi vận hành Server Heino Ngọc Rồng Online trên Android (Termux).

---

## 📋 Yêu Cầu Chuẩn Bị
Tải sẵn các file sau vào thư mục **Download** (`/sdcard/Download/`) trên điện thoại:
1. File source game: `heino.rar` (hoặc `heino.zip`, `heino (2).rar`).
2. File bản vá Termux: `Heino-Termux-Patch.zip`.
3. File bản vá Web Panel Navicat: `Heino-Navicat-Patch.zip`.
4. File Cơ sở dữ liệu: `heino.sql`.

---

## ⚡ 1. Các Câu Lệnh Cài Đặt & Vận Hành Từ A - Z

### Bước 1: Cài đặt môi trường ban đầu
Mở Termux, dán lệnh sau và chọn **Cho phép (Allow)** khi máy hỏi quyền truy cập bộ nhớ:
```bash
termux-wake-lock && termux-setup-storage && pkg update -y && pkg install -y openjdk-21 mariadb curl lsof termux-tools unar unzip nano

```
### Bước 2: Giải nén Source & Fix lỗi tên file Viết Hoa/Thường
```bash
cd ~ && mkdir -p heino && cd heino
unar /sdcard/Download/heino*.rar -f
cd ~/heino/"heino"/heino
unzip -o /sdcard/Download/Heino-Termux-Patch.zip 2>/dev/null || true
chmod +x run-termux.sh termux-setup.sh 2>/dev/null || true
cd ~/heino/"heino"/heino/data/map && mv tile_set_Info tile_set_info 2>/dev/null || true

```
### Bước 3: Nâng cấp Web Panel Database kiểu Navicat (Tùy chọn)
```bash
cd ~/heino/"heino"/heino
unzip -o /sdcard/Download/Heino-Navicat-Patch.zip
chmod +x run-termux.sh

```
### Bước 4: Khởi chạy MariaDB, Reset Mật Khẩu Root & Nạp Database
```bash
pkill -f mysqld; pkill -f mariadbd
mariadbd-safe &
sleep 5
mariadb -uroot -e "ALTER USER 'root'@'localhost' IDENTIFIED BY ''; FLUSH PRIVILEGES;"
mariadb -uroot -e "CREATE DATABASE IF NOT EXISTS heino CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;"
mariadb -uroot heino < /sdcard/Download/heino.sql

```
### Bước 5: Chạy Server Game
```bash
cd ~/heino/"heino"/heino
java -jar cc2.jar

```
*(Hoặc chạy lệnh: ./run-termux.sh)*
## 🌐 2. Hướng Dẫn Sử Dụng Web Panel Quản Lý (Navicat)
 * **Truy cập:** Mở trình duyệt Chrome trên điện thoại, truy cập http://127.0.0.1:8080.
 * **Đăng nhập:** Bằng tài khoản Admin game (admin >= 1 hoặc is_admin = 1).
 * **Tính năng tab Database:**
   * **Xem dữ liệu:** Chọn bảng ở menu bên trái, dùng ô tìm kiếm hoặc sắp xếp theo cột.
   * **Chỉnh sửa:** Bấm đúp vào ô dữ liệu để sửa nhanh, hoặc dùng Modal Thêm/Sửa/Xóa dòng.
   * **SQL Console:** Thực thi các câu lệnh SQL tự do. Để chạy lệnh can thiệp lớn (ALTER, DROP, DELETE), tài khoản cần đạt cấp Super Admin (admin >= 3).
## 🛠️ 3. Tổng Hợp Các Lỗi Thường Gặp & Cách Sửa Triệt Để
### 1. Lỗi FileNotFoundException: data/map/tile_set_info
 * **Nguyên nhân:** Do hệ điều hành Linux/Termux phân biệt chữ hoa/chường, trong file nén tên là tile_set_Info (chữ **I** viết hoa) nên Java không tìm thấy.
 * **Cách khắc phục:**
   ```bash
   cd ~/heino/"heino"/heino/data/map && mv tile_set_Info tile_set_info
   
   ```
### 2. Lỗi ERROR 2002: Can't connect to local server through socket hoặc HikariPool - Connection failed
 * **Nguyên nhân:** Dịch vụ MariaDB chưa được bật hoặc tiến trình MariaDB bị treo.
 * **Cách khắc phục:**
   ```bash
   pkill -f mysqld; pkill -f mariadbd
   mariadbd-safe &
   sleep 5
   
   ```
### 3. Lỗi Table 'heino.part' doesn't exist (hoặc thiếu bảng khác)
 * **Nguyên nhân:** MariaDB đã bật nhưng chưa nạp dữ liệu từ file heino.sql vào database.
 * **Cách khắc phục:**
   ```bash
   mariadb -uroot -e "CREATE DATABASE IF NOT EXISTS heino;"
   mariadb -uroot heino < /sdcard/Download/heino.sql
   
   ```
### 4. Lỗi java-jar: command not found
 * **Nguyên nhân:** Gõ sai cú pháp (viết liền dấu gạch ngang java-jar).
 * **Cách khắc phục:** Phải có **khoảng cách** giữa java và -jar:
   ```bash
   java -jar cc2.jar
   
   ```
## 🔄 4. Lệnh Mở Lại Server Cho Các Lần Sau (Chỉ Cần 2 Dòng)
Mỗi lần tắt Termux mở lại, bạn chỉ cần dán 2 dòng này:
```bash
mariadbd-safe &
cd ~/heino/"heino"/heino && java -jar cc2.jar

```
```

```

