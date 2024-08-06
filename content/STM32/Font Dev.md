
# Using U8g2 with STM32
1. Download the repository for U8g2 from GitHub here https://github.com/olikraus/u8g2
2. Copy the "csrc" folder within the downloaded and extracted repo into "Src" folder in STM32 project
3. Rename the folder "U8g2"
4. Head to "mui_U8g2.h" and add 
```c
#include "u8g2.h"
```
5. Add to user includes
```c
#include "u8g2/u8g2.h"
```
6. Add to private user code
> [!Note] This is the SPI GPIO Implementation of the callback function with PA04 = CS, PA01 = DC/RS and PA00 = RESET. I2C implementation would differ but have similar structure. 
>  

```c
uint8_t u8g2_gpio_and_delay_stm32(u8x8_t *u8x8, uint8_t msg, uint8_t arg_int, U8X8_UNUSED void *arg_ptr)
{
	switch (msg)
	  {
	  case U8X8_MSG_GPIO_AND_DELAY_INIT:
	    HAL_Delay(1);
	    break;
	  case U8X8_MSG_DELAY_MILLI:
	    HAL_Delay(arg_int);
	    break;
	  case U8X8_MSG_GPIO_CS:
		/* Insert codes for SS pin control */
		HAL_GPIO_WritePin(GPIOA, GPIO_PIN_4, arg_int);
		break;
	  case U8X8_MSG_GPIO_DC:
	    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_1, arg_int);
	    break;
	  case U8X8_MSG_GPIO_RESET:
	    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_0, arg_int);
	    break;
	  }
	  return 1; // command processed successfully.
}

```
7. Add to user code in main function
```c
u8g2_t u8g2;
```
8. Add the SPI initialization to user code
```c
uint8_t u8x8_byte_4wire_hw_spi(u8x8_t *u8x8, uint8_t msg, uint8_t arg_int, void *arg_ptr)
        {
          switch (msg)
          {
          case U8X8_MSG_BYTE_SEND:
            HAL_SPI_Transmit(&hspi1, (uint8_t *) arg_ptr, arg_int, 10000);
            break;
          case U8X8_MSG_BYTE_INIT:
            break;
          case U8X8_MSG_BYTE_SET_DC:
            HAL_GPIO_WritePin(GPIOA, GPIO_PIN_1, arg_int);
            break;
          case U8X8_MSG_BYTE_START_TRANSFER:
            break;
          case U8X8_MSG_BYTE_END_TRANSFER:
            break;
          default:
            return 0;
          }
          return 1;
        }

```
9. Finally initialize the library for use
```c
u8g2_Setup_st7565_erc12864_f(&u8g2, U8G2_R0, u8x8_byte_4wire_hw_spi, u8g2_gpio_and_delay_stm32);
    u8g2_InitDisplay(&u8g2); // send init sequence to the display, display is in sleep mode after this,
    u8g2_SetPowerSave(&u8g2, 0); // wake up display
    u8g2_SetContrast(&u8g2, 10);

```
8. Write your own code before and within the while loop. An example is show below
```c
u8g2_ClearBuffer(&u8g2);
  //  u8g2_SetBitmapMode(&u8g2,1);
  //  u8g2_DrawBitmap(&u8g2, 0, 0, bitmap_height, bitmap_width,  bitmap);
  //  u8g2_DrawXBM(&u8g2, 0, 0, 58, 64,  GR);
    u8g2_SendBuffer(&u8g2);
    HAL_Delay(1500);
    u8g2_ClearBuffer(&u8g2);
  /* USER CODE END 2 */

  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
	  HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, 1);
	  HAL_Delay(500);
	  HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, 0);
	  HAL_Delay(500);

	 	  u8g2_FirstPage(&u8g2);
	 	      do
	 	      {
	 	    	  int yCoord = 11;
	 	    	  while (yCoord < 39)
	 	    	  {
	 	    		  u8g2_DrawBox(&u8g2, 116, yCoord, 10, 6);
	 	    	    yCoord += 8;
	 	    	  }
	 	    	  u8g2_DrawFrame(&u8g2, 116, 43, 10, 6);
	 	    	  u8g2_DrawCircle(&u8g2, 8, 9, 4, U8G2_DRAW_ALL);
	 	    	  u8g2_DrawCircle(&u8g2, 8, 20, 4, U8G2_DRAW_ALL);
	 	    	  u8g2_DrawCircle(&u8g2, 8, 31, 4, U8G2_DRAW_ALL);
	 	    	  u8g2_DrawCircle(&u8g2, 8, 42, 4, U8G2_DRAW_ALL);
	 	    	  u8g2_DrawDisc(&u8g2, 8, 53, 4, U8G2_DRAW_ALL);
	 	    	  u8g2_SetFont(&u8g2, AEB_24_font);
	 	    	  u8g2_DrawStr(&u8g2,42, 40, "15");
	 	    	  u8g2_SetFont(&u8g2, u8g2_font_6x10_tr);
	 	    	  u8g2_DrawStr(&u8g2,67, 31, "km/h");
	 	    	  u8g2_DrawStr(&u8g2,50, 57, "09:23");
	 	    	  u8g2_DrawStr(&u8g2, 110, 10, "80%");
	 	    	  u8g2_DrawXBM(&u8g2, 105, 50, 16, 14,  epd_bitmap_plug_circle_bolt_solid_resize);
	 	      } while (u8g2_NextPage(&u8g2));

```
The official font for Goliath Robotics is Adelle Regular. 

