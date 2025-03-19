# Reflexion zur Pipeline "Prüfung M324"

## 1. Auseinandersetzung und Verständnis mit der Pipeline

Die Pipeline besteht aus vier Haupt-Jobs:

- **setup:**  
  Dieser Job klont das Repository, installiert die benötigten Abhängigkeiten mittels `npm install` und lädt das `node_modules`-Verzeichnis als Artifact hoch.  

- **testing:**  
  Nach erfolgreichem Abschluss des Setup-Jobs wird in diesem Job ein einfacher Testschritt ausgeführt (repräsentiert durch einen Echo-Befehl).  

- **linting:**  
  Dieser Job führt die Code-Linting-Prüfung durch, um den Code auf Stil- und Syntaxfehler zu überprüfen.  
  *Besonderheit:* Hier wird versucht, den Befehl `next lint` auszuführen. Es muss sichergestellt werden, dass das Next.js-Binary verfügbar ist (gegebenenfalls über `npx` oder durch Anpassung des PATH).

- **deployment:**  
  Der Deployment-Job startet erst, wenn die Jobs `setup`, `testing` und `linting` erfolgreich abgeschlossen wurden.  
  *Ziel:* Eine letzte Deployment-Aktion (hier nur als Echo repräsentiert), die in einer echten Pipeline durch den tatsächlichen Build- und Deploy-Schritt ersetzt wird.

## 2. Überlegungen zu den einzelnen Schritten und dem Aufbau

- **Wiederverwendbarkeit der Abhängigkeiten:**  
  Das Verwenden von Artifacts (Upload/Download von `node_modules`) ist ein intelligenter Ansatz, um die Dauer der Pipeline zu verkürzen, da `npm install` nur einmal ausgeführt werden muss. Dies ist besonders bei größeren Projekten vorteilhaft.

- **Job-Abhängigkeiten:**  
  Die Verwendung von `needs` stellt sicher, dass die Jobs in einer sinnvollen Reihenfolge ablaufen:
  - `testing` und `linting` werden erst ausgeführt, wenn der `setup`-Job abgeschlossen ist.
  - Der `deployment`-Job wartet auf das erfolgreiche Abschließen von Setup, Testing und Linting.
  
- **Einfache Repräsentation:**  
  Da in dieser Übung vor allem das Verständnis der Sequenzialisierung und Parallelisierung geübt wird, wurden die Aktionen durch einfache `echo`-Befehle ersetzt. Dies erlaubt es, den logischen Ablauf zu überprüfen, ohne sich mit komplexen Build-Prozessen auseinanderzusetzen.

## 3. Verbesserungsvorschläge für die Zukunft

- **Verwendung von `npx` für Next.js-Befehle:**  
  Um Probleme wie `command not found` zu vermeiden, könnte man den Linting-Befehl anpassen:
  ```yaml
  - name: Linting
    run: npx next lint
