## Schritt 4

Jetzt haben wir zwar eine Alarmanlage, aber noch keine Verbindung mit der Claviscloud.
Das wollen wir jetzt ändern. 😉

* **Ziehe** den ``||IoTCube:LoRa Netzwerk-Verbindung||`` Block in den ``||basic:beim Start||``
Block **rein**.

* **Ziehe** danach den ``||loops:während||`` Block rein und in seinen **Parameter** den 
``||logic:nicht||`` Block rein. In die Schleife kommt der ``||IoTCube:Gerätstatus-Bit||``
Block, der auf **"Verbunden"** gestellt ist.

* Während der IoT-Cube noch **nicht verbunden** ist, sollen die LED's unter ``||basic:zeige Symbol||``
ein X zeigen, sonst einen **Haken**.

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
## Schritt 5

Nun bauen wir eine Funktion, mit der wir Daten an die Claviscloud und so zum Dashboard
schicken können. Davor erstellst du 3 ``||variables:Variablen |`` mit dem Namen
**spaeterSenden**, **sendeErlaubnis** und **msBeiLetztemSenden**, setzt
die ersten zwei auf **falsch**, während die letzte Variable den Wert ``||control:Millisekunden||``
hat und fügst alle beim Start hinzu.

```blocks
IoTCube.LoRa_Join(
eBool.enable,
eBool.enable,
10,
8
)
while (!(IoTCube.getStatus(eSTATUS_MASK.JOINED))) {
    basic.showIcon(IconNames.Pitchfork)
}
basic.showIcon(IconNames.Yes)
spaeterSenden = false
sendeErlaubnis = false
msBeiLetztemSenden = control.millis()
```

## Schritt 6

Da wir unsere Variablen für die Funktion haben, erstellst du jetzt eine neue
``||functions:Funktion||`` mit dem namen **sendeDaten** mit 
einer Zahl als Parameter namens **status** und befolgst folgende Schritte.

* Zieh eine ``||logic:Wenn-Abfrage mit ansonsten||`` in die Funktion, die abfragt,
ob ``||control:Millisekunden||`` größer ist als die
``||variables: msBeiLetztemSenden |`` und addierst zur variable per 
``||math: + ||`` 5000, indem du den Matheblock in die rechte Seite der Abfrage ziehst.
* Zieh den ``||IoTCube:Wahrheitswert||`` Block mit **ID_0 = status** in die Abfrage rein
und sende mit dem ``||IoTCube:sende Daten||`` Block die Daten zur Claviscloud
* Setze danach ``||variables: spaeterSenden |`` auf falsch und die
``||variables: msBeiLetztemSenden |`` auf ``||control:Millisekunden||``. In
ansonsten setzt du ``||variables: spaeterSenden |`` auf wahr.

```blocks
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
```
## Schritt 7

Da du jetzt weißt, wie man ``||functions:Funktion||`` erstellt, machen wir zwei neue
mit dem Namen **objektSicher** und **objektGeklaut**.

* Wir machen eine neue ``||variables: Variable |`` namens **objektVorhanden**
und setzen sie in ``||functions:objektSicher||`` auf **1**, während wir sie in
``||functions:objektGeklaut||`` auf **0** setzen.
* In **beiden Funktionen** ziehst du den ``||functions:Aufruf sendeDaten||`` rein und ziehst
in den **Parameter** die ``||variables: objektVorhanden |`` Variable rein.

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

## Schritt 8

Nun haben wir unsere zwei Funktionen, welche unserem Dashboard Bescheid geben,
ob unser Objekt vom Ultraschallsensor erfasst wird oder geklaut wurde.

* **Füge** den ``||functions:Aufruf objektSicher||`` in den **Start** hinzu, um
beim Starten den Programms das Dashboard auf den richtigen Zustand zu aktuallisieren.
* **Füge** den ``||functions:Aufruf objektGeklaut||`` in die ``||logic:Wenn-Abfrage||`` 
von dauerhaft **hinzu**, drücke auf das **Plus der Abfrage** und füge dann in  **ansonsten** den
``||functions:Aufruf objektSicher||`` hinzu.

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
sendeErlaubnis = false
msBeiLetztemSenden = control.millis()
objektSicher()

basic.forever(function () {
    if (smartfeldSensoren.measureInCentimetersV2(DigitalPin.P1) > 10) {
        music.play(music.tonePlayable(262, music.beat(BeatFraction.Half)), music.PlaybackMode.UntilDone)
        objektGeklaut()
    } else {
        objektSicher()
    }
})
```
## Schritt 9

Um sicherzugehen, dass die Daten verlässlich gesendet wurden, machen wir eine Schleife
die prüft, ob die Daten beim Aufrufen der Funktion geschickt wurden.

* **Ziehe** den Block ``||loops: alle 500ms ||`` ins **Programm**.
* **Ziehe** den ``||logic: wenn dann ||`` Block in die **Schleife**.
* **Ziehe** die Variable ``||variables: spaeterSenden |`` ins leere Feld
der **Wenn-Abfrage**.
* **Rufe** die Funktion ``||functions: sendeDaten(objektVorhanden) ||`` in der **Schleife** auf.

```blocks
loops.everyInterval(500, function () {
    if (spaeterSenden) {
        sendeDaten(objektVorhanden)
    }
})
```
## Glückwunsch🤩

Du hast eine einfache Alarmanlage programmiert und somit den ersten Teil des Tutorials 
absolviert! Durch das Klicken auf den [Link](https://makecode.microbit.org/#tutorial:github:kosta11111/SmartAlarmanlage/docs/tutorials/tutorial_part2)
kommst du zum zweiten Teil des Turials, in dem du den IoT-Cube mit der Claviscloud verbindest.

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