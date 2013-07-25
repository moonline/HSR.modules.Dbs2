=====================================
DBs2 FS13 Repetitionsfragen Antworten
=====================================

Dieses Dokument wird vorzu erweitert. Ergänzungen und Antworten sind herzlich willkommen.
Repetitionsfragen: https://github.com/moonline/Dbs2/blob/master/RepetitionQuestions.DBs1.tex

Keine Garantie auf Korrektheit!

DBs1 Repetition
===============

1
-

Drei Abstraktionsebenen:

* Benutzersicht auf die Daten (Externe Ebene)
* Logische Gesamtsicht auf die Daten (Konzeptionelle Ebene) bestehend aus
	* dem konzeptionelle Schema (Abbildung der Realität) und 
	* dem Datenbankschema (DB Spezifisch)
* Physische Sicht auf die Daten (Interne Ebene)

2
-

Online Transaction Processing.
Sofortige Verarbeitung, nicht als Batch über die Nacht irgendwann sondern in Echtzeit.
Tendenziell viele kurze Queries / Transaktionen und eher einfache Queries.
Hoch normalisierte Daten.

3
-

* Atomicity: Transaction wird als ganzes oder gar nicht abgeschlossen.
* Consistency: Es gelten immer die definierten Konsistenzbedingungen.
* Isolation: Transaktionen werden ausgeführt, als wären sie seriell gestartet worden.
* Durability: Gespeicherte Änderungen gehen nicht verloren, auch nicht bei einem Crash oder Stromausfall.

4
-

.. code-block:: sql

	CREATE TABLE Abschluss (id NUMBER PK, name VARCHAR(50), note NUMBER, studiengang VARCHAR(50));
	INSERT ...
	SELECT studiengang, AVG(note) FROM Abschluss
	GROUP BY studiengang;


PL/SQL
======

5
-

* Procedures können 0 oder n Resultate zurückgeben, Funktionen nur eines, welches obligatorisch ist.
* Procedures können IN/OUT Parameter haben, Funktionen nur IN.
* Procedures erlauben DML, Funktionen nur SELECT.
* Eine Procedure kann Funktionen aufrufen, eine Funktion aber keine Procedures.
* Eine Procedure kann im Body Exception Handling mit try-catch implementieren, eine Funktion nicht.
* Procedures unterstützen Transaktionen, Funktionen nicht.
* Funktionen können in SELECT Statements verwendet werden, Procedures nicht.

6
-

**Funktion**

	Aufbau:
	
	.. code-block:: sql

		CREATE OR REPLACE FUNCTION test (note IN NUMBER) IS
			-- do something
		END;
		/

	Benutzung:
	
	.. code-block:: sql

		SELECT name, test(note) from Abschluss

**Stored Procedure**

	Aufbau:
	
	.. code-block:: sql

		CREATE OR REPLACE PROCEDURE test (note IN NUMBER) AS
		DECLARE
			-- Declare used variables and exceptions
		BEGIN
			-- Body
		EXCEPTION
			-- Exception handling
		END;
		/

	Benutzung:
	
	.. code-block:: sql

		DECLARE 

		BEGIN
			test(10);
		END;
		/

7
-

Systemexceptions werden durch das DBMS vordefiniert.
Benutzerexceptions werden in der Deklarations-Section der Stored Procedure vom Benutzer definiert.
Systemexceptions werden vom System geworfen, Benutzerexceptions vom Benutzer.
Exceptions werden in der EXCEPTOIN Section behandelt.

.. code-block:: sql

	...
	DECLARE
		-- benannte Exception
		Ausnahme1 exception;
	BEGIN
		raise Ausnahme1;
	EXCEPTION
	...

8
-

* Verbesserung der Performance
* Security
* Domain Logik
	
9
-

Updateable Views

10
--

Eigenheit von Oracle.
Eine Dummy-Tabelle die verwendet werden kann, wenn man von keiner echten Tabelle SELECTen will. zB:

.. code-block:: sql

	SELECT 1+1 FROM DUAL;
	SELECT sysdate FROM DUAL;  
	SELECT AbteilungSalaer('Entwicklung') FROM DUAL;


Stored Procedures
=================

11
--

Anonymes PL/SQL wird von einem Client aus ausgeführt.

