# Power BI Service Case Study

## Projektübersicht

Diese Fallstudie analysiert synthetische Service-Case-Daten eines international aufgestellten Unternehmens. Ziel war es, die bereitgestellten Rohdaten zunächst systematisch auf Datenqualitätsprobleme zu prüfen, die operativen Servicefälle mit den Standortstammdaten zu verbinden und die Ergebnisse anschließend in einem managementgerechten Power-BI-Dashboard aufzubereiten.

Die Analyse beantwortet insbesondere folgende Fragen:

- Wie zuverlässig und vollständig ist die vorhandene Datenbasis?
- Wie viele Servicefälle sind offen, abgeschlossen, eskaliert oder überfällig?
- Welche Standorte, Regionen und Fehlerkategorien verursachen das höchste Fallaufkommen?
- Wo entstehen besonders hohe Kosten und Aufwände?
- Wie termintreu werden Servicefälle bearbeitet?
- Welche Einschränkungen müssen bei der Interpretation der Ergebnisse berücksichtigt werden?

Die Quelldaten sind laut Aufgabenstellung vollständig synthetisch und haben keinen direkten Bezug zu einem realen Unternehmen.

## Ausgangsdaten

Die Excel-Arbeitsmappe enthält drei Tabellenblätter:

### `Service_Cases`

Die operative Haupttabelle enthält 200 Datensätze und 18 Spalten. Jede Zeile repräsentiert grundsätzlich einen Servicefall. Enthalten sind unter anderem:

- Case-ID und Eingangsquelle
- Standort in uneinheitlicher Rohschreibweise
- Produktgruppe, Priorität und Fehlerkategorie
- Erfassungs-, Ziel- und Ist-Fertigstellungsdatum
- Bearbeitungsstatus
- Aufwand und Kosten
- Kundensegment und Komplexität
- verantwortlicher Bereich, Ursache-Code und Bemerkung

### `Standorte`

Die Stammdatentabelle enthält 20 Standorte mit folgenden Merkmalen:

- eindeutige Standort-ID
- standardisierter Standortname
- Land und Region
- organisatorisch verantwortlicher Bereich
- monatliche Fallkapazität und Monatsbudget
- Kostenstelle und Manager-Code

### `Hinweise`

Der Hinweisreiter erläutert, dass Inkonsistenzen bewusst eingebaut wurden. Erwartet werden keine perfekte fachliche Bereinigung, sondern ein strukturiertes Vorgehen, eine nachvollziehbare Excel-/BI-Methodik und eine managementgerechte Verdichtung.

## Projektstruktur

```text
power-bi-service-case-study/
├── README.md
├── dashboard/
│   └── Service_Case_Analyse.pbix
├── data/
│   └── Fallstudie_Rohdaten_Service_Cases.xlsx
├── documentation/
├── exports/
│   └── Service_Case_Analyse.pdf
└── screenshots/
    ├── 01_management_overview.png
    ├── 02_operativ.png
    └── 03_datenqualitaet.png
```

## Vorgehensweise

Die Umsetzung erfolgte in sechs aufeinander aufbauenden Schritten:

1. Rohdaten unverändert einlesen und absichern
2. Datenqualität profilieren und Fehler kennzeichnen
3. Standort- und Organisationswerte standardisieren
4. Dubletten analysieren und eine eindeutige Analysetabelle erzeugen
5. Datenmodell und DAX-Kennzahlen erstellen
6. Ergebnisse in drei Berichtsseiten visualisieren

## Datenaufbereitung in Power Query

### Rohdatenabfragen

Die Excel-Tabellen wurden zunächst als Rohdatenabfragen importiert:

- `Raw_Service_Cases`
- `Raw_Standorte`

Für diese Abfragen wurde das Laden ins Datenmodell deaktiviert. Sie dienen ausschließlich als unveränderte Ausgangsschicht. Die weiteren Abfragen wurden über Verweise aufgebaut, sodass die Transformationen nachvollziehbar bleiben.

Die relevanten Folgeabfragen sind:

