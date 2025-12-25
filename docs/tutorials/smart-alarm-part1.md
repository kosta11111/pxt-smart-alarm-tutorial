```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```

# Alarmanlage

## Willkommen!
In dem ersten Teil des Tutorials entwickelst du eine Alarmanlage, die bei einem Diebstahl deines 
bewachten Objekts einen Alarm abgibt.

## Wichtig! @showdialog
Stecke den **Ultraschallsensor** am **Port J2** an.  

![Tutorialbild](https://github.com/kosta11111/pxt-smart-alarm-tutorial/blob/master/docs/imgs/IoTCube.png?raw=true) 

Falls du **Probleme** beim Tutorial hast, kannst du beim Klicken auf der **Glühbirne** sehen,
wie der Code ausschauen soll. 
![Tutorialbild](https://github.com/kosta11111/pxt-smart-alarm-tutorial/blob/master/docs/imgs/Gl%C3%BChbirne.png?raw=true)

Klicke auf die **Codeschnipsel** im Text, um direkt zu den **Codeblöcken** zu kommen!
![Tutorialbild](https://github.com/kosta11111/pxt-smart-alarm-tutorial/blob/master/docs/imgs/Codeschnipsel.png?raw=true)

## Schritt 1

Als ersten Schritt **ziehen** wir eine ``||logic:Wenn-Abfrage||`` 
in den ``||basic:dauerhaft||`` Codeblock **rein**. 

Das ist wichtig, um zu prüfen,ob unser Objekt noch da ist.

```blocks 
basic.forever(function () {
    if (true) {
    }
})
```

## Schritt 2

Jetzt wollen wir, dass der Cube einen Alarm schlägt, wenn der Ultraschallsensor
nichts in 10 cm Reichweite erkennt.

* **Ziehe** den ``||logic:0 < 0||`` Block in die **Bedinung** der **Wenn-Abfrage** rein

* **Ziehe** den ``||smartfeldSensoren:Distanz in cm||`` Codeblock in die rechte Null

* **Schreibe** in die linke Null eine **10**

* Der Codeblock ``||music:spiele Ton||`` kommt in die Abfrage

* Ändere den **Schlag** auf **1/2**, um einen typischen Alarmsound zu bekommen

```blocks
basic.forever(function () {
    if (smartfeldSensoren.measureInCentimetersV2(DigitalPin.P1) > 10) {
        music.play(music.tonePlayable(262, music.beat(BeatFraction.Half)), music.PlaybackMode.UntilDone)
    } 
})
```
## Schritt 3

Der IoT-Cube funktioniert jetzt als eine Alarmanlage, aber man kann den Alarm nicht deaktivieren.
Das Deaktivieren implementieren wir mit den A + B-Knöpfen.

* **Ziehe** den ``||input: Wenn Knopf A geklickt||`` Codeblock **zweimal** in das Programm
 
* **Ändere bei einem** der beiden Codeblöcken **den Buchstaben** auf ein **B** um.

* **Erstelle** eine neue ``||Variables:Variable||`` namens **"aktiv"**.

* **Füge** ``||Variables:setze aktiv auf 0||`` in ``||input: Wenn Knopf B geklickt||`` ein.

* **Wiederhole** den Schritt bei ``||input: Wenn Knopf A geklickt||`` und änder die Zahl auf 1.

* **Füge** den ``||loops:während||`` Codeblock oben in den ``||basic:dauerhaft||`` Block hinzu 

* **Ziehe** den dort **bereits stehenden Code in die Schleife**.

* **Ziehe** in die **Bedinung** der Schleife die neue Variable ``||Variables:aktiv||``
rein.

Drücke auf **A**, um die Alarmanlage einzuschalten und auf **B**, um sie zu deaktivieren.

```blocks
input.onButtonPressed(Button.A, function () {
    aktiv = 1
})

input.onButtonPressed(Button.B, function () {
    aktiv = 0
})

basic.forever(function () {
    while (aktiv) {
        if (smartfeldSensoren.measureInCentimetersV2(DigitalPin.P1) > 10) {
            music.play(music.tonePlayable(262, music.beat(BeatFraction.Half)), music.PlaybackMode.UntilDone)
        } 
    }
})
```

## Glückwunsch🤩

Du hast den ersten Teil des Tutorials erfolgreich absolviert!🙌

Lade den Code auf dein IoT-Cube herunter, und aktiviere die Alarmanlage durchs drücken
auf den A-Button. Lege ein Objekt vor dem Ultraschallsensor und schau was passiert, wenn
du diesen wegnimmst.

Klicke [Hier](https://makecode.microbit.org/#tutorial:github:kosta11111/pxt-smart-alarm-tutorial/docs/tutorials/smart-alarm-part2),
um den zweiten Part des Tutorials zu starten!