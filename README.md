<div align="center">

# SRAM Interface with RISC-V SoC

[![English](https://img.shields.io/badge/Language-English-blue?style=for-the-badge)](README.md)
[![Vietnamese](https://img.shields.io/badge/Ngôn%20Ngữ-Tiếng%20Việt-red?style=for-the-badge)](README_VI.md)

</div>

---

## 🇺🇸 Introduction
This project is part of the **Logic Design Course** at **Ho Chi Minh City University of Technology (HCMUT)**.

The goal is to design a complete **System-on-Chip (SoC)** on FPGA, integrating a **RISC-V processor** (PicoRV32), a custom **SRAM Controller** interfacing with Block RAM, and **UART** communication for data transfer between the FPGA and a PC.

* **Instructor:** M.S. Pham Kieu Nhat Anh
* **Team Members:**
  * Pham Le Minh Khoi - 2352622
  * Le Chuong Quyen - 2353034
  * Nguyen Tuan Ngoc - 2352815

## 🏗️ System Architecture
The system is built around the **PicoRV32** core, connected to peripherals via a simple memory bus.

### Block Diagram
The figure below illustrates the high-level architecture, including the CPU, SRAM Controller, UART, and Memory-Mapped I/O (MMIO).

![System Block Diagram](images/TOP%20LEVEL%20.png)
*(Figure 4.1: SoC Implementation Block Diagram)*

### Key Components:
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

## ⚙️ Implementation & RTL
### 1. Top Level RTL Schematic
Synthesis result of the entire system (Level-0 Wrapper):

![Top Level RTL Schematic](images/TOP%20LEVEL%20SCHEMATIC.png)
*(Figure 4.2: Post-synthesis RTL Schematic of picorv32_top)*

### 2. MMIO Bus & Address Decoding
The `picorv32_mmio_bus` module handles address decoding and routes data between the CPU and peripherals (SRAM, UART, LED).

![MMIO Bus Schematic](images/MIMO%20BUS%20AND%20ADDRESS%20DECODING%20SCHEMATIC.png)
*(Figure 4.3: Detailed RTL Schematic of MMIO Bus and Address Decoder)*

## 🔄 Operation Flow (Loopback Test)
The firmware performs a "Loopback" test to verify system integrity:

1.  **Startup:** Write `0x55` to LEDs.
2.  **Receive:** Wait for bytes from PC via UART RX and store them in SRAM.
3.  **Store & Signal:** Once completed, write `0xCC` to LEDs.
4.  **Transmit:** Read data back from SRAM and send it to PC via UART TX.
5.  **Finish:** Write `0xAA` to LEDs.

![Firmware Processing Flow](images/Processing%20flow%20.png)
*(Figure 4.7: Firmware Processing Flowchart)*

## 📊 FPGA Resource Utilization
Post-synthesis resource usage:

![FPGA Resource Utilization](images/FPGA%20RESOURCE%20UTILISATION%20AFTER%20SYNTHESIS.png)
*(Table 1: FPGA Resource Utilization after Synthesis)*

* **PicoRV32 CPU:** Consumes the majority of logic resources (927 LUTs).
* **SRAM Controller & UART:** highly optimized, using minimal resources.
* **Block RAM:** Uses 64 tiles for Instruction/Data memory.

## 🚀 Getting Started
### Requirements
* **Hardware:** FPGA Development Board (with Block RAM and UART support).
* **Software:** Vivado/Quartus (for bitstream), RISC-V GCC Toolchain (for firmware).

### Running the Demo
1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/MKHZ2108/SDRAM-CONTROLLER-FOR-RISC-V-CPU.git](https://github.com/MKHZ2108/SDRAM-CONTROLLER-FOR-RISC-V-CPU.git)
    ```
2.  **Upload Bitstream:** Flash the design to your FPGA.
3.  **Connect UART:** Connect Serial cable to PC (Baud 115200).
4.  **Run Test Script:** Use the provided Python script to send an image and verify the loopback.

---
© 2026 Logic Design Project - HCMUT
