# Reflexion zur Pipeline "Prüfung M324"

## 1. Auseinandersetzung und Verständnis mit der Pipeline

Die Pipeline besteht aus vier Haupt-Jobs:

- **setup:**  
  In diesem Job wird das Repository geklont, die notwendigen Abhängigkeiten mittels `npm install` installiert und das `node_modules`-Verzeichnis als Artifact hochgeladen. Dadurch werden alle nachfolgenden Jobs von einer einmal installierten, gemeinsamen Abhängigkeitenbasis profitieren.

- **testing:**  
  Dieser Job wird nach einem erfolgreichen Setup ausgeführt. Hier wird ein einfacher Testschritt (repräsentiert durch einen Echo-Befehl) ausgeführt, um sicherzustellen, dass die Testumgebung korrekt vorbereitet wurde.

- **linting:**  
  Dieser Job führt eine Code-Linting-Prüfung durch, um den Code auf mögliche Stil- und Syntaxfehler zu überprüfen. Dabei wird aktuell der Befehl `next lint` verwendet – es muss sichergestellt werden, dass das Next.js-Binary vorhanden ist (zum Beispiel durch Verwendung von `npx next lint` oder durch Anpassung des PATH), sofern Next.js tatsächlich Teil des Projekts ist.

- **deployment:**  
  Der Deployment-Job wird erst gestartet, wenn die Jobs `setup`, `testing` und `linting` erfolgreich abgeschlossen wurden. Aktuell repräsentiert ein Echo-Befehl die finale Deployment-Aktion, die in einer produktiven Umgebung durch einen echten Build- und Deployment-Prozess ersetzt werden müsste.

## 2. Überlegungen zu den einzelnen Schritten und dem Aufbau

- **Wiederverwendbarkeit der Abhängigkeiten:**  
  Durch den Upload des `node_modules`-Verzeichnisses im Setup-Job und dessen anschließendes Herunterladen in den weiteren Jobs wird `npm install` nur einmal ausgeführt. Dies reduziert die Gesamt-Dauer der Pipeline, was besonders bei größeren Projekten ein großer Vorteil ist.

- **Job-Abhängigkeiten mittels `needs`:**  
  Die Nutzung von `needs` sorgt dafür, dass die Jobs in der korrekten Reihenfolge ausgeführt werden:
  - Die Jobs `testing` und `linting` starten erst, wenn der `setup`-Job erfolgreich abgeschlossen wurde.
  - Der `deployment`-Job wird nur angestoßen, wenn sowohl das Setup als auch die Test- und Linting-Jobs fehlerfrei abgeschlossen wurden.

- **Einfache Darstellung zur Übung:**  
  In dieser Übung wurden die eigentlichen Befehle (wie Tests, Linting und Deployment) durch einfache `echo`-Befehle ersetzt. Dadurch wird der Fokus auf das Verständnis der Sequenzialisierung und Parallelisierung innerhalb der Pipeline gelegt, ohne sich in komplexen Build-Prozessen zu verlieren.

## 3. Verbesserungsvorschläge für die Zukunft

- **Verwendung von `npx` für Next.js-Befehle:**  
  Um Probleme wie `command not found` zu vermeiden, sollte der Linting-Befehl eventuell angepasst werden:
  ```yaml
  - name: Linting
    run: npx next lint
