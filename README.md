# SRAM Interface with RISC-V CPU

## 🏫 Giới thiệu (Introduction)
Dự án này là đồ án môn học **Thiết kế Logic (Logic Design)** tại **Trường Đại học Bách Khoa ĐHQG-HCM**.

Mục tiêu của dự án là thiết kế một hệ thống SoC (System-on-Chip) trên FPGA, bao gồm:
* Vi xử lý **PicoRV32** (RISC-V core).
* Bộ điều khiển **SRAM Controller** giao tiếp với Block RAM.
* Giao tiếp **UART** để truyền nhận dữ liệu với máy tính.
* Các ngoại vi Memory-Mapped I/O (MMIO) như LED để hiển thị trạng thái.

**Giảng viên hướng dẫn:** ThS. Phạm Kiều Nhật Anh

### 👥 Nhóm thực hiện (Team Members)
| Họ và tên | MSSV |
|-----------|------|
| Phạm Lê Minh Khôi | 2352622 |
| Lê Chương Quyền | 2353034 |
| Nguyễn Tuấn Ngọc | 2352815 |

---

## 🏗️ Kiến trúc hệ thống (System Architecture)
Hệ thống được thiết kế theo mô hình SoC tích hợp trên FPGA với các module chính:

### 1. PicoRV32 RISC-V Core
* Sử dụng tập lệnh **RV32I** (32-bit integer).
* Giao tiếp qua bus đơn giản: `mem_valid`, `mem_addr`, `mem_wdata`, `mem_rdata`, `mem_ready`.

### 2. SRAM Controller (BRAM Interface)
* Chuyển đổi yêu cầu từ CPU thành thao tác đọc/ghi đồng bộ với **FPGA Block RAM**.
* Sử dụng **Pipeline 2 tầng (2-stage pipeline)**:
    * *Stage 1:* Bắt tín hiệu yêu cầu (Request Capture).
    * *Stage 2:* Truy cập BRAM và phản hồi (BRAM Access & Response).
* Cơ chế **Handshake**: Sử dụng tín hiệu `mem_ready` để stall CPU khi bộ nhớ đang bận, đảm bảo đồng bộ dữ liệu.

### 3. UART Interface (RX/TX)
* **Cấu hình:** 115200 baud rate, 8 data bits, No parity, 1 stop bit (8N1).
* **Cơ chế Flow Control:** Phần cứng tự động stall CPU (giữ `mem_ready = 0`) khi UART đang bận gửi (TX busy) hoặc chưa có dữ liệu để đọc (RX empty). Điều này giúp code C đơn giản hơn, không cần polling liên tục.

### 4. Memory Map (Bản đồ bộ nhớ)
Hệ thống sử dụng cơ chế Memory-Mapped I/O (MMIO) với địa chỉ cụ thể như sau:

| Module | Địa chỉ (Address) | Chức năng |
|--------|-------------------|-----------|
| **Instruction Memory**| `0x0000_0000` | Chứa mã lệnh chương trình |
| **SRAM Data** | `0x0001_0000` | Vùng nhớ dữ liệu (lưu ảnh/file từ PC) |
| **LED Register** | `0x1000_0000` | Điều khiển 8 LED đơn (Ghi) |
| **UART TX** | `0x1000_0004` | Gửi dữ liệu ra PC (Ghi) |
| **UART RX** | `0x1000_0008` | Đọc dữ liệu từ PC (Đọc) |

---

## 🔄 Luồng hoạt động (Operation Flow)
Firmware chạy trên PicoRV32 thực hiện quy trình "Loopback" để kiểm tra hệ thống:

1.  **Khởi động (Startup):**
    * Ghi giá trị `0x55` ra LED.
2.  **Nhận dữ liệu (Data Reception):**
    * Nhận liên tiếp N bytes từ PC qua UART RX.
    * Lưu trữ vào SRAM.
    * Ghi giá trị `0xCC` ra LED sau khi nhận xong.
3.  **Gửi lại dữ liệu (Data Transmission):**
    * Đọc dữ liệu từ SRAM.
    * Gửi ngược lại PC qua UART TX.
    * Ghi giá trị `0xAA` ra LED khi hoàn tất.

---

## 📊 Tài nguyên sử dụng (Resource Utilization)
Kết quả tổng hợp (Synthesis) trên FPGA:

* **PicoRV32 CPU:** ~927 LUTs, 574 FFs.
* **SRAM Controller:** ~42 LUTs, 285 FFs.
* **UART (RX+TX):** ~79 LUTs, 75 FFs.
* **Tổng cộng:** ~1048 LUTs, 985 FFs, 64 BRAM tiles.

---

## 🛠️ Công cụ & Hướng dẫn (Tools & Usage)

### Công cụ sử dụng
* **Hardware Design:** Verilog HDL.
* **Software Toolchain:** RISC-V GCC (để biên dịch code C thành file `.hex`/`.mem`).
* **Simulation:** ModelSim / Vivado Simulator.
* **Utilities:** Python script (`make_mem.py`) để tạo file khởi tạo bộ nhớ; Python script để gửi/nhận file qua cổng Serial.

### Cách chạy mô phỏng/thực nghiệm
1.  **Biên dịch Firmware:** Dùng GCC biên dịch code C và tạo file `.mem`.
2.  **Synthesis:** Chạy tổng hợp mạch trên Vivado/Quartus và nạp bitstream xuống FPGA.
3.  **Kết nối:** Cắm cáp USB-UART vào máy tính.
4.  **Chạy Test:**
    * Mở script Python trên PC để gửi một file ảnh (ví dụ 96kB).
    * Quan sát LED trên board thay đổi từ `0x55` -> `0xCC` -> `0xAA`.
    * Kiểm tra file nhận được trên PC có khớp với file gốc không.

---

## 📂 Cấu trúc thư mục (Folder Structure)
* `src/`: Mã nguồn Verilog (PicoRV32, SRAM Controller, UART, Top-level).
* `firmware/`: Mã nguồn C và Makefile cho vi xử lý.
* `tb/`: Testbench cho mô phỏng.
* `scripts/`: Các file Python hỗ trợ.
* `docs/`: Báo cáo và tài liệu thiết kế.
