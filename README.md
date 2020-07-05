# Neotron 600

> A Neotron based home computer, powered by an 32-bit ARM Cortex-M7, with USB, Ethernet and hardware accelerated graphics.

## The top-level Specs

* 600 MHz 32-bit ARM Cortex-M7 CPU core
* 1024 KiB of internal SRAM (used as VRAM and for OS buffers)
* 8 MiB external QuadSPI Flash
* 8 MiB external QuadSPI SRAM (optional)
* Super-VGA output
   * 640x480
   * 800x600
   * 256 colours from a pallette of 262,144
* 16-bit 48 kHz audio input and output (line-in, mic-in, line-out and headphone-out)
* Four USB ports
   * Two USB A ports on-board
   * Header for additional two ports
* IEEE-1284 Parallel Port
* RS232 Port (five-wire)
* 2x SD/MMC Slots
* PS/2 Keyboard and Mouse ports
* 2x Atari/Sega joystick ports
* Battery-backed Real-time Clock and CMOS RAM
* SPI and I2C based expansion bus (Neotron-32 Compatible)

## Why Neotron-600?

Because it's clocked at 600 MHz.

## The detailed specs

### The Processor

The Neotron-600 uses a [Teensy 4.1] board, which features an NXP [iMXRT1062] microcontroller.

[Teensy 4.1]: https://www.pjrc.com/store/teensy41.html
[iMXRT1062]: https://www.st.com/en/microcontrollers-microprocessors/stm32h753ii.html

The microcontroller on the Teensy 4.1 has:

* An ARM Cortex-M7F core
  * Armv7-M Instruction Set
  * dual-issue, six-stage pipeline
  * hardware floating point (VFPv5)
  * 32 KiB instruction cache
  * 32 KiB data cache  
* Clockspeed of up to 600 MHz
* 1024 KiB of FlexRAM - can be divided up into:
   * tightly-coupled (Cortex-M only) data RAM (DTCM)
   * tightly-coupled (Cortex-M only) instruction RAM (ITCM)
   * General-purpose RAM (OCRAM)
* External parallel memory bus interface with SDRAM support
* 2x Memory-mapped QuadSPI interface
* 2x 10/100 Ethernet MAC
* 2x USB OTG MAC/PHY
* 24-bit LCD-TFT interface with 2D graphics accelerator
* RTC with battery-backed NVRAM
* MIPI CSI Camera interface
* Multiple UART interfaces
* Multiple SPI bus interfaces
* Multiple I2C bus interfaces
* Multiple CAN bus interfaces

In terms of [CoreMark] performance, the previous model of this chip (the iMXRT1050), when running from internal SRAM, stacks up like this:

| CPU                       | CoreMarks |
|---------------------------|-----------|
| Pentium @ 100 MHz         | 213       |
| Pentium III @ 866 MHz     | 1,950     |
| Raspberry Pi Zero @ 1 GHz | 2,060     |
| iMXRT1050 @ 600 MHz       | 3,040     |
| Raspberry Pi 3 @ 1.2 GHz  | 15,400    |
| Core i7-3930K @ 3.2 GHz   | 116,000   |

[CoreMark]: https://www.eembc.org/coremark/

Obviously, accessing external QuadSPI RAM or Flash will incur a performance penalty, but we're still talking good Pentium-II era levels of performance. Maybe enough to run a source port of Doom? Or even Quake?

### Teensy 4.1

The Teensy 4.1 takes the CPU and adds:

* USB micro-B connector
* 8 MiB external Flash
* Pads for an 8-pin external QuadSPI PSRAM
* Pads for an additional 8-pin external QuadSPI Flash
* An Ethernet PHY
* A micro SD slot
* Lots of easy to solder plated-through holes with a 2.54mm pitch

Our Motherboard matches the Teensy 4.1 pinout, allowing you to solder some headers (sockets one side, pins the other) and drop the Teensy right on.

### Memory Layout

We expect to use:

* Around 128 KiB of tightly-coupled SRAM for the system stack, OS buffers, and performance-critical routines.
* The remaining 896 KiB SRAM is split between video frame buffers and user applications
  * 640x480 @ 8bpp = 300 KiB, leaving 596 KiB for applications
  * 800x600 @ 8bpp = 468 KiB, leaving 428 KiB for applications
* 4 KiB NVRAM for system settings
* 512 KiB Flash for the Neotron BIOS
* 512 KiB Flash for Neotron OS
* Remaining 7 MiB of Flash for ROM-based user applications

## The Motherboard

The Neotron 600 Motherboard is completely open-source hardware, and most of the components are through-hole parts.

The motherboard has:

* Regulated 5V Input
  * If you want to power four 500 mA USB devices from your Neotron 600, this needs to be capable at least 2.5A
  * TBD whether this is a DC barrel jack, full-size USB B, mini USB B or micro USB B
  * Or even take 5V from a 110V/240V IEC inlet PSU
  * Or we could take 6V-9V from a DC barrel jack and have an on-board 5V regulator
* Pin headers to accept your Teensy 4.1
* RJ45 Ethernet Magjack
* Cirrus Logic WM8731SEDS Audio Codec (28-SSOP footprint)
  * 16-bit 48 kHz I2S input and output to the Teensy
  * Amplified stereo 3.5mm TRS headphone output
  * Kycon PC99 triple-jack (STX-4335-5BGP-S1)
    * Line-level stereo 3.5mm TRS output
    * Line-level stereo 3.5mm TRS input
    * Mono microphone 3.5mm input
* Cypress CY7C65634 4-port USB Hub
  * USB Full-Speed (11 Mbit/sec)
  * 2x USB A ports on-board
  * Header for further 2x USB ports
* 7-bit (2-3-2) R2R DAC and HD video output buffer, with standard DE15HD VGA connector
* Full-size external-facing SD Card slot (in addition to micro SD slot on Teensy)
* 2x PS/2 Ports for keyboard and mouse (2x mini-DIN 6-pin sockets)
* 2x 9-pin Atari Joystick/SEGA Genesis game-pad ports
* Power switch and reset switch
* MIDI In and Out ports (2x 5-pin DIN180 sockets)
* IEEE1284 Parallel Port (26-pin IDC box header as used on PCs)
* RS232 Port (five-wire, 10-pin IDC box header as used on PCs)
* Coin-cell for the real-time clock and NVRAM
* SPI and I2C based expansion bus (compatible with the Neotron 32)

Expected price is $10 for the PCB, $25 for the Teensy, and another $30 for the through-hole connectors and other parts.

## Software

This system is designed to run the Neotron OS. To do that, it has a bespoke version of the Neotron BIOS. It is compatible with all 'well-behaved' Neotron applications that stick to the Neotron OS APIs rather than poking hardware directly.
