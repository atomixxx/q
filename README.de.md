<p align="center">
  <a href="https://github.com/atomixxx/q/blob/master/README.md">English</a> |
  <span>Deutsch</span>
</p>

# Quarkus 2 versus Quarkus 1 – Entwicklererfahrung

Vor ein Paar Tage hat die Quarkus Gemeinschaft eine neue Hauptversion veröffentlicht – Quarkus 2.

Quarkus 1 ist ausgezeichnet. Nicht nur wegen der Features es packt, sondern am meistens wegen der Tatsache, wie einfach
es ist Software dadurch zu entwickeln. Dann kommt Quarkus 2 und bringt das Entwicklererfahrung auf eine andere Ebene.

## Verbesserungen des Dev Modus

In Quarkus 1 ist Hot-Code-Ersatz schon gebrauchsfertig. Das ist ein riesiger Vorteil im Vergleich mit den non-Quarkus
Umgebungen, wo man kämpfen soll, bis Hot-Code-Ersatz funktioniert. Oft ist das durch Drittanbieterwerkzeuge erreicht,
die aber nicht immer zuferlässig funktionieren.

Thechnisch gibt es keinen echten Hot-Code-Ersatz in Quarkus, sondern das Code komplett neu laden, wenn das Code geändert
wird und wir mit der Anwendung interagieren.

Wir, die Entwickler sind damit mehr als zufrieden. Gibt es aber noch etwas, was man in die Richtung machen kann?

Offensichtlich kann das Quarkus-Team noch mehr bieten. Seit Quarkus 2 is der Dev Modus anders geworden und zwar ein

### Interaktiver Dev Modus

Man startet ihn so:

```shell script
./mvnw compile quarkus:dev
```

Quarkus 1 würde die Anwendung kompilieren und starten. Danach würde es bei eine Interagierung überprüfen, ob es
Änderungen gäbe und wenn doch, würde die Anwendung neu gestartet. In Dev Modus kann man APIs aufrufen, sich zum Debuggen
verbinden oder Dev Modus unterbrechen (durch Ctrl-C).

Quarkus 2 Dev Modus hat noch viel mehr zu bieten.

### Dev Modus Menu

Beim Starten des Dev Modus taucht ein einfaches, aber sehr hilfreiches Menü auf:

![Tests paused, press [r] to resume, [w] to open the browser, [h] for more options>](Quarkus2DevMenu.PNG "Quarkus 2 Dev Mode Menu")

Die Tests fangen nicht gleich an, was absolut Sinn macht. Es könnte sein, dass sie einerseits viel Zeit in Anspruch
nehmen, anderseits braüchten wir die Ergebnisse nicht. Falls wir sie doch bräuchten, könnten wir sie mir der Taste `"r"`
zum ersten Mal starten.

Nächstes Mal, wenn wir uns entscheiden, die Tests auszuführen und wir die Taste `"r"` drücken, werden nicht alle Tests
wieder ausgeführt, sondern nur diese, die entweder 1) geändert wurden oder 2) neu ertellt wurden oder 3) dessen
getesteten Code verändert wurde. Das macht Sinn, besonders wenn es viele Tests gibt.

> **_NOTIZ:_** Dieses Feature hat in den verschiedene 2.x Versionien bestimmte Abweichungen. In manche Versionen soll man
> [p]ause und danach [r]esume damit das neue Code und Test-Code neu geladen wird.

Das ist bei weitem nicht die einzigen Verbesserungen. Um alle Möglichkeiten zu sehen, die in Dev Modus zur Verfügung
stehen, eifach Taste `"h"` drücken und die Hilfe-Menü auftauchen wird:

![Dev Mode Help](Quarkus2DevMenuHelp.PNG "Dev Mode Help")

### Eingebettete Abhängigkeiten starten

