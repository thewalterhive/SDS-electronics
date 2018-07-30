# SDS-electronics
Arduino tutorials for Summer Design Series, 2018

# Blink
```
const int LED = 3;

void setup() {
  pinMode(LED, OUTPUT);
}

void loop() {
  digitalWrite(LED_BUILTIN, HIGH);
  delay(500);
  digitalWrite(LED_BUILTIN, LOW);
  delay(500);
}
```

# Button
```
const int buttonPin = 2;
const int LED = 3;
int buttonState = 0;

void setup() {
  pinMode(LED, OUTPUT);
  pinMode(buttonPin, INPUT_PULLUP);
}

void loop() {
  buttonState = digitalRead(buttonPin);
  if (buttonState == LOW) {
    digitalWrite(LED_BUILTIN, HIGH);
  } else {           
    digitalWrite(LED_BUILTIN, LOW);
  }
  delay(500);
}
```

# Light Strip
```
#include <Adafruit_NeoPixel.h>
#define PIN            8
#define NUMPIXELS      5
Adafruit_NeoPixel pixels = Adafruit_NeoPixel(NUMPIXELS, PIN, NEO_GRB + NEO_KHZ800);

void setup() {
  pixels.begin();
} 

void loop() {
  pixels.setPixelColor(0, 0, 100, 0);
  pixels.setPixelColor(1, 0, 100, 0);
  pixels.setPixelColor(2, 0, 0, 100);
  pixels.setPixelColor(3, 100, 0, 0);
  pixels.setPixelColor(4, 100, 0, 100);
  pixels.show();
  delay(500);
}
```

# Blinking Strand
```
#include <Adafruit_NeoPixel.h>
#define PIN            8
#define NUMPIXELS      5
Adafruit_NeoPixel pixels = Adafruit_NeoPixel(NUMPIXELS, PIN, NEO_GRB + NEO_KHZ800);

void setup() {
  pixels.begin();
}

void loop() {
  for(int i; i<NUMPIXELS; i++) {
    pixels.setPixelColor(i, 0, 100, 0);
  }
  pixels.show();
  delay(500);
  for(int i; i<NUMPIXELS; i++) {
    pixels.setPixelColor(i, 100, 0, 100);
  }
  pixels.show();
  delay(500);
}
```
