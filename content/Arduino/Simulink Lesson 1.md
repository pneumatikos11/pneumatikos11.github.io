# Blinking LED 
The following is the connection being used. 
![[Pasted image 20240806135839.png]]

You can use the repeating staircase to get pulse the signals in simulink
![[Pasted-image-20231112204158.png]]
# Dimming LED
- You would need to introduce a constant input and a slider gain before connecting to the PWM block![[Pasted image 20231112211810.png]]
- If you would want to do it in real-time you would have to click the "Monitor & Tune" button. 

