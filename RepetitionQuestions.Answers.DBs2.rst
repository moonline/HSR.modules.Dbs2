DBs2 FS13 Repetitionsfragen Antworten
=====================================

Repetitionsfragen: https://github.com/moonline/Dbs2/blob/master/RepetitionQuestions.DBs1.tex

Keine Garantie auf Korrektheit!
Korrekturen und Ergänzungen sind herzlich willkommen.

DBs1 Repetition
---------------
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
------
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
-----------------
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
--------
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
-------
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
-----------
22) Definieren Konsistenzbedingungen. Gewährleisten, dass bestimmte Bedingungen zwischen den Daten immer gelten.

23) 
	* primär: Während einer Operation geprüfte (z.B. Werttyp)
	* sekundär: Nach einer Operation geprüfte (z.B. Summe über Rows, ...)
	* stark: während Transaktion geprüft
	* schwach: erst nach der Transaktion geprüft
24) Auf jeder Spalte.

25)..code-block:: sql

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
--------
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

31) ..code-block:: sql
	
	CREATE OR REPLACE TRIGGER check BEFORE INSERT ON messdaten FOR EACH ROW AS
	BEGIN
		IF :new.temperatur > 100 THEN
			-- planet destroit or failure -> don't insert
			INSERT INTO log (:new.id, :new.temperature);
		ENF IF;
	END;
	/

s
32)  ..code-block:: sql
	
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
^^^^^^^^^^^^^^^^
