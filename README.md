#include <Adafruit_NeoPixel.h>
#ifdef __AVR__
#include <avr/power.h> 
#endif

#define BUTTON_PIN   2
#define PIXEL_PIN    6  
#define PIN 5

Adafruit_NeoPixel strip0(70, PIXEL_PIN, NEO_GRB + NEO_KHZ800);
Adafruit_NeoPixel strip1(6, PIN, NEO_GRB + NEO_KHZ800);

boolean oldState = HIGH;
int     mode     = 0;  

const int sensorPin = A0;
const int onePin = A1;
const int twoPin = A2;
const int threePin = A3;
const int ledPin = 3;
const int threshold = 300;

void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  pinMode(ledPin, OUTPUT);
  strip0.begin(); 
  strip0.show();  
  strip1.begin(); 
  strip1.show(); 

  Serial.begin(9600);
}

void loop() {
                                                       //Button and Neopixel strip surrounding box
  boolean newState = digitalRead(BUTTON_PIN);

  if((newState == LOW) && (oldState == HIGH)) {
      newState = digitalRead(BUTTON_PIN);
        Serial.println("BUTTON");
    if(newState == LOW) {      
      if(++mode > 4) mode = 0; 
      switch(mode) {     
        Serial.println("click!");      
        case 0:
          colorWipe(strip0.Color(  0,   0,   0), 50);    // off
          break;
        case 1:
          colorWipe(strip0.Color(  200,   0,   255), 50);   // Purple
          break;
        case 2:
          colorWipe(strip0.Color(  100, 255,   100), 50);   // Green
          break;
        case 3:
          colorWipe(strip0.Color(  50,   50, 255), 50);    // Blue/White
          break;
        case 4:
          rainbowCycle(0);
          break;
        case 5:
          colorWipe(strip0.Color(  0,   0,   0), 50);    // off
          break;
      }
    }
    delay(0);
  }

  oldState = newState;
                                                      //Sensor Pin and LEDs on top of house
int val= analogRead(sensorPin);
  Serial.print(val);
if  (val < threshold)
{
digitalWrite(ledPin, HIGH);
  Serial.println("                                          Knock!");
  delay(2000);
}
else
if  (val > threshold) {
digitalWrite(ledPin, LOW);
  Serial.println("                                          Go away!");
 }
                                                     //Photo-sensors and Neopixel lighting trees
  int oneVal = analogRead(A1); //Red
      Serial.print("Sensor 1:");
    Serial.println(oneVal);
  int twoVal = analogRead(A2); //Green
      Serial.print("             Sensor 2:");
    Serial.println(twoVal);
  int threeVal = analogRead(A3); //Blue
      Serial.print("                          Sensor 3:");
    Serial.println(threeVal);
 
       if(oneVal <= 200){
    strip1.setPixelColor(0, 255, 0, 0); //Red
    strip1.setPixelColor(1, 255, 0, 0);
    strip1.show();
  }
  else if(twoVal <= 200){
    strip1.setPixelColor(2, 0, 255, 0); //Green
    strip1.setPixelColor(3, 0, 255, 0);
    strip1.show();
  }
  else if(threeVal <= 200){
    strip1.setPixelColor(4, 0, 0, 255); //Blue
    strip1.setPixelColor(5, 0, 0, 255);
    strip1.show();
  }
  else {
    strip1.setPixelColor(0, 0, 0, 0);
    strip1.setPixelColor(1, 0, 0, 0);
    strip1.setPixelColor(2, 0, 0, 0);
    strip1.setPixelColor(3, 0, 0, 0);
    strip1.setPixelColor(4, 0, 0, 0);
    strip1.setPixelColor(5, 0, 0, 0);
    strip1.show();
  } 
}

void rainbowCycle(int SpeedDelay) {
  byte *c;
  uint16_t i, j;
for(j=0; j<256*5; j++) { // 5 cycles of all colors on wheel
    for(i=0; i< 70; i++) {
      c=Wheel(((i * 256 / 70) + j) & 255);
      strip0.setPixelColor(i, *c, *(c+1), *(c+2));
    }
    strip0.show();
    delay(SpeedDelay);
  }
}
byte * Wheel(byte WheelPos) {
  static byte c[3];
 
  if(WheelPos < 85) {
   c[0]=WheelPos * 3;
   c[1]=255 - WheelPos * 3;
   c[2]=0;
  } else if(WheelPos < 170) {
   WheelPos -= 85;
   c[0]=255 - WheelPos * 3;
   c[1]=0;
   c[2]=WheelPos * 3;
  } else {
   WheelPos -= 170;
   c[0]=0;
   c[1]=WheelPos * 3;
   c[2]=255 - WheelPos * 3;
  }
  return c;
}

void colorWipe(uint32_t color, int wait) {
  for(int i=0; i<strip0.numPixels(); i++) { // For each pixel in strip...
    strip0.setPixelColor(i, color);         //  Set pixel's color (in RAM)
    strip0.show();                          //  Update strip to match
    delay(wait);                           //  Pause for a moment
  }
}
