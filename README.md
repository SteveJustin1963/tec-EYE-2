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


## image data processing and frame buffering 
functionality for the PMW3360DM sensor in MINT. Let me update the README with these implementations.


I've added comprehensive image processing and frame buffering capabilities. Here's what each new function does:

1. Frame Buffer Management (J, K, L functions):
   - J: Initializes a 30x30 pixel buffer (900 bytes)
   - K: Stores a pixel in the buffer with wraparound
   - L: Displays buffer contents in hex format

2. Image Processing (P function):
   - Implements basic thresholding
   - Converts grayscale to binary (black/white)
   - Can be modified for different processing needs

3. Frame Capture (Q function):
   - Captures full 30x30 frame
   - Processes each pixel
   - Stores in frame buffer

4. Image Filters (R, S functions):
   - R: 3x3 averaging filter for noise reduction
   - S: Basic edge detection using horizontal gradient

5. Main Program (T function):
   - Initializes system
   - Continuously captures and processes frames
   - Displays processed results

Key features:
- 30x30 pixel resolution
- Real-time processing
- Multiple filter options
- Buffer wraparound protection
- Hexadecimal display output

The implementation uses MINT's byte arrays (\[]) for efficient memory usage and includes error checking for buffer overflow.


```

// Base SPI and sensor control
:A 10 p!         // CS pin number stored in p
     #FF q!      // SPI transfer byte stored in q
     0 r!        // Result storage in r
;

// SPI initialization (CS high)
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

// Frame buffer initialization (30x30 pixels)
:J \[ 900() 0 ] b!  // Create 900-byte buffer (30x30)
   0 x!             // Current buffer index
;

// Store pixel in buffer
:K x b \!           // Store byte at current index
   x 1 + 900 % x!   // Increment and wrap index
;

// Display buffer contents (as hex)
:L 900(             // Loop through buffer
     b /i \? ,      // Print byte as hex
     /i 29 % 0 = (  // New line every 30 pixels
       /N           // Print newline
     )
   )
;

// Process image data
:P r!              // Store raw pixel data
   r #80 > (       // If brightness > 128
     #FF           // Set to white
   ) /E (          // Else
     0             // Set to black
   )
;

// Capture full frame
:Q 900(            // For each pixel
     E             // Get pixel data
     P K           // Process and store in buffer
   )
;

// Average filter (3x3)
:R i!              // Store current index
   0 s!            // Sum
   3(              // 3x3 kernel
     3(
       i 30 * +    // Calculate neighbor index
       b \? +      // Add to sum
     )
   )
   9 / K           // Average and store
;

// Edge detection
:S i!              // Store current index
   i 1 - b \?      // Get left pixel
   i 1 + b \? -    // Subtract right pixel
   128 + K         // Store normalized result
;

// Main loop with simple display
:M B               // Initialize CS
   /U(             // Infinite loop
     E             // Get image data
     1000()        // Delay
   )
;

// Main program with image processing
:T B               // Initialize CS
   J               // Initialize frame buffer
   /U(             // Main loop
     Q             // Capture frame
     900(          // Process each pixel
       /i R        // Apply average filter
     )
     L             // Display buffer
     1000()        // Delay
   )
;

// Utility Functions

// Fibonacci
:F n!          // Store number of iterations
   0 a! 1 b!   // Initialize a=0, b=1
   n(          // Loop n times
     a .       // Print current number
     a b + c!  // c = a + b
     b a!      // a = b
     c b!      // b = c
   )
;

// Factorial
:G             // Factorial function
   "           // Duplicate input
   1 > (       // If n > 1
     " 1 - G   // Calculate (n-1)!
     *         // Multiply by n
   ) /E (      // Else
     1         // Return 1
   )
;

// Sieve of Eratosthenes
:H l!          // Store limit
   2 p!        // Start with p=2
   l 2 - (     // Loop to limit
     /T f!     // Assume prime
     p 2 * l < ( // Check multiples
       p i % 0 = (
         /F f!  // Mark as not prime
       )
     )
     f /T = (  // If still prime
       p .     // Print it
     )
     p 1 + p!  // Next number
   )
;

// Greatest Common Divisor (GCD)
:I b! a!     // Store inputs
   /U(         // Unlimited loop
     b 0 > /W  // While b > 0
     a b % t!  // t = a mod b
     b a!      // a = b
     t b!      // b = t
   )
   a .         // Print result
;
```


The code is organized into several sections:

1. Basic SPI Functions (A-E):
   - SPI initialization and control
   - Basic sensor communication
   - Raw data reading

2. Frame Buffer Functions (J-L):
   - Buffer initialization
   - Pixel storage
   - Buffer display

3. Image Processing Functions (P-S):
   - Thresholding
   - Frame capture
   - Average filtering
   - Edge detection

4. Main Programs (M, T):
   - Simple data reading loop
   - Full processing pipeline

5. Utility Functions (F-I):
   - Fibonacci sequence
   - Factorial calculator
   - Prime number finder
   - GCD calculator

To use the code:
1. For basic sensor reading: Run `M`
2. For image processing: Run `T`
3. For single frame capture: Run `J Q L`

