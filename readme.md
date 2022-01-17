Dokumentacja ogólna

# Hardware

![img](./Arduino_Uno.jpg)
![img](./Silnik_DC.jpg)
![img](./Pozostale_Elementy.jpg)

# Projekt układu elektronicznego z wykorzystaniem mikrokontrolera Arduino

## Wprowadzenie

-   Temat projektu:
    Sterowanie silnikiem DC z wykorzystaniem mostka H.

-   Opis: 
    Mostek H zbudowany na tranzystorach bipolarnych 2N2222.
    Sygnały PWM podam na bramki tranzystorów, sterowanie obrotami z potencjometru.
    Diody które pokazałem na schemacie nie zostaną zastosowane. Nie jest to wymagane.

    ![img](./schemat_4tranz.jpg)

-   Lista elementów: 
    Arduino UNO, 
    płytka stykowa, 
    silnik DC 1,5V-3V, 
    źródło zasialania (baterie 9V), 
    4x rezystor 1k, 
    4x tranzystor 2N2222, 
    przycisk, 
    potencjometr, 
    przewody.

W projekcie pokażę, jak osiągnąć kontrolę prędkości i kierunku silnika 
prądu stałego za pomocą Arduino UNO.

Silniki prądu stałego znajdują się wszędzie: w elektronice, zabawkach, 
wentylatorach, narzędziach, tarczach, pompach itp. Silnik prądu stałego jest 
elementem wykonawczym, który przekształca zasilanie prądem stałym na 
obrót lub ruch.
Istnieją różne rodzaje silników prądu stałego: Szczotkowy silnik prądu 
stałego, bezszczotkowy silnik prądu stałego, przekładniowy silnik prądu 
stałego, serwomotor, silnik krokowy i liniowy element wykonawczy prądu 
stałego.
Różne typy silników są wykorzystywane w różnych zastosowaniach, takich 
jak robotyka, precyzyjne pozycjonowanie, automatyka przemysłowa itp.

Opis komponentów:
Arduino Uno - Jest to płytka prototypowa oparta na mikrokontrolerze. 
Mikrokontroler zastosowany na płytce Arduino Uno to ATmega328p. 
Arduino jest odpowiedzialne za sterowanie prędkością i kierunkiem obrotów 
silnika za pomocą innych komponentów.
2N2222 - Jest to tranzystor NPN o prądzie wyjściowym 800 mA. 
Maksymalny prąd wyjściowy, który jest dostępny z pinów I/O Arduino 
wynosi 50 mA, co nie jest wystarczające do napędzenia silnika. W związku 
z tym zastosowano cztery tranzystory o dużej wydajności prądowej.

## Wykonanie

![img](./zdj1.jpg)
![img](./zdj2.jpg)
![img](./zdj3.jpg)

Działanie:
Celem tego projektu jest kontrola prędkości i kierunku silnika prądu stałego 
bez użycia układu scalonego sterownika silnika. W związku z tym musimy 
utworzyć mostek H z tranzystorów, aby napędzać silnik. 
POT jest podłączony do pinu analogowego A0 układu Arduino. Służy on do 
regulacji prędkości obrotowej silnika. Normalna praca silnika polega na 
obracaniu się w kierunku naprzód.
Kiedy przycisk, który jest podłączony do pinu 7 Arduino, zostanie 
aktywowany lub naciśnięty, kierunek obrotu zostanie odwrócony i będzie 
się obracał w tym kierunku, dopóki przycisk nie zostanie naciśnięty 
ponownie.
Aby silnik obracał się do przodu, tranzystory Q2 i Q3 muszą być włączone. 
Stąd na wyjściach 5 i 4 Arduino jest stan wysoki.
Arduino jest zaprogramowane tak, aby wykrywać logiczny stan niski na 
pinie 7, gdy przycisk jest wciśnięty. Gdy przycisk zostanie wciśnięty raz, 
tranzystory Q1 i Q4 muszą zostać włączone. Stąd piny 3 i 2 Arduino są 
ustawione w stan wysoki. Silnik obraca się w odwrotnym kierunku, jeśli 
przycisk zostanie wciśnięty ponownie.

Fragment kodu:

```cpp
const int potPin = A0;
const int buttonPin = 7;
const int forward1 = 5;
const int forward2 = 4;
const int backward1 = 3;
const int backward2 = 2;
int potValue = 0;
int motorValue = 0;
int buttonState = 0;
boolean a;
void setup()
{
pinMode(buttonPin, INPUT_PULLUP);
pinMode (forward1, OUTPUT);
pinMode (forward2, OUTPUT);
pinMode (backward1, OUTPUT);
pinMode (backward2, OUTPUT);
}
void loop()
{
potValue = analogRead(potPin);
motorValue = map(potValue, 0, 1023, 0, 255);
buttonState = digitalRead(buttonPin);
if (buttonState == LOW)
{
a=!a;
}
if(a)
{
analogWrite(backward1, motorValue);
digitalWrite (backward2, HIGH);
digitalWrite (forward1, LOW);
digitalWrite (forward2, LOW);
}
else
{
analogWrite(forward1, motorValue);
digitalWrite (forward2, HIGH);
digitalWrite (backward1, LOW);
digitalWrite (backward2, LOW);
}
}
```