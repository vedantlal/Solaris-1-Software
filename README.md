# Solaris-1-Software

Bare-metal embedded firmware application for the STM32H753 microcontroller. Built on CMake and Ninja

## Architecture & System Stack

* **Operating System:** FreeRTOS 
* **Hardware Abstraction:** STM32CubeMX HAL 
* **Build Generator:** CMake (v3.20+)
* **Build Engine:** Ninja 
* **Cross-Compiler:** GNU Arm Embedded Toolchain (`arm-none-eabi-gcc`)

### Core Hardware Configurations
The build system natively injects parameters optimized for the ARM Cortex-M7 architecture:
* **Thumb-2 Mode Execution (`-mthumb`):** Required for Cortex-M processors.
* **Hardware FPU Activation (`-mfloat-abi=hard -mfpu=fpv5-d16`):** Maximizes floating-point hardware performance, enabling FreeRTOS context-switching optimization across registers `s16-s31`.
* **Embedded Memory Layout (`-T`):** Bypasses standard host OS linking structures to map binaries directly into micro-controller sector addresses via `STM32H753xx_FLASH.ld`.

---

## Prerequisites

Ensure the following tools are installed on your machine and properly mapped to your system's global environment `PATH` variables:

1. **CMake** (v3.20 or newer)
2. **Ninja Build System**
3. **GNU Arm Embedded Toolchain** (`arm-none-eabi-gcc`)

---

## How to Build

Execute these steps sequentially within your terminal inside the root project directory:

### 1. Configure the Environment Layout
This initializes the build subsystem, links the compilers, sets bare-metal configurations, and dynamically references the linker memory script relative to the project root:

```powershell
cmake -B build -G "Ninja" `
  -DCMAKE_SYSTEM_NAME=Generic `
  -DCMAKE_C_COMPILER=arm-none-eabi-gcc `
  -DCMAKE_CXX_COMPILER=arm-none-eabi-g++ `
  -DCMAKE_C_FLAGS="-mthumb -mcpu=cortex-m7 -mfloat-abi=hard -mfpu=fpv5-d16" `
  -DCMAKE_CXX_FLAGS="-mthumb -mcpu=cortex-m7 -mfloat-abi=hard -mfpu=fpv5-d16" `
  -DCMAKE_EXE_LINKER_FLAGS="-T ${CMAKE_CURRENT_SOURCE_DIR}/STM32H753xx_FLASH.ld --specs=nano.specs -Wl,--gc-sections" `
  -DCMAKE_EXECUTABLE_SUFFIX=".elf"
