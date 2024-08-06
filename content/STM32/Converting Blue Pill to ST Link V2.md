
# Making your own ST-LINK V2 from STM32 Blue-Pill or STM32 Black-Pill


1. Move the `Boot0` jumper from 0 to 1
2. Connect the FTDI to the STM32 Board. Refer to [[Using Arduino as FTDI]] if you do not have the FTDI to perform this project. Also refer to your private OneDrive folder for all the resources used in this tutorial. **Be careful to set adapter voltage to 3.3V!!!**
![[Pasted image 20240625194200.png]]
3. Flash the STM32F103C8T6 with the `STLinkV2.J16.S4.bin` from the folder. 
>[!Warning]
>The device you intend to flash must have at least 64Kb of flash memory. This means STM32F103C6T6A will not work since it has 32Kb of flash. Some ST-Link bootloaders are able to work on this but I am yet to figure out how to make them work!

4.  Now we disconnect everything and connect the board to computer with micro USB cable and also move the BOOT0 jumper back to 0. Use the `ST-LinkUpgrade.exe` tool to update the new ST-Link V2. 
>[!Warning ]
>The ST-LinkUpgrade tool is an old one. Using a newer version of this will not work 

5. Connect to the new ST-Link using the STM32CubeProgrammer. Click on Firmware Update there. Keep unplugging and click refresh and update and it should pick it up. 
6. Now all we need to do is test it. I am testing it according to diagram below with another Blue Pill (**don’t miss that short connection between PB12 and PB14!!!**):![](http://slemi.info/wp-content/uploads/2018/08/ST-LINK-Testing.png)

# References 
- [st-link-v2-clone/README.md at main · Zelmoghazy/st-link-v2-clone (github.com)](https://github.com/Zelmoghazy/st-link-v2-clone/blob/main/README.md)
- [Making your own ST-LINK V2 from STM32 Blue-Pill or STM32 Black-Pill | STM32 Programming | Slemi's webpage](http://slemi.info/2018/08/14/making-your-own-st-link-v2/)
- 