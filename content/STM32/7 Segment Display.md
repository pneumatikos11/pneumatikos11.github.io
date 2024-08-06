###   **7 Segments Display Interfacing**  

**7 Segments Display Internally**

![](https://deepbluembedded.com/wp-content/uploads/2020/07/7-segment-display.png?ezimgfmt=rs:326x222/rscb6/ng:webp/ngcb6)

The 7-Segment display units are basically some LED bars arranged as shown in this picture above. Two types are available to use (common anode and common cathode). The one shown above is a common cathode 7-Segment display unit. For this, you need to give each LED a positive voltage in order to turn it one while the common pin is hooked to the ground. And in common anode type of 7-Segments, you’ll have to drive each LED pin low in order to turn it ON while the common anode pin is hooked to Vdd+.

Controlling the 7-Segment display should be an easy task to do as it doesn’t require anything special other than turning the output pins HIGH & LOW. And just don’t forget to use current-limiting resistors on the output pins. Not all microcontrollers can directly drive LEDs with too much current. Using a CCS (constant current source) can also help to make the brightness of the LED bars evenly distributed.

You can use the following online tool to generate the code for any 7-segment display pattern or number you need to display in your project, it’s a very handy tool.

[7-Segment Display Code Generator Online Tool](https://deepbluembedded.com/seven-segment-display-generator/)

**Interfacing 7 Segments Display**

With that being said, a 7-Segment display unit requires 7 pins to be hooked to the (A-B-C-…-G) pins. And if you need to display more than one digit, it’ll turn out to be a waste of GPIO pins. Imagine having to display a 3-Digit value, that’s a 21 GPIO pin required. So, if we’re going to control only one 7-Segment display unit, we can directly do it or use a BCD-To-7Seg converter IC to reduce the required pin count. And if more than one digit is to be displayed, then we do what is called multiplexing as demonstrated in the next section.

**POV & 7-Segments Display**

Multiple 7-Segment display units can be all hooked together to the same GPIO lines of the microcontroller. And multiplexed by control signals which means the 7-Segment display units are not going to be working together all the time. But they’ll be turned ON and OFF one by one. Doing this so quickly will make the human brain see the multiple 7-Segment units as if they are all turned ON together, and each of which is displaying a different number. This effect is called persistence of vision (POV) and there is a link to an article on that topic at the end of this section as well.

The diagram below show you how can a 3-Digit 7-Segment display unit be hooked to a microcontroller. For this, we use 3 driving transistors to switch ON and OFF each 7-Segment unit individually and perform the multiplexing process to show the right number on the right digit at the right time. And keep repeating!

![7 Segment Display Interfacing With STM32](https://deepbluembedded.com/wp-content/uploads/2020/07/7-Segment-Display-Interfacing-With-STM32.jpg?ezimgfmt=rs:727x477/rscb6/ng:webp/ngcb6)

|   |   |
|---|---|
|**That was a quick introduction for 7Segments Display. For more info, I’d highly recommend those articles below as well.**|   |
|[![Multi-Digit 7Segments Tutorial Thumbnail](https://deepbluembedded.com/wp-content/uploads/2019/11/Multi-Digit-7Segments-Tutorial-Thumbnail-300x142.jpg?ezimgfmt=rs:300x142/rscb6/ng:webp/ngcb6)](https://deepbluembedded.com/multiplexing-7-segment-displays-3-digit-7-segment-interfacing-with-pic-microcontrollers/)|[![How To Interface 7-Segments Display With Microcontrollers](https://deepbluembedded.com/wp-content/uploads/2018/07/How-To-Interface-7-Segments-Display-With-Microcontrollers-300x142.jpg?ezimgfmt=rs:300x142/rscb6/ng:webp/ngcb6)](https://deepbluembedded.com/interfacing-7-segments-display-with-pic-microcontrollers/)|

You can use the following online tool to generate the code for any 7-segment display pattern or number you need to display in your project, it’s a very handy tool.

[**7-Segment Display Code Generator Online Tool**](https://deepbluembedded.com/seven-segment-display-generator/)

---

###    **STM32 7-Segment Display ECUAL Driver**   

The ECUAL 7-Segments driver is built for STM32 microcontrollers using some GPIO pins. You’ll have to configure an instance of it and use the APIs to control your 7-segment display and that’s all. The code should be easily ported to any other STM32 microcontroller or reconfigured to use any GPIO pins you want just as we’ll see in this section. And here is a link for the [course’s repo](https://github.com/Khaled-Magdy-DeepBlue/STM32_Course_DeepBlue), and you’ll find the SEVEN_SEGMENTS driver in the ECUAL directory as usual.

**7 Segment Display Driver Code Files**

The 7-Segments Display driver consists of the following files:

|   |   |
|---|---|
|- SEVEN_SEGMENTS.h<br>- SEVEN_SEGMENTS.c<br>- SEVEN_SEGMENTS_cfg.h<br>- SEVEN_SEGMENTS_cfg.c|![7 Segment Display Interfacing With STM32 Microcontrollers](https://deepbluembedded.com/wp-content/uploads/2020/07/7-Segment-Display-Interfacing-With-STM32-Microcontrollers.jpg?ezimgfmt=rs:183x95/rscb6/ng:webp/ngcb6)|

You’ll need only to modify the configuration files. The source code for this driver is found in (SEVEN_SEGMENTS.c) and to use it you’ll include the header (SEVEN_SEGMENTS.h). Nothing in the source code needs to be changed at all unless you need to add any extra features or customize the driver for your application’s needs. For today’s labs, we’ll only be changing the configuration files to build some test applications.

Therefore, I’ll write here the code listing for the SEVEN_SEGMENTS.h & SEVEN_SEGMENTS_cfg.c files.

**SEVEN_SEGMENTS.h File**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24|#define HAL_GPIO_MODULE_ENABLED<br><br>#include "stm32f1xx_hal.h"<br><br>// The Number OF 7-Segments Display Units To Be Used In The Project<br><br>#define SEVEN_SEG_UNITS  1<br><br>#define DIGITS           3<br><br>typedef struct<br><br>{<br><br>    GPIO_TypeDef * SEG_GPIO[7];<br><br>    uint16_t       SEG_PIN[7];<br><br>    GPIO_TypeDef * EN_GPIO[DIGITS];<br><br>    uint16_t       EN_PIN[DIGITS];<br><br>}SEVEN_SEG_CfgType;<br><br>/*-----[ Prototypes For All Functions ]-----*/<br><br>void SEVEN_SEG_Init(uint16_t au16_Instance);<br><br>void SEVEN_SEG_Write(uint16_t au16_Instance, uint16_t au16_Data);<br><br>void SEVEN_SEG_Enable(uint16_t au16_Instance);<br><br>void SEVEN_SEG_Disable(uint16_t au16_Instance);<br><br>void SEVEN_SEG_Main(void);|

If you’re willing to use many 7-Segment display units, just adjust that number definition (SEVEN_SEG_UNITS).

**SEVEN_SEGMENTS_cfg.c File**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15|#include "SEVEN_SEGMENTS.h"<br><br>const SEVEN_SEG_CfgType SEVEN_SEG_CfgParam[SEVEN_SEG_UNITS] =<br><br>{<br><br>    // 7-Segments Display Unit 1 Configurations<br><br>    {<br><br>        /* Seven Segments Pins Info */<br><br>        {GPIOA, GPIOA, GPIOA, GPIOA, GPIOA, GPIOA, GPIOA},<br><br>    {GPIO_PIN_0, GPIO_PIN_1, GPIO_PIN_2, GPIO_PIN_3, GPIO_PIN_4, GPIO_PIN_5, GPIO_PIN_6},<br><br>        /* Enable Control Signal's Pins */<br><br>    {GPIOB, GPIOB, GPIOB},<br><br>    {GPIO_PIN_14, GPIO_PIN_13, GPIO_PIN_12}<br><br>    }<br><br>};|

I’ll discuss those configuration parameters in the next section down below.

**7-Segments Display Driver APIs**

As you’ve seen in the SEVEN_SEGMENTS.h file, the provided APIs does all the basic functionalities that you may need from a 7-Segment Display driver library. It initialized the required GPIO pins to control the 7-Segment lines and the driving transistors.

The SEVEN_SEG_Write function takes the integer you want to display and pare it into separate digits. The SEVEN_SEG_Main function does the job of sending the data and controlling the drive lines (multiplexing) and all of the logical operations. This can be easily used with any operating system such as what we’re going to build in the future.

The structure of the files and the provided APIs look very intuitive and self-explanatory to me. And the example LABs will put everything under test and you’ll see how and when to use each of these functions.

**Available Configurations For ECUAL 7-Segments Driver**

From the code above in SEVEN_SEG.h & SEVEN_SEG_cfg.c you can see that there are not many parameters in the configuration structure. Only the GPIO pins that you’re willing to use for the 7-Segment display.

**Typical Usage Application Example**

Here is a typical usage application for this driver in order to initialize a seven-segment unit instance and read a temperature sensor’s value and write it to the 7-segment display. And also note that the seven segments main function is called from the SysTick timer interrupt handler once each 5msec.

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36<br><br>37<br><br>38<br><br>39|#include "main.h"<br><br>#include "../ECUAL/SEVEN_SEGMENTS/SEVEN_SEGMENTS.h"<br><br>#include "../ECUAL/LM35/LM35.h"<br><br>uint8_t SysTicks = 0;<br><br>void SysTick_CallBack(void);<br><br>void SystemClock_Config(void);<br><br>static void MX_GPIO_Init(void);<br><br>int main(void)<br><br>{<br><br>    uint16_t Temperature = 0;<br><br>    HAL_Init();<br><br>    SystemClock_Config();<br><br>    MX_GPIO_Init();<br><br>    LM35_Init(0);<br><br>    SEVEN_SEG_Init(0);<br><br>    SEVEN_SEG_Enable(0);<br><br>    SEVEN_SEG_Write(0, 0);<br><br>    while (1)<br><br>    {<br><br>        Temperature = (uint16_t)LM35_Read(0);<br><br>       SEVEN_SEG_Write(0, Temperature);<br><br>        HAL_Delay(100);<br><br>    }<br><br>}<br><br>void SysTick_CallBack(void)<br><br>{<br><br>    SysTicks++;<br><br>    if(SysTicks == 5) // Each 5 msec<br><br>    {<br><br>    SEVEN_SEG_Main();<br><br>    SysTicks = 0;<br><br>    }<br><br>}|

**7-Segments Display Main Function**

In the SEVEN_SEGMENTS.c source code file, you’ll find the implementation of this function “**SEVEN_SEG_Main()**“. I highly recommend that you take the time to figure it out. This function is written in this way in order to be called asynchronously from the main application or the OS dispatcher if you have got a task for the 7-segment display. And it handles all the logical operations and stores context variables to resume it’s operation when called again. And yes, it has to be called repeatedly at a steady rate.

Lowering this rate may cause the display units to have ghosting effects and over-calling this function can lead to similar results. So you’ll have to figure out a good periodicity for this routine and I find 5mSec to be good for me.

---

###  **7-Segment Display Interfacing With STM32 – LAB** 

|   |   |
|---|---|
|**LAB Number**|29|
|**LAB Title**|STM32 7-Segment Display Interfacing|

- Set up a new project as usual with system clock @ 72MHz
- Add the ECUAL / SEVEN_SEGMENTS driver files to our project
- configure an instance in SEVEN_SEGMENTS_cfg.c file
- Initialize the seven segments instance and make a counter variable that increments twice per second for example or whatever and write it to the display using the write function

**And now, let’s build this system step-by-step**

**Step1: Open CubeMX & Create New Project**

**Step2: Choose The Target MCU & Double-Click Its Name**

STM32F103C8

**Step3: Go To The Clock Configuration**

**Step4: Set The System Clock To Be 72MHz**

**Step5: Generate The Initialization Code & Open The Project In Your IDE**

**Step6: Add the ECUAL SEVEN_SEGMENTS driver files to your project**

Follow **[This Tutorial which shows you How To Add Any ECUAL Driver To An STM32 Project](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)** step-by-step.

[![Adding ECUAL Driver To STM32 Project](https://deepbluembedded.com/ezoimgfmt/i2.wp.com/deepbluembedded.com/wp-content/uploads/2020/06/Adding-ECUAL-Driver-To-STM32-Project.jpg?resize=254%2C120&ssl=1&ezimgfmt=rs:254x120/rscb6/ng:webp/ngcb6)](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)

**Here is The Application Code For This LAB (main.c)**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36<br><br>37<br><br>38|#include "main.h"<br><br>#include "../ECUAL/SEVEN_SEGMENTS/SEVEN_SEGMENTS.h"<br><br>uint8_t SysTicks = 0;<br><br>void SysTick_CallBack(void);<br><br>void SystemClock_Config(void);<br><br>static void MX_GPIO_Init(void);<br><br>int main(void)<br><br>{<br><br>    uint8_t Counter = 0;<br><br>    HAL_Init();<br><br>    SystemClock_Config();<br><br>    MX_GPIO_Init();<br><br>    SEVEN_SEG_Init(0);<br><br>    SEVEN_SEG_Enable(0);<br><br>    SEVEN_SEG_Write(0, 0);<br><br>    while (1)<br><br>    {<br><br>        // Each 300mSec Increase The Counter Value By 1<br><br>        // & Write It To The 7-Segments Display<br><br>       SEVEN_SEG_Write(0, Counter++);<br><br>        HAL_Delay(300);<br><br>    }<br><br>}<br><br>void SysTick_CallBack(void)<br><br>{<br><br>    SysTicks++;<br><br>    if(SysTicks == 5) // Each 5msec<br><br>    {<br><br>    SEVEN_SEG_Main();<br><br>    SysTicks = 0;<br><br>    }<br><br>}|

Note that the SEVEN_SEG_Main function is being called from the SysTick callback function. This function needs to be called inside the ISR handler for the SysTick timer. The HAL_Init initializes the systick timer and the HAL_Delay uses it to work as well. So, let’s open this file (stm32f1xx_it.c) and search for the SysTick handler and call our SysTick_Callback from there are shown below.

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5|void SysTick_Handler(void)<br><br>{<br><br>    HAL_IncTick();<br><br>    SysTick_CallBack();<br><br>}|

And here is the **SEEVEN_SEGMENTS_cfg.c** configuration file. As you can see here, I’m using the following GPIO pins.

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14|#include "SEVEN_SEGMENTS.h"<br><br>const SEVEN_SEG_CfgType SEVEN_SEG_CfgParam[SEVEN_SEG_UNITS] =<br><br>{<br><br>    // 7-Segments Display Unit 1 Configurations<br><br>    {<br><br>        /* Seven Segments Pins Info */<br><br>    {GPIOA, GPIOA, GPIOA, GPIOA, GPIOA, GPIOA, GPIOA},<br><br>    {GPIO_PIN_0, GPIO_PIN_1, GPIO_PIN_2, GPIO_PIN_3, GPIO_PIN_4, GPIO_PIN_5, GPIO_PIN_6},<br><br>    /* Enable Control Signal's Pins */<br><br>    {GPIOB, GPIOB, GPIOB},<br><br>        {GPIO_PIN_14, GPIO_PIN_13, GPIO_PIN_12}<br><br>    }<br><br>};|

**[Download The STM32 Seven Segments Display LAB29](https://drive.google.com/file/d/1ZznJGuHOV-7R16jGoyS1e8uY0B_o5VFC/view?usp=sharing)**

**The Result For This LAB Testing (Video)**

**Final Remark**

Download the code, take your time in reading and understanding what’s going on. And drop me a comment if you’ve got any questions. I’ll be always here for help!

There is a specific pattern being followed in all ECUAL drivers and I hope you get it from those different examples and tutorials. And I’m pretty sure, you’ll be able to optimize and make it even better and customized for your needs.

---

###    **7-Segments Display + Knob (ADC) – LAB**   

|   |   |
|---|---|
|**LAB Number**|30|
|**LAB Title**|STM32 Seven Segments Display & ADC Knob Analog Input|

- Set up a new project as usual with system clock @ 72MHz
- Set up an ADC analog input channel (CH8 on B0 pin)
- Add the ECUAL / SEVEN_SEGMENTS driver files to our project
- configure an instance in SEVEN_SEGMENTS_cfg.c file
- Initialize the seven segments instance and read the ADC pin and divide the result by 4 and cap it at 999 as we can’t display a larger number than this using only 3 digits

**And now, let’s build this system step-by-step**

**Step1: Open CubeMX & Create New Project**

**Step2: Choose The Target MCU & Double-Click Its Name**

STM32F103C8

**Step3: Go To The Clock Configuration**

**Step4: Set The System Clock To Be 72MHz**

**Step5: In ADC1 Tab Enable CH8 Analog Input (B0 Pin)**

**Step6: Generate The Initialization Code & Open The Project In Your IDE**

**Step7: Add the ECUAL SEVEN_SEGMENTS driver files to your project**

Follow **[This Tutorial which shows you How To Add Any ECUAL Driver To An STM32 Project](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)** step-by-step.

[![Adding ECUAL Driver To STM32 Project](https://deepbluembedded.com/ezoimgfmt/i2.wp.com/deepbluembedded.com/wp-content/uploads/2020/06/Adding-ECUAL-Driver-To-STM32-Project.jpg?resize=254%2C120&ssl=1&ezimgfmt=rs:254x120/rscb6/ng:webp/ngcb6)](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)

**Here is The Application Code For This LAB (main.c)**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36<br><br>37<br><br>38<br><br>39<br><br>40<br><br>41<br><br>42<br><br>43<br><br>44<br><br>45<br><br>46<br><br>47<br><br>48<br><br>49<br><br>50<br><br>51<br><br>52|#include "main.h"<br><br>#include "../ECUAL/SEVEN_SEGMENTS/SEVEN_SEGMENTS.h"<br><br>uint8_t SysTicks = 0;<br><br>ADC_HandleTypeDef hadc1;<br><br>void SysTick_CallBack(void);<br><br>void SystemClock_Config(void);<br><br>static void MX_GPIO_Init(void);<br><br>static void MX_ADC1_Init(void);<br><br>int main(void)<br><br>{<br><br>    uint16_t AD_RES = 0;<br><br>    uint16_t DATA = 0;<br><br>    HAL_Init();<br><br>    SystemClock_Config();<br><br>    MX_GPIO_Init();<br><br>    MX_ADC1_Init();<br><br>    SEVEN_SEG_Init(0);<br><br>    SEVEN_SEG_Enable(0);<br><br>    SEVEN_SEG_Write(0, 0);<br><br>    while (1)<br><br>    {<br><br>        // Start ADC Conversion<br><br>        HAL_ADC_Start(&hadc1);<br><br>        // Poll ADC1 Perihperal & TimeOut = 1mSec<br><br>        HAL_ADC_PollForConversion(&hadc1, 1);<br><br>        // Read The ADC Conversion Result<br><br>        AD_RES = HAL_ADC_GetValue(&hadc1);<br><br>        DATA = AD_RES>>2;<br><br>        if(DATA >= 999)<br><br>        {<br><br>            // Cap The Maximum Value To Be Displayed<br><br>            DATA = 999;<br><br>        }<br><br>       SEVEN_SEG_Write(0, DATA);<br><br>        HAL_Delay(100);<br><br>    }<br><br>}<br><br>void SysTick_CallBack(void)<br><br>{<br><br>    SysTicks++;<br><br>    if(SysTicks == 5) // Each 5msec<br><br>    {<br><br>    SEVEN_SEG_Main();<br><br>    SysTicks = 0;<br><br>    }<br><br>}|

Note that the SEVEN_SEG_Main function is being called from the SysTick callback function. This function needs to be called inside the ISR handler for the SysTick timer. The HAL_Init initializes the systick timer and the HAL_Delay uses it to work as well. So, let’s open this file (stm32f1xx_it.c) and search for the SysTick handler and call our SysTick_Callback from there are shown below.

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5|void SysTick_Handler(void)<br><br>{<br><br>    HAL_IncTick();<br><br>    SysTick_CallBack();<br><br>}|

**[Download The STM32 7-Segments Display With Knob LAB30](https://drive.google.com/file/d/1irGeeigqS9tPbfcSzoLau2Adw1rMBnaT/view?usp=sharing)**

**The Result For This LAB Testing (Video)**

---

###  **STM32 Temperature Sensor + 7-Segment Display** 

|   |   |
|---|---|
|**LAB Number**|31|
|**LAB Title**|STM32 Seven Segments Display With LM35 Temperature Sensor|

- Set up a new project as usual with system clock @ 72MHz
- Add the ECUAL / LM35 driver files to our project
- Add the ECUAL / SEVEN_SEGMENTS driver files to our project
- configure an LM35 sensor line instance in LM35_cfg.c file
- configure an instance in SEVEN_SEGMENTS_cfg.c file
- Initialize the seven segments instance and the LM35 sensor and read the temperature and write it to the display using the APIs. Sample the temperature sensor 10 times per second and update the display.

**And now, let’s build this system step-by-step**

**Step1: Open CubeMX & Create New Project**

**Step2: Choose The Target MCU & Double-Click Its Name**

STM32F103C8

**Step3: Go To The Clock Configuration**

**Step4: Set The System Clock To Be 72MHz**

**Step5: In ADC1 Tab Enable AN8 Channel (B0 pin) For The LM35 Sensor**

**Step6: Generate The Initialization Code & Open The Project In Your IDE**

**Step7: Add the ECUAL LM35 driver files to your project**

Follow **[This Tutorial which shows you How To Add Any ECUAL Driver To An STM32 Project](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)** step-by-step.

[![Adding ECUAL Driver To STM32 Project](https://deepbluembedded.com/ezoimgfmt/i2.wp.com/deepbluembedded.com/wp-content/uploads/2020/06/Adding-ECUAL-Driver-To-STM32-Project.jpg?resize=254%2C120&ssl=1&ezimgfmt=rs:254x120/rscb6/ng:webp/ngcb6)](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)

**Step8: Add the ECUAL SEVEN_SEGMENTS driver files to your project**

**Here is The Application Code For This LAB (main.c)**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36<br><br>37<br><br>38<br><br>39|#include "main.h"<br><br>#include "../ECUAL/SEVEN_SEGMENTS/SEVEN_SEGMENTS.h"<br><br>#include "../ECUAL/LM35/LM35.h"<br><br>uint8_t SysTicks = 0;<br><br>void SysTick_CallBack(void);<br><br>void SystemClock_Config(void);<br><br>static void MX_GPIO_Init(void);<br><br>int main(void)<br><br>{<br><br>    uint16_t Temperature = 0;<br><br>    HAL_Init();<br><br>    SystemClock_Config();<br><br>    MX_GPIO_Init();<br><br>    LM35_Init(0);<br><br>    SEVEN_SEG_Init(0);<br><br>    SEVEN_SEG_Enable(0);<br><br>    SEVEN_SEG_Write(0, 0);<br><br>    while (1)<br><br>    {<br><br>        Temperature = (uint16_t)LM35_Read(0);<br><br>       SEVEN_SEG_Write(0, Temperature);<br><br>        HAL_Delay(100);<br><br>    }<br><br>}<br><br>void SysTick_CallBack(void)<br><br>{<br><br>    SysTicks++;<br><br>    if(SysTicks == 5) // Each 5msec<br><br>    {<br><br>    SEVEN_SEG_Main();<br><br>    SysTicks = 0;<br><br>    }<br><br>}|

**[Download The STM32 Seven Segments + LM35 Temperature Sensor LAB31](https://drive.google.com/file/d/1nSzSQxl-wOi5tUkM8vxQbSx2eGjZV7Hi/view?usp=sharing)**

**The Result For This LAB Testing (Video)**

---

###    **One Last Testing For STM32 7-Segment Driver**   

I’ve also done one last test that I’d like to share with you. It was an approximate measurement for the execution time for the function SEVEN_SEG_Main. Because we’re going to build a very basic operating system in the future and also make some RTOS applications, it’s important to know the execution time of such routines like the 7-segment display logic handler.

I’ve used a GPIO pin configured in fast mode push-pull driver output for fast edges switching on the output pin and driven it high before calling the SEVEN_SEG_Main function and bring it low when it returns. And it turned out to be 19μs which is amazing as a beginning and needs no more optimization in the meantime. We may not use this display in the future unless there is some requests by the readers. Because the LCD in I2C mode can be advantageous and displays more digits and information with just two pins that can be also used for other sensors. That’s it for today! and we’ll move on to the next tutorial.