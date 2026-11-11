# WazeHUD ESP32-S3

Dự án màn hình hiển thị thông tin trên kính lái (Head-Up Display - HUD) sử dụng vi điều khiển **ESP32-S3** và framework **ESP-IDF**. Thiết bị kết nối với điện thoại thông qua Bluetooth Low Energy (BLE) để đồng bộ dữ liệu dẫn đường, cảnh báo giao thông và giới hạn tốc độ theo thời gian thực.

---

## ✨ Tính năng chính

* **Kết nối không dây BLE:** Tự động ghép nối và nhận dữ liệu dẫn đường từ ứng dụng trên điện thoại.
* **Giao diện trực quan:** Hiển thị rõ nét tốc độ hiện tại, biển báo giới hạn tốc độ, hướng rẽ tiếp theo và các cảnh báo camera/giao thông.
* **Hỗ trợ đa dạng phần cứng:** Tương thích với nhiều kích thước màn hình và bo mạch thông qua các file cấu hình `sdkconfig` dựng sẵn.
* **Chế độ giả lập (Mock Mode):** Tích hợp sẵn cấu hình chạy thử nghiệm giao diện mà không cần kết nối điện thoại.

---

## 🛠️ Phần cứng hỗ trợ

Dự án đã được cấu hình sẵn cho các dòng bo mạch và màn hình phổ biến:

1. **LILYGO T-Display S3** (ESP32-S3, Flash 16MB) — Cấu hình mặc định.
2. **Màn hình TFT 3.5 inch** (Độ phân giải 480x320) — Xem chi tiết tại `DISPLAY_35_PORTING.md`.
3. **Màn hình TFT 4.0 inch** (Độ phân giải 480x320, chip điều khiển ST7796S) — Xem chi tiết tại `DISPLAY_40_PORTING.md`.

---

## 📂 Cấu trúc thư mục

```text
waze-hud/
├── main/                       # Mã nguồn chính của ứng dụng
├── tools/                      # Các công cụ hỗ trợ build và đóng gói
├── DISPLAY_35_PORTING.md       # Hướng dẫn cấu hình cho màn hình 3.5 inch
├── DISPLAY_40_PORTING.md       # Hướng dẫn cấu hình cho màn hình 4.0 inch
├── FLASH_FIRMWARE_VI.md        # Hướng dẫn chi tiết cách nạp firmware (Tiếng Việt)
├── partitions.csv              # Bảng phân vùng bộ nhớ Flash
├── sdkconfig.defaults          # Cấu hình mặc định (LILYGO T-Display S3)
├── sdkconfig.35.defaults       # Cấu hình cho màn hình 3.5 inch
├── sdkconfig.40.defaults       # Cấu hình cho màn hình 4.0 inch
└── sdkconfig.mock.defaults     # Cấu hình chạy chế độ giả lập UI
```

---

## 🚀 Hướng dẫn cài đặt và nạp Firmware

### Cách 1: Nạp nhanh bằng file Factory BIN (Khuyên dùng)
Nếu bạn chỉ muốn nạp firmware để sử dụng ngay mà không cần cài đặt môi trường lập trình, hãy làm theo hướng dẫn chi tiết trong file **[FLASH_FIRMWARE_VI.md](./FLASH_FIRMWARE_VI.md)**.

### Cách 2: Biên dịch từ mã nguồn (ESP-IDF)

**Yêu cầu môi trường:**
* **ESP-IDF v5.5.4** (hoặc phiên bản v5.x tương thích).
* Cáp USB-C hỗ trợ truyền dữ liệu.

**Các bước thực hiện:**

1. **Clone dự án về máy:**
   ```bash
   git clone https://github.com/duyhpe/waze_hud_esp32s3.git
   cd waze_hud_esp32s3
   ```

2. **Chọn cấu hình phần cứng tương ứng:**
   * *Mặc định (T-Display S3):* Không cần thao tác thêm.
   * *Màn hình 3.5 inch:*
     ```bash
     cp sdkconfig.35.defaults sdkconfig
     ```
   * *Màn hình 4.0 inch:*
     ```bash
     cp sdkconfig.40.defaults sdkconfig
     ```

3. **Biên dịch và nạp vào bo mạch:**
   *(Thay `COM9` bằng cổng COM thực tế của thiết bị trên máy tính của bạn)*
   ```bash
   idf.py set-target esp32s3
   idf.py build
   idf.py -p COM9 flash monitor
   ```

---

## 📖 Tài liệu tham khảo thêm

* [Hướng dẫn nạp Firmware tiếng Việt](./FLASH_FIRMWARE_VI.md)
* [Hướng dẫn Porting màn hình 3.5 inch](./DISPLAY_35_PORTING.md)
* [Hướng dẫn Porting màn hình 4.0 inch](./DISPLAY_40_PORTING.md)

---

## 📄 Giấy phép (License)

Dự án được phát triển phục vụ mục đích học tập và nghiên cứu DIY. Vui lòng tuân thủ luật lệ an toàn giao thông khi sử dụng thiết bị trên xe.