* (-) wird jedes Mal geparst
* (-) Wird wie SQL genutzt
* (+) Einfacher zu deklarieren

Stored Procedures werden geparst und in der DB zu den Daten abgelegt.
Stored Procedures können mit dem Namen von andern PL/SQL Blöcken aus abgerufen werden.

* (+) SP können von Triggers aufgerufen werden.
* (+) Werden nur einmal geparst
* (+) von überall aufrufbar
* (+) Kann von externer App aufgerufen werden

12
--

* In Java geschriebene Prozedur wird als .java oder .class File in die DB geladen.
* Java SP wird als solche "publiziert" in der DB.
* Clients und andere SP's können SP verwenden.

13
--

DB Benötigt dazu Java VM inkl. Garbage Collection, Memory, Class Loader, etc...
Java Code wird als Blob in DB abgelegt.

14
--

SP schreiben, in die DB laden, publizieren, verwenden.

15
--

Folgendes Beispiel funktioniert nur mit PostgreSQL.

.. code-block:: sql

	CREATE LANGUAGE plpythonu;
	CREATE OR REPLACE FUNCTION multiplier (numbers integer[])
	RETURNS integer
	AS $$
		from operator import mul
		return reduce(mul, numbers)
	$$ LANGUAGE plpythonu;

Packages
========

16
--

Dienen der Gruppierung von Funktionen und Stored Procedures. Können weder verschachtelt noch parametrisiert werden.

Vorteile von Packages:

* Modularität: Gruppieren von logisch zusammenhängenden Komponenten.
* Einfacheres Applikationsdesign: Interfaces und Implementation sind getrennt.
* Information Hiding: Es können auch "private" und dadurch versteckte Komponenten deklariert werden.
* Zusätzliche Funktionalität: Öffentliche Variabeln und Cursor eins Packages sind während einer gesamten Session aktiv. D.h. sie können zwischen verschiedenen Unterprogrammen geteilt werden und können Daten über mehrere Transaktionen hinweg speichern, ohne dass eine separate Tabelle benötigt wird.
* Bessere Performance: Funktionen werden beim ersten Zugriff ins Memory geladen und sind danach ohne zusätzlichen Disk I/O verfügbar.

17
--

Weil ein DBMS kein Terminal besitzt und nicht interaktiv bedient wird. 

Beispielcode:

.. code-block:: sql

	-- Package SET:
	SET SERVEROUTPUT ON
	DBMS_OUTPUT.PUT_LINE --(works like OS Pipe)

18
--

``dbms_output`` oder ``user_lock``.

Beispiel eigenes Paket:

.. code-block:: sql

	CREATE OR REPLACE PACKAGE emp_actions AS  -- spec
		-- function and procedure declaration
	END emp_actions;

	CREATE OR REPLACE PACKAGE BODY emp_actions AS  -- body
		-- function and procedure specification
	END emp_actions;


Cursors
=======

19
--

Cursor werden benutzt, um in einem Set von Rows auf eine bestimmte Row zu zeigen, bzw. über Rows zu iterieren.

20
--

.. code-block:: sql

	CREATE TABLE messwerte (standort INTEGER, temperatur NUMERIC);
	INSERT ...
	CREATE TABLE tropenNaechte (standort INTEGER, temperatur NUMERIC);
	CREATE TABLE settings (option VARCHAR, value INTEGER);
	INSERT INTO settings ('level', 20);

.. code-block:: sql

	DECLARE
		-- variablen und cursor deklarieren
		CURSOR temperatureAlarm (level IN INTEGER) IS
			SELECT temperatur, standort FROM messwerte FOR INSERT;
		temperatur messwerte.temperatur%TYPE;
		standort messwerte.standort%TYPE;
	BEGIN
		-- öffnen, iterieren
		OPEN temperatureAlarm;
		LOOP
			FETCH temperatureAlarm INTO temperatur, standort;
			IF temperatur > level THEN
				INSERT INTO tropenNaechte ('standort', 'temperatur') VALUES (standort, temperatur);
			END IF;
		END LOOP;
	END;
	/

21
--

Überprüfen, ob der Cursor geöffnet ist (``%ISOPEN``), ob etwas gefunden wurde (``%NOTFOUND`` / ``%FOUND``) und wie viele Zeilen vorhanden sind (``%ROWCOUNT``).

Constraints
===========

