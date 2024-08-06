Items to use:
1. STM32F103x boards 
2. MCP2551 CAN Transceivers

## About CAN
CAN (Controlled Area Network) Protocol is a way of communication between different devices, but under certain rules. These rules must be followed when a message is transmitted over the CAN bus.

## Rules for Standard CAN frame
-   Here, **Identifier** is the ID of the transmitting Device
-   **RTR** (Remote Transmission Request) Specifies if the data is Remote frame or Data frame
-   **IDE** specifies if we are using Standard ID or Extended ID
-   **r** is the Reserved bit
-   **DLC** specifies the data length in Bytes
-   **Data Field** is where we can send the data, which should be upto 8 bytes
-   **Checksum** and DEL are the CRC data and it’s Delimiter
-   **ACK** and DEL is the acknowledgment bit and it’s Delimiter

# Setting Up IOC
-   Here the **BAUD RATE** is set to 500000 bps. You can try different different combinations for **Prescalar and Time Quanta** to achieve this.
-   The Operating Mode is **NORMAL Mode**
-   Pins PA11 and PA12 are set as CAN_RX and CAN_TX from [[STM32 Development/Introduction#Pinout|here]]   
# Hardware Connections
![[Pasted image 20221103193329.png]]
https://lastminuteengineers.com/mcp2515-can-module-arduino-tutorial/ 

Change 500kb/s time prescaler from 18 to 36 in stm32 and it should work with arduino uno 