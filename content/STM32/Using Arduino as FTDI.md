# Using Arduino Uno
1. Remove the ATMEGA 328P IC from the Arduino Uno
2. make sure the `Boot0` jumper is set to `1` on the blue pill. 
3. Connect Arduino to the STM32

| Arduino Uno | STM32                                                              |
| ----------- | ------------------------------------------------------------------ |
| 3.3V        | 3.3V                                                               |
| GND         | GND                                                                |
| RX          | A9 (or serial TX when the STM is in bootloader mode for flashing)  |
| TX          | A10 (or serial RX when the STM is in bootloader mode for flashing) |
| RESET       | RESET                                                              |
- press and hold reset, click connect on stm32cubeprogrammer, then release

3. Adjust the settings on the STM32Cube Programmer like below. Check the COM Port for the Arduino Uno. You can find this in `Device Manager` on Windows.  
4. Set the baud rate to 9600. This is what worked for me, higher baud rates seemed to connect, cannot read the memory of the chip, then disconnect. 
![[Pasted image 20240625194335.png]]
If the flash memory is read protected, click to check the `Read Unprotect (MCU)`. Which would (do some stuff and) disconnect. Uncheck it and run it again and the flash memory should be accessible but completely erased. 
4. Select and flash your compiled file. By default, when you build the project in STM32CubeIDE, it should generate a `.elf` file. This would be the file you would select in the STM32CubeProgrammer to be flashed. It should be in the `\<project folder>\Debug\<file>.elf`.
5. Disconnect from UART, move the `Boot 0` jumper to `0`, and reboot the board and the program should run. 

# Using Arduino Nano
- I am yet to test one. I do not have a genuine Nano which I am sure should be easier to use. For ones with CH340 chip, [this](https://forum.arduino.cc/t/arduino-nano-ch340g-based-usb-ttl-adapter/479991) might  help. 


>[!Notes]
>1. I am not certain if there are other boards that could be used as a USB to TTL converter but it is certainly worth the shot, especially if you do not have a programmer readily available. 
>2. This may also be used to program other boards that require FTDIs to program them such as the `Arduino Pro Mini`. I will update if it works on other boards apart form STM boards. 
>3. I have seen some Arduino Unos with square ATMEGA chipsets that are soldered to the board and hence do not have the IC removable. I am yet to test this but I presume grounding the reset line should achieve the same effect. 