# Hỗ trợ màn hình 4.0 inch 320×480
# Chip ST7796S
Tạo ngày: 11/11/2026 by DUCA IoT

Branch `support-4.0-in-screen` có profile bố cục cho panel TFT 4.0 inch độ phân giải gốc **320×480**, sử dụng ở chế độ landscape **480×320** (`CONFIG_WAZE_HUD_DISPLAY_40_480X320`).

## Build profile 480×320 (4.0 inch)

Chạy trong ESP-IDF 5.5.4:

```powershell
idf.py -B build-40 `
    -D SDKCONFIG=sdkconfig.40 `
    -D "SDKCONFIG_DEFAULTS=sdkconfig.defaults;sdkconfig.40.defaults" `
    build
```

Profile cũ 320×170 vẫn build theo cách thông thường:

```powershell
idf.py -B build build
```

Build mock 480×320 để kiểm tra trực tiếp trên module mà không cần điện thoại:

```powershell
idf.py -B build-40-mock `
    -D SDKCONFIG=sdkconfig.40.mock `
    -D "SDKCONFIG_DEFAULTS=sdkconfig.defaults;sdkconfig.40.defaults;sdkconfig.mock.defaults" `
    build
```

## Thiết kế layout

Renderer sử dụng mặt phẳng thiết kế 320×213 và scale đều khoảng 1,5 lần sang framebuffer 480×320. Cách này giữ đúng tỷ lệ của:

- icon hướng rẽ;
- biển giới hạn tốc độ hình tròn;
- icon cảnh báo;
- font và nét vẽ primitive.

Các dirty region ở profile 4.0 inch:

| Vùng | Logical | Physical xấp xỉ |
| --- | --- | --- |
| Maneuver | 85×175 | 128×263 |
| Speed | 80×175 | 120×263 |
| Speed limits | 60×175 | 90×263 |
| Alerts | 95×175 | 142×263 |
| Street/clock | 320×38 | 480×57 |

HLP `dev.disp` tự khai báo `480×320` khi profile này được chọn.

## Backend phần cứng TFT 4.0" ST7796S

Profile sử dụng driver chính thức `espressif/esp_lcd_st7796` với giao tiếp **SPI 4 dây** (`SPI3_HOST`) tốc độ 40 MHz:

| Tín hiệu | GPIO | Ghi chú |
| :--- | ---: | :--- |
| LCD CS | 41 | Chip Select |
| LCD CLK | 21 | SPI Clock (SCK) |
| LCD MOSI | 47 | SPI Data Out (SDI) |
| LCD MISO | NC (-1) | Không sử dụng |
| LCD DC | 40 | Data / Command |
| LCD RST | 45 | Hardware Reset |
| Backlight | 42 | Điều khiển độ sáng PWM |
| Built-in LED | 48 | Đèn LED trạng thái |
| KEY / BOOT | 0 | Nút Boot |
| Touch Button | 17 | Nút cảm ứng |
| Volume Up | 9 | Nút tăng âm lượng |
| Volume Down | 10 | Nút giảm âm lượng |

### Lưu ý kỹ thuật
- **Hardware Reset:** Màn hình sử dụng chân `GPIO 45` để reset phần cứng trước khi khởi tạo ST7796S. Lưu ý `GPIO 45` là strapping pin trên ESP32-S3, cần đảm bảo không bị kéo lên mức cao (`3.3V`) lúc cấp nguồn khởi động.
- **Định dạng màu:** Giao tiếp SPI gửi dữ liệu theo thứ tự MSB-first nên firmware thực hiện đảo byte màu RGB565 (`__builtin_bswap16`) trước khi truyền DMA và sử dụng thứ tự phần tử màu `BGR`.