22
--

Constraints definieren Konsistenzbedingungen.
Sie gewährleisten, dass bestimmte Bedingungen zwischen den Daten immer gelten.
Wenn eine solche Bedingung/Regel verletzt wird, wird die laufende Operation abgebrochen.

23
--

* Primär: Wird während einer Operation geprüft (z.B. Werttyp)
* Sekundär: Wird nach einer Operation geprüft (z.B. Summe über Rows, etc...)
* Stark: Wird während Transaktion geprüft
* Schwach: Wird erst nach der Transaktion geprüft

24
--

Auf jeder Spalte.

25
--

.. code-block:: sql

	-- anlegen
	ALTER TABLE x ADD CONSTRAINT myConstraint 'name' NOT NULL;
	-- löschen
	ALTER TABLE x DROP CONSTRAINT myConstraint;
	-- deaktivieren
	ALTER TABLE x DISABLE CONSTRAINT myConstraint;
	-- aktivieren
	ALTER TABLE x ENABLE CONSTRAINT myContraint;
	-- auflisten
	SELECT constraint_name, constraint_type FROM user_constraints WHERE table_name = 'x';


Triggers
========

26
--

* Abhängige Attribute berechnen
* Updateable Views
* Constraints
* Zugriffsschutz

27
--

Alle Operationen die Daten verändern: ``INSERT``, ``UPDATE``, ``DELETE``.

28
--

* Before-Triggers: Werden VOR der Änderung der Daten ausgeführt, gedacht zur Überprüfung von Vorbedingungen
* After-Trigger: Werden NACH der Änderung der Daten ausgeführt, gedacht zur Überprüfung von Nachbedingungen
* Row-Triggers: Werden für jede betroffene Row ausgeführt
* Statement-Trigger: Wird für jedes ausgeführte Statement aufgerufen

29
--

Bei Oracle zur Referenzierung der alten Daten (Row vor der Änderung) und der neuen Daten (Row nach der Änderung).
Unter PostgreSQL heissen die entsprechenden Variablen ``OLD`` und ``NEW``.

30
--

* Oracle: Mit den Rechten ihres Owners.
* PostgreSQL: Mit den Rechten des Callers.
* MS SQL Server: Kann mit ``EXECUTE AS`` definiert werden. Standard: ``EXECUTE AS CALLER``.

31
--

Oracle:

.. code-block:: sql
	
	CREATE OR REPLACE TRIGGER check
	BEFORE INSERT ON messdaten
	FOR EACH ROW
	AS
	BEGIN
		IF :new.temperatur > 100 THEN
			-- planet destroyed or failure -> don't insert
			INSERT INTO log (:new.id, :new.temperature);
		END IF;
	END;
	/
	
PostgreSQL:

.. code-block:: sql

	-- First, create trigger function
	CREATE OR REPLACE FUNCTION verify_temperature() RETURNS trigger AS
	$$
	BEGIN
		-- If temperature is too high, write log and return OLD version of row.
		-- Otherwise return NEW version of row.
		IF NEW.temperatur > 100 THEN
			INSERT INTO log VALUES (NEW.id, NEW.temperatur);
			RETURN OLD;
		ELSE
			RETURN NEW;
		END IF;
	END
	$$ LANGUAGE plpgsql;
	
	CREATE TRIGGER mycheck
	BEFORE INSERT ON messdaten
	FOR EACH ROW
	EXECUTE PROCEDURE verify_temperature();


32
--

Oracle:

.. code-block:: sql
	
	CREATE OR REPLACE TRIGGER check
	BEFORE INSERT ON messdaten
	FOR EACH ROW
	AS
	BEGIN
		:new.absolute := :new.temperature + 273;
	END;
	/


33
--

1. Before Statement Trigger
2. Row Trigger:
	1. Before Row Trigger
	2. After Row Trigger
3. After Statement Trigger

34
--

* Instead Of: Ersetzen Aktionen. Z.B. DELETE Trigger, der statt dem Löschen der Rows diese nur als gelöscht markiert.
* Log on/Log off: Triggern bei Anmeldung/Abmeldung am System.

Updateable Views
----------------
35) Temporäre Tabellen werden nicht dauerhaft gespeichert, sondern am Ende der Transaktion wieder gelöscht. Zudem ist die Aktualität der temp. Tabelle unbekannt.

