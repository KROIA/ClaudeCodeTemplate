# Claude Startvorlagen


## Vorlage verwenden
Für die Verwendung einer der Vorlagen in diesem ordner, werden folgende Schritte benötigt.
- Kopiere `CLAUDE.md` in das Hauptverzeichnis deines Projektes — Claude Code lädt sie beim Sessionstart automatisch.
- Starte claude in dem Verzeichnis wo sich die `CLAUDE.md` Datei befindet. 
- Sende claude einen startbefehl um das Setup zu starten. Beispiel: `init`

Claude wird selbständig das Projekt aufsetzen und während des Setups bestimmte Dinge fragen, die relevant für das Aufsetzen sind.

---

## Vorlagen
### ClaudeProjectManagerSetup

Vorlage, um Claude Code in einem neuen Projekt als **Projektmanager-Agent** einzurichten.

Die Datei [`ClaudeProjectManagerSetup/CLAUDE.md`](./ClaudeProjectManagerSetup/CLAUDE.md) enthält die vollständige Setup-Spezifikation. 



**Was die Vorlage macht:**
- Definiert die Rolle als Projektmanager (Modell: Opus, Planung und Orchestrierung von Subagenten, keine eigene Code-Implementierung).
- Initialisiert beim ersten Start die Projektstruktur unter `.claude/` (PROJECT_STATUS, ISSUES, TASKS, UNIT_TEST_TASKS, settings.json) und `.claude/ProjectManager/` (PROJECT_SUMMARY, CODING_STYLE, FINISHED_TASKS, DECISIONS, GLOSSARY, PREFERENCES, agents/).
- Fragt vor jeder Strukturänderung beim Nutzer nach und speichert Präferenzen dauerhaft.
- Legt Subagenten-Vorlagen an (Code Review, Unit Tests, API-Doku, Security Audit, Migration, Performance u. a.) mit Agent-Contracts.
- Schema für Tasks und Issues inkl. Hotfix-Spur, Status-Lifecycle, Definition of Done mit Checkboxen, Prioritätsraster.
- Versionsverwaltung mit zweistufigen Berechtigungen (Commit / Push), Commit-Message-Stil (`+`, `~`, `-`), Branch-Policy.
- Release-Workflow mit automatischer Versionserkennung, Changelog-Pflege (Features / Bugfixes / Deprecations) und Projekt-Cleanup.
- Konflikt- und Fehlerbehandlung für Subagenten, strikte Secrets-Policy.
- **Selbstüberschreibung nach Init:** Nach abgeschlossener Einrichtung ersetzt der Projektmanager den Inhalt der `CLAUDE.md` durch eine schlanke Runtime-Briefing-Version, damit nicht in jeder Session die komplette Setup-Spezifikation in den Kontext geladen wird.
