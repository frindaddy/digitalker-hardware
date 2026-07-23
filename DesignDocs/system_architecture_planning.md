# System Architecture Planning

This doc hold notes about the system architecture plan.

## General Architecture

For flexibility, the ideal architecture should emulate the speech ROMs rather than using actual parallel bus memory ICs. The final system will require an external control chip for recording/storing audio and controlling the MM4104, so it simplifies the hardware design to include ROM emulation as a part of the control chip's duties.

The industry standard method of moving audio signals around a PCB is to use the I2S (Inter-IC Sound) protocol. This is a widely-used serial communication protocol for transmitting digitial audio data between sound-processing devices. Because of the high adpotion of this standard, many turn-key I2S ICs and SoMs are cheaply availible. Additionally, analog to digital audio processing and recording is computationally expensive for microcontrollers and FPGAs that lack dedicated audio processing silicon. As such, the MM4104 audio output will be converted to a digital I2S stream with discrete components and supplied to the MCU for SD card storage.

## MCU Selection

With these requirements, the obvious question is whether to pick a microcontroller or a CPLD/FPGA. Because this is a hobbyist grade project, cost is a heavily weighted factor. So long as the minimum performance characteristics are met, the cheaper the better (with full context, so part cost, surrounding hardware, manufacturing costs, software licensing, etc.). First, minimum requirements must be outlined (I/O lines, timing performance, etc.).

### I/O Requirements

For clarity, we'll count out the I/O required for each interface. The I or O designation is with respect to the MCU.

#### MM4104 Control Signals

These are the I/O lines required to control the MM4104.

| Signal | I/O | Pin Count |
| ------ | --- | --------- |
| SPC Word Selection Bus | O | 8 |
| SPC Chip Select | O | 1 |
| SPC Command Select | O | 1 |
| SPC Write Strobe | O | 1 |
| SPC Interrupt | I | 1 |

#### ROM Emulation Signals

These are the I/O lines required to emulate the parallel bus ROM chips the MM4104 typically interfaces with. These have additional timing requirements, which are discussed in a later section.

| Signal | I/O | Pin Count |
| ------ | --- | --------- |
| ROM Address Bus | I | 14 |
| ROM Data Bus | O | 8 |
| ROMEN | I | 1 |

#### I2S Bus

As discussed earlier, the MCU will recieve the MM4104 audio stream over I2S. An I2S interface consists of 3 data lines, although often requires a fourth clock line separate from the protocol to run the sampling logic in the ADC. Modern I2S ICs (like the PCM1809) come equipped with internal PLLs that can generate this clock from the interface itself. A modern I2S ADC will be used to reduce the complexity of the hardware and reduce the required MCU I/O pins.

| Signal | I/O | Pin Count |
| ------ | --- | --------- |
| Serial Data Line | I | 1 |
| FSYNC | O | 1 |
| BCLK | O | 1 |

#### SD Card SPI Interface

Finally, an SPI interface is required to store the I2S audio stream as .WAV files.

| Signal | I/O | Pin Count |
| ------ | --- | --------- |
| SCLK | O | 1 |
| MOSI | O | 1 |
| MISO | I | 1 |
| CS | O | 1 |

#### I/O Totals

Combining all these interfaces, the minimum number of required I/O pins is **42**

### Timing Requirements

Timing requirements are an important consideration for this design, and are a deciding factor between a microcontroller and an FPGA. While FPGAs are significantly faster and allow strict timing constraints, modern microcontrollers are cheap and fast enough to get the job done in many cases. For this design, the fastest timing constraint comes from the ROM emulation. According to the MM4104 datasheet, the maximum allowable time between a receiving a request for data at a given address and presenting that data to the MM4104 is 2us. This is an *eternity* in the modern timing regime, so no special hardware is required (although care should still be taken to minimize instruction time in the software).

### Final Selection

With these considerations in mind, the ideal choice for the MCU is the RP2350B. With its dual 150MHz ARM (or RISC-V) cores, 3 150MHz PIO state machines, QSPI FLASH interface, and 48 I/O pins, the RP2350B meets all the requirements with pins to spare. It is a widely-used microcontroller with pre-existing libraries for I2S and SPI interfaces. Finally, the RP2350B is *cheap*. At just $1 at the time of writing, its flexibility compared to its cost blows modern CPLDs and FPGAs out of the water.
