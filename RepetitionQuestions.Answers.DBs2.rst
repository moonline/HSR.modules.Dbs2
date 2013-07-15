=====================================
DBs2 FS13 Repetitionsfragen Antworten
=====================================

Dieses Dokument wird vorzu erweitert. Ergänzungen und Antworten sind herzlich willkommen.
Repetitionsfragen: https://github.com/moonline/Dbs2/blob/master/RepetitionQuestions.DBs1.tex

Keine Garantie auf Korrektheit!

DBs1 Repetition
===============
1)
	* Externe Ebene (Anwendungsebene)
	* konzeptionelle Ebene bestehend aus
		* dem konzeptionelle Schema (Abbildung der Realität) und 
		* dem Datenbankschema (DB Spezifisch)
	* interne (phyische) Ebene

2) Online Transaction Processing. Sofort Verarbeitung, nicht als Batch über die Nacht irgendwann sondern in Echtzeit

3)
	* Atomarität: Transaction wird als ganzes oder nicht abgeschlossen
	* Consistenz: Es gelten immer die Konsistenzbedingungen
	* Isolation: Transaktionen werden ausgeführt, als wären sie seriell
	* Durability: Gespeicherte Änderungen gehen nicht irgendwann verloren

4) .. code-block:: sql

	CREATE TABLE Abschluss (id NUMBER PK, name VARCHAR(50), note NUMBER, studiengang VARCHAR(50));
	INSERT ...
	SELECT studiengang, avg(note) as studiengang, note from Abschluss
	GROUP BY studiengang


PL/SQL
======
5)	
	* Funktion kann in SQL und PL/SQL ausgeführt werden, SP nur in PL/SQL.
	* Funktion besitzt Rückgabewert.

6)	
	* Funktion
		* Aufbau:
			.. code-block:: sql

				CREATE OR REPLACE FUNCTION test (note IN NUMBER) IS
					-- do something
				END;
				/


		* Benutzung:
			.. code-block:: sql
	
				SELECT name, test(note) from Abschluss


	* Stored Procedure:
		* Aufbau:
			.. code-block:: sql
	
				CREATE OR REPLACE PROCEDURE test (note IN NUMBER) AS
				DECLARE
					-- declare used variables
				BEGIN
					-- programm
				EXCEPTION
					-- Exception handling
				END;
				/



		* Benutzung: 
			.. code-block:: sql

				DECLARE 
	
				BEGIN
					test(10);
				END;
				/


7) Systemexceptions werden vom System geworfen, Benutzerexceptions vom Benutzer.
	.. code-block:: sql

		...
		DECLARE
			-- benannte Exception
			Ausnahme1 exception;
		BEGIN
			raise Ausnahme1;
		EXCEPTION
		...


8) Verbesserung der Performance, Security, Domain Logik
	
9) Updateable Views

10)	
	* Um mittels SQL Systeminformationen oder Funktionen abzurufen, gibt es die Pseudotabelle dual, welche über gewöhnliche Select Statements Systeminformationen zurückgibt. 
	* Bsp: 
		.. code-block:: sql

			select sysdate from DUAL;  
			select AbteilungSalaer('Entwicklung') from DUAL;


Stored Procedures
=================
11)	
	* Anonymes PL/SQL wird von einem Client aus ausgeführt.
		* (-) wird jedes Mal geparst
		* (-) Wird wie SQL genutzt
		* (+) Einfacher zu deklarieren
	* Stored Procedures werden geparst und in der DB zu den Daten abgelegt. Stored Procedures können mit dem Namen von andern PL/SQL Blöcken aus abgerufen werden. 
		* (+) SP können von Triggers aufgerufen werden.
		* (+) Werden nur einmal geparst
		* (+) von überall aufrufbar
		* (+) Kann von externer App aufgerufen werden

12)	
	* In Java geschriebene Prozedur wird als .java oder .class File in die DB geladen.
	* Java SP wird als solche "publiziert" in der DB.
	* Clients und andere SP's können SP verwenden.
	