Eine Anwendung braucht wahrscheinlich seine Abhängigkeiten, um richtig funktionieren zu können. Bedeutet das, dass wir
lokal sowohl eine Datenbank, als auch Kafka Broker und andere brauchen? Beim Quarkus 1, ja, unbedingt. Zum Glück ist es
beim Quarkus 2 nicht der Fall. Es wird sich um alle Abhängigkeiten kümmern, es sei den wir unbedingt einen bestimmten
externen Server verwenden möchten. In dem Fall können wir die Verbindungsdaten, wie bei Quarkus 1
in `application.properties` für die Dev Umgebung definieren.

Durch Testcontainers schafft Quarkus ein Kafka Broker mit Zookeeper, eine Datenbank und alles, was unsere Anwendung
bräuchte. Z. B., wenn wir in Quarkus 1 keine Kafka Verbindung configurieren, sucht Quarkus am `localhost:9092`. Wenn
nichts darauf zuhört, wird es natürlich fehlschlagen. In Quarkus 2 ist das Standardverhalten, ein Kafka Broker zu
starten. So wird es sowohl für Dev Modus, als auch für Tests funktionieren.

Also, wenn die Dev Umgebung schon läuft, könnten wir dazu in parallel Tests starten und zwar im gleichen JVM. Zusätzlich
startet Quarkus 2 seine eigene DB und Kafka. Wenn ich schon mit dem API gespielt hätte und den Inhalt der DB geändert
hätte, wären die Ergebnisse der Tests potentiell unzufersichtlich, oder? Das Quarkus Team hat das auch in Rücksicht
genommen, also, die Antwort wäre "Machen Sie sich keine Sorge". Die Dev und Tests laufen zwar im gleichen JVM, aber sie
sind in unterschiedliche Classloaders. Der DB Inhalt wird auch nicht verdorben, weil die Tests seinen eigenen Container
starten. Irgendwann könnten alle Abhängigkeiten doppelt laufen – 2 DB Container, 2 Kafka Brokern usw. Es wäre maximal
bis zum Ende der Tests dauern und das ist absolut normal.

## Allgemeine Bemerkungen

Quarkus 2 ist eine Hauptversion und es gibt einen Grund dafür. Es gibt inkompatible Änderungen. Während wir für eine
Migration etwas Aufwand investieren sollen, wäre es keine große Sache. Das Team hat sich darum gekümmert, man sein Code
relativ einfach migrieren zu können. Sie stellen eine gut
dokumentierte [Migrationsprocedure](https://github.com/quarkusio/quarkus/wiki/Migration-Guide-2.0) zur Verfügung.

Sowohl die Bibliotheken und die Spezifikationen, die aktualisiert wurden, als auch die Voraussetzungen (JDK, Docker,
Maven, Gradle) sind gut dokumentiert und es würde nichts bringen, sie nochmal hier zu erwähnen. Jedoch ist es
erwähnenswert, dass manche noch in _Experimental-_ oder _Vorschauphase_ sind.

Ist Quarkus 2 Produktionsbereit? Wie es oft passiert, ist die Antwort "Es kommt darauf an". Wenn mann keine
_experimentelle_ oder _Vorschau_ Features verwendet oder man sicher ist, dass die Anwendung gut getestet ist, mit
alle Eckfälle abgedeckt, inkl. Performanztesting, dann vielleicht, ja. Für meistens der Projekte, besonders für die
geschäftskritische, wäre es aber vermutlich nicht nicht so schlau sofort zu migrieren. Da das Projekt noch in eine
aktive Entwicklungsphase ist, ist die Wahrscheinlichkeit auf ein Bug zu stoßen nicht zu unterschätzen.

Kudos zu jedem einzelnen Mitglied der Quarkus Gemeinschaft. Nicht nur sind die Ideen echt ausgezeichnet, sondern auch sind
sie richtig aufgesetzt. Mit so eine starke und progressive Gemeinschaft hat Quarkus höhe Chancen, die am beforzuste Plattform
für Entvicklung von Microservices in JVM Weld zu werden.