36) Weil die unter den Views liegenden Tabellen bereits Indexe enthalten und Indexe auf Views unnötig sind.

37) Updateable View sind Views, die über Trigger Änderungen in die dahinter liegenden Tabellen schreiben. Views lassen sich per Default nur updaten, wenn sie keine Joins enthalten und der Primär Schlüssel enthalten ist. Ansonsten kann das DBS die geänderten Rows nicht mehr eindeutig den Rows den dahinter liegenden Tabellen zuordnen.

38) Ein Update-Instead-Of Trigger auf der View übernimmt die Update Daten und schreibt sie einzeln in die dahiner liegenden Tabellen.
	.. code-block:: sql
	
		CREATE OR REPLACE TRIGGER updateMesswertzusammenfassung INSTEAD OF UPDATE ON messwertzusammenfassung FOR EACH ROW
		BEGIN
			UPDATE messwerte SET temperature = :n.temperature WHERE id = :n.messwertId;
			UPDATE standort SET name = :n.name WHERE id = :n.standortId;
		END;
		/
		

Materialized Views
------------------
39) Gespeicherte Ausgaben einer View. Die Materialized View aktualisiert sich nicht, sondern stellt eine Snapshot zu einem bestimmten Zeitpunkt von einer View dar.

40) Virtual Tables haben überhaupt nichts mit Views zu tun, auch wenn es danach tönt. Virtual Tables sind Data Wrappers für externe Schnittstellen, z.B. csv.


Datenstrukturen
===============

Arrays
------
41) Arrays sind indexierte Listen von Elementen gleichen Datentyps in einer Datenbankzelle.

42) Sets dürfen das gleiche Element nur einmal beinhalten.

43) Wenn eine dynamische Liste von Werten in der Datenbank abgelegt werden muss, z.B. wenn bei jedem Wareneingang die Anzahl Paletten notiert werden und später daraus der Tagestotal ermittelt werden soll (Jeden Tag sind es unterschiedliche Anzahl Wareneingänge), man jedoch nicht für jeden Wareneingang eine einzelne Zeile anlegen möchte.

44) Der schreibende Zugriff auf Arrays ist aufwendig, weil jeweils die gesammte Zelle (das ganze Array) neu geschrieben werden muss.

45) .. code-block:: sql
		
		CREATE TABLE wareneingangsStatistik (
			DATE datum,
			INTEGER[] anzahlPaletten
		);
		
		INSERT INTO wareneingangsStatistik (12.04.13, ARRAY[5,6,7,3]);
		
		SELECT anzahlPaletten FROM wareneingangsStatistik; // {5,6,7,3}
		
		
46) 
	array_to_text()
		Gibt ein Array als Text zurück.
	unnest()
		Gibt ein Array als Tabelle zurück
		
47)
	<@ Operator
		Ermittelt, ob das Linke Array ein Subset des rechten ist. ARRAY[1,2] <@ ARRAY[1,2,3] // true
	= Operator
		Vergleicht zwei Arrays
	&& Operator
		Ermittelt, ob ein Element in beiden Arrays vorkommt ARRAY[1,3] && ARRAY[1,2,3] // true

48) Mit FIND_IN_SET oder unnest und einer subquery falls es etwas aufwändiger ist.


Graphen
-------
49) Graphen sind Netzstrukturen und können zur Abbildung von vermaschten Netzen wie das Internet, Liniennetzen von Verkehrsmitteln oder Verbindungen zwischen Personen, etc. eingesetzt werden.
	Graphen setzen sich zusammen aus Knoten (Nodes/Vertices V), verbunden mit Ecken (Edges E).

50) 

51) Common Table Expression, Eine Sprache, um über die temporäre Tabelle zu iterieren und sie zu verändern, die während eine Transaktion besteht. Im Unterschied zur Subquery ist die CTE viel mächtiger als die Subquery, die einfach eine Query innerhalb einer Query aufruft und keinen schreibenden Zugriff auf die temporäre Tabelle hat.

52) Ein Tree ist ein Graph, der keine Zyklen besitzt und oft gerichtet ist.

53) Trees eignen sich, um Hirarchien (z.B. in Firmen) oder Verwandtschaften abzubilden. Write-once, read-many Szenarien.

