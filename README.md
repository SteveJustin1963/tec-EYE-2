# tec-EYE-2


![image](https://github.com/SteveJustin1963/tec-EYE-2/assets/58069246/bf1b8a80-b41d-4577-814f-2461f2c01500)

![image](https://github.com/SteveJustin1963/tec-EYE-2/assets/58069246/c03e3b7c-72e4-4fed-9917-c4514ebec4fe)



 

## Overview
A project to interface the PMW3360DM optical sensor with the TEC-1 computer, capturing image data and transmitting it via UART at 4800 baud.

## Hardware Requirements
- TEC-1 Computer
- PMW3360DM optical sensor
- SPI interface connections
- UART interface

## Connection Details

### SPI Connections (PMW3360DM to TEC-1)
- VCC → 5V
- GND → GND
- CS → D10 (Chip Select)
- MOSI → D11
- MISO → D12
- SCK → D13

### UART Connections
- GND → GND
- TX → D0
- RX → D1

## MINT Implementation

The original Arduino code has been converted to MINT for the TEC-1. Here's the MINT version:

```mint
// Define variables for SPI control
:A 10 p!         // CS pin number stored in p
     #FF q!      // SPI transfer byte stored in q
     0 r!        // Result storage in r
;

// SPI initialization
:B p 1 /O        // Set CS pin high
;

// SPI chip select low
:C p 0 /O        // Set CS pin low
;
 
// SPI transfer function
:D #80 /O q /O   // Send byte on SPI bus (Port 80)
   #81 /I r!     // Read result from SPI bus (Port 81)
;

// Get image data function
:E C             // CS low
   #42 q! D      // Send read command
   3(            // Loop 3 times
     #FF q! D    // Read byte
     r .         // Print byte
   )
   B             // CS high
;

// Main loop
:M B             // Initialize CS
   /U(           // Infinite loop
     E           // Get image data
     1000()      // Delay
   )
;

// Run program
M
```

## Protocol Details

The PMW3360DM uses the following communication protocol:
1. CS pin is set low to begin communication
2. Send command byte (0x42 for image data)
3. Read 3 bytes of image data
4. CS pin is set high to end communication

## Operation

1. Initialize SPI by setting CS pin high
2. Enter main loop:
   - Pull CS low
   - Send read command (0x42)
   - Read 3 bytes of image data
   - Pull CS high
   - Delay 1 second
   - Repeat

## Data Format

The PMW3360DM returns 3 bytes of image data per read:
- Byte 1: Image data high byte
- Byte 2: Image data middle byte
- Byte 3: Image data low byte

## Notes

- The MINT implementation uses ports 0x80 and 0x81 for SPI communication
- The program runs continuously until interrupted
- Data is output via UART at 4800 baud
- CS pin must be toggled between readings

## Future Improvements

1. Add error checking for SPI communication
2. Implement image data processing
3. Add configurable sensor settings
4. Implement frame buffering
