# Introduction
When you start working with electronics, you realise that when you do not assign some pins a value they become a floating pin. Due to the Electromagnetic interference of things around us, they influence the pin to a random high or low which is not good. A good example of this is in a button. If you do not connect anything to the pin it becomes undefined or floating. 
![[Button Floating Pin.png]]
# Pull-Up resistors
What pull-up resistors do is that they connect the floating pin to high (which for example in an Arduino uno is the 5V pin or 3V3 pin in STM32F103xx). This assigns a default high to the pin when it is not connected to any pin pushing out some voltage or is GND. 
![[PullUp.png]]
# Pull-Down resistors
What pull-down resistors do is that they connect the floating pin to low (which for example in an Arduino uno is the GND pin). This assigns a default low to the pin when it is not connected to any pin pushing out some voltage or is GND. 

> [!Question] Why not just use a wire instead of a resistor?
> When you use a wire, you could say the wire has approximately zero ohms. When you tie the pin to +V, you are essentially shorting it since the other pin is also connected to GND with a nearly zero ohm wire (Check first image below).  Without the wire is also infinite ohms (also technically not true since air, solder mask amongst others between the wire and +V have some resistance but it is very high) which just means nothing is connected and it floats. That is why resistors are used because they give you some value between the zero and infinity, between shorting the circuit and floating the pin. 

![[Pull-Up Wire Short.png]]

> [!Question] What value of resistor should I use?
> There really is no one answer for all but what suits your project. Picking low resistors are strong pullups because they allow more current to flow and vice versa for weak pullups. So you would generally pick from a range that suits your project. Like everything in engineering, it is a tradeoff. If you use bigger resistors, you would save power from batteries because it allows less current to flow, but it starts to float more often. If you use smaller resistors, it would almost never float but the resistor would burn more power from the battery and use more power. A good example of a use case is in I2C. When the lengths of the wires are long, you would need a stronger pullup to prevent floating and that is a smaller resistance value. The actual value is not all that critical.  


![[ResistanceRange.png]]



References 
[Picking Pull-Up Resistor Values | AO #25 - YouTube](https://www.youtube.com/watch?v=u3Xiy2DVnI4) 