Use a website to convert image to cpp. 


# Using any custom font
First download the font which comes usually zipped and extract it. In the zipped file you would usually find it in a format apart from bdf which we are gauging. Especially with Adelle Regular which is a font from Adobe, it comes in otf files. OpenType Font was developed by Adobe and Microsoft for saving their font types. Use [Vertopal Converter](https://www.vertopal.com/en/convert/otf-to-fon) or any convenient converter to change the font from otf to fon. Change the conversion settings to the correct pixel height that would be printed on the screen. [[http://hukka.ncn.fi/?fony|Fony]] is a used to convert fon file typed to bdf. Open fon file with Fony and export as bdf. Convert the bdf into c code that can be placed in the library by using the command 

- Move the bdf file into the same directory as the tool which is in:
```
...u8g2\tools\font\bdfconv
```
- Run the following command in the terminal opened from that directory
```
bdfconv -f 1 -m '32-255' -n fontname -o myfont.c myfont.bdf
```
where;
	- "-f 1" generates a u8g2 font.
	- "-m '32-255'" selects unicode 32 to 255. On Windows, please use double quotes: -m "32-255"
	- "-n fontname": This is the name of the font in C/C++/Ino files.
	- "-o myfont.c": The font array will be stored in this file.
	- "myfont.bdf": The input file with the font data (bdf format).
- Open the generated file in the same directory and copy the entire text. 
- look for the file ../u8g2_fonts.c in your workspace in STM32 IDE with your copied text looking something like this:
```
const uint8_t font_name[5626] U8G2_FONT_SECTION("font_name") =
  "\340\0\4\4\5\5\4\5\6\32\36\0\372\22\373\22\374\2\304\6\235\25\335 \6\0 ,\1!\17E"
  "&<\341\7)\210\214\33\241b\4\0\42\11\311\344V\201\4\276 #\34\255%|g\310\250!cX"
  ",Y$d\324\220\61,\270\31\61l\304\60!\203\0$\31kfk\247X\251\24n\320$C\243$"
  ...;
```
- Add the copied text at the bottom of the file
- Add the array declaration in u8g2.h to the existing declarations as;
```
extern const uint8_t font_name[] U8G2_FONT_SECTION("font_name");
```
- Use the font like any other u8g2 font (if the character is available in the font)



https://forum.arduino.cc/t/u8g2-custom-font-tutorial-with-fony/686061



# Todo 
Repurpose A2 and A3 from the screen
GPS antennas
Save memory by taking only alphanums we need


PWM for the screen brightness






