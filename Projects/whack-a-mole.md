![Imgur](https://i.imgur.com/ixe8tuT.jpg)

I scoured the internet for a DIY Wack a mole game and failed. I actually thought I could make one so I went ahead and did it. Check it out.

##### MATERIALS:
 - Open-Cell/ Urethane-Ether Foam
 - Copper clad board
 - Wire
 - Solder
 - Ardruino
 - ¼ birch plywood
 - Hot glue
 - Adafruit Neopixel strip
 - Wooden Dowel
 - Wood screws
 
#####TOOLS:
 - Laser cutter
 - Hot glue gun
 - Soldering iron
 - Drill
 
 #### THE PROCESS
 
I had some pause about using a mechanical solution to simulate moles. I figured I would donate the project to a preschool after it was done so I wanted to make it safe and easily serviceable.

#### THE MOLES:
The Moles have shelled out high-density foam with a piece of copper PCB glued to it. I then soldered a wire to the board. This could be compared to a basic tactile switch, in that a conductive plate hovers over another.

![Imgur](https://i.imgur.com/bCDTumo.png)

On the wood base under the foam is another copper pcb with a wire solder on. When the foam is depressed the 2 copper boards come in contact completing a circuit. The Arduino registers this as a button input.

I then needed and output to tell the player which mole to hit. In a traditional game the mole would pop-up. At first I had the idea to install LEDs to the top of the moles but they could possibly break so that was no longer an option. I then decided to post a Neopixel strip because RGB has at least 9 colors. That gave me the flexibility of design and simplicity to make the game portable.

![Imgur](https://i.imgur.com/XXd2es7.jpg)

#### THE CODE:

The code was simple randomly choose a color; Display it; wait for input; Display score; repeat.

The fun part was the randomizer. The idea is to use an analog pin in idle mode this makes it susceptible to static electricity. As it spits out “Random”  values I map them from 0 - 1023 to 1-9(# of moles).

```
#include <Adafruit_NeoPixel.h>
#include <LiquidCrystal_I2C.h>

// constants won't change. They're used here to
// set pin numbers:
const int neoLed = 4;
const int buttonPin1 = 13;     // the number of the pushbutton pin
const int buttonPin2 = 12;     // the number of the pushbutton pin
const int buttonPin3 = 11;     // the number of the pushbutton pin
const int buttonPin4 = 10;     // the number of the pushbutton pin
const int buttonPin5 = 9;     // the number of the pushbutton pin
const int buttonPin6 = 8;     // the number of the pushbutton pin
const int buttonPin7 = 7;     // the number of the pushbutton pin
const int buttonPin8 = 6;     // the number of the pushbutton pin

// colors 
int green[3] = {0,138,0};
int blue[3] = {0,80,239};
int yellow[3] = {227,200,0};
int red[3] = {229,20,0};
int purple[3] = {170,0,255};
int orange[3] = {250,104,0};
int teal[3] = {0,171,169};
int pink[3] = {244,114,208};
int off[3] = {0,0,0};

// variables will change:
int buttonState = 0; 
int buttonState1 = 0;         // variable for reading the pushbutton status
int buttonState2 = 0;         // variable for reading the pushbutton status
int buttonState3 = 0;         // variable for reading the pushbutton status
int buttonState4 = 0;         // variable for reading the pushbutton status
int buttonState5 = 0;         // variable for reading the pushbutton status
int buttonState6 = 0;         // variable for reading the pushbutton status
int buttonState7 = 0;         // variable for reading the pushbutton status
int buttonState8 = 0;         // variable for reading the pushbutton status

long moleUp;
int score = 0;
int wrong = 0;
int start = 1;
int lastButtonState = 0;

Adafruit_NeoPixel strip = Adafruit_NeoPixel(8, neoLed, NEO_GRB + NEO_KHZ800);
LiquidCrystal_I2C lcd(0x27,16,2);

void setup() {
  // initialize the pushbutton pin as an input:
  pinMode(buttonPin1, INPUT);
  pinMode(buttonPin2, INPUT);
  pinMode(buttonPin3, INPUT);
  pinMode(buttonPin4, INPUT);
  pinMode(buttonPin5, INPUT);
  pinMode(buttonPin6, INPUT);
  pinMode(buttonPin7, INPUT);
  pinMode(buttonPin8, INPUT);
  start = 1;
  lcd.init();
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0,0);
  lcd.print("WHACK THE MOLE!");

  lcd.setCursor(0,1);
  lcd.print("I Wont Tell");

  strip.begin();
  strip.setBrightness(100);
  strip.show();

  for(int k=0; k< strip.numPixels(); k++) {
    strip.setPixelColor(k, 255, 255, 255);
  }

  strip.show();

  Serial.begin(9600);
}

void changeColor(int setColor[]){
  // Set strip Colors
  for(int k=0; k< strip.numPixels(); k++) {
    strip.setPixelColor(k, setColor[0], setColor[1], setColor[2]);
  }
  strip.show();
}

void loop() {
  moleUp = random(1, 8);
  int TARGET = off;

  int wrongHIT1 = 0;
  int wrongHIT2 = 0;
  int wrongHIT3 = 0;
  int wrongHIT4 = 0;
  int wrongHIT5 = 0;
  int wrongHIT6 = 0;
  int wrongHIT7 = 0; 
  int HIT;

  if (moleUp == 1){
    TARGET = green;
    HIT = buttonPin1;
    wrongHIT1 = buttonPin2;
    wrongHIT2 = buttonPin3;
    wrongHIT3 = buttonPin4;
    wrongHIT4 = buttonPin5;
    wrongHIT5 = buttonPin6;
    wrongHIT6 = buttonPin7;
    wrongHIT7 = buttonPin8;
  }
  if (moleUp == 2){
    TARGET = red;
    HIT = buttonPin2;
    wrongHIT1 = buttonPin1;
    wrongHIT2 = buttonPin3;
    wrongHIT3 = buttonPin4;
    wrongHIT4 = buttonPin5;
    wrongHIT5 = buttonPin6;
    wrongHIT6 = buttonPin7;
    wrongHIT7 = buttonPin8;
  }
  if (moleUp == 3){
    TARGET = yellow;
    HIT = buttonPin3;
    wrongHIT1 = buttonPin2;
    wrongHIT2 = buttonPin1;
    wrongHIT3 = buttonPin4;
    wrongHIT4 = buttonPin5;
    wrongHIT5 = buttonPin6;
    wrongHIT6 = buttonPin7;
    wrongHIT7 = buttonPin8;
  }
  if (moleUp == 4){
    TARGET = pink;
    HIT = buttonPin4;
    wrongHIT1 = buttonPin2;
    wrongHIT2 = buttonPin3;
    wrongHIT3 = buttonPin1;
    wrongHIT4 = buttonPin5;
    wrongHIT5 = buttonPin6;
    wrongHIT6 = buttonPin7;
    wrongHIT7 = buttonPin8;
  }
  if (moleUp == 5){
    TARGET = purple;
    HIT = buttonPin5;
    wrongHIT1 = buttonPin2;
    wrongHIT2 = buttonPin3;
    wrongHIT3 = buttonPin4;
    wrongHIT4 = buttonPin1;
    wrongHIT5 = buttonPin6;
    wrongHIT6 = buttonPin7;
    wrongHIT7 = buttonPin8;
  }
  if (moleUp == 6){
    TARGET = blue;
    HIT = buttonPin6;
    wrongHIT1 = buttonPin2;
    wrongHIT2 = buttonPin3;
    wrongHIT3 = buttonPin4;
    wrongHIT4 = buttonPin5;
    wrongHIT5 = buttonPin1;
    wrongHIT6 = buttonPin7;
    wrongHIT7 = buttonPin8;
  }
  if (moleUp == 7){
    TARGET = orange;
    HIT = buttonPin7;
    wrongHIT1 = buttonPin2;
    wrongHIT2 = buttonPin3;
    wrongHIT3 = buttonPin4;
    wrongHIT4 = buttonPin5;
    wrongHIT5 = buttonPin6;
    wrongHIT6 = buttonPin1;
    wrongHIT7 = buttonPin8;
  }
  if (moleUp == 8){
    TARGET = teal;
    HIT = buttonPin8;
    wrongHIT1 = buttonPin2;
    wrongHIT2 = buttonPin3;
    wrongHIT3 = buttonPin4;
    wrongHIT4 = buttonPin5;
    wrongHIT5 = buttonPin6;
    wrongHIT6 = buttonPin7;
    wrongHIT7 = buttonPin1;
  }
  long var = 20000;

  while(var > 0){
    buttonState = digitalRead(HIT);
    changeColor(TARGET);
    if (digitalRead(wrongHIT1) == HIGH ||
        digitalRead(wrongHIT2) == HIGH ||
        digitalRead(wrongHIT3) == HIGH ||
        digitalRead(wrongHIT4) == HIGH ||
        digitalRead(wrongHIT5) == HIGH ||
        digitalRead(wrongHIT6) == HIGH ||
        digitalRead(wrongHIT7) == HIGH)  {
          wrong++;
          break;
    }
    if (buttonState == HIGH)  {
      score++;
      lcd.clear();
      lcd.setCursor(0,0);
       lcd.print("SCORE: ");
       lcd.setCursor(9,0);
       lcd.print(score);

       lcd.setCursor(0,1);
       lcd.print("WRONG: ");
       lcd.setCursor(9,1);
       lcd.print(wrong);
      break;
    }
    var--;
  }
  if (wrong == 0 && start == 0){
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("SCORE: ");
    lcd.setCursor(9,0);
    lcd.print(score);

    lcd.setCursor(0,1);
    lcd.print("WRONG: ");
    lcd.setCursor(9,1);
    lcd.print(wrong);
  }
  if (wrong > 1200){
    changeColor(red);
    delay(200);
    changeColor(off);
    delay(200);
    changeColor(red);
    delay(200);
    changeColor(off);
    delay(200);
    changeColor(red);
    delay(200);
    changeColor(off);
    delay(200);
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("GAME OVER !!!!!");

    lcd.setCursor(0,1);
    lcd.print("SCORE: ");
    lcd.setCursor(9,1);
    lcd.print(score);

    delay(3000);
    lcd.clear();
    wrong = 0;
    score = 0;
  }
  start = 0;
}

```
**This project was very FUN and all my co-workers had fun playing with it. One of them (Jess) actually painted it. It looks amazing.**
