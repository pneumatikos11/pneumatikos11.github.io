# Intro
In embedded systems, the clock is an essential element responsible for synchronizing all the different parts of the system and ensuring that they work together correctly. Modern microcontrollers are complex systems having different domains running often at different speeds. However, the clock tends often to be generated from the same source so that all the domains are synchronized to one another.

The High Speed Internal (HSI) clock is the internal RC oscillator clock that comes with the microcontroller board. The HSI clock consists of an RC circuit, which is a ==resistor-capacitor circuit== that oscillates at a specific frequency and can function as the clock.

The High Speed External clock is the external clock that sometimes comes attached to a microcontroller. Other times, the external circuitry may need to be added. The HSE normally consists of an external crystal oscillator. The external crystal oscillator is manufactured to operate at a specific frequency and and, like the HSI, can function as the system clock for a microcontroller. The system clock is the main clock for the entire microcontroller.

HSI is the default clock for STM32F103C6T6 microcontrollers, so configuring HSE requires more setup.
# Pros and Cons
There are advantages and disadvantages to using HSI as the clock vs HSE.

Advantages of using the HSI clock as the system clock is a faster startup time, less power consumption, and no need to add any external circuitry to a board. A disadvantage is a less accurate clock signal, which is a clock signal that can vary a good deal, especially based on the ambient temperature.

An advantage of using the HSE clock as the system clock is a much more accurate clock signal. Disadvantages include a longer startup time and more power consumption. 



# References 
[HSI vs HSE Clock in an STM32F4xx Microcontroller Board- Explained (learningaboutelectronics.com)](https://www.learningaboutelectronics.com/Articles/HSI-vs-HSE-clock-in-an-STM32F4xx-microcontroller.php)
[ARM Cortex clock tree 101: Navigating clock domains (playembedded.org)](https://www.playembedded.org/blog/arm-cortex-clock-tree-101/)
