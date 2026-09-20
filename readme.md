# LB 324

Tagebbbuch-Applikation (Flask) mit CI/CD: Tests bei jedem Push, Tests bei jedem Pull Request auf `dev`, automatische Auslieferung auf Azure bei jedem Merge in `main`.

Laufende Applikation: https://brkicedwin-tagebbbuch-gjd3gaajfzf5dzak.germanywestcentral-01.azurewebsites.net

## Lokal starten

```
pip install -r requirements.txt
flask run
```

Die Applikation liest das Passwort aus einer `.env`-Datei im Hauptverzeichnis. Diese Datei ist in der `.gitignore` und wird nicht ins Repository eingecheckt. Sie enthält eine Zeile:

```
PASSWORD="einSehrGeheimesPasswort"
```

Tests ausführen: `pytest`

## Äste

* `main`: nur Stand, der ausgeliefert wird. Jeder Merge löst eine Auslieferung auf Azure aus.
* `dev`: abgeschlossene und getestete Änderungen.
* `feature/...`: eigentliche Entwicklung, jeder Ast geht von `dev` aus und hat eine zugehörige Issue.

Neue Anforderungen werden als Issue mit der Vorlage «Anforderung» erfasst und mit dem passenden Label (Funktionale Anforderung, Qualitätsanforderung, Randanforderung) versehen.

## Aufgabe 2

`pre-commit` einmalig installieren und im Repository aktivieren:

```
pip install pre-commit
pre-commit install --hook-type pre-commit --hook-type pre-push
```

Der zweite Befehl ist nötig, weil zwei Hook-Typen installiert werden:

* Bei jedem `git commit` wird der Code mit `black` formatiert. Falls `black` Dateien verändert, bricht der commit ab. Die geänderten Dateien mit `git add` erneut hinzufügen und den commit wiederholen.
* Bei jedem `git push` werden die Tests mit `pytest` ausgeführt. Schlägt ein Test fehl, wird nicht gepusht.

Damit die Tests laufen, müssen die Abhängigkeiten installiert sein (`pip install -r requirements.txt`).

## Aufgabe 4

Das Passwort steht lokal in der `.env` und ist nicht im Repository. Auf Azure wird es als Umgebungsvariable der Web App gesetzt:

1. Im Azure-Portal die Web App öffnen.
2. Unter *Einstellungen* auf *Umgebungsvariablen* (früher *Konfiguration*) gehen und den Reiter *App-Einstellungen* öffnen.
3. *Hinzufügen* wählen: Name `PASSWORD`, Wert = mein GitHub-Benutzername (gleicher Wert wie in der lokalen `.env`, ohne Anführungszeichen).
4. *Übernehmen* bzw. *Speichern* und die Web App neu starten.

Azure stellt die App-Einstellungen der Applikation als Umgebungsvariablen zur Verfügung, `os.getenv("PASSWORD")` liest sie daher direkt.

Die Auslieferung läuft über eine GitHub Action, die bei jedem Push auf `main` (also bei jedem Merge) ausgelöst wird.

