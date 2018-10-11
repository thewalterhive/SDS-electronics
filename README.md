# SDS-electronics
Arduino tutorials for Summer Design Series, 2018

# Blink
```
const int LED = 3;

void setup() {
  pinMode(LED, OUTPUT);
}

void loop() {
  digitalWrite(LED, HIGH);
  delay(500);
  digitalWrite(LED, LOW);
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
    digitalWrite(LED, HIGH);
  } else {           
    digitalWrite(LED, LOW);
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
# Audio Test
```
#include <SoftwareSerial.h>
#define ARDUINO_RX 5//should connect to TX of the Serial MP3 Player module
#define ARDUINO_TX 6//connect to RX of the module
SoftwareSerial mySerial(ARDUINO_RX, ARDUINO_TX);
static int8_t Send_buf[8] = {0} ;
const int buttonPin = 2;
int buttonState = 0;
int buttonStateOld = 0;
#define CMD_SEL_DEV 0X09
#define DEV_TF 0X02
#define CMD_PLAY_W_VOL 0X22
#define CMD_PLAY 0X0D
#define CMD_PAUSE 0X0E
#define CMD_PREVIOUS 0X02
#define CMD_NEXT 0X01

void setup() {
  mySerial.begin(9600);
  Serial.begin(9600);
  delay(500);//Wait chip initialization is complete
  sendCommand(CMD_SEL_DEV, DEV_TF);//select the TF card  
  pinMode(buttonPin, INPUT_PULLUP);
}

void loop() {
  buttonState = digitalRead(buttonPin);
  
  if (buttonState == LOW) {
    if (buttonStateOld == HIGH) {
      int16_t songs[] = {0X1E01, 0X1E02, 0X1E03, 0X1E04, 0X1E05, 0X1E06};
      int number = random (0, 6);
      Serial.println(songs[number]);
      sendCommand(CMD_PLAY_W_VOL, songs[number+1]);
      buttonStateOld = digitalRead(buttonPin);
      delay(2000);
    } else {
      Serial.println("continue");
      Serial.println(buttonStateOld);
      delay(2000);
    }
  } else {
    sendCommand(CMD_PAUSE, 0X0E);//pause the playing track
    buttonStateOld = digitalRead(buttonPin);
    Serial.println("nope");
    Serial.println(buttonStateOld);
    delay(500);
  }
}

void sendCommand(int8_t command, int16_t dat) {
  delay(20);
  Send_buf[0] = 0x7e; //starting byte
  Send_buf[1] = 0xff; //version
  Send_buf[2] = 0x06; //the number of bytes of the command without starting byte and ending byte
  Send_buf[3] = command; //
  Send_buf[4] = 0x00;//0x00 = no feedback, 0x01 = feedback
  Send_buf[5] = (int8_t)(dat >> 8);//datah
  Send_buf[6] = (int8_t)(dat); //datal
  Send_buf[7] = 0xef; //ending byte
  for(uint8_t i=0; i<8; i++)//
  {
    mySerial.write(Send_buf[i]) ;
  }
}
```

#Rainbow rampup

```
const int buttonPin = 2;
int buttonState = 0;
int buttonStateOld = 0;
#include <Adafruit_NeoPixel.h>
#ifdef __AVR__
  #include <avr/power.h>
#endif

#define LOVE 8
#define LOVE_LED 5
#define ON 6
#define ON_LED 5

Adafruit_NeoPixel love = Adafruit_NeoPixel(LOVE_LED, LOVE, NEO_GRB + NEO_KHZ800);
Adafruit_NeoPixel on = Adafruit_NeoPixel(ON_LED, ON, NEO_GRB + NEO_KHZ800);

void setup() {
  pinMode(buttonPin, INPUT_PULLUP);
  love.begin();
  love.show();
  on.begin();
  on.show();
}

void loop() {
  // put your main code here, to run repeatedly:
  buttonState = digitalRead(buttonPin);
  
  if (buttonState == LOW) {
    if (buttonStateOld == HIGH) {
      flashWhite();
      buttonStateOld = buttonState;
    } else {
      solidWhite();
      rainbowCycle(5);
      delay(2000);
    }
  } else {
    buttonStateOld = digitalRead(buttonPin);
    pinkout();
    blackout();
  }
}

void flashWhite() {
  for(int i=0; i<6; i++) {
    for(int p=0; p<ON_LED; p++) {
      on.setPixelColor(p, 150, 150, 150);
    }
    on.show();
    delay(250);
    blackout();
    delay(250);
  }
}

void solidWhite() {
    for(int i=0; i<ON_LED; i++) {
    on.setPixelColor(i, 255, 255, 255);
  }
  on.show();
}

void pinkout() {
  for(int i=0; i<LOVE_LED; i++) {
    love.setPixelColor(i, 50, 50, 50);
  }
  love.show();
}

void blackout() {
  for(int i=0; i<ON_LED; i++) {
    on.setPixelColor(i, 0, 0, 0);
  }
  on.show();
}

uint32_t Wheel(byte WheelPos) {
  WheelPos = 255 - WheelPos;
  if(WheelPos < 85) {
    return love.Color(255 - WheelPos * 3, 0, WheelPos * 3);
  }
  if(WheelPos < 170) {
    WheelPos -= 85;
    return love.Color(0, WheelPos * 3, 255 - WheelPos * 3);
  }
  WheelPos -= 170;
  return love.Color(WheelPos * 3, 255 - WheelPos * 3, 0);
}

void rainbowCycle(uint8_t wait) {
  uint16_t i, j;

  for(j=0; j<256*5; j++) { // 5 cycles of all colors on wheel
    for(i=0; i< love.numPixels(); i++) {
      love.setPixelColor(i, Wheel(((i * 256 / love.numPixels()) + j) & 255));
    }
    love.show();
    delay(wait);
  }
}
```