- `Fact_Service_Cases`: vollständige Case-Tabelle einschließlich Datenqualitätskennzeichen
- `Fact_Service_Cases_Analyse`: deduplizierte Tabelle für die operative Analyse
- `Dim_Standorte`: bereinigte Standortdimension
- `Mapping_Standorte`: kontrollierte Zuordnung von Rohwerten zu Masterstandorten
- `Check_Dubletten`: Anzahl der Datensätze je Case-ID

### Datentypen und Textbereinigung

Die Spalten wurden explizit typisiert:

- IDs, Kategorien und Codes als Text
- Datumsfelder als Datum
- `Aufwand_Std` und `Kosten_EUR` als Dezimalzahlen
- Datenqualitätskennzeichen als ganze Zahlen

Relevante Textspalten wurden in Power Query mit **Kürzen** und **Bereinigen** verarbeitet. Fehlende Werte wurden nicht pauschal ersetzt, da ihr Fehlen selbst eine relevante Qualitätsinformation darstellt.

## Datenqualitätsprüfung

### Festgestellte Auffälligkeiten

| Qualitätsprüfung | Ergebnis |
|---|---:|
| Rohdatensätze | 200 |
| Eindeutige Case-IDs | 196 |
| Doppelt vorkommende Case-IDs | 4 |
| Fehlende Standorte | 5 |
| Fehlender Aufwand | 10 |
| Fehlende Kosten | 5 |
| Fehlende Ursache-Codes | 30 |
| Fehlende Bemerkungen | 27 |
| Fehlende Ist-Fertigstellung | 126 |
| Zieltermin vor Erfassungsdatum | 3 |
| Ist-Fertigstellung vor Erfassungsdatum | 1 |
| Nicht abgeschlossene Fälle mit Ist-Fertigstellung | 11 |
| Kosten-Ausreißer nach IQR-Methode | 9 |
| Aufwand-Ausreißer nach IQR-Methode | 1 |

Ein fehlendes Ist-Fertigstellungsdatum ist bei einem offenen Fall nicht automatisch ein Fehler. Es wurde deshalb im Kontext des Status bewertet.

### Datenqualitätskennzeichen

Anstatt problematische Zeilen sofort zu löschen, wurden separate Prüfkennzeichen angelegt:

- `DQ_Fehlender_Standort`
- `DQ_Fehlender_Aufwand`
- `DQ_Fehlende_Kosten`
- `DQ_Fehlender_Ursache`
- `DQ_Ziel_vor_Erfassung`
- `DQ_Ist_vor_Erfassung`
- `DQ_Status_Datum_Widerspruch`
- `DQ_Dublette`
- `DQ_Anzahl_Probleme`
- `DQ_Status`

Beispiel für einen Status-Datum-Widerspruch in Power Query:

```powerquery
if [Status] <> "Abgeschlossen"
    and [Ist_Fertigstellung] <> null
then 1
else 0
```

Die Summe der einzelnen Kennzeichen bildet `DQ_Anzahl_Probleme`. Fälle mit mindestens einem Problem erhalten den Status `Prüfbedarf`, alle anderen `Ohne Befund`.

## Standortstandardisierung

Die Servicefälle enthalten keine verlässliche Standort-ID, sondern unterschiedliche Namen, Abkürzungen und Sprachvarianten. Beispiele:

| Rohwerte | Masterwert |
|---|---|
| Köln, Koeln, Cologne | Koeln |
| Muenchen, Munich, MUC | Muenchen |
| Stuttgart, Stuttg., STR | Stuttgart |
| Prag, Prague, Praha | Prag |
| Warschau, Warsaw, Warszawa | Warschau |

Zur Harmonisierung wurde die Tabelle `Mapping_Standorte` erstellt. `Fact_Service_Cases` wurde per linkem äußeren Join über `Standort_Roh` mit dieser Mappingtabelle verbunden. Anschließend wurde der standardisierte `Standort_Master` mit `Dim_Standorte` verknüpft und die `Standort_ID` übernommen.

Das Ergebnis des Mapping-Joins war:

