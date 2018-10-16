# F15 - Anleitung: Messwerterfassung und Prozesssteuerung
##### Sven Brieden 13.10.2018

## Vorwort zum Versuch
"
Im Verlauf des Versuchs werden Sie einige Prozesse zur Aufnahme von Messgrößen erleben sowie Strategien kennenlernen und selbst entwickeln, um externe Parameter zu kontrollieren.

Solche Prozesse sind oft mit vielen einzelnen Schritten verbunden (Kalibrierungen, Referenzierungen,etc.) die erst sauber durchgeführt werden müssen, bevor zuverlässige Ergebnisse generiert werden können.

Ziel des heutigen Versuchs wird es sein, Ihnen solche Prozesse an diversen Beispielen aufzuzeigen und Schritt für Schritt aus unbekannten Signalen belastbare Daten zu generieren. Der Ablauf und die Sequenz der einzelnen Versuchsteile orientieren sich an dem, was bei der Vorbereitung eines komplexen Forschungsexperiments real auftritt." Originalanleitung

## Inhaltsverzeichnis
- Vorwort zum Versuch
- Stichworte
- Theoretische Grundlagen
  - Stromkreis
  - Programmierung
    - Kontrollstrukturen
    - Datenstruktur/(Dateisystem)
  - Thermoelemente
  - Hardware für Prozesssteuerung
    - PC, Raspy, Ardoino, Hardwareansteuerung
- Experimentelles
- Messaufgaben  :
  - Messdaten mittels Raspberry Pi
  - Heizregelung mittels Raspberry Pi
  - Datenaufnahme mittels Labview
  - Analyse von elektronischen Bauelementen
- Erwartungen an die Ausarbeitung
- Literatur

## Programmierung

### Kontrollstrukturen
Kontrollstrukturen (Steuerkonstrukte) sind Anweisungen um den Ablauf eines Computerprogramms zu steuern. Eine Kontrollstruktur ist entweder eine Verzweigung oder eine Schleife. Meist wird ihre Ausführung über logische Ausdrücke der booleschen Algebra beeinflusst.

#### Verzweigungen
Eine Verzweigung legt fest, welcher von zwei (oder mehr) Programmabschnitten, abhängig von einer (oder mehreren) Bedingungen, ausgeführt wird. Eine bedingte Anweisung besteht aus einer Bedingung und einem Codeabschnitt, der wiederum aus einer oder mehreren Anweisungen besteht. Wird bei der Programmausführung die bedingte Anweisung erreicht, dann wird erst die Bedingung ausgewertet, und falls diese zutrifft (und nur dann) wird anschließend der Codeabschnitt ausgeführt. Danach wird in jedem Fall die Programmausführung mit den auf die bedingte Anweisung folgenden Anweisungen fortgesetzt.

Beispielcode in C:
``` C
const char *x;
//if-else-Konstrukt
if (zahl == 5)
  x = "Zahl gleich 5";
else
  x = "Zahl ungleich 5";
```

In vielen Programmiersprachen gibt es auch mehrfache Verzweigungen, auch Fallunterscheidungen genannt. Diese sind für diesen Versuch nicht vorausgesetzt, können aber bei Interesse benutzt werden. In C kann dies durch switch-Konstrukt oder if-elif-else-Konstrukt realisiert werden.

#### Schleifen
Eine Schleife (auch „Wiederholung“ oder englisch loop) wiederholt einen Anweisungs-Block – den sogenannten Schleifenrumpf oder Schleifenkörper –, solange die Schleifenbedingung als Laufbedingung gültig bleibt bzw. als Abbruchbedingung nicht eintritt. Schleifen, deren Schleifenbedingung immer zur Fortsetzung führt oder die keine Schleifenbedingung haben, sind Endlosschleifen.

Schleifen können beliebig verschachtelt werden: Innerhalb des Schleifenkörpers der äußeren Schleife befindet sich wiederum eine Schleife, sie liegt innen, oder unter der äußeren Schleife.