54) 
	Adjazenzliste
		Zu jedem Knoten wird eine Referenz auf den Elternknoten abgespeichert. Die Wurzel besitzt eine NULL Referenz, die Blätter besitzen keine Knoten, die auf sie verweisen.
			.. code-block:: sql
				
				CREATE TABLE tree (id, name, parent);
				INSERT INTO TABLE tree (1, "CEO", NULL);
				INSERT INTO TABLE tree (2, "chef technic", 1);
				INSERT INTO TABLE tree (3, "chef accounts", 1);
				INSERT INTO TABLE tree (4, "robert, the mechanic", 2);
				INSERT INTO TABLE tree (5, "paul, the bimbo", 2);
				
				SELECT name FROM tree WHERE parent = 2;
				
	
	Nested Set Model
		Baum mit Knoten, die jeweils einen linken und rechten Wert (minimal und Maximal Knoten Id der Childes) und eine Referenz auf einen Parent Knoten besitzen. Beim Einfügen oder Entfernen muss unter Umständen der Baum umsortiert werden und die Werte für links und rechts müssen angepasst werden. Der linke Wert ist immer kleiner als der rechte. Beider Werte sind grösser als der linke Wert der Elternmenge und kleiner als der Rechte.
			.. code-block:: sql
				
				CREATE TABLE tree (id, name, parent, left, right);
				INSERT INTO tree (1, "CEO", NULL, 1, 7);
				INSERT INTO tree (2, "chef technic", 1, 2, 8);
				INSERT INTO tree (3, "chef accounts", 1, 3, 4);
				INSERT INTO tree (4, "robert, the mechanic", 2, 4, 7);
				INSERT INTO tree (5, "paul, the bimbo", 2, 5, 6);
				
				SELECT name FROM tree WHERE parent = 2;
				
				// 1 CEO l:1, r:2
				
				// 1 CEO l:1, r:4
				//   2 chef technic l:2, r: 3
				
				// 1 CEO l:1, r:5
				//   2 chef technic l:2, r: 3
				//   3 chef accounts l:3, r:4
				
				// 1 CEO l:1, r:7
				//   2 chef technic l:2, r: 6
				//     4 robert l:4, r:5
				//   3 chef accounts l:3, r:4
				
				// 1 CEO l:1, r:7
				//   2 chef technic l:2, r: 8
				//     4 robert l:4, r:7
				//     5 paul, l:5, r:6
				//   3 chef accounts l:3, r:4
				
				
55) Itree's bestehen aus Pfaden, deren Knoten mit Punkten getrennt sind. Mehrere Pfade ergeben einen Baum.
	.. code-block:: sql
	
		CREATE TABLE enterprise (id INTEGER, pers ltree);
		INSERT INTO enterprise (1, 'CEO');
		INSERT INTO enterprise (2, 'CEO.chefTechnic');
		INSERT INTO enterprise (3, 'CEO.chefAccounts');
		INSERT INTO enterprise (4, 'CEO.chefTechnic.robert');
		INSERT INTO enterprise (5, 'CEO.chefTechnic.paul');
		
		SELECT * FROM enterprise WHERE pers ~ '*.chefTechnic.*'; // Liefert alle die einen chefTechnic im Pfad haben
		SELECT * FROM enterprise WHERE pers <@ 'CEO.chefTechnic'::ltree; // Liefert alle Nachfolger von chefTechnic
		SELECT lca(pers) FROM enterprise WHERE id in (4,5); // Liefert gemeinsame Vorgesetzte von robert und paul
		
		
56) Zur Speicherung von dynamischen Inhalten wie E-mails, Datensätzen mit zusätzlichen Feldern oder Metadaten zu in der DB abgelegten Dateien.

57) .. code-block:: sql

	SELECT query_to_xml(query, TRUE, FALSE);
	
	
58) Mit XPATH können XML Strukturen definiert werden. Mit XQUERY lassen sie sich parsen.


Dictionaries
------------
59) Dictionaries sind Key-/Value Stores, die es erlauben eine Assoziative Liste von Elementen in einer Datenbankzelle abzulegen.

60) .. code-block:: sql
	
	CREATE TABLE settings (id INTEGER, values hstore);
	
	INSERT INTO settings (1, '"col1"=>"456", "col2"=>"zzz"');
	UPDATE settings SET values = values || ('size' => '3') WHERE id = 1; // add or update value of key 'size'
	UPDATE settings SET values = delete(values, 'col2'); // delete key
	SELECT values->'size' FROM settings WHERE values @> '"size"=>3'
	

