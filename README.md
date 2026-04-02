# 74HC595N--Shift-Register---Serial-to-Parallel-Conversion

Demonstrates serial in parallel out data conversion using a
74HC595 shift register driven by an Elegoo Mega R3.
8 LEDs display an 8-bit binary counter counting 0 to 255.

## Components
- Elegoo Mega R3
- 74HC595 shift register
- 8x LEDs
- 8x 220 ohm resistors
- Jumper wires
- Breadboard

## Wiring
DS    (pin 14) → Mega pin 11  — serial data
SH_CP (pin 11) → Mega pin 12  — shift clock
ST_CP (pin 12) → Mega pin 13  — latch clock
QA-QH (pins 15,1-7) → LEDs via 220 ohm resistors

## How It Works
The 74HC595 contains 8 chained D flip flops. Data enters
one bit at a time on DS and shifts through the chain on
each SH_CP clock pulse. After 8 pulses the full byte is
loaded. Pulsing ST_CP copies the shift register contents
to the output pins all at once preventing flicker during
data loading.

Only 3 wires from the Mega control 8 outputs. Chaining
multiple chips extends this to any number of outputs
while still using only 3 control wires.

## Concepts Demonstrated
- Serial to parallel data conversion
- D flip flop chains and clock driven data movement
- Latch register separating input from output
- Pin reduction through serial communication
- MSBFIRST vs LSBFIRST bit ordering

## Hardware Design Connection
SPI, UART, and I2C all use serial communication for the
same reason — fewer wires at the cost of speed. The
74HC595 is a physical implementation of the shift register
structures used in serial interface controllers. The clock
driven data movement here is identical to how all
synchronous digital circuits operate in chip design.
The UVM testbench for a UART controller verifies exactly
this serial to parallel conversion behavior.

## Images
![IMG_3780](https://github.com/user-attachments/assets/dbc8fe03-afb2-4464-90d9-cfa404fded23)
![IMG_3781](https://github.com/user-attachments/assets/f537d064-0c8b-4003-8dd7-6e70a6e7fda4)

## Code

int dataPin  = 11;
int clockPin = 12;
int latchPin = 13;

void setup() {
  pinMode(dataPin,  OUTPUT);
  pinMode(clockPin, OUTPUT);
  pinMode(latchPin, OUTPUT);
}

void shiftOutByte(byte val) {
  digitalWrite(latchPin, LOW);
  shiftOut(dataPin, clockPin, MSBFIRST, val);
  digitalWrite(latchPin, HIGH);
}

void loop() {
  for (int i = 0; i < 256; i++) {
    Serial.begin(9600);
    Serial.println(i);
    shiftOutByte(i);
    delay(100);
  }
}

