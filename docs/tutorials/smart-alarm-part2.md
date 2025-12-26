```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
#Alarmanlage

## Willkommen!

Im zweiten Teil des Tutorials verbinden wir die Alarmanlage mit der Claviscloud, um über
das Dashboard zu prüfen, ob das überwachte Objekt geklaut wurde, oder noch da ist.

Die Funktion, um Daten an die Cloud zu senden, ist bereits vorhanden!

## Schritt 1

* **Ziehe** den ``||basic:beim Start||`` Block ins Programm.

* **Ziehe** den ``||IoTCube:LoRa Netzwerk-Verbindung||`` Block in den ``||basic:beim Start||``
Block **rein**.

* **Ziehe** danach den ``||loops:während||`` Block rein 

* **Füge** in die **Bedinung** den ``||logic:nicht||`` Codeblock hinzu

* In den ``||logic:nicht||`` Codeblock kommt der ``||IoTCube:Gerätstatus-Bit||`` Block

* **Ziehe** mit dem ``||basic:zeige Symbol||`` ein X in die **Während-Schleife**

* **Wiederhole** den Schritt unter der **Während-Schleife** mit einem Haken

```blocks
IoTCube.LoRa_Join(
eBool.enable,
eBool.enable,
10,
8
)
while (!(IoTCube.getStatus(eSTATUS_MASK.JOINED))) {
    basic.showIcon(IconNames.No)
}
basic.showIcon(IconNames.Yes)
```

## Schritt 2

Wir erstellen jetzt 2 Funktionen, mit denen wir die richtigen Daten an die Cloud senden.

* **Erstelle** zwei ``||functions:Funktionen||`` namens **objektSicher** und **objektGeklaut**

* **Erstelle** eine neue Variable namens ``||variables: objektVorhanden |``
 
* **Setze** die neue Variable mit ``||variables: setze objektVorhanden auf||`` in 
**objektSicher** auf **1** und in **objektGeklaut** auf **0**

* In **beiden Funktionen** ziehst du den ``||functions:Aufruf sendeDaten||`` rein 

* **Ziehe** ins leere Feld des **Aufrufs** die **Variable** rein


```blocks
function objektSicher () {
    objektVorhanden = 1
    sendeDaten(objektVorhanden)
}
function objektGeklaut () {
    objektVorhanden = 0
    sendeDaten(objektVorhanden)
}
```

## Schritt 3

Nun haben wir unsere zwei Funktionen, welche unserem Dashboard Bescheid geben,
ob unser Objekt vom Ultraschallsensor erfasst wird oder geklaut wurde.

* **Füge** in die **Wenn-Abfrage** von ``||basic:dauerhaft||`` den 
``||functions:Aufruf objektGeklaut||`` hinzu

* In das Feld unter **Ansonsten** kommt der ``||functions:Aufruf objektSicher||`` 

```blocks
IoTCube.LoRa_Join(
eBool.enable,
eBool.enable,
10,
8
)
while (!(IoTCube.getStatus(eSTATUS_MASK.JOINED))) {
    basic.showIcon(IconNames.No)
}
basic.showIcon(IconNames.Yes)
spaeterSenden = false
msBeiLetztemSenden = control.millis()

basic.forever(function () {
    if (smartfeldSensoren.measureInCentimetersV2(DigitalPin.P1) > 10) {
        music.play(music.tonePlayable(262, music.beat(BeatFraction.Half)), music.PlaybackMode.UntilDone)
        objektGeklaut()
    } else {
        objektSicher()
    }
})
```
## Glückwunsch🤩

Du hast die Alarmanlage mit all seinen Funktionen programmiert! Gehe auf die [Claviscloud](https://iot.claviscloud.ch/home),
um dort dein IoT-Cube mit dem Dashboard für die Alarmanlage zu verbinden!

```template
function sendeDaten (status: number) {
    if (control.millis() > msBeiLetztemSenden + 5000) {
        IoTCube.addBinary(eIDs.ID_0, status)
        IoTCube.SendBufferSimple()
        spaeterSenden = false
        msBeiLetztemSenden = control.millis()
    } else {
        spaeterSenden = true
    }
}

let msBeiLetztemSenden = 0
let spaeterSenden = false

basic.forever(function () {
    while (aktiv) {
        if (smartfeldSensoren.measureInCentimetersV2(DigitalPin.P1) > 10) {
            music.play(music.tonePlayable(262, music.beat(BeatFraction.Half)), music.PlaybackMode.UntilDone)
        } else {

        }
    }
})

loops.everyInterval(500, function () {
    if (spaeterSenden) {
        sendeDaten(objektVorhanden)
    }
})

input.onButtonPressed(Button.A, function () {
    aktiv = 1
})

input.onButtonPressed(Button.B, function () {
    aktiv = 0
})
```