61) .. code-block:: sql

	CREATE TABLE content (id, tags);
	
	INSERT INTO content (1, '"Ferien"=>1, "Freizeit"=>1');
	INSERT INTO content (2, '"Freizeit"=>4, "Arbeit"=>7');
	INSERT INTO content (3, '"Ferien"=>3, "Wochenende"=>2');
	
	SELECT * FROM content WHERE tags 'Ferien=>:t';
	
	
ORDBMS
======
62) Objektrelationale Datenbanken arbeiten wie relational, d.h. sie liefern als Resultat eine Tabelle, ermöglichen es jedoch, in Zellen Objekte mit Eigenschaften und Methoden abzulegen.


Objekttypen
-----------
63) Objekttypen sind benutzerdefinierte Typen
	.. code-block:: sql

		CREATE OR REPLACE TYPE Person AS OBJECT (
			Name VARCHAR(30),
			Birthdate DATE,
			Addr Adress, -- Nutzung von Objekttypen als Member
			MEMBER FUNCTION getAge RETURN NUMBER -- Methode, implementation extern
		);
		
		
64) Eine Spalte vom Typ Objekt. Ermöglicht das Ablegen von Objekten in Zellen.
	.. code-block:: sql
	
		CREATE TABLE Material (
			Name VARCHAR(20),
			Owner Person
		);
		
		INSERT INTO Material VALUES (
			"Beistelltisch", 
			Person(
				"Peter Muster", 
				TO_DATE("31.03.69","DD.MM.JJ"), 
				Address("Bahnhofstrasse 3", "8000", "Zürich)
			)
		);
		
		SELECT Owner.Name FROM Material WHERE Owner.Address = Address("Bahnhofstrasse 3", "8000", "Zürich);
		
		
65) Objekttabellen repräsentieren ganze Objekte(sind von einem Objekttyp) und sind, objektwertige, referenzierbare Tabellen. Die Rows sind Objekte des zugrundeliegenden Typs und können über OIDs angesprochen werden. Vorteil: Durch die OIDs sind die Zeilen Datenbankweit eindeutig indentifizierbar.
	.. code-block:: sql
	
		CREATE TABLE Mitarbeiter OF Person ( -- Spalten müssen den Objektattributen entsprechen
			Name NOT NULL,
			Birthdate NOT NULL,
			Addr Not NULL
		);
		
		-- Vererbung (NOT FINAL)
		CREATE OR REPLACE TYPE Person AS OBJECT (
			-- members
		) NOT FINAL; -- not final definiert den Objekttyp als Erbbar
		
		CREATE OR REPLACE Type Student UNDER Person (
			StudentenNr VARCHAR(10)
		);
		
		
66) Wie gewöhnliche SQL Queries
	.. code-block:: sql
	
		SELECT Name, Brithdate FROM Person p WHERE p.Addr = Address("Bahnhofstrasse 3", "8000", "Zürich);
		
		
67) .. code-block:: sql
		
	-- Implementation in separat übersetzbarer Typedefinition
	CREATE OR REPLACE TYPE BODY Person AS
		MEMBER FUNCTION getAge RETURN NUMBER IS
			BEGIN
				RETURN (SYSDATE - SELF.Birthdate) / 365;
			END;
	END;
	
	-- usage
	SELECT p.Name, p.Birthdate, p.getAge() FROM Mitarbeiter p;
	

68) MAP liefert einen Basistyp mit bekannter Sortierreihenfolge zurück, ORDER ermöglicht das implementieren einer eigenen Sortierung.
	.. code-block:: sql
	
		MAP MEMBER FUNCTION getAge RETURN INTEGER;
		
	
69) REF(t) liefert eine Referenz, DEREF(ref) liefert Zugriff auf das Row Object. Referenzen sind nicht fest verdrahtet sondern nur Zeiger, daher muss Für den Objektzugriff Dereferenziert werden.
	.. code-block:: sql

		CREATE OR REPLACE TYPE Person AS OBJECT (
			Parent REF Person,
			...
		);
	
70) Schränkt den Werteberech der über die ganze Datenbank eindeutigen OIDs ein auf die betreffende Tabelle (Optimierung).

