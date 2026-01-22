<div align="center">

# SRAM Interface with RISC-V SoC

[![English](https://img.shields.io/badge/Language-English-blue?style=for-the-badge)](#-english-version)
[![Vietnamese](https://img.shields.io/badge/Ngôn%20Ngữ-Tiếng%20Việt-red?style=for-the-badge)](#-phiên-bản-tiếng-việt)

</div>

---

<div id="english"></div>

## 🇺🇸 English Version

### 📖 Introduction
This project is part of the **Logic Design Course** at **Ho Chi Minh City University of Technology (HCMUT)**.

The goal is to design a complete **System-on-Chip (SoC)** on FPGA, integrating a **RISC-V processor** (PicoRV32), a custom **SRAM Controller** interfacing with Block RAM, and **UART** communication for data transfer between the FPGA and a PC.

* **Instructor:** M.S. Pham Kieu Nhat Anh
* **Team Members:**
  * Pham Le Minh Khoi - 2352622
  * Le Chuong Quyen - 2353034
  * Nguyen Tuan Ngoc - 2352815

### 🏗️ System Architecture
The system is built around the **PicoRV32** core, connected to peripherals via a simple memory bus.

#### Block Diagram
The figure below illustrates the high-level architecture, including the CPU, SRAM Controller, UART, and Memory-Mapped I/O (MMIO).

![System Block Diagram](TOP%20LEVEL%20.png)
*(Figure 4.1: SoC Implementation Block Diagram)*

#### Key Components:
1.  **PicoRV32 RISC-V Core:**
    * ISA: RV32I (32-bit integer).
    * Role: Central processing unit executing C firmware to manage data flow.
2.  **SRAM Controller (Custom Design):**
    * Interfaces with FPGA Block RAM.
    * Uses a **2-stage pipeline** (Request -> Access) to ensure timing closure.
    * Implements a Handshake mechanism (`mem_ready`) to stall the CPU during memory access.
3.  **UART Interface (RX/TX):**
    * Baudrate: 115200 (8N1).
    * Features internal FIFO buffering and hardware flow control, ensuring reliable data transfer without complex interrupts.

### ⚙️ Implementation & RTL
#### 1. Top Level RTL Schematic
Synthesis result of the entire system (Level-0 Wrapper):

![Top Level RTL Schematic](TOP%20LEVEL%20SCHEMATIC.png)
*(Figure 4.2: Post-synthesis RTL Schematic of picorv32_top)*

#### 2. MMIO Bus & Address Decoding
The `picorv32_mmio_bus` module handles address decoding and routes data between the CPU and peripherals (SRAM, UART, LED).

![MMIO Bus Schematic](MIMO%20BUS%20AND%20ADDRESS%20DECODING%20SCHEMATIC.png)
*(Figure 4.3: Detailed RTL Schematic of MMIO Bus and Address Decoder)*

### 🔄 Operation Flow (Loopback Test)
The firmware performs a "Loopback" test to verify system integrity:

1.  **Startup:** Write `0x55` to LEDs.
2.  **Receive:** Wait for bytes from PC via UART RX and store them in SRAM.
3.  **Store & Signal:** Once completed, write `0xCC` to LEDs.
4.  **Transmit:** Read data back from SRAM and send it to PC via UART TX.
5.  **Finish:** Write `0xAA` to LEDs.

![Firmware Processing Flow](Processing%20flow%20.png)
*(Figure 4.7: Firmware Processing Flowchart)*

### 📊 FPGA Resource Utilization
Post-synthesis resource usage:

![FPGA Resource Utilization](FPGA%20RESOURCE%20UTILISATION%20AFTER%20SYNTHESIS.png)
*(Table 1: FPGA Resource Utilization after Synthesis)*

* **PicoRV32 CPU:** Consumes the majority of logic resources (927 LUTs).
* **SRAM Controller & UART:** highly optimized, using minimal resources.
* **Block RAM:** Uses 64 tiles for Instruction/Data memory.

### 🚀 Getting Started
#### Requirements
* **Hardware:** FPGA Development Board (with Block RAM and UART support).
* **Software:** Vivado/Quartus (for bitstream), RISC-V GCC Toolchain (for firmware).

#### Running the Demo
1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/MKHZ2108/SDRAM-CONTROLLER-FOR-RISC-V-CPU.git](https://github.com/MKHZ2108/SDRAM-CONTROLLER-FOR-RISC-V-CPU.git)
    ```
2.  **Upload Bitstream:** Flash the design to your FPGA.
3.  **Connect UART:** Connect Serial cable to PC (Baud 115200).
4.  **Run Test Script:** Use the provided Python script to send an image and verify the loopback.

<div align="right">
    <a href="#sram-interface-with-risc-v-soc">⬆️ Back to Top</a>
</div>

---

<div id="vietnamese"></div>

## 🇻🇳 Phiên bản Tiếng Việt

### 📖 Giới thiệu
Dự án này là đồ án môn học **Thiết kế Logic (Logic Design)** tại **Trường Đại học Bách Khoa ĐHQG-HCM (HCMUT)**.

Mục tiêu của dự án là xây dựng một hệ thống **SoC (System-on-Chip)** hoàn chỉnh trên FPGA, tích hợp vi xử lý **RISC-V** (PicoRV32) và bộ điều khiển **SRAM Controller** tùy biến để giao tiếp với Block RAM, cùng với giao tiếp **UART** để truyền nhận dữ liệu với máy tính.

* **Giảng viên hướng dẫn:** ThS. Phạm Kiều Nhật Anh
* **Sinh viên thực hiện:**
  * Phạm Lê Minh Khôi - 2352622
  * Lê Chương Quyền - 2353034
  * Nguyễn Tuấn Ngọc - 2352815

### 🏗️ Kiến trúc hệ thống
Hệ thống được thiết kế xoay quanh lõi **PicoRV32**, kết nối với các ngoại vi qua bus bộ nhớ đơn giản.

#### Sơ đồ khối tổng quát
Hình dưới đây mô tả cấu trúc mức hệ thống của SoC, bao gồm PicoRV32 Core, SRAM Controller, UART và các thanh ghi ngoại vi (MMIO):

![System Block Diagram](TOP%20LEVEL%20.png)
*(Hình 4.1: Sơ đồ khối hệ thống thực thi trên FPGA)*

#### Các thành phần chính:
1.  **PicoRV32 RISC-V Core:**
    * Tập lệnh: RV32I (32-bit integer).
    * Vai trò: Điều khiển trung tâm, chạy firmware C để quản lý luồng dữ liệu.
2.  **SRAM Controller (Custom Design):**
    * Giao tiếp với FPGA Block RAM.
    * Sử dụng **Pipeline 2 tầng** (Request -> Access) để đảm bảo đồng bộ timing.
    * Cơ chế Handshake (`mem_ready`) giúp CPU tự động đợi (stall) khi bộ nhớ đang truy xuất.
3.  **UART Interface (RX/TX):**
    * Baudrate: 115200 (8N1).
    * Tích hợp bộ đệm (FIFO) và flow control phần cứng, giúp truyền nhận dữ liệu tin cậy mà không cần ngắt (interrupt) phức tạp.

### ⚙️ Thiết kế chi tiết & RTL
#### 1. Sơ đồ RTL tổng quát
Kết quả tổng hợp mạch (Synthesis) của toàn bộ hệ thống (Level-0 Wrapper):

![Top Level RTL Schematic](TOP%20LEVEL%20SCHEMATIC.png)
*(Hình 4.2: Sơ đồ RTL sau khi tổng hợp module picorv32_top)*

#### 2. Chi tiết Bus MMIO và Giải mã địa chỉ
Module `picorv32_mmio_bus` chịu trách nhiệm giải mã địa chỉ và điều hướng dữ liệu giữa CPU và các ngoại vi (SRAM, UART, LED):

![MMIO Bus Schematic](MIMO%20BUS%20AND%20ADDRESS%20DECODING%20SCHEMATIC.png)
*(Hình 4.3: Sơ đồ RTL chi tiết của Bus MMIO và Logic giải mã địa chỉ)*

### 🔄 Luồng hoạt động (Kiểm tra Loopback)
Hệ thống hoạt động theo quy trình "Loopback" để kiểm chứng độ tin cậy. Firmware chạy trên RISC-V thực hiện các bước sau:

1.  **Khởi động:** Ghi `0x55` ra LED.
2.  **Nhận dữ liệu:** Chờ nhận bytes từ PC qua UART RX và lưu vào SRAM.
3.  **Lưu trữ & Báo hiệu:** Sau khi nhận đủ, ghi `0xCC` ra LED.
4.  **Gửi lại:** Đọc dữ liệu từ SRAM và gửi ngược về PC qua UART TX.
5.  **Kết thúc:** Ghi `0xAA` ra LED.

![Firmware Processing Flow](Processing%20flow%20.png)
*(Hình 4.7: Lưu đồ thuật toán xử lý của Firmware)*

### 📊 Tài nguyên FPGA
Bảng dưới đây thống kê tài nguyên sử dụng trên FPGA sau khi tổng hợp:

![FPGA Resource Utilization](FPGA%20RESOURCE%20UTILISATION%20AFTER%20SYNTHESIS.png)
*(Bảng 1: Thống kê tài nguyên FPGA sau khi Synthesis)*

* **PicoRV32 CPU:** Chiếm phần lớn tài nguyên Logic (927 LUTs).
* **SRAM Controller & UART:** Sử dụng rất ít tài nguyên, tối ưu cho các thiết kế nhỏ gọn.
* **Block RAM:** Sử dụng 64 tiles cho bộ nhớ Instruction/Data.

### 🚀 Hướng dẫn sử dụng
#### Yêu cầu
* **Phần cứng:** FPGA Development Board (có hỗ trợ Block RAM và UART).
* **Phần mềm:** Vivado/Quartus (nạp mạch), RISC-V GCC Toolchain (biên dịch C).

#### Các bước chạy demo
1.  **Clone dự án:**
    ```bash
    git clone [https://github.com/MKHZ2108/SDRAM-CONTROLLER-FOR-RISC-V-CPU.git](https://github.com/MKHZ2108/SDRAM-CONTROLLER-FOR-RISC-V-CPU.git)
    ```
2.  **Nạp Bitstream:** Mở project và nạp file bitstream xuống FPGA.
3.  **Kết nối UART:** Kết nối cáp Serial với máy tính (Baud 115200).
4.  **Chạy script test:** Dùng script Python để gửi file ảnh mẫu và kiểm tra kết quả loopback.

<div align="right">
    <a href="#sram-interface-with-risc-v-soc">⬆️ Về đầu trang</a>
</div>

---
© 2026 Logic Design Project - HCMUT
