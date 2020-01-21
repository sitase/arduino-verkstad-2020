# Arduino-verkstad

## JFokus kidz 2020

Vi ska bygga en robot! En robot är en dator som kan röra på sig. Vi behöver
kunna koppla ihop datorn med en motor och någon slags känselspröt så att den
kan reagera på sin omgivning. "Reagera" betyder att den behöver ett datorprogram
som bestämmer vad den ska göra om den ser ett hinder.

## komponentlista
* Arduino Uno R3
* USB B-A kabel
* Kopplingsplatta (breadboard)
* Kopplingskablar
* 3 lysdioder (röd, grön, gul)
* fotoresistor
* buzzer
* tryckknapp
* ultraljudsavståndsmätare
* USB powerbank
* motstånd 220 ohm
* roboten:
  * två likströmsmotorer med växellåda
  * två hjul
  * ett noshjul
  * chassi, skruvor och mässingsdistanser
* motorkontroller VMA409 (L298N)

## annan utrustning som behövs

* elektronikmejslar

## utvecklingsmiljön

I första hand använder vi den webbaserade editorn på https://create.arduino.cc. Du behöver skapa ett konto där, samt installera ett
hjälpprogram som låter webbrowsern prata med arduino-datorn.

## vad är det här?



### blinka en lampa

![Krets med arduino, breadboard och lysdiod][diodknapp]

[diod]: kretsar/diod-knapp.png "Krets med arduino, breadboard och lysdiod"

```ino
#define RED 2

void setup() {

}

void loop() {
  digitalWrite(RED,HIGH);
  delay(100);
  digitalWrite(RED,LOW);
  delay(100);
}

```

### är knappen intryckt?

![Krets med arduino, breadboard, lysdiod och knapp][diod]

[diodknapp]: kretsar/blinka-diod_bb.png "Krets med arduino, breadboard, lysdiod och knapp"

```ino
#define RED 2
#define BUTTON 4

int button = LOW;

void setup() {

}

void loop() {
  button=digitalRead(BUTTON);
  if(button==HIGH){
    digitalWrite(RED,HIGH);
    delay(100);
    digitalWrite(RED,LOW);
    delay(100);
  } else {
    digitalWrite(BLUE,HIGH);
    delay(100);
    digitalWrite(BLUE,LOW);
    delay(100);
  }
}

```
## hur ljust är det?

## tänd en lampa om det är mörkt

## spela en stege när vi trycker på knappen

![Krets med arduino, breadboard, knapp och buzzer][buzzer]

[buzzer]: kretsar/buzzer.png "Krets med arduino, breadboard, knapp och buzzer"


## motor och avståndsmätare: vi bygger en robot


![Krets med arduino, breadboard, avståndsmätare och motorer][motorer]

[motorer]: kretsar/motor-bil-sensor.png "Krets med arduino, breadboard, avståndsmätare och motorer"