71) .. code-block:: sql
	
		INSERT INTO TABLE Family VAUES (
			( SELECT REF(t) FROM Family f WHERE f.Name = "Esmeralda Reihner" ), -- Referenz erzeugen
			"Maria Reihner",
			...
		);
		
		SELECT DEREF(Parent) FROM Family f WHERE f.Name = "Maria Reihner";
		
72) Oracle prüft Referenzen nicht auf Veraltete (Dangling). Daher muss mit ISDANGLING oder ISNOTDANGLING dies selbst abgefragt werden.


Varrays und Nested Tables
-------------------------
73) VARRAYS sind eindimensionale inline Elementmengen. Genutzt für Elementmengen, die bevorzugt einmal geschrieben und dann nur noch gelesen werden.
	.. code-block:: sql
	
		CREATE Or Replace TYPE addressList AS VARRAY(2) OF VARCHAR2(50);
		
		
74) Nested Tables sind Tabellen in Tabellen. Sie sind sinnvoll, wenn in einer Zelle strukturierte Daten abgelegt werden müssen, die nur zu dieser Row gehören. 
	.. code-block:: sql
	
		CREATE TYPE Phonenumbers AS TABLE OF NUMBER;
		
		CREATE TABLE Telefonbuch (Name VARCHAR(30), Phones Phonenumbers);
		
		-- Nested Table als extern verfügbare Tabelle speichern:
		CREATE TABLE Telefonbuch (
			...
		) NESTED TABLE Phones STORE AS PhoneList;


75)
	VARRAY
		* Eindimensionale Daten
		* bevorzugt write once, read multiple
		* Wenn Grösse vorher bekannt
	Nested Table
		* Komplexere Daten, die je nach dem auch als direkte Tabelle ansprechbar sein sollen
		* Wenn Grösse von Angang an nicht bekannt ist
		
76) Hash Table mässiger Array Store. Über den Key kann der Index eines Elementes ermittelt werden.


ODBS
====
77) Ist die DB sehr nah mit der Anwendung verzahnt (z.B. eine Smartphone App, die Daten nur für sich persistiert), so ist ein ODBS die sinnvollste Anwendung. Auch wenn das Anwendungsumfeld der Datenbank sehr homogen ist (z.B. alles Java), kann eine ODBS sinnvoll sein.

78) Die Relationale Datenbank kann keine verschachtelten Queries ausführen, wie z.B über die Telefonnummern der Kinder eines Mitarbeiters.

79) ODBS speichert und liefert Objekt und macht objektorientierte Abfragen. Relationale Datanbanken behandeln Daten immer als Tabellen und liefern auch das Resultat als Tabelle.

80) 
	Page Server
		weiss nichts über die innere Struktur der Objekte und kann auch keine Abfragen darüber machen. Liefert Pages als Ergebnis.
	Object Server
		Kennt die innere Struktur der Objekte, besitzt einen Object Manager und kann Abfragen auf Attribute von Objekten machen. Liefert als Ergebnis Objekte.
	
81)
	* Speicherung komplexer Objekte mit Identität, Kapselung, Typen, Klassen und Hirarchien
	* Effiziente Persistierung
	* Concurrency
	* Reliability
	* Deklarative Query Language

82) 
	* Die Objekte werden nicht transparent (durchsuchbar) abgelegt
	* Referenzen sind ein Problem
	* die Struktur der Objekte (Klasse) wird nicht mit abgelegt.
	* Transaktionen sind nicht möglich
	* Die Objekte werden unvollständig abgelegt
	
83) Von einer Wurzel aus werden alle durch Referenzen erreichbare Objekte persistiert

84) Siehe 80.

85) Konvertierung der Objektreferenzen im Hauptspeicher in Datenbankreferenzen und umgekehrt

86)
	logische OID
		In der DB wird eine eigene Object ID verwendet -> Mapping notwendig
	physische OID
		In der DB wird die Objektreferenz vom Hauptspeicher verwendet -> Direkte übernahme, kein Mapping
		
87) Über ein Mapping werden die Datenbankreferenzen der Objekte in in-Memory Referenzen übersetzt.