- 195 von 200 Zeilen konnten einem Masterstandort zugeordnet werden.
- Die verbleibenden fünf Zeilen besitzen keinen Standort-Rohwert und wurden als Qualitätsproblem gekennzeichnet.
- Fuzzy Matching wurde bewusst nicht als finale Logik verwendet, da ein explizites Mapping reproduzierbarer und auditierbarer ist.

## Standardisierung der Verantwortungsbereiche

Auch die verantwortlichen Bereiche lagen in unterschiedlichen Schreibweisen vor:

| Rohwert | Standardwert |
|---|---|
| `Service Ops` | `Service Operations` |
| `Field-Service` | `Field Service` |
| `Cust. Service` | `Customer Service` |
| `QS` | `Quality Support` |

Die Rohspalte wurde beibehalten und zusätzlich eine bereinigte Spalte erzeugt. Abweichungen zwischen der fallbezogenen Zuständigkeit und dem Stammdatenbereich eines Standorts wurden nicht automatisch überschrieben. Ohne fachliche Rücksprache ist nicht eindeutig, ob es sich dabei um einen Datenfehler oder eine legitime abweichende Fallzuständigkeit handelt.

## Dublettenbehandlung

Folgende Case-IDs kommen jeweils zweimal vor:

- `SC-2026-0023`
- `SC-2026-0086`
- `SC-2026-0139`
- `SC-2026-0174`

Es handelt sich nicht um vollständig identische Zeilen. Die Datensätze unterscheiden sich teilweise bei Quelle oder Bemerkung. Deshalb wurden sie zunächst in der Qualitätsanalyse beibehalten und gekennzeichnet.

Für die operative Analysetabelle wurde pro Case-ID ein Datensatz ausgewählt. Dafür wurde folgende angenommene Quellenpriorität verwendet:

| Quelle | Priorität |
|---|---:|
| ERP Export | 1 |
| Portal | 2 |
| Partner Upload | 3 |
| E-Mail Import | 4 |
| Manuelle Liste | 5 |

Power-Query-Logik:

```powerquery
if [Quelle] = "ERP Export" then 1
else if [Quelle] = "Portal" then 2
else if [Quelle] = "Partner Upload" then 3
else if [Quelle] = "E-Mail Import" then 4
else if [Quelle] = "Manuelle Liste" then 5
else 99
```

Die Tabelle wurde nach `Case_ID` und `Quellenprioritaet` sortiert, gepuffert und anschließend anhand der `Case_ID` dedupliziert. Dadurch entstanden 196 eindeutige Servicefälle.

Diese Quellenpriorisierung ist eine methodische Annahme der Fallstudie. In einem produktiven System müsste die Golden-Record-Regel mit den fachlich Verantwortlichen abgestimmt werden.

## Behandlung von Ausreißern

Ausreißer wurden über die Interquartilsabstandsmethode geprüft:

```text
IQR = Q3 - Q1
Untere Grenze = Q1 - 1,5 × IQR
Obere Grenze = Q3 + 1,5 × IQR
```

Für `Kosten_EUR` lag die obere Grenze bei ungefähr 4.609 EUR. Neun Datensätze lagen darüber. Beim Aufwand wurde ein Fall als IQR-Ausreißer identifiziert.

Die betroffenen Datensätze wurden nicht automatisch entfernt. Ein hoher Aufwand oder hohe Kosten können geschäftlich reale und besonders relevante Fälle darstellen. Ausreißer sind daher als Prüf- und Analysemerkmal zu verstehen, nicht automatisch als fehlerhafte Daten.

## Datenmodell

Das Power-BI-Modell wurde als vereinfachtes Sternschema aufgebaut:

- `Fact_Service_Cases_Analyse` als Faktentabelle
- `Dim_Standorte` als Standortdimension
- `Dim_Datum` als zentrale Datumstabelle

Beziehungen:

