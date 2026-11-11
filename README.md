<div align="center">

# 🚗 WazeHUD ESP32-S3

**Hệ thống hiển thị thông tin trên kính lái (Head-Up Display) thông minh dành cho ô tô**

[![ESP-IDF](https://shields.io)](https://espressif.com)
[![Hardware](https://shields.io)](https://espressif.com)
[![Connectivity](https://shields.io)](#)
[![Language](https://shields.io)](#)

*Đồng bộ dữ liệu dẫn đường, cảnh báo camera phạt nguội và giới hạn tốc độ theo thời gian thực qua Bluetooth Low Energy (BLE).*

[Tính năng](#-tính-năng-nổi-bật) • [Phần cứng](#-tương-thích-phần-cứng) • [Cài đặt nhanh](#-hướng-dẫn-nạp-firmware) • [Biên dịch mã nguồn](#-biên-dịch-từ-mã-nguồn-esp-idf) • [Cấu trúc dự án](#-cấu-trúc-mã-nguồn)

</div>

---

## 📖 Tổng quan dự án

**WazeHUD ESP32-S3** là dự án firmware nhúng được phát triển trên nền tảng **ESP-IDF**, biến các bo mạch **ESP32-S3** kết hợp màn hình TFT tốc độ cao thành một thiết bị Head-Up Display (HUD) chuyên dụng trên xe hơi. 

Thiết bị giao tiếp không dây với điện thoại thông minh thông qua giao thức **Bluetooth Low Energy (BLE)** độ trễ thấp, trích xuất và hiển thị trực quan các thông tin quan trọng nhất giúp tài xế tập trung quan sát cung đường mà không cần rời mắt khỏi kính lái.

---

## ✨ Tính năng nổi bật

* **⚡ Kết nối BLE độ ổn định cao:** Tự động quảng bá (Advertising), ghép nối nhanh và duy trì kết nối bền bỉ với ứng dụng dẫn đường trên điện thoại.
* **🎨 Giao diện tối ưu cho lái xe:**
  * Hiển thị tốc độ hiện tại với kích thước lớn, độ tương phản cao.
  * Cảnh báo biển báo giới hạn tốc độ và khu vực có camera giao thông/phạt nguội.
  * Hiển thị mũi tên chỉ hướng rẽ tiếp theo, khoảng cách và tên đường (hỗ trợ đầy đủ bảng mã tiếng Việt có dấu).
  * Hỗ trợ chế độ lật gương (Mirror Mode) để phản chiếu trực tiếp lên kính lái.
* **🖥️ Hỗ trợ đa nền tảng màn hình tốc độ cao:** Tích hợp sẵn các component chính thức từ Espressif (QSPI 40 MHz, SPI 40 MHz, 8-bit Parallel) thông qua hệ thống file cấu hình `sdkconfig` tách biệt.
* **🧪 Chế độ giả lập tích hợp (Mock Mode):** Cho phép chạy kiểm thử toàn bộ hiệu ứng UI, font chữ và bố cục ngay trên bàn làm việc mà không cần kết nối điện thoại hay di chuyển thực tế.

---

## 🛠️ Tương thích phần cứng

Dự án được tối ưu hóa cho vi điều khiển **ESP32-S3** (khuyến nghị phiên bản có PSRAM và Flash từ 8MB–16MB) và hỗ trợ sẵn các cấu hình phần cứng sau:

| Cấu hình | Bo mạch / Màn hình | Độ phân giải | Driver & Giao tiếp | File cấu hình | Tài liệu hướng dẫn |
| :--- | :--- | :---: | :--- | :--- | :--- |
| **Mặc định** | **LILYGO T-Display S3** (16MB Flash) | `320 x 170` | `ST7789` (8-bit Parallel) | `sdkconfig.defaults` | *Tích hợp sẵn* |
| **Màn hình 3.5"** | ESP32-S3 + Màn hình TFT 3.5 inch | `480 x 320` | `espressif/esp_lcd_st77922` (**QSPI 40 MHz**) | `sdkconfig.35.defaults` | [DISPLAY_35_PORTING.md](./DISPLAY_35_PORTING.md) |
| **Màn hình 4.0"** | ESP32-S3 + Màn hình TFT 4.0 inch | `480 x 320` | `ST7796S` (SPI 40 MHz) | `sdkconfig.40.defaults` | [DISPLAY_40_PORTING.md](./DISPLAY_40_PORTING.md) |
| **Giả lập UI** | Tất cả bo mạch ESP32-S3 hỗ trợ | Tùy chọn | — | `sdkconfig.mock.defaults` | *Chạy dữ liệu mẫu* |

---

## 🚀 Hướng dẫn nạp Firmware

### Cách 1: Nạp nhanh bằng Factory BIN (Khuyên dùng cho người dùng cuối)

Phương pháp này không yêu cầu cài đặt môi trường lập trình phức tạp. File `factory.bin` đã đóng gói đầy đủ **Bootloader**, **Partition Table**, **OTA Data** và **Application**.

1. **Chuẩn bị công cụ:**
   * Cáp USB-C có hỗ trợ truyền dữ liệu.
   * Cài đặt Python 3.10+ và công cụ `esptool`:
     ```bash
     python -m pip install --upgrade esptool
     ```
2. **Đưa bo mạch vào chế độ Download (Bootloader):**
   * Nhấn và giữ nút **BOOT (GPIO0)**, sau đó cắm cáp USB vào máy tính (hoặc nhấn nhả nút **RST**), rồi thả nút **BOOT**.
   * *Lưu ý: Nút **KEY (GPIO14)** của WazeHUD là nút chức năng, không phải nút BOOT.*
3. **Thực hiện nạp Firmware:**
   * Xem hướng dẫn chi tiết từng bước và câu lệnh PowerShell tự động nhận diện file tại tài liệu: 👉 **[FLASH_FIRMWARE_VI.md](./FLASH_FIRMWARE_VI.md)**.

> ⚠️ **Lưu ý quan trọng:** Lệnh `erase_flash` hoặc nạp `factory.bin` sẽ xóa toàn bộ cấu hình NVS và dữ liệu ghép đôi BLE cũ. Hãy thực hiện thao tác này khi nạp lần đầu hoặc khi thiết bị hoạt động thiếu ổn định.

---

## 💻 Biên dịch từ mã nguồn (ESP-IDF)

Dành cho lập trình viên muốn tùy biến giao diện, thêm phần cứng mới hoặc phát triển tính năng.

### 1. Yêu cầu môi trường
* **ESP-IDF:** Phiên bản `v5.5.4` (hoặc các bản v5.x tương thích).
* **Trình biên tập:** Visual Studio Code kèm tiện ích mở rộng **Espressif IDF**.

### 2. Các bước biên dịch

```bash
# Bước 1: Tải mã nguồn từ GitHub
git clone https://github.com
cd waze_hud_esp32s3

# Bước 2: Thiết lập chip mục tiêu là ESP32-S3
idf.py set-target esp32s3

# Bước 3: Áp dụng cấu hình phần cứng mong muốn
# - Dùng cho màn hình 3.5" (ST77922 QSPI 40 MHz):
cp sdkconfig.35.defaults sdkconfig
# - Dùng cho màn hình 4.0" (ST7796S SPI 40 MHz):
# cp sdkconfig.40.defaults sdkconfig
# - Bỏ qua bước này nếu dùng cấu hình mặc định LILYGO T-Display S3

idf.py reconfigure

# Bước 4: Biên dịch dự án
idf.py build

# Bước 5: Nạp firmware và mở Serial Monitor (Thay COM9 bằng cổng thực tế)
idf.py -p COM9 flash monitor
```

---

## 📂 Cấu trúc mã nguồn

```text
waze-hud/
├── main/                        # Mã nguồn chính (BLE server, xử lý dữ liệu JSON, điều khiển UI)
├── tools/                       # Script hỗ trợ đóng gói binary và tiện ích phát triển
├── partitions.csv               # Bảng phân vùng bộ nhớ Flash tùy chỉnh (hỗ trợ OTA & NVS)
├── CMakeLists.txt               # Cấu hình build cấp dự án của ESP-IDF
├── dependencies.lock            # Khóa phiên bản các thư viện thành phần (như esp_lcd_st77922)
├── sdkconfig.defaults           # Cấu hình mặc định (LILYGO T-Display S3)
├── sdkconfig.35.defaults        # Cấu hình cho màn hình 3.5" (ST77922 QSPI 40 MHz)
├── sdkconfig.40.defaults        # Cấu hình cho màn hình 4.0" (ST7796S SPI 40 MHz)
├── sdkconfig.mock.defaults      # Cấu hình bật chế độ giả lập dữ liệu dẫn đường
├── FLASH_FIRMWARE_VI.md         # Tài liệu hướng dẫn nạp firmware chi tiết (Tiếng Việt)
├── DISPLAY_35_PORTING.md        # Tài liệu kỹ thuật porting màn hình 3.5" (ST77922)
└── DISPLAY_40_PORTING.md        # Tài liệu kỹ thuật porting màn hình 4.0" (ST7796S)
```

---

## 🤝 Đóng góp & Phát triển

Mọi đóng góp nhằm cải thiện hiệu năng, hỗ trợ thêm các dòng màn hình mới (như ESP32-P4, CYD...) hoặc tối ưu hóa giao diện đều được hoan nghênh:

1. **Fork** kho lưu trữ này.
2. Tạo nhánh tính năng mới (`git checkout -b feature/TenTinhNang`).
3. Commit các thay đổi của bạn (`git commit -m 'Add: Mô tả tính năng'`).
4. Push lên nhánh (`git push origin feature/TenTinhNang`) và tạo **Pull Request**.

---

## ⚠️ Tuyên bố miễn trừ trách nhiệm

Dự án này được chia sẻ dưới dạng mã nguồn mở phục vụ mục đích nghiên cứu, học tập và chế tạo thiết bị cá nhân (DIY). Người dùng cần tự chịu trách nhiệm trong việc lắp đặt thiết bị chắc chắn trên phương tiện, đảm bảo không che khuất tầm nhìn và luôn tuân thủ nghiêm ngặt luật lệ an toàn giao thông.
