# Power BI Service Case Study

Dieses Repository enthält eine Power-BI-Fallstudie zur Analyse synthetischer Service-Case-Daten. Enthalten sind die Power-BI-Datei, die Excel-Rohdaten, Dashboard-Screenshots und eine Projektdokumentation mit den einzelnen Schritten.

## Repository verwenden

### Voraussetzungen

Zum Öffnen und Bearbeiten des Dashboards werden benötigt:

- Windows 10 oder Windows 11
- [Power BI Desktop](https://www.microsoft.com/power-platform/products/power-bi/desktop)
- optional Git für das Klonen des Repositories

Power BI Desktop kann kostenlos von Microsoft heruntergeladen und installiert werden. Ein Power-BI-Pro-Account ist für die lokale Nutzung der `.pbix`-Datei nicht erforderlich.

### Repository klonen

Das Repository kann über PowerShell, die Windows-Eingabeaufforderung oder Git Bash geklont werden:

```bash
git clone https://github.com/eibrahi/power-bi-service-case-study.git
cd power-bi-service-case-study
```

Alternativ kann das Repository auf GitHub über **Code → Download ZIP** heruntergeladen und anschließend entpackt werden.

### Dashboard öffnen

1. Power BI Desktop installieren und starten.
2. Im geklonten oder entpackten Repository den Ordner `dashboard` öffnen.
3. Die Datei `Service_Case_Analyse.pbix` mit Power BI Desktop öffnen.
4. Falls Power BI nach dem Pfad der Datenquelle fragt, die Excel-Datei aus dem Ordner `data` auswählen.
5. In Power BI über **Start → Aktualisieren** die Daten neu laden.

### Berichtsseiten

Die `.pbix`-Datei enthält drei Seiten:

- **Management Overview**
- **Operative Analyse**
- **Datenqualität**

Die Seiten können über die Reiter am unteren Rand von Power BI geöffnet werden. Filter und Diagramme sind interaktiv und wirken auf die jeweils verbundenen Visualisierungen.

### Repository-Struktur

```text
power-bi-service-case-study/
├── README.md
├── dashboard/
│   └── Service_Case_Analyse.pbix
├── data/
│   └── Fallstudie_Rohdaten_Service_Cases.xlsx
└── screenshots/
    ├── 01_management_overview.png
    ├── 02_operativ.png
    └── 03_datenqualitaet.png
```

## Dokumentation

## 1. Zielsetzung

Das Power-BI-Dashboard analysiert synthetische Service-Case-Daten eines international tätigen Unternehmens. Ziel ist es, den Bearbeitungsstand, die Terminsituation, die Kostenverteilung und die Datenqualität transparent darzustellen.

Der Bericht besteht aus drei Seiten:

1. **Management Overview** – zentrale Kennzahlen und Gesamtüberblick
2. **Operative Analyse** – Detailanalyse nach Region, Priorität und Fehlerstruktur
3. **Datenqualität** – Übersicht über fehlende, doppelte und widersprüchliche Daten

Die Rohdatentabelle enthält 200 Zeilen. Da vier Case-IDs jeweils doppelt vorkommen, stehen für die operative Analyse 196 eindeutige Servicefälle zur Verfügung. Die Datenqualitätsseite verwendet weiterhin alle 200 Rohzeilen, damit die Dubletten sichtbar bleiben.

Als Berichtsdatum wird der **15.06.2026** verwendet. Dieses Datum entspricht dem maximalen Erfassungsdatum im Datensatz. Ein Fall gilt als überfällig, wenn sein Status nicht `Abgeschlossen` ist und sein Zieltermin vor diesem Berichtsdatum liegt.

---

## 2. Management Overview

![Management Overview](../screenshots/01_management_overview.png)

### Zweck

Die Seite richtet sich an Führungskräfte und bietet einen kompakten Überblick über Fallbestand, Bearbeitungsfortschritt, Terminsituation und Kosten.

Über die Filter **Region**, **Standort**, **Priorität**, **Produktgruppe** und **Zeitraum** kann die Analyse auf bestimmte Bereiche eingeschränkt werden.

### Zentrale Kennzahlen

| Kennzahl | Wert | Bedeutung |
|---|---:|---|
| Anzahl Cases | 196 | Eindeutige Servicefälle nach der Dublettenbereinigung |
| Offener Bestand | 134 | Alle Cases, deren Status nicht `Abgeschlossen` ist |
| Abschlussquote | 31,6 % | Anteil der 62 abgeschlossenen Cases an allen 196 Cases |
| Überfällige offene Cases | 111 | Offene Cases mit Zieltermin vor dem 15.06.2026 |
| Gesamtkosten | ca. 375.467 EUR | Summe der vorhandenen Kostenwerte |

Der offene Bestand umfasst die Statuswerte `Offen`, `In Bearbeitung`, `Warten auf Rückmeldung` und `Eskaliert`. Von den 134 offenen Cases sind 111 überfällig. Das entspricht ungefähr 82,8 % des offenen Bestands und deutet auf einen erheblichen Bearbeitungsrückstand hin.

Die Gesamtkosten können aufgrund von fünf fehlenden Kostenwerten unvollständig sein. Sie entsprechen daher der Summe der dokumentierten Kosten und nicht zwingend den vollständig entstandenen Kosten.

### Offener Bestand nach Region

Das Diagramm zeigt, in welchen Regionen sich die noch nicht abgeschlossenen Fälle konzentrieren. `Nord` besitzt den höchsten offenen Bestand, gefolgt von `Sued`. Fälle ohne zuordenbaren Standort erscheinen unter `(Leer)`.

Die absoluten Werte sollten für eine Kapazitätsbeurteilung zusätzlich in Relation zum gesamten Fallaufkommen und zur monatlichen Kapazität der Standorte gesetzt werden.

### Gesamtkosten nach Fehlerkategorie

Die Fehlerkategorie `Prozess` verursacht mit ungefähr 112.673 EUR das höchste Kostenvolumen. Danach folgen insbesondere `Software` und `Elektrik`.

Prozessbezogene Servicefälle stellen damit den größten Kostenblock dar und sollten hinsichtlich wiederkehrender Ursachen und möglicher Prozessverbesserungen vertieft untersucht werden.

### Anzahl Cases nach Monat

Das Liniendiagramm zeigt die Anzahl neu erfasster Servicefälle pro Monat. Damit wird die Entwicklung des Case-Eingangs im Zeitverlauf sichtbar.

Da der Datenstand am 15.06.2026 endet, ist Juni kein vollständiger Monat. Ein niedriger Juniwert darf deshalb nicht ohne weitere Prüfung als tatsächlicher Rückgang interpretiert werden.

### Standorte nach offenem Bestand

Das Diagramm zeigt die Standorte mit den meisten nicht abgeschlossenen Fällen. Besonders auffällig sind Frankfurt, Hannover, Lyon, Berlin Nord, Dortmund und Leipzig.

Die Darstellung unterstützt die operative Priorisierung von Standorten mit einem hohen Bearbeitungsrückstand. Für eine abschließende Bewertung sollte auch die jeweilige Standortkapazität berücksichtigt werden.

### Anzahl Cases nach Status

| Status | Anzahl Cases |
|---|---:|
| Abgeschlossen | 62 |
| In Bearbeitung | 58 |
| Offen | 43 |
| Warten auf Rückmeldung | 20 |
| Eskaliert | 13 |
| **Gesamt** | **196** |

Besondere Aufmerksamkeit verdienen die 13 eskalierten Fälle sowie die 20 Fälle, bei denen eine Rückmeldung aussteht.

---

## 3. Operative Analyse

![Operative Analyse](../screenshots/02_operativ.png)

### Zweck

Die Seite richtet sich an Serviceleitung, Teamleitung und operative Verantwortliche. Sie ermöglicht eine detailliertere Untersuchung nach Region, Standort, Status, Priorität, Produktgruppe und Fehlerkategorie.

### Cases nach Region und Status

Die Matrix verteilt die 196 eindeutigen Cases nach Region und Status. Regionen können bis auf Standortebene aufgeklappt werden.

Wesentliche Beobachtungen:

- `Sued` besitzt mit 34 Cases das höchste gesamte Fallaufkommen.
- `Nord` folgt mit 31 Cases.
- `West` besitzt 29 Cases, davon 15 abgeschlossene Fälle.
- `Suedwest` weist vier eskalierte Fälle auf.
- Fünf Cases können wegen fehlender Standortangaben keiner Region zugeordnet werden.

### Überfällige Cases nach Priorität

Die 111 überfälligen offenen Cases verteilen sich ungefähr wie folgt:

| Priorität | Überfällige Cases |
|---|---:|
| B | 40 |
| A | 36 |
| C | 35 |

Die fachliche Rangfolge der Prioritäten ist in den Quelldaten nicht definiert. Für eine risikobasierte Bewertung müsste diese Rangfolge fachlich bestätigt werden.

### Fehlerkategorie nach Produktgruppe

Das 100-%-gestapelte Balkendiagramm zeigt, wie sich die Produktgruppen `Alpha`, `Beta`, `Delta`, `Epsilon` und `Gamma` innerhalb der einzelnen Fehlerkategorien verteilen.

Erkennbar ist unter anderem:

- Prozessfälle betreffen alle Produktgruppen.
- Logistikfälle konzentrieren sich vergleichsweise stark auf `Delta`.
- Softwarefälle besitzen einen hohen Anteil der Produktgruppe `Alpha`.
- Die Zusammensetzung unterscheidet sich je Fehlerkategorie.

Das Diagramm zeigt relative Anteile. Die absolute Größe einer Fehlerkategorie muss deshalb ergänzend über die Case-Anzahl beurteilt werden.

### Case-Detailtabelle

Die Detailtabelle zeigt einzelne Servicefälle mit Case-ID, Masterstandort, Status, Priorität, Zieltermin, Ist-Fertigstellungsdatum, Aufwand und Kosten.

Sie ermöglicht den Wechsel von der aggregierten Darstellung zum einzelnen Case. Nach Auswahl einer Region oder Priorität können die betroffenen Vorgänge direkt identifiziert und operativ geprüft werden.

---

## 4. Datenqualität

![Datenqualität](../screenshots/03_datenqualitaet.png)

### Zweck

Die Seite zeigt Einschränkungen der Rohdaten und schafft Transparenz über die Belastbarkeit der Managementkennzahlen. Sie basiert auf allen 200 Rohzeilen.

### Zentrale Qualitätskennzahlen

| Qualitätsproblem | Anzahl | Auswirkung |
|---|---:|---|
| Fehlende Standorte | 5 | Keine Standort- oder Regionszuordnung möglich |
| Fehlende Kosten | 5 | Gesamtkosten möglicherweise untererfasst |
| Zusätzliche Dublettenzeilen | 4 | Gefahr einer Mehrfachzählung |
| Fehlender Aufwand | 10 | Aufwand und Kapazitätsbedarf möglicherweise untererfasst |
| Status-Datum-Widersprüche | 11 | Nicht abgeschlossene Cases besitzen ein Ist-Fertigstellungsdatum |

### Fehlende Standorte

Fünf Datensätze besitzen keinen Standortwert. Dadurch können sie keinem Masterstandort, keiner Region und keinen Standortstammdaten wie Kapazität, Budget oder Kostenstelle zugeordnet werden.

### Fehlende Kosten und Aufwände

Fünf Datensätze enthalten keine Kostenangabe und zehn Datensätze keinen Aufwand. Die Werte wurden nicht durch null ersetzt, da null einen bekannten Wert von null Euro beziehungsweise null Stunden bedeuten würde. Ein leerer Wert steht dagegen für eine unbekannte oder nicht gepflegte Angabe.

### Dubletten

Die Rohdatentabelle enthält 200 Zeilen, aber nur 196 eindeutige Case-IDs. Daraus ergeben sich vier zusätzliche Dublettenzeilen.

Die betroffenen Zeilen sind nicht vollständig identisch. Für die operative Analyse wurde pro Case-ID nach einer dokumentierten Quellenpriorität ein Datensatz ausgewählt. In der Qualitätsanalyse bleiben alle Vorkommen sichtbar.

### Status-Datum-Widersprüche

Elf Cases besitzen ein Ist-Fertigstellungsdatum, obwohl ihr Status nicht `Abgeschlossen` lautet. Dies kann auf eine fehlende Statusaktualisierung, ein falsch gepflegtes Datum oder unterschiedliche Datenstände der Quellsysteme hinweisen.

Die betroffenen Werte wurden nicht automatisch korrigiert, da ohne fachliche Geschäftsregel nicht eindeutig entschieden werden kann, welches Feld korrekt ist.

### Qualitätsdetailtabelle

Die Detailtabelle zeigt pro Rohdatensatz die Case-ID, Quelle, Standortangabe, den Dublettenstatus und die einzelnen Datenqualitätskennzeichen. Damit kann jede Qualitätskennzahl bis zum betroffenen Datensatz zurückverfolgt werden.

Die Summe `DQ_Anzahl_Probleme` beschreibt die Gesamtzahl erkannter Qualitätsverletzungen. Sie ist nicht mit der Anzahl betroffener Cases gleichzusetzen, da ein Case mehrere Probleme gleichzeitig besitzen kann.

---

## 5. Zusammenspiel der Dashboardseiten

| Seite | Kernfrage | Zielgruppe |
|---|---|---|
| Management Overview | Wie ist die aktuelle Gesamtsituation? | Management |
| Operative Analyse | Wo entstehen Rückstände und Auffälligkeiten? | Service- und Teamleitung |
| Datenqualität | Wie belastbar sind die Kennzahlen? | BI, Data Owner und Management |

Der typische Analyseablauf ist:

1. Eine Auffälligkeit wird auf der Managementseite erkannt.
2. Die operative Seite grenzt die betroffenen Regionen, Prioritäten oder Fehlerkategorien ein.
3. Die Detailtabelle identifiziert einzelne Case-IDs.
4. Die Datenqualitätsseite zeigt, ob fehlende oder widersprüchliche Werte die Aussage einschränken.

---

## 6. Zentrale Erkenntnisse

- Von 196 eindeutigen Servicefällen sind 134 noch nicht abgeschlossen.
- Die Abschlussquote beträgt 31,6 %.
- Zum Datenstand vom 15.06.2026 sind 111 offene Cases überfällig.
- Prozessbezogene Fehler verursachen das höchste Kostenvolumen.
- Der offene Bestand konzentriert sich besonders auf die Regionen `Nord` und `Sued`.
- Mehrere Standorte besitzen einen auffällig hohen offenen Bestand.
- Die Datenbasis enthält relevante Qualitätsprobleme, die bei der Interpretation berücksichtigt werden müssen.

Zusammenfassende Managementaussage:

> Der Bericht zeigt einen hohen offenen Bestand und eine große Anzahl überfälliger Servicefälle. Besonders die Regionen Nord und Süd sowie Standorte mit hohem Bearbeitungsrückstand sollten operativ untersucht werden. Prozessbezogene Fehler stellen den größten Kostenblock dar. Gleichzeitig müssen die Ergebnisse unter Berücksichtigung der dokumentierten Datenqualitätsprobleme interpretiert werden.

---

## 7. Annahmen und Einschränkungen

- Die Daten sind synthetisch und bilden keine reale Unternehmensperformance ab.
- Das Berichtsdatum wurde aus dem maximalen Erfassungsdatum abgeleitet.
- Die Quellenpriorität zur Dublettenbehandlung ist eine methodische Annahme und nicht fachlich bestätigt.
- Die Rangfolge der Prioritäten `A`, `B` und `C` ist nicht definiert.
- Fehlende Werte wurden nicht automatisch durch null ersetzt.
- Auffällige Kosten- und Aufwandswerte wurden nicht pauschal gelöscht.
- Der Juni 2026 ist nur bis zum 15.06.2026 enthalten.
- Ohne fachliche Sollwerte kann aus der Abschlussquote allein keine abschließende Leistungsbewertung abgeleitet werden.

---

## 8. Fazit

Das Dashboard verbindet Managementübersicht, operative Detailanalyse und Datenqualitätskontrolle in einem Bericht. Dadurch können Auffälligkeiten nicht nur erkannt, sondern bis auf Regionen, Standorte und einzelne Servicefälle zurückverfolgt werden.

Die Datenqualitätsseite macht gleichzeitig transparent, welche Einschränkungen bei der Interpretation bestehen. Die dargestellten Ergebnisse bilden damit eine nachvollziehbare Grundlage für weitere fachliche Prüfungen und operative Entscheidungen.
