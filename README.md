# Digital Timer
 
Include your responses to the bold questions below. Include snippets of code that explain what you did. Deliverables are due next Tuesday. Post your lab reports as README.md pages on your GitHub, and post a link to that on your main class hub page.

## Part A. Solder your LCD panel

**Take a picture of your soldered panel and add it here!**
![soldering](https://github.com/ziggydale45/IDD-Fa19-Lab2/blob/master/IMG_0392.JPG)

## Part B. Writing to the LCD
 
**a. What voltage level do you need to power your display?**
5V

**b. What voltage level do you need to power the display backlight?**
3V
   
**c. What was one mistake you made when wiring up the display? How did you fix it?**
My wires were really messy, so it became difficult to track what went were. I ended up redoing my wiring so that each wire was color coded and easier to track.
**d. What line of code do you need to change to make it flash your name instead of "Hello World"?**
`lcd.print("hello, world!");` to `lcd.print("hello, Jesse!");`
 
**e. Include a copy of your Lowly Multimeter code in your lab write-up.**
```/*
  LiquidCrystal Library - Hello World

 Demonstrates the use a 16x2 LCD display.  The LiquidCrystal
 library works with all LCD displays that are compatible with the
 Hitachi HD44780 driver. There are many of them out there, and you
 can usually tell them by the 16-pin interface.

 This sketch prints "Hello World!" to the LCD
 and shows the time.

  The circuit:
 * LCD RS pin to digital pin 12
 * LCD Enable pin to digital pin 11
 * LCD D4 pin to digital pin 5
 * LCD D5 pin to digital pin 4
 * LCD D6 pin to digital pin 3
 * LCD D7 pin to digital pin 2
 * LCD R/W pin to ground
 * LCD VSS pin to ground
 * LCD VCC pin to 5V
 * 10K resistor:
 * ends to +5V and ground
 * wiper to LCD VO pin (pin 3)

 Library originally added 18 Apr 2008
 by David A. Mellis
 library modified 5 Jul 2009
 by Limor Fried (http://www.ladyada.net)
 example added 9 Jul 2009
 by Tom Igoe
 modified 22 Nov 2010
 by Tom Igoe
 modified 7 Nov 2016
 by Arturo Guadalupi

 This example code is in the public domain.

 http://www.arduino.cc/en/Tutorial/LiquidCrystalHelloWorld

*/

// include the library code:
#include <LiquidCrystal.h>

// initialize the library by associating any needed LCD interface pin
// with the arduino pin number it is connected to
const int rs = 12, en = 11, d4 = 5, d5 = 4, d6 = 3, d7 = 2;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

//Analog values
int sensorPin = A0;    // select the input pin for the potentiometer
int sensorValue = 0;  // variable to store the value coming from the sensor

void setup() {
  // set up the LCD's number of columns and rows:
  lcd.begin(16, 2);

  // Turn on the display:
  lcd.display();
  // Print a message to the LCD.
  lcd.print("Mike is stinky!");
}

void loop() {
  sensorValue = analogRead(sensorPin);
  lcd.clear();
  lcd.print(sensorValue);
  delay(500);
}
```


## Part C. Using a time-based digital sensor

**Upload a video of your working rotary encoder here.**
https://www.youtube.com/watch?v=Ek7LX42Rp0s


## Part D. Make your Arduino sing!

**a. How would you change the code to make the song play twice as fast?**
 `int pauseBetweenNotes = noteDuration * .5;`

**b. What song is playing?**
Star Wars!


## Part E. Make your own timer

**a. Make a short video showing how your timer works, and what happens when time is up!**
https://www.youtube.com/watch?v=wof22MpvAXM


Code:
```/*
  LiquidCrystal Library - Hello World
 Demonstrates the use a 16x2 LCD display.  The LiquidCrystal
 library works with all LCD displays that are compatible with the
 Hitachi HD44780 driver. There are many of them out there, and you
 can usually tell them by the 16-pin interface.
 This sketch prints "Hello World!" to the LCD
 and shows the time.
  The circuit:
 * LCD RS pin to digital pin 12
 * LCD Enable pin to digital pin 11
 * LCD D4 pin to digital pin 5
 * LCD D5 pin to digital pin 4
 * LCD D6 pin to digital pin 3
 * LCD D7 pin to digital pin 2
 * LCD R/W pin to ground
 * LCD VSS pin to ground
 * LCD VCC pin to 5V
 * 10K resistor:
 * ends to +5V and ground
 * wiper to LCD VO pin (pin 3)
 Library originally added 18 Apr 2008
 by David A. Mellis
 library modified 5 Jul 2009
 by Limor Fried (http://www.ladyada.net)
 example added 9 Jul 2009
 by Tom Igoe
 modified 22 Nov 2010
 by Tom Igoe
 modified 7 Nov 2016
 by Arturo Guadalupi
 This example code is in the public domain.
 http://www.arduino.cc/en/Tutorial/LiquidCrystalHelloWorld
*/

//Button setup
// this constant won't change:
const int  buttonPin = 10;    // the pin that the pushbutton is attached to
const int ledPin = 13;       // the pin that the LED is attached to

// Variables will change:
int buttonPushCounter = 0;   // counter for the number of button presses
int buttonState = 0;         // current state of the button
int lastButtonState = 0;     // previous state of the button
int timeStart = 0;
int elapsedTime = 0;
float remainingTime = 0.0;
float runtime = 10.0;

boolean timerOn;
boolean initialDisplay;


// include the library code:
#include <LiquidCrystal.h>
#include "pitches.h"

// notes in the melody:
int melody[] = {
  NOTE_C4, NOTE_G3, NOTE_G3, NOTE_A3, NOTE_G3, 0, NOTE_B3, NOTE_C4};
 
int noteDurations[] = {
   4, 8, 8, 4, 4, 4, 4, 4};

// initialize the library by associating any needed LCD interface pin
// with the arduino pin number it is connected to
const int rs = 12, en = 11, d4 = 5, d5 = 4, d6 = 3, d7 = 2;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

void setup() {
  lcd.begin(16, 2);
  timerOn = false;
  pinMode(buttonPin, INPUT);
  Serial.begin(9600);
  initialDisplay = true;
  
}

void loop() {

  // read the pushbutton input pin:
  buttonState = digitalRead(buttonPin);

  // compare the buttonState to its previous state
  if (buttonState != lastButtonState) {
    // if the state has changed, increment the counter
    if (buttonState == HIGH) {
      // if the current state is HIGH then the button went from off to on:
      buttonPushCounter++;
      
      timerOn = true;
      timeStart = millis();
      lcd.clear();
      
      Serial.println("on");
      Serial.print("number of button pushes: ");
      Serial.println(buttonPushCounter);
    } else {
      // if the current state is LOW then the button went from on to off:
      Serial.println("off");
    }
  }
  // save the current state as the last state, for next time through the loop
  lastButtonState = buttonState;

  if (timerOn == true) {
    elapsedTime = (millis() - timeStart)/1000;
    lcd.setCursor(0,0);
    remainingTime = runtime-elapsedTime;
    lcd.print( remainingTime, 1 ) ;
    lcd.setCursor(0,1);
    lcd.print("RUN!!");  
  }
    
  if (remainingTime < 0 && timerOn == true) {
    lcd.clear();
    lcd.print(0);
    lcd.setCursor(0,1);
    lcd.print("Out of time!");
    timerOn = false;

    for (int thisNote = 0; thisNote < 20; thisNote++) {
    // to calculate the note duration, take one second divided by the note type.
    //e.g. quarter note = 1000 / 4, eighth note = 1000/8, etc.
    int noteDuration = (1000 / noteDurations[thisNote]);
    tone(8, melody[thisNote], noteDuration);
    
    // to distinguish the notes, set a minimum time between them.
    // the note's duration + 30% seems to work well:
    int pauseBetweenNotes = noteDuration * 1.30;
    delay(pauseBetweenNotes);
    }
    
    
  }

  if (initialDisplay == true) {
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("Push button");
    lcd.setCursor(0,1);
    lcd.print("To start timer");
    initialDisplay = false;
  }
  
}
```

**b. Post a link to the completed lab report your class hub GitHub repo.**
