# Arduino-verkstad

Vi ska bygga en robot! En robot är en dator som kan röra på sig. Vi behöver
kunna koppla ihop datorn med en motor och någon slags känselspröt så att den
kan reagera på sin omgivning. "Reagera" betyder att den behöver ett datorprogram
som bestämmer vad den ska göra om den ser ett hinder.

## komponentlista
* Arduino Uno R3
* USB B-A-kabel
  * (vissa arduinos har USB mini, då är det en USB mini-A-kabel, inte alla kablar har databanor utan är bara laddkablar, så var uppmärksam på det om du inte kan prata med din arduino)
* Kopplingsplatta (breadboard)
* Kopplingskablar, 20 ha-ha, 10 ha-ho
* 3 lysdioder (röd, grön, gul)
* fotoresistor
* buzzer
* tryckknapp
* ultraljudsavståndsmätare
* USB powerbank
* motstånd 220 ohm, 2st (röd röd brun)
* roboten:
  * två likströmsmotorer med växellåda
  * två hjul
  * ett noshjul
  * chassi, skruvor och mässingsdistanser
* motorkontroller VMA409 (L298N)

## annan utrustning som behövs

* elektronikmejslar
* USB-C-A-adapter om din dator inte har USB-A

## utvecklingsmiljön

I första hand använder vi den webbaserade editorn på https://create.arduino.cc. Du behöver skapa ett konto där, samt installera ett
hjälpprogram som låter webbrowsern prata med arduino-datorn.

För de som kör MacOS Ventura så installerar man hjälpprogrammet härifrån: https://github.com/arduino/arduino-create-agent/releases/tag/1.6.1


## vad är det här?



### blinka en lampa -- UT

Det första vi testar är om vi kan blinka en lysdiod med hjälp av datorn.

![Krets med arduino, breadboard och lysdiod][diodknapp]

[diod]: kretsar/blinka-diod_bb.png "Krets med arduino, breadboard och lysdiod"

Ett arduino-program består av två delar: En del som sätter i ordning saker
när datorn startas; ```setup()```, och en slinga som körs om och om igen; `loop()`.