| Dimension | Faktentabelle | Kardinalität | Status |
|---|---|---|---|
| `Dim_Standorte[Standort_ID]` | `Fact_Service_Cases_Analyse[Standort_ID]` | 1:n | aktiv |
| `Dim_Datum[Date]` | `Fact_Service_Cases_Analyse[Erfassungsdatum]` | 1:n | aktiv |
| `Dim_Datum[Date]` | `Fact_Service_Cases_Analyse[Zielfertigstellung]` | 1:n | inaktiv |
| `Dim_Datum[Date]` | `Fact_Service_Cases_Analyse[Ist_Fertigstellung]` | 1:n | inaktiv |

Die Filterrichtung ist jeweils einzeln von der Dimension zur Faktentabelle. Viele-zu-viele-Beziehungen wurden vermieden.

### Datumstabelle

```DAX
Dim_Datum =
ADDCOLUMNS(
    CALENDAR(DATE(2026, 1, 1), DATE(2026, 12, 31)),
    "Jahr", YEAR([Date]),
    "Monatsnummer", MONTH([Date]),
    "Monat", FORMAT([Date], "MMM"),
    "JahrMonat", FORMAT([Date], "YYYY-MM"),
    "Quartal", "Q" & FORMAT([Date], "Q")
)
```

`Dim_Datum` wurde in Power BI als Datumstabelle markiert. Die aktive Beziehung über das Erfassungsdatum steuert die Standard-Zeitanalyse. Ziel- und Ist-Datum stehen über inaktive Beziehungen für spezifische Measures zur Verfügung.

## Berichtsdatum

Da kein expliziter Datenstichtag vorgegeben wurde, wird das maximale Erfassungsdatum des Datensatzes als Berichtsdatum verwendet:

```DAX
Berichtsdatum =
CALCULATE(
    MAX(Fact_Service_Cases_Analyse[Erfassungsdatum]),
    REMOVEFILTERS(Fact_Service_Cases_Analyse)
)
```

Das Ergebnis ist der 15.06.2026. Diese Annahme verhindert, dass offene Fälle anhand des jeweils aktuellen Systemdatums nachträglich als überfällig eingestuft werden.

## Zentrale DAX-Kennzahlen

### Anzahl eindeutiger Cases

```DAX
Anzahl Cases =
DISTINCTCOUNT(Fact_Service_Cases_Analyse[Case_ID])
```

### Abgeschlossene Cases

```DAX
Abgeschlossene Cases =
CALCULATE(
    [Anzahl Cases],
    Fact_Service_Cases_Analyse[Status] = "Abgeschlossen"
)
```

### Offener Bestand

```DAX
Offener Bestand =
CALCULATE(
    [Anzahl Cases],
    Fact_Service_Cases_Analyse[Status] <> "Abgeschlossen"
)
```

### Abschlussquote

```DAX
Abschlussquote =
DIVIDE([Abgeschlossene Cases], [Anzahl Cases])
```

### Gesamtkosten

```DAX
Gesamtkosten =
SUM(Fact_Service_Cases_Analyse[Kosten_EUR])
```

### Gesamtaufwand

```DAX
Gesamtaufwand Stunden =
SUM(Fact_Service_Cases_Analyse[Aufwand_Std])
```

### Überfällige offene Cases

```DAX
Überfällige offene Cases =
VAR Stichtag = [Berichtsdatum]
RETURN
    CALCULATE(
        [Anzahl Cases],
        FILTER(
            Fact_Service_Cases_Analyse,
            Fact_Service_Cases_Analyse[Status] <> "Abgeschlossen"
                && NOT ISBLANK(Fact_Service_Cases_Analyse[Zielfertigstellung])
                && Fact_Service_Cases_Analyse[Zielfertigstellung] < Stichtag
        )
    )
```

### Verspätet abgeschlossene Cases

```DAX
Verspätet abgeschlossen =
CALCULATE(
    [Anzahl Cases],
    FILTER(
        Fact_Service_Cases_Analyse,
        Fact_Service_Cases_Analyse[Status] = "Abgeschlossen"
            && Fact_Service_Cases_Analyse[Ist_Fertigstellung]
                > Fact_Service_Cases_Analyse[Zielfertigstellung]
    )
)
```

