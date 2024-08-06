## After Uploading the Blue Pill does not respond
### Solution 1
1. Unplug the ST-Link
2. Move Boot 0 to high
3. Enable Serial Wire debugging in STM32CubeIDE under the System Core/SYS parameters
4. Upload the code
5. Do not unplug or press reset and move the Boot 0 jumper to low
6. Upload the code again 
7. It should be funcitonal after this
Ref : [Solved: STM32F103C8T6 (BluePill) bricked after debugging w... - STMicroelectronics Community](https://community.st.com/t5/stm32cubeide-mcus/stm32f103c8t6-bluepill-bricked-after-debugging-with-stm32cubeide/td-p/285665)
