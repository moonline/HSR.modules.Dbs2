DBs2 FS13 Repetitionsfragen Antworten
=====================================

Keine Garantie für Korrektheit!

DBs1 Repetition
---------------
1) 	
	* Externe Ebene (Anwendungsebene)
	* konzeptionelle Ebene bestehend aus
		* dem konzeptionelle Schema (Abbildung der Realität) und 
		* dem Datenbankschema (DB Spezifisch)
	* interne (phyische) Ebene

2) 	Online Transaction Processing. Sofort Verarbeitung, nicht als Batch über die Nacht irgendwann sondern in Echtzeit

3) 	
	* Atomarität: Transaction wird als ganzes oder nicht abgeschlossen
	* Consistenz: Es gelten immer die Konsistenzbedingungen
	* Isolation: Transaktionen werden ausgeführt, als wären sie seriell
	* Durability: Gespeicherte Änderungen gehen nicht irgendwann verloren

4) 	.. code-block:: sql

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
	* Funktion (Procedure):	
		.. code-block:: sql

			CREATE OR REPLACE PROCEDURE test (note IN NUMBER) IS
				-- do something
			END;
			/


		* Benutzung:
			.. code-block:: sql
	
				SELECT name, test(note) from Abschluss


	* Stored Procedure:
		* .. code-block:: sql
	
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
		.. code-block:: PL/SQL	
		DECLARE 
	
		BEGIN
			test(10);
		END;
		/

7)	Systemexceptions werden vom System geworfen, Benutzerexceptions vom Benutzer.
	.. code-block:: PL/SQL	
	...
	DECLARE
		/* benannte Exception: */
		Ausnahme1 exception;
	BEGIN
		raise Ausnahme1;
	EXCEPTION
	...

8)	Verbesserung der Performance, Security, Domain Logik
	
9)	Updateable Views

10)	
	* Um mittels SQL Systeminformationen oder Funktionen abzurufen, gibt es die Pseudotabelle dual, welche über gewöhnliche Select Statements Systeminformationen zurückgibt. 
	* Bsp: 
		.. code-block:: SQL
		select sysdate from DUAL;  
		select AbteilungSalaer('Entwicklung') from DUAL;

Stored ProcedureS
----------------
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
	* .. code-block:: PL/SQL
		-- Package SET:
		SET SERVeROUTPUT ON
		DBMS_OUTPUT.PUT_LINE --(works like OS Pipe)

18) 
	* dbms_output, user_lock
	* .. code-block:: PL/SQL
		CREATE OR REPLACE PACKAGE emp_actions AS  -- spec
			-- function and proedure declaration
		END emp_actions;

		CREATE OR REPLACE PACKAGE BODY emp_actions AS  -- body
			-- function and proedure specification
		END emp_actions;

Cursors
-------
19) 