### Termintreue

```DAX
Termintreue =
DIVIDE(
    [Abgeschlossene Cases] - [Verspätet abgeschlossen],
    [Abgeschlossene Cases]
)
```

### Durchschnittliche Durchlaufzeit

```DAX
Durchschnittliche Durchlaufzeit =
AVERAGEX(
    FILTER(
        Fact_Service_Cases_Analyse,
        Fact_Service_Cases_Analyse[Status] = "Abgeschlossen"
            && NOT ISBLANK(Fact_Service_Cases_Analyse[Ist_Fertigstellung])
    ),
    DATEDIFF(
        Fact_Service_Cases_Analyse[Erfassungsdatum],
        Fact_Service_Cases_Analyse[Ist_Fertigstellung],
        DAY
    )
)
```

## Dashboardaufbau

### Seite 1: Management Overview

Die Übersichtsseite verdichtet die wichtigsten Managementinformationen.

KPI-Karten:

- Anzahl eindeutiger Cases
- offener Bestand
- Abschlussquote
- überfällige offene Cases
- Gesamtkosten
- dynamischer Datenstand

Visualisierungen:

- Cases nach Status
- neu erfasste Cases nach Monat
- offene Cases nach Region
- Top-5-Standorte nach offenem Bestand
- Kosten nach Fehlerkategorie

Slicer:

- Zeitraum
- Region
- Standort
- Priorität
- Produktgruppe

### Seite 2: Operative Analyse

Die operative Seite ermöglicht eine detailliertere Ursachen- und Standortanalyse.

Visualisierungen:

- Matrix Region → Standort → Status
- überfällige Cases nach Priorität
- Fehlerkategorien nach Produktgruppe
- Aufwand-Kosten-Streudiagramm nach Komplexität
- Case-Detailtabelle mit Status, Terminen, Aufwand und Kosten

Für überfällige und eskalierte Fälle wird bedingte Formatierung eingesetzt.

### Seite 3: Datenqualität

Die Qualitätsseite basiert auf der vollständigen Tabelle mit 200 Rohdatensätzen. Sie macht transparent, welche Probleme vor der Managementanalyse berücksichtigt wurden.

KPI-Karten:

- zusätzliche Dublettenzeilen
- fehlende Standorte
- fehlende Aufwände
- fehlende Kosten
- Datumsfehler
- Status-Datum-Widersprüche

Visualisierungen:

- Qualitätsprobleme nach Art
- Qualitätsprobleme nach Eingangsquelle
- Detailtabelle mit Case-ID und sämtlichen DQ-Kennzeichen
- Gegenüberstellung von Standort-Rohwert und Masterstandort

## Zentrale Ergebnisse

Nach der beschriebenen Dublettenbehandlung ergeben sich ungefähr folgende Kennzahlen:

| Kennzahl | Ergebnis |
|---|---:|
| Eindeutige Servicefälle | 196 |
| Abgeschlossene Cases | 62 |
| Offener Bestand | 134 |
| Abschlussquote | 31,6 % |
| Überfällige offene Cases zum Datenstand | 111 |
| Gesamtkosten | ca. 375.467 EUR |
| Gesamtaufwand | ca. 2.621 Stunden |
| Verspätet abgeschlossene Cases | 39 |
| Durchschnittliche Durchlaufzeit | ca. 24,8 Tage |

Weitere Beobachtungen:

- Die Fehlerkategorie `Prozess` verursacht mit ungefähr 112.673 EUR das höchste Kostenvolumen.
- `Koeln` weist mit 15 eindeutigen Cases das höchste Fallaufkommen auf.
- `Barcelona` fällt mit vier eskalierten Fällen auf.
- Ein großer Anteil der offenen Fälle hat den Zieltermin zum angenommenen Datenstand bereits überschritten.
- Die Datenqualität variiert und muss bei jeder Managementaussage berücksichtigt werden.

## Managementinterpretation

