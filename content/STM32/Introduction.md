# Introduction
I am going to start off by dumping in a bunch of notes and organizing it as much as possible

# Board Description 
[STM32F103C8T6](https://www.st.com/en/microcontrollers-microprocessors/stm32f103c8.html)

STM32F103xx performance line family incorporates the high-performance ARM® Cortex™-M3 32-bit RISC core operating at a 72 MHz frequency, high-speed embedded memories, and an extensive range of enhanced I/Os and peripherals connected to two APB buses. All devices offer two 12-bit ADCs, three general purpose 16-bit timers plus one PWM timer, as well as standard and advanced communication interfaces: up to two I2Cs and SPIs, three USARTs, an USB and a CAN. The STM32F103xx low-density performance line family operates from a 2.0 to 3.6 V power supply. It is available in both the –40 to +85 °C temperature range and the –40 to +105 °C extended temperature range. A comprehensive set of power-saving mode allows the design of low-power applications.


The STM32F103xx low-density performance line family includes devices in four different package types: from 36 pins to 64 pins. Depending on the device chosen, different sets of peripherals are included, the description below gives an overview of the complete range of peripherals proposed in this family. 

The board has 3 GPIO ports; Port A, B and C. Every pin associated with a particular port is tied to a single register and therefore for more efficient programs that have ports linked together, it would be more efficient to use pins in the same Port. Every pin on 


## Specs

With respect to the STM32F103C8T6, It has less Flash and SRAM, one less timer, and one less Serial.

STM32F103C6T6

STM32F103C8T6

Flash

32K

64K

RAM

10K

20K

Timer

3 (No timer 4)

4

Serial port

2 (No serial port 3)

3

I2C

1

2

SPI

1

2+

-   ARM 32-bit Cortex™-M3 CPU Core
    -   72 MHz maximum frequency,1.25 DMIPS/MHz (Dhrystone 2.1) performance at 0 wait state memory access
    -   Single-cycle multiplication and hardware division
-   Memories
    -   16 or 32 Kbytes of Flash memory
    -   6 or 10 Kbytes of SRAM
-   Clock, reset and supply management
    -   2.0 to 3.6 V application supply and I/Os
    -   POR, PDR, and programmable voltage detector (PVD)
    -   4-to-16 MHz crystal oscillator
    -   Internal 8 MHz factory-trimmed RC
    -   Internal 40 kHz RC
    -   PLL for CPU clock
    -   32 kHz oscillator for RTC with calibration
-   Low power
    -   Sleep, Stop and Standby modes
    -   VBAT supply for RTC and backup registers
-   2 x 12-bit, 1 μs A/D converters (up to 16 channels)
    -   Conversion range: 0 to 3.6 V
    -   Dual-sample and hold capability
    -   Temperature sensor
-   DMA
    -   7-channel DMA controller
    -   Peripherals supported: timers, ADC, SPIs, I2Cs and USARTs

-   Up to 51 fast I/O ports
    -   26/37/51 I/Os, all mappable on 16 external interrupt vectors and almost all 5 V-tolerant
-   Debug mode
    -   Serial wire debug (SWD) & JTAG interfaces
-   6 timers
    -   Two 16-bit timers, each with up to 4 IC/OC/PWM or pulse counter and quadrature (incremental) encoder input
    -   16-bit, motor control PWM timer with dead-time generation and emergency stop
    -   2 watchdog timers (Independent and Window)
    -   SysTick timer 24-bit downcounter
-   6 communication interfaces
    -   1 x I2C interface (SMBus/PMBus)
    -   2 × USARTs (ISO 7816 interface, LIN, IrDA capability, modem control)
    -   1 × SPI (18 Mbit/s)
    -   CAN interface (2.0B Active)
    -   USB 2.0 full-speed interface
-   CRC calculation unit, 96-bit unique ID

## How to

1.  [STM32F1 Blue-Pill: pinout, specs, and Arduino IDE configuration (STM32duino and STMicroelectronics)](https://www.mischianti.org/2022/05/24/stm32f1-pinout-specs-and-arduino-ide-configuration-stm32duino-and-stmicroelectronics-1/)
2.  [STM32: program (STM32F1) via USB with STM32duino bootloader](https://www.mischianti.org/2022/06/09/stm32-programming-stm32f1-via-usb-with-stm32duino-boot-loader-2/)
3.  [STM32: programming (STM32F1 STM32F4) via USB with HID boot-loader](https://www.mischianti.org/2022/06/20/stm32-programming-stm32f1-stm32f4-via-usb-with-hid-boot-loader-3/)
4.  [STM32F4 Black-Pill: pinout, specs, and Arduino IDE configuration](https://www.mischianti.org/2022/07/02/black-pill-stm32f4-pinout-specs-and-arduino-ide-configuration-4/)
5.  [STM32: ethernet w5500 with plain HTTP and SSL (HTTPS)](https://www.mischianti.org/2022/07/13/stm32-ethernet-w5500-with-plain-http-and-ssl-https/)
6.  STM32: ethernet enc28j60 with plain HTTP and SSL (HTTPS)
7.  STM32: WiFiNINA with ESP32 WiFi Co-Processor
8.  How to use SD card with stm32 and SdFat library
9.  STM32: SPI flash memory FAT FS
10.  STM32: internal RTC, clock, and battery backup (VBAT)

11.  STM32 Power saving
    1.  STM32F1 Blue-Pill clock and frequency management
    2.  STM32F4 Black-Pill clock and frequency management
    3.  Intro and Arduino vs STM framework
    4.  Library LowPower, wiring, and Idle (STM Sleep) mode
    5.  Sleep, deep sleep, shutdown, and power consumption
    6.  Wake up from RTC alarm and Serial
    7.  Wake up from the external source
    8.  Backup domain intro and variable preservation across reset
    9.  RTC backup register and SRAM preservation


Sinusoidal instead of trapezoidal controls 

# Pinout
![[Goliath Labs/Img/STM32F103C6 Pinout.png]]
# Understanding STM32 Naming Conventions

## 5/20/2020 | By Maker.io Staff
![Understanding STM32 Naming Conventions](https://www.digikey.com/-/media/MakerIO/Images/blogs/2020/Understanding%20STM32%20Naming%20Conventions/Understanding-STM32-Naming-Conventions.jpg?la=en-US&ts=638cd51f-e2bd-4a16-b2d0-97db568cb2a0 "Understanding STM32 Naming Conventions")

The [STM32](https://www.digikey.com/en/products/filter/embedded-microcontrollers/685?s=N4IgTCBcDaIKIFsBGBTAJm9ACAtFgsgJYDGATgPbHkB2ALhQDYMqkDOIANCAMoAq%2BAZjAAZAAwgAugF8gA) is a 32-bit family of microcontrollers produced by STM based on popular 32-bit ARM RISC processing cores. Many of these MCUs come with numerous features, and it’s often quite hard to make sense of the name that’s printed on the IC’s case. However, these descriptors aren’t chosen at random. They follow a clever naming convention, and in this blog, we’ll discuss what each part of that identifier means so that you can quickly decipher the cryptic model numbers in the future!

### Dissecting the Model Identifier for STM32

This article will use the [STM32L151R8T6](https://www.digikey.com/en/maker/blogs/2020/understanding-stm32-naming-conventions) to explain what each part of its name means.

The name of every STM32 MCU starts with STM32, which denotes the MCU family. That is then followed by a single uppercase letter, which describes the type. The number that follows tells you which ARM Core was used. The next two numbers describe the line of the MCU. The line describes the features of this particular device, for example, the peripherals, and the speed. The following letter denotes the number of pins. After that comes either a number or a letter that denotes the MCU’s flash-memory size. This is followed by the package descriptor. The last number informs you about the device’s operating temperature range:

![Understanding STM32 Naming Conventions](https://www.digikey.com/-/media/MakerIO/Images/blogs/2020/Understanding%20STM32%20Naming%20Conventions/model_A.jpg?la=en&ts=ade1a469-05fb-4495-ab91-5f99f81ad324 "Understanding STM32 Naming Conventions")

### Understanding the different values

The following tables will help you understand what each value within every field mentioned above means.

![Understanding STM32 Naming Conventions](https://www.digikey.com/-/media/MakerIO/Images/blogs/2020/Understanding%20STM32%20Naming%20Conventions/Table_1.jpg?la=en&ts=a261c593-8694-49ce-9c75-0203b511b4b3 "Understanding STM32 Naming Conventions")

It’s interesting to note that the names largely follow a scheme, however, they evolve with the products, and some names fall out of the scheme. For example, the STM32 F2 series, which is a high-performance MCU. Next, the core:

![Understanding STM32 Naming Conventions](https://www.digikey.com/-/media/MakerIO/Images/blogs/2020/Understanding%20STM32%20Naming%20Conventions/Table_2.jpg?la=en&ts=cc625d6c-d4ba-4398-8e27-12c843a468f4 "Understanding STM32 Naming Conventions")

Note that some numbers might refer to the same core, for example, one and two. For the line of the MCU, refer to the STM product page, as this is not universal among the devices.

![Understanding STM32 Naming Conventions](https://www.digikey.com/-/media/MakerIO/Images/blogs/2020/Understanding%20STM32%20Naming%20Conventions/Table_3.jpg?la=en&ts=75dd5f39-9d54-44c6-b4bf-d0b5c25d7f9e "Understanding STM32 Naming Conventions")

![Understanding STM32 Naming Conventions](https://www.digikey.com/-/media/MakerIO/Images/blogs/2020/Understanding%20STM32%20Naming%20Conventions/Table_4.jpg?la=en&ts=900b3625-e38e-4a29-8c2e-1cf696fc9316 "Understanding STM32 Naming Conventions")

![Understanding STM32 Naming Conventions](https://www.digikey.com/-/media/MakerIO/Images/blogs/2020/Understanding%20STM32%20Naming%20Conventions/Table_5.jpg?la=en&ts=60d5382b-8a3a-4832-a2d4-aa1d012fe7c8 "Understanding STM32 Naming Conventions")

And last, but not least, the temperature range:

![Understanding STM32 Naming Conventions](https://www.digikey.com/-/media/MakerIO/Images/blogs/2020/Understanding%20STM32%20Naming%20Conventions/Table_6.jpg?la=en&ts=d44f1a2f-1990-48e8-9a63-063bf9d24821 "Understanding STM32 Naming Conventions")

The beginning of this article featured the [STM32L151R8T6](https://www.digikey.com/en/maker/blogs/2020/understanding-stm32-naming-conventions) as an example. With the tables presented to you in this article, you should quickly be able to decipher that name. If you do so, you'll see that this is a low-power Cortex M3 MCU with 64 KByte of flash memory that comes in a 64-pin LQFP package that'll operate in a temperature range between -40°C and 85°C.

### Using STM32 Naming Conventions to Your Advantage

Almost all models of the STM32 family of MCUs will follow these naming conventions. However, in some rare cases, there might be inconsistencies. To decode the identifier of an MCU, break up its name in the described parts and use the tables to decipher the values.

# Using STM32CubeIDE
We would be using STM32CubeIDE which is the official eclipse-based IDE supported by ST Microelectronics. Fortuanltely it has the CubeMX now built into it so that you could setup hardware and peripherals at ease while still being in the same app. You can get there by clicking on your .ioc file in your main directory. Also the CubeMX has the potential of generating some portions of the code for you so that you would not have to type it our after using the tool. 
# Using ST7565
# Programming STM32
There are two ways of doing this: 
- Burning bootloader so Arduino is available to use with
- Serial programming with an adaptor 
We are using and an [FT232RL FTDI USB to TTL Serial Module 3.3V/5V](https://www.okuelectronics.com/shop/ft232rl-ftdi-usb-to-ttl-serial-module/) to program the board through serial communication. This is a temporary fix for using STLink which we do not have atm this was documented.
## Using FT232RL FTDI USB to TTL Serial Module 3.3V/5V
1. Download and install drivers for the module based on your OS [here](https://www.usb-drivers.org/ft232r-usb-uart-driver.html) 
2. Before downloading program onto board, ensure the board is in programming mode and connected like this. ![[Pasted image 20221030082925.png]]
Sidenote: Programming mode is enabled by the configuration of the jumpers (represented by yellow rectangles in image above) and you could use other pins apart from A9 and A10 as long as they are Hardware UART pins. Many FTDIs also come with the option of 5V or 3V3 which can be changed with a jumper or a switch so make sure it's on the right voltage for the board (in this case 3V3). 
3. Open STM32CubeProgrammer, select UART from the list, select the right COM port and connect. 

# Blinking Internal LED
Just like the Arduino there is also an internal LED that is hooked up to Pin 13 on the board (PC13 to be exact). Then where you would write your main code would be in the while loop labelled like this:
```c
while (1)
  {
	HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, 1);
	HAL_Delay(500);
	HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, 0);
	HAL_Delay(500);
    /* USER CODE END WHILE */

    /* USER CODE BEGIN 3 */
  }
```
Then you would put your code between those brackets in the loop. This is like your void loop in Arduino. 

# References 
https://www.mischianti.org/2022/05/15/stm32f103c6t6-high-resolution-pinout-and-specs/
https://www.digikey.com/en/maker/blogs/2020/understanding-stm32-naming-conventions
[https://deepbluembedded.com/stm32-arm-programming-tutorials/](https://deepbluembedded.com/stm32-arm-programming-tutorials/)
https://stm32-base.org/boards/STM32F103C8T6-Blue-Pill
https://controllerstech.com/gps-neo-6m-with-stm32/
https://youtu.be/IrB-FPcv1Dc
http://bitmasktech.com/stm32-blue-pill-programming-using-ftdi/
https://www.teachmemicro.com/getting-started-blue-pill-stm32cube/
http://bitmasktech.com/stm32-blue-pill-programming-using-ftdi/
https://deepbluembedded.com/stm32-serial-port-uart-with-usb-ttl-converter-pc-interfacing/
https://www.partitionwizard.com/partitionmanager/ft232r-usb-uart-driver.html
https://deepbluembedded.com/stm32-gpio-write-pin-digital-output-lab/




# Stuff to Look at Later on 
- https://github.com/VeryBusyBee/FiatMon/blob/master/Core/Src/sleep.cpp
	- This contains mockup on CAN controls with screen no interfering