# 🛡️ SQL Injection Cheat Sheet

<p align="center">
  <img src="https://img.shields.io/badge/Cybersecurity-SQL%20Injection-red?style=for-the-badge">
  <img src="https://img.shields.io/badge/Level-Beginner%20to%20Advanced-blue?style=for-the-badge">
  <img src="https://img.shields.io/badge/Focus-Web%20Exploitation-black?style=for-the-badge">
</p>

---

## ⚡ Overview

This repository contains a **structured SQL Injection cheat sheet** covering essential techniques used in web security testing, penetration testing labs, and CTF challenges.

> ⚠️ **Disclaimer:**  
> This project is for **educational and ethical hacking purposes only**. Use it only in labs or systems you own or have permission to test.

---

## 🧠 What You'll Learn

- String manipulation across SQL engines  
- Database enumeration techniques  
- Error-based data extraction  
- Time-based blind SQL injection  
- Conditional logic exploitation  
- DNS exfiltration concepts  

---

## 🔗 Supported Databases

- Oracle  
- Microsoft SQL Server  
- PostgreSQL  
- MySQL  

---

## 📌 Cheat Sheet Sections

### ✨ String Concatenation
```sql
-- Oracle / PostgreSQL
'foo' || 'bar'

-- Microsoft SQL Server
'foo' + 'bar'

-- MySQL
CONCAT('foo','bar')
