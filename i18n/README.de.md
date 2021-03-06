


# Design- und Namenskonventionen für Datenbanken


## Inhaltsverzeichnis

  1. [Sprache](#sprache)
  1. [Projektpräfix](#projektpräfix)
  1. [Tabellenkürzel](#tabellenkürzel)
  1. [Spaltenkürzel](#spaltenkürzel)
  1. [Generelle Namenskonventionen](#generelle-namenskonventionen)
  1. [Tabellen](#tabellen)
  1. [Views](#views)
  1. [Trigger](#trigger)
  1. [Sequenzen](#sequenzen)
  1. [Spalten](#spalten)
  1. [Constraints](#constraints)
  1. [Indizes](#indizes)
  1. [Functions, Procedures, Types, Packages](#functions,-procedures,-types,-packages)
  1. [Synonyme](#synonyme)
  1. [Jobs](#jobs)
## Sprache
###### [Regel [R001](#regel-r001)]

Als Sprache für die Benennung der Schema-Objekte ist Englisch zu wählen.

Die Sprache für Quelltext- und Dokumentationskommentare kann unabhängig gewählt werden.

**[Zurück nach oben](#inhaltsverzeichnis)**
## Projektpräfix

###### [Regel [R002](#regel-r002)]
Es _kann_ ein Projektpräfix vereinbart werden.
Diese Präfixe werden, gefolgt von einem Unterstrich '_', jedem Tabellen- oder Viewnamen vorangestellt.
Ein Präfix besteht aus maximal 3 alphanumerischen Zeichen, beginnend mit einem Buchstaben.

_Warum?_ Projektpräfixe sind sinnvoll, um die Verwendung von Tabellen oder Views im Applikationsquelltext identifizieren zu können, insbesondere bei Nutzung von
eingebettetem SQL oder im Quelltext verteilten SQL (JDBC). Alternativ kann mitunter konsequent das Schema verwendet werden.


**[Zurück nach oben](#inhaltsverzeichnis)**
## Tabellenkürzel

###### [Regel [R003](#regel-r003)]
Für jede Tabelle _muss_ ein eindeutiges Tabellenkürzel vereinbart werden.
Alle Tabellenkürzel und Tabellennamen sind innerhalb ihres Schemas eindeutig.
Ein Tabellenkürzel besteht aus bis zu 5 alphanumerischen Zeichen, beginnend mit einem Buchstaben.
Dieses Tabellenkürzel _muss_ in den Kommentar zur Tabelle wie folgt aufgenommen werden.

  ```sql
  comment on table employee is 'abbrev=emp; ...';
  ```

Alternativ:

  ```sql
  comment on table employee is '...; abbrev=emp';
  ```


_Warum?_ Dies ermöglicht unter Anderem die automatische Zuordnung einer Sequenz zu einer Tabelle.


**[Zurück nach oben](#inhaltsverzeichnis)**
## Spaltenkürzel
###### [Regel [R004](#regel-r004)]


Für jede Spalte _kann_ ein eindeutiges Spaltenkürzel vereinbart werden.
Ein Spaltenkürzel besteht aus bis zu 6 alphanumerischen Zeichen, beginnend mit einem Buchstaben.
Dieses Spaltenkürzel _kann_ in den Kommentar zur Spalte wie folgt aufgenommen werden.

  ```sql
  comment on column employee.last_name is 'abbrev=empnam; ...';
  ```

Alternativ:

  ```sql
  comment on column employee.last_name is '...; abbrev=empnam';
  ```

**[Zurück nach oben](#inhaltsverzeichnis)**
## Generelle Namenskonventionen
###### [Regel [R005](#regel-r005)]

Alle Namen bestehen nur aus den Buchstaben 'A'-'Z', den Zahlen '0'-'9', sowie dem Unterstrich '_'.
Alle Namen beginnen mit einem Buchstaben.
Alle Namen sind höchstens 30 Zeichen lang.
Objekte sind immer case-insensitiv anzulegen.
Namen entsprechen nicht den reservierten Wörtern oder Schlüsselwörtern.

**[Zurück nach oben](#inhaltsverzeichnis)**
## Tabellen
###### [Regel [R006](#regel-r006)]

| Objekt-Typ | Regel | Beispiel |
|:---|:---|:---|
| Tabellen | \<FachlicherName\> | employee |
| Journal-Tabellen | \<FachlicherName\>_JN | employee_jn,<br />_Es existiert ebenso die Tabelle \<FachlicherName\> als Grundlage der Journals._ |
| Logging-Tabellen | \<FachlicherName\>_LOG | import_log |
| DML-Error-Logging-Tabellen | \<FachlicherName\>_ERR| debitor_err,<br />_Es existiert ebenso die Tabelle \<FachlicherName\> als Grundlage betreffender DML-Statements._ |
| Backups/Kopien | \<FachlicherName\>_BAK| debitor_bak,<br />_Es existiert ebenso die Tabelle \<FachlicherName\> als Ursprung der Kopie._ |

**[Zurück nach oben](#inhaltsverzeichnis)**
## Views

###### [Regel [R007](#regel-r007)]
| Objekt-Typ | Regel | Beispiel |
|:---|:---|:---|
| View | \<FachlicherName\>_V | employee_v,<br />_\<FachlicherName\> darf ebenso ein Tabellenname sein, muss aber nicht._ |


###### [Regel [R008](#regel-r008)]
Views werden nicht geschachtelt.

_Warum?_ ineinander geschachtelte Views führen früher oder später zu Performance-Problemen.

###### [Regel [R009](#regel-r009)]
Views sind als Teil einer Applikationszugriffsschicht verboten.

###### [Regel [R010](#regel-r010)]
Views sind erlaubt:
- als Hilfsmittel/Komfort für Entwickler oder DBA.
- zum Verbergen von Komplexität (Denormalisierung) vor Fremdsystemen.
- zum Verbergen von Informationen in Zeilen oder Spalten (Information Hiding) vor Fremdsystemen.

**[Zurück nach oben](#inhaltsverzeichnis)**
## Trigger

###### [Regel [R011](#regel-r011)]
Es gibt 3 verschiedene Arten von Triggern, die verwendet werden dürfen.
- Sequenz-Trigger dienen der Befüllung der technischen Schlüsselspalte id (before row insert).
- Auditing-Trigger dienen der Befüllung der Auditing-Spalten (before row insert/update).
- Journalling-Trigger dienen der Befüllung von Journal-Tabellen (after row insert/update/delete).

###### [Regel [R012](#regel-r012)]
| Objekt-Typ | Regel | Beispiel |
|:---|:---|:---|
| Sequenz-Trigger | \<TabellenKürzel\>_SEQ_TG | emp_seq_tg |
| Journalling-Trigger | \<TabellenKürzel\>_JN_TG | emp_jn_tg |
| Auditing-Trigger | \<TabellenKürzel\>_AUD_TG | emp_aud_tg |

###### [Regel [R013](#regel-r013)]
Die Erstellung weiterer Trigger ist verboten.

###### [Regel [R014](#regel-r014)]
Als Alternative zu Triggern ist eine geeignete Zugriffsschicht zu implementieren.

###### [Regel [R015](#regel-r015)]
Alles-oder-Nichts-Prinzip:

- Wenn es eine technische Schlüsselspalte gibt, die aus einem Sequenz-Trigger befüllt wird, dann gilt dies für alle technischen Schlüsselspalten.
- Wenn es einen Auditing-Trigger gibt, der Auditing-Spalten befüllt, so werden alle Auditing-Spalten über einen Auditing-Trigger befüllt.
- Wenn es eine Journal-Tabelle gibt, die über einen Journalling-Trigger befüllt wird, dann gilt dies für alle Journal-Tabellen.

###### [Regel [R016](#regel-r016)]
Sämtliche Trigger sind automatisiert, z.B. aus einem Template, zu erstellen.


**[Zurück nach oben](#inhaltsverzeichnis)**
## Sequenzen

Dieser Abschnitt gilt für Sequenzen, die zur Befüllung der technischen Schlüsselspalte dienen.
Weitere Sequenzen sind prinzipiell erlaubt, aufgrund ihrer Seltenheit hier jedoch nicht erfasst.

###### [Regel [R017](#regel-r017)]

| Objekt-Typ | Regel | Beispiel |
|:---|:---|:---|
| Sequenz | <TabellenKürzel>_SEQ | emp_seq,<br />_Für Spalte id der Tabelle mit dem betreffenden \<TabellenKürzel\>._ |

###### [Regel [R018](#regel-r018)]

Sequenzen starten immer bei 1.

###### [Regel [R019](#regel-r019)]

Tabellen, die über bulk-inserts befüllt werden, dürfen cachende Sequenzen verwenden.


###### [Regel [R020](#regel-r020)]

Beispiel einer nicht-cachenden Sequenz für die Befüllung der Spalte _id_ der Tabelle mit dem Kürzel _emp_:

  ```sql
  create sequence emp_seq nocycle nocache maxvalue 999999999999999999 minvalue 0 start with 1;
  ```



**[Zurück nach oben](#inhaltsverzeichnis)**
## Spalten

###### [Regel [R021](#regel-r021)]

| Objekt-Typ | Regel | Beispiel |
|:---|:---|:---|
| Technische Schlüsselspalte | ID | employee.id |
| Fremdschlüsselspalte | \<TabellenKürzel\>\_ID,<br />\<TabellenKürzel\>\_\<Qualifikation\>\_ID | employee.dep_id<br />_\<TabellenKürzel\> der referenzierten Tabelle<br />\<Qualifikation\> bei mehreren Referenzen._ |
| Auditing-Spalte,<br />Erstellungsdatum | CREATED_DATE | employee.created_date |
| Auditing-Spalte,<br />Änderungsdatum | MODIFIED_DATE | employee.modified_date |
| Weitere Spalten | nicht 'ID', enden nicht auf '_ID', keine Auditing-Spalte | employee.last_name |

**[Zurück nach oben](#inhaltsverzeichnis)**
### Technischer Schlüssel
###### [Regel [R022](#regel-r022)]
Jede Tabelle verfügt über einen technischen Primärschlüssel.
Die Tabelle wird ausschliesslich über diesen Primärschlüssel referenziert.

_Warum?_ Fachliche Schlüssel, die sich durchaus ändern können, sind von der Aufgabe der referentiellen Integrität entkoppelt.

###### [Regel [R023](#regel-r023)]
Die technische Schlüsselspalte heisst immer id.
Joins sind somit immer über die id-Spalte aufzubauen, nicht über fachliche Schlüssel, die sich ändern können.

###### [Regel [R024](#regel-r024)]
Die technische Schlüsselspalte ist nicht nullable.

_Warum?_ Datensätze sind so immer referenzierbar.

###### [Regel [R025](#regel-r025)]
Die technische Schlüsselspalte ist vom Typ NUMBER(18,0)

  ```
  999.999.999.999.999.999 bedeutet:
  999.999 Tage jeweils 999 Milliarden Einträge erstellt.
  999.999 Tage entsprechen ca. 2738 Jahren
  ```

_Warum?_ Damit passt die id bequem in gängige Datentypen, 64bit signed integer (Java: long, C: int64_t/signed long long).

###### [Regel [R026](#regel-r026)]
Die technische Schlüsselspalte wird immer von einer Sequenz befüllt.
Dies passiert bevorzugt in einer Zugriffsschicht.


**[Zurück nach oben](#inhaltsverzeichnis)**
### Auditing-Spalten
###### [Regel [R027](#regel-r027)]
Auditing-Spalten sind für alle Tabellen verbindlich und ebenso verbindlich zu pflegen.
Dies passiert bevorzugt in einer Zugriffsschicht.

###### [Regel [R028](#regel-r028)]
Auditing-Spalten sind nicht nullable, d.h. die Erstanlage zählt als Modifikation.

###### [Regel [R029](#regel-r029)]
Auditing-Spalten sind i.d.R. vom Typ Date.




**[Zurück nach oben](#inhaltsverzeichnis)**
## Constraints
###### [Regel [R030](#regel-r030)]

| Objekt-Typ | Regel | Beispiel |
|:---|:---|:---|
| Primary Key Constraint | \<TabellenKürzel\>\_PK | emp_pk |
| Unique Constraint | \<TabellenKürzel\>\_\<Qualifikation\>\_UK | emp_username_uk,<br />_Bsp. für \<Qualifikation\>: Spaltenname, Spaltenkürzel (ggf. mehrere), fachlicher Aspekt._ |
| Foreign Key Constraint | \<TabellenKürzel\>\_\<TabellenKürzel\>\_FK,<br />\<TabellenKürzel\>\_\<TabellenKürzel\>\_\<Qualifikation\>\_FK| emp_dep_fk,<br />_Bsp. für \<Qualifikation\>: Spaltenname, Spaltenkürzel (ggf. mehrere), fachlicher Aspekt._ |
| Check Constraint | \<TabellenKürzel\>\_\<Qualifikation\>\_CK | emp_manager_ck,<br />_... constraint emp_manager_ck check (manager in (0,1))<br />Bsp. für \<Qualifikation\>: Spaltenname, Spaltenkürzel (ggf. mehrere), fachlicher Aspekt._ |
| Not Null Constraint | - (kein Name notwendig) | |


**[Zurück nach oben](#inhaltsverzeichnis)**
### Not Null Constraints
###### [Regel [R031](#regel-r031)]
Not Null constraints werden nicht namentlich ausgewiesen, da:

1. änderbar via

   ```sql
   alter table ... modifiy ... [not] null;
   ```

1. Weiterhin liefert die Fehlermeldung zur Constraint-Verletzung (ORA-01400) ausreichend Kontext um das Problem zu identifizieren.


**[Zurück nach oben](#inhaltsverzeichnis)**
## Indizes
###### [Regel [R032](#regel-r032)]

| Objekt-Typ | Regel | Beispiel |
|:---|:---|:---|
| Index | \<TabellenKürzel\>\_\<Qualifikation\>\_IX | emp_id_ix,<br />_Bsp. für \<Qualifikation\>: Spaltenname, Spaltenkürzel (ggf. mehrere), fachlicher Aspekt._ |



**[Zurück nach oben](#inhaltsverzeichnis)**
## Functions, Procedures, Types, Packages
###### [Regel [R033](#regel-r033)]
Functions, Procedures und Types _sollten_ bevorzugt in Packages abgelegt werden.

###### [Regel [R034](#regel-r034)]
Functions, Procedures, Types und Packages _können_ mit einem Projektpräfix versehen werden.





**[Zurück nach oben](#inhaltsverzeichnis)**
## Synonyme
###### [Regel [R035](#regel-r035)]
Synonyme sind in einem Owner-Schema verboten.

###### [Regel [R036](#regel-r036)]
Synonyme sind nur in einem Schema erlaubt, über die ein Fremdsystem auf ein Owner-Schema zugreift ("Access-Schema").

Nachteil: Müssen in diesem Fremdschema gepflegt werden, je nach Betriebskonzept bedeutet dies möglicherweise eine weitere Auslieferung des Access-Schemas.


**[Zurück nach oben](#inhaltsverzeichnis)**
### Öffentliche Synonyme
###### [Regel [R037](#regel-r037)]
Öffentliche Synonyme sind verboten.





**[Zurück nach oben](#inhaltsverzeichnis)**
## Jobs

###### [Regel [R038](#regel-r038)]
DBMS Jobs sind verboten. Es sind Scheduler Jobs zu verwenden.

_Warum?_ DBMS Jobs wurden durch Scheduler Jobs abgelöst.

###### [Regel [R039](#regel-r039)]
Scheduler Jobs sind bevorzugt mit dem Job-Typ "STORED_PROCEDURE" anzulegen.

_Warum?_ für eine stored procedure ist sichergestellt, dass diese kompiliert. Das gilt nicht für Anonyme PL/SQL-Blöcke.



