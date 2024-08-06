##### HAL_GPIO_WritePin(GPIOx, GPIO_Pin, PinState);
- This is to write a particular state to a pin. It takes three inputs, 
1. Port Specification for example GPIOC or GPIOB
2. The GPIO pin for example GPIO_PIN_13
3. Pin state which indicates whether it is for high or for low. Takes 1 for high or 0 for low
You could take a look [[STM32 Development/Introduction#Blinking Internal LED|here]] for an example on how to use it. 
#### HAL_Delay(Delay);
- This is used to create a delay or pause in your function. It takes one parameter which is an unsigned 32 bit integer which is translated to the number mof microseconds that it delays for. 