Der offene Bestand ist im Verhältnis zur Gesamtzahl der Cases hoch. Gleichzeitig wurde ein relevanter Anteil der abgeschlossenen Fälle erst nach dem vorgesehenen Zieltermin beendet. Daraus ergibt sich fachlicher Prüfbedarf hinsichtlich Kapazitäten, Priorisierung, Prozessqualität und möglicher Bearbeitungsengpässe.

Die Kategorie `Prozess` sollte wegen ihres hohen Kostenvolumens tiefer untersucht werden. Zusätzlich sollten Standorte mit hohem offenem Bestand oder mehreren Eskalationen priorisiert analysiert werden.

Die Ergebnisse dürfen jedoch nicht losgelöst von der Datenqualität interpretiert werden. Dubletten, fehlende Werte und Status-Datum-Widersprüche können Kennzahlen verzerren. Die separate Qualitätsseite ist deshalb ein integraler Bestandteil des Berichts und kein technischer Anhang.

## Annahmen und Einschränkungen

- Die Daten sind synthetisch und bilden keine reale Unternehmensperformance ab.
- Das Berichtsdatum wurde mangels expliziter Vorgabe aus dem maximalen Erfassungsdatum abgeleitet.
- Die Quellenpriorität zur Dublettenbehandlung ist eine Annahme und nicht fachlich bestätigt.
- Die Rangfolge der Prioritäten `A`, `B` und `C` ist in den Quelldaten nicht definiert. Es wird daher keine unbestätigte Aussage darüber getroffen, welche Klasse die höchste Priorität darstellt.
- Abweichende Verantwortungsbereiche wurden nicht automatisch durch Standortstammdaten überschrieben.
- Ausreißer wurden gekennzeichnet, aber nicht pauschal entfernt.
- Fehlende Werte wurden nur dann als Fehler bewertet, wenn sie im jeweiligen fachlichen Kontext erforderlich sind.
- Die Kapazitäts- und Budgetdaten der Standorte sind monatliche Werte. Ein belastbarer Soll-Ist-Vergleich erfordert daher eine saubere zeitliche Aggregation und zusätzliche fachliche Definitionen.
- Es liegen keine Umsatz-, Gewinn- oder Kundenzufriedenheitsdaten vor. Die Analyse bewertet primär Serviceleistung, Termintreue, Aufwand, Kosten und Datenqualität.

## Qualitätssicherung

Die Umsetzung wurde anhand folgender Kontrollwerte geprüft:

- 200 Zeilen in der vollständigen Qualitätsbasis
- 196 eindeutige Case-IDs in der operativen Analysetabelle
- 195 erfolgreiche Standortzuordnungen und fünf fehlende Standorte
- korrekte 1:n-Beziehungen von den Dimensionen zur Faktentabelle
- aktive Datumsbeziehung über `Erfassungsdatum`
- konsistente Reaktion der KPI-Karten und Diagramme auf Slicer
- unverändertes Berichtsdatum trotz Seitenfiltern
- plausible Summen für Cases, Kosten und Aufwand

## Verwendung

1. Die Excel-Quelldatei im Ordner `data` ablegen.
2. `dashboard/Service_Case_Analyse.pbix` mit Power BI Desktop öffnen.
3. Falls erforderlich, den Datenquellenpfad in Power Query anpassen.
4. **Aktualisieren** ausführen.
5. Die drei Berichtsseiten über die Seitennavigation öffnen.

Für die Betrachtung ohne Power BI Desktop steht zusätzlich ein PDF-Export im Ordner `exports` zur Verfügung.

## Fazit

Die Fallstudie zeigt einen vollständigen BI-Prozess von der Rohdatenprüfung über kontrollierte Bereinigung und Modellierung bis zur managementgerechten Visualisierung. Der Schwerpunkt liegt nicht ausschließlich auf optisch ansprechenden Diagrammen, sondern auf einer transparenten und belastbaren Datenbasis. Qualitätsprobleme und fachliche Annahmen werden sichtbar dokumentiert, sodass die dargestellten Ergebnisse nachvollziehbar eingeordnet werden können.