13) DB Benötigt dazu Java VM inkl. Garbage Collection, Memory, Class Loader, ... . Java Code wird als Blob in DB abgelegt.

14) SP schreiben, in die DB laden, publizieren, verwenden.

15)

Packages
========
16) Dienen der Gruppierung von Funktionen und Stored Procedures. Können weder verschachtelt noch parametrisiert werden.

17) 
	* Weil ein DBs kein Terminal besitzt und nicht interaktiv bedient wird. 
	* Code:
		.. code-block:: sql

			-- Package SET:
			SET SERVeROUTPUT ON
			DBMS_OUTPUT.PUT_LINE --(works like OS Pipe)


18) 
	* dbms_output, user_lock
	* Eigene: 
		.. code-block:: sql

			CREATE OR REPLACE PACKAGE emp_actions AS  -- spec
				-- function and proedure declaration
			END emp_actions;

			CREATE OR REPLACE PACKAGE BODY emp_actions AS  -- body
				-- function and proedure specification
			END emp_actions;


Cursors
=======
19) Cursor werden benutzt, um in einem Set von Rows auf eine bestimmte Row zu zeigen, bzw. über Rows zu iterieren.

20) .. code-block:: sql
	
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

21) Überprüfen, ob der Cursor geöffnet ist (%ISOPEN), ob etwas gefunden wurde (%NOTFOUND) / (%FOUND) und die Anzahl Zeilen ermitteln (%ROWCOUNt)

Constraints
===========
22) Definieren Konsistenzbedingungen. Gewährleisten, dass bestimmte Bedingungen zwischen den Daten immer gelten.

23) 
	* primär: Während einer Operation geprüfte (z.B. Werttyp)
	* sekundär: Nach einer Operation geprüfte (z.B. Summe über Rows, ...)
	* stark: während Transaktion geprüft
	* schwach: erst nach der Transaktion geprüft
24) Auf jeder Spalte.

25) .. code-block:: sql

	-- anlegen
	ALTER TABLE x ADD CONSTRAINT myConstraint 'name' NOT NULL;
	-- löschen
	ALTER TABLE x DROP CONSTRAINT myConstraint;
	-- deaktivieren
	ALTER TABLE x DISABLE CONSTRAINT myConstraint;
	-- enable
	ALTER TABLE x ENABLE CONSTRAINT myContraint;
	-- list
	SELECT constraint_name, constraint_type FROM user_constraints WHERE table_name = 'x';


Triggers
========
26) 
	* Abhängige Attribute berechnen
	* Updateable Views
	* Constraints
	* Zugriffsschutz

27) update, insert, delete

28) 
	* before-Triggers: werden VOR der Änderung der Daten ausgeführt, gedacht zur Überprüfung von Vorbedingungen
	* after-Trigger: werden NACH der Änderung der Daten ausgeführt, gedacht zur Überprüfung von Nachbedingungen
	* Row-Triggers: Werden für jede betroffene Row ausgeführt
	* Statement-Trigger: Wird für jedes ausgeführte Statement aufgerufen

29) Zur Referenzierung der alten Daten (Row vor der Änderung) und der neuen (Row nach der Änderung)

30) Mit den Rechten ihres Owners

31) .. code-block:: sql
	
	CREATE OR REPLACE TRIGGER check BEFORE INSERT ON messdaten FOR EACH ROW AS
	BEGIN
		IF :new.temperatur > 100 THEN
			-- planet destroit or failure -> don't insert
			INSERT INTO log (:new.id, :new.temperature);
		ENF IF;
	END;
	/


32)  .. code-block:: sql
	
	CREATE OR REPLACE TRIGGER check BEFORE INSERT ON messdaten FOR EACH ROW AS
	BEGIN
		:new.absolute := :new.temperature + 273;
	END;
	/


33) 
	1. Before statement Trigger
	2. Row Trigger:
		1. Before Row Trigger
		2. After Row Trigger
	3. After statement trigger

34) 
	* Instead Of: Ersetzen Aktionen. Z.B. Delete Trigger, der statt dem Löschen der Rows diese nur als gelöscht markiert
	* log on/log of: Triggern Benutzer Events

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