För ändra spänningen på en pinne skriver vi till den: `digitalWrite(_pinne_, _värde_)`. För att göra det lite
enklare för oss att läsa vår egen kod kallar vi pinnen för RED (#define). Pinnar kan vara input (vi kan läsa från den) eller output (skriva till den). Alla pinnar är normalt input, så för att tala om att vi vill skriva till den så gör vi `pinMode(RED,OUTPUT)` i `setup()`.


```ino
#define RED 2

void setup() {
  pinMode(RED,OUTPUT);
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

![knapp][knapp]

[knapp]: kretsar/button-circuit.png "Kretsschema för knapp"

I programmet så vill vi läsa om pinnen för knappen (som vi kallar `BUTTON`) är `HIGH` eller `LOW`. Vi sparar svaret i en variabel (en låda) som vi kallar `button`.
Om `button` är `HIGH` så tänder vi lampan, väntar, släcker den, väntar. Om den är `LOW` låter vi lampan vara.

![Krets med arduino, breadboard, lysdiod och knapp][diodknapp]

[diodknapp]: kretsar/diod-knapp.png "Krets med arduino, breadboard, lysdiod och knapp"

```ino
#define RED 2
#define BUTTON 4

int button = LOW;

void setup() {
  pinMode(RED,OUTPUT);
  pinMode(BUTTON,INPUT);
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
resistanserna. Alltså, om vi har två stycken 220 Ω-motstånd i serie och lägger på 5 V spänning
så kommer 2,5 V att ligga över vardera. Det kallas spänningsdelning och det kan vi använda för
att för att jämför motståndet hos fotoresistorn med en känd resistans.



![Krets med arduino, breadboard, lysdiod och fotoresistor][fotoresistor]

[fotoresistor]: kretsar/fotoresistor-diod.png "Krets med arduino, breadboard, fotoresistor"

`digitalRead` läser bara ut om en pinne är `HIGH` eller `LOW`. Om vi vill läsa utan en kontinuerlig skala (0 till 1023)
så använder vi `analogRead`. Vi måste också använda en analog pinne.

I den fotoresistor-modul som de flesta har fått så finns både själva fotoresistorn och motståndet i. Om vi kopplar in den enligt
nedan så är det samma krets, fast vi har krymt ihop två komponenter på en modul. (På den skissen finns inte lysdioden med, men den kopplas på samma sätt som ovan)

![Krets med arduino, breadboard, lysdiod och fotoresistormodul][fotoresistormodul]

[fotoresistormodul]: kretsar/fotoresistor-modul.png "Krets med arduino, breadboard, fotoresistormodul"

Vi kan skriva saker på "konsolen" (`Serial`). Det är jättepraktiskt om vi till exempel vill veta
vilka värden som vi läser ut från fotoresistorn. För att se konsolen, välj "Monitor" i utvecklingsmiljö.


```ino
#define RED 2
#define PHOTO 0

int light = 0;

void setup() {
   Serial.begin (9600);
   pinMode(RED, OUTPUT);
}

void loop() {
  light = analogRead(PHOTO); // high values for bright environment
  Serial.println(light, DEC);

  if(light>500){
    digitalWrite(RED,HIGH);
  } else {
    digitalWrite(RED,LOW);
  }
}
```

## spela en stege när vi trycker på knappen

_Överkurs, hoppa över om du känner att du har lite ont med tid.__

![Krets med arduino, breadboard, knapp och buzzer][buzzer]

[buzzer]: kretsar/buzzer.png "Krets med arduino, breadboard, knapp och buzzer"

```ino
#define BUTTON 4
#define BUZZER 3

int button = LOW;
int note=440;
double step=1.0594630943592952646;
void setup() {
   Serial.begin (9600);
   pinMode(BUTTON, INPUT);
}

void loop() {
  button=digitalRead(BUTTON);
  if(button  == HIGH) {
    tone(BUZZER,note);
    note=note*step;
  } else {
    noTone(BUZZER);
    note=440;
  }
  delay(1000);
}
```

Om man har flera knappar, kan man bygga ett piano då?

☞ [defines för noter](./pitches.h "defines för noter")

Lite knepigare: Kan man göra olika saker varje gång man trycker på knappen?

## motor och avståndsmätare: vi bygger en robot


![Krets med arduino, breadboard, avståndsmätare och motorer][motorer]

[motorer]: kretsar/motor-bil-sensor.png "Krets med arduino, breadboard, avståndsmätare och motorer"

För roboten behövs ett chassi och två likströmsmotorer med växelåda. Dessa har vi redan monterat.
Dessutom behöver vi en motorstyrningskrets som kan driva båda motorerna, och en ultraljudsavståndsmätare.

### Motorstyrning

Motorstyrningskretsen har, förutom '5V' och 'GND', sex pinnar som
styr motorerna, tre för varje. Två bestämmer vilket håll motorn ska gå, och en
bestämmer hastigheten. Hastighetspinnen ska vi skriva *analoga* värden till, det vill
säga allt mellan 0 och 255. Det fixar vi genom att använda *pulsviddsmodulering*. pinnen
kommer att vara antingen hög eller låg, men växla fort fram och tillbaka. Det gör
arduinon åt oss om vi använder någon av de pinnar som är märkta PWM (eller ~).

![Pulsviddsmodulering][pwm]

[pwm]: bilder/pwm.png "Pulsviddsmodulering"

Lägg till följande kod i din sketch:

```
#define IN1 13
#define IN2 12
#define IN3 11
#define IN4 10

#define SPEEDA 6
#define SPEEDB 9

void right(int pin1, int pin2) {
  digitalWrite(pin1, HIGH);
  digitalWrite(pin2, LOW);
}

void left(int pin1, int pin2) {
  digitalWrite(pin1, LOW);
  digitalWrite(pin2, HIGH);
}

void stop(int pin1, int pin2) {
  digitalWrite(pin1, HIGH);
  digitalWrite(pin2, HIGH);
}

void setSpeed(int pin, int speed) {
  analogWrite(pin,speed);
}
```

`void` (som betyder "tomrum" på engelska) betyder att funktionen inte returnerar
något. Vi bara säger åt datorn att göra något, men vill inte ha ett svar.

Nu kan vi få roboten att röra på sig:

```ino
int speed=0;

void setup() {
  pinMode(IN1,OUTPUT);
  pinMode(IN2,OUTPUT);
  pinMode(IN3,OUTPUT);
  pinMode(IN4,OUTPUT);
}

void loop() {
  right(IN1,IN2);
  left(IN3,IN4);

  long distance=measureDistance();
  if(true) {
    speed=255;
  }

  setSpeed(SPEEDA,speed);
  setSpeed(SPEEDB,speed);
  delay(300);
}
```

Det är inte säkert att hjulen snurrar åt rätt håll. Om det blir fel, byt `right` mot `left`.

### Avståndsmätaren

För att roboten inte ska köra in i saker och ting behöver vi mäta avståndet till hinder. Om vi upptäcker att vi har
kommit för nära ett hinder kan vi stanna. Eller kanske backa och vända?

Avståndsmätaren behöver anslutas till `5V` (den pinen är märkt `VCC` på sensorn) och till `GND`. Man använder den
genom att skicka en kort puls till `TRIG` så att den sedan skickar ut en kort ultraljudspuls som studsar mot något
hinder. Sensorn lyssnar sedan efter att ljudet studsar tillbaka. Tiden det tar för ljudet fram och tillbaka får vi
ut som vidden på pulsen på pinne `ECHO`. Eftersom vi vet hur fort ljudet går (ungefär 340 m/s) kan vi räkna ut vilket avstånd det
motsvarar.

För att mäta avståndet måste vi alltså göra ett antal steg med att slå på och av `TRIG`, läsa `ECHO` och räkna
ut avståndet i cm. Vi kan paketera den biten i en _funktion_, en bit kod som räknar ut något, som vi ger ett namn
och som vi sen kan använda.

Lägg till det här sist i din sketch:

```ino

#define TRIG 7
#define ECHO 8

// add the following to setup
//  pinMode(TRIG,OUTPUT);
//  pinMode(ECHO,INPUT);

long measureDistance() {
  long durationindigit, distanceincm;
  digitalWrite(TRIG, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG, LOW);
  durationindigit = pulseIn(ECHO, HIGH);
  distanceincm = (durationindigit/5) / 29.1;
  Serial.print("distance ");
  Serial.println(distanceincm,DEC);
  return distanceincm;
}
```

En sak att vara uppmärksam på. Ultraljudssensorn "ser" lite snett
upp och ned, så om den är för nära underlaget kan den se det av misstag.

![Ultraljudssensorns riktningskänslighet][riktning]

[riktning]: bilder/IMG_2212.jpg "Ultraljudssensorns riktningskänslighet"


### Allt

Nu har vi bitarna som vi kan sätta ihop för att få roboten att köra om det
är fritt fram, och stanna om den kommer nära ett hinder.

```ino
int speed=0;

void loop() {
  right(IN1,IN2);
  left(IN3,IN4);

  long distance=measureDistance();
  if(distance<15) {
    speed=0;
  } else {
    speed=255;
  }

  setSpeed(SPEEDA,speed);
  setSpeed(SPEEDB,speed);
  delay(300);
}
```

Återigen, det är inte säkert att hjulen snurrar åt rätt håll. Det beror på hur motorerna är inkopplade till motorstyrningen.
Om det är fel så är det lättast att rätta det i mjukvara (byt left mot right eller tvärtom).
