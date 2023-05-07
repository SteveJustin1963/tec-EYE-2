/*


get an image from PMW3360DM and send it out on uart 4800


In this code, we use the Arduino SPI and Serial libraries. 
The SPI settings are configured in the setup function, and the chip select pin is set to pin 10. 
The Serial communication is initialized at 4800 baud rate. 
The loop function reads the image data using get_image_data and then sends it via the Serial connection.


Arduino Uno       SPI Device
-----------       ----------
     5V       ->     VCC
    GND       ->     GND
     D10      ->     CS
     D11      ->     MOSI
     D12      ->     MISO
     D13      ->     SCK
     
     Arduino Uno       UART Device
-----------       ----------
     GND       ->     GND
     D0        ->     TX
     D1        ->     RX
*/

 


#include <SPI.h>

// SPI chip select pin
const int CS_PIN = 10;

void setup() {
  // Configure SPI settings
  SPI.begin();
  SPI.setDataMode(SPI_MODE3);
  SPI.setClockDivider(SPI_CLOCK_DIV16);

  // Configure chip select pin as output
  pinMode(CS_PIN, OUTPUT);
  digitalWrite(CS_PIN, HIGH);

  // Initialize Serial communication at 4800 baud rate
  Serial.begin(4800);
}

void get_image_data(uint8_t *rx_buf) {
  // Assert chip select (active low)
  digitalWrite(CS_PIN, LOW);

  // Send read image command
  SPI.transfer(0x42);
  for (int i = 0; i < 3; i++) {
    rx_buf[i] = SPI.transfer(0xFF);
  }

  // Deassert chip select (active low)
  digitalWrite(CS_PIN, HIGH);
}

void loop() {
  uint8_t rx_buf[3] = {0x00, 0x00, 0x00}; // Receive buffer for image data

  // Get the image data
  get_image_data(rx_buf);

  // Send the received image data via Serial
  Serial.print("Image data: ");
  Serial.print(rx_buf[0], HEX);
  Serial.print(" ");
  Serial.print(rx_buf[1], HEX);
  Serial.print(" ");
  Serial.println(rx_buf[2], HEX);

  // Add a delay between image data reads (optional)
  delay(1000);
}
