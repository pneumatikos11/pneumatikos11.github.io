# STM32 Servo Motor Control With PWM – Servo Library Example Code

In this tutorial, we’ll discuss how to control servo motors using STM32 PWM to generate the control signal for servo motors. We’ll also develop all the necessary equations and code that is required to build the ECUAL servo motor driver library. I’ll show you how I built it, the available APIs, and how to use it. And finally, we’ll do a couple of LABs or examples using the ECUAL STM32 servo motor driver. While highlighting all the possible options and configurations in the driver’s config files. Without further ado, let’s get started!

---

###   **Introduction To Servo Motor Control**  

![Servo Motor Tutorial MG90S](https://deepbluembedded.com/wp-content/uploads/2019/11/Servo-Motor-Tutorial-MG90S.jpg?ezimgfmt=rs:271x203/rscb6/ng:webp/ngcb6)

**Servo Motor Control Signal**

To control the position (angle) of the servo motor’s shaft, you’ll have to generate a PWM signal that has a frequency of 50Hz and a variable duty cycle. The datasheet for each stepper motor has to have the exact specifications for the pulse width that corresponds to the minimum angular position (0°) it’s called Minimum_Pulse and the pulse width that corresponds to the maximum angular position (180°) and we’ll call this the Maximum_Pulse.

![How To Control Servo Motor With Microcontroller](https://deepbluembedded.com/wp-content/uploads/2019/11/snap4arduino-servo-funcionamiento-1024x550.png?ezimgfmt=rs:570x306/rscb6/ng:webp/ngcb6)

The servo motor that I’ve in my LAB and will be using today is the micro servo with metal gear MG90s. Unfortunately, “The datasheet” found on the internet seems to be a poorly translated Chinese paper with a little bit of information about the control signal specs and it turned out to be false. Other than that, the practical experimentation didn’t only give me the exact pulse widths that correspond to (0° & 180°) but it also proved that those little motors are not the same. A quick characterization with a DDS function generator and a DSO has given me the exact figures for the min & max pulse widths (in ms).

This picture shows what are the values I’ve got for a different 4 servo motors, and I preferred to label them. In a previous tutorial, I’ve assumed that the minimum pulse is 0.6ms and the maximum pulse is 2.4ms in order to make everything as simple as possible and not trying to be a perfectionist about it. But in today’s tutorial, I’ll make a room for these values to be in our code configurations and to take effect. All in all, it’s not a much of a big deal anyway!

![STM32 Servo Motor Control](https://deepbluembedded.com/wp-content/uploads/2020/07/STM32-Servo-Motor-Control.jpg?ezimgfmt=rs:727x409/rscb6/ng:webp/ngcb6)

**Servo Motor Resolution**

The angular position of the servo motor is determined by the pulse width of the PWM signal. Therefore, the PWM signal’s resolution dictates how fine can we adjust the servo motor’s angle. The range of the angle in degrees is from 0 up to 180 will get mapped to whatever values that produces a pulse width between Minimum_pulse and Maximum_Pulse and all depends on the PWM resolution itself.

Maximizing the resolution will be one of our targets while deriving the equations to be used in code later on. The STM32 PWM hardware can go up to 16-Bit @ 50Hz and we shall seek to get as close as possible to that limit.

**And here is an example for clarification of the concept of resolution.**

Just imagine that we’ve set up the PWM hardware to generate a PWM signal with a frequency of 50Hz and the duty cycle has a 16-Bit of resolution. This means that the duty cycle has 65536 discrete levels between (0% to 100%). That’s a too accurate control signal for such an application and seems more like overkill but let’s continue.

Suppose that our servo motor has a minimum pulse width of 0.6ms (for the 0° position) and a maximum pulse of 2.4ms (for the 180° position). This means that the entire range of PWM signal that covers the full motor rotation is from 3% up to 12%. Why these numbers? well, the minimum pulse is 0.6ms in width as time, in order to get the duty cycle percentage that represents this pulse width time, we’ll divide 0.6 by 20ms which is the period of the PWM signal (which has a frequency of 50Hz, so it’s period is 20ms). This division will give you that the minimum pulse 0.6/20 is a 3% duty cycle. And the maximum pulse is 12% duty cycle be the same procedure.

Now, given that the entire PWM range (0% up to 100%) has a 16-Bit of resolution or 65536 discrete levels. The motor rotation range is (12% – 3% = 9%) and this 9% has (9/100)*65536 discrete levels of control. This means that the servo motor’s angular range (0° up to 180°) is mapped to nearly 5900 discrete levels. This means we can control the servo motor’s angle with a precision of 0.03°. Which is not true! This is obviously ridiculous due to mechanical limitations and the way this motor is built. However, math is always true but this can’t be practically achieved at least with those cheap little motors.

![STM32 Servo Motor Control And Angle Resolution Control](https://deepbluembedded.com/wp-content/uploads/2020/07/STM32-Servo-Motor-Control-And-Angle-Resolution-Control.jpg?ezimgfmt=rs:612x222/rscb6/ng:webp/ngcb6)

**Power Requirements & Precautions**

First of all, if you’re using a decent servo motor that has well-documented specs, do check them out and see the current draw and its power consumption. Mostly no servo motor can work directly from the USB programmer circuit as it is. Except for a few tiny servo motors that may be an exception but in most cases, you’ll be in need to connect your servo motors to an external power source.

The same power source used by the microcontroller can be used of course but please make sure you’re decoupling the power supply correctly and bulk bypass capacitors are placed near the power supply and small caps near the MCU power input pins.

There are servo motors that can rotate 360 degrees freely and there are other types like mine that are limited to 180 degrees. There is a mechanical buffer that blocks motion at this angle. Reaching this angle can cause the servo motor to release some wining sound and draw a significant amount of current compared to its normal conditions. This situation needs to be considered as well and if it can’t be well-handled, then it should be avoided in the first place. Maybe by capping the maximum pulse width (if it’s 2.4ms, just use 2.3 or something like that).

---

###    **STM32 ECUAL / Servo Motor Driver**   

The ECUAL Servo motor driver is built for STM32 microcontrollers using the hardware PWM channels in various timers. You’ll have to configure an instance of it and used the APIs to control your motor and that’s all. The code should be easily ported to any other STM32 microcontroller or reconfigured to use any timer or channel you want just as we’ll see in this section. And here is a link for the [course’s repo](https://github.com/Khaled-Magdy-DeepBlue/STM32_Course_DeepBlue), and you’ll find the Servo driver in the ECUAL directory as usual.

**Servo Motor Driver Code Files**

The servo motor driver consists of the following files:

|   |   |
|---|---|
|- SERVO.h<br>- SERVO.c<br>- SERVO_cfg.h<br>- SERVO_cfg.c|![STM32 Servo Motor Library Code Examples](https://deepbluembedded.com/wp-content/uploads/2020/07/STM32-Servo-Motor-Library-Code-Examples.jpg?ezimgfmt=rs:142x95/rscb6/ng:webp/ngcb6)|

You’ll need only to modify the configuration files. The source code for this driver is found in (SERVO.c) and to use it you’ll include the header (SERVO.h). Nothing in the source code needs to be changed at all unless you need to add any extra features or customize the driver for your application’s needs. For today’s labs, we’ll only be changing the configuration files to build some test applications.

Therefore, I’ll write here the code listing for the SERVO.h & SERVO_cfg.c files.

**SERVO.h File**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28|// The Number OF Servo Motors To Be Used In The Project<br><br>#define SERVO_NUM  1<br><br>typedef struct<br><br>{<br><br>    GPIO_TypeDef * SERVO_GPIO;<br><br>    uint16_t       SERVO_PIN;<br><br>    TIM_TypeDef*   TIM_Instance;<br><br>    uint32_t*      TIM_CCRx;<br><br>    uint32_t       PWM_TIM_CH;<br><br>    uint32_t       TIM_CLK;<br><br>    float          MinPulse;<br><br>    float          MaxPulse;<br><br>}SERVO_CfgType;<br><br>/*-----[ Prototypes For All Functions ]-----*/<br><br>void SERVO_Init(uint16_t au16_SERVO_Instance);<br><br>void SERVO_MoveTo(uint16_t au16_SERVO_Instance, float af_Angle);<br><br>void SERVO_RawMove(uint16_t au16_SERVO_Instance, uint16_t au16_Pulse);<br><br>uint16_t SERVO_Get_MaxPulse(uint16_t au16_SERVO_Instance);<br><br>uint16_t SERVO_Get_MinPulse(uint16_t au16_SERVO_Instance);<br><br>void SERVO_Sweep(uint16_t au16_SERVO_Instance);|

If you’re willing to use many servo motors, just adjust that number definition (SERVO_NUM). And we’ll do this in the LABs of today’s tutorial.

**SERVO_cfg.c File**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16|#include "SERVO.h"<br><br>const SERVO_CfgType SERVO_CfgParam[SERVO_NUM] =<br><br>{<br><br>    // Servo Motor 1 Configurations<br><br>    {<br><br>    GPIOA,<br><br>    GPIO_PIN_0,<br><br>    TIM2,<br><br>    &TIM2->CCR1,<br><br>    TIM_CHANNEL_1,<br><br>    72000000,<br><br>    0.65,<br><br>    2.3<br><br>    }<br><br>}|

I’ll discuss those configuration parameters in the next section down below.

**Servo Motor Driver APIs**

As you’ve seen in the SERVO.h file, the provided APIs does all the basic functionalities that you may need from a servo driver library. It initialized the PWM hardware & GPIO pin required to generate the 50Hz control signal.

The servo_moveto function takes the number of the servo motor you wanna command and an angle to move its shaft to that angle (from 0 up to 180).

The structure of the files and the provided APIs look very intuitive and self-explanatory to me. And the example LABs will put everything under test and you’ll see how and when to use each of these functions.

**Available Configurations For ECUAL Servo Driver**

From the code above in SERVO.h & SERVO_cfg.c you can see that there are a handful of parameters that you (the application programmer) can actually configure for each servo motor individually. They are as follows:

- The timer module you wanna use for this servo motor PWM generation (TIM1, 2, or whatever)
- The timer PWM Channel number that you wanna use for this servo motor (CH1, 2, 3, ..)
- The GPIO port for the PWM CHx pin
- The PIN number for the PWM CHx pin
- The clock frequency (in Hz) for the timer you’re going to use
- A pointer to the Timer CCRx register for the chosen PWM Channel
- The minimum pulse width for the servo motor as in its datasheet (0.6ms, 0.5ms, or whatever)
- The maximum pulse width for the servo motor (2.4ms, 2.5ms, or whatever value)

**Typical Usage Application Example**

Here is a typical usage application for this driver in order to initialize a servo motor and move it from 0 to 180 back and forth. It’ll be something like this shown below.

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16|#include "ECUAL/SERVO/SERVO.h"<br><br>#define SERVO_Motor1    0<br><br>void main()<br><br>{<br><br>    SERVO_Init(SERVO_Motor1);<br><br>    while(1)<br><br>    {<br><br>        SERVO_MoveTo(SERVO_Motor1, 0);<br><br>        Delay_ms(2000);<br><br>        SERVO_MoveTo(SERVO_Motor1, 180);<br><br>        Delay_ms(2000);<br><br>    }<br><br>}|

---

###    **STM32 PWM For Servo Motor Control**   

In this section, I’ll show you the derivation for most of the equations used in the driver’s code that may seem ambiguous and highlight the goals and intentions I had while writing it in this way.

**Prescaler Value**

And let’s start with the timer Prescaler (PSC) and Auto-Reload Register (ARR). We need to calculate those parameters and write the number in the respective register. In order to control the PWM’s frequency and make it equal to 50Hz which is required by the servo motor, we’ll use the following equation.

![STM32 PWM Frequency Formula - STM32 PWM Frequency Equation](https://deepbluembedded.com/wp-content/uploads/2020/06/STM32-PWM-Frequency-Formula-STM32-PWM-Frequency-Equation.png?ezimgfmt=rs:247x43/rscb6/ng:webp/ngcb6)

Recall the discussion of the PWM resolution at the beginning of this tutorial. I said that it’s one of my goals to maximize the PWM resolution, hence in this equation, I’ve assumed the ARR value to be 65535 which is the maximum possible value that achieves the maximum possible resolution. Then plug in the timer clock frequency FCLK, the 50Hz for FPWM and solve for PSC. This calculation is done in code in the initialization function.

PSC = FCLK / 3276800

|   |   |
|---|---|
|1|PSC_Value = (uint32_t)(SERVO_CfgParam[au16_SERVO_Instance].TIM_CLK/3276800.0);|

And pay attention to the fact that it’ll maximize the ARR but won’t make it 65535 exactly! it’ll just pick for us the best Prescaler value that maximizes the PWM range (resolution). Therefore, we still need to find out the value of ARR that should be written in its respective register.

**ARR Register Value**

To find out the value of ARR you’ll use also the first equation

![STM32 PWM Frequency Formula - STM32 PWM Frequency Equation](https://deepbluembedded.com/wp-content/uploads/2020/06/STM32-PWM-Frequency-Formula-STM32-PWM-Frequency-Equation.png?ezimgfmt=rs:247x43/rscb6/ng:webp/ngcb6)

Given that FCLK is known as well as FPWM, even PSC is now known. After we’ve found the PSC that maximizes PWM resolution, now plug all variables in the equation and solve for ARR.

|   |   |
|---|---|
|1|ARR_Value = (uint32_t) ((SERVO_CfgParam[au16_SERVO_Instance].TIM_CLK / (50.0*(PSC_Value+1.0)))-1.0);|

**Pulse Width Min & Max Period**

Last but not least, the servo motor’s minimum & maximum pulse width specs given in the datasheet. Should also be converted into digital values that represent the number of ticks in the CCRx register of the respective PWM channel to achieve those pulse widths and hence control the servo motor’s angle.

Calculating both numbers is also critical for determining the full range to which we’ll be mapping the angle given by the user. When a user call the moveTo function and gives me 90 degrees. What should be the value of the CCRx register to achieve this?

Well, it should be in the middle of the range, as 90degree is in middle between (0 and 180). But we still don’t know the number of ticks that represent it. Hence, it’s so important now to find those values.

Depending on the fact that the PWM signal’s frequency is 50Hz, so it’s period is 20ms. And the min_pulse is given to us in ms as well by the user. Let’s say it’s 0.6ms, so the PWM duty cycle is 0.6/20 = 3%. To achieve 3% duty cycle, the value of the CCRx register will be calculated from this equation.

![STM32 PWM Duty Cycle Formula - STM32 PWM Duty Cycle Equation](https://deepbluembedded.com/wp-content/uploads/2020/06/STM32-PWM-Duty-Cycle-Formula-STM32-PWM-Duty-Cycle-Equation.png?ezimgfmt=rs:240x39/rscb6/ng:webp/ngcb6)

Period_Min = ARR x (min_pulse_width / 20ms)

Period_Max = ARR x (max_pulse_width / 20ms)

|   |   |
|---|---|
|1<br><br>2|gs_SERVO_info[au16_SERVO_Instance].Period_Min = (uint16_t) (ARR_Value * (SERVO_CfgParam[au16_SERVO_Instance].MinPulse/20.0));<br><br>gs_SERVO_info[au16_SERVO_Instance].Period_Max = (uint16_t) (ARR_Value * (SERVO_CfgParam[au16_SERVO_Instance].MaxPulse/20.0));|

And that’s it for most of the parts that I think would be problematic and ambiguous for the readers. If there still something not clear enough, drop me a comment I’ll be here for help as usual.

---

###    **STM32 PWM Servo Motor Control – Basic LAB**   

|   |   |
|---|---|
|**LAB Number**|26|
|**LAB Title**|STM32 Servo Motor Control – Basic LAB|

- Set up a new project as usual with system clock @ 72MHz
- Add the ECUAL / SERVO driver files to our project
- configure a servo motor instance in SERVO_cfg.c file
- Initialize the servo motor and command it to move to the following angle positions in the same pattern (0, 45, 90, 135, 180, 90, 0)

**And now, let’s build this system step-by-step**

**Step1: Open CubeMX & Create New Project**

**Step2: Choose The Target MCU & Double-Click Its Name**

STM32F103C8

**Step3: Go To The Clock Configuration**

**Step4: Set The System Clock To Be 72MHz**

**Step5: Generate The Initialization Code & Open The Project In Your IDE**

**Step6: Add the ECUAL SERVO driver files to your project**

Follow **[This Tutorial which shows you How To Add Any ECUAL Driver To An STM32 Project](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)** step-by-step.

[![Adding ECUAL Driver To STM32 Project](https://deepbluembedded.com/ezoimgfmt/i2.wp.com/deepbluembedded.com/wp-content/uploads/2020/06/Adding-ECUAL-Driver-To-STM32-Project.jpg?resize=254%2C120&ssl=1&ezimgfmt=rs:254x120/rscb6/ng:webp/ngcb6)](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)

**Step7: One Last step is to open stm32f1xx_hal_conf.h file in the include directory and uncomment the following line of code**

|   |   |
|---|---|
|1|#define HAL_TIM_MODULE_ENABLED|

To allow the HAL timer drivers to be included and compiled as our servo driver is built on top of the HAL driver from STMicroelectronics. Now, we’re ready to write a test application code and compile it.

**Here is The Application Code For This LAB (main.c)**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31|#include "main.h"<br><br>#include "../ECUAL/SERVO/SERVO.h"<br><br>// The Servo Instance Index Must Start From 0<br><br>#define SERVO_Motor1   0<br><br>void SystemClock_Config(void);<br><br>static void MX_GPIO_Init(void);<br><br>int main(void)<br><br>{<br><br>    HAL_Init();<br><br>    SystemClock_Config();<br><br>    MX_GPIO_Init();<br><br>    SERVO_Init(SERVO_Motor1);<br><br>    while (1)<br><br>    {<br><br>        SERVO_MoveTo(SERVO_Motor1, 0);<br><br>        HAL_Delay(1000);<br><br>        SERVO_MoveTo(SERVO_Motor1, 45);<br><br>        HAL_Delay(500);<br><br>        SERVO_MoveTo(SERVO_Motor1, 90);<br><br>        HAL_Delay(500);<br><br>        SERVO_MoveTo(SERVO_Motor1, 135);<br><br>        HAL_Delay(500);<br><br>        SERVO_MoveTo(SERVO_Motor1, 180);<br><br>        HAL_Delay(500);<br><br>        SERVO_MoveTo(SERVO_Motor1, 90);<br><br>        HAL_Delay(1000);<br><br>    }<br><br>}|

And here is the **SERVO_cfg.c** configuration file. As you can see here, I’m using Timer2 channel1 with a frequency of 72Mz, the servo’s min pulse is 0.65ms, the max is 2.3ms, the GPIO port is A, the pin is A0.

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16|#include "SERVO.h"<br><br>const SERVO_CfgType SERVO_CfgParam[SERVO_NUM] =<br><br>{<br><br>    // Servo Motor 1 Configurations<br><br>    {<br><br>    GPIOA,<br><br>    GPIO_PIN_0,<br><br>    TIM2,<br><br>    &TIM2->CCR1,<br><br>    TIM_CHANNEL_1,<br><br>    72000000,<br><br>    0.65,<br><br>    2.3<br><br>    }<br><br>};|

**[Download The STM32 Servo Motor Control Basic LAB26](https://drive.google.com/file/d/18HIBhFr1RPgEDCsy5sqzDu41P6wwp6tu/view?usp=sharing)**

**The Result For This LAB Testing (Video)**

**Here is a snapshot for the Servo PWM Signal (a clean 50Hz)**

**![Servo Motor Control PWM Signal](https://deepbluembedded.com/wp-content/uploads/2020/07/STM32-Servo-Motor-Control-PWM-Signal.png?ezimgfmt=rs:727x436/rscb6/ng:webp/ngcb6)**

**Extremely Important Notes From This LAB**

First of all, the GPIO pin selection is not an arbitrary process. You just can’t configure the driver to use Timer2 channel 1 and configure the pin to whatever you want. Once you’ve made your decision on which timer and which channel you want to assign to your servo motor, you’ll have to check its pin GPIO port and number and use that in your configuration.

Another note is the servo motor index (instance number) you’re using while calling the APIs. For an application where is only 1 servo motor the SERVO_NUM define is 1 and the index you shall be using is 0. Therefore, you’ll init this servo like this

SERVO_Init(0);

However, if more than a servo motor is being used, the indexing will start also from 0 and counts up. Therefore, in order to initialize multiple motors, you’ll do this.

SERVO_Init(0);

SERVO_Init(1);

And so on.

Another note is the power source. As you’ve seen in the video I’m using a power bank and the Servo motor is connected to the +5v power pin on the Blue Pill board.

---

###    **STM32 Analog Servo Motor Control + Knob LAB**   

|   |   |
|---|---|
|**LAB Number**|27|
|**LAB Title**|STM32 Servo Motor Control With Knob (ADC Input) LAB|

- Set up a new project as usual with system clock @ 72MHz
- Set up an ADC analog input channel (CH7 on A7 pin)
- Add the ECUAL / SERVO driver files to our project
- configure a servo motor instance in SERVO_cfg.c file
- Initialize the servo motor and read the ADC 12-Bit result and map it between the maximum & minimum pulse widths for the servo motor to control its angular position

**And now, let’s build this system step-by-step**

**Step1: Open CubeMX & Create New Project**

**Step2: Choose The Target MCU & Double-Click Its Name**

STM32F103C8

**Step3: Go To The Clock Configuration**

**Step4: Set The System Clock To Be 72MHz**

**Step5: In ADC1 Tab Enable CH7 Analog Input**

**Step6: Generate The Initialization Code & Open The Project In Your IDE**

**Step7: Add the ECUAL SERVO driver files to your project**

Follow **[This Tutorial which shows you How To Add Any ECUAL Driver To An STM32 Project](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)** step-by-step.

[![Adding ECUAL Driver To STM32 Project](https://deepbluembedded.com/ezoimgfmt/i2.wp.com/deepbluembedded.com/wp-content/uploads/2020/06/Adding-ECUAL-Driver-To-STM32-Project.jpg?resize=254%2C120&ssl=1&ezimgfmt=rs:254x120/rscb6/ng:webp/ngcb6)](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)

**Step8: One Last step is to open stm32f1xx_hal_conf.h file in the include directory and uncomment the following line of code**

|   |   |
|---|---|
|1|#define HAL_TIM_MODULE_ENABLED|

To allow the HAL timer drivers to be included and compiled as our servo driver is built on top of the HAL driver from STMicroelectronics. Now, we’re ready to write a test application code and compile it.

**Here is The Application Code For This LAB (main.c)**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36<br><br>37<br><br>38<br><br>39<br><br>40<br><br>41<br><br>42<br><br>43|#include "main.h"<br><br>#include "../ECUAL/SERVO/SERVO.h"<br><br>#define SERVO_Motor1  0<br><br>ADC_HandleTypeDef hadc1;<br><br>void SystemClock_Config(void);<br><br>static void MX_GPIO_Init(void);<br><br>static void MX_ADC1_Init(void);<br><br>int main(void)<br><br>{<br><br>    uint16_t AD_RES = 0;<br><br>    uint16_t Min_Pulse = 0, Max_Pulse = 0;<br><br>    float temp = 0.0;<br><br>    uint16_t Servo_Pulse = 0;<br><br>    HAL_Init();<br><br>    SystemClock_Config();<br><br>    MX_GPIO_Init();<br><br>    MX_ADC1_Init();<br><br>    SERVO_Init(SERVO_Motor1);<br><br>    Min_Pulse = SERVO_Get_MinPulse(SERVO_Motor1);<br><br>    Max_Pulse = SERVO_Get_MaxPulse(SERVO_Motor1);<br><br>    while (1)<br><br>    {<br><br>        // Start ADC Conversion<br><br>        HAL_ADC_Start(&hadc1);<br><br>        // Poll ADC1 Perihperal & TimeOut = 1mSec<br><br>        HAL_ADC_PollForConversion(&hadc1, 1);<br><br>        // Read The ADC Conversion Result & Map It To PWM DutyCycle<br><br>        AD_RES = HAL_ADC_GetValue(&hadc1);<br><br>        // Map The ADC Result To Servo Pulse Width<br><br>        temp = ((Max_Pulse-Min_Pulse)/4096.0);<br><br>        Servo_Pulse = (uint16_t)(AD_RES*temp) + Min_Pulse;<br><br>        // Send The Raw Servo Pulse Width To The PWM Hardware<br><br>        SERVO_RawMove(SERVO_Motor1, Servo_Pulse);<br><br>        HAL_Delay(10);<br><br>    }<br><br>}|

And here is the **SERVO_cfg.c** configuration file. As you can see here, I’m using Timer2 channel1 with a frequency of 72Mz, the servo’s min pulse is 0.65ms, the max is 2.3ms, the GPIO port is A, the pin is A0.

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16|#include "SERVO.h"<br><br>const SERVO_CfgType SERVO_CfgParam[SERVO_NUM] =<br><br>{<br><br>    // Servo Motor 1 Configurations<br><br>    {<br><br>    GPIOA,<br><br>    GPIO_PIN_0,<br><br>    TIM2,<br><br>    &TIM2->CCR1,<br><br>    TIM_CHANNEL_1,<br><br>    72000000,<br><br>    0.65,<br><br>    2.3<br><br>    }<br><br>};|

**[Download The STM32 Servo Motor Control With Knob LAB27](https://drive.google.com/file/d/1PHIqVEHNUKMiZdzLcp84zbvK05uOhfZy/view?usp=sharing)**

**The Result For This LAB Testing (Video)**

---

###    **STM32 Multi Servo Motors Control – LAB**   

|   |   |
|---|---|
|**LAB Number**|28|
|**LAB Title**|STM32 Multi Servo Motors Control LAB|

- Set up a new project as usual with system clock @ 72MHz
- Add the ECUAL / SERVO driver files to our project
- configure 4 servo motor instances in SERVO_cfg.c file
- Initialize the servo motors and command them individually to make whatever pattern you want

**And now, let’s build this system step-by-step**

**Step1: Open CubeMX & Create New Project**

**Step2: Choose The Target MCU & Double-Click Its Name**

STM32F103C8

**Step3: Go To The Clock Configuration**

**Step4: Set The System Clock To Be 72MHz**

**Step5: Generate The Initialization Code & Open The Project In Your IDE**

**Step6: Add the ECUAL SERVO driver files to your project**

Follow **[This Tutorial which shows you How To Add Any ECUAL Driver To An STM32 Project](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)** step-by-step.

[![Adding ECUAL Driver To STM32 Project](https://deepbluembedded.com/ezoimgfmt/i2.wp.com/deepbluembedded.com/wp-content/uploads/2020/06/Adding-ECUAL-Driver-To-STM32-Project.jpg?resize=254%2C120&ssl=1&ezimgfmt=rs:254x120/rscb6/ng:webp/ngcb6)](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)

**Step7: One Last step is to open stm32f1xx_hal_conf.h file in the include directory and uncomment the following line of code**

|   |   |
|---|---|
|1|#define HAL_TIM_MODULE_ENABLED|

To allow the HAL timer drivers to be included and compiled as our servo driver is built on top of the HAL driver from STMicroelectronics. Now, we’re ready to write a test application code and compile it.

**Here is The Application Code For This LAB (main.c)**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36<br><br>37<br><br>38<br><br>39<br><br>40<br><br>41<br><br>42<br><br>43<br><br>44<br><br>45<br><br>46<br><br>47<br><br>48<br><br>49<br><br>50<br><br>51<br><br>52|#include "main.h"<br><br>#include "../ECUAL/SERVO/SERVO.h"<br><br>#define SERVO_Motor1  0<br><br>#define SERVO_Motor2  1<br><br>#define SERVO_Motor3  2<br><br>#define SERVO_Motor4  3<br><br>void SystemClock_Config(void);<br><br>static void MX_GPIO_Init(void);<br><br>int main(void)<br><br>{<br><br>    HAL_Init();<br><br>    SystemClock_Config();<br><br>    MX_GPIO_Init();<br><br>    SERVO_Init(SERVO_Motor1);<br><br>    SERVO_Init(SERVO_Motor2);<br><br>    SERVO_Init(SERVO_Motor3);<br><br>    SERVO_Init(SERVO_Motor4);<br><br>    while (1)<br><br>    {<br><br>        SERVO_MoveTo(SERVO_Motor1, 0);<br><br>        SERVO_MoveTo(SERVO_Motor2, 0);<br><br>        SERVO_MoveTo(SERVO_Motor3, 0);<br><br>        SERVO_MoveTo(SERVO_Motor4, 0);<br><br>        HAL_Delay(1500);<br><br>        SERVO_MoveTo(SERVO_Motor1, 180);<br><br>        HAL_Delay(500);<br><br>        SERVO_MoveTo(SERVO_Motor2, 180);<br><br>        HAL_Delay(500);<br><br>        SERVO_MoveTo(SERVO_Motor3, 180);<br><br>        HAL_Delay(500);<br><br>        SERVO_MoveTo(SERVO_Motor4, 180);<br><br>        HAL_Delay(1500);<br><br>        SERVO_MoveTo(SERVO_Motor1, 0);<br><br>        SERVO_MoveTo(SERVO_Motor2, 0);<br><br>        SERVO_MoveTo(SERVO_Motor3, 0);<br><br>        SERVO_MoveTo(SERVO_Motor4, 0);<br><br>        HAL_Delay(1500);<br><br>        SERVO_MoveTo(SERVO_Motor1, 180);<br><br>        SERVO_MoveTo(SERVO_Motor2, 180);<br><br>           SERVO_MoveTo(SERVO_Motor3, 180);<br><br>        SERVO_MoveTo(SERVO_Motor4, 180);<br><br>        HAL_Delay(1500);<br><br>    }<br><br>}|

And here is the **SERVO_cfg.c** configuration file.

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36<br><br>37<br><br>38<br><br>39<br><br>40<br><br>41<br><br>42<br><br>43<br><br>44<br><br>45<br><br>46<br><br>47<br><br>48<br><br>49|#include "SERVO.h"<br><br>const SERVO_CfgType SERVO_CfgParam[SERVO_NUM] =<br><br>{<br><br>    // Servo Motor 1 Configurations<br><br>    {<br><br>    GPIOA,<br><br>    GPIO_PIN_0,<br><br>    TIM2,<br><br>    &TIM2->CCR1,<br><br>    TIM_CHANNEL_1,<br><br>    72000000,<br><br>    0.65,<br><br>    2.3<br><br>    },<br><br>    // Servo Motor 2 Configurations<br><br>    {<br><br>    GPIOA,<br><br>    GPIO_PIN_3,<br><br>    TIM2,<br><br>    &TIM2->CCR4,<br><br>    TIM_CHANNEL_4,<br><br>    72000000,<br><br>    0.75,<br><br>    2.5<br><br>    },<br><br>    // Servo Motor 3 Configurations<br><br>    {<br><br>    GPIOB,<br><br>    GPIO_PIN_0,<br><br>    TIM3,<br><br>    &TIM3->CCR3,<br><br>    TIM_CHANNEL_3,<br><br>    72000000,<br><br>    0.62,<br><br>    2.4<br><br>    },<br><br>    // Servo Motor 4 Configurations<br><br>    {<br><br>        GPIOB,<br><br>    GPIO_PIN_9,<br><br>    TIM4,<br><br>    &TIM4->CCR4,<br><br>    TIM_CHANNEL_4,<br><br>    72000000,<br><br>    0.55,<br><br>    2.35<br><br>    }<br><br>};|

As you can see here:

I’m mixing between different timers and PWM channels in order to give you an idea of how can you easily configure this driver code. But I’d recommend using one-timer and its 4 channels if it’s possible in your application. Using a timer for servo motor control will restrict the timing in such a way that’s convenient for the servo driver but not the other parts of the system that wanna share the same resource.

For Servo Motor1: TIM2, Ch1, pin A0

For Servo Motor2: TIM2, Ch4, pin A3

For Servo Motor3: TIM3, Ch3, pin B0

For Servo Motor4: TIM4, Ch4, pin B9

Don’t forget to change this line of code in the SERVO.h file to make the number of servo motors 4

|   |   |
|---|---|
|1<br><br>2|// The Number OF Servo Motors To Be Used In The Project<br><br>#define SERVO_NUM  4|

**[Download The STM32 Multi Servo Motors Control LAB28](https://drive.google.com/file/d/1tOvu1Tcwa3dZVX991maWK3UqPnybvhag/view?usp=sharing)**

**The Result For This LAB Testing (Video)**

---

###    **One Last Testing For STM32 Servo Driver**   

I’ve also done one last test that I’d like to share with you. It was an approximate measurement for the execution time for the function SERVO_MoveTo(). Because we’re going to build a very basic operating system in the future and also make some RTOS applications, it’s important to know the execution time of such routines like the servo motor controller.

I’ve used a GPIO pin configured in fast mode push-pull driver output for fast edges switching on the output pin and driven it high before calling the servo move function and bring it low when it returns. And it turned out to be 7μs which is amazing as a beginning and needs no more optimization in the meantime. So, that’s great! and we’ll move on to the next tutorial.

![Servo Motor Library Code Example Tutorial](https://deepbluembedded.com/wp-content/uploads/2020/07/STM32-Servo-Motor-Library-Code-Example-Tutorial.png?ezimgfmt=rs:727x436/rscb6/ng:webp/ngcb6)

---

**Did you find this helpful? If yes, please consider [supporting this work](https://www.patreon.com/deep_blue) and sharing these tutorials**