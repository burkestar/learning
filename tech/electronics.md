# Electronics

## Concepts

Voltage, Current, and Resistance

- Voltage (`V`) is electrical potential difference, measured in _volts_
- Current (`I`) is the flow of electric charge, measured in _amperes_ (amps)
- Resistance (`R`) opposes current flow, measured in _ohms_.  Materials have a fundamental property of resistivity (`ρ`, measured in _ohm-meters_). `R = ρ × (L/A)` for Length (L) and Cross-sectional Area (A).
- Ohm's Law: `V = I × R`. Voltage is directly proportional to current when resistance is constant.

Power

- Power (`P`) is the rate of energy consumption, measured in _watts_
- `P = V × I = I²R = V²/R`
- Higher power means more energy used per unit time

AC vs DC

- DC (Direct Current) flows in one direction constantly
- AC (Alternating Current) changes direction periodically
- Most household power is AC; batteries provide DC

Circuit Laws

- Kirchhoff's Voltage Law: The sum of voltages around any closed loop equals zero
- Kirchhoff's Current Law: Current flowing into a junction equals current flowing out

Series vs Parallel Circuits

- Series: Components share the same current; voltages add up
- Parallel: Components share the same voltage; currents add up
- Resistance combinations differ between series (`R₁ + R₂`) and parallel (`1/Rtotal = 1/R₁ + 1/R₂`)

Semiconductor

- Material whose electrical conductivity falls between that of conductors (like metals) and insulators (like glass). 
- Foundation of all modern electronics because their conductivity can be precisely engineered and controlled.
- Conductivity increases with temperature (opposite of metals)

Computing Platforms

- Microcontroller (MCU)
    - A microcontroller is a compact integrated circuit designed for specific control applications. 
    - It contains a CPU, memory (both RAM and flash storage), and input/output peripherals all on a single chip. 
    - Microcontrollers are optimized for real-time control tasks, low power consumption, and cost efficiency. 
    - Examples include Arduino boards (using ATmega chips), PIC microcontrollers, and ARM Cortex-M series. 
    - They typically run simple programs directly on the hardware without an operating system, making them ideal for embedded applications like IoT devices, sensors, and automation systems.
- Single Board Computer (SBC)
    - A single board computer is a complete computer built on a single circuit board, including a processor, memory, storage, and various input/output connectors. 
    - Unlike microcontrollers, SBCs are designed to run full operating systems like Linux or Windows. 
    - They offer much more processing power and can handle complex applications, multimedia processing, and multitasking. 
    - Popular examples include Raspberry Pi, BeagleBone, and NVIDIA Jetson boards. 
    - SBCs are suitable for projects requiring significant computational power, such as media centers, desktop replacements, or AI applications.
- System on Chip (SoC)
    - A system on chip is an integrated circuit that combines multiple components of a computer system onto a single chip. 
    - This includes the CPU, GPU, memory controllers, and various other functional units. 
    - SoCs are the underlying technology that powers both some microcontrollers and single board computers. 
    - They're designed for specific applications and can range from simple embedded processors to complex chips found in smartphones, tablets, and laptops. 
    - Examples include Apple's M-series chips, Qualcomm Snapdragon processors, and Broadcom chips used in Raspberry Pi boards.

## Learning Resources

- [Tinkercad](https://www.tinkercad.com) online circuit simulator
- [Ask Electronics Sub-Reddit](https://www.reddit.com/r/AskElectronics/wiki/education/)
- [Learn about Electronics](https://learnabout-electronics.org)

## Hardware

- Where to buy electronics components: [SparkFun](https://www.sparkfun.com) and [DigiKey](https://www.digikey.com)

Microcontrollers (MCU)

- [Arduino](https://www.arduino.cc) open source programmable electronics platform with a single board microcontroller.
- [ESP32](https://www.google.com/search?q=esp32&client=safari&sca_esv=3625eca33e5fb376&rls=en&ei=P9KtaM-MG-mxptQPoN7pqAg&ved=0ahUKEwiPqILr5KiPAxXpmIkEHSBvGoUQ4dUDCBA&uact=5&oq=esp32&gs_lp=Egxnd3Mtd2l6LXNlcnAiBWVzcDMyMhAQABiABBixAxhDGIMBGIoFMgsQABiABBixAxiDATILEAAYgAQYsQMYgwEyCxAAGIAEGLEDGIMBMggQABiABBixAzIOEAAYgAQYsQMYgwEYigUyCBAAGIAEGLEDMgsQABiABBixAxiDATILEAAYgAQYsQMYgwEyCxAAGIAEGLEDGIMBSJ0CUH5YfnABeAGQAQCYAVWgAVWqAQExuAEDyAEA-AEBmAICoAJgwgIKEAAYsAMY1gQYR8ICDRAAGLADGNYEGEcYyQPCAg4QABiABBiwAxiSAxiKBcICDRAAGIAEGLADGEMYigXCAhMQLhiABBiwAxjRAxhDGMcBGIoFmAMAiAYBkAYIkgcBMqAH5gSyBwExuAdZwgcDMi0yyAcK&sclient=gws-wiz-serp) is a more powerful microcontroller than Arduino with integrated Wi-fi, Bluetooth, faster processor and more memory. Can be programmed using Arduino IDE.
- [STM32](https://www.st.com/en/microcontrollers-microprocessors/stm32-32-bit-arm-cortex-mcus.html)

Single-Board Computers (SBC)

- [Raspberry Pi](https://www.raspberrypi.com)
- [Beagleboard](https://www.beagleboard.org/boards)

## Software

- [PlatformIO](https://platformio.org) VS Code-based IDE
- [Arduino IDE](https://www.arduino.cc/en/software/)

## Notes

- Install CH340 USB to Serial driver to connect Arduino or ESP32 to laptop
