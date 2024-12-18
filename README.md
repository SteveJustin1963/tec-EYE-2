# tec-EYE-2


![image](https://github.com/SteveJustin1963/tec-EYE-2/assets/58069246/bf1b8a80-b41d-4577-814f-2461f2c01500)

![image](https://github.com/SteveJustin1963/tec-EYE-2/assets/58069246/c03e3b7c-72e4-4fed-9917-c4514ebec4fe)


get an image from PMW3360DM and send it out on uart 4800


ardunio sketch
`te2.ino`

```
START
- Initialize SPI communication
- Set SPI data mode and clock divider
- Configure chip select pin as OUTPUT
- Deselect image sensor

LOOP:
- Declare and initialize the receive buffer for image data
- Call get_image_data function to retrieve image data
- Print the received image data on the Serial monitor
- Add a delay of 1 second

END
```

## summary of each line and its purpose:

- `#include <SPI.h>`: Includes the SPI library for SPI communication.
- `const int CS_PIN = 10;`: Declares a constant integer variable to hold the chip select pin number.
- `void setup() {`: Begins the definition of the setup function.
- `SPI.begin();`: Initializes SPI communication.
- `SPI.setDataMode(SPI_MODE3);`: Sets the SPI data mode to mode 3 (clock normally high, data captured on falling edge).
- `SPI.setClockDivider(SPI_CLOCK_DIV16);`: Sets the SPI clock speed to one-sixteenth of the system clock frequency.
- `pinMode(CS_PIN, OUTPUT);`: Configures the chip select pin as an output.
- `digitalWrite(CS_PIN, HIGH);`: Deselects the image sensor by setting the chip select pin to a high logic level.
- `Serial.begin(4800);`: Initializes the Serial communication at a baud rate of 4800.
- `void get_image_data(uint8_t *rx_buf) {`: Defines the function `get_image_data` that takes a pointer to an array as a parameter.
- `digitalWrite(CS_PIN, LOW);`: Selects the image sensor by setting the chip select pin to a low logic level.
- `SPI.transfer(0x42);`: Sends a command (0x42) to the image sensor via SPI communication.
- `for (int i = 0; i < 3; i++) { rx_buf[i] = SPI.transfer(0xFF); }`: Receives three bytes of image data from the image sensor and stores them in the `rx_buf` array.
- `digitalWrite(CS_PIN, HIGH);`: Deselects the image sensor by setting the chip select pin to a high logic level.
- `void loop() {`: Begins the definition of the loop function.
- `uint8_t rx_buf[3] = {0x00, 0x00, 0x00};`: Declares an array to store the received image data and initializes it with three zeros.
- `get_image_data(rx_buf);`: Calls the `get_image_data` function to retrieve image data and store it in the `rx_buf` array.
- Serial print statements: Prints the received image data as hexadecimal values to the Serial monitor.
- `delay(1000);`: Adds a delay of 1 second before the next iteration of the loop function.


 
