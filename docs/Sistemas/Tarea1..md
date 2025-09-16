#T1 Comparación de microcontroladores 
| Microcontroladores | MSP430F2617TPM                             | ESP32                                   | RP2040                 | STM32F407VG         |
|--------------------|--------------------------------------------|-----------------------------------------|------------------------|---------------------|
| Periféricos        | Brown-out Detect/Reset, DMA, POR, PWM, WDT | PWM, ADC 12 BITS, DAC 8 BITS            | PWM, UART, ADC, SPI    | 3x ADC, 2x DAC, 17x Timers |
| Memoria            | 92 KB FLASH                                | 4 MB FLASH                              | 2 MB FLASH             | 1 MB FLASH          |
| Ecosistema         | Familia MSP430                              | Arduino                                 | MicroPython, C/C++     | IDE OFICIAL HALL/LL |
| Costos             | USD 483.04                                  | USD 2                                   | USD 4-10               | USD 20-28           |
| Arquitectura       | Núcleo RISC de 16 bits                      | Procesador Xtensa LX6, dual-core, 32 bits RISC | ARM Cortex 32 bits | ARM Cortex 32 bits  |
| Vel. De Trabajo    | 16 MHz                                      | 240 MHz                                 | 133 MHz                | 168 MHz             |


1.	ESP32: Elegido porque ha sido el único con el que he trabajado y no se me hizo muy complicad utilizarlo, aparte de que es muy barato 
2.	RP2040: Es el segundo con mas memoria FLASH y es compatible con C/C++
3.	 STM32F407VG: Varios periféricos pero más caro, aparte de que su ecosistema no lo conozco
4.	MSP430F2617TPM: Carísimo, es más limitado con su memoria FLASH