Prinzipiell werden folgende Typen von Schleifen unterschieden
 - kopfgesteuerte Schleife: Bei dieser Schleife wird eine Bedingung geprüft, mit der vorher entschieden wird, ob der Schleifenrumpf (Schleifeninhalt) ausgeführt wird (meist mit WHILE = solange eingeleitet).

 - Die nachprüfende oder fußgesteuerte Schleife: Bei dieser Schleife wird nach dem Durchlauf des Schleifenrumpfes (Schleifeninhalts) eine Bedingung überprüft, ob der Schleifenrumpf nochmal ausgeführt wird (meist als Konstrukt DO…WHILE = „ausführen … solange“ oder REPEAT…UNTIL = „wiederholen … bis“).

 - Die Zählschleife, eine Sonderform der vorprüfenden Schleife (meist als FOR = für-Schleife implementiert).

 - Die Mengenschleife, eine Sonderform der Zählschleife (meist als FOREACH = „für jedes Element der Menge“ implementiert; die Reihenfolge der Elemente ist beliebig).

 - Schleife mit Laufbedingung: Die Schleife wird solange durchlaufen, wie die Schleifenbedingung zu „wahr“ ausgewertet wird.

 - Schleife mit Abbruchbedingung: Wertet die Bedingung zu „wahr“ aus, so wird die Schleife abgebrochen.

Eine Endlosschleife ohne Schleifenbedingung (und ohne Schleifenabbruch darin) kann nur von außen unterbrochen werden, etwa durch einen Programmabbruch durch den Benutzer, Reset, Interrupt, Defekt, Abschalten des Gerätes oder ähnliches.

Für diesen Versuch ist es ausreichend die Zählschleife und die kopfgesteuerte Schleife genauer zu betrachten:
  - Bei einer For-Schleife zählt der Computer von einer Anfangszahl bis zu einer Endzahl und wiederholt dabei jedes Mal den Codeblock („Schleifenrumpf“). Die aktuelle Zahl wird in eine Variable („Iterator“) gesetzt, damit sie bei Bedarf in dem Codeblock Verwendung finden kann. Häufig ist die Zählschleife auf Ganzzahlen beschränkt. Das Ändern der Iterator-Variablen im Schleifenkörper ist bei vielen Programmiersprachen verboten und gilt als schlechter Programmierstil, da es oft zu schwer verständlichem Code führt – es läuft der Denkweise zuwider, direkt am Schleifenkopf die Anzahl der Durchläufe ablesen zu können.

    Beispielcode in C:
    ``` C
    int i;

    for(i=0; i<5; i++) {
     printf("Zahl %d\n", i+1);
    }
    ```

  - Bei einer kopfgesteuerten Schleife erfolgt die Abfrage der Bedingung, bevor der Schleifenrumpf ausgeführt wird, also am Kopf des Konstruktes. Eine logische Operation kann beispielsweise sein: (x > 4) Solange diese Bedingung wahr ist, werden die Anweisungen innerhalb der Schleife ausgeführt. Wird der Inhalt der logischen Operation nicht im Schleifenrumpf verändert, ist diese Kontrollstruktur meist nicht die richtige, weil diese Schleife sonst kein einziges Mal durchlaufen wird oder unendlich lang läuft.
    Beispielcode in C:
    ``` C
    float fahr, celsius;
    float max, step;
    ...
    while (celsius < max) {
      fahr = 9.0/5.0*celsius + 32.0;
      printf( "%5.1f °C sind %5.1f F\n", celsius, fahr);
      celsius = celsius + step;
    }
    ```

Schleifenabbruch im Sonderfall

In Fällen, die schwierig als Schleifenbedingung zu fassen sind, kann eine Schleife (aus dem Schleifenkörper heraus) meist abgebrochen werden.
Meist gibt es einen Befehl zum Gesamt-Abbruch der Schleife, das Programm wird dann mit der ersten Anweisung nach der Schleife fortgesetzt.
Beispielcode in C:
``` C
while (1) {
  printf("Bitte gib ein positive Zahl ein: ");
  scanf("%lf",&input);

  if(input <= 0)
  {
      printf("Nicht positiver Input, das Programm bricht ab");
      break;
  }
  printf("Die Wurzel von %lf ist %lf \n",input, sqrt(input));
}
```