88) Object Data Management Group specification: Definiert einen Standard für die Objektdarstellung und Abfragesprachen für Objectdatenbanken.
		ODL
			* Object Definition Language
			* Attribute und Beziehungn
			* Verwerbung, Schnittstellen
			* OIDs
			* Persistence by Reachability
			* ACID Transaktionen
		OQL
			Object Query Language
			
89) Definiert Objekte
	.. code-block:: odl
	
		class Node {
			attribute string name;
			relationship Node parent inverse Node::children; // one-to many
			relationship set(Node) children inverse Node::parent; // many to one
		}
			
		class Tree (extent allNodes, key nodeId) { // extent: root of reachability tree
			...
		}
		
		
90) Eine SQL ähnliche Abfragesprache für Objekte

91) .. code-block:: oql

		select c.name from node n, n.children.children c; // get names of the child-child nodes

		
db4o
----
92) db4o speichert Java Objekte als Objekte ab und liefert über eine Abfragesprache Objektsets zurück

93)
	* SODA Queries: Abfrage anhand von SODA Attribut Bedingungen
	* Native Queries: Abfrage in der verwendeten Sprache (z.B. java) mit Attribut Bedingungen
	* Query by Example QBE: Anhand eines Teilobjektes wird der Rest gefunden

94) Jedes öffnen des DB Containers erzeugt eine Transaction, die mit commit oder rollback abgeschlossen werden kann.

95) 
	* aktualisieren: Objekt laden, verändern, db.store(objekt). 
	* db.delete(objekt)
	* Kasdade: db4o löscht nur explizit übergebene Objekte. Für Cascade Delete muss dies explizit verlangt werden.

96) Lazy Loading von Referenzierten Objekten, bzw. die Ladetiefe in einem Objektgraph und die Grenze, ab wo mit Lazy Loading gearbeitet wird.


NoSQL Datenbanken
=================
97)
	* Entstanden im 21. Jht
	* Keine genormte Abfragesprache, oft JSON verwendet
	* Consistency keine Absolute Bedingungen
	* Gut verteilbar, skalierbar
	* Open Source
	* Schemalos
	* Ausgelegt auf grosse Datenmengen
	
98) 
	Key- / Value DB
		* Speicherung von Schlüsselpaaren
		* Vorteil: einfach, effizient
		* Nachteil: Keine Struktur möglich, Value ist für DB unsichtbar (opak)
	Document DB
		* Speicherung von Strukturen
		* Vorteil: Daten können sauber strukturiert werden, Abfragen über Beziehungen möglich, Values sind strukturiert und dokumentiert
	Column Family Stores
		* Zusammenfassen von Columnen zu Gruppen
		* Vorteil: Gruppieren von Columnen
	Graph DB
		* Speichern von Beziehungen und Netz Strukturen
		* Effiziente Algorithmen für Graphverarbeitung und Graphsuchverfahren
	XML DB
		* Ablegen von XML Strukturen
	Objekdatenbanken
		* Speichern von Objektstrukturen
		
99)
	Sharding
		Aufteilen der Daten (bsp. Kunden a-f auf Knoten 1, Kunden f-k auf Knoten 2, ...) -> Verbessert Performance
	Replication
		Kopieren der Daten auf die Knoten -> Erhöht Availability
		
100)
	CAP
		consistency, availability, partition tolerance
	Theorem
		Es können nur zwei der drei Bedingungen eingehalten werden. Bei relationalen Datenbanken wird vor allem auf consistency und availability gsetzt, bei NoSQL DB's auf availability und partition tolerance.
		
101)
	BASE
		* BA: basically available. Verfügbarkeit ist von hoher Priorität
		* S: soft state: Konsistenz ist kein dauerhafter Zustand
		* Eventual consistent: Daten sind manchmal inkonsistent
		
102) Daten werden zerlegt und die zerlegten Teile verteilt berechnet. Anschliessend werden die Resultate verteilt zusammengefasst. Aus den erneuten Resultaten werden zusammen mit andern Resultaten wieder neue generiert. Map Reduce eignet sich sehr gut zur verteilten Berechnung von zerlegbaren Daten.
	Beispiel Statistik: Besucherstatistik wird in Tage zerlegt. Pro Tag und Angebot werden die Besucher berechnet. Anschliessend werden alle Besucher pro Angebot zusammengefasst und am Schluss die Besucher aller ANgebote.
	
	

		
		
		
