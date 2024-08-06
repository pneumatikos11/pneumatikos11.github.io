# STM32 DC Motor Speed Control PWM With L293D – Motor Driver Library Examples
In this tutorial, we’ll be discussing DC motor speed control with STM32 PWM and L293D H-bridge motor driver. I’ll also show you the DC_MOTOR library (driver) that I’ve developed for STM32 microcontrollers and discuss how it works and how it’s been built in this way. And we’ll create 3 different example projects with STM32 uC and DC motors.

We’ll conclude this tutorial with some tests, measurements, and spot the light on potential improvements and features that you can make and add to this driver library code. But first of all, we’ll discuss how the DC Motor & L293D H-Bridge Driver work and how to control the DC motor’s speed and direction of rotation.


|**LAB 45**|1 DC Motor Speed Control With Tip122 Transistor – Testing Our Motor Library|   |   |   |   |   |   |
|**LAB 46**|1 DC Motor Control With L293D – Testing Our Motor Library|   |   |   |   |   |   |
|**LAB 47**|2 DC Motors Control With L293D – Testing Our Motor Library|   |   |   |   |   |   |

---

###   **DC Motor Control & L293D H-Bridge Driver**  

At the beginning of this tutorial, I’d like to make sure that all of you have the basic concepts behind DC motors operation and how we use PWM signals to control the speed of a DC motor. How an H-Bridge works and does the polarity-inversion to control the direction of the motor’s rotation. All of that and more are discussed in [this previous tutorial about DC motor control with L293D driver](https://deepbluembedded.com/dc-motor-speed-control-l293d-motor-driver/). Check it out and come back to resume developing our DC motor driver library for STM32 microcontrollers.

[![DC Motor Speed Control - PWM With L293D IC](https://deepbluembedded.com/wp-content/uploads/2018/09/DC-Motor-Speed-Control-PWM-With-L293D-IC-300x142.jpg?ezimgfmt=rs:300x142/rscb6/ng:webp/ngcb6)](https://deepbluembedded.com/dc-motor-speed-control-l293d-motor-driver/)

This [file is the datasheet](https://www.ti.com/lit/ds/symlink/l293.pdf) for the H-Bridge IC we’ll be using in this tutorial (L293D). Which is capable of delivering 0.6A of current per CH for 2 independent channels (2 motors). The motor supply voltage VM can be in the range from 4.5v up to 36v. You’ll also find the most important piece of information which is the pinout diagram and also a PCB layout example if you’re designing your own board, it’s going to be a good reference.

![STM32 DC Motor Speed Control L293D H-Bridge Pinout](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-DC-Motor-Speed-Control-L293D-H-Bridge-Pinout.png?ezimgfmt=rs:727x262/rscb6/ng:webp/ngcb6)

Near the end of the datasheet document, you’ll find some application examples. The most important one for us is the bidirectional motor control using one channel of the L293D IC. Here is the given figure for that configuration. And note that the fly-back diodes are not integrated within the IC. Therefore, we may include them in the wiring diagram later on. If not placed, you can still have everything running just as fine. As long as, your motor’s load is moderate. As it goes up, the IC will start heating up.

![STM32 With L293D DC Motor Direction Control](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-With-L293D-DC-Motor-Direction-Control.png?ezimgfmt=rs:707x342/rscb6/ng:webp/ngcb6)

---

###  **STM32 With L293D Motor Driver IC** 

#### **Connection Diagram**

![STM32 DC Motor Speed Control L293D H-Bridge Connection Diagram](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-DC-Motor-Speed-Control-L293D-H-Bridge-Connection-Diagram.png?ezimgfmt=rs:727x349/rscb6/ng:webp/ngcb6)

#### **Guidelines For Designing Our DC Motor Driver (Library)**

Here are some guidelines and requirements that I did consider before designing the DC_MOTOR driver library with the current APIs. It can be improved in the future but for the beginning, it should be in line with the following:

- Has Configurable TIMERx and CHx for eachDC_MOTOR instance
- Multiple DC_MOTOR Instances Should Be Running Smoothly On the Same Timer With No Intervention (The PWM Frequency & Resolution Shall Be Shared Among Multiple Motors On The Same Timer).
- The User has full control over the PWM Resolution for motor speed control
- The PWM Frequency shall be an approximated value to what the user selects

---

###    **STM32 Dc Motor Driver (Library)**   

The ECUAL DC_MOTOR driver is built for STM32 microcontrollers using one Timer channel in PWM mode. You’ll have to configure an instance of it and use the APIs to initialize, start, change speed, stop your motor, and that’s all. The code should be easily ported to any other STM32 microcontroller or reconfigured to use any Timer and PWM Channel that you want just as we’ll see in this section. And here is a link for the [course’s repo](https://github.com/Khaled-Magdy-DeepBlue/STM32_Course_DeepBlue), and you’ll find the DC_MOTOR driver in the ECUAL directory as usual.

**DC MOTOR Driver Code Files**

The DC_MOTOR driver consists of the following files:

|   |   |
|---|---|
|- DC_MOTOR.h<br>- DC_MOTOR.c<br>- DC_MOTOR_cfg.h<br>- DC_MOTOR_cfg.c|![STM32 DC Motor Control PWM L293D Driver Library Files](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-DC-Motor-Control-PWM-L293D-Driver-Library-Files.png?ezimgfmt=rs:221x94/rscb6/ng:webp/ngcb6)|

You’ll need only to modify the configuration files. The source code for this driver is found in (DC_MOTOR.c) and to use it you’ll include the header (DC_MOTOR.h). Nothing in the source code needs to be changed at all unless you need to add any extra features or customize the driver for your application’s needs. For today’s labs, we’ll only be changing the configuration files to build some test applications.

Therefore, I’ll write here the code listing for the DC_MOTOR.h & DC_MOTOR_cfg.c files.

**DC_MOTOR.h File**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36|#define HAL_TIM_MODULE_ENABLED<br><br>#include "stm32f1xx_hal.h"<br><br>// DC Motor Rotation Directions<br><br>#define DIR_CW    0<br><br>#define DIR_CCW   1<br><br>// DC Motor PWM Properties<br><br>#define DC_MOTOR_PWM_RES  10<br><br>#define DC_MOTOR_F_PWM    500<br><br>// The Number OF DC MOTORs To Be Used In The Project<br><br>#define DC_MOTOR_UNITS  1<br><br>typedef struct<br><br>{<br><br>    GPIO_TypeDef * DIR1_GPIO;<br><br>    GPIO_TypeDef * DIR2_GPIO;<br><br>    uint16_t       DIR1_PIN;<br><br>    uint16_t       DIR2_PIN;<br><br>    TIM_TypeDef*   TIM_Instance;<br><br>    uint32_t       PWM_TIM_CH;<br><br>    uint16_t       TIM_CLK_MHz;<br><br>    uint32_t       PWM_FREQ_Hz;<br><br>    uint8_t        PWM_RES_BITS;<br><br>}DC_MOTOR_CfgType;<br><br>/*-----[ Prototypes For All Functions ]-----*/<br><br>void DC_MOTOR_Init(uint8_t au8_MOTOR_Instance);<br><br>void DC_MOTOR_Start(uint8_t au8_MOTOR_Instance, uint8_t au8_DIR, uint16_t au16_SPEED);<br><br>void DC_MOTOR_Set_Speed(uint8_t au8_MOTOR_Instance, uint16_t au16_SPEED);<br><br>void DC_MOTOR_Set_Dir(uint8_t au8_MOTOR_Instance, uint8_t au8_DIR);<br><br>void DC_MOTOR_Stop(uint8_t au8_MOTOR_Instance);<br><br>uint32_t DC_MOTOR_Get_MaxFreq(uint8_t au8_MOTOR_Instance);|

If you’re willing to use multiple DC Motors, just adjust that number definition (DC_MOTOR_UNITS).

**DC MOTOR Driver APIs**

As you’ve seen in the DC_MOTOR.h file, the provided APIs do all the basic functionalities that you may need from a motor driver library.

DC_MOTOR_Init: initializes the required GPIO pins for direction control logic (DIR1 & DIR2), the associated Timer with the selected PWM channel. And sets the PWM resolution and frequency as configured by the user in the config file.

DC_MOTOR_Start: Sets the motor direction and speed to start the motor

DC_MOTOR_Set_Speed: Sets the speed of the motor by adjusting the PWM duty cycle

DC_MOTOR_Set_Dir: Sets the direction of rotation for the motor (CW or CCW)

DC_MOTOR_Stop: Stops the motor

DC_MOTOR_GET_MaxFreq: Returns the maximum operating frequency (in Hz) for the PWM channel at the selected PWM resolution in the config structure for that specific motor

The example LABs will put everything under test and you’ll see how and when to use each of these functions.

**DC_MOTOR_cfg.c File**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17|#include "../DC_MOTOR/DC_MOTOR.h"<br><br>const DC_MOTOR_CfgType DC_MOTOR_CfgParam[DC_MOTOR_UNITS] =<br><br>{<br><br>    // DC MOTOR 1 Configurations<br><br>    {<br><br>    GPIOB,<br><br>    GPIOB,<br><br>    GPIO_PIN_12,<br><br>    GPIO_PIN_13,<br><br>    TIM2,<br><br>    TIM_CHANNEL_1,<br><br>    72,<br><br>    DC_MOTOR_F_PWM,<br><br>    DC_MOTOR_PWM_RES<br><br>    }<br><br>};|

I’ll discuss those configuration parameters in the next section down below.

**Available Configurations For ECUAL DC Motor Driver**

From the code above in DC_MOTOR.h & DC_MOTOR_cfg.c you can see that there are a handful of parameters in the configuration structure. The config structure will be used to assign the DIR1 & DIR2 GPIO pins, the associated TIMER peripheral you’ll be using (it can be TIM1, 2, and so on), the PWM_TIMER channel you’ll be using for the motor ENABLEx pin, the CLK speed in MHz for that specific TIMER module, the PWM frequency (in Hz), and finally the PWM resolution (in bits).

**Typical Usage Application Example**

Here is a typical usage application for this driver code in order to initialize & start a DC Motor, read an analog potentiometer with ADC. And use that reading to control the motor speed in the main loop of the system.

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36|#include "main.h"<br><br>#include "../ECUAL/DC_MOTOR/DC_MOTOR.h"<br><br>#define DC_MOTOR1    0<br><br>ADC_HandleTypeDef hadc1;<br><br>void SystemClock_Config(void);<br><br>static void MX_GPIO_Init(void);<br><br>static void MX_ADC1_Init(void);<br><br>int main(void)<br><br>{<br><br>    uint16_t AD_RES = 0;<br><br>    HAL_Init();<br><br>    SystemClock_Config();<br><br>    MX_GPIO_Init();<br><br>    MX_ADC1_Init();<br><br>    // Calibrate The ADC On Power-Up For Better Accuracy<br><br>    HAL_ADCEx_Calibration_Start(&hadc1);<br><br>    DC_MOTOR_Init(DC_MOTOR1);<br><br>    DC_MOTOR_Start(DC_MOTOR1, DIR_CW, 100);<br><br>    while (1)<br><br>    {<br><br>        // Start ADC Conversion<br><br>        HAL_ADC_Start(&hadc1);<br><br>        // Poll ADC1 Perihperal & TimeOut = 1mSec<br><br>        HAL_ADC_PollForConversion(&hadc1, 1);<br><br>        // Read The ADC Conversion Result & Map It To PWM DutyCycle<br><br>        AD_RES = HAL_ADC_GetValue(&hadc1);<br><br>        DC_MOTOR_Set_Speed(DC_MOTOR1, AD_RES>>2);<br><br>        HAL_Delay(1);<br><br>    }<br><br>}|

**PWM Resolution & Frequency Calculations**

As I’ve stated earlier in this tutorial, my DC Motor driver (library) gives the user full control over the PWM signal’s resolution. And on the other hand, it does approximate the PWM frequency given by the user. In other words, for a configuration like this (desired resolution = 10-Bits, desired FPWM = 1kHz).

The actual PWM signal will have exactly 10Bits of resolution (0 up to 1023 speed levels) but the FPWM will be 1.003kHz as shown below. Not bad right? of course it’s not an exact value like the resolution but it’s just as fine as we won’t need that much FPWM accuracy in DC motor control applications.

![STM32 DC Motor Control L293D Example Testing Driver](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-DC-Motor-Control-L293D-Example-Testing-Driver.png?ezimgfmt=rs:727x436/rscb6/ng:webp/ngcb6)

This step is done at the initialization function for the DC Motor driver. Check the source code of the DC_MOTOR_Init function. And here is what I’m doing: first of all, we need to set the Timer’s Prescaler (PSC) and auto-reload register’s value (ARR) to control both PWM signal’s frequency and resolution.

This is the first formula for PWM resolution. We’ll solve for ARR given the user-defined resolution (in bits) and other parameters. We can easily solve for ARR value.

![STM32 PWM Resolution General Equation - Formula](https://deepbluembedded.com/wp-content/uploads/2020/06/STM32-PWM-Resolution-General-Equation-Formula.png?ezimgfmt=rs:302x44/rscb6/ng:webp/ngcb6)

And now after knowing the ARR value, we can plug that into the FPWM equation and solve for PSC to find the approximate Prescaler value for the timer to get an FPWM that is as close as possible to the pre-defined value by the user.

![STM32 PWM Frequency Formula - STM32 PWM Frequency Equation](https://deepbluembedded.com/wp-content/uploads/2020/06/STM32-PWM-Frequency-Formula-STM32-PWM-Frequency-Equation.png?ezimgfmt=rs:247x43/rscb6/ng:webp/ngcb6)

---

###  **STM32 DC Motor Speed Control With Tip122 – LAB** 

|   |   |
|---|---|
|**LAB Number**|45|
|**LAB Title**|STM32 DC Motor Speed Control With Tip122 Transistor|

- Set up a new project as usual with a system clock @ 72MHz or whatever your uC board supports
- Enable TIMERx/CHx For PWM mode in CubeMX
- Enable Any ADC Channel in CubeMX
- Add the ECUAL / DC_MOTOR driver files to our project. As shown [here](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)
- Configure 1 DC_MOTOR instance in DC_MOTOR_cfg.c file
- Initialize the DC_MOTOR in the main application, start the DC Motor, read an analog potentiometer with ADC. And use that reading to control the motor speed in the main loop of the system.

![STM32 DC Motor Speed Control With Tip122 Transistor](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-DC-Motor-Speed-Control-With-Tip122-Transistor.png?ezimgfmt=rs:727x276/rscb6/ng:webp/ngcb6)

**And now, let’s build this system step-by-step**

**Step1: Open CubeMX & Create New Project**

**Step2: Choose The Target MCU & Double-Click Its Name**

STM32F103C8 (the one I’ll be using) or any other STM32 part you’ve got

**Step3: Go To The RCC Clock Configuration**

**Step4: Set The System Clock To Be 72MHz or whatever your uC board supports**

**Step5: Enable The TIMERx/CHx PWM That You’ll be using For L293D ENABLEx Pin**

We’ll have to do this step just to have the TIM_HAL files added to our project by CubeMX. And also it gives us a startup configuration for the PWM channel of that specific timer module as we’ll see hereafter. This is the best way to keep the code generic and portable to any STM32 microcontroller.

This step will result in adding a TIMER_Init function in our code by CubeMX, but we’ll remove it afterward as the DC_MOTOR_Init will handle that initialization procedure.

![STM32 DC Motor Control PWM With L293D](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-DC-Motor-Control-PWM-With-L293D.png?ezimgfmt=rs:727x342/rscb6/ng:webp/ngcb6)

**Step6: Enable The ADCx / CHx That You’ll Be Using For The Potentiometer**

![STM32 DC Motor Speed Control L293D Example Code](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-DC-Motor-Speed-Control-L293D-Example-Code.png?ezimgfmt=rs:727x343/rscb6/ng:webp/ngcb6)

**Step7: Generate The Initialization Code & Open The Project In Your IDE**

**Step8: Add the ECUAL/ DC_MOTOR driver files to your project**

Follow **[This Tutorial which shows you How To Add Any ECUAL Driver To An STM32 Project](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)** step-by-step.

[![Adding ECUAL Driver To STM32 Project](https://deepbluembedded.com/ezoimgfmt/i2.wp.com/deepbluembedded.com/wp-content/uploads/2020/06/Adding-ECUAL-Driver-To-STM32-Project.jpg?resize=254%2C120&ssl=1&ezimgfmt=rs:254x120/rscb6/ng:webp/ngcb6)](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)

You basically right-click the project name in the IDE’s navigator and choose to create a new > source folder. And name it **ECUAL** and go to the GitHub repo, download the files and copy the “DC_MOTOR” folder and back to the IDE, right-click on the ECUAL and paste the library into it.

Now, we can start developing our application in the main.c source file.

**Here Are The Drivers Configurations I Used For This LAB**

**DC_MOTOR_cfg.c File**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15|const DC_MOTOR_CfgType DC_MOTOR_CfgParam[DC_MOTOR_UNITS] =<br><br>{<br><br>    // DC MOTOR 1 Configurations<br><br>    {<br><br>    GPIOB,<br><br>    GPIOB,<br><br>    GPIO_PIN_12,<br><br>    GPIO_PIN_13,<br><br>    TIM2,<br><br>    TIM_CHANNEL_1,<br><br>    72,<br><br>    DC_MOTOR_F_PWM,<br><br>    DC_MOTOR_PWM_RES<br><br>    }<br><br>};|

The DC_MOTOR_F_PWM and DC_MOTOR_PWM_RES are defined in the DC_MOTOR.h file. The FPWM is 500Hz and the resolution is 10-Bits.

**Here is The Application Code For This LAB (main.c)**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36|#include "main.h"<br><br>#include "../ECUAL/DC_MOTOR/DC_MOTOR.h"<br><br>#define DC_MOTOR1    0<br><br>ADC_HandleTypeDef hadc1;<br><br>void SystemClock_Config(void);<br><br>static void MX_GPIO_Init(void);<br><br>static void MX_ADC1_Init(void);<br><br>int main(void)<br><br>{<br><br>    uint16_t AD_RES = 0;<br><br>    HAL_Init();<br><br>    SystemClock_Config();<br><br>    MX_GPIO_Init();<br><br>    MX_ADC1_Init();<br><br>    // Calibrate The ADC On Power-Up For Better Accuracy<br><br>    HAL_ADCEx_Calibration_Start(&hadc1);<br><br>    DC_MOTOR_Init(DC_MOTOR1);<br><br>    DC_MOTOR_Start(DC_MOTOR1, DIR_CW, 0);<br><br>    while (1)<br><br>    {<br><br>        // Start ADC Conversion<br><br>        HAL_ADC_Start(&hadc1);<br><br>        // Poll ADC1 Perihperal & TimeOut = 1mSec<br><br>        HAL_ADC_PollForConversion(&hadc1, 1);<br><br>        // Read The ADC Conversion Result & Map It To PWM DutyCycle<br><br>        AD_RES = HAL_ADC_GetValue(&hadc1);<br><br>        DC_MOTOR_Set_Speed(DC_MOTOR1, AD_RES>>2);<br><br>        HAL_Delay(1);<br><br>    }<br><br>}|

Note that: I’ve deleted the auto-generated TIM2 initialization function and its parameter. Since our DC Motor library will handle that, we don’t need that function at all.

Do also look into the stm32f1xx_hal_msp.c file. You’ll find some initialization functions that get called in main.c at this line ( HAL_Init(); ),. While one of which is being used by my library for the DC Motor initialization routine. Which is the Timer PWM pin initialization.

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15|void HAL_TIM_MspPostInit(TIM_HandleTypeDef* htim)<br><br>{<br><br>  GPIO_InitTypeDef GPIO_InitStruct = {0};<br><br>  if(htim->Instance==TIM2)<br><br>  {<br><br>    __HAL_RCC_GPIOA_CLK_ENABLE();<br><br>    /**TIM2 GPIO Configuration    <br><br>    PA0-WKUP     ------> TIM2_CH1<br><br>    */<br><br>    GPIO_InitStruct.Pin = GPIO_PIN_0;<br><br>    GPIO_InitStruct.Mode = GPIO_MODE_AF_PP;<br><br>    GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;<br><br>    HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);<br><br>  }<br><br>}|

As you can see, it does handle the PWM pin initialization for us for TIMER2 / CH1 pin. This can be hectic to do in our library init function ( DC_Motor_Init() ) in a generic way. So I’ve decided to use that function instead to keep the code more generic across all STM32 microcontrollers. Without having to figure out a way to find out which pin belongs to which timer’s channel and all that stuff.

[**Download The STM32 DC Motor Speed Control LAB45**](https://drive.google.com/file/d/1HwO21SD_Z40nx3zHs8eryn1uFngUvqsi/view?usp=sharing)

![STM32 DC Motor Speed Control With Tip122 Example](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-DC-Motor-Speed-Control-With-Tip122-Example-scaled.jpg?ezimgfmt=rs:727x328/rscb6/ng:webp/ngcb6)

**The Result For This LAB Testing (Video)**

Note that: this LAB is just a test for the basic functionality of our DC Motor Control library. Which is mainly built for L293D drivers and any H-Bridge that share the same control logic. That’s why the DIR1 & DIR2 pins are not useful in this LAB, despite the fact that they’re already initialized and reserved by the DC Motor library for DC_MOTOR1 structure.

---

###  **STM32 DC Motor Control With L293D – LAB** 

|   |   |
|---|---|
|**LAB Number**|46|
|**LAB Title**|1 DC Motor Control With L293D – Testing Our Motor Library|

- Set up a new project as usual with a system clock @ 72MHz or whatever your uC board supports
- Enable TIMERx/CHx For PWM mode in CubeMX
- Enable Any ADC Channel in CubeMX
- Add the ECUAL / DC_MOTOR driver files to our project. As shown [here](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)
- Add the ECUAL / BUTTONS driver files to our project
- Configure 1 DC_MOTOR instance in DC_MOTOR_cfg.c file
- Configure 1 BUTTON instance in BUTTONS_cfg.c file
- Initialize the BUTTON & DC_MOTOR in the main application, start the DC Motor, read an analog potentiometer with ADC. And use that reading to control the motor speed in the main loop of the system. Read the buttons and use it to flip the direction of motor rotation. Repeat!

![STM32 DC Motor Speed Control With L293D Driver IC Connection](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-DC-Motor-Speed-Control-With-L293D-Driver-IC-Connection.png?ezimgfmt=rs:727x388/rscb6/ng:webp/ngcb6)

**And now, let’s build this system step-by-step**

**Step1: Open CubeMX & Create New Project**

**Step2: Choose The Target MCU & Double-Click Its Name**

STM32F103C8 (the one I’ll be using) or any other STM32 part you’ve got

**Step3: Go To The RCC Clock Configuration**

**Step4: Set The System Clock To Be 72MHz or whatever your uC board supports**

**Step5: Enable The TIMERx/CHx PWM That You’ll be using For L293D ENABLEx Pin**

We’ll have to do this step just to have the TIM_HAL files added to our project by CubeMX. And also it gives us a startup configuration for the PWM channel of that specific timer module as we’ll see hereafter. This is the best way to keep the code generic and portable to any STM32 microcontroller.

This step will result in adding a TIMER_Init function in our code by CubeMX, but we’ll remove it afterward as the DC_MOTOR_Init will handle that initialization procedure.

![STM32 DC Motor Control PWM With L293D](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-DC-Motor-Control-PWM-With-L293D.png?ezimgfmt=rs:727x342/rscb6/ng:webp/ngcb6)

**Step6: Enable The ADCx / CHx That You’ll Be Using For The Potentiometer**

![STM32 DC Motor Speed Control L293D Example Code](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-DC-Motor-Speed-Control-L293D-Example-Code.png?ezimgfmt=rs:727x343/rscb6/ng:webp/ngcb6)

**Step7: Generate The Initialization Code & Open The Project In Your IDE**

**Step8: Add the ECUAL/ DC_MOTOR driver files to your project**

Follow **[This Tutorial which shows you How To Add Any ECUAL Driver To An STM32 Project](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)** step-by-step.

[![Adding ECUAL Driver To STM32 Project](https://deepbluembedded.com/ezoimgfmt/i2.wp.com/deepbluembedded.com/wp-content/uploads/2020/06/Adding-ECUAL-Driver-To-STM32-Project.jpg?resize=254%2C120&ssl=1&ezimgfmt=rs:254x120/rscb6/ng:webp/ngcb6)](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)

You basically right-click the project name in the IDE’s navigator and choose to create a new > source folder. And name it **ECUAL** and go to the GitHub repo, download the files and copy the “DC_MOTOR” folder and back to the IDE, right-click on the ECUAL and paste the library into it.

**Step9: Add the ECUAL/ BUTTONS driver files to your project**

The same as the previous step

**Step10: Add the MATH files to your project**

Now, we can start developing our application in the main.c source file.

**Here Are The Drivers Configurations I Used For This LAB**

**DC_MOTOR_cfg.c File**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15|const DC_MOTOR_CfgType DC_MOTOR_CfgParam[DC_MOTOR_UNITS] =<br><br>{<br><br>    // DC MOTOR 1 Configurations<br><br>    {<br><br>    GPIOB,<br><br>    GPIOB,<br><br>    GPIO_PIN_12,<br><br>    GPIO_PIN_13,<br><br>    TIM2,<br><br>    TIM_CHANNEL_1,<br><br>    72,<br><br>    DC_MOTOR_F_PWM,<br><br>    DC_MOTOR_PWM_RES<br><br>    }<br><br>};|

The DC_MOTOR_F_PWM and DC_MOTOR_PWM_RES are defined in the DC_MOTOR.h file. The FPWM is 500Hz and the resolution is 10-Bits.

**BUTTONS_cfg.c File**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10|const BTN_CfgType BTN_CfgParam[BTN_UNITS] =<br><br>{<br><br>    // Button 1 Configurations<br><br>    {<br><br>        GPIOA,<br><br>    GPIO_PIN_6,<br><br>    GPIO_NOPULL,<br><br>    BTNS_FILTER_ORDER<br><br>    }<br><br>};|

**Here is The Application Code For This LAB (main.c)**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36<br><br>37<br><br>38<br><br>39<br><br>40<br><br>41<br><br>42<br><br>43<br><br>44<br><br>45<br><br>46<br><br>47<br><br>48<br><br>49<br><br>50<br><br>51<br><br>52<br><br>53<br><br>54<br><br>55<br><br>56<br><br>57<br><br>58<br><br>59<br><br>60|#include "main.h"<br><br>#include "../ECUAL/DC_MOTOR/DC_MOTOR.h"<br><br>#include "../ECUAL/BUTTONS/BUTTONS.h"<br><br>#define DC_MOTOR1    0<br><br>#define BTN1         0<br><br>ADC_HandleTypeDef hadc1;<br><br>void SystemClock_Config(void);<br><br>static void MX_GPIO_Init(void);<br><br>static void MX_ADC1_Init(void);<br><br>int main(void)<br><br>{<br><br>    uint16_t AD_RES = 0;<br><br>    uint8_t BTNS_States[BTN_UNITS] = {BTN_RELEASED};<br><br>    uint8_t MOTOR1_DIR = DIR_CW;<br><br>    uint8_t DIR_FLAG = 0;<br><br>    HAL_Init();<br><br>    SystemClock_Config();<br><br>    MX_GPIO_Init();<br><br>    MX_ADC1_Init();<br><br>    // Calibrate The ADC On Power-Up For Better Accuracy<br><br>    HAL_ADCEx_Calibration_Start(&hadc1);<br><br>    DC_MOTOR_Init(DC_MOTOR1);<br><br>    DC_MOTOR_Start(DC_MOTOR1, DIR_CW, 0);<br><br>    BTNs_Init(BTNS_States);<br><br>    while (1)<br><br>    {<br><br>        // Read Pot. ADC CH & Set Motor Speed<br><br>        HAL_ADC_Start(&hadc1);<br><br>        HAL_ADC_PollForConversion(&hadc1, 1);<br><br>        AD_RES = HAL_ADC_GetValue(&hadc1);<br><br>        DC_MOTOR_Set_Speed(DC_MOTOR1, AD_RES>>2);<br><br>        // Read BTN1 & Set Motor1 Direction<br><br>        BTN_Read(BTN1, &BTNS_States[BTN1]);<br><br>        if(BTNS_States[BTN1] == BTN_PRESSED)<br><br>        {<br><br>            if(DIR_FLAG == 0)<br><br>            {<br><br>            MOTOR1_DIR = DIR_CW;<br><br>            DIR_FLAG = 1;<br><br>            }<br><br>            else<br><br>            {<br><br>            MOTOR1_DIR = DIR_CCW;<br><br>            DIR_FLAG = 0;<br><br>            }<br><br>            DC_MOTOR_Set_Dir(DC_MOTOR1, MOTOR1_DIR);<br><br>            while(BTNS_States[BTN1] == BTN_PRESSED)<br><br>            {<br><br>            BTN_Read(BTN1, &BTNS_States[BTN1]);<br><br>            }<br><br>        }<br><br>        HAL_Delay(1);<br><br>    }<br><br>}|

Note that: I’ve deleted the auto-generated TIM2 initialization function and its parameter. Since our DC Motor library will handle that, we don’t need that function at all.

[**Download The STM32 DC Motor Speed Control With L293D LAB46**](https://drive.google.com/file/d/1f_xTdZpV77lzjQRbOT0q6j9AVmDa_DfN/view?usp=sharing)

![STM32 DC Motor Speed Control With L293D Example Wiring](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-DC-Motor-Speed-Control-With-L293D-Example-Wiring-scaled.jpg?ezimgfmt=rs:727x328/rscb6/ng:webp/ngcb6)

**The Result For This LAB Testing (Video)**

---

###  **STM32 Multi DC Motor Control L293D – LAB** 

|   |   |
|---|---|
|**LAB Number**|47|
|**LAB Title**|2 DC Motors Control With L293D – Testing Our Motor Library|

- Set up a new project as usual with a system clock @ 72MHz or whatever your uC board supports
- Enable 2 TIMERx/CHx For PWM mode in CubeMX
- Enable Any 2 ADC Channels in CubeMX
- Add the ECUAL / DC_MOTOR driver files to our project. As shown [here](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)
- Add the ECUAL / BUTTONS driver files to our project
- Configure 2 DC_MOTOR instance in DC_MOTOR_cfg.c file
- Configure 2 BUTTON instance in BUTTONS_cfg.c file
- Initialize the BUTTONs & DC_MOTORs in the main application, start the DC Motors, read the analog potentiometers with ADC. And use that reading to control the 2 motor speeds in the main loop of the system. Read the buttons and use that signal to flip the direction of the 2 motors rotation. Repeat!

**And now, let’s build this system step-by-step**

**Step1: Open CubeMX & Create New Project**

**Step2: Choose The Target MCU & Double-Click Its Name**

STM32F103C8 (the one I’ll be using) or any other STM32 part you’ve got

**Step3: Go To The RCC Clock Configuration**

**Step4: Set The System Clock To Be 72MHz or whatever your uC board supports**

**Step5: Enable The 2 TIMERx/CHx PWMs That You’ll be using For L293D ENABLE1, 2 Pins**

We’ll have to do this step just to have the TIM_HAL files added to our project by CubeMX. And also it gives us a startup configuration for the PWM channel of that specific timer module as we’ll see hereafter. This is the best way to keep the code generic and portable to any STM32 microcontroller.

This step will result in adding a TIMER_Init function in our code by CubeMX, but we’ll remove it afterward as the DC_MOTOR_Init will handle that initialization procedure.

![STM32 Multi DC Motors Control L293D Example Project L47](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-Multi-DC-Motors-Control-L293D-Example-Project-L47.png?ezimgfmt=rs:727x348/rscb6/ng:webp/ngcb6)

**Step6: Enable The 2 ADCx / CHx That You’ll Be Using For The 2 Potentiometers**

![STM32 Multi DC Motors Control L293D Example Code L47](https://deepbluembedded.com/wp-content/uploads/2021/03/STM32-Multi-DC-Motors-Control-L293D-Example-Code-L47.png?ezimgfmt=rs:727x346/rscb6/ng:webp/ngcb6)

**Step7: Generate The Initialization Code & Open The Project In Your IDE**

**Step8: Add the ECUAL/ DC_MOTOR driver files to your project**

Follow **[This Tutorial which shows you How To Add Any ECUAL Driver To An STM32 Project](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)** step-by-step.

[![Adding ECUAL Driver To STM32 Project](https://deepbluembedded.com/ezoimgfmt/i2.wp.com/deepbluembedded.com/wp-content/uploads/2020/06/Adding-ECUAL-Driver-To-STM32-Project.jpg?resize=254%2C120&ssl=1&ezimgfmt=rs:254x120/rscb6/ng:webp/ngcb6)](https://deepbluembedded.com/adding-ecual-drivers-to-your-stm32-project-configurations-options/)

You basically right-click the project name in the IDE’s navigator and choose to create a new > source folder. And name it **ECUAL** and go to the GitHub repo, download the files and copy the “DC_MOTOR” folder and back to the IDE, right-click on the ECUAL and paste the library into it.

**Step9: Add the ECUAL/ BUTTONS driver files to your project**

The same as the previous step

**Step10: Add the MATH files to your project**

Now, we can start developing our application in the main.c source file.

**Here Are The Drivers Configurations I Used For This LAB**

**DC_MOTOR_cfg.c File**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27|const DC_MOTOR_CfgType DC_MOTOR_CfgParam[DC_MOTOR_UNITS] =<br><br>{<br><br>    // DC MOTOR 1 Configurations<br><br>    {<br><br>    GPIOB,<br><br>    GPIOB,<br><br>    GPIO_PIN_12,<br><br>    GPIO_PIN_13,<br><br>    TIM2,<br><br>    TIM_CHANNEL_1,<br><br>    72,<br><br>    DC_MOTOR_F_PWM,<br><br>    DC_MOTOR_PWM_RES<br><br>    },<br><br>    // DC MOTOR 2 Configurations<br><br>    {<br><br>    GPIOB,<br><br>    GPIOB,<br><br>    GPIO_PIN_14,<br><br>    GPIO_PIN_15,<br><br>    TIM2,<br><br>    TIM_CHANNEL_2,<br><br>    72,<br><br>    DC_MOTOR_F_PWM,<br><br>    DC_MOTOR_PWM_RES<br><br>    }<br><br>};|

The DC_MOTOR_F_PWM and DC_MOTOR_PWM_RES are defined in the DC_MOTOR.h file. The FPWM is 500Hz and the resolution is 10-Bits.

**BUTTONS_cfg.c File**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17|const BTN_CfgType BTN_CfgParam[BTN_UNITS] =<br><br>{<br><br>    // Button 1 Configurations<br><br>    {<br><br>        GPIOA,<br><br>    GPIO_PIN_6,<br><br>    GPIO_NOPULL,<br><br>    BTNS_FILTER_ORDER<br><br>    },<br><br>    // Button 2 Configurations<br><br>    {<br><br>    GPIOA,<br><br>    GPIO_PIN_7,<br><br>    GPIO_NOPULL,<br><br>    BTNS_FILTER_ORDER<br><br>    }<br><br>};|

**Here is The Application Code For This LAB (main.c)**

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36<br><br>37<br><br>38<br><br>39<br><br>40<br><br>41<br><br>42<br><br>43<br><br>44<br><br>45<br><br>46<br><br>47<br><br>48<br><br>49<br><br>50<br><br>51<br><br>52<br><br>53<br><br>54<br><br>55<br><br>56<br><br>57<br><br>58<br><br>59<br><br>60<br><br>61<br><br>62<br><br>63<br><br>64<br><br>65<br><br>66<br><br>67<br><br>68<br><br>69<br><br>70<br><br>71<br><br>72<br><br>73<br><br>74<br><br>75<br><br>76<br><br>77<br><br>78<br><br>79<br><br>80<br><br>81<br><br>82<br><br>83<br><br>84<br><br>85<br><br>86<br><br>87<br><br>88<br><br>89<br><br>90<br><br>91<br><br>92<br><br>93<br><br>94<br><br>95<br><br>96<br><br>97<br><br>98<br><br>99<br><br>100<br><br>101<br><br>102<br><br>103<br><br>104<br><br>105<br><br>106<br><br>107<br><br>108<br><br>109|#include "main.h"<br><br>#include "../ECUAL/DC_MOTOR/DC_MOTOR.h"<br><br>#include "../ECUAL/BUTTONS/BUTTONS.h"<br><br>#define DC_MOTOR1    0<br><br>#define DC_MOTOR2    1<br><br>#define BTN1         0<br><br>#define BTN2         1<br><br>ADC_HandleTypeDef hadc1;<br><br>ADC_ChannelConfTypeDef sConfig = {0};<br><br>uint8_t BTNS_States[BTN_UNITS] = {BTN_RELEASED};<br><br>uint8_t MOTOR1_DIR = DIR_CW;<br><br>uint8_t MOTOR2_DIR = DIR_CW;<br><br>uint8_t DIR1_FLAG = 0;<br><br>uint8_t DIR2_FLAG = 0;<br><br>void SystemClock_Config(void);<br><br>static void MX_GPIO_Init(void);<br><br>static void MX_ADC1_Init(void);<br><br>static void BUTTONS_HANDLER(void);<br><br>static void Sys_Init(void);<br><br>int main(void)<br><br>{<br><br>    uint16_t AD_RES = 0;<br><br>    Sys_Init();<br><br>    while (1)<br><br>    {<br><br>        // Read Pot. ADC CH4 & Set Motor1 Speed<br><br>        sConfig.Channel = ADC_CHANNEL_4;<br><br>        HAL_ADC_ConfigChannel(&hadc1, &sConfig);<br><br>        HAL_ADC_Start(&hadc1);<br><br>        HAL_ADC_PollForConversion(&hadc1, 1);<br><br>        AD_RES = HAL_ADC_GetValue(&hadc1);<br><br>        DC_MOTOR_Set_Speed(DC_MOTOR1, AD_RES>>2);<br><br>        // Read Pot. ADC CH5 & Set Motor2 Speed<br><br>        sConfig.Channel = ADC_CHANNEL_5;<br><br>        HAL_ADC_ConfigChannel(&hadc1, &sConfig);<br><br>        HAL_ADC_Start(&hadc1);<br><br>        HAL_ADC_PollForConversion(&hadc1, 1);<br><br>        AD_RES = HAL_ADC_GetValue(&hadc1);<br><br>        DC_MOTOR_Set_Speed(DC_MOTOR2, AD_RES>>2);<br><br>        // Read Buttons States & Handle Direction Logic<br><br>        BUTTONS_HANDLER();<br><br>        HAL_Delay(1);<br><br>    }<br><br>}<br><br>static void Sys_Init(void)<br><br>{<br><br>    HAL_Init();<br><br>    SystemClock_Config();<br><br>    MX_GPIO_Init();<br><br>    MX_ADC1_Init();<br><br>    // Calibrate The ADC On Power-Up For Better Accuracy<br><br>    HAL_ADCEx_Calibration_Start(&hadc1);<br><br>    DC_MOTOR_Init(DC_MOTOR1);<br><br>    DC_MOTOR_Init(DC_MOTOR2);<br><br>    DC_MOTOR_Start(DC_MOTOR1, DIR_CW, 0);<br><br>    DC_MOTOR_Start(DC_MOTOR2, DIR_CW, 0);<br><br>    BTNs_Init(BTNS_States);<br><br>}<br><br>static void BUTTONS_HANDLER(void)<br><br>{<br><br>    // Read BTN1 & Set Motor1 Direction<br><br>    BTN_Read(BTN1, &BTNS_States[BTN1]);<br><br>    if(BTNS_States[BTN1] == BTN_PRESSED)<br><br>    {<br><br>    if(DIR1_FLAG == 0)<br><br>    {<br><br>        MOTOR1_DIR = DIR_CW;<br><br>        DIR1_FLAG = 1;<br><br>    }<br><br>    else<br><br>    {<br><br>        MOTOR1_DIR = DIR_CCW;<br><br>        DIR1_FLAG = 0;<br><br>    }<br><br>    DC_MOTOR_Set_Dir(DC_MOTOR1, MOTOR1_DIR);<br><br>    while(BTNS_States[BTN1] == BTN_PRESSED)<br><br>    {<br><br>        BTN_Read(BTN1, &BTNS_States[BTN1]);<br><br>    }<br><br>    }<br><br>    // Read BTN2 & Set Motor2 Direction<br><br>    BTN_Read(BTN2, &BTNS_States[BTN2]);<br><br>    if(BTNS_States[BTN2] == BTN_PRESSED)<br><br>    {<br><br>    if(DIR2_FLAG == 0)<br><br>    {<br><br>        MOTOR2_DIR = DIR_CW;<br><br>        DIR2_FLAG = 1;<br><br>    }<br><br>    else<br><br>    {<br><br>        MOTOR2_DIR = DIR_CCW;<br><br>        DIR2_FLAG = 0;<br><br>    }<br><br>    DC_MOTOR_Set_Dir(DC_MOTOR2, MOTOR2_DIR);<br><br>    while(BTNS_States[BTN2] == BTN_PRESSED)<br><br>    {<br><br>        BTN_Read(BTN2, &BTNS_States[BTN2]);<br><br>    }<br><br>    }<br><br>}|

Note that: I’ve deleted the auto-generated TIM2 initialization function and its parameter. Since our DC Motor library will handle that, we don’t need that function at all.

[**  
Download The STM32 Multi DC Motor Control L293D Driver LAB47**](https://drive.google.com/file/d/1KT4PQc9roJb4mJPY_O3m9Mf5rvFTqgwj/view?usp=sharing)

**The Result For This LAB Testing (Video)**

---

###  **Questions I Usually Get About DC Motor Control** 

#### **PWM Frequency For DC Motor Speed Control?**

This is one of the interesting questions that I’ve received a couple of times in the past and led us to a nice discussion about the matter. What you’d always hear regarding this topic is that FPWM should be not too low not too high for a reliable DC motor speed control.

And it can be really vague to decide which frequency is too low or high. But the commonly suggested values lie between 200Hz up to 2kHz or 5kHz. This is stated in many motor drivers’ ICs datasheets as the maximum switching frequency as well. While in other cases, the favored frequency can be up to 20kHz so as not to have any audible noise from the electronics side of the system. This can’t always be the case for all sorts of applications.

In fact, there is strong reasoning behind this being true. Which is a topic for a future article I’ll be publishing to show you my testing results and how to model a DC motor and take the parameters to MATLAB and simulate what’s going on and how FPWM affects the actual motor speed which can be weird to hear. Most of us at the beginning think that only the duty cycle controls the DC motor speed. However, it’ll turn out to be not exactly true, also the FPWM has a strong effect if not picked correctly as we’ll see.

#### **PWM Resolution For DC Motor Speed Control?**

Now, let’s see what value you should pick for the PWM resolution. It totally depends on the levels of control you want to have over the motor’s speed. And it should be the same as the ADC’s resolution for the potentiometer used for control.

If your ADC is 10-bits, there won’t be any reason to have a PWM resolution that goes beyond 10-bits as well. But generally speaking, having 10-bits of resolution is more than enough in most applications. And if you’re going to use my library as it is, you’ll have to keep in mind that the speed values you’re sending to the control function should be correct for the resolution you’ve selected. For example (Desired Resolution = 8bit, so the max speed is 255 & min is 0) and so on.

#### **DC Motor Position Control**

How to do position control using a DC Motor? well, this sounds like willing to create a servo motor. Basically, you’ll need to have some sort of feedback to tell the controller where is the DC Motor’s shaft. It can be physical contact with a rotary potentiometer or an optical encoder. I’ll try to include a similar tutorial for this in the future as well. But you can search this topic online to find more ideas and techniques to achieve servo motor control using DC motors.