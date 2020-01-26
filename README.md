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



### blinka en lampa -- UT

Det första vi testar är om vi kan blinka en lysdiod med hjälp av datorn.

![Krets med arduino, breadboard och lysdiod][diodknapp]

[diod]: kretsar/diod-knapp.png "Krets med arduino, breadboard och lysdiod"

Ett arduino-program består av två delar: En del som sätter i ordning saker
när datorn startas; ```setup()```, och en slinga som körs om och om igen; `loop()`.

För ändra spänningen på en pinne skriver vi till den: `digitalWrite(_pinne_, _värde_)`. För att göra det lite
enklare för oss att läsa vår egen kod kallar vi pinnen för RED (#define).

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


### är knappen intryckt? -- IN

När knappen är öppen så kommer pinnen att vara kopplad via knappen och resistorn till
`GND`, och alltså läsas ut `LOW`. När knappen stängs så kommer den vara kopplad till `5V`. Den är samtidigt
kopplad till `GND`, men eftersom det är ett motstånd emellan så kommer `5V` att lyfta upp den.

I programmet så vill vi läsa om pinnen för knappen (som vi kallar `BUTTON`) är `HIGH` eller `LOW`. Vi sparar svaret i en variabel (en låda) som vi kallar `button`.
Om `button` är `HIGH` så tänder vi lampan, väntar, släcker den, väntar. Om den är `LOW` låter vi lampan vara.

![Krets med arduino, breadboard, lysdiod och knapp][diodknapp]

[diodknapp]: kretsar/diod-knapp.png "Krets med arduino, breadboard, lysdiod och knapp"

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
    delay(200);
  }
}
```
Kan vi blinka med flera lysdioder?

## hur ljust är det?

En fotoresistor är ett motstånd vars motstånd beror på hur ljust det är. Om vi har flera
resistanser i serie så kommer spänningen vi lägger på att fördela sig i proportion till
resistanserna. Alltså, om vi har två stycken 220 Ω-motstånd i serie och lägger på 5 V spänning
så kommer 2,5 V att ligga över vardera. Det kallas spänningsdelning och det kan vi använda för
att för att jämför motståndet hos fotoresistorn med en känd resistans.



![Krets med arduino, breadboard, lysdiod och fotoresistor][fotoresistor]

[fotoresistor]: kretsar/fotoresistor-diod.png "Krets med arduino, breadboard, fotoresistor och knapp"

`digitalRead` läser bara ut om en pinne är `HIGH` eller `LOW`. Om vi vill läsa utan en kontinuerlig skala (0 till 1023)
så använder vi `analogRead`. Vi måste också använda en analog pinne.

Vi kan skriva saker på "konsolen" (`Serial`). Det är jättepraktiskt om vi till exempel vill veta
vilka värden som vi läser ut från fotoresistorn. För att se konsolen, välj "Monitor" i utvecklingsmiljö.


```ino
#define RED 5
#define PHOTO 0
int light = 0;
void setup() {
   Serial.begin (9600);
   pinMode(RED, OUTPUT);
}

void  loop(){
        light = analogRead(PHOTO); // high values for bright environment
        Serial.println(light, DEC);

        if(light>250){
          digitalWrite(YELLOW,HIGH);
        } else {
          digitalWrite(YELLOW,LOW);
        }
}
```

## spela en stege när vi trycker på knappen



![Krets med arduino, breadboard, knapp och buzzer][buzzer]

[buzzer]: kretsar/buzzer.png "Krets med arduino, breadboard, knapp och buzzer"

```ino
#define BUTTON 2
#define BUZZER 8

int button = LOW;
int note=440;
double step=1.0594630943592952646;
void setup() {
   Serial.begin (9600);
   pinMode(BUTTON, INPUT);
}

void  loop(){
        button=digitalRead(BUTTON);
        if(button  == HIGH){
          tone(BUZZER,note);
          note=note*step;
        }else{
          noTone(BUZZER);
          note=440;
        }
        delay(1000);
}
```

Om man har flera knappar, kan man bygga ett piano då?

Lite knepigare: Kan man göra olika saker varje gång man trycker på knappen?

## motor och avståndsmätare: vi bygger en robot


![Krets med arduino, breadboard, avståndsmätare och motorer][motorer]

[motorer]: kretsar/motor-bil-sensor.png "Krets med arduino, breadboard, avståndsmätare och motorer"
