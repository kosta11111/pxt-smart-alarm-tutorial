```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
# Alarmanlage

## Willkommen!

Im zweiten Teil des Tutorials verbinden wir die Alarmanlage mit der Claviscloud, um über
das Dashboard zu prüfen, ob das überwachte Objekt geklaut wurde, oder noch da ist.

Die Funktion, um Daten an die Cloud zu senden, ist bereits vorhanden!

## Wichtig! @showdialog

**Schließe das Fenster des vorherigen Tutorials**, um beim Herunterladen des Codes Fehler zu vermeiden!

Durch das **Klicken** auf die **Kommentar-Zeichen** neben der Namen bestehender Codeblöcke, kannst du **genauere Beschreibungen** zu den Codeabschnitten lesen!
## Schritt 1

- **Ziehe** den ``||basic:beim Start||`` Block ins Programm.

- **Ziehe** den ``||IoTCube:LoRa Netzwerk-Verbindung||`` Block in den ``||basic:beim Start||``
Block **rein**.

- **Ziehe** danach den ``||loops:während||`` Block **darunter** rein 

- **Füge** in das **Falsch-Feld** den ``||logic:nicht||`` Codeblock ein

- In den ``||logic:nicht||`` Codeblock kommt der ``||IoTCube:lese Gerätstatus-Bit||`` Block

- **Ändere** den ``||IoTCube:Gerätstatus-Bit||`` von **Initialisierung** auf **Verbunden**

- **Ziehe** mit dem ``||basic:zeige Symbol||`` ein X in die **Während-Schleife**

- **Wiederhole** den letzten Schritt unter des **Während-Codeblocks** mit einem Haken

Mit dem zweiten Schritt verbinden wir am Anfang den IoT-Cube mit dem LoRa Netzwerk. Solange Der IoT-Cube noch nicht Verbunden ist, zeigt dieser ein X auf der LED-Fläche an. Verbindet sich der IoT-Cube erfolgreich, ändert sich das X zu einem Haken.

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

- **Erstelle** zwei ``||functions:Funktionen||`` namens **objektSicher** und **objektGeklaut**
 
- **Setze** die Variable **objektVorhanden** mit ``||variables: setze objektVorhanden auf||`` in 
**objektSicher** auf **1** und in **objektGeklaut** auf **0**

- In **beiden Funktionen** ziehst du den ``||functions:Aufruf sendeDaten||`` rein 

- **Ersetze** die **1** im ``||functions:Aufruf sendeDaten||`` mit der neuen **Variable**


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

- **Füge** in den **Wenn-Block** von ``||basic:dauerhaft||`` den 
``||functions:Aufruf objektGeklaut||`` hinzu

- In das Feld unter **Ansonsten** kommt der ``||functions:Aufruf objektSicher||`` 

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
// Durch diese Funktion wird der Claviscloud Datenpakete versendet, die dort vom zugehörigen Widget verarbeitet werden.
function sendeDaten (status: number) {
 // In dieser Wenn-Abfrage wird überprüft, ob das Datenpaket innerhalb der erwünschten Zeit versendet wird. Ist das nicht der Fall, redet man von einer Zeitüberschreitung.
    if (control.millis() > msBeiLetztemSenden + 5000) {
        IoTCube.addBinary(eIDs.ID_0, status)
        IoTCube.SendBufferSimple()
        spaeterSenden = false
        msBeiLetztemSenden = control.millis()
    } else {
     // Dadurch, dass die Variable spaeterSenden auf true gesetzt wird, führt der alle 500 ms Codeblock die Funktion neu aus. Dies passiert nur bei einer Zeitüberschreitung.
        spaeterSenden = true
    }
}

let msBeiLetztemSenden = 0
let spaeterSenden = false

basic.forever(function () {
// Die While Schleife (Im Blockcode "während" genannt) aktiviert die Alarmanlage, je nachdem ob dieser durch den A Knopf aktiviert oder durch den B Knopf deaktiviert wurde.
    while (aktiv) {
    // Es wird geprüft, ob der Ultraschallsensor zu dem überwachten Objekt eine Entfernung abmisst, die länger als zehn Zentimeter beträgt.
        if (smartfeldSensoren.measureInCentimetersV2(DigitalPin.P1) > 10) {
         // Falls das überwachte Objekt nicht in zehn Zentimeter Reichweite ist, wird hier ein Ton im schnellen Takt abgespielt.
            music.play(music.tonePlayable(262, music.beat(BeatFraction.Half)), music.PlaybackMode.UntilDone)
        } else {

        }
    }
})

// Falls das Senden durch die sendeDaten Funktion aufgrund von Zeitüberschreitung nicht geklappt hat, wird die Funktion in dieser Schleife erneut ausgeführt.
loops.everyInterval(500, function () {
    if (spaeterSenden) {
        sendeDaten(objektVorhanden)
    }
})

// Die Alarmanlage wird aktiviert.
input.onButtonPressed(Button.A, function () {
    aktiv = 1
})

// Die Alarmanlage wird deaktiviert.
input.onButtonPressed(Button.B, function () {
    aktiv = 0
